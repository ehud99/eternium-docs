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
