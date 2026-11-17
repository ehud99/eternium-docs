# Incidencias de QA — Eternium

> Incidencias reportadas por QA sobre UAT (`https://uat.eternium.partnertech.pe`). Fuente:
> [planilla de seguimiento](https://docs.google.com/spreadsheets/d/1H8syuXIpKXkhjORbhuuBBMowa5bGkrrG/edit?gid=1433277971)
> y un documento `INC_ETD_00NN` por incidencia.
>
> **Cómo usarla:** una fila por incidencia en el resumen y una sección con el detalle. Cada incidencia
> se corrige en su propio commit, con el asunto `<tipo>(<ámbito>): inc-NN-<qué se hizo>`. Si la
> corrección deja una lección o un patrón que se repite, va también a
> [`bitacora-ajustes.md`](bitacora-ajustes.md). Las incidencias de la evaluación interna (T, G,
> F1–F8) siguen en [`incidencias.md`](incidencias.md).
>
> Rutas abreviadas: `F/` = `eternium_frontend/libs/eternium/`,
> `B/` = `eternium_backend/src/main/java/com/partnertech/eternium/modules/`.

## Resumen

| N.° | Título | Módulo | Prioridad | Registro | Estado | Commits |
|---|---|---|---|---|---|---|
| 16 | Plantillas prediseñadas no visibles en una empresa que ya tenía plantillas (reabierta) | Configuración / Plantillas de documentos | Alta | 28/09/2026 | Corregida — pendiente de validar en UAT | B ea92546 · F 86e562c |
| 18 | RUC y teléfono de sedes (18-1); sedes «No operativa» en los filtros (18-2) | Configuración / Sedes | Alta | 20/10/2026 | Corregida — pendiente de validar en UAT | B eed61f2, e82707b · F f80e513, 2d29460, a4b30f1 |
| 19 | Licencias duplicadas del mismo tipo; el adjunto persiste en el modal | Configuración General / Licencias | Alta | 20/10/2026 | Corregida — pendiente de validar en UAT | B bb297f7 · F 306cd6b |
| 20 | Tarifa con vigencia retroactiva | Configuración / Tarifas | Alta | 20/10/2026 | Corregida — pendiente de validar en UAT | B 8153a1f · F a999e63 |
| 21 | Huecos superpuestos; desplegable que desborda; no se puede quitar el plano del pabellón | Configuración / Pabellones y sectores | Alta | 20/10/2026 | Corregida — pendiente de validar en UAT | B 2ed8a40 · F 7986215 |
| 22 | Tarifas duplicadas del mismo concepto y vigencia que luego no se pueden aprobar | Configuración / Tarifas | Alta | 21/10/2026 | Corregida — pendiente de validar en UAT | B 9301a67 |
| 23 | Tarifas aprobadas sin acciones; sin fecha fin no se puede cambiar el precio | Configuración / Tarifas | Alta | 21/10/2026 | Corregida — pendiente de validar en UAT | B 6e3fbb0 · F 691cadc |
| 24 | El tarifario exportado incluye tarifas rechazadas | Configuración / Tarifas | Alta | 21/10/2026 | Corregida — pendiente de validar en UAT | B 9d75661 |
| 25 | El ícono de calendario de la vigencia no abre el selector | Configuración / Paquetes | Media | 21/10/2026 | Corregida — pendiente de validar en UAT | F da9d35a |
| 26 | El botón «Guardar» desaparece en «Requisitos previos» | Configuración / Servicios | Media | 22/10/2026 | Corregida — pendiente de validar en UAT | F afceb2d |
| 27 | Logo, firma y sello fallan con 413 aunque pesan menos de 2 MB | Configuración / Plantillas | Alta | 22/10/2026 | Corregida — pendiente de desplegar y validar en UAT | F 06331df |
| 28 | PDF del contrato con interlineado excesivo | Configuración / Plantillas | Alta | 22/10/2026 | Corregida — pendiente de validar en UAT | B 7379f15 |
| 29 | El alta de espacios ofrece pabellones sin sectores habilitados para el tipo | Espacios / Inventario | Alta | 23/10/2026 | Corregida — pendiente de validar en UAT | B a5b10b7 · F df1549a |
| 31 | El historial exportado muestra códigos técnicos y detalles insuficientes | Espacios / Historial | Alta | 23/10/2026 | Corregida — pendiente de validar en UAT | B d7c5ab4 |
| 32 | RUC de persona jurídica sin validación de prefijo 20 | Contratos / Titulares | Alta | 26/10/2026 | Corregida — pendiente de validar en UAT | B e976538 · F f30734c |
| 33 | El contrato no recupera los beneficiarios del titular | Contratos | Alta | 26/10/2026 | Corregida — pendiente de validar en UAT | B 8382086 · F 272e8c0 |
| 34-3 | Borrador de contrato con precio pactado S/ 0.00 | Contratos | Media | 26/10/2026 | Corregida — pendiente de validar en UAT | B 4d3bf54 · F cac1dcd |
| 34-1 | Una sede sin moneda dejaba un borrador que no se podía activar | Contratos | Media | 26/10/2026 | Corregida — pendiente de validar en UAT | B 7cb1de4 · F 7c74e7d |
| 30 | Sin tarifa aprobada, el precio de lista del espacio se escribía a mano | Espacios / Inventario | Alta | 23/10/2026 | Corregida — pendiente de validar en UAT | B cda3d4b · F 6f77c45 |
| 35 | El QR del espacio debe abrir el espacio al escanearlo (reemplaza a la 35 anterior) | Espacios / Inventario | Media | 27/10/2026 | Corregida — pendiente de validar en UAT | B 17e69b4 · F a628546 |
| 36 | Historial exportado: «de X a Y», detalle de alta e importación, zona horaria | Espacios / Historial | Media | 27/10/2026 | Corregida — pendiente de validar en UAT | B 9e72893 |
| 37 | «Espacio funerario» ofrece espacios sin contrato o fuera de servicio | Servicios funerarios / Inhumaciones | Media | 28/10/2026 | Corregida — pendiente de validar en UAT | B 3de992f · F 3d0ffb5 |
| 39 | Los espacios no se filtran por la sede de la orden | Servicios funerarios / Inhumaciones | Media | 28/10/2026 | Corregida — pendiente de validar en UAT | B 1503375 · F 3dede17 |
| 38 | Fecha programada anterior a la defunción; no queda en el acta | Servicios funerarios / Inhumaciones | Media | 28/10/2026 | Corregida — pendiente de validar en UAT | B 76e692c · F c113bbf |
| 34 (nueva) | No se ve el precio registrado al modificar una tarifa desde Acciones | Configuración / Tarifas | Media | 26/10/2026 | Corregida — pendiente de validar en UAT | F 8bf1c02 |
| 40 | Beneficiarios: la fecha de nacimiento no se valida contra «Es menor de edad» | Contratos / Titularidad | Media | 28/10/2026 | Corregida — pendiente de validar en UAT | B ffd1cc9 · F f03c274 |
| 41 | RENIEC: Titulares valida cualquier DNI ficticio | Contratos / Titulares | Media | 28/10/2026 | Pendiente — falta decidir el comportamiento esperado | — |
| 42 | La alerta de vencimiento no muestra un contrato que vence dentro de 90 días | Contratos / Renovaciones | Media | 29/10/2026 | Corregida — pendiente de validar en UAT | B 328d9ae · F 1b1cfc6 |
| 43 | La notificación de vencimiento sale en inglés y con fecha ISO | Configuración / Notificaciones | Media | 30/10/2026 | Corregida — pendiente de validar en UAT | B 5df06f4, 6bb2c2c |
| 44 | La renovación completada no genera evento en el historial del espacio | Espacios / Historial | Media | 30/10/2026 | Corregida — pendiente de validar en UAT | B b56fee3 · F 996544d |
| 45 | «No tiene permisos sobre este registro» al vincular un documento en la transferencia | Contratos / Transferencias | Alta | 30/10/2026 | Corregida — pendiente de validar en UAT | B a1837ee · F 1aa3a0e |
| 46 | El simulador de plan de pagos permite inicial 100 % con cuotas de S/ 0.00 | Ventas / Cotizaciones | Media | 02/11/2026 | Corregida — pendiente de validar en UAT | B 52c4601, 07461cc · F 48658d1, e32a9a5 |
| 47 | El tablero del pipeline no tiene filtro por vendedor | Ventas / Pipeline comercial | Media | 03/11/2026 | Corregida — pendiente de validar en UAT | B f07cb53 · F 5223459, 1a1e12d |
| 48 | Venta cobrada sin comisión; no hay dónde configurar la tasa de comisión | Ventas / Pipeline comercial | Media | 03/11/2026 | Corregida — pendiente de validar en UAT | B 1f04914 · F 3302247 |
| 49 | No hay reporte del % de conversión entre etapas del pipeline | Ventas / Pipeline comercial | Media | 03/11/2026 | Corregida — pendiente de validar en UAT | B 4e39694 · F 9eeaebc |
| 50 | «Nueva meta» con alcance Sede no lista ninguna sede | Ventas / Pipeline comercial | Media | 03/11/2026 | Corregida — pendiente de validar en UAT | B 521157c · F 64cff36 |
| 51 | El modal de inhumación no avisa que el difunto ya está inhumado | Servicios funerarios / Inhumaciones | Media | 04/11/2026 | Corregida — pendiente de validar en UAT | F 8d8c446 |
| 52 | No hay integración con Google Calendar en la agenda de ceremonias | Servicios funerarios / Agenda de ceremonias | Media | 11/11/2026 | Corregida — pendiente de validar en UAT | F 304d2aa |
| 53 | El comprobante no llega a Aceptado vía OSE (queda en Contingencia) | Pagos / Facturación electrónica | Media | 11/11/2026 | Corregida — pendiente de validar en UAT | B db9a53c |
| 54 | No existe el tipo «Recibo por honorarios» | Pagos / Facturación electrónica | Media | 11/11/2026 | No aplica — respondida a QA (el RH no lo emite la empresa) | — |
| 55 | El recibo de pago no se envía al correo del titular | Pagos / Caja y pagos | Media | 12/11/2026 | Corregida — pendiente de validar en UAT | B fa63801 · F faab879 |
| 56 | El botón de cierre de la orden correctiva no completa la acción | Mantenimiento / Historial de infraestructura | Media | 12/11/2026 | Corregida — pendiente de validar en UAT | F 32036b5 |
| 57 | Error 401 después de verificar el código del Portal de Familias | Portal de familias / Acceso | Media | 12/11/2026 | Corregida — pendiente de validar en UAT | B 6c83474 · F 317791f |
| 58 | La cartera no ordena por monto adeudado ni filtra por fechas | Pagos / Reportes financieros | Media | 13/11/2026 | Corregida — pendiente de validar en UAT | B d447f5a · F 205b635 |
| 59 | El reporte de ingresos no filtra por tipo de servicio ni por pabellón | Pagos / Reportes financieros | Media | 13/11/2026 | Corregida — pendiente de validar en UAT | B 1aca182 · F bc08a6a |
| 60 | Error 500 al emitir por series y correlativos solapados entre sedes | Pagos / Facturación electrónica | Media | 13/11/2026 | Corregida — pendiente de validar en UAT | B 6f81f62 · F c33ff69 |
| 61 | El titular no puede descargar su comprobante de pago desde el portal | Portal de familias / Documentos | Media | 13/11/2026 | Corregida — pendiente de validar en UAT | B 475ad4d · F 2029b3b |
| 62 | El portal solo permite pagar con tarjeta: faltan Yape y Plin | Portal de familias / Pagos en línea | Media | 13/11/2026 | Pospuesta — depende de la pasarela y de los medios contratados | — |
| 63 | El correo de confirmación de pago llega en inglés, con variables sin reemplazar y marca PartnerCore | Portal de familias / Notificaciones | Media | 13/11/2026 | Corregida — pendiente de validar en UAT | B e9d9fea |
| 64 | Las alertas ejecutivas se muestran en inglés | Análisis / Dashboard ejecutivo | Media | 13/11/2026 | Corregida — pendiente de validar en UAT | B b7a5872 · F fd5a6e6 |
| 65 | Falta el reporte regulatorio para la municipalidad | Análisis / Reportes | Media | 13/11/2026 | Pospuesta — falta el formato que pide la municipalidad | — |
| 66 | El reporte de inhumaciones y exhumaciones muestra «INTERNAL_TRANSFER» | Análisis / Reportes de auditoría | Media | 13/11/2026 | Corregida — pendiente de validar en UAT | B f3999c9 · F 2f479b4 |
| 67 | La ficha del contrato en Auditoría muestra el estado «ACTIVE» | Análisis / Auditoría | Media | 13/11/2026 | Corregida — pendiente de validar en UAT | F 9083590 |
| 68 | La cartera no muestra la clasificación de estados del requerimiento | Pagos / Reportes financieros | Media | 13/11/2026 | Corregida — pendiente de validar en UAT | B c03035b · F bf51f93 |
| 69 | La proyección de ingresos no muestra todos los meses | Pagos / Reportes financieros | Media | 13/11/2026 | Corregida — pendiente de validar en UAT | B 9eb62a2 · F 88aec27 |
| 70 | El Excel y el PDF no muestran el período consultado ni el usuario | Pagos / Reportes financieros | Media | 13/11/2026 | Corregida — pendiente de validar en UAT | B 63e1a62 |
| 71 | La consulta de RUC muestra los estados en inglés (ACTIVE / FOUND) | Pagos / Facturación electrónica | Media | 16/11/2026 | Abierta | — |
| 72 | No hay integración con Yape/Plin en el portal | Pagos / Integración bancos y pagos | Media | 16/11/2026 | Abierta (misma causa que la 62) | — |

### Descartadas por QA

| N.° | Título | Motivo |
|---|---|---|
| 34-2 | El espacio sigue «Disponible» con un contrato en borrador | Descartada por QA (27/10/2026): un borrador no reserva el espacio |
| 35 (anterior) | El historial no muestra el contrato en borrador | Descartada por QA (27/10/2026); el número pasó a la incidencia del QR |
| 34 (contratos) | 34-1 sede sin moneda, 34-2 espacio «Disponible», 34-3 precio S/ 0.00 | Revisión del 30/10/2026: QA reemplazó el texto de la 34 por un caso de Tarifas («No se visualiza el precio registrado al modificar una tarifa desde Acciones») y dejó el comentario DEV anterior. Las correcciones 34-1 y 34-3 siguen subidas; la 34 nueva queda pendiente. Confirmar con QA |

---

## INC-18 — Validación insuficiente de RUC y teléfono en sedes

[Documento](https://docs.google.com/document/d/1e9cUTf7OXt-ZHHYAzfqSJ81xjek7SX6W/edit) · Reportada por QA · Incidencia funcional · Alta

**Reporte:** al crear o editar una sede se guardaba un RUC con formato inválido (`40923823888`) y un
teléfono de 19 dígitos, sin advertencia visible. El mensaje del teléfono hablaba de «6–30 dígitos».
**Esperado:** RUC con longitud y prefijo según el tipo de contribuyente; teléfono de 6 a 9 dígitos
(fijo o celular) y, si no, impedir el guardado con «El teléfono debe tener entre 6 y 9 dígitos
(fijo o celular)».

**Causa:**
- RUC: el formulario solo exigía 11 dígitos y, con error, marcaba el campo en rojo sin decir por qué.
  El back de la sede tampoco miraba prefijo ni dígito verificador, y en el **alta** no validaba nada.
  La empresa sí validaba el dígito verificador, con su propia copia de la regla en back y front.
- Teléfono: el patrón admitía de 6 a 30 caracteres; el back solo limitaba a 30.

**Corrección:**
- Una sola regla de RUC para empresa y sedes: 11 dígitos, prefijo SUNAT (10, 15, 16, 17, 20) y dígito
  verificador (módulo 11), con un mensaje distinto para cada fallo.
  - Back: `IdentityRules.rucProblem` (B/holders) + `TaxIdRules` (B/configuration), aplicada en el alta,
    la edición y el perfil de la sede, y en el RUC de la empresa.
  - Front: `rucValidator`/`rucErrorKey` en `F/ui/src/identity/identity-rules.ts`; Datos de la empresa
    deja su copia local.
- Teléfono de sede: 6 a 9 dígitos. El campo solo admite dígitos (máx. 9); el back acepta espacios,
  guiones o paréntesis, valida y guarda solo los dígitos.
- Tests: `IdentityRulesTest` (+2), `BranchProfileServiceTest` (+3; el RUC inventado del test de alta
  pasa a uno real).

**Para validar en UAT:** Configuración → Sedes → Editar/Nueva: `40923823888` → «debe empezar en 10, 15,
16, 17 o 20»; `20131312956` → dígito verificador; `20131312955` se acepta. Teléfono de 19 dígitos no se
puede escribir (máx. 9); con 5 dígitos → «El teléfono debe tener entre 6 y 9 dígitos (fijo o
celular)». Lo mismo en Configuración General → Datos de la empresa para el RUC.

---

## INC-19 — Licencias duplicadas y adjunto que persiste

[Documento](https://docs.google.com/document/d/1Y0go97bM48wLFYZ7K9EAIQIES1JUMroL/edit) · Incidencia funcional · Alta

**19-1 — Reporte:** con una «Licencia municipal de funcionamiento» vigente en la sede, se registraba otra
del mismo tipo sin advertencia. **Esperado:** impedirlo con «La sede ya cuenta con una licencia vigente de
este tipo. Revisa el registro existente antes de continuar.», y permitir renovaciones conservando el historial.

**Corrección (B):** `OperatingLicenseService.requireNoOverlap` — dos licencias del mismo tipo y sede no pueden
cubrir los mismos días (alta, edición y restaurar desde la papelera). Una renovación cabe si empieza el día en
que vence la actual o después; cada licencia conserva su registro y el estado (vigente / por vencer / vencida)
dice cuál rige. Error `ETN_LICENSE_DUPLICATE` con el texto de QA y cómo renovar. Tests: +4.

**19-2 — Reporte:** tras guardar una licencia con adjunto, «Registrar licencia» mostraba el archivo anterior.
**Causa:** el componente de subida guarda su propio estado; el modal limpiaba su referencia pero no el
componente. **Corrección (F):** al abrir el modal se cancela y reinicia el componente de subida. Los demás
usos del componente no tenían el problema (Pabellones crea uno por pabellón; el resto usa zonas propias).

**Para validar en UAT:** registrar una segunda licencia municipal con fechas que se crucen → mensaje de QA;
con emisión desde el vencimiento de la actual → se registra. Guardar una licencia con adjunto y abrir
«Registrar licencia» → sin archivo.

---

## INC-20 — Tarifa con vigencia retroactiva

[Documento](https://docs.google.com/document/d/18YWsU1ZAhATztSTwvo1mP7ekWGKgKRUV/edit) · Incidencia funcional · Alta

**Reporte:** «Vigencia desde» permitía fechas anteriores a hoy; una tarifa registrada el 20/10 con vigencia
desde el 05/10 se superpone a contratos y comprobantes ya emitidos con el precio anterior.
**Esperado:** fechas pasadas deshabilitadas, mensaje y no guardar; la tarifa rige desde hoy.

**Corrección:**
- F: el calendario de «Vigencia desde» empieza hoy y el guardado lo vuelve a comprobar con mensaje. Al
  editar (solo tarifas no aprobadas) se puede conservar la fecha que ya tenía.
- B (espacios y servicios): crear o mover la vigencia a una fecha pasada → `ETN_RATE_RETROACTIVE`. Además,
  no se aprueba una tarifa pendiente cuya vigencia ya empezó (aprobarla la haría retroactiva): hay que
  corregir la fecha antes. Tests: +5; los de aprobación usaban fechas fijas de enero y pasan a fechas
  relativas a hoy.
- No se tocó la importación masiva de tarifas (puede necesitar cargar históricos); queda a decisión.

**Para validar en UAT:** Tarifas → Nueva tarifa: los días anteriores a hoy aparecen deshabilitados; intentar
aprobar una tarifa pendiente cuya vigencia ya empezó → mensaje para corregir la fecha.

---

## INC-22 — Tarifas duplicadas con vigencia cruzada

[Documento](https://drive.google.com/file/d/1oPI121IVIftM6UJer4aSlCI7Wa33T3o1/view) · Incidencia funcional · Alta

**Reporte:** en Tarifas de servicios se registraron tres tarifas de Exhumación de la misma sede con la misma
vigencia; todas quedaron en la bandeja y ninguna se podía aprobar. **Esperado:** validar al registrar que no
haya otra tarifa del mismo servicio con vigencia cruzada, con error y sin guardar.

**Causa:** el cruce solo lo controlaba el EXCLUDE de la BD, que mira únicamente las **aprobadas**: al crear no
se comprobaba nada y el choque aparecía recién al aprobar.

**Corrección (B, servicios y espacios):** crear, editar o restaurar una tarifa cuya vigencia se cruce con otra
**pendiente o aprobada** del mismo concepto (servicio + sede; tipo de espacio + pabellón) se rechaza con
`ETN_RATE_OVERLAP` y un mensaje que pide ajustar las fechas o editar la existente. Mismo criterio que la BD
(fechas inclusivas, sin fin = abierta); las rechazadas no cuentan. Tests: +6.
- No se tocó la importación masiva (igual que en la 20).
- Con la 23, una tarifa que empieza **después** de una aprobada no es duplicada: es un nuevo precio que la
  reemplaza al aprobarse.

**Para validar en UAT:** Tarifas de servicios → registrar dos veces la misma tarifa con la misma vigencia → la
segunda muestra el mensaje y no llega a la bandeja. Con vigencias contiguas (la nueva empieza el día después
del fin de la otra) se registra.

---

## INC-24 — El tarifario exportado incluye rechazadas

[Documento](https://drive.google.com/file/d/1RWfQS0fYmXombWFrJ6BbWds0lG4ISCcY/view) · Incidencia funcional · Alta

**Reporte:** al exportar el Tarifario de Tipos de Espacio salían 3 tarifas: 1 aprobada y 2 rechazadas.
**Esperado:** solo las aprobadas.

**Causa:** la exportación seguía el filtro de estado de la pantalla y, con «Todos», no filtraba nada.

**Corrección (B, espacios y servicios):** sin un estado pedido, el tarifario exportado lleva solo las
**aprobadas** (es lo que rige). Si se filtra explícitamente por Pendientes o Rechazadas, se exporta eso.
Tests: +2.

**Para validar en UAT:** Tarifas → filtro «Todos» → Exportar (Excel, CSV, PDF) → solo aprobadas.

---

## INC-25 — El calendario de vigencia de Paquetes no abre

[Documento](https://drive.google.com/file/d/1SgU0cEo_VmId_fn4GKYziDBMXfCl5F-P/view) · Incidencia funcional · Media

**Reporte:** en el Configurador de paquete, el ícono de calendario de «Vigencia desde/hasta» no abría el
selector; solo se podía escribir la fecha.

**Causa:** el configurador vive en un `app-drawer`, que se desliza con `transform`. Un ancestro con
`transform` pasa a ser el bloque contenedor de los `position: fixed`, así que el popup del selector (fixed,
calculado contra la ventana) se pintaba desplazado, fuera de la pantalla.

**Corrección (F):** `EtnDateInputComponent` busca ese ancestro (transform, filter, perspective, contain…) y
resta su esquina al posicionar el popup. Sirve para cualquier selector de fecha dentro de un drawer.

**Para validar en UAT:** Paquetes → Nuevo paquete → pulsar el ícono de calendario de ambas vigencias → abre
junto al campo; elegir un día lo carga.

---

## INC-26 — «Guardar» desaparece en «Requisitos previos»

[Documento](https://drive.google.com/file/d/14PkG35BdxA6GxvugKb3DMHyjRDhMgNJI/view) · Incidencia funcional · Media

**Reporte:** al editar un servicio y abrir «Requisitos previos», solo quedaba «Cancelar»; para guardar había
que volver a «Datos». **Esperado:** «Guardar» visible en ambas pestañas.

**Causa:** el pie del drawer mostraba «Guardar» solo con la pestaña «Datos» activa.

**Corrección (F):** «Guardar» se ve en las dos pestañas. Si falta el nombre, vuelve a «Datos», donde está el
error. Los requisitos se guardan con «Agregar requisito»: si hay uno escrito (o con documento) sin agregar, se
avisa y no se guarda, para no perderlo en silencio; el aviso se apaga al agregarlo o borrarlo.

**Para validar en UAT:** Servicios → Editar → «Requisitos previos» → aparece «Guardar» y guarda. Escribir un
requisito sin agregarlo y pulsar «Guardar» → aviso.

---

## INC-27 — Logo, firma y sello fallan con 413

[Documento](https://drive.google.com/file/d/1LYMnh53RUhVmLy68oA4kAazncyikmT93/view) · Incidencia funcional · Alta

**Reporte:** en el editor de plantillas, un logo PNG de 1,75 MB (dentro de los 2 MB que anuncia la pantalla)
fallaba con «413 Request Entity Too Large» de Nginx, mostrado como código HTML. Igual con firma y sello.

**Causa:** el `nginx.conf` del contenedor del front no fijaba `client_max_body_size`, así que regía el valor por
defecto de nginx (1 MB) y la subida se cortaba antes de llegar al backend (que admite 10 MB por archivo y 30 MB
por petición). El interceptor de errores mostraba como texto cualquier cuerpo de error, aunque fuera HTML.

**Corrección (F):**
- `nginx.conf`: `client_max_body_size 30m`, igual que el `max-request-size` del backend.
- Interceptor global: un cuerpo de error en HTML ya no se muestra; el 413 tiene título y mensaje propios.
- Editor: el 413 deja junto al activo un mensaje claro (y no duplica el aviso).

**Despliegue:** el cambio de `nginx.conf` entra con la imagen del front. Si en UAT hay otro proxy delante del
contenedor (el dominio público), su límite también debe ser de al menos 30 MB; si no, el 413 seguirá saliendo,
ahora con el mensaje claro.

**Para validar en UAT:** Plantillas → Editor → Activos de la sede → subir un logo, una firma y un sello de 1–2 MB.

---

## INC-28 — PDF del contrato con interlineado excesivo

[Documento](https://drive.google.com/file/d/1OCkOg6L0hEzsjJrr3zlx1w-F0aKVQcSu/view) · Incidencia funcional · Alta

**Reporte:** el PDF del contrato de concesión (vista previa con BORRADOR) salía con huecos enormes entre cada
renglón, incluso dentro de un mismo párrafo, y un documento corto ocupaba varias páginas.

**Causa:** la plantilla está escrita como texto con saltos de línea (`<br>`) y lleva el logo arriba. `HTMLWorker`
la convierte en UN solo párrafo con el logo dentro, y el interlineado es uno por párrafo: el ajuste que reserva el
alto del logo para que el margen no lo corte (~100 pt) se aplicaba a todos los renglones del contrato.

**Corrección (B):** `DocumentRenderService.splitImageLines` parte en renglones el párrafo que lleva una imagen;
solo la línea del logo reserva su alto y el texto vuelve a su interlineado normal. Se conservan los renglones en
blanco, la alineación y las sangrías. Las plantillas con párrafos propios (`<p>`, `<div>`) no cambian. Tests: +1.

**Pendiente conocido (no reportado):** HTMLWorker calcula el interlineado sobre 12 pt aunque el texto sea más
chico (un texto de 9–10 pt queda algo holgado). Se puede afinar si QA lo observa.

**Para validar en UAT:** Plantillas → plantilla 11 (contrato de concesión) → Vista previa PDF → interlineado
normal y el logo entero arriba.

---

## INC-23 — Tarifas aprobadas sin acciones; no se puede cambiar el precio

[Documento](https://drive.google.com/file/d/1NjGsRqnKf3KIBhrvxQTAJf8kPMMWv0M_/view) · Incidencia funcional · Alta

**Reporte:** en Tarifas de servicios, las aprobadas no tenían botones en «ACC.». La Exhumación aprobada sin fecha
fin no se podía editar ni cerrar: si el servicio sube de precio, no había forma de actualizarla. **Esperado:**
acciones para cerrar la vigencia o registrar un nuevo precio desde una fecha.

**Causa:** una aprobada es inmutable (RN-009) y la pantalla solo ofrecía «Corregir» para las no aprobadas. Una
tarifa nueva del mismo concepto chocaba con la aprobada abierta: la 22 lo rechazaba al registrar y el EXCLUDE de
la BD al aprobar.

**Corrección:**
- **Nuevo precio** (B + F): en una aprobada, el botón 🏷 abre el formulario con el concepto cargado y la fecha
  desde mañana (o desde hoy si la vigente empezó antes), con un aviso de qué tarifa reemplaza. Va a la bandeja
  como cualquier tarifa. Al **aprobarse**, la aprobada que empezó antes se cierra el día anterior al nuevo precio
  (se graba antes de aprobar la nueva para que el EXCLUDE no salte). Una tarifa que empieza el mismo día o antes
  que la aprobada sigue siendo duplicada.
- **Cerrar vigencia** (B + F): botón 📅✕ en una aprobada que sigue abierta. Pide el último día: desde hoy, no
  antes del inicio y solo acorta. `POST /{id}/close` en espacios y servicios (permiso de edición).
- Tests: +7 (servicios 20, espacios 20).

**Para validar en UAT:** Tarifas de servicios → Exhumación aprobada sin fin → «Nuevo precio» con otro monto desde
mañana → aprobar en la bandeja → la anterior queda hasta hoy y la nueva desde mañana. «Cerrar vigencia» en una
aprobada → elegir una fecha → la vigencia muestra el fin.

---

## INC-21 — Pabellones y sectores: huecos, desplegable y plano

[Documento](https://drive.google.com/file/d/1gaMGaYITtrUHMPP0G5d9SCCtRUnGDTY-/view) · Incidencia funcional · Alta

**21-1 — Huecos superpuestos.** Con cara 1, filas 1–1 / col 1–1 y filas 1–2 / col 1–1, ambos incluían (1,1); se
guardaba sin aviso y la previsualización mostraba 57 en vez de 58.
- **Causa:** la capacidad del formulario sumaba el área de cada hueco (sin deduplicar ni mirar la cara); el back
  deduplicaba al calcular pero no rechazaba el solape.
- **Corrección:** F — la capacidad descuenta cada celda una vez y solo en su cara; un aviso en «Huecos» nombra los
  dos huecos y la primera celda común, y «Guardar» lleva a esa pestaña sin confirmar. B — `validateGaps` rechaza el
  solape en la misma cara (`ETN_SECTOR_GAP_OVERLAP`). Rangos contiguos o en otra cara se permiten. Tests: +2.

**21-2 — «Tipo de sector» desborda al desplazar.** El campo es un `<select>` nativo: la lista la pinta el navegador
fuera de la página y no sigue al campo cuando el modal se desplaza. **Corrección (F, componente compartido
`app-select`):** si un contenedor se desplaza con la lista abierta, la lista se cierra (QA aceptaba «conservar la
alineación o cerrarse»). Aplica a todos los desplegables del sistema.

**21-3 — Quitar el plano del pabellón.** Solo había «Abrir» y «Reemplazar». **Corrección:** botón «Eliminar archivo»
(también cuando el archivo ya no está disponible) con confirmación; suelta la referencia del pabellón (B: clave vacía
= sin plano), borra el archivo del storage y limpia el uploader.

**Para validar en UAT:** Editar sector → Huecos → el caso de QA muestra el aviso y no guarda; Previsualización → 58.
Nuevo sector → abrir «Tipo de sector» y desplazar con la rueda → la lista se cierra. Pabellón con plano → «Eliminar
archivo» → confirmar → queda sin plano.

---

## INC-16 — Plantillas prediseñadas que QA no ve (reabierta)

Reabierta como «mejora en la plantilla de documentos»: en UAT las plantillas no aparecían prediseñadas.

**Causa:** `DefaultTemplateProvisioner` crea las estándar (contrato de cesión y propuesta comercial, publicadas)
solo en empresas que **nunca** tuvieron una plantilla de ese tipo, para no pisar lo redactado ni dejar dos
contratos publicados. La empresa de QA ya tenía plantillas propias, así que nunca las recibió.

**Corrección:** botón **«Plantilla estándar»** en Plantillas (permiso de alta). Se elige contrato de cesión o
propuesta comercial y se crea como **borrador** de empresa (vale para todas las sedes), sin tocar lo existente;
se abre en el editor para revisarla y publicarla. Si ya hay un contrato publicado, al publicar la estándar el
sistema lo nombra y pide enviarlo a la papelera (una sola vigente por tipo). B:
`POST /document-templates/standard?kind=CONTRACT|PROPOSAL`. Tests: +1.

**Para explicar a QA:** las prediseñadas se crean solas en empresas nuevas (o sin plantillas de ese tipo); en una
empresa que ya tiene las suyas se piden con «Plantilla estándar».

**Para validar en UAT:** Plantillas → «Plantilla estándar» → Contrato → se abre el borrador con logo, partes,
cláusulas y firmas → enviar a la papelera el contrato propio → publicar la estándar.

---

## INC-18-2 — Sedes «No operativa» en los filtros

Parte nueva de la 18 (planilla): los filtros de sede mostraban sedes «No operativa» en los distintos módulos.

**Causa:** la regla existía (RN-014: una sede no operativa queda fuera del selector) pero casi ninguna pantalla la
aplicaba: de 45 selectores de sede solo uno filtraba.

**Corrección (F):** las pantallas que operan dejan de ofrecer sedes «No operativa»: espacios (inventario, plano,
disponibilidad), catálogo (tarifas, tipos de espacio, pabellones), contratos, cotizaciones, pagos (caja, cobranza,
facturación, conciliación, planes de pago), mantenimiento, servicios funerarios, solicitudes, servicios, paquetes y
plantillas. Se mantienen todas en Configuración (Sedes, Configuración general, Series) y en las vistas históricas
(historial de espacios, reportes, dashboard, auditoría) para poder consultar datos pasados. Servicios, Paquetes y
Plantillas usan el listado de sedes de partner-core (sin ese dato) y lo cruzan con los perfiles
(`EtnSedesOperativasService`).

**Para validar en UAT:** Configuración General → Parámetros → marcar una sede como no operativa → no aparece en los
selectores de Inventario, Tarifas, Caja, etc.; sí en Sedes y en Reportes.

---

## INC-29 — Pabellones sin sectores habilitados para el tipo

[Documento](https://drive.google.com/file/d/1DHNaizVFBd4oh8J29ACJp2LwdPMV58ZO/view) · Incidencia funcional · Alta

**Reporte:** al elegir «Nicho individual estándar» en Nuevo espacio, el selector ofrecía «Pabellón San José» aunque
sus sectores no tienen ese tipo habilitado. (El documento al principio era una copia del de la 26; se reescribió.)

**Corrección:** B — `GET /spaces/eligible-sectors` devuelve los sectores hoja donde el tipo se puede registrar, con
la MISMA regla que valida el alta (`SpacePositionValidator.typeFits`: familia del sector + tipos habilitados en el
sector o, si no declara, pabellones compatibles del tipo). F — al elegir el tipo, Pabellón y Sector muestran solo
ubicaciones compatibles; si no hay ninguna: «No hay sectores habilitados para este tipo de espacio. Configura su
compatibilidad antes de continuar». En edición se conserva la ubicación actual. Tests: +1.

**Nota:** un sector que no declara tipos admite cualquiera si el tipo tampoco declara pabellones compatibles (regla
de V113). Si QA espera que «sin tipos» signifique «ninguno», es un cambio de regla a decidir.

---

## INC-32 — RUC de persona jurídica sin validar el prefijo

[Documento](https://drive.google.com/file/d/1Po8-SDwzgHF-8w2_B9xfcxQ88UcUhH0R/view) · Incidencia funcional · Alta

**Reporte:** Nuevo titular → Persona jurídica → RUC `40283823921` sin advertencia. **Esperado:** 11 dígitos, empieza en
20 y dígito verificador válido; si no, impedir avanzar con «Ingrese un RUC de persona jurídica válido, de 11 dígitos
y que comience con 20».

**Corrección:** F — `legalRucError` (`F/ui/src/identity/identity-rules.ts`) en el formulario de titular: error al
escribir y «Siguiente» bloqueado. B — `IdentityRules.isValidLegalEntityRuc` en `HolderService.validateFormat`
(alta y edición), con el mismo mensaje. Tests: +1 (y el RUC inventado del test de alta jurídica pasa a uno real).

**Para validar en UAT:** Titulares → Nuevo → Persona jurídica → `40283823921` → mensaje y no avanza;
`20131312955` → avanza.

---

## INC-34-3 — Borrador de contrato a S/ 0.00

[Documento](https://drive.google.com/file/d/1dxHHSe98UiNyEDO5yHZt_c3GsL8EICyq/view) · Incidencia funcional · Media

**Corrección:** el precio pactado debe ser mayor que cero. F — error «El precio pactado debe ser mayor que cero» y no
se avanza del paso 2. B — `@DecimalMin` exclusivo en `ContractRequestDTO.agreedPrice` (antes admitía 0). Tests: +1.
La 34-1 (moneda de sede) se corrige aparte, más abajo; la 34-2 (espacio «Disponible» con borrador) la descartó QA.

---

## INC-31 — Historial exportado con códigos técnicos

[Documento](https://drive.google.com/file/d/1urxv3ce6thfkHT74rwfgbQw3WtfkTFaK/view) · Incidencia funcional · Alta

**Reporte:** el PDF del historial de NIC-SJ-A-001 mostraba SPACE_CREATED, RESERVATION_HOLD, STATUS_CHANGE…, detalles
vacíos y un cambio de estado que solo decía «inhabilitar». Fechas también según la zona configurada.

**Corrección (B, `SpaceHistoryService.buildPdf`):** el evento sale traducido (las mismas etiquetas de la pantalla; un
tipo sin traducción muestra su código, nunca falla); el detalle dice «Estado: Disponible → Inhabilitado» o
«Capacidad: X → Y» cuando el evento lo registró, más el motivo, y «Sin observaciones registradas» si no hay nada; las
fechas van en la zona del usuario (`UserZoneResolver`) y no en UTC. El hash de integridad no cambia. Tests: +3.

**Para validar en UAT:** Historial de espacios → NIC-SJ-A-001 → Exportar PDF.

---

## INC-33 — El contrato no recupera los beneficiarios del titular

[Documento](https://drive.google.com/file/d/15LVmLky1v9XvJdFb4Ilto4HQCkZYXHYS/view) · Incidencia funcional · Alta

**Reporte:** el borrador del titular Jose Manuel Rodriguez decía «El contrato no tiene beneficiarios vinculados» aunque el
titular tenía una beneficiaria registrada y validada (Ana Maria). **Esperado:** recuperar los beneficiarios del titular
al asociarlo y reutilizar sus datos, completando porcentaje y prelación al vincularlos.

**Causa:** el contrato no los traía: había que «Agregar» y elegir cada beneficiario a mano. Porcentaje y prelación son
del contrato, no del beneficiario del titular.

**Corrección:**
- B — al crear el contrato se vinculan los beneficiarios vivos del titular: prelación en orden (1, 2, 3…) y el
  porcentaje restante hasta 100 en partes iguales (el último absorbe el redondeo: 33,33 / 33,33 / 33,34). Nuevo
  `POST /contracts/{id}/beneficiaries/from-holder` para los borradores que ya existían: vincula los que falten con la
  prelación siguiente y el porcentaje que quede. Todo editable mientras sea borrador. Tests: +3.
- F — en la pestaña Beneficiarios de un borrador: «El titular tiene N beneficiario(s) designado(s) sin vincular» con
  el botón «Vincular beneficiarios del titular».

**Pendiente conocido:** si en un borrador se cambia el titular, los beneficiarios del anterior siguen vinculados (se
quitan a mano). Se puede automatizar si QA lo pide.

**Para validar en UAT:** el borrador de QA → Beneficiarios → aviso con 1 pendiente → «Vincular» → Ana Maria al 100 %,
prelación 1. Un contrato nuevo de un titular con beneficiarios ya nace con ellos vinculados.

---

## INC-34-1 — Sede sin moneda: borrador que no se podía activar

Incidencia funcional · Media

**Corrección (avisar y bloquear al iniciar):** B — `ContractService.resolveCurrencyId` rechaza crear el contrato si ni el
formulario ni la sede traen moneda (`ETN_CONTRACT_BRANCH_CURRENCY`, «La sede no tiene moneda configurada…»); antes quedaba
un borrador que la activación rechazaba después. F — al elegir la sede, el paso 1 muestra el aviso y no deja avanzar.
Tests: 1 reescrito.

**Para validar en UAT:** nuevo contrato con una sede sin moneda → aviso bajo «Sede» y «Siguiente» deshabilitado.

---

## INC-30 — Precio de lista del espacio escrito a mano

Incidencia funcional · Alta

**Corrección (registrar sin precio):** el precio de lista ya no se escribe: es el de la tarifa APROBADA y vigente hoy del
tipo (la del pabellón si existe, si no la general; mismo criterio que `SpaceTypeRate.currentFor`), con su moneda. Sin
tarifa vigente el espacio se registra sin precio. B — `FuneralSpaceService` lo impone al crear y al editar (ignora el
valor que llegue). F — campo de solo lectura con el aviso de la tarifa aplicada o «se registra sin precio». Tests: +1.

**Fuera de alcance:** importación por Excel y generación por grilla no se tocaron.

---

## INC-35 — Escanear el QR del espacio para verlo

Incidencia funcional · Media. Reemplaza a la 35 anterior (historial con borradores), descartada.

**Corrección (solo personal con sesión):** el QR ya no lleva solo el token: lleva el enlace
`<app>/espacio-qr/<token>`. Al abrirlo se pide iniciar sesión si hace falta, el front resuelve el token con
`GET /spaces/by-qr/{token}` (solo espacios de la empresa del usuario; 404 si no) y abre el historial del espacio.
Tests: +1.

**Importante:** los QR impresos antes de este cambio contienen solo el token y hay que reimprimirlos.

**Para validar en UAT:** Inventario → QR de un espacio → escanear con el celular → abre el historial de ese espacio.

---

## INC-36 — Historial exportado: textos y zona horaria

Incidencia funcional · Media

**Corrección (B, `SpaceHistoryService`):** cabecera «Generado el … (hora de <zona>)», columna «Fecha (<zona>)», detalle
«Estado: de X a Y» / «Capacidad: de X a Y» (la flecha no existe en la fuente del PDF), y detalle para el alta manual,
la importación y la importación por lote. Tests: 4.

---

## INC-37 — «Espacio funerario» ofrece espacios sin contrato o fuera de servicio

Incidencia funcional · Media (requerimiento 37, vinculación difunto–espacio–contrato)

**Reporte:** en Registrar inhumación se podía elegir un espacio sin contrato (MAU-01) y la operación fallaba recién al
ejecutar con «Conflicto: El espacio MAU-01 no tiene un contrato vigente». **Causa:** el selector cargaba los primeros 200
espacios de la empresa sin filtro; solo la ejecución comprobaba contrato y estado (patrón P22).

**Corrección:**
- B — `GET /spaces?forInterment=true` (`FuneralSpaceSpecification.intermentEligible`): contrato ACTIVE sobre el espacio,
  ni inhabilitado ni en mantenimiento y con plaza libre (sin capacidad cuenta 1, como la ejecución). La ejecución sigue
  validando todo.
- F — el selector pide ese filtro y lo explica debajo («Solo aparecen espacios con contrato vigente…»).
- En `eternium_qa` (empresa 2) quedan 24 de 181 espacios; los «OCCUPIED 0/n» entran: vendidos sin inhumados.

**Para validar en UAT:** Inhumaciones → Registrar inhumación → MAU-01 (sin contrato) ya no aparece en «Espacio funerario».

---

## INC-39 — Los espacios no se filtran por la sede de la orden

Incidencia funcional · Media

**Corrección:** F — al elegir la orden se recargan los espacios de su sede (se conserva el que fija la orden). B — la
ejecución rechaza un espacio de otra sede que la orden (`ETN_ACT_ORDER_OTHER_BRANCH`, queda en la bitácora de rechazos).
Tests: +1.

**Para validar en UAT:** elegir una orden de la Sede Norte → solo aparecen espacios de esa sede.

---

## INC-38 — Fecha programada anterior a la defunción; no queda en el acta

Incidencia funcional · Media

**Corrección:**
- F — el selector de «Fecha programada» empieza el día de la defunción; si se escribe una anterior: «La fecha programada no
  puede ser anterior a la fecha de defunción (dd/mm/aaaa)» y no se puede ejecutar.
- B — misma regla (`ETN_INTERMENT_SCHEDULED_BEFORE_DEATH`), comparando el día en la zona del usuario. `scheduledAt` entra
  en el contenido firmado del acta (y en su hash) y en el PDF como «Programada para». Las actas anteriores no cambian.
  Tests: +2.

**Para validar en UAT:** programar una fecha anterior a la defunción → error. Con una fecha válida → el acta (detalle y
PDF) muestra «Programada para».

---

## INC-34 (nueva) — Nuevo precio de una tarifa sin el precio registrado

Incidencia funcional · Media

**Reporte:** al modificar una tarifa desde «ACC.», el campo de precio no muestra el precio registrado.

**Causa:** «Corregir» (no aprobadas) ya cargaba el precio; «Nuevo precio» (🏷, aprobadas, INC-23) abría el campo
vacío y el precio vigente solo aparecía en el aviso de arriba.

**Corrección (F, `TarifasComponent.openNewPrice`):** el campo arranca con el precio registrado, en espacios y en
servicios; el usuario lo cambia y se envía a la bandeja como antes. Sin cambios en el backend.

**Para validar en UAT:** Tarifas → una aprobada → 🏷 «Nuevo precio» → el campo muestra el precio vigente.

---

## INC-40 — Beneficiario: «Es menor de edad» contra la fecha de nacimiento

Incidencia funcional · Media

**Reporte:** el registro de beneficiarios no validaba la fecha de nacimiento contra la opción «Es menor de edad».

**Causa:** la opción y la fecha eran independientes. El backend tomaba «menor» si cualquiera de las dos lo decía, así
que un adulto marcado como menor quedaba como menor (y le pedía tutor); en pantalla se podía marcar lo contrario a la
fecha.

**Corrección:**
- F — con fecha de nacimiento, el interruptor se fija solo según la edad, queda deshabilitado y muestra «Se calcula
  con la fecha de nacimiento (N años)». Sin fecha sigue siendo manual. Al editar uno antiguo se recalcula (un menor
  que ya cumplió 18 deja de serlo).
- B — `BeneficiaryService.resolveMinor`: con fecha, la edad decide y una opción declarada que la contradice se
  rechaza (`ETN_BENEFICIARY_MINOR_MISMATCH`, «…el beneficiario tiene N años»). Sin fecha vale lo declarado. Tests: +2.

**Para validar en UAT:** Titulares → detalle → Beneficiarios → nuevo → fecha de hace 30 años: el interruptor queda
apagado y bloqueado; fecha de hace 10 años: se enciende y pide tutor.

---

## INC-42 — La alerta de vencimiento no muestra un contrato dentro de los 90 días

Incidencia funcional · Media

**Reporte:** un contrato que vence dentro del plazo de 90 días no aparece en «Alertas de vencimiento» de Contratos.

**Causa:** el panel listaba solo las filas de `contract_expiry_alerts`, que crea el job diario de las 06:25
(`ContractExpiryJob`). Un contrato que entraba en la ventana (activado hoy, o con fin dentro de 90 días) no salía
hasta la corrida siguiente, y nunca si el servidor no estaba encendido a esa hora (en la base local el job no corre
desde el 11/10). Además seguían saliendo filas de contratos ya renovados o vencidos.

**Corrección:**
- B — `ContractService.expiryAlerts` lee en vivo los contratos ACTIVOS de la empresa que vencen entre hoy y hoy+90
  (la misma consulta del tablero ejecutivo), ordenados por vencimiento. La fila del job, si existe para ese
  vencimiento, solo aporta las marcas de aviso; sin ella van en null. La ventana de 90 queda en
  `ContractExpiryAlertDTO.ALERT_DAYS` y la usa también el job. Los avisos (portal, campana, escalamiento) siguen
  saliendo solo del job. Tests: +1.
- F — la lista se identifica por contrato (la alerta puede no tener id todavía).

**Para validar en UAT:** activar un contrato con fecha fin dentro de 90 días → Contratos → «Alertas de
vencimiento»: aparece en el momento, con su semáforo.

---

## INC-43 — La notificación de vencimiento sale en inglés y con fecha ISO

Incidencia funcional · Media

**Reporte:** en Notificaciones, el aviso del contrato CON-CHIC-0000002 decía «Contract CON-CHIC-0000002 expires on
2027-01-15…», con el sistema en español.

**Causa:** el aviso lo arma `ContractExpiryJob` (06:25), fuera de una petición HTTP. Sin `Accept-Language`,
`messages.get(clave)` toma el idioma por defecto de la JVM, que en el servidor de UAT es inglés. La fecha se pasaba
con `String.valueOf(LocalDate)` (ISO). Lo mismo le pasaba a la fecha del aviso al titular en el portal.

**Corrección:**
- B — `ContractExpiryJob.notifyStaff` arma título y mensaje por destinatario, en su idioma
  (`RecipientLocaleResolver`: preferencia del usuario → idioma de la empresa → español) con `messages.get(locale, …)`.
  Las fechas (aviso al staff y al titular) salen con `ExportDates.format` (dd/mm/aaaa). Tests: +1.

**Ojo:** la notificación que QA ya tiene se guardó en inglés y no cambia; además esa alerta ya quedó sellada, así que
el job no la vuelve a enviar. Para validar hace falta un contrato nuevo que entre en la ventana de 90 días.

**Para validar en UAT:** activar un contrato con fecha fin dentro de 90 días → al día siguiente (06:25) la campana
muestra «El contrato … vence el dd/mm/aaaa».

**Pendiente (mismo patrón, no reportado):** `ExceptionSlaJob` (conciliación) y el escalamiento de ceremonias también
arman textos fuera de una petición con el idioma por defecto.

---

## INC-44 — La renovación completada no genera evento en el historial del espacio

Incidencia funcional · Media

**Reporte:** la renovación manual de CON-CHIC-0000002 (15/01/2027 → 15/01/2028) figura «Completada», pero el historial
de NIC-SM-B-102 sigue con 2 eventos (Importación y Contrato activado).

**Causa:** la activación, la cancelación y la transferencia de un contrato escriben en `space_events`; la renovación
(`ContractService.applyRenewal`, por donde pasan la manual y la tácita) solo dejaba el historial del contrato.

**Corrección:**
- B — `applyRenewal` agrega el evento `CONTRACT_RENEWED` al espacio con `contractNumber`, `previousEndDate` y
  `newEndDate` (dd/mm/aaaa). El PDF del historial lo detalla («Contrato X: vigencia extendida del … al …»). Etiquetas
  `etn.history.event.*` y `export.enum.spaceEventType.*` en es/en/pt; en el portal sale como hito de contrato.
  Tests: +2.
- F — Historial: «Contrato renovado» y las etiquetas «Vigencia anterior hasta» / «Nueva vigencia hasta».

**Ojo:** las renovaciones completadas antes del cambio no generan el evento hacia atrás.

**Para validar en UAT:** completar una renovación nueva → Espacios → Historial del espacio: aparece «Contrato
renovado» con las dos vigencias y el contador de eventos sube en uno.

---

## INC-45 — «No tiene permisos sobre este registro» al vincular un documento en la transferencia

Incidencia funcional · Alta

**Reporte:** un usuario ADMIN (228/228 permisos) recibía «Acción no permitida — No tiene permisos sobre este registro»
al pulsar «Vincular» en el checklist documental de la Transferencia #6. QA sospechaba que el permiso iba ligado al
creador del registro.

**Causa:** no era el rol. La fila pedía teclear un «Id doc.» (el id interno del documento, que nadie conoce) y el
backend, si ese id era de un documento de otra empresa, respondía con `etn.common.wrongCompany`, que se lee como
falta de permisos. Además, «Vincular» no cumplía nada: un requisito se da por cumplido solo si el **contrato** tiene un
documento vivo del tipo pedido; vincular por id solo guardaba algo en el caso de la sucesión intestada (patrón P2 de
la bitácora).

**Corrección:**
- F — en las filas sin cumplir, el campo «Id doc.» y «Vincular» se reemplazan por «Subir y vincular»: el archivo se
  sube como documento del contrato con el tipo del requisito y se vincula en el acto; la fila pasa a cumplida.
- B — `TransferService.attachDocument`: un documento que no es de la empresa o del contrato responde «El documento no
  pertenece al contrato de la transferencia», no «sin permisos». Tests: +2.

**Para validar en UAT:** Contratos → Transferencias → Transferencia #6 → en un requisito sin cumplir, «Subir y
vincular» → elegir un archivo: el requisito queda en verde con el documento. Con eso se puede seguir el flujo (y
validar la INC-02 de notificaciones).

---

## INC-46 — El simulador de plan de pagos acepta inicial 100 % con cuotas de S/ 0.00

Incidencia funcional · Media

**Reporte:** en el detalle de la cotización, «Simular plan» con inicial 100 % generaba un plan con todas las cuotas
en S/ 0.00.

**Causa:** el front solo rechazaba una inicial **mayor** que 100 y el back solo controlaba el mínimo
(`sales.min_down_payment_pct`). Con el 100 % no queda saldo que financiar: eso es una venta al contado, no un plan.

**Corrección:**
- B — `QuotationService.createPaymentPlan` y `simulateLateFee` rechazan una inicial ≥ 100 %
  (`ETN_QUOTE_DOWN_TOTAL`, «Con una inicial del 100 % no queda saldo para cuotas…»). Tests: +2.
- F — el campo «Inicial (%)» marca error desde 100 y la ayuda dice «Mínimo N % y menor a 100 %».

**Para validar en UAT:** Cotizaciones → detalle → simulador: inicial 100 → el campo queda en rojo y no deja simular;
con 99,99 sí genera cuotas.

---

## INC-47 — El tablero del pipeline no tiene filtro por vendedor

Incidencia funcional · Media

**Reporte:** el tablero muestra todas las oportunidades juntas y la barra superior no tiene filtro por vendedor.

**Causa:** el back ya aceptaba `ownerUserId` en `GET /pipeline/board` (y en la exportación), pero la pantalla
nunca lo enviaba.

**Corrección:**
- F — selector «Todos los vendedores / <vendedor>» en la barra superior (pestañas Tablero y Conversión) con los
  miembros de la empresa. Filtra el tablero, la exportación de oportunidades y el embudo de la INC-49.

**Para validar en UAT:** Pipeline comercial → elegir un vendedor en la barra: el tablero muestra solo sus
oportunidades; «Exportar» desde el tablero exporta solo las suyas.

---

## INC-48 — Venta cobrada sin comisión; no hay dónde configurar la tasa

Incidencia funcional · Media

**Reporte:** la venta VEN5-0000001 (S/ 5,310.00) está cobrada, pero «Comisiones» dice «Todavía no hay comisiones
devengadas», y en «Metas y tarifario» no hay forma de configurar la tasa de comisión.

**Causa:** la comisión se devenga al **asignar definitivamente** la venta, con la regla vigente
(`commission_rules`). Si no hay regla no se devenga nada, y es a propósito: la falta de parametrización no bloquea la
venta. Pero las reglas solo tenían un `GET`: no existía pantalla ni endpoint para crearlas, así que ninguna empresa
podía tener comisiones.

**Corrección** (decisiones del usuario, 2026-11-03: regla por empresa con tipo de espacio opcional, sin dimensión
por vendedor; devengo retroactivo):
- B — `POST /pipeline/commission-rules` (nombre, % **o** monto fijo, tipo de espacio opcional, vigencia) y
  `DELETE /pipeline/commission-rules/{id}` (retira: deja de aplicar y conserva lo devengado). Al crear una regla
  que ya rige se devengan las ventas **asignadas** que no tenían comisión (`CommissionService.accruePending`, con
  el snapshot de la regla). `SaleLookupPort.assignedSales`. Tests: +5.
- F — «Metas y tarifario» → tarjeta «Reglas de comisión» (lista, «Nueva regla», «Retirar»). El vacío de
  «Comisiones» explica que se devengan al asignar la venta si hay una regla vigente.

**Ojo:** si VEN5-0000001 todavía no está en «Asignada» (cobrada pero sin «Asignar definitivamente»), la comisión
saldrá al asignarla, no al crear la regla.

**Para validar en UAT:** Metas y tarifario → «Nueva regla» (p. ej. 3 %, todos los tipos) → el aviso dice cuántas
comisiones se devengaron → «Comisiones»: aparece VEN5-0000001 con base S/ 5,310.00 y su monto.

---

## INC-49 — No hay reporte del % de conversión entre etapas

Incidencia funcional · Media

**Reporte:** no hay reporte ni gráfica del % de conversión entre etapas (Prospección → Contacto → Cotización →
Negociación → Reserva → Ganada).

**Causa:** el pipeline solo tenía la conversión final por vendedor (ranking: ganadas / cerradas).

**Corrección:**
- B — `GET /pipeline/funnel` (`PipelineReportService.funnel`): para las oportunidades creadas en el periodo (y del
  vendedor, si se filtra), cuántas llegaron a cada etapa abierta y a la Ganada, el % que pasó de la anterior y el %
  sobre el total. Una oportunidad «llegó» a una etapa si pasó por ella o por una posterior según su historial
  (saltarse una etapa la cuenta como pasada; una ganada pasó por todas; una perdida cuenta hasta la última etapa
  abierta que alcanzó). Tests: +2.
- F — pestaña «Conversión» con el embudo en barras, periodo (mes / año / todo) y el filtro de vendedor de la INC-47.

**Para validar en UAT:** Pipeline comercial → «Conversión»: una barra por etapa con «N · %» y, entre etapas,
«X % pasó de la etapa anterior».

---

## INC-50 — «Nueva meta» con alcance Sede no lista ninguna sede

Incidencia funcional · Media

**Reporte:** en Metas y tarifario → Nueva meta, con alcance «Sede», el campo obligatorio Sede solo muestra
«— Id de la sede —».

**Causa:** el modal declara el input `branches`, pero la pantalla del pipeline nunca se lo pasaba (patrón nuevo P37).
Además el back guardaba una meta de alcance Sede sin sede si el cliente no la mandaba.

**Corrección:**
- F — la pestaña carga las sedes operativas y se las pasa al modal. La columna «Alcance» dice a qué vendedor o sede
  apunta cada meta.
- B — `PipelineAdminService.createTarget` exige la sede (y que sea de la empresa) en el alcance Sede, y el vendedor
  en el alcance Vendedor. Tests: +4.

**Para validar en UAT:** Metas y tarifario → Nueva meta → alcance «Sede»: el selector lista las sedes; la meta creada
se ve como «Sede · <nombre>».

---

## Correcciones del 09/11 y 13/11 a incidencias ya registradas

- **INC-18 (reabierta, 09/11):** las sedes «No operativa» se podían elegir en Series y Correlativos. B e82707b
  impide crear, mover o reabrir una serie en una sede no operativa (RN-014, antes solo al emitir); F a4b30f1 las oculta
  del filtro y del modal (al editar se conserva la sede de la propia serie). Está en UAT, a la espera de la nueva
  prueba de QA.
- **INC-43 (13/11):** el motor de alertas armaba título y detalle en el idioma de la JVM. B 6bb2c2c: la campana y el
  correo salen en el idioma de cada destinatario, el evento en el de la empresa, R7 dice cuántos días faltan y el
  umbral se muestra sin ceros de relleno.
- **INC-46 (13/11):** con un descuento del 100 % la cotización quedaba en S/ 0.00 y se emitía con propuesta y PDF.
  B 07461cc: crear, editar, clonar y emitir rechazan un total cero (`ETN_QUOTE_ZERO_TOTAL`); F e32a9a5: el descuento
  se marca fuera de rango desde 100 mientras se escribe («El descuento debe ser de 0 a menos de 100 %…») y
  «Siguiente» queda deshabilitado.
- **INC-47 (13/11):** el detalle de la oportunidad mostraba `#id` cuando el asesor ya no era miembro activo.
  B f07cb53: el historial devuelve el nombre de cada usuario; F 1a1e12d lo usa y el selector de reasignación dice
  «Selecciona el nuevo asesor».

---

## INC-51 — El modal de inhumación no avisa que el difunto ya está inhumado

Incidencia funcional · Media

**Reporte:** al pulsar «Ejecutar inhumación» con un difunto que ya está inhumado, el modal se queda congelado y no
muestra ningún mensaje.

**Causa:** el back rechazaba la inhumación, pero el motivo solo salía en el aviso flotante y el panel de
comprobaciones no revisaba si el difunto ya ocupaba un espacio.

**Corrección:**
- F — el panel de comprobaciones marca «El difunto ya está inhumado en un espacio: no puede estarlo en dos a la vez»
  (y «El expediente no está confirmado») y bloquea la ejecución. Si el back rechaza, el motivo se ve dentro del modal.

**Para validar en UAT:** Inhumaciones y Exhumaciones → Ejecutar inhumación con la OS de un difunto ya inhumado: la
comprobación sale en rojo y el botón no se habilita.

---

## INC-52 — No hay integración con Google Calendar en la agenda

Incidencia funcional · Media

**Reporte:** el botón de calendario de «Próximos eventos» solo cambia a la pestaña «Agenda del día»: no abre Google
Calendar ni descarga un `.ics`.

**Corrección:**
- F — botón «Agregar al calendario» en Próximos eventos y en el detalle del evento, con dos opciones: «Agregar a
  Google Calendar» (abre el evento prellenado con difunto, responsable, OS y asistentes) y «Descargar archivo .ics
  (Outlook, Apple, celular)». «Exportar datos» también ofrece el `.ics` de los eventos que se ven.

**Para validar en UAT:** Agenda de Ceremonias → Próximos eventos → icono de calendario → Google Calendar abre el
evento con fecha, hora y sala; el `.ics` se abre en Outlook o en el celular.

---

## INC-53 — El comprobante no llega a Aceptado vía OSE

Incidencia funcional · Media

**Reporte:** el comprobante queda en Contingencia y nunca pasa a Aceptado.

**Causa:** con el modo simulado apagado, la emisión podía ir por Factiliza (`eternium.sunat.provider`), que no es el
emisor contratado.

**Corrección:**
- B — con el stub apagado, el único emisor es el facturador (`FacturadorSunatAdapter`); se elimina
  `eternium.sunat.provider`. Factiliza queda solo para consultar el padrón RUC (`FactilizaRucClient`, sin emisión).
  Tests: +4.

**Para validar en UAT:** emitir una boleta desde un pago: pasa a Aceptado con el CDR del facturador. Si queda en
Contingencia, revisar las credenciales del facturador en el servidor.

---

## INC-54 — No existe el tipo «Recibo por honorarios»

Respondida a QA sin cambios de código: el recibo por honorarios lo emite la persona natural que presta el servicio,
no la empresa. Boletas y facturas ya están cubiertas.

---

## INC-55 — El recibo de pago no se envía al correo del titular

Incidencia funcional · Media

**Causa:** el recibo quedaba en `PENDING` y ningún proceso lo enviaba.

**Corrección:**
- B — el recibo sale al confirmar la transacción del pago, al correo escrito en Caja o, si se dejó vacío, al correo
  principal del titular; queda `SENT` o `FAILED`. Tests: +5.
- F — en Caja, la ayuda del campo de correo dice «Si lo dejas vacío, el recibo se envía al correo del titular».

**Para validar en UAT:** Caja y Pagos → registrar un pago sin escribir correo → llega el recibo al correo del titular.

---

## INC-56 — El botón de cierre de la orden correctiva no completa la acción

Incidencia funcional · Media

**Causa:** el back exige `finalCost` (igual a la suma del desglose) y respondía 400; la pantalla no lo enviaba ni
mostraba el error, así que la orden seguía «En ejecución».

**Corrección:**
- F — al cerrar se envía el costo final y, si el back rechaza, el motivo se ve en el panel de cierre.

**Para validar en UAT:** Historial de Infraestructura → orden correctiva en ejecución → «Cerrar» con el desglose de
costos → la orden pasa a Cerrada con el costo final.

---

## INC-57 — Error 401 después de verificar el código del portal

Incidencia funcional · Media

**Causa:** detrás del proxy la API se publica bajo `/api`, pero las cookies de sesión del portal (`pat`/`prt`)
quedaban con `Path=/api/eternium/portal` sin el prefijo público: el navegador no las enviaba y `/me` y `refresh`
respondían 401.

**Corrección:**
- B — las cookies usan `app.cookie.path-prefix`, igual que las de partner-core. Tests: +3.
- F — si el código es correcto pero la sesión no se pudo iniciar, se dice así («El código es correcto, pero no se
  pudo iniciar la sesión…») en vez de dejar al usuario en la pantalla del código.

**Para validar en UAT:** entrar al portal con DNI y código: llega a Inicio sin 401.

---

## INC-58 — La cartera no ordena por monto adeudado ni filtra por fechas

Incidencia funcional · Media

**Corrección** (RF-017):
- B — la cartera devuelve, junto a los cuatro estados, el detalle de cuentas (contrato, titular, estado, días de
  atraso, vencimiento más antiguo, vencido, por vencer y total), de mayor a menor monto adeudado y acotado por el
  vencimiento de la cuota pendiente más antigua. La exportación pasa a ser ese detalle. Tests: +2.
- F — tabla «Detalle de cuentas» con filtro Desde/Hasta.

**Ojo:** el filtro de fechas solo acota el detalle; los cuatro estados de arriba siguen siendo la foto completa al
día (comentario DEV en la planilla).

**Para validar en UAT:** Reportes Financieros → Cartera → la tabla está ordenada por total adeudado; con Desde/Hasta
solo quedan las cuentas cuyo vencimiento más antiguo cae en el rango.

---

## INC-59 — El reporte de ingresos no filtra por tipo de servicio ni por pabellón

Incidencia funcional · Media

**Corrección:**
- B — el reporte y su exportación aceptan `pavilionId` (de `mv_income_consolidated`) y lo guardan en el registro de
  la descarga. El tipo de servicio es el **concepto del plan de pagos** (venta de espacio, servicio, mantenimiento
  perpetuo, otro), que ya se filtraba. Tests: +1.
- F — filtros «Tipo de servicio» y «Pabellón» en Ingresos.

**Para validar en UAT:** Reportes Financieros → Ingresos → elegir un pabellón: los totales y el Excel solo cuentan
lo de ese pabellón.

---

## INC-60 — Error 500 al emitir por series solapadas entre sedes

Incidencia funcional · Media

**Causa:** dos sedes con la misma serie (p. ej. B001) generaban el mismo número completo; la base lo rechazaba como
duplicado y la emisión respondía 500.

**Corrección:**
- B — el número completo es único por empresa: crear una serie que ya usa otra sede se rechaza con una sugerencia
  libre (`ETN_INV_SERIES_CODE_TAKEN`), y la emisión que chocaría con un número ya emitido devuelve un mensaje de
  negocio (`ETN_INV_SERIES_OVERLAP`: crear en la sede una serie propia, marcarla por defecto y volver a emitir).
  Tests: +3.
- F — el modal de serie avisa «La serie {{codigo}} ya la usa otra sede…».

**Para validar en UAT:** para que Chiclayo emita, crear su propia serie (p. ej. B002) y marcarla por defecto. Intentar
crear B001 en Chiclayo muestra el aviso.

---

## INC-61 — El titular no puede descargar su comprobante desde el portal

Incidencia funcional · Media

**Causa:** si la emisión automática del pago en línea falló y el personal emitió el comprobante a mano en
Facturación, la transacción seguía `FAILED` y el portal mostraba «Comprobante en emisión» sin descarga.

**Corrección:**
- B — al emitir el comprobante en Facturación se pone al día la transacción del portal. Tests: +1.
- F — «Mis documentos» lista los comprobantes de pago («Comprobante de pago B001-…») para descargarlos.

**Para validar en UAT:** Portal → Mis documentos → aparece el comprobante del pago y se descarga en PDF.

---

## INC-63 — El correo de confirmación de pago llega en inglés y con marca PartnerCore

Incidencia funcional · Media

**Causa:** el worker de notificaciones del portal usaba el idioma de la JVM, el aviso de pago no traía nombre ni fecha
y se enviaba con la plantilla genérica de PartnerCore.

**Corrección:**
- B — el correo sale en el idioma de la cuenta del portal, completa nombre y fecha, borra los marcadores sin valor y
  usa una plantilla propia con el nombre del cementerio y un botón al portal. Tests: +2.

**Para validar en UAT:** pagar una cuota desde el portal → el correo llega en español, con el nombre del titular, la
fecha y la marca del cementerio.

---

## INC-64 — Las alertas ejecutivas se muestran en inglés

Incidencia funcional · Media

**Causa:** las alertas disparadas antes de la corrección de idioma de la INC-43 conservaban título y detalle en
inglés.

**Corrección:**
- B — V147 pasa al español los textos ya grabados.
- F — título y detalle se arman en el idioma de quien mira, con el nombre traducido de la regla y «Valor actual … ·
  umbral …»; el modal de detalle y el de reglas reconocen R6 y R7 como reglas sembradas.

**Para validar en UAT:** Dashboard Ejecutivo → Alertas: todas en español; al cambiar el idioma del usuario cambian.

---

## INC-66 — El reporte de inhumaciones y exhumaciones muestra «INTERNAL_TRANSFER»

**Corrección:** B f3999c9 traduce traslado interno y externo en el reporte exportado; F 2f479b4 muestra «Traslado
interno» / «Traslado externo» en «Tipo de acta».

---

## INC-67 — La ficha del contrato en Auditoría muestra «ACTIVE»

**Corrección:** F 9083590 traduce el estado del contrato en la trazabilidad («Activo», «Pendiente de firma»…).

---

## INC-68 — La cartera no muestra la clasificación de estados del requerimiento

Incidencia funcional · Media

**Causa:** la cartera vigente (planes activos sin cuotas vencidas) no entraba al reporte, porque la cuenta de
cobranza solo se abre al vencer una cuota.

**Corrección:**
- B — V146 incluye la cartera vigente con su saldo por vencer y el reporte devuelve siempre los cuatro estados.
  Tests: +2.
- F — tarjetas **Vigente** (sin cuotas vencidas), **Vencida** (1–30 días), **En mora** (31–180) e **Incobrable**
  (más de 180), con su saldo por vencer.

**Para validar en UAT:** Reportes Financieros → Cartera → aparecen los cuatro estados aunque alguno esté en cero.

---

## INC-69 — La proyección de ingresos no muestra todos los meses

Incidencia funcional · Media

**Causa:** la proyección cubría solo el trimestre, cortaba el último mes en el día de hoy y omitía los meses sin
cuotas.

**Corrección** (RF-011):
- B — la ventana llega al cierre del mes doce, lista todos los meses (en cero si no hay vencimientos) y devuelve los
  acumulados a 1, 3, 6 y 12 meses. Tests: +1.
- F — tarjetas «A N meses» y la tabla con los 12 meses.

**Para validar en UAT:** Reportes Financieros → Proyección: 12 filas de mes y los cuatro acumulados.

---

## INC-70 — El Excel y el PDF no muestran el período ni el usuario

**Corrección:** B 63e1a62 agrega el período consultado y «Solicitado por» en la línea de información de la
exportación, junto a la fecha de generación (RN-008). Tests: +1.

**Para validar en UAT:** Reportes Financieros → exportar a Excel y PDF: la cabecera muestra el período y el usuario.

---

## Pendientes

- **INC-62 y 72 (Yape / Plin en el portal):** dependen de la pasarela que use la empresa y de que tenga esos medios
  contratados. Pospuestas por decisión del usuario.
- **INC-65 (reporte regulatorio para la municipalidad):** falta el formato que pide la municipalidad.
- **INC-71 (consulta de RUC con estados en inglés):** registrada el 16/11, sin corregir.
- **INC-41 (RENIEC):** sigue esperando la API de consulta.
