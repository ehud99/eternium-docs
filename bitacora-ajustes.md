# Bitácora de ajustes — recorrido del flujo de Cementerio

> Ajustes y correcciones hechos al recorrer la app con la guía [`README.md`](README.md), fase por fase.
> Sirve para dos cosas: **no repetir los mismos errores** y **replicar las correcciones en otros
> módulos** que tienen el mismo patrón (sección 0).
>
> Repos (Forgejo `forgejo.partnertech.pe/PartnerTech-Distribuido`): `eternium_backend` (B) y
> `eternium_frontend` (F). Rama de trabajo `fix/general` → PR a `dev`. Los hashes son de esos repos.
>
> Formato de cada entrada: **Síntoma** (lo que se veía) → **Causa** → **Corrección** (commits) →
> **Lección**.

---

## 0. Patrones transversales (revisar en cualquier módulo nuevo o que se toque)

Errores que aparecieron en varios módulos. Antes de dar un módulo por bueno, pasarle esta lista.

| # | Patrón del error | Cómo detectarlo | Corrección de referencia |
|---|---|---|---|
| P1 | **Selector de usuarios con `/api/admin/users`**: para el Provider devuelve usuarios de *todas* las empresas y corta en 100. | Buscar `listUsuarios` / `/api/admin/users` en el front. | Usar `GET /api/eternium/company-members` (B 4ad78e9, F 913469e). El back además valida que el usuario sea miembro (B 7d4d6df, `ETN_PROSPECT_ADVISOR_COMPANY`). |
| P2 | **Un campo pide teclear el id numérico de un usuario** (supervisor, gerente, autorizador). Nadie lo conoce. | Buscar labels «Id del…», inputs `type=number` de usuario. | Select con buscador de usuarios de la empresa (F baab0c3), o quitar la regla si negocio la descarta (B c3d3c32 / F 63f97c7). |
| P3 | **Aprobación «por otra persona» en una empresa de un solo usuario**: nadie puede aprobar. | Validaciones `*_SELF`, CHECK `*_distinct` en BD. | Decisión de negocio: el mismo usuario puede aprobar y se registra quién aprobó (descuento: B 1683d15 + V136; 2.ª extensión de reserva: B a23b8c4). Mantener el «otro usuario» solo donde la guía §10 lo exige. |
| P4 | **Buscador que solo muestra resultados al escribir**: sin pista por dónde empezar. | Componentes `app-*-search`. | Desplegar los primeros 20 al enfocar y filtrar en el servidor al escribir (F a155e58, 2ff0e7c). Listas que crecen → `app-select-search` (F 26d0ccd). |
| P5 | **Modal de edición abre vacío** cuando `[isOpen]` está enlazado antes que el registro. | Abrir «Editar» en cada modal. | Reset en `ngOnChanges`, no en el setter de `isOpen` (F 71b2bd0, cdc3d25 — 43 modales). Al crear otro, el modal debe abrir limpio. |
| P6 | **El body se queda sin scroll** tras cerrar un modal montado con `@if` + `[isOpen]="true"`. | Cerrar un modal y probar scroll en otra pantalla. | El modal libera pila, foco y scroll en `ngOnDestroy` (F b38f5ef). |
| P7 | **Botones del pie pegados** en modales con `<div etnModalFooter>` sin clases. | Mirar el pie de cada modal. | `flex gap-2` en el contenedor proyectado (F e94a2eb). |
| P8 | **Montos/tasas no aceptan decimales** (o coma). | Teclear `12,5` en cualquier monto. | `parseEtnDecimal` de `@eternium/ui` con los decimales de la columna (F 1ec5bf5, ~40 campos). |
| P9 | **El formulario muestra un dato pero no lo envía** (p. ej. la moneda junto al precio). | Comparar lo que se ve con el payload. | Back: si falta, derivarlo (moneda de la sede) y migración que rellena lo guardado (B 4686d1d, V135). |
| P10 | **Normalizar un valor con `valueChange` del componente** queda pisado por lo tecleado. | Campos que pasan a mayúsculas, recortan, etc. | Normalizar sobre `valueChanges` del `FormControl` (F 706bc7c). |
| P11 | **Validación solo en el front** (o solo en el back). | Probar el endpoint directo / probar el formulario. | Siempre las dos: el front avisa antes de enviar, el back rechaza con código `ETN_*` (difuntos INC-017: B efa24ad + F fd5a9cb; documentos de identidad en `@eternium/ui`, F 71b2bd0). |
| P12 | **Exportaciones con códigos crudos o fechas ISO**. | Exportar Excel/PDF de cada listado. | `ExportLabels` (`export.enum.<dominio>.<CÓDIGO>`) y `ExportDates` dd/MM/yyyy (B 55d1155, c96b271). |
| P13 | **Claves `.properties` partidas en varias líneas sin `\`**: solo sale la primera línea. | `PortalMailTemplatesTest`, paridad de claves es/en/pt. | Una línea por clave; test de paridad (B c96b271). Toda clave nueva va en `messages`, `_en` y `_pt`, y en `es/en/pt.json` del front. |
| P14 | **Una entidad «hija» no se ata a la de su orden/contrato**: se podía ejecutar o programar sobre otro difunto, espacio o titular. | En cada paso que parte de una OS/contrato, ¿el back exige que coincidan? | Inhumación exige difunto, espacio, contrato y certificado de la OS (B ef68033 / F 03647a0). Sepelio exige difunto de la OS y titular del contrato (sesión 2026-10-09). |
| P15 | **Datos sensibles viajando en claro al navegador para «copiarlos»**. | Autocompletados de solicitante, titular, documento. | El servidor copia el dato (o los bytes cifrados) sin descifrarlo; el front solo ve el documento enmascarado (B 5358a6f; herencia de solicitante, sesión 2026-10-09). |
| P16 | **Acciones de fila solo con iconos**. | Tablas. | `app-etn-row-actions` / `row-action` / `row-menu`: ver/editar/descargar con texto de ayuda; lo que cambia estado, en «Más acciones» (F fd5a9cb). |
| P17 | **Checklist del front evalúa una cosa y se publica otra** (cuerpo en edición vs. borrador guardado). | Publicar sin guardar antes. | Publicar guarda primero el cuerpo en edición (F 6e8984e, test B 50a5060). |
| P18 | **No tocar partner-core** (lo mantiene otro equipo). | Cambios en paquetes `partnercore`. | Revertir y documentar para ese equipo (B bf34b7b → `partner-core-fix-system-alerts-details.md`). |
| P20 | **Claves técnicas visibles al usuario** (`maintenance.overdue_hours.REWORK`, `eternium_settings`, «RN-04», tipos con su código). | Buscar en plantillas `font-mono` o textos con `_`/`.` en minúscula; referencias a documentos de requisitos («RN-», «RF-»). | Nombre y descripción traducidos por un `code` estable; la clave nunca sale al navegador (Umbrales, sesión 2026-10-09; Documentos, F 60fbb6c). |
| P21 | **Valores de configuración copiados a mano en el frontend** (umbrales, defaults, «(15)» en un hint). Se desincronizan: 3 de 11 claves ya no existían. | Buscar constantes con valores de negocio en `models/*.ts` y números fijos en textos i18n. | Endpoint que devuelva lo que **rige** para la empresa, leído con las mismas claves y defaults que los servicios, centralizados en una sola clase (`common/settings/MaintenanceSettings`). |
| P22 | **Un selector ofrece una opción que la validación rechaza** («Todos» en el pabellón de un plan que exige pabellón). | Comparar las opciones de cada select con las reglas del back y los CHECK de la BD. | Quitar la opción, explicar la regla junto al campo y marcarlo en rojo (plan preventivo, sesión 2026-10-09). |
| P23 | **Una cifra que se lee como otra** («Duración 56 ms» del job leída como duración de las tareas; promedio redondeado hacia arriba que da «1 tarea diaria» con 3 al año; «Sin contrato vigente» que suma también a los que sí tienen contrato pero no perpetuo). | Revisar cada contador de un panel: ¿de qué es (job, tarea, espacio)?, ¿qué redondeo usa?, ¿mezcla motivos distintos? | Etiqueta con el sujeto («Tiempo de proceso del job»), decimales en promedios y un contador por motivo de exclusión (Programación, sesión 2026-10-09). |
| P24 | **Texto que promete un comportamiento que no existe** («se reenvía por push a los 30 min», «escala al jefe de atención», «se enviará a Contratos»). | Por cada «se notifica / se reenvía / escala / se enviará», buscar en el back quién lo hace y a quién. | Quitar la promesa o implementarla; si el destinatario es configurable sin pantalla, respaldo a administradores (F7-102, F6-20). |
| P25 | **Ids internos «#123» donde el usuario espera un nombre o un folio** (pago, plan, cuota, contrato, usuario, movimiento). | Buscar `#{{` y `'#' +` en plantillas. | El DTO trae el folio/nombre resuelto en una sola consulta por página, o el front usa la caché de referencias (F4-13, F4-26, F4-27). |
| P26 | **Moneda ignorada**: importes siempre en PEN o moneda elegida a mano distinta de la del contrato. | `formatMoney(x)` sin moneda; selectores de moneda en cobros/planes. | Formatear con la moneda del registro (`MonedaService`); cobrar y crear planes solo en la moneda del contrato, validado en el back (F4-01, F4-05, F4-22). |
| P27 | **«Segundo aprobador» nominal**: el solicitante elige a otro y queda aprobado sin que esa persona actúe; el selector incluye al propio usuario. | Flujos de doble aprobación. | La aprobación queda pendiente hasta que el aprobador actúe en su sesión; excluir al solicitante (F5-06). Excepción: si el negocio acepta que lo apruebe la misma persona (cobranza, G-02/G-03), no se finge un segundo actor: se valida que el aprobador sea un usuario activo de la empresa y se dice así en la pantalla. |
| P28 | **Plurales fijos** («{{n}} tareas» → «1 tareas»). | Buscar `{{n}} ` seguido de una palabra en plural. | Redacción neutra («Tareas: 1») mientras no haya plural ICU global (T-03). |
| P29 | **«No existe» cuando sí existe pero un filtro lo excluye** (plantilla «CONTRATO DE COMPRA» guardada con tipo «Acta de exhumación»: enviar a firma decía «no hay ninguna plantilla»). | Toda búsqueda que filtra por tipo, sede, idioma, estado o vigencia y responde «no hay». | El error nombra los casi-candidatos y qué cambiar (tipo, sede, publicar) y la ruta real del menú; probar con datos cargados «fuera del flujo» de la guía (B 9d9f23e). |
| P35 | **Endpoint que recibe o devuelve la entidad JPA** (`@Valid @RequestBody SpaceLegalRestriction`): la entidad no tiene anotaciones de validación, así que un campo vacío llega a la BD y el error es un 500; la respuesta expone columnas internas. | Buscar `@RequestBody` y `ResponseEntity<List<` con tipos de `domain.model`. | DTO de entrada con Bean Validation y mensajes por clave, DTO de salida; reglas de catálogo y de rango en el servicio (F2-12). |
| P34 | **Clase de color de una paleta que el tema no define** (`text-info-600`): Tailwind 4 no genera CSS y el texto hereda el color, ilegible en modo oscuro. | Buscar prefijos de color usados en plantillas (`grep -oE "(text|bg|border)-\w+-[0-9]+"`) y comparar con los `--color-*` de `@theme` en `styles.css`. | Definir la paleta en `@theme` (alias de una existente) o usar la que ya existe. |
| P33 | **Fecha en ISO dentro de un mensaje** («vence el 2026-10-15T19:00:00Z»): se pasa el `Instant`/`LocalDate` tal cual a `messages.get`. | Buscar `messages.get(` con `.toString()`, `String.valueOf(` o un getter de fecha como argumento. | `ExportDates.format(fecha)` para calendario y `ExportDates.format(instante, zona)` con la zona de `UserZoneResolver` (del destinatario si va a otra persona) (T-12). |
| P32 | **Filtro que el formulario ofrece y la consulta ignora** (rango de fechas de la cartera: el Excel decía «Período X–Y» sobre la foto de hoy). | Para cada filtro del `filter_schema` (o del formulario), seguirlo hasta el `WHERE`; desconfiar de los reportes «foto» (saldos vivos, inventario) que piden fechas. | Si la consulta no puede usarlo, quitarlo del esquema y descartarlo en el back; el encabezado dice lo que de verdad se filtró («Corte al …») (F8-01). |
| P31 | **KPI «de la empresa» calculado sobre la página cargada** («Por vencer ≤ 6 h», «Sin contacto ≥ 7 días»: cambiaban al paginar y con 25 filas nunca pasaban de 25). | KPIs que hacen `filter(...).length` sobre la lista paginada o sobre `totalElements` con filtros. | Contarlo en el back y servirlo en el `meta` del listado, junto con el umbral que usa (F3-16, F3-25). |
| P30 | **La pantalla muestra un dato y el sistema cobra otro** (precio del espacio vs tarifa vigente que usa la cotización); la misma regla vive en dos sitios y diverge. | Comparar lo que ve el vendedor con lo que cotiza el contrato; buscar la misma regla (filtro, orden) escrita dos veces. | Una sola definición en el dominio (`SpaceTypeRate.currentFor`) que usan todos; el DTO trae el valor efectivo ya resuelto (B e4475db). |
| P19 | **Un módulo inyecta el repositorio (`infrastructure.persistence`) de otro módulo.** Lo rechaza `EterniumArchitectureTest.modulo_no_importa_persistencia_ajena_nueva`. | Correr la suite **completa** (`mvnw test`), no solo los tests de la clase tocada. | Puerto en `<módulo>/domain/port` con un record de solo lo necesario, implementado por el servicio dueño del repositorio (`BranchBusinessHoursPort` en `BranchScheduleService`, sesión 2026-10-09). |

**Reglas de trabajo** (del montaje local, ver `CLAUDE.md` global):

- Tras editar Java: `.\dev.ps1 -Compile` (devtools reinicia solo). **Nunca `mvnw clean`** con el backend arriba.
- No commitear `.env`, `dev.ps1`, `environment.ts`, `run/`, ni cambios de CORS/hosts.
- Cada decisión de negocio que cambia una regla se anota en el cuerpo del commit con fecha («Decisión de negocio (2026-09-29): …»).
- Cuando una corrección cambia lo que hace el usuario, se actualiza la guía `README.md` en el mismo ciclo.

---

## 1. FASE 1 — Configuración

### 1.1 Series: dos series con el mismo prefijo emitían el mismo folio
- **Síntoma:** Contrato y Renovación con prefijo `CON` daban ambos `CON-2026-00001`; `CTR` en dos sedes chocaba con el índice único al emitir en la segunda sede.
- **Causa:** solo se impedía repetir (sede, tipo), pero los números son únicos por empresa.
- **Corrección:** el prefijo es único en toda la empresa, activo o cerrado (`ETN_SEQUENCE_PREFIX_TAKEN`); el seeder usa PAG, PAG2… (B 0fccf90). El modal avisa del prefijo usado y lo normaliza a mayúsculas (F 706bc7c).
- **Lección:** la unicidad de una validación debe coincidir con la del índice de BD que protege. Ver P10.

### 1.2 Tarifas: «El pabellón no es compatible con el tipo de espacio: asígnelo primero»
- **Síntoma:** crear una tarifa por pabellón obligaba a ir antes a Tipos de Espacio.
- **Corrección:** al tarifar (o importar tarifas) y al crear/editar un espacio, el pabellón se agrega al tipo; se vincula **antes** del validador de posición (B 0a3377d). Un pabellón de otra sede sigue rechazado (`ETN_STP_BRANCH`).
- **Lección:** si el sistema puede deducir un prerrequisito sin ambigüedad, lo crea en vez de mandar al usuario a otra pantalla.

### 1.3 Tarifas: bandeja vacía aunque hubiera pendientes; «Todos los pabellones» no reseleccionable
- **Corrección:** contadores por pestaña y aviso de pendientes en la otra pestaña (F ad64888). INC-015: opción vacía reseleccionable y tarifas acotadas a los tipos de la sede elegida (F 011d41a).

### 1.4 Plantillas de documentos
- Editor visual con chips de variables que se serializan a `{{path}}`, saneo de HTML (F 74f8d9f).
- Variables filtradas por tipo de plantilla; publicar con variables que el tipo no resuelve se bloquea (`ETN_TPL_UNAVAILABLE_VARS`) en vez de fallar al generar; `{{sede.logo|firma|sello}}` como imagen (B 9c03af7, F 62ee712).
- Acta de exhumación: el checklist decía «3 de 3» pero se publicaba el borrador guardado → publicar guarda primero (F 6e8984e, B 50a5060). Ver P17.

### 1.5 Configuración general — INC-013 / INC-014
- Licencias filtradas por sede con vista del archivo; exportación de series y de sedes con filtros (B 6fdcce6, F 8143f50).

---

## 2. FASE 2 — Espacios

### 2.1 El espacio mostraba «S/ 900.00» pero la cotización decía «sin tarifa ni precio base»
- **Causa:** el formulario mostraba la moneda de la sede pero no la enviaba.
- **Corrección:** se usa la moneda del espacio o la de la sede; V135 rellena los existentes (B 4686d1d). Ver P9.

### 2.2 Plano: el panel del espacio no decía de quién era
- **Corrección:** `GET /spaces/{id}/engagement` con titular, contrato, reserva y difuntos, solo nombres (B 688f892, F 1d7d6d3).

---

## 3. FASE 3 — Ventas → Contrato

### 3.1 Prospectos: «Asesor asignado» ofrecía usuarios de otras empresas
- B 7d4d6df + F 8183b34. Ver P1.

### 3.2 Pipeline: saltar etapas pedía el id de un supervisor
- Decisión de negocio (2026-09-29): el salto se permite sin autorización y queda marcado en el historial (B c3d3c32, F 63f97c7). Ver P2.
- Se probó el avance automático del pipeline al cotizar/reservar (B bd97c80) y se **revirtió** por decisión de negocio (2026-09-30): el tablero es manual (B 02ca403, F 0931de5). Solo «Asignar definitivamente» marca Ganada.
- **Lección:** confirmar con negocio antes de acoplar un módulo de seguimiento a uno transaccional.

### 3.3 Se podía cotizar y reservar un espacio ya comprometido por un contrato
- **Causa:** un contrato en borrador o en firma no cambia el estado del espacio (sigue AVAILABLE), y Cotización/Reserva no lo miraban: el fallo salía tarde, en «Generar contrato».
- **Corrección:** `SpaceEngagementPort`; cotización y reserva rechazan el espacio nombrando el contrato (`ETN_QUOTE_SPACE_ENGAGED` / `ETN_RESV_SPACE_ENGAGED`) (B bd97c80). El selector de la cotización los oculta (F 3022f57).
- **Lección:** validar el prerrequisito en el **primer** paso donde se elige el recurso, no en el último.

### 3.4 Cotización: nadie podía aprobar el descuento sobre el límite
- B 1683d15 (+ V136). Ver P3.

### 3.5 Reserva: el pago inicial no aceptaba decimales; la 2.ª extensión pedía el id del gerente
- F 8849a30; B a23b8c4 + F baab0c3. Ver P2, P3, P8.

### 3.6 «Generar contrato» fallaba con `ETN_TPL_UNRESOLVED_VARS` / `ETN_POWER_EXPIRED`
- **Causa:** el titular promovido desde el prospecto no tiene dirección ni representante legal.
- **Corrección:** `GET /sales/{id}/contract-requirements` y `holderCompletion` en `generate-contract`; modal «Completar datos del titular» que pide solo lo que falta (B 547ec20, F d2fb939). Crear antes el titular ya es opcional.

### 3.7 Proceso de Venta: stepper incoherente y «Retroceder estado»
- Stepper por hechos cumplidos, «Pago validado» segundo, botón «Firmar contrato» directo (F 461075d). Se quitó «Retroceder estado» (F baab0c3). Selector de archivo visible (F 9e9f355).

### 3.8 Contratos: evidencia de firma
- Ver y reemplazar la evidencia de cada firmante; reemite la escritura con el hash nuevo (B 295bec9, F ce95b0f). Tipo «Evidencia de firma» legible en Gestión Documental (F 60fbb6c).

---

## 4. FASE 4 — Pagos

### 4.1 El plan de pagos no tomaba el plan simulado en la cotización
- `GET /payment-plans/quotation-options` y botón «Traer de cotización»; re-elegir un plan de id menor chocaba con `uq_etn_qplan_selected` → desmarcar + `flush` antes de marcar (B 2505516, F 5a2a740).
- **Lección:** con índices únicos parciales («solo uno seleccionado»), hacer `flush` del desmarcado antes del nuevo marcado.

### 4.2 Registrar pago: excedente y vista previa
- Política por defecto «adelantar cuotas» (V137), cambiarla por plan o por pago, `POST /payments/preview` con el mismo calculador FIFO que el registro (B b32f871, F f80e6cd). «Registrar pago» se rellena con la próxima cuota + mora (F 1ec5bf5).

### 4.3 Conciliación: no se sabía qué formato aceptaba el extracto
- Plantilla Excel/CSV descargable con las columnas del parser y test que la importa (B 8e6ef8a, F aa73a92).

### 4.4 Exportaciones y correos
- Estados/tipos traducidos y fechas dd/MM/yyyy (B 55d1155, c96b271). Correos del portal truncados por claves partidas (B c96b271). Ver P12, P13.

---

## 5. FASE 5 — Servicios Funerarios

### 5.1 Paso 1 · Difuntos (INC-017)
- Tipo de documento del familiar responsable (V138; B 9c15333, F a35fd42).
- «Motivo del cambio» obligatorio al editar un borrador: el back lo exigía y el front no lo enviaba (F 5d3710c). Ver P11.
- Validaciones en ambos lados: nombres solo letras, formato según tipo (DNI 8 dígitos), nacimiento no futuro, fallecimiento ≥ nacimiento; ver/descargar el certificado con lectura auditada (B efa24ad, F fd5a9cb).
- «Editar borrador» abría vacío → P5 (F cdc3d25).

### 5.2 Pasos 2–4 · Solicitud, cotización y aprobación
- El contrato elige el espacio; el solicitante Titular/Beneficiario se autocompleta (documento copiado en el servidor); un beneficiario nuevo se designa en el titular porque el contrato firmado es inmutable (B 5358a6f, F 8266c96).
- Elegibilidad: «moroso» solo con cuotas vencidas; nuevo `CURRENT_WITH_BALANCE` = «Al día con saldo» (V139).
- Paso «Aprobación» visible en el detalle (Aprobar / Observar / Rechazar).

### 5.3 Paso 7 · Ejecutar inhumación
- **Síntoma:** se podía ejecutar con un difunto, espacio, contrato o certificado distinto al de la OS.
- **Corrección:** el back exige los de la OS y un certificado vigente del mismo difunto; inhumación/exhumación exigen OS del tipo correcto (B ef68033). El front fija difunto y espacio y autoselecciona el certificado (F 03647a0). Ver P14.

---

## 6. Sesión 2026-10-09 — Fase 5, pasos 6 y 8 (sepelio)

### 6.0 Trabajo heredado sin commitear (sesión anterior)
Estaba en el árbol de trabajo de ambos repos sobre `dev` (sin commit). Commiteado el 2026-10-09 en `fix/general`:
B 41536ac (órdenes de servicio) y 0f90f4c (ceremonias); F f7bd8cb (órdenes de servicio) y 7869cad (agenda y capillas).
Contenido:

**Back**
- **Una OS viva por difunto y tipo:** generar una segunda OS de inhumación (o exhumación, cremación, traslado) para el mismo difunto se rechaza (`ETN_SVCORD_DECEASED_DUPLICATE`). La ceremonia queda fuera (velorio y sepelio pueden ser dos servicios). `ServiceOrderRepository.findInProgressByDeceasedAndType`.
- **La solicitud muestra su OS:** `ServiceRequestDetailDTO.orders`, para que el paso «Orden de Servicio» muestre la existente en vez de ofrecer crearla otra vez.
- **OS con nombres:** `ServiceOrderDTO` trae `deceasedFullName` y `requesterFullName` (una lectura por difunto distinto, una consulta por página) para elegir la OS correcta sin abrir expedientes.
- **Ceremonia enlazada a una inhumación:** `relatedServiceOrderId` + `inheritRequester` en la solicitud. Difunto y contrato salen de la OS de inhumación; el solicitante se copia de la solicitud de origen **con los bytes cifrados** (no se descifra). Solo CEREMONY y solo inhumaciones no anuladas (`ETN_SVCREQ_RELATED_TYPE` / `ETN_SVCREQ_RELATED_ORDER`).
- **Sepelio atado a su OS:** el difunto del evento debe ser el de la OS (`ETN_CEREMONY_BURIAL_DECEASED_MISMATCH`) y el responsable, el titular del contrato de la OS (`ETN_CEREMONY_BURIAL_RESPONSIBLE`). Ver P14.
- **Capillas:** `POST /ceremonial-spaces/{id}/schedules/copy-branch-hours` copia el horario de atención de la sede como bandas (solo sobre horario vacío, todo o nada).
- **Agenda:** el filtro `eventStatus` acepta varios estados separados por coma.

**Front**
- Agenda: vista inicial **«Próximos eventos»** (desde hoy, solo vivos, agrupados por día, confirmar desde la lista, «Ver en agenda») además de la cuadrícula del día; exportación según la vista.
- Evento de sepelio: la OS se elige primero y fija difunto y responsable; propone la fecha programada de la OS como hora de inicio sin pisar lo elegido.
- Solicitud de ceremonia: enlazar la inhumación y heredar el solicitante.
- Detalle de la solicitud: muestra la OS ya generada.
- Capillas: botón para copiar el horario de la sede cuando el espacio no tiene bandas.

**Ajustes de esta sesión sobre ese código:**
- En `CeremonyEventService` el javadoc de `resolveResponsibleName` había quedado encima del método nuevo `requireBurialMatchesOrder`; se devolvió a su sitio.
  - **Lección:** al insertar un método, hacerlo **antes** del javadoc del siguiente, no entre el javadoc y su método.
- **La suite completa fallaba en `EterniumArchitectureTest`** (los tests de la clase sí pasaban): `CeremonialSpaceService` inyectaba `BranchBusinessHourRepository` de configuration. Se creó `configuration/domain/port/BranchBusinessHoursPort` (record `BusinessHourBand`), implementado por `BranchScheduleService`. Ver P19.
  - **Lección:** antes de commitear, `mvnw test` completo; los tests de arquitectura solo saltan ahí.
- Verificación antes del commit: backend 1588 tests en verde (6 omitidos); frontend `ng build` en verde y los JSON de i18n válidos (no hay specs de los componentes tocados).

### 6.1 Paso 8 · Agenda → cerrar sepelio (con acta y certificado)
- Probado por el usuario con el código de §6.0: **funciona**, sin ajustes adicionales.

### 6.2 Ramas y commits (regla desde 2026-10-09)
- **Síntoma:** el trabajo de la sesión anterior estaba sin commitear sobre `dev` local; en Forgejo ya no existía `fix/general` porque se borró al mergear las PR #13 (back) y #15 (front).
- **Regla:** todo se commitea en **`fix/general`** y se hace push en cada cambio; `dev` no se toca en local ni se crean otras ramas. El usuario abre las PR `fix/general` → `dev` y → `uat` en Forgejo.
- Comprobado el 2026-10-09: `dev` y `uat` tienen el mismo contenido; ninguna rama remota tiene commits que falten en `dev` (los dos «revert SystemAlert» de `feat/notificaciones-alertadores` y `sync-dev-uat` son copias del que ya está en `dev`). `fix/general` se recreó desde `origin/dev`.
- **Si la rama se borra al mergear la PR**, recrearla desde `origin/dev` actualizado (`git fetch` + `git switch -c fix/general origin/dev`) antes del siguiente cambio.

---

## 7. FASE 6 — Mantenimiento

### 7.1 Paso 1 · Configuración de Mantenimiento (sesión 2026-10-09)

Commits: B 79e7ef3 (pabellón), 36f99d5 (umbrales); F 113b3b3. Verificado: backend 1572 tests en verde
(incluye `EterniumArchitectureTest`), `ng build` en verde.

**Personal: «¿hacen falta 3 usuarios con los 3 roles?»**
- **Respuesta:** no. Solo el **Operario** ejecuta (y solo el asignado a la tarea); asignar y aprobar en Supervisión no exigen rol de mantenimiento, solo permiso del módulo. Capataz y Director corrigen ejecuciones enviadas y reciben alertas y el resumen diario. Un usuario tiene **un rol por sede** (`uq_etn_mstaff_user`); el rol se edita, no hace falta otra ficha.
- **Corrección:** el modal explica bajo «Rol» qué hace cada uno; el hint de capacidad muestra la capacidad vigente de la empresa en vez de un «(15)» fijo.

**Geocerca: «¿es lo que ocupa la sede?»**
- **Respuesta:** es un círculo (centro + radio) que rodea el predio para marcar las fotos de evidencia tomadas fuera; no bloquea. Con centro `1, 1` (océano Atlántico) todas las fotos saldrían marcadas.
- **Corrección:** ayuda de cómo obtener el centro, botón «Usar mi ubicación» (geolocalización del navegador), enlace «Ver en el mapa» (Google Maps) y hint del radio; textos sin «RN-04» ni «polígono» (es un círculo).

**Infraestructura común: el tipo «Pabellón» obligaba a recrear el pabellón**
- **Causa:** el tipo PABELLÓN representa la estructura del pabellón (techo, muros), pero el formulario pedía código y nombre nuevos y el pabellón era opcional: se podía duplicar el mismo pabellón.
- **Corrección:** con tipo Pabellón se elige el pabellón de Espacios y se proponen código (`PAB-SAN-JOSE`) y nombre; el back exige el pabellón, de la misma sede, y uno solo vivo por pabellón (`ETN_MAINTENANCE_ELEMENT_PAVILION_REQUIRED` / `_BRANCH` / `_DUPLICATE`). Un jardín dentro de un pabellón sigue permitido.

**Umbrales: claves técnicas y valores falsos**
- **Síntoma:** cada tarjeta mostraba la clave (`maintenance.overdue_hours.REWORK`) y el aviso hablaba de `eternium_settings`.
- **Causa (peor):** los valores estaban **fijos en el frontend** (`MAINTENANCE_SETTINGS`), no se leían de la empresa, y 3 de las 11 claves no coincidían con las que leen los servicios (`sector_alert_threshold`, `unassigned_hours`, `rejections_per_month` en vez de `sector_overdue_threshold`, `unassigned_alert_hours`, `rejections_per_month_alert`).
- **Corrección:** `common/settings/MaintenanceSettings` centraliza claves y defaults (los servicios y el dominio los toman de ahí); `GET /api/eternium/maintenance/thresholds` devuelve el valor vigente, el de fábrica, la unidad y si está personalizado; la pantalla muestra nombre, descripción y valor, sin claves. Se agregó «Historial mínimo para comparar costos», que existía y no se mostraba. Ver P20, P21.
- **Lección:** una pantalla «informativa» que copia valores del backend miente en cuanto alguien cambia un valor; si hay que mostrarlo, se lee.

### 7.2 Paso 2 · Programación de Mantenimiento (sesión 2026-10-09)

Commit: F a8c64dd. Verificado: `ng build` en verde.

**Plan preventivo: «Todos» en pabellón y luego «Elige al menos un pabellón o un sector»**
- **Causa:** la regla es deliberada (`ck_etn_mplan_scope`): el plan crea **una tarea por espacio y por periodo**; sobre toda la sede y a diario serían cientos de tareas al día. El selector ofrecía «Todos» y el error salía al guardar.
- **Decisión (2026-10-09):** se mantiene la regla. El pabellón ya no ofrece «Todos» («Elige un pabellón»), se marca en rojo si falta y un texto explica el porqué; sector y tipo «Todos» siguen cubriendo todo el pabellón. La limpieza general del predio va como Infraestructura común.
- **Lección:** no ofrecer en un selector una opción que la validación rechaza (P22).

**«Frecuencia del plan seleccionado»: hacer clic en «Mensual» no hacía nada**
- **Causa:** es un **simulador** de carga anual del plan elegido en la tabla; sin plan elegido los botones respondían sin mostrar nada, y el subtítulo era una nota de desarrollo («Parametrizable sin desplegar código»).
- **Corrección:** botones desactivados hasta elegir un plan, marca «actual» en la frecuencia del plan y textos que explican qué hace («Simula cuántas tareas generaría el plan elegido con cada frecuencia, sin cambiar el plan»). Ver P20.

**Pestañas (respuesta):** «Asignación del día» es donde están las **tareas** generadas (por fecha), para asignarlas a un operario; «Calendario» es la vista mensual con cuántas tareas hay cada día por estado, y al hacer clic en un día lista sus tareas. Las tareas aparecen tras «Ejecutar generación ahora» (o el job de las 00:30).

**Incidente de herramientas (para no repetirlo):** al editar los `i18n/*.json` con `json.dump` se reescribía el archivo entero: la indentación del repo es **mixta** y hay `\r` sueltos dentro de algunos valores (no es CRLF). Se editan con parcheo quirúrgico por nodo, partiendo de `git show HEAD:…`, y se comprueba que `git diff --stat` solo toque las líneas esperadas.

### 7.3 Paso 3 · Generación de tareas: 0 tareas y simulador confuso (sesión 2026-10-09)

Commits: B a109a46, F 1b89f2b. Verificado: suite completa 1599 tests, 0 fallos; `ng build` en verde.

**«Tengo una tarea diaria activa y no puedo asignarla»**
- **Causa:** no era un error de asignación: la generación produjo **0 tareas**. El plan «limpieza gral» tiene activado «Exigir mantenimiento perpetuo vigente» y en el pabellón hay 3 espacios: NICHO01 con contrato en **borrador**, LOTE001 con contrato activo pero **solo plan de pago de venta**, y LOTE004 **sin vender**. Ninguno cumple.
- **Respuesta:** no se crea un contrato por tarea. El contrato es la **venta del espacio** a una familia, y la regla (RN-01) es: solo se mantiene lo vendido con contrato activo, y si el plan exige perpetuo, además un plan de pago de concepto «Mantenimiento perpetuo» (Pagos → Planes de pago → Nuevo, sobre el contrato del espacio; nace ACTIVO). El operario no necesita contrato: es personal de Mantenimiento. Para probar sin perpetuo basta desactivar el interruptor en el plan (LOTE001 generaría tarea).
- **Corrección:** el panel «Cobertura contractual» decía «Con mantenimiento perpetuo 0 / Sin contrato vigente 3» aunque LOTE001 sí tiene contrato. Ahora `PlanScopeResolver.coverage()` da el motivo y el panel muestra «Generan tarea», «Sin contrato activo» y, si el plan lo exige, «Con contrato, sin mantenimiento perpetuo», con una nota que dice dónde se crea ese plan de pago.

**«Duración 56 ms, pero las tareas suman 5 minutos»**
- **Causa:** es lo que tardó el **proceso** de generación, no la duración de las tareas (los minutos del checklist son la estimación de trabajo del operario).
- **Corrección:** «Tiempo de proceso del job» y «Espacios omitidos (sin cobertura)». Ver P23.

**Simulador: «Anual» daba 3 tareas al año y «1 tarea» diaria frente a 15 de capacidad**
- **Respuesta:** tareas al año = espacios del alcance × veces por año (anual: 3 × 1 = 3; diaria: 3 × 365 = 1095). La capacidad (15) es lo que la cuadrilla **puede** hacer por día, no lo que se genera.
- **Causa del «1»:** el promedio se redondeaba **hacia arriba** (3 / 260 = 0,01 → 1) y repartía sobre 260 días aunque el calendario solo salta domingos y feriados.
- **Corrección:** 2 decimales y 313 días laborables (igual que `MaintenanceBusinessCalendar`); el mensaje dice «El plan generaría N tareas al año, unas X por día laborable, y la cuadrilla absorbe C por día». El simulador cuenta todos los espacios del alcance, tengan o no cobertura: mide la carga si todos estuvieran contratados.
- **Lección:** en promedios de carga, nunca `CEILING` a entero (P23).

### 7.4 Pasos 3–5 · Ejecución en campo, calendario y supervisión (sesión 2026-10-11)

Commits: B 4e51152, 5a25537, 5bc73e4 · F 61f60cc, 762f529. Verificado: suite completa 1612 tests, 0 fallos; `ng build` en verde.
Incidencias cerradas: F6-01/02/03/07/11/32/37–43 (ver `incidencias.md`).

**Registrar ejecución ofrecía tareas sin asignar (F6-01)**
- **Causa:** el buscador listaba PENDING_ASSIGNMENT y el back, al iniciar la tarea, exigía ASSIGNED y revertía el alta.
- **Corrección:** solo tareas con operario y abiertas (ASSIGNED, IN_PROGRESS, OVERDUE), en el front y en el back (`requireRegistrable`). Ver P22.

**Un borrador no se podía retomar (F6-02, F6-43)**
- **Causa:** cerrar el asistente dejaba la ejecución en DRAFT sin acción para seguir, y registrar otra vez creaba otra (en QA, 4 borradores de la tarea 1).
- **Corrección:** el alta devuelve el borrador existente (`resumed=true`) y el modal lo carga con su evidencia; la ficha de un borrador tiene «Continuar registro». Los 4 de QA se descartaron con `status = 0` (la tabla no admite DELETE).

**Los ítems obligatorios del checklist no se exigían (F6-03)**
- **Corrección:** el checklist se puede marcar hasta el envío (paso 3) y el back rechaza el envío con `ETN_MAINTENANCE_CHECKLIST_INCOMPLETE` si falta uno obligatorio. Ver P11.

**No se podía quitar una foto o un audio subido por error (F6-39) y la duración del audio se tecleaba (F6-40)**
- **Causa:** la evidencia es inmutable en el motor (triggers que bloquean DELETE) y el formulario pedía la duración a mano (29 s quedaba como «5 s»).
- **Corrección:** mientras el registro es borrador, la evidencia se DESCARTA (V140: `discarded_at/discarded_by`, `@SQLRestriction`, el trigger de topes deja de contarla); la fila se conserva. La duración se lee del archivo en el navegador.
- **Lección:** cuando el motor prohíbe borrar, la corrección de un error del usuario es un estado («descartado»), no un DELETE.

**Alertas que no se cerraban (F6-07)**
- **Corrección:** el auto-cierre cubre todo tipo con entidad tarea (vencida, escalada, sin asignar al asignarse, reprogramaciones, sync) y el motivo traduce el estado. Las 80 abiertas de QA son de 26 tareas vencidas sin operario: siguen abiertas con razón (que sean tres por tarea es F6-16, pendiente).

**Plan diario: lunes doble, calendario vacío y días perdidos (F6-37, F6-38)**
- **Decisión (2026-10-11):** domingos y feriados son **configurables por empresa** (Configuración → Umbrales → «Días de trabajo de la cuadrilla»). En un día no laborable el plan diario se salta; uno de periodo mayor pasa al siguiente día hábil.
- **Corrección:** «Ejecutar generación ahora» y el job recuperan los días sin corrida exitosa (tope 7); el calendario muestra con borde punteado las tareas previstas de los planes (`GET /calendar/projection`).

**Cumplimiento por pabellón en rojo sin atrasos (F6-41) y badge descuadrado (F6-42)**
- **Corrección:** solo cuentan tareas terminadas, vencidas o con el plazo cumplido; una completada a la espera de veredicto cuenta como cumplida (decisión 2026-10-11). El texto lo explica. Ver P23.

**Textos:** «polígono» → «geocerca» (F6-11) y la ficha ya no promete «correctiva en menos de 10 segundos».

### 7.5 Supervisión, alertas y autoría de la ejecución (sesión 2026-10-11, tarde)

Commits: B ecf97c5, de3550c, 030e579, 4bc0860 · F 90dc0c9, 470ee13. Verificado: suite completa 1616 tests, 0 fallos; `ng build` en verde.
Incidencias cerradas: F6-05/06/08/09/13/16/24.

**«Operario» mostraba la cuadrilla en «Por revisar» (F6-05)**
- **Corrección:** el DTO trae `operatorName`; la columna muestra el nombre y, debajo en gris, la cuadrilla (igual que «Rendimiento de la cuadrilla»).

**Toda preventiva quedaba «cubierta por mantenimiento perpetuo» (F6-06)**
- **Causa:** al aprobar se pasaba «viene de un plan» como `perpetualCareCovered`.
- **Corrección:** solo se marca cubierta si el plan exige plan de mantenimiento perpetuo (RN-01). Un plan de limpieza general es preventivo pero no se carga a ningún contrato.

**Alertas duplicadas (F6-08, F6-16)**
- **Reprogramaciones:** la reprogramación (al Director, clave `RESCHED-`) y el motor (al Capataz, `RESCH-`) levantaban dos alertas con el mismo umbral. **Decisión (2026-10-11):** una sola, al **Capataz**; la reprogramación la adelanta con la misma clave.
- **Vencida sin operario:** daba tres alertas (vencida, escalada 48 h, sin asignar). Ahora solo «sin asignar», que además se levanta en cuanto vence (antes esperaba 24 h desde la creación y, si ya estaba vencida, nunca llegaba). Las vencidas/escaladas abiertas de tareas sin operario se cierran solas con el motivo «la cubre la alerta sin asignar».
- **Lección:** dos reglas que miden lo mismo deben compartir clave de deduplicación; si no, la bandeja cuenta el problema dos veces.

**Cualquiera registraba a nombre de cualquiera (F6-09)**
- **Decisión (2026-10-11):** la ejecución queda **siempre a nombre del operario asignado**. La registra él o alguien de la oficina (capataz, director) en su nombre; otro operario recibe «La tarea está asignada a otro operario». Quién la cargó queda en `created_by`.
- **Corrección:** el selector «Operario» del modal se reemplazó por el nombre del asignado (solo lectura) y el campo `staffId` salió de la petición. Ver P22.

**Terminología del rechazo (F6-13)**
- **Decisión (2026-10-11):** lo que nace de un rechazo es un **retrabajo**; «correctiva» queda para las tareas ad-hoc y las órdenes correctivas. Se cambiaron los textos del rechazo, la bandeja y los mensajes del back («Reproceso», «rework»).

**Latencia «0 s» sin dato (F6-24)**
- **Corrección:** sin ejecuciones de campo ese día se muestra «—»; el texto aclara que es el promedio del día elegido (el back ya usaba esa fecha). Ver P23.

### 7.6 Resto de la Fase 6 (sesión 2026-10-11, noche)

Commits: B 4f296d7, 87122bb, 8a752f9 · F a472ca9, 6094a22, c793786. Verificado: suite completa 1619 tests, 0 fallos; `ng build` en verde; arranque real del backend (puerto temporal) para validar las JPQL nuevas.
Incidencias cerradas: F6-04/10/12/14/15/17–31/33–36. Con esto la Fase 6 queda sin pendientes.

**Capacidad en «Minutos» (F6-04)**
- **Causa:** el cupo se compara con un conteo de tareas y las tareas no llevan duración: «480 minutos» eran 480 tareas por día.
- **Corrección:** solo «tareas por día» (se quitó el selector; el back rechaza MINUTES en altas y cambios).

**GPS y geocerca (F6-10)**
- El GPS propio de la foto (EXIF) manda sobre lo tecleado; una foto con GPS real reevalúa la geocerca mientras la ejecución no conste dentro. Botón «Usar mi ubicación» (geolocalización del navegador).

**Reglas copiadas en el front (F6-12, F6-33, F6-34)** — Ver P11/P21.
- Topes de evidencia: `GET /executions/limits` con los valores de la empresa (claves ahora en `MaintenanceSettings`).
- Reprogramar a una fecha pasada y cerrar una OT sin costo final: ahora los rechaza también el back.

**Fallos del job que nadie veía (F6-20)** — Ver P24.
- La generación capturaba la excepción y `job_runs` registraba éxito. Ahora el job queda FALLIDO si alguna sede falló y, con dos fallos seguidos de una sede, los administradores de la empresa reciben campana y correo.

**Intervención aprobada (F6-25)**
- Se atribuye a quien ejecutó (la ejecución) y un retrabajo se clasifica como su tarea de origen (preventivo si venía de un plan).

**Acciones y avisos que no aplican (F6-15, F6-17, F6-18)** — Ver P22/P23.
- «Conectividad» solo en alertas de vencida; Asignar/Reprogramar/Cancelar solo en tareas abiertas; «sin asignar +N h» con el umbral de la empresa y la hora de generación (nuevo `createdAt` en la tarea).

**Textos y códigos (F6-14, F6-19, F6-21, F6-22, F6-23, F6-26–F6-31, F6-35, F6-36)** — Ver P20/P23/P25/P28.
- Una sola descripción de la regla de vencidas por pabellón; «asignadas para el día elegido»; error de la corrida legible con el detalle técnico plegado; estados, severidad y categoría traducidos en los mensajes del back; fechas dd/MM/yyyy en avisos; elemento de infraestructura en «Espacio»; «Generada» con la fecha real; sin promesas de nivel de servicio («menos de 30 segundos», «5 años», «meta 15 s»); tipo de tarea en vez de «TSK-id»; folio OT-0000001 en vez de «#id»; «Triada» → «Revisada»; «sincronizado HH:mm» solo si sincronizó.
- **Plurales (F6-35):** sin soporte ICU todavía, se usó redacción neutra («Tareas vencidas: 1»). El arreglo global sigue en T-03.
- KPI «de N tareas asignadas» sin las que no tienen operario; «Pendientes»/«En error» aclaran que son la cola actual.

---

## 8. FASE 7 — Portal de Familias (sesión 2026-10-12)

Commits: B 4bbe9c5 · F 87d29f5. Verificado: suite completa del backend en verde (0 fallos); `ng build` en verde.
Incidencias cerradas: F7-01–06, F7-101–107. Con esto la Fase 7 queda sin pendientes.

**«No encontramos un cementerio con ese RUC» con el RUC 20523799623 (no era un error)**
- **Causa:** ese RUC es de Partner Tech E.I.R.L., que en `core.company` es `PROVIDER` (el dueño de la plataforma). El portal solo abre empresas `TENANT` activas (`PortalProvisioningService.tenantByRuc`). En QA el cementerio es Jardines del Recuerdo (20603058934).
- **Lección:** para probar el portal hay que estar en la empresa cementerio, no en la del proveedor, aunque la Configuración General deje editar a ambas.
- **Enlace con el nombre en vez del RUC:** el `/portal/{ruc}` venía del código importado el 2026-09-10 (decisión «Q-8.4»); el Excel solo pide «un portal de autoservicio». Se consultó y el usuario decidió **mantener el RUC**.

**Códigos en crudo (F7-01, F7-02)** — Ver P20.
- «HOLDER · DNI», «PASSPORT»: helpers compartidos `etiquetaRelacion`/`etiquetaDocumento` en `portal-comun.model.ts` (un código nuevo se muestra tal cual).

**Números fijos que el back configura (F7-04)** — Ver P21.
- «20 minutos»: la marca pública (`GET /public/company/{ruc}`) ahora trae `sessionMinutes`.

**Contador de intentos (F7-05, F7-06)** — Ver P23/P28.
- Bajaba con cualquier error; ahora solo con `PORTAL_OTP_INVALID` y queda en 0 con `PORTAL_ACCOUNT_LOCKED`. Mensaje del back en redacción neutra.

**Promesa de escalamiento sin destinatario (F7-102)** — Ver P24.
- Sin `portal.requests.escalation_user_id` (no tiene pantalla) una solicitud sin asignar vencía sin avisar a nadie. Ahora avisa a los administradores (`StaffDirectoryPort.recipientsFor`, con respaldo en propietarios).

**Mensajes de validación equivocados (F7-106, F7-107)** — Ver P11.
- Plazo fuera de 1–90 decía «no negativo»; ambiente desconocido decía «proveedor desconocido». Cada uno con su mensaje.

**Textos y guía (F7-03, F7-101, F7-103–105)**
- 5 plantillas de aviso traducidas y en el filtro; «Invitar titular» ya no promete activar la cuenta; guía: cuotas pagables (vencidas o que vencen hoy), Culqi por nombre de variable de entorno, costo del tipo de solicitud informativo.

---

## 9. FASE 4 — Pagos (sesión 2026-10-13)

Commits: B fa31021, a0acf11 · F ae2c003, d1e6c25, 79d3e85 (T-01: B ec15eaf · F e8da449). Verificado: suite completa del backend 1630 tests, 0 fallos; `ng build` en verde tras cada tanda.
Incidencias cerradas: F4-01 a F4-37 y T-01. Con esto la Fase 4 queda sin pendientes. Decisiones del usuario: cobrar solo en la moneda del plan; importe vacío y obligatorio para conceptos que no son venta; punto «Portal» oculto en Caja.

**Dinero mal aplicado (F4-01, F4-22, F4-05)** — Ver P26.
- 100 USD se aplicaban como 100 PEN (`exchangeRate` nulo ⇒ 1). Ahora el cobro y el plan van en la moneda del contrato y el back rechaza otra; todos los importes se formatean con la moneda de su registro (`MonedaService`).

**Doble facturación (F4-02, F4-03)**
- La idempotencia era por tipo (`PAY:id:tipo`): el mismo pago podía llevar boleta y factura. Ahora un pago admite un comprobante vivo; el consumo de saldo a favor no se factura (ya se facturó el pago que lo generó).

**Reglas que solo validaba el front (F4-06, F4-08, F4-10, F4-11, F4-30)** — Ver P11.
- N.º de operación, plan del contrato, efectivo en el punto, punto del portal en caja e inicial = total: ahora también los rechaza el back.

**Ids y códigos en pantalla (F4-13 a F4-16, F4-24 a F4-27)** — Ver P20/P25.
- Detalle del pago con n.º de cuota, código del plan y punto; bitácoras traducidas; respuesta de SUNAT por clase; folio del pago y del movimiento en conciliación; contrato y usuario en reportes.

**Valores copiados en el front (F4-17 a F4-21, F4-23, F4-29, F4-35, F4-37)** — Ver P21.
- Nivel de refinanciación, umbral del Director, plazo de baja, meta de conciliación y horas de plazo los manda el back; «60 días», «S/ 0.10» y «5 días» salen de los textos.

**Lección:** no recompilar (`mvnw compile`) mientras corre la suite completa: cambia `target/classes` a mitad de camino y da cientos de `NoClassDefFoundError` falsos.

---

## 10. FASE 5 — Servicios Funerarios (sesión 2026-10-13)

Commits: B 8d3cadb, 6385f14 · F bcb1747, fad09a6. Verificado: suite completa del backend 1634 tests, 0 fallos; `ng build` en verde.
Incidencias cerradas: F5-01 a F5-24. Decisiones del usuario: doble aprobación hecha por el propio aprobador; aprobar la solicitud sin cotización sigue siendo opcional; requisitos con subida de archivo; generar el PDF de las actas.

**La OS no mandaba sobre el difunto (F5-02 a F5-04)** — Ver P14.
- Cremación, exhumación y traslado validaban tipo y estado de la OS, no que el difunto fuera el de la orden. Ahora lo exigen (como ya hacía la inhumación) y la exhumación solo ofrece OS del difunto elegido.

**Segundo aprobador nominal (F5-06)** — Ver P27.
- El emisor elegía a otro y la OS se emitía al instante. Ahora queda pendiente, el designado recibe campana y correo y decide desde su sesión; el emisor no aparece en el selector. Sin migración: PENDING_APPROVAL ya admitía segundo aprobador sin fecha.

**Registros sin salida (F5-01, F5-05)**
- Solicitud sin difunto: no había forma de añadirlo. Requisito: había que teclear id, clave de almacenamiento y hash. Ahora «Vincular difunto» y «Subir archivo» (el hash lo calcula el sistema).

**Pedir lo que la OS ya trae (F5-08, F5-09)**
- Cremación pedía difunto y fecha; inhumación, la fecha. Ahora se elige la OS y propone lo demás.

**Ids y estados crudos (F5-10 a F5-18, F5-21 a F5-23)** — Ver P20/P25.
- Nombres de difunto, n.º de OS, folios y referencias resueltos en el back con una consulta por página (`fullNamesByIds`, `orderNumbersByIds`); estados traducidos y actor por nombre.

**Promesa de PDF/A (F5-19)** — Ver P24.
- No existía ningún PDF de actas. Se implementó un PDF legible para todos los tipos (contenido canónico con etiquetas, sin ids, con el código de integridad) y se corrigieron los textos que prometían «PDF/A con firma digital».

**Lección:** en los tests con Mockito, `Messages.get(String, Object...)` es un solo método varargs: dos `when(...)` (con y sin argumentos) se pisan; basta un stub.

---

## 11. UAT — «No hay plantilla de contrato publicada» teniendo una (sesión 2026-10-13)

- **Síntoma:** en UAT (usuario Partner), «Enviar a firma» devolvía 409 «No hay ninguna plantilla de contrato publicada para esta sede. Publique una en Cementerio > Plantillas». En local, con demo y con Partner, funcionaba.
- **Causa:** la plantilla de UAT se llamaba «CONTRATO DE COMPRA» pero su **Tipo de documento** era «Acta de exhumación» (QA la armó fuera del flujo de la guía; su cuerpo usa variables de autorización y difunto). El envío solo considera plantillas publicadas de tipo CONTRACT_PURCHASE / CONTRACT_LEASE / CONTRACT_ASSIGNMENT de la sede o de toda la empresa, así que la ignoraba. En local las plantillas de QA tienen el tipo correcto. El mensaje además indicaba un menú inexistente.
- **Corrección (B 9d9f23e):** el 409 dice qué tipo se exige, da la ruta real (Configuración > Plantillas de Documentos) y nombra las plantillas publicadas con otro tipo («La plantilla «CONTRATO DE COMPRA» está publicada con el tipo «Acta de exhumación»: si es un contrato, cambie su Tipo de documento…») y las de contrato publicadas solo para otra sede. Test del caso.
- **Para resolverlo en UAT:** en Plantillas de Documentos, abrir «CONTRATO DE COMPRA», cambiar Tipo de documento a «Contrato de compra», Guardar propiedades (y publicar si la versión vigente no lo está).
- **Lección (P29):** cuando una búsqueda filtrada no encuentra nada, el error debe explicar qué existe y por qué no sirve. Probar también con datos creados fuera del recorrido de la guía.

---

## 12. FASE 2 — Espacios (sesión 2026-10-13)

Commits: B 27b7f1f, e4475db · F f164778, 6e4b9d8. Verificado: tests de espacios, catálogo, cotizaciones, contratos y `EterniumArchitectureTest` en verde; `ngc --noEmit` sin errores ni advertencias. Incidencias cerradas: F2-01 a F2-12. Decisiones del usuario: quitar «Formalizar» (el contrato ya formaliza el bloqueo); dejar DISPONIBLE solo si no hay ocupación ni contrato vivo.

**Espacios que quedaban mal para siempre (F2-01, F2-02)**
- «Formalizar» dejaba el espacio RESERVED sin bloqueo vivo y nada lo liberaba: se quitó botón, modal, método de servicio y endpoint. `SpaceHoldService.formalize` queda solo para el contrato (puerto `HoldFormalizationPort`).
- OCCUPIED → MAINTENANCE → AVAILABLE saltaba la guarda y ponía a la venta un nicho con difunto o contrato. `requireFreeToRelease` mira `currentOccupancy` y `SpaceEngagementPort`; aplica también al cambio en lote, que no pasaba por `changeStatus`.

**El precio que se ve no es el que se cobra (F2-08, F2-09)** — Ver P30.
- Disponibilidad mostraba, filtraba y ordenaba por `space.price`; la cotización cobra primero la tarifa aprobada vigente. `SpaceTypeRate.currentFor` es ahora la única definición y la usan las dos. La búsqueda comercial filtra y ordena en BD por `coalesce(tarifa del pabellón, tarifa genérica, precio del espacio)` (una subconsulta por nivel; la BD garantiza una sola aprobada vigente por concepto, así que `max` es exacto).
- Importes con la moneda del propio espacio (`money(monto, monedaId)`), no la de la sede.

**Importación (F2-04 a F2-07)** — Ver P8/P9.
- Moneda de la sede junto al precio; «900,50» ya no se guarda como 90 050 (coma o punto, ≤ 2 decimales, y un formato ambiguo se rechaza); sector no operativo ⇒ DISABLED también en alta e importación; la ayuda documenta la plantilla v2.

**Textos y pantallas (F2-03, F2-10 a F2-12)** — Ver P20/P21/P25.
- El lote solo ofrece estados que el back acepta; el error muestra estados traducidos. Sin «72 h» fijo. Historial con los 29 tipos de evento, módulo por nombre y payload sin ids. Tope de 500 en el lote.

**Pendiente de decisión (F2-12):** `/legal-restrictions`, `/holds/reassign` y `/holds/active` siguen sin pantalla.

**Pendiente de verificar en la app (no hay prueba contra base real):** Disponibilidad ordenada por precio (asc/desc) con paginación, y con filtro de precio mín./máx. La consulta usa subconsultas correlacionadas en un `Specification`; solo está probada la lógica de la tarifa vigente, no el SQL.

**Lecciones:**
- Con `Specification` + `Pageable`, un `ORDER BY` por expresión debe ir en la especificación (y el `Pageable` sin orden), y **nunca en la consulta de conteo** (`query.getResultType() == Long.class`): en PostgreSQL rompe el `count(*)`.
- Una guarda de estado hay que ponerla en TODOS los caminos que cambian el estado (individual, lote, importación, alta), no solo en el primero que se vio.
- Los specs de `libs/` no corren: `tsconfig.spec.json` apunta a un `src/` inexistente (ya era así).

## 13. FASE 3 — Ventas → Contrato (sesión 2026-10-14)

Commits: B ada1126, 4a83e46, d871b67 · F 2eceb77, 0748322, cc3f334. Verificado: 265 + 88 + 203 tests de cotizaciones, reservas, ventas, transferencias, documentos, titulares, prospectos, pipeline, contratos y `EterniumArchitectureTest` en verde; `ngc --noEmit` sin errores. Incidencias cerradas: F3-01 a F3-38. Decisiones del usuario: deuda de la transferencia solo desde Pagos (sin ingreso manual); una reserva CONFIRMED no se libera; «Ganada» solo al asignar definitivamente; nuevo tipo documental «Documento de identidad».

**Lo que cobra la cotización (F3-01 a F3-03, F3-22)** — Ver P30.
- El comparativo y el resumen calculaban con `space.price` y sin IGV. Ahora piden `GET /quotations/price-preview`, que usa `resolvePricing` + `amounts` (la misma cuenta que `create`).
- El simulador aceptaba 1 cuota; el plan real exige 2–60. Constantes en `QuotationService` vigiladas por un test contra `PaymentPlanService`; el detalle sirve la inicial mínima y el rango.

**Reglas que solo vivían en un lado (F3-04, F3-06, F3-11, F3-17, F3-26, F3-28, F3-32)** — Ver P11/P22.
- Front que ofrecía lo que el back rechaza: reservas sin pago validado en «Nuevo expediente» (ahora `GET /sales/ready-reservations`), contratos no renovables, plazo sin tope.
- Back que permitía lo que la pantalla negaba: liberar una CONFIRMED, mover a Ganada a mano, responsable de otra empresa, reservar desde una cotización referencial.
- Transferencias: con un 0 tecleado la deuda quedaba CLEARED con cuotas vencidas. Ahora `debtCheck` siempre consulta `DebtStatusPort` y bloquea si Pagos no responde.

**Datos de catálogo y configuración (F3-05, F3-07, F3-16, F3-25, F3-29, F3-38)** — Ver P21/P31.
- Tipo documental libre: «DNI» ≠ «DNI_TITULAR» y el checklist nunca casaba. Catálogo `Document.USER_TYPES` (+ `IDENTITY`) validado en subida y requisitos; V141 normaliza lo guardado.
- Versión de la política de consentimiento fija en «1.0». Ahora la pone el back desde la publicada (`ConsentPolicyPort`). **Sin política publicada no se registra ni prospecto ni titular** (lo dice la guía).
- Umbrales (6 h crítico, 7 días sin contacto, 15 días en riesgo, 30/60/90 del semáforo) servidos por el back en vez de copiados en el front.

**Moneda, folios y nombres (F3-12, F3-15, F3-19, F3-20, F3-27, F3-30, F3-34)** — Ver P25/P26.
- La reserva trae número y moneda de su cotización (`QuotationPort.refsByIds`); importes de cotizaciones, reservas y oportunidades con `MonedaService`.
- Comisiones con número de venta y vendedor (`SaleLookupPort.saleNumbersByIds` + `StaffDirectoryPort`); bitácora documental con nombre y detalle traducido (lo guardado se conserva para auditoría).
- Monto de oportunidad sin moneda ⇒ moneda funcional (`CompanyCurrencyPort`, adaptador sobre `CompanyCurrencyResolver`: los servicios de partner-core no se usan desde `application`).

**Lecciones:**
- Cuando el front pinta un importe, que lo calcule el back con la misma función que lo congela; dos fórmulas acaban divergiendo.
- Una «regla de pantalla» (texto que dice «no se puede») sin guarda en el back no es regla: probar el endpoint directo.
- Un KPI que debe ser de la empresa no puede salir de la lista paginada (P31).
- Un text block Java (`"""`) dentro de un parche Python con `"""` rompe el script: usar `'''` en el parche.
- `app-select-search` filtra solo lo cargado; para listas grandes, `(searchChange)` + búsqueda en servidor con debounce.
- La suite de varios módulos con la arquitectura tarda varios minutos y puede caer por memoria si corre en segundo plano con los servidores encendidos: correrla en primer plano.

## 14. FASE 1 — Configuración (sesión 2026-10-14)

Commits: B 10cd0ba, de34c32, 1b1ba0e · F a57feb2, 57d1fbb, 8250ff9. Verificado: 467 + 80 + 253 tests (configuración, plantillas, catálogos, inventario, contratos y `EterniumArchitectureTest`) en verde; `ngc --noEmit` sin errores. Incidencias cerradas: F1-01 a F1-32. Decisiones del usuario: una sola plantilla publicada por tipo, idioma y alcance; paquetes con componentes sin tarifa se publican con motivo; compatibilidad servicio↔tipo informativa; los parámetros de la sede solo en Parámetros.

**Lo que hacía fallar Ventas y Espacios (F1-01, F1-24, F1-25, F1-26)**
- Plantillas: dos publicadas del mismo tipo, idioma y alcance se rechazan al publicar (`ETN_TPL_PUBLISHED_DUPLICATE`), y la de la sede tapa a la de la empresa al resolver la del contrato.
- Paquetes: el precio de los componentes se toma al PUBLICAR (no al guardar el borrador) y una sola regla para los que no tienen tarifa (motivo obligatorio), igual en back, configurador y tarjeta.
- Sede sin moneda ⇒ espacios no cotizables: la moneda es obligatoria en front y back.

**Reglas que solo vivían en un lado (F1-03, F1-12, F1-17, F1-20, F1-27, F1-31)** — Ver P11/P22.
- «Generar espacios» no validaba empresa/sede/familia del tipo ni la capacidad máxima del tipo; ahora sí, con la misma regla que el formulario del sector.
- Patrón de código «al menos un token», familia del pabellón al vincular desde la tarifa, empresa del tipo en la compatibilidad y moneda de las tarifas del paquete: ahora en el back.

**Pantallas que no dejaban corregir (F1-06, F1-07, F1-11, F1-16)**
- «Editar sede» corrige los datos base (vía partner-core, conservando lo que no edita) y el RUC; los parámetros solo en Parámetros.
- Horarios con varios rangos por día (antes se perdían al guardar); tarifas rechazadas «Corregir y reenviar».

**Catálogos y textos (F1-08, F1-15, F1-19, F1-22, F1-23, F1-29, F1-30, F1-32)** — Ver P20/P21.
- Matriz tipología→familia servida por el back; etiquetas de tipología/modo en los mensajes del back; tipo de documento traducido; moneda de la tarifa = la de la sede, en solo lectura.

**Lecciones:**
- Cuando dos módulos aplican la misma regla (formulario del sector y «Generar espacios»), ponerla en ambos caminos o en un solo servicio: el segundo camino suele olvidarse (P11).
- Para editar algo que vive en partner-core desde Eternium, el adaptador debe partir de la entidad actual: un DTO incompleto borra lo que la pantalla no muestra (teléfono, responsable).
- Un mensaje sin argumentos no pasa por MessageFormat: no escapar llaves ni comillas en él.
- GateGuard (hook ECC) pide datos antes de cada archivo nuevo: con muchos archivos conviene declarar `GATEGUARD_EXEMPT_GLOBS` para los repos de trabajo.

## 15. FASE 8 — Análisis (sesión 2026-10-15)

Commits: B 49307a0 · F 94bd651. Verificado: 174 tests (reporting, auditreports, analytics, finreports, auditoría de difuntos, `ExportLabelsTest` y `EterniumArchitectureTest`) en verde; `ngc --noEmit` sin errores; V142 y V143 probadas sobre `eternium_qa` dentro de una transacción con ROLLBACK (1 definición y 104 554 filas). Incidencias cerradas: F8-01 a F8-04.

**Reporte con un filtro de adorno (F8-01)** — Ver P32.
- REP-CAR-05 ofrecía rango de fechas, pero la cartera son saldos vivos: el rango no filtraba y el export decía «Período X–Y». V142 lo quita del `filter_schema`; `buildQuery` descarta las fechas en todo reporte cuyo esquema no tenga `DATE_RANGE` (así también las programaciones ya guardadas), y el subtítulo pasa a «Corte al <hoy>».

**Bitácora con nombres de clase (F8-02, F8-03)** — Ver P20.
- El listener global (`AuditHibernateListener`) no guardaba el módulo: las ~100 000 filas de QA lo tenían vacío y el filtro por módulo no servía. Ahora lo deriva del paquete (`.modules.<x>` o `.common.<x>`) y V143 rellena lo existente con el mismo criterio (mapa entidad→módulo generado del código).
- Entidad, módulo y operación se traducen en el back (`AuditLabels`, claves `export.enum.auditEntity/auditModule/auditOperation`, 279 entidades y 67 módulos en es/en/pt) y viajan como `entityLabel/moduleLabel/operationLabel` y `label` en las facetas; el valor crudo se sigue enviando porque es el que filtra. Una entidad nueva sin traducción se muestra separada en palabras, no pegada.
- La traza ya no arma «Contract · UPDATE»: el título lo pone el back con las etiquetas y reemplaza la descripción genérica del listener («Entidad actualizada»).

**Pantalla de diagnóstico en jerga (F8-04)** — Ver P20.
- Salud de datos traduce fuentes (`mv_*`), tareas, estados de corrida e integraciones; el último error técnico queda plegado en «Detalle técnico» y los textos ya no hablan de `calculated_at`, `job_runs` ni vistas materializadas.

**Lecciones:**
- Un listener genérico debe guardar todo lo que la pantalla filtra: un campo que solo rellenan los servicios a mano queda vacío en el 99 % de las filas.
- Traducir en el back lo que también va al export (entidad, módulo) evita dos diccionarios; el front solo pinta `*Label` con respaldo al código.
- Al rellenar datos a partir del código (clase→módulo), generar el mapa con un script sobre las fuentes y usar la misma regla que el código nuevo.

## 16. T — Transversal (sesión 2026-10-15)

Commits: B b0ef4e2 · F 41235e7. Verificado: 140 tests de los servicios tocados (con `ExportDatesTest` nuevo y `EterniumArchitectureTest`) y los que leen los `messages` reales, en verde; `ngc --noEmit` sin errores; paridad es/en/pt y mismos placeholders en las tres. Incidencias cerradas: T-02 a T-12 (T-10 sin cambio).

**Plurales (T-03)** — Ver P28. Decisión del usuario: redacción neutra, sin plural ICU ni dependencia nueva.
- Front: 137 textos y 28 paginadores pasan a «Etiqueta: n» («Tareas: 1», «Registros: 1»); back: 24 mensajes (importaciones, tareas, reservas, portal). Se dejaron a propósito los topes de configuración (mínimo de caracteres, máximo de meses, horas de TTL: siempre > 1) y las claves `*Plural/*Many` que el código ya elige según el número.

**Enums y jerga (T-02, T-05, T-08, T-09)** — Ver P20.
- Estados del historial del prospecto, eventos de reserva, vínculos de oportunidad y severidad de auditoría por clave; los códigos se contrastaron con los que hay en `eternium_qa`.
- El tipo de evento de una pasarela no se traduce: lo inventa cada proveedor; se muestra como referencia técnica, igual que su id.
- «job / idempotente / sync / UUID / append-only / backend / endpoint» reescritos para el usuario. «Hash» se queda en back-office: es el nombre de la prueba de integridad que audita el usuario.

**Textos que prometen o copian valores (T-06, T-07)** — Ver P24 y P21.
- La nota de asignación prometía push y reenvío a los 30 min: ahora dice aviso en el sistema y correo, que es lo que hace el back.
- Los textos de reprogramaciones y rechazos interpolan el umbral vigente con un servicio compartido (`UmbralesVigentesService`, una llamada a `/thresholds` por empresa); el corte del ámbar del pabellón, que es constante del back y no umbral configurable, viaja en el DTO del tablero.

**Formato (T-11, T-12)** — Ver P33.
- Números del front con el locale del idioma activo. Los formateadores de moneda con `style: 'currency'` siguen en 'es-PE' (cambiarlos alteraría el símbolo «S/»): pendiente si se quiere.
- Fechas de los mensajes del back en dd/MM/yyyy; los instantes con hora en la zona del usuario o del destinatario (`UserZoneResolver`), nunca zona fija.

**Lecciones:**
- Antes de corregir una incidencia transversal, comprobar si una fase anterior ya la cerró (M03, RF-357, «Te quedan {0} intentos» y los umbrales de asignación ya no estaban).
- Un valor que vive como constante del back (no como umbral) también se sirve en el DTO si un texto lo cita.
- Para traducir enums, sacar los valores reales de la BD: evita claves faltantes que muestran la ruta de la clave.

## 17. G — Guía (sesión 2026-10-15)

Commits: B 901bf4a · F 235eb0a · guía `README.md`. Verificado: `CollectionExceptionServiceTest`, `CollectionAccountServiceUnblockTest` (nuevo), los demás tests de cobranza, `EterniumArchitectureTest` y `ExportLabelsTest` en verde; paridad es/en/pt. Incidencias cerradas: G-01 a G-08. Con esto `incidencias.md` queda sin pendientes.

**Aprobador de cobranza (G-02, G-03)** — Ver P27.
- La excepción de cobranza la aprobaba al instante a quien eligiera el solicitante, y el CHECK exigía otra persona: un segundo actor de nombre. Decisión del usuario: hay cementerios que lleva una sola persona, así que quien registra puede ser el aprobador. V144 quita `ck_etn_collexc_distinct`; el back valida que el aprobador sea un usuario activo de la empresa (`StaffDirectoryPort.activeMembers`, código `ETN_COLL_APPROVER`).
- El desbloqueo manual no validaba nada del aprobador; ahora aplica la misma regla. El usuario eligió primero «otra persona» y se le señaló que contradecía G-02: dejaría sin desbloqueo manual a los cementerios de una persona. Se quedó en «puede ser él mismo».

**Guía desalineada con el código (G-01, G-04 a G-08)**
- Exhumación: la doble validación del director solo con autorización judicial.
- Procesos automáticos: la tabla se rehízo leyendo los `@Scheduled`; «cada 5–15 min» escondía frecuencias de 1 min a 1 h y faltaban once procesos diarios.
- Errores frecuentes: el refresco de reportes tiene botón; «plan sin checklist» no puede pasar (el plan no se guarda sin checklist); `ETN_RATE_PAVILION` ya se había quitado en F1-18.

**Lecciones:**
- Una regla de cuatro ojos que el negocio no puede cumplir (una sola persona) termina en aprobaciones de nombre: preguntar antes si la segunda persona existe siempre.
- Si dos respuestas del usuario se contradicen, señalarlo antes de implementar.
- La tabla de procesos de la guía se genera leyendo los `@Scheduled` (cron y `fixedDelay`), no de memoria.

## 18. Mejoras pedidas por el usuario (sesión 2026-10-15)

Commits: B 080e5b7 · F 2df6f91 · guía `README.md`. Verificado: `DocumentSequenceServiceTest` (21), `DocumentSequenceAutoProvisionerTest` (5, nuevo), `DocumentSequenceExportServiceTest` y `EterniumArchitectureTest` en verde; el bloqueo consultivo probado en `eternium_qa`; `ngc --noEmit` sin errores.

**Nota sin color en Atención al Titular** — Ver P34.
- La nota del workflow usaba `text-info-600 / dark:text-info-400`, pero el tema no tenía paleta `info`: no se generaba CSS y en modo oscuro el texto salía casi negro. Se define `--color-info-*` como alias del azul informativo (`blue-light`); arregla también los otros tres modales que usaban `info-*`.

**Series que se crean solas**
- Decisión del usuario: la serie se crea al emitir el primer documento de un tipo en una sede que no tiene serie activa (no al crear la sede ni con migración). Prefijo: el base del tipo con el primer número libre de la empresa (COT1, COT2…), porque el prefijo es único por empresa; 7 dígitos (el folio de reportes, 6 con reinicio anual).
- `DocumentSequenceAutoProvisioner` en transacción propia (`REQUIRES_NEW`) con `pg_advisory_xact_lock` por empresa: dos emisiones simultáneas no eligen el mismo prefijo, y la serie queda aunque falle el documento (no consume número, no deja saltos).
- Una serie cerrada sin sucesora también cuenta como «sin serie»: el siguiente documento abre una nueva con el siguiente prefijo libre. Borrar una serie sin emisiones ya no deja la sede sin poder emitir.

**Series en una sola tabla**
- Selector de sede (por defecto la primera) en lugar de una tabla por sede. Los tipos sin serie se informan en azul («se crean solas al emitir») en vez de avisarse como falta.

**Lecciones:**
- Antes de pedir al usuario que configure algo por cada sede, ver si el sistema puede crearlo con un valor por defecto en el primer uso.
- Crear datos de configuración dentro de la transacción de otro documento: transacción propia y un bloqueo que cubra la regla de unicidad que solo vive en la aplicación.

**Plantillas por defecto (misma sesión)** — B 6a0922e · guía. Verificado: `DefaultTemplateProvisionerTest` (4, nuevo), `DocumentTemplateServiceTest`, `GeneratedDocumentServiceTest`, `ContractServiceTest` (70), `QuotationServiceTest` y `EterniumArchitectureTest` en verde.
- Decisiones del usuario: de empresa (para todas las sedes, no por sede); solo contrato y propuesta, las dos que el sistema emite (actas, OS, constancia y certificado no los imprime ningún proceso); al arrancar y en el primer uso.
- `DefaultTemplateProvisioner`: una sola escritura de contrato (cesión de uso, texto neutro respecto de la tenencia) y la propuesta, publicadas y con `branch_id` nulo. No crea nada si la empresa tuvo alguna vez una del tipo (aunque esté borrada): borrar la estándar y redactar la propia funciona. Corre al arrancar para todo cementerio y, para empresas dadas de alta después, al abrir Plantillas o al resolver plantillas al emitir; en transacción propia y recordando en memoria las empresas ya revisadas.
- Sustituye al sembrador de propuesta y al de contrato que solo corría en `dev`.
- Lección: antes de sembrar un juego completo, ver qué consume el sistema. Publicar las tres plantillas de contrato habría dejado el contrato ambiguo (`ETN_CONTRACT_TEMPLATE_AMBIGUOUS`).

## 19. Menores pendientes y mapa de la geocerca (sesión 2026-10-15)

Commits: B f0a7498, 30b74b4 · F b26fb9c, b38e651, 2fe786d. Verificado: `AuditLabelsTest`, `ContractTraceServiceTest`, `ActionLogServiceTest`, `CeremonyStaffServiceTest`, `SpaceHoldServiceTest`, `FuneralSpaceServiceTest`, `ExportLabelsTest` y `EterniumArchitectureTest` en verde; `ngc --noEmit` sin errores; paridad es/en/pt. V142–V144 aplicadas en `eternium_qa` al reiniciar.

**Importes en otros idiomas**
- `Intl.NumberFormat` con `style: 'currency'` no conoce «S/» fuera de es-PE (sale «PEN 1,234.50», también con `narrowSymbol`). Decisión del usuario: el número sigue el idioma y el símbolo sale del catálogo de monedas. `formatCurrencyAmount` en `@shared/utils/i18n`; los dos `formatMoney` y `MonedaService` (que ahora guarda el símbolo) pasan por él. USD deja de salir como «USD 1,234.50».
- Pendiente menor: los `computed()` que formatean importes sin leer el idioma no se recalculan al cambiarlo hasta la siguiente carga.

**Códigos crudos en mensajes y traza** — Ver P20.
- Aviso y escalamiento de ceremonias con el rol traducido (`export.enum.ceremonyStaffRole`).
- Traza del contrato: estado de contrato, evento de espacio, estado de pago y tipo/estado de acta traducidos en el back (también van al export).
- `changedFields`: catálogo `export.field.*` con los 333 nombres de campo que hay en `audit_logs` de QA; acepta camelCase y snake_case y, sin traducción, separa las palabras. Se traducen en la lista, la traza y el export; el detalle conserva el nombre técnico porque se cruza con los valores anterior/nuevo.

**F2-12: funciones del back sin pantalla** — Ver P35.
- Restricciones legales: DTOs con validación (tipo del catálogo, expediente, inicio, fin ≥ inicio), no se levanta dos veces, GET con permiso de ver. Pantalla en el menú de fila del inventario y en el distintivo «Restringido» (su tooltip describía la protección de datos).
- Bloqueos: decisión del usuario, «Todos» y «Reasignar» para quien puede editar (el GET no pedía permiso). El nuevo vendedor es obligatorio, distinto del actual y usuario activo; el DTO trae su nombre.

**Mapa de la geocerca**
- Decisión del usuario: OpenStreetMap (sin llave ni costo), como infrasys. `EtnGeofenceMapComponent`: clic o arrastre del marcador, búsqueda con Nominatim, círculo del radio y del radio con tolerancia. Reemplaza el enlace a Google Maps.

**Lecciones:**
- `Intl` con `style: 'currency'` ata el símbolo al locale: para mostrar la moneda local en otros idiomas, el símbolo tiene que venir de datos.
- Antes de crear una pantalla para un endpoint huérfano, revisar el endpoint: los tres de F2-12 tenían huecos (sin validación, sin permiso, sin comprobar el vendedor).
- Un componente de mapa no debe buscar su contenedor por id: con dos instancias o dentro de un modal, Leaflet engancha el que no es.

## 20. Primera versión sin SMS, Culqi ni Yape/Plin (sesión 2026-10-19)

Commits: B 2c93fa5 · F c4ffc6f. Verificado: `CollectionAssessmentServiceTest` (20, uno nuevo), `PortalCheckoutServiceTest`, `WebhookIngestServiceTest`, `WebhookProcessingServiceTest`, `PortalPaymentHistoryReceiptTest` y `EterniumArchitectureTest` en verde; `ngc --noEmit` sin errores.

**Decisión del equipo:** la pasarela es Izipay (solo tarjeta); Culqi, Yape/Plin y SMS se ocultan sin borrar el código hasta tener credenciales. WhatsApp e Izipay se integran después.

- `ChannelFeatures` (back) lee `ETERNIUM_SMS_ENABLED`, `ETERNIUM_CULQI_ENABLED`, `ETERNIUM_YAPE_PLIN_ENABLED` (false por defecto). Apagado: fuera de Pasarelas y del alta de plantillas, no cobra en el checkout, y la etapa `DAY_3_SMS` de cobranza se omite. `GET /api/eternium/features` y `EtnFeaturesService` (front) ocultan las opciones; un registro que ya tiene SMS lo sigue mostrando.
- SMS/WhatsApp sin proveedor real ya no simulan la entrega: fallan con motivo salvo `ETERNIUM_MESSAGING_STUB=true`. La entrega simulada contaba como evidencia para el bloqueo de cobranza.
- Se mantienen: «Yape/Plin» como medio manual en Caja (es dinero recibido, no pasarela) y el registro manual de WhatsApp en cotizaciones.

**Lección:** ocultar un canal en pantalla no basta si una regla del back lo usa sola (la etapa del día 3 de cobranza era SMS fija).
