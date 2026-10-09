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

_(siguiente fase a revisar)_
