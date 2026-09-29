# Bug partner-core: toda inserción en `security.system_alerts` falla

**Módulo:** `partnercore/modules/system_alerts`
**Archivo:** `src/main/java/com/partnertech/partnercore/modules/system_alerts/model/SystemAlert.java`
**Severidad:** alta: ninguna alerta de sistema se puede guardar.

## Síntoma

Cualquier `systemAlertRepository.save(...)` lanza:

```
org.springframework.dao.InvalidDataAccessResourceUsageException: could not execute statement
[ERROR: la columna «details» es de tipo jsonb pero la expresión es de tipo character varying
  Position: 150]
[insert into security.system_alerts (alert_type,assigned_to,company_id,created_at,details,is_read,message,read_at,severity,title)
 values (?,?,?,?,?,?,?,?,?,?)]
```

Falla **aunque `details` sea `null`**: el driver envía el null tipado como `VARCHAR` y PostgreSQL no lo convierte implícitamente a `jsonb`.

## Causa

```java
@Column(name = "details", columnDefinition = "jsonb")
private String details;
```

`columnDefinition` solo influye en el DDL. En el binding, Hibernate 6 trata el `String` como `VARCHAR`. La columna es `jsonb` (`V1__init_tables.sql`, tabla `system_alerts`, `details JSONB`) y el `DB_URL` no lleva `stringtype=unspecified`, así que PostgreSQL rechaza el tipo.

## Fix propuesto (una anotación)

```java
import org.hibernate.annotations.JdbcTypeCode;
import org.hibernate.type.SqlTypes;

@JdbcTypeCode(SqlTypes.JSON)
@Column(name = "details", columnDefinition = "jsonb")
private String details;
```

- No necesita migración: la columna ya es `jsonb`.
- Es el mismo patrón que el proyecto ya usa para otros `String` sobre `jsonb` (p. ej. `responsePayload`).
- A tener en cuenta: con el tipo JSON, un `details` que **no** sea JSON válido fallará en la BD (`invalid input syntax for type json`). Hoy el único escritor que rellena `details` es `SystemAlertService` (`.details(dto.details())`, desde la API). Conviene validar que sea JSON en el DTO, o documentarlo.

Alternativa sin tocar el tipo: añadir `?stringtype=unspecified` a la URL JDBC. No la recomendamos: afecta a todos los `String` de la aplicación, no solo a esta columna.

## Cómo reproducirlo

1. BD con el esquema de Flyway aplicado (cualquier base de dev vale).
2. Ejecutar algo que guarde una `SystemAlert`, por ejemplo:
   ```java
   systemAlertRepository.save(SystemAlert.builder()
           .company(company).alertType("test").severity("info")
           .title("t").message("m").build());   // details = null
   ```
3. Resultado: la excepción de arriba.

Con el fix aplicado, la inserción funciona. Lo verificamos contra PostgreSQL 17 con el esquema completo: las alertas quedan guardadas y `details` queda `NULL`.

## A quién afecta hoy

Todo lo que inserta en `system_alerts` falla y **revierte su transacción completa**:

| Llamador | Módulo | Efecto |
|---|---|---|
| `SystemAlertService` | partner-core | El endpoint de creación de alertas devuelve error. |
| `ReservationService.sendAlerts` | cementerio (reservas) | El job diario de alertas de vencimiento de reserva cae entero en cuanto hay una reserva por vencer: no se sellan los flags ni sale ningún aviso. |
| `LicenseExpiryAlertJob` | cementerio (configuración) | El aviso de licencias por vencer no se guarda. |
| `ExceptionSlaJob` | cementerio (conciliación bancaria) | Las alertas de SLA de excepciones no se guardan. |

Del lado de cementerio no tocamos partner-core. Si necesitáramos las alertas antes de que llegue el fix, escribiríamos esas filas desde un adaptador propio con un `CAST(? AS jsonb)` explícito, pero preferimos que se corrija en el origen.

## Contexto

Lo detectamos en una prueba E2E de las notificaciones de cementerio (rama `feat/notificaciones-alertadores`). Habíamos aplicado el fix en esa rama y luego lo revertimos (commit `a89e6cb`), porque partner-core lo mantiene su equipo. **La rama `uat` todavía contiene el fix** (commit `212c52a`); cuando fusionen su corrección, conviene que coincidan para no tener conflictos.
