# Módulos del Excel vs. submódulos de la aplicación

> Por qué la aplicación tiene más pantallas en unos módulos y menos en otros que el Excel
> original `Cementerio - Seguimiento General.xlsx` (hoja *General*, sección "APLICATIVO CEMENTERIO").
> Elaborado el 2026-09-26 a partir del Excel y del código (`EterniumModuleInitializer.java` y las
> referencias `doc NN RF-xxx` en los controladores).

## La idea clave

El Excel describe **alcance funcional**; el menú muestra **pantallas**.

- Cada **submódulo del Excel** equivale a un **documento de diseño** (`DOC_0001` … `DOC_0045`),
  numerado en el mismo orden que las filas del Excel. El código los cita constantemente:
  `Sedes (doc 01 RF-005..012)`, `Tarifas (doc 02 RF-011..014)`, `Reserva 72h (doc 15)`,
  `Dashboard (doc 37)`, etc.
- Cada **ítem del menú** es una **pantalla con su propio permiso** (`canView/canCreate/canEdit/canDelete`
  de partner-core). Se siembran en
  `eternium_backend/src/main/java/com/partnertech/eternium/config/EterniumModuleInitializer.java`.

Por eso la relación no es 1 a 1. Una pista: **cada submódulo del Excel tiene exactamente 4 viñetas**
(salvo `gestion_difuntos`). Es una plantilla para listar alcance, no un diseño de pantallas. Al diseñar,
cada documento se convirtió en tantas pantallas como entidades o flujos distintos tenía.

Las diferencias salen de 5 causas:

1. **Separar**: un documento con varias entidades distintas se parte en varias pantallas.
2. **Mover**: una función pasa al módulo donde realmente se usa en el día a día.
3. **Agregar configuración**: el documento pedía catálogos o parámetros que necesitan su propia pantalla de back-office.
4. **Renombrar**: el nombre técnico del Excel se cambió por uno de negocio.
5. **No tener menú**: la app móvil, las integraciones y la seguridad no son pantallas del back-office de cementerio.

## Numeración de documentos de diseño (orden del Excel)

| Módulo Excel | Submódulos (doc) |
|---|---|
| M01 Maestros y Configuración | configuracion_cementerio (01), catalogo_espacios (02), catalogo_servicios (03), plantillas_documentos (04) |
| M02 Gestión de Espacios | inventario_espacios (05), plano_interactivo (06), busqueda_disponibilidad (07), historial_espacio (08), gestion_difuntos (09) |
| M03 Contratos y Titularidades | registro_titulares (10), contratos_titularidad (11), transferencias_renovaciones (12), gestion_documental (13) |
| M04 Ventas y Reservas | cotizacion_propuesta (14), reserva_espacio (15), proceso_venta (16), pipeline_comercial (17) |
| M05 Servicios Funerarios | solicitud_cotizacion_servicio (18), inhumaciones_exhumaciones (19), cremaciones_traslados (20), agenda_ceremonias (21) |
| M06 Facturación y Cobranza | facturacion_electronica (22), planes_pago (23), registro_pagos (24), cobranza_morosos (25), reportes_financieros (26) |
| M07 Mantenimiento | programacion (27), ejecucion_tareas_campo (28), supervision_control (29), historial_infraestructura (30) |
| M08 Portal de Familias | consulta_online (31), pagos_online (32), solicitudes_online (33) |
| M09 App Móvil | app_operarios, app_vendedores, funcionalidades_comunes (34–36) |
| M10 Dashboard y Reportería | dashboard_ejecutivo (37), reportes_operativos (38), reportes_auditoria (39) |
| M11 Integraciones | integracion_sunat (40), integracion_bancos_pagos (41), integracion_externa (42) |
| M12 Seguridad y Auditoría | autenticacion_autorizacion (43), auditoria_trazabilidad (44), configuracion_seguridad (45) |

## Módulo por módulo

### M01 Maestros y Configuración → **Configuración** (4 → 9) · causa: separar

| Excel (doc) | Pantallas en la app |
|---|---|
| configuracion_cementerio (01) | **Configuración General** (datos del cementerio, parámetros, licencias, horarios), **Sedes** (RF-005..012), **Series y Correlativos** (RF-014..017) |
| catalogo_espacios (02) | **Tipos de Espacio** (con su capacidad máxima), **Pabellones y Sectores** (árbol sede→pabellón→sector), **Tarifas** (vigencias y bandeja de aprobación) |
| catalogo_servicios (03) | **Servicios** (funerarios y de mantenimiento, con precio), **Paquetes** |
| plantillas_documentos (04) | **Plantillas de Documentos** (incluye logo, firma y sello por sede) |

Sedes, correlativos, tipos, pabellones y tarifas son entidades distintas, cada una con su propio CRUD y
ciclo de vida. Además se suelen dar permisos distintos: alguien puede aprobar tarifas sin poder crear pabellones.

### M02 Gestión de Espacios → **Espacios** (5 → 4) · causas: mover y renombrar

| Excel | App |
|---|---|
| inventario_espacios (05) | Inventario de Espacios |
| plano_interactivo (06) | Plano Interactivo |
| busqueda_disponibilidad (07) | **Disponibilidad y Bloqueos** (renombrado: su núcleo es el bloqueo de 72 h) |
| historial_espacio (08) | Historial de Espacios |
| gestion_difuntos (09) | ➜ **movido a Servicios Funerarios / Difuntos** |

El registro del difunto (DNI, certificado de defunción, familiares) es el primer paso de inhumaciones,
cremaciones y órdenes de servicio, así que se ubicó junto a ellos. `DeceasedController` todavía lo dice:
*"Servicios Funerarios / Difuntos (doc 09, M02/09)"*.

