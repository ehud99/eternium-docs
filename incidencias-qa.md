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
| 18 | RUC y teléfono de sedes (18-1); sedes «No operativa» en los filtros (18-2) | Configuración / Sedes | Alta | 20/10/2026 | Corregida — pendiente de validar en UAT | B eed61f2 · F f80e513, 2d29460 |
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
| 34 (nueva) | No se ve el precio registrado al modificar una tarifa desde Acciones | Configuración / Tarifas | Media | 26/10/2026 | **Pendiente** (el Excel la marca «Resuelto») | — |
| 40 | Beneficiarios: la fecha de nacimiento no se valida contra «Es menor de edad» | Contratos / Titularidad | Media | 28/10/2026 | Pendiente | — |
| 41 | RENIEC: Titulares valida cualquier DNI ficticio | Contratos / Titulares | Media | 28/10/2026 | Pendiente — falta decidir el comportamiento esperado | — |
| 42 | La alerta de vencimiento no muestra un contrato que vence dentro de 90 días | Contratos / Renovaciones | Media | 29/10/2026 | Pendiente | — |

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
