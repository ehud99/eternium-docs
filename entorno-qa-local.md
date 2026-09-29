# Entorno local de QA — datos de prueba de Eternium

La base **`eternium_qa`** (PostgreSQL local, puerto 5432) contiene el cementerio de prueba creado desde cero durante la
QA y usado para verificar las correcciones. Está migrada hasta **V134** (la última de la rama `fix/qa-portal-varios`).
No se borra: úsala para editar, crear más datos o validar.

## Qué hay dentro

Tenant **«Cementerio Jardines del Recuerdo»** (companyId 2). Usuario: `demo@eternium.pe` / contraseña de
`EterniumDemoTenantSeeder.OWNER_PASSWORD`.

- Configuración completa (datos de empresa, parámetros, licencias, horarios), 2 sedes (Sede Principal y Sede Norte),
  series, 10 servicios, 2 paquetes, 5 plantillas publicadas (contrato, actas, orden de servicio, propuesta comercial),
  logo/firma/sello de la sede principal.
- 5 tipos de espacio, 2 pabellones (San Martín y Santa Rosa), 6 sectores, ~181 espacios, tarifas aprobadas.
- 11 prospectos (incluida una persona jurídica: Funeraria San Gabriel S.A.C.), cotizaciones con propuestas en PDF,
  reservas, 4 ventas cerradas, oportunidades del pipeline, metas.
- ~16 titulares y ~20 contratos, transferencias (una completada, una bloqueada por deuda), una renovación firmada.
- Difuntos, órdenes de servicio, inhumaciones, exhumación, cremación con urna, traslados interno y externo, capillas y
  agenda.
- Mantenimiento: planes, tareas ejecutadas con fotos, orden correctiva.
- Planes de pago (incluido uno quincenal), caja, comprobantes emitidos en stub (boletas, factura, notas), cobranza con
  alertas, conciliación con un extracto BBVA en .xlsx.
- Portal de familias: 4 cuentas de titulares, pagos simulados, solicitudes.
- Analítica: reglas de alerta (R1–R7), suscripción al resumen diario, reportes ejecutados.

## Cómo levantarla

**Importante:** arranca con `SPRING_PROFILES_ACTIVE=qa` (o cualquier perfil que no sea `dev`). Con `dev`, los seeders
demo sembrarían un pabellón «San José» con 338 espacios encima de estos datos.

Variables (además de las de tu configuración habitual de IntelliJ):

```
DB_URL=jdbc:postgresql://localhost:5432/eternium_qa
SPRING_PROFILES_ACTIVE=qa
ETERNIUM_SUNAT_STUB=true          # nunca emitir ante SUNAT real con estos datos
MAIL_TRANSPORT=smtp
MAIL_HOST=127.0.0.1               # sin salida real de correo (los datos tienen correos ficticios)
MAIL_PORT=9
STORAGE_TRANSPORT=filesystem      # o rustfs con eternium-bucket y un prefijo propio (p. ej. qa-local-e2e/)
STORAGE_FS_ROOT=<carpeta local>
STORAGE_FS_SIGNING_SECRET=<cualquier texto>
```

- Backend en el puerto 5000 y front (`ng serve`, configuración local) en 4200.
- Para probar las correcciones, compila la rama **`fix/qa-portal-varios`** (contiene todas las demás, apiladas):
  `mvnw clean package -DskipTests` (con el backend detenido) y arranca el jar con las variables de arriba.
- Los archivos que se subieron durante la QA a rustfs / al almacén local de la sesión de pruebas pueden no estar
  disponibles en tu almacén: si una descarga antigua falla, sube el archivo de nuevo.

## Orden de merge de las ramas

`feat/facturador-llave-por-empresa` (V123) → `fix/qa-criticos` → `fix/qa-sede-por-defecto` → `fix/qa-comprobantes` →
`fix/qa-contratos` → `fix/qa-ventas` → `fix/qa-plantillas-config` → `fix/qa-pagos` → `fix/qa-servicios` →
`fix/qa-analitica` → `fix/qa-portal-varios`. Están apiladas (cada una sale de la anterior) y usan las migraciones
V124–V134, así que la del facturador (V123) debe entrar antes.