### M03 Contratos y Titularidades → **Contratos** (4 → 4) · causa: renombrar

Relación 1 a 1: Titulares y Beneficiarios (10), Contratos de Titularidad (11), Gestión Documental (13)
y Transferencias y Renovaciones (12). Solo cambian los nombres y el orden.

### M04 Ventas y Reservas → **Ventas** (4 → 5) · causa: separar

Cotización (14), Reserva (15), Proceso de Venta (16) y Pipeline (17) son 1 a 1. **Prospectos (CRM)** es
nuevo: salió de doc 14 RF-003/RF-019. Para cotizar primero hace falta registrar al prospecto, y eso es un
mini-CRM con su propia pantalla.

### M05 Servicios Funerarios (4 → 7) · causas: mover, separar y renombrar

| Excel | App |
|---|---|
| *(viene de M02)* gestion_difuntos (09) | **Difuntos** |
| solicitud_cotizacion_servicio (18) | **Órdenes de Servicio**: un solo flujo solicitud → cotización con versiones → OS |
| inhumaciones_exhumaciones (19) | Inhumaciones y Exhumaciones |
| cremaciones_traslados (20) | **Cremaciones y Traslados** + **Permisos Municipales** (el traslado externo requiere documentación municipal, que es su propio trámite) |
| agenda_ceremonias (21) | **Agenda de Ceremonias** + **Capillas y Salas** (catálogo de espacios ceremoniales que la agenda necesita) |

### M06 Facturación y Cobranza → **Pagos** (5 → 6) · causas: separar y renombrar

Planes de Pago (23), **Caja y Pagos** (24: puntos de cobro, turnos, cierre diario), Facturación Electrónica (22),
Cobranza y Morosos (25) y Reportes Financieros (26). **Conciliación Bancaria** se separó de doc 24
(el código la llama `M06/24-bis`) porque tiene su propio flujo (importar extracto, cruzar pagos, revertir con
motivo) y la usa otro rol, Tesorería.

### M07 Mantenimiento (4 → 5) · causa: agregar configuración

Programación (27), Ejecución en Campo (28, la **versión web**), Supervisión y Control (29) e Historial de
Infraestructura (30). Se agregó **Configuración de Mantenimiento** (doc 27 §1-4 y doc 30 §1) para las
frecuencias y catálogos que las otras cuatro pantallas necesitan.

### M08 Portal de Familias (3 → 5) · causa: son submódulos distintos

Los 3 submódulos del Excel (consulta_online 31, pagos_online 32, solicitudes_online 33) son **el portal
público de las familias**: otra interfaz, sin menú lateral (rutas `/portal/:ruc/...`). Lo que se ve en el
menú es el **back-office** que ese portal necesita:

- **Atención al Titular**: bandeja de solicitudes (33)
- **Tipos de Solicitud** y sus SLA (33 RF-023)
- **Cuentas del Portal** (31 RF-020/024)
- **Consentimientos** según la Ley 29733 (31 RF-027)
- **Pasarelas de Pago** (32)

### M09 App Móvil (3 → 0) · causa: no tiene menú

Es una aplicación aparte y no está en este repositorio. Lo que se podía usar desde la web se hizo ahí
(*Ejecución en Campo* en Mantenimiento), y el backend ya expone cosas para la app de vendedores (por
ejemplo, el tarifario congelado, doc 34).

### M10 Dashboard y Reportería → **Análisis** (3 → 3) · causa: renombrar

Dashboard Ejecutivo (37), Reportes (38) y Auditoría (39): relación 1 a 1.

### M11 Integraciones Externas (3 → 0) · causa: no tiene menú

No son pantallas, son conexiones del backend usadas desde otros módulos:

- **SUNAT (40)**: dentro de Facturación.
- **Bancos y pagos (41)**: dentro de Conciliación y Pasarelas.
- **API pública y servicios externos (42)**: en el backend.

Las credenciales se guardan en la tabla `integration_credentials`.

### M12 Seguridad y Auditoría (3 → 0 propios) · causa: no tiene menú propio

- **Autenticación, roles, sesiones y usuarios (43)**: los provee **partner-core** en sus propios menús
  (Usuarios, Roles, Permisos, Sesiones, Auditorías).
- **Trazabilidad inmutable (44)**: triggers de base de datos que impiden modificar o borrar registros
  (tabla `immutability_violations`).
- **Reportes de auditoría**: se ven en *Análisis / Auditoría*.

## Resumen numérico

- **Excel:** 12 módulos, 45 submódulos.
- **App:** 9 categorías con **48 pantallas**.

Tres módulos del Excel (App Móvil, Integraciones, Seguridad) no tienen menú propio. Los demás ganaron
pantallas por separación, movimiento o configuración. El orden también cambió: en la app van Ventas (4),
Pagos (5) y Servicios Funerarios (6), porque se construyeron por fases (5a, 6a…).

## Etiqueta SYS

"Cementerio Sistema" es un **espejo** del grupo "Cementerio": las mismas pantallas, pero en la vista de
plataforma que ve el Provider (`/cementerio-sistema/...`), no en la de cada empresa (`/cementerio/...`).

## Aclaración

Los documentos de diseño `DOC_00xx` no están en el repo. La relación Excel → documento → pantalla se dedujo
de las referencias `doc NN RF-xxx` del código y de los comentarios de `EterniumModuleInitializer`.
Coinciden en todos los casos revisados, pero el "por qué" detallado de cada decisión debería estar en esos
documentos y en los mockups.
