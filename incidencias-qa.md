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
| 18 | Validación insuficiente de RUC y teléfono en sedes | Configuración / Sedes | Alta | 20/10/2026 | Corregida — pendiente de validar en UAT | B eed61f2 · F f80e513 |
| 19 | Licencias duplicadas del mismo tipo; el adjunto persiste en el modal | Configuración General / Licencias | Alta | 20/10/2026 | Corregida — pendiente de validar en UAT | B bb297f7 · F 306cd6b |
| 20 | Tarifa con vigencia retroactiva | Configuración / Tarifas | Alta | 20/10/2026 | Corregida — pendiente de validar en UAT | B 8153a1f · F a999e63 |
| 22 | Tarifas duplicadas del mismo concepto y vigencia que luego no se pueden aprobar | Configuración / Tarifas | Alta | 21/10/2026 | Corregida — pendiente de validar en UAT | B 9301a67 |
| 23 | Tarifas aprobadas sin acciones; sin fecha fin no se puede cambiar el precio | Configuración / Tarifas | Alta | 21/10/2026 | Corregida — pendiente de validar en UAT | B 6e3fbb0 · F 691cadc |
| 24 | El tarifario exportado incluye tarifas rechazadas | Configuración / Tarifas | Alta | 21/10/2026 | Corregida — pendiente de validar en UAT | B 9d75661 |
| 25 | El ícono de calendario de la vigencia no abre el selector | Configuración / Paquetes | Media | 21/10/2026 | Corregida — pendiente de validar en UAT | F da9d35a |
| 26 | El botón «Guardar» desaparece en «Requisitos previos» | Configuración / Servicios | Media | 22/10/2026 | Corregida — pendiente de validar en UAT | F afceb2d |
| 27 | Logo, firma y sello fallan con 413 aunque pesan menos de 2 MB | Configuración / Plantillas | Alta | 22/10/2026 | Corregida — pendiente de desplegar y validar en UAT | F 06331df |
| 28 | PDF del contrato con interlineado excesivo | Configuración / Plantillas | Alta | 22/10/2026 | Corregida — pendiente de validar en UAT | B 7379f15 |

### Abiertas en la planilla (leída el 23/10/2026; actualizada el 26/10/2026), por facilidad

| N.° | Título (planilla) | Módulo | Prioridad | Estimación |
|---|---|---|---|---|
| 21 | Huecos superpuestos en una celda con capacidad inconsistente; desplegable «Tipo de sector» desbordado; no se puede quitar el adjunto del pabellón | Pabellones y sectores | Alta | Media |
| 29 | Sin detalle en la planilla (Espacios / Inventarios); su documento repite el de la 26 | Inventarios de espacios | Media | Por precisar con QA |
| 18-2 | Los filtros de sede muestran sedes «No operativa» en varios módulos (parte nueva de la 18) | Varios | Alta | Media (cada pantalla carga sus sedes) |
| 16 | Reabierta: «mejora en la plantilla de documentos» | Plantillas de documentos | Alta | Corregida, a la espera de aprobación (no se toca) |

---|---|---|---|---|
| 25 | El ícono de calendario de «Vigencia desde/hasta» no abre el selector | Paquetes | Media | Fácil |
| 24 | El reporte exportado incluye tarifas rechazadas; debe mostrar solo las aprobadas | Tarifas | Alta | Fácil |
| 22 | Permite tarifas duplicadas del mismo servicio y vigencia, que luego no se pueden aprobar | Tarifas | Alta | Fácil–media |
| 21 | El formulario de sectores tiene campos redundantes y configuración avanzada sin agrupar | Pabellones y sectores | Alta | Media (rediseño de formulario) |
| 16 | No permite publicar la plantilla pese a cumplir los obligatorios (reabierta: «mejora en la plantilla de documentos») | Plantillas de documentos | Alta | Por precisar con QA |

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

