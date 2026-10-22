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

### Abiertas en la planilla (leída el 22/10/2026), por facilidad

| N.° | Título (planilla) | Módulo | Prioridad | Estimación |
|---|---|---|---|---|
| 25 | El ícono de calendario de «Vigencia desde/hasta» no abre el selector | Paquetes | Media | Fácil |
| 24 | El reporte exportado incluye tarifas rechazadas; debe mostrar solo las aprobadas | Tarifas | Alta | Fácil |
| 22 | Permite tarifas duplicadas del mismo servicio y vigencia, que luego no se pueden aprobar | Tarifas | Alta | Fácil–media |
| 23 | Tarifas aprobadas sin acciones en «ACC.»; sin fecha fin no se puede cambiar el precio | Tarifas | Alta | Media (definir el flujo de «nueva vigencia») |
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
