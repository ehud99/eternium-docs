# Incidencias pendientes — evaluación del flujo completo (2026-10-11)

> Evaluación **sin corregir nada**, recorriendo [`README.md`](README.md) fase por fase contra el código
> real (front + back) y la base `eternium_qa` (solo SELECT). Busca casos aún pendientes de los patrones
> de [`bitacora-ajustes.md`](bitacora-ajustes.md) §0 (P1–P23) y cualquier otra inconsistencia.
>
> **Cómo usarla:** al corregir una incidencia, márcala `[x]`, anota el commit y pasa la lección a la
> bitácora. Las rutas van abreviadas: `F/` = `eternium_frontend/libs/eternium/`,
> `B/` = `eternium_backend/src/main/java/com/partnertech/eternium/modules/`,
> `i18n` = `apps/shell/src/assets/i18n/es.json`, `msg` = `src/main/resources/messages.properties`.
>
> **Severidad:** **A** = bloquea el flujo o deja datos erróneos · **M** = confunde o es inconsistente ·
> **B** = cosmético.

## Resumen

| Fase | A | M | B | Total |
|---|---|---|---|---|
| T · Transversal | 3 | 7 | 2 | 12 |
| 1 · Configuración | 7 | 22 | 3 | 32 |
| 2 · Espacios | 5 | 6 | 1 | 12 |
| 3 · Ventas → Contrato | 8 | 26 | 4 | 38 |
| 4 · Pagos | 6 | 22 | 9 | 37 |
| 5 · Servicios Funerarios | 6 | 14 | 4 | 24 |
| 6 · Mantenimiento | 9 | 23 | 11 | 43 |
| 7 · Portal de Familias | 2 | 6 | 5 | 13 |
| 8 · Análisis | 1 | 3 | 0 | 4 |
| G · Guía (secciones 10–12) | 0 | 5 | 3 | 8 |
| **Total** | **47** | **134** | **42** | **223** |

**Progreso:** 2026-10-11 — Fase 6 (Mantenimiento) completa: las 43 F6 corregidas y marcadas con [x]. 2026-10-12 — Fase 7 (Portal de Familias) completa: las 13 F7 corregidas.

**Prioridad sugerida (las A que rompen dinero o datos):** F4-01, F4-02, F4-03 (moneda y doble facturación) ·
F5-02/03/04 (difunto ≠ el de la OS) · F2-01, F2-02 (espacios que quedan RESERVED/AVAILABLE mal) ·
F3-06 (transferencia con deuda en 0) · F6-01, F6-02, F6-03 (ejecución en campo) · F1-26 (plantillas ambiguas).

### Patrones nuevos que aparecen (proponer a la bitácora al corregir)

| # | Patrón | Ejemplos |
|---|---|---|
| P24 | **Texto que promete un comportamiento que no existe** («llega por push; si no confirma en 30 min se reenvía», «se notifica a Gerencia», «se enviará a Contratos (M03)», «PDF/A con firma digital», «escala al jefe de atención») | T-06, F1-05, F2-01, F5-19, F6-20, F7-102 |
| P25 | **Ids internos «#123» donde el usuario espera un nombre o un folio** (difunto, OS, cotización, usuario, plan, cuota) | F4-13, F5-10…F5-16, F5-22, F3-15, F3-30, F3-34, F6-30/31 |
| P26 | **Moneda ignorada**: importes formateados siempre en PEN, o moneda elegida a mano distinta de la del contrato/sede | F4-01, F4-05, F4-22, F1-15, F2-09, F3-19, F3-20 |
| P27 | **«Segundo aprobador» nominal**: el solicitante elige a otro usuario y queda aprobado sin que esa persona haga nada; el selector incluye al propio usuario | F5-06, G-02, G-03 |
| P28 | **Plurales fijos** «{{n}} tareas» → «1 tareas» (178 valores i18n) | T-03 |

---

## T · Transversal

- [ ] **T-01 · A · P20** — **El portal público (lo ve la familia) usa jerga técnica:** «FIFO», «webhook firmado/idempotente registrado», «SLA», «hash SHA-256», «enlace firmado». i18n `etnPortalPagos.subtitulo`, `.aviso.pasarela`, `.espera.nota`, `.proceso.nota`, `.pie.webhook`, `.imputacion.valor`, `etnPortalSolicitudes.sla.*`, `.slaCard.*`, `etnPortalEspacios.docs.hashTitle`. → Redactar para la familia: «se aplica a la cuota más antigua», «plazo de atención», «código de verificación del documento».
- [ ] **T-02 · A · P20** — **Referencias internas visibles:** «(M03)» en `etnDisponibilidad.confirmFormalizar.mensaje` y `.msg.formalizada`; «(RF-357/358)» en `etnCotizaciones.form.compararCuotaHint` (es/en/pt); «job KPI_SNAPSHOT (00:10)» y «job KPI_REFRESH» en `etnDashboard.grafico.sinSerieHint` / `.salud.vistasVacioHint`; «(doc 08)» y «append-only» en `msg` `etn.history.pdfAppendOnlyNote` (sale en el PDF del historial). → Quitar.
- [ ] **T-03 · M · P28** — **178 textos con plural fijo** («{{n}} tareas», «{{n}} pagos», «Se reanudaron {{n}} transferencias»): con n=1 sale «1 tareas». También en el back: `msg:3055` «Te quedan {0} intentos». → Plural ICU de Transloco (`{n, plural, one {…} other {…}}`) o claves `_one/_other`; en el back `ChoiceFormat`.
- [ ] **T-04 · A** — **Clave i18n inexistente:** `etnEspacios.sectores.msg.errorGuardar` (F/feature-catalogo-espacios/src/pabellones/components/sector-form-modal/sector-form-modal.component.ts:651 y sector-generate-modal.component.ts:142). Si el back no manda mensaje se muestra la clave cruda. → Crearla en es/en/pt.
- [ ] **T-05 · M · P20** — **Enums crudos en pantalla** (confirmados en la BD): historial del prospecto `item.status` (DRAFT, ACCEPTED…) `F/feature-ventas/.../prospecto-detalle-modal.component.html:143,160,177`; bitácora de reserva `event.eventType` (CREATED, PAYMENT_VALIDATED) `reserva-detalle-modal.component.html:91`; checklist de venta `documentType` (CONTRACT, DNI_TITULAR) `ventas.component.html:431,544`; vínculos de oportunidad «RESERVATION #12» `pipeline-detalle-modal.component.html:193`; auditoría `row.severity` (MEDIUM) `auditoria.component.html:750`; dashboard `job.runStatus` (SUCCESS), `integracion.healthStatus` `dashboard-ejecutivo.component.html:1146,1174`; pasarelas `evento.eventType` (charge.approved). → Traducir por clave.
- [ ] **T-06 · M · P24** — Programación de Mantenimiento: «La asignación llega al operario por push; si no confirma en 30 min, se reenvía» (`etnMtoProgramacion.asignacion.nota`). El back solo crea notificación interna + correo (`B/maintenanceplanning/application/MaintenanceTaskService.java:664-683`); no hay push ni reenvío. → Quitar o implementar.
- [ ] **T-07 · M · P21** — **Umbrales de mantenimiento escritos en los textos** aunque ya se sirven por `/thresholds`: «24 h» (`asignacion.subtitulo`, `sinAsignar24`, `tipoAlerta.UNASSIGNED_24H`), «48 h» y «5 vencidas» (`etnMtoSupervision.alertas.reglas`, `tipoAlerta.OVERDUE_ESCALATED_48H`), «tercera reprogramación» (`etnMtoProgramacion` reprogramación `nota`), «Tres rechazos en un mes» (`rechazo.aviso3`, `rechazo.tresRechazos`), «Bajo 70 %» (`pabellones.desc`; `SupervisionDashboardService.LEVEL_AMBER_FROM`). → Interpolar el valor vigente.
- [ ] **T-08 · M · P20** — Jerga de desarrollo en back-office: «job», «idempotente» (`etnMtoProgramacion.job.subtitulo`, `etnMtoConfig.frecuencias.sembrarTooltip`, `etnAtencion.tipos.sembrarTooltip`), «sync / cola offline / UUID» (`etnMtoEjecucion.*`), «log inmutable · retención 10 años», «Append-only», «el backend rechazará» (`etnReportes.generar.excedeCap`), «endpoint» (`etnAuditoria.filtro.textoPlaceholder`), «mv_kpi_ejecutivo» visible (`dashboard-ejecutivo.component.html:22`). → Lenguaje de usuario.
- [ ] **T-09 · M · P20** — Placeholders técnicos: `COLLECTION_D1` (cobranza plantillas-modal.component.ts:26), `BCP` (extracto-import-modal.component.ts:68), `ALL` (tax-configs-modal.component.ts:100). → Ejemplos legibles.
- [ ] **T-10 · M** — pt sin traducir: `etnCremaciones.externo.confirmar` «Confirmar traslado externo» (→ «translado»).
- [ ] **T-11 · B** — Formato de números con `toLocaleString('es-PE')` fijo (no sigue el idioma): `F/feature-analitica/src/models/reportes.model.ts:284`, `F/feature-portal/src/models/portal-comun.model.ts:99`, `auditoria.component.ts:445` (sin locale).
- [ ] **T-12 · B** — Fechas ISO dentro de mensajes del back (`newDate.toString()`): reprogramación y asignación de tareas (`msg:2940,2965,2973`), alertas de OT vencida. → dd/MM/yyyy con la zona del usuario.

---

## Fase 1 · Configuración

**Config. General, Sedes y Series**

- [ ] **F1-01 · A · P11** — La moneda de la sede lleva asterisco pero no es obligatoria en front ni back; sede sin moneda ⇒ espacios no cotizables. `F/feature-configuracion/.../parametros-tab.component.html:16-18`, `.ts:75`; `BranchProfileRequestDTO.currencyId` sin `@NotNull`. → `required` + `@NotNull`.
- [ ] **F1-02 · M · P9** — Vaciar RUC propio o moneda «se guarda con éxito» pero no cambia nada (el back solo copia no nulos). `B/configuration/.../BranchProfileService.java:125-130`. → Tratar vacío como borrar, o impedirlo y explicar.
- [ ] **F1-03 · M · P21/P11** — Tokens del patrón de nomenclatura copiados en el front y distintos del back (el front rechaza `{ROW:03}`, `{SEQ:05}`); «al menos un token» solo en el front. `configuracion-general.model.ts:150-160`; `BranchProfileService.java:54-55,205`. → Tokens servidos por el back y validación allí.
- [ ] **F1-04 · M · P23** — El aviso dice que cambiar moneda o formatos «afecta tarifas y contratos vigentes»; nadie lee `numberFormat/dateFormat/locale/timezone` y la moneda solo es default de lo nuevo. i18n ≈6916/6921. → Reescribir.
- [ ] **F1-05 · M · P24** — «Los cambios en RUC o moneda se notifican a Gerencia»: solo hay un `log.info("RF-025…")`. `CemeteryProfileService.java:78`. → Quitar o implementar.
- [ ] **F1-06 · M · input redundante** — El paso 2 de «Nueva/Editar sede» repite los parámetros de la pestaña Parámetros, sin nomenclatura ni confirmación de cambio crítico. `sede-form-modal.component.html:172-201`. → Una sola pantalla dueña.
- [ ] **F1-07 · M** — «Editar» sede solo abre la «extensión»: nombre, código y dirección no se corrigen; títulos técnicos «Editar extensión de sede». i18n ≈7056/7191. → Editar datos base y renombrar.
- [ ] **F1-08 · M · P20** — `ETN_SEQUENCE_MISSING` dice «tipo de documento ACT_INTERMENT en esta sede» (código crudo, sin nombre de sede). `msg:829,833`; `DocumentSequenceService.java:308,381`. → Tipo traducido + sede.
- [ ] **F1-09 · M** — Una sede sin ninguna serie no aparece en la tabla de Series, y el aviso «N tipos sin serie» no dice cuáles faltan. `series/series.component.ts:126,164-179`. → Grupo por sede viva con tipos faltantes.
- [ ] **F1-10 · M · P20** — Licencias «Otro (especificar)» pide un código técnico (`ITSE_ANEXO`) que luego sale crudo en tabla y exportación. `licencia-modal.component.ts:392-394`. → Descripción libre.
- [ ] **F1-11 · B · P11** — Horarios: el back admite varios rangos por día, la pantalla toma el primero y borra los demás al guardar. Facturador: regex y vencimiento distintos front/back; la guía dice que el RUC «se marca a mano» y hay botón «Verificar RUC». `horarios-tab.component.ts`; `facturador-credencial-card.component.ts:52,150,229`.

**Tipos de Espacio, Pabellones/Sectores, Tarifas**

- [ ] **F1-12 · A · P22/P11** — «Generar espacios» lista todos los tipos de la sede y el back no valida empresa/sede/familia: se puede generar mausoleos en un muro de nichos. `sector-generate-modal.component.ts:85`; `SpaceGridGenerationService.java:120-126`. → `requireCompatibleType` en `generate()` y filtrar.
- [ ] **F1-13 · A · P22** — Interruptor «Crear solo las celdas que faltan» no hace nada (el back lo ignora) y su ayuda promete que desactivado falla si hay espacios. `sector-generate-modal.component.html:100`; `GridGenerationRequestDTO.java:19-20`. → Quitar.
- [ ] **F1-14 · M · P11** — El sector acepta 500×500×2 celdas; el back genera máx. 20 000 y `ETN_GRID_TOO_LARGE` muestra el mensaje de «sector sin cuadrícula». `SpaceGridGenerationService.java:69,131-133`. → Mensaje propio + aviso.
- [ ] **F1-15 · A · P26** — Tarifas: el select «Moneda» muestra siempre «S/ PEN» pero envía la moneda de la sede; sin perfil usa `DEFAULT_PEN_CURRENCY_ID = 1`. `tarifas.component.ts:57,151,317-320,679-682`. → Moneda real de la sede, solo lectura.
- [ ] **F1-16 · M** — Una tarifa RECHAZADA no se puede corregir: el `PUT` de espacios existe sin botón; servicios no tiene `PUT`. `rate.service.ts:52`; `ServiceRateController`. → «Editar y reenviar».
- [ ] **F1-17 · M · P22** — «Pabellón» de la tarifa lista todos; el vínculo automático tipo↔pabellón no comprueba familia. `tarifas.component.ts:304-310`; `SpaceTypeRateService.java:304-323`. → Filtrar por familia y validar.
- [ ] **F1-18 · M · guía** — Guía desactualizada: paso 7 (asignar pabellón al tipo) ya no hace falta; `ETN_RATE_PAVILION` no existe (clave huérfana `ratePavilionIncompatible`, `msg:886`); «Generar espacios» está en Pabellones → sector hoja; usa enums crudos (NICHE, OPERATIONAL). `README.md:106-108,123,407-408`.
- [ ] **F1-19 · M · P20** — Errores con códigos: «tipología NICHE_WALL_SINGLE», «modo GRID». `SectorLayoutService.java:290,293,467`; `msg:3249-3250,3272`. → Etiqueta traducida.
- [ ] **F1-20 · M · input redundante** — La capacidad se pide en el tipo, en el sector (máx. 99) y en el asistente; el máximo del tipo no limita (se genera capacidad 10 en un tipo de máximo 1). `SpaceGridGenerationService.java:152-153`. → Default del tipo y rechazar mayores.
- [ ] **F1-21 · M · P9** — Aviso de sector sin moneda: «se guardará sin símbolo»; en realidad los espacios nacen sin moneda y no se pueden cotizar. i18n ≈7298. → Decir la consecuencia y enlazar a Parámetros.
- [ ] **F1-22 · M · P21** — Matriz tipología→familia copiada a mano en el front (`F/feature-catalogo-espacios/src/models/space-type.model.ts:36-64` vs `PavilionSector.FAMILIES_BY_KIND`). → Servirla desde el back.
- [ ] **F1-23 · B · P23/P11** — Tarjeta «Huecos» cuenta registros, no celdas; la cara de un hueco admite 2 en sector de una cara; textos «bi-grid-1x2», «viewBox» visibles. `sector-generate-modal.component.ts:98`; i18n ≈7476/7487.

**Servicios, Paquetes, Plantillas**

- [ ] **F1-24 · A · P11/P22** — Tres reglas distintas para componentes sin tarifa: el back publica sin motivo si el precio no supera la suma; el configurador bloquea siempre; la tarjeta dice «no puede publicarse» pero su botón publica. El motivo se exige incluso para guardar borrador. `ServicePackageService.java:222-236`; `paquete-configurador.component.ts:227,248-250`; `paquetes.component.html:182-221`. → Una sola regla en el back.
- [ ] **F1-25 · A** — El precio de los componentes se congela al guardar el borrador, no al publicar: aprobar la tarifa después no lo arregla. `ServicePackageService.java:211-216,382-395`. → Re-tomar precios en `publish`.
- [ ] **F1-26 · A** — Plantillas: `findPublishedTemplates` junta empresa y sede, los tres tipos de contrato y todos los idiomas; publicar una variante por sede, un arrendamiento o una versión en inglés rompe Ventas con `ETN_CONTRACT_TEMPLATE_AMBIGUOUS`, sin aviso al publicar. `GeneratedDocumentService.java:270-300`; `ContractService.java:137-138,720-731`. → La de sede tapa a la de empresa del mismo tipo e idioma; avisar en el checklist.
- [ ] **F1-27 · M** — La compatibilidad servicio↔tipo de espacio no la consulta ningún módulo y `addCompat` no valida empresa. `ServiceCatalogService.java:256-269`. → Validarla en la OS o marcarla informativa.
- [ ] **F1-28 · M · P11** — El front exige adjunto en cada requisito del servicio; el back no. `servicio-drawer.component.ts:162,381`. → Opcional.
- [ ] **F1-29 · M · P22** — Importación de plantillas: la ayuda dice «CONTRATO, ACTA, ORDEN…»; el back solo acepta `CONTRACT_PURCHASE` o el nombre completo. `DocumentTemplateImportExportService.java:85,142-144`.
- [ ] **F1-30 · M · P20** — El editor muestra la `fileStorageKey` interna y «183245 B». `plantilla-editor.component.html:305-310`. → Nombre y KB/MB.
- [ ] **F1-31 · M · P26** — Servicios y Paquetes usan la moneda funcional de la empresa; el back suma tarifas de sedes con otra moneda sin comprobarla. `ServicePackageService.java:222-226`.
- [ ] **F1-32 · B · P21/P20** — «mín. 2» componentes fijo en el front (`MIN_COMPONENTS` en back); nombres de tipo de documento de la BD sin traducir.

---

## Fase 2 · Espacios

- [ ] **F2-01 · A · P24** — Disponibilidad → «Formalizar» no crea reserva ni contrato: cierra el bloqueo como FORMALIZED y el espacio queda RESERVED sin bloqueo vivo; nada lo libera (el job solo mira bloqueos ACTIVE; la máquina de estados no tiene salida de RESERVED). El texto dice «Se enviará a Contratos (M03)». El contrato ya formaliza el bloqueo solo (`ContractService.java:1189`). `SpaceHoldService.java:133-149,190-203`; `FuneralSpaceService.java:79-84`. → Quitar el botón (o crear la reserva de verdad).
- [ ] **F2-02 · A** — Un espacio OCUPADO se puede dejar Disponible a mano: OCCUPIED → MAINTENANCE → AVAILABLE sin mirar ocupación ni contrato. `FuneralSpaceService.java:79-84,397-420`. → AVAILABLE solo con ocupación 0 y sin contrato vivo.
- [ ] **F2-03 · M · P22/P20** — Cambio de estado en lote ofrece RESERVED y OCCUPIED, que el back siempre rechaza con «AVAILABLE → RESERVED» crudo. `espacio-lote-modal.component.ts:143`; `msg:1021`. → Solo AVAILABLE/MAINTENANCE/DISABLED y traducir.
- [ ] **F2-04 · A · P9** — La importación guarda precio sin moneda ⇒ `ETN_QUOTE_NO_PRICE` aunque se vea precio (se repite bitácora 2.1). `SpaceImportExportService.java:262-283`. → `resolveCurrency` como en el alta.
- [ ] **F2-05 · A · P8** — Importación: «900,50» se guarda como 90 050 (`replace(",", "")`). `SpaceImportExportService.java:241`. → Misma regla que `parseEtnDecimal`.
- [ ] **F2-06 · M** — Importación y alta individual aceptan «OCUPADO» a mano; la regla «sector no operativo ⇒ espacio DISABLED» solo se aplica en «Generar espacios». `SpaceImportExportService.java:224-235`; `FuneralSpaceService.java:812-830`.
- [ ] **F2-07 · M · P20/P21** — La ayuda «Ver estructura de datos» no corresponde a la plantilla v2 (faltan `sector_code`, `cara`, `ancho_celdas`, `alto_celdas`; documenta enums crudos que el back ignora). `espacio-import-template-modal.component.ts:102-114`. → Generarla de `TEMPLATE_HEADERS`.
- [ ] **F2-08 · A** — Disponibilidad muestra, filtra y ordena por `space.price`; la cotización cobra primero la tarifa aprobada: el vendedor ve otro precio, y un espacio con solo tarifa sale «—». `disponibilidad.component.html:254`; `FuneralSpaceSpecification.java:57-60`. → `effectivePrice` en el DTO.
- [ ] **F2-09 · M · P26** — Inventario/Plano/Disponibilidad formatean con la moneda de la sede (o `'PEN'` fijo) ignorando la del espacio. `inventario.component.ts:177-181`, `plano.component.ts:200-204`, `disponibilidad.component.ts:171-175`.
- [ ] **F2-10 · M · P21** — «Bloquear 72h» fijo en textos aunque `settings.holdHours` es configurable. i18n `etnDisponibilidad.resultados.*`, `etnPlano.detalle.*`, `etnUi.sectorGrid.hold`, `etnReservas.kpi.activasDesc`.
- [ ] **F2-11 · M · P20** — Historial de Espacios: badge «M02/M03/M04/M05»; 10 de 15 tipos de evento reales sin traducción (`CONTRACT_ACTIVATED` ×27, `GRID_GENERATED`, `SALE_ASSIGNED`, `INTERMENT_EXECUTED`…); payload crudo («sectorId: 12 · created: 30», «from/to: AVAILABLE», «Tipo de espacio: 7»). `historial.component.html:151-154`, `.ts:312-357`.
- [ ] **F2-12 · B** — Endpoints sin pantalla (`/legal-restrictions`, `/holds/reassign`, `/holds/active`); la selección del lote no tiene el tope de 500 del back. `plano.component.ts:799`.

---

## Fase 3 · Ventas → Contrato

- [ ] **F3-01 · A · P9/P23** — El comparativo de la cotización usa `space.price` (el back congela primero la tarifa aprobada), sin IGV aunque dice «con IGV»; puede marcar como «menor costo» al espacio equivocado. `cotizacion-form-modal.component.ts:165-172`; `QuotationService.java:905-930`. → Vista previa de precio con `resolvePricing`.
- [ ] **F3-02 · A · P23** — Una cotización REFERENCIAL se ve igual que una en firme y cuenta en «Emitidas vigentes»; el front no usa `isFirm`. → Distintivo «Referencial (no habilita reserva)».
- [ ] **F3-03 · A · P11** — El simulador de la cotización acepta 1 cuota; el plan real exige 2–60: «Traer de cotización» falla después. `quotations/.../PaymentPlanRequestDTO.java:20-21`. → Mínimo 2 compartido.
- [ ] **F3-04 · A · P22** — «Nuevo expediente» lista reservas CONFIRMED con pago sin validar; el back responde `ETN_SALE_PAYMENT`. `venta.service.ts:135-141`; `SaleService.java:190`.
- [ ] **F3-05 · A · P20/P22** — Requisitos de la venta: el tipo documental es texto libre comparado por igualdad exacta (`ETN_SALE_DOC_TYPE`); Gestión Documental acepta cualquier tipo. En QA ya hay «DNI_TITULAR», «DNI», «Documento Personal». `ventas.component.html:527-533`; `DocumentService.java:168-193`. → Select de `DOCUMENT_TYPES` validado en el back.
- [ ] **F3-06 · A** — Transferencia: el monto de deuda se teclea y teclear 0 deja la deuda CLEARED y habilita la firma con cuotas vencidas; el back sabe consultar la deuda real solo si llega null (el front nunca manda null). No consulta el bloqueo de Cobranza que menciona la guía. `transferencia-detalle-modal.component.ts:93-104,433-441`; `TransferService.java:202-241`. → «Verificar deuda» contra Pagos; manual como excepción con permiso.
- [ ] **F3-07 · A · P21** — Versión de la política de consentimiento fija `'1.0'` en prospectos y titulares; no es la publicada en Consentimientos. `prospecto.model.ts:180`, `titular.model.ts:317`. → Que la ponga el back.
- [ ] **F3-08 · A · P11** — «Generar contrato» con tenencia Temporal/Renovable y plazo vacío envía `termValue: null` ⇒ `ETN_CONTRACT_TENURE`; unidad fija YEARS, sin tope, admite decimales. `ventas.component.html:360-367`. → Misma validación que el contrato directo.
- [ ] **F3-09 · M · guía** — «Generar contrato» no deja elegir plantilla aunque el back acepta `templateVersionId`; con varias publicadas queda bloqueado. `ventas.component.ts:444,484`.
- [ ] **F3-10 · M** — «Ir al proceso de venta» navega con `?reservationId=` que Ventas nunca lee. `reservas.component.ts:511-513`.
- [ ] **F3-11 · M · P22** — El back permite reservar desde cotización ISSUED o ACCEPTED; «Nueva reserva» solo pide ISSUED. `reserva.service.ts:101-108`.
- [ ] **F3-12 · M · P11** — Pago inicial validado solo > 0; `ETN_RESV_PAYMENT_MIN` responde un número sin moneda. `reserva-pago-modal.component.ts:121-134`; `msg:1713`.
- [ ] **F3-13 · M · P11** — Extender reserva: «La primera extensión la autoriza el Director» aparece cuando ya hubo una; el selector de Gerencia sale siempre y en la segunda no se exige (`ETN_RESV_EXT_GM_REQUIRED`). `reserva-extender-modal.component.ts:39-43,72-82,166`.
- [ ] **F3-14 · M** — Detalle de reserva: « h adicionales» sin número (se pasa `hours`, la clave usa `{{n}}`). `reserva-detalle-modal.component.html:68`.
- [ ] **F3-15 · M · P25** — Tarjeta de reserva «desde #123» (id de la cotización). `reservas.component.html:108,113`; `ReservationDTO` sin `quotationNumber`.
- [ ] **F3-16 · M · P21/P23** — «72 h» y umbral crítico de 6 h fijos; KPI «Por vencer ≤ 6 h» cuenta solo la página y afirma «alerta crítica enviada» sin comprobarlo. `reservas.component.ts:44,198-204`.
- [ ] **F3-17 · M** — En CONFIRMED el texto dice que no se puede liberar y no hay botón, pero el back lo permite. `ReservationService.java:445-447`.
- [ ] **F3-18 · M · P22** — Chips de estados que el back nunca asigna (PENDING_VALIDATION, REJECTED en cotización y reserva) — siempre 0. `cotizacion.model.ts:17-20`, `reserva.model.ts:7-18`.
- [ ] **F3-19 · M · P26** — Montos de cotizaciones y reservas siempre en PEN. `cotizaciones.component.ts:369-371`, `reservas.component.ts:274-276`.
- [ ] **F3-20 · M · P26** — `etn.transfers.debtBlocked` con «S/ {0}» fijo (`msg:1484`).
- [ ] **F3-21 · M · P20** — Chip «Snapshot tomado al cotizar · v123 / · space» (clave interna `priceListVersion`). `cotizacion-detalle-modal.component.html:26`.
- [ ] **F3-22 · M · P11/P21** — Simulador: inicial vacía cuenta como 0 % sin pista del mínimo (`sales.min_down_payment_pct`) ni del rango de cuotas. `cotizacion-detalle-modal.component.ts:89-95`.
- [ ] **F3-23 · M** — «Aprobar descuento» depende de `canDelete()`; el back exige editar. `cotizaciones.component.html:163`.
- [ ] **F3-24 · M · P11** — Fecha de nacimiento solo «no futura»; el back rechaza menores (`ETN_PROSPECT_MINOR`) después del consentimiento. `prospecto-form-modal.component.html:95-98`.
- [ ] **F3-25 · M · P21/P23/P24** — KPI «Sin contacto ≥ 7 días · alerta al asesor»: cuenta la página, 7 días fijos, la alerta no existe; «Activos · del periodo» no filtra periodo. `prospectos.component.ts:171-184`.
- [ ] **F3-26 · M · guía** — Pipeline: «Marcar como ganada» en cada tarjeta; la guía y bitácora §3.2 dicen que solo «Asignar definitivamente» marca Ganada. `pipeline.component.html:125-133`.
- [ ] **F3-27 · M · P9** — «Monto estimado» de la oportunidad sin moneda (`currencyId` null). `pipeline-oportunidad-modal.component.ts:269-273`.
- [ ] **F3-28 · M · P1/P11** — Oportunidad: el back no valida que el responsable sea miembro de la empresa. `OpportunityService.java:147-160`.
- [ ] **F3-29 · M · P21/P23** — «N d en etapa» rojo desde 10 d y ámbar desde 5 («del mockup»); «En riesgo» usa `sales.at_risk_days` (15). `pipeline.component.ts:559-570`.
- [ ] **F3-30 · M · P25** — Comisiones «#saleId», «#salespersonId»; etapas «v{{stageVersion}}». `pipeline.component.html:220-221`.
- [ ] **F3-31 · M · P22** — Renovaciones ofrecen todos los contratos (perpetuos, borradores, anulados) que el back rechaza. `renovacion-form-modal.component.ts:160`.
- [ ] **F3-32 · M · P11** — Plazo de renovación sin tope ni en front ni en back (el contrato limita 200 años / 2400 meses). `RenewalCreateRequestDTO`.
- [ ] **F3-33 · M · P4/P22** — Transferencias: selectores cargan 100 registros sin búsqueda; «nuevo titular» incluye al titular actual (`etn.transfers.sameHolder`). `transferencia.service.ts:175-191`.
- [ ] **F3-34 · M · P25/P20** — Gestión Documental, bitácora de accesos: usuario numérico; detalle técnico en inglés («generated deed contract=7», «integrity mismatch», «storage: <Excepción>»). `documento-detalle-modal.component.ts:255-257`; `DocumentService.java:270,292,298`.
- [ ] **F3-35 · B · P23** — Stepper: check verde en «Documentos pendientes» al superarlo (se lee al revés); «Sincronizar avance» no está en la guía. `venta.model.ts:26-28`.
- [ ] **F3-36 · B · P20** — «etapas configurables y versionadas… comparabilidad histórica», «RENIEC en modo degradado», UUID «Identificador de sincronización» visible. `prospecto-detalle-modal.component.html:101-105`.
- [ ] **F3-37 · B · P11** — Evidencia de firma sin tope de tamaño en el front (el back rechaza > 10 MB al final). `contrato-detalle-modal.component.html:595`.
- [ ] **F3-38 · B · P21** — Semáforo de vencimientos 30/60/90 días fijo en el front. `contratos.component.ts:381-386`.

---

## Fase 4 · Pagos

- [ ] **F4-01 · A · P26** — Caja: se puede cobrar en otra moneda y se aplica como si fuera la del plan (100 USD pagan 100 PEN); el front manda `exchangeRate: null` y el back usa 1. `pago-nuevo-modal.component.html:97-107`, `.ts:497-498`; `PaymentService.java:153,203,228-230`. → Moneda del plan; rechazar otra sin tipo de cambio.
- [ ] **F4-02 · A · P11** — Un pago puede tener boleta **y** factura: la idempotencia es `PAY:{id}:{tipo}`; solo lo frena el front. `ElectronicDocumentService.java:303-309`. → Rechazar si ya hay comprobante vivo.
- [ ] **F4-03 · A** — Doble facturación: los pagos con medio «Saldo a favor» salen como facturables y el pago original ya se facturó por el monto completo. `ElectronicDocumentService.java:93,297,316`. → Excluir `CREDIT_BALANCE`.
- [ ] **F4-04 · A · P9** — El plan de «Mantenimiento perpetuo» propone como importe el precio de venta del espacio (`agreedPrice`) y no cambia al cambiar de concepto. `plan-nuevo-modal.component.ts:293-295,379-382`.
- [ ] **F4-05 · A · P26** — Todos los importes del módulo se muestran en soles: `formatMoney(amount)` sin moneda en planes, caja, cierres, facturación, cobranza, conciliación y reportes. `F/feature-pagos/src/utils/format.util.ts:29`.
- [ ] **F4-06 · A · P11** — N.º de operación obligatorio (transferencia, POS, Yape/Plin, en línea) solo en el front. `pago-nuevo-modal.component.ts:233-234,484`; `PaymentService.java:139-207`.
- [ ] **F4-07 · M · P23** — Pista del plan «la imputación real la decide el FIFO»: con plan elegido el back imputa solo a ese plan. `etnCaja.pago.planHint`; `PaymentService.java:467-479`.
- [ ] **F4-08 · M · P14** — El back no comprueba que el plan sea del contrato del pago. `PaymentService.java:467-477`.
- [ ] **F4-09 · M** — Con plan de venta y de perpetuo en el mismo contrato no se distinguen («código · 1.ª cuota» sin concepto). `pago-nuevo-modal.component.ts:220-229`.
- [ ] **F4-10 · M · P22** — «Acepta efectivo = No» no se respeta (ni back ni modal). `CollectionPoint.java:56`.
- [ ] **F4-11 · M · P22** — El punto «Portal» aparece en Caja y permite abrir turno y cobrar en efectivo. `caja.component.ts:266-275`.
- [ ] **F4-12 · M · P23** — «Cobrado en el turno» suma saldo a favor y pagos PENDING/FAILED/SUSPICIOUS. `caja.component.ts:246-258`.
- [ ] **F4-13 · M · P20/P25** — Detalle del pago: canal «CASHIER», turno y plan «#id», cuota = id de BD (no el n.º), bitácora «PENDING → CONFIRMED». `pago-detalle-modal.component.html:67,72,78,163,199`.
- [ ] **F4-14 · M** — El resultado del pago no dice qué cuota se pagó (`PaymentApplicationDTO` sin `installmentNumber`).
- [ ] **F4-15 · M · P20** — Bitácora del plan: `plan_status`, `activated_at`, «CREDIT_BALANCE → PREPAY_NEXT», `PLAN_CREATED`, `LATE_FEE_ACCRUAL_BATCH`. `plan-detalle-modal.component.html:239-244`.
- [ ] **F4-16 · M · P20** — «Parámetros de cálculo congelados»: `calcVersion`, `periodicRate`, `roundingMode=HALF_UP`, `lateFeeConfigId`. `plan-detalle-modal.component.ts:111-118`.
- [ ] **F4-17 · M · P22** — «Refinanciar» se ofrece en el nivel máximo (`ETN_PLAN_REFI_LIMIT`). `planes-pago.component.ts:476-478`.
- [ ] **F4-18 · M · P21** — Umbral de 12 cuotas para aprobación del Director copiado en el front. `plan-refinanciar-modal.component.ts:36`.
- [ ] **F4-19 · M · P21** — «con 60 días o más… la mora antes que el capital»: es configurable por tasa (en QA vale 0). `etnCaja.detalle.imputacionesHint`.
- [ ] **F4-20 · M** — «Mora primero tras» parece opcional y el back guarda 60 sin avisar; la tabla no muestra ese dato. `LateFeeConfigService.java:69`.
- [ ] **F4-21 · M · P23** — «Tasa» de mora sin unidad (0.05 = 0,05 %); el tope dice «del capital» y se aplica sobre el saldo pendiente. `mora-configs-modal.component.html:52-58,94`.
- [ ] **F4-22 · M · P26/P11** — La moneda del plan se elige a mano y el back no valida que sea la del contrato. `plan-nuevo-modal.component.html:81-92`.
- [ ] **F4-23 · M · P22** — Baja de comprobantes: el checkbox se habilita fuera de los 7 días o mezclando sedes (el back rechaza). `facturacion.component.ts:444-447`.
- [ ] **F4-24 · M · P20** — CDR de SUNAT como código («0», «2033») sin descripción; bitácora «GENERATED → SENT». `facturacion.component.html:162-165`.
- [ ] **F4-25 · M · P20** — Cobranza «Servicios bloqueados: NEW_SERVICES»; `unblockType` crudo. `cuenta-detalle-modal.component.html:183,187`.
- [ ] **F4-26 · M · P25** — Conciliación: pago cruzado «#id», `matchType` crudo, «Movimiento #id». `conciliacion.component.html:158-159,280`.
- [ ] **F4-27 · M · P25** — Reportes financieros: usuario «#id», estado «COMPLETED», contrato «#id». `reportes.component.html:231,365-373`.
- [ ] **F4-28 · M · P13** — Mensajes de validación con el límite equivocado (max 20/15 → «maxLength40», 60 → «120», 160 → «120»). `RegisterPaymentRequestDTO.java:49,52,55,66`.
- [ ] **F4-29 · B · P21** — «(por defecto S/ 0.10)» fijo en `etnPlanesPago.nuevo.toleranciaHint`.
- [ ] **F4-30 · B · P11** — Inicial = importe: front rechaza (`>=`), back acepta (`>`).
- [ ] **F4-31 · B · P11** — «Días de gracia» max 90 solo como atributo HTML.
- [ ] **F4-32 · B · P23** — «Mora congelada 0.10 %» sin decir «diaria».
- [ ] **F4-33 · B · P28** — «1 pagos», «Se reanudaron 1 transferencias».
- [ ] **F4-34 · B · P21** — Aviso manual «esperando 5 días» copiado de `ServiceBlock.PRE_NOTICE_DAYS`.
- [ ] **F4-35 · B · P21** — Conciliación: meta 95 % y 48 h copiados en `conciliacion.model.ts:23,70`.
- [ ] **F4-36 · B · guía** — La guía dice «registrar con canal Caja»; la pantalla no tiene ese campo (va `CASHIER` fijo).
- [ ] **F4-37 · B · P21** — Rojo a partir de 90 días de atraso fijo en cobranza y reportes.

---

## Fase 5 · Servicios Funerarios

- [ ] **F5-01 · A · P11** — La solicitud se puede registrar sin difunto, pero aprobar lo exige y no hay forma de añadirlo (no hay PUT de `/requests/{id}`): queda bloqueada y otra nueva salta como duplicado. `ServiceRequestService.java:324-326`. → `PUT /requests/{id}/deceased` + «Vincular difunto».
- [ ] **F5-02 · A · P14** — Cremación: se valida tipo y estado de la OS, no que el difunto sea el de la OS. `CremationService.java:140-148`.
- [ ] **F5-03 · A · P14** — Exhumación: la OS no se compara con el difunto de la inhumación y el selector lista todas las OS de exhumación de la empresa. `ExhumationService.java:146-147`; `exhumacion-solicitud-modal.component.ts:274-283`.
- [ ] **F5-04 · A · P14** — Traslado: `deceasedId` puede ser otro difunto distinto del de la OS. `RemainsTransferService.java:159-171`.
- [ ] **F5-05 · A · P2** — «Registrar cumplimiento» de un requisito pide teclear «Id del documento», «Clave de almacenamiento» y «SHA-256»; no se puede subir el archivo. `orden-detalle-modal.component.html:241-255`. → Subir archivo o elegir del expediente.
- [ ] **F5-06 · A · P27/P22** — Doble aprobación de OS > S/ 15 000 nominal: el emisor elige a otro y queda aprobado sin que esa persona actúe; el selector incluye al propio emisor (`ETN_SVCORD_APPROVER_SELF`). `ServiceOrderService.java:438-457`.
- [ ] **F5-07 · M · P21** — `DRAFT_TTL_HOURS = 72` fijo en el front; el back lo lee por empresa (`deceased.draft_expiry_hours`). `difuntos.model.ts:81`.
- [ ] **F5-08 · M · input redundante** — «Ejecutar inhumación» vuelve a pedir la fecha programada vacía aunque la OS la trae. `inhumacion-form-modal.component.ts:247,396`.
- [ ] **F5-09 · M · input redundante** — Cremación pide difunto, luego OS y fecha (la OS ya trae difunto y fecha): patrón inverso a la inhumación corregida. `cremacion-form-modal.component.ts:47-125,229`.
- [ ] **F5-10 · M · P25** — Selectores de OS (inhumación, cremación, traslado, exhumación) muestran solo número o «#id», aunque el DTO trae difunto y solicitante.
- [ ] **F5-11 · M** — La tabla de OS no tiene columna difunto/solicitante; las no emitidas salen «#12». `ordenes-servicio.component.html:252-300`.
- [ ] **F5-12 · M · P25** — Detalle de solicitud: difunto «#id»; contrato y espacio no aparecen (`ServiceRequestDTO` sin nombres).
- [ ] **F5-13 · M · P25** — Listado de inhumaciones: difunto y OS «#id» (`IntermentDTO`, `ExhumationDTO`). `inhumaciones.component.html:96,99`.
- [ ] **F5-14 · M · P25** — Detalle del evento de agenda: «OS #id», «Difunto #id». `evento-detalle-modal.component.html:67,73`.
- [ ] **F5-15 · M · P25** — Traslados y cremaciones con OS/difunto «#id». `cremaciones.component.html:108,357,503,508,554`.
- [ ] **F5-16 · M · P20** — Traza de estados cruda («RECEIVED → QUOTED», «PENDING_APPROVAL → ISSUED») y actor «#userId». `solicitud-detalle-modal.component.html:239,246`; `orden-detalle-modal.component.html:214`.
- [ ] **F5-17 · M · P20** — Actas de cremación/traslado (`CREMATION`, `INTERNAL_TRANSFER`, `EXTERNAL_TRANSFER`) sin traducción ni filtro en Actas. `actas.model.ts:81-83`.
- [ ] **F5-18 · M · P20** — Bitácora de rechazos con `CREMATION`, `TRANSFER`, `URN_DELIVERY` sin claves. `actas.model.ts:115-118`.
- [ ] **F5-19 · M · P24** — «El acta se emite en PDF/A … firma digital del coordinador»: no hay render de actas en el back. i18n `etnCremaciones.interno.notaActa` y ≈2325/2329.
- [ ] **F5-20 · M · guía** — «Aprobar» la solicitud no exige cotización aprobada (es solo una pista) y hay botón «Aprobar» directo en la fila. `ServiceRequestService.java:321-334`. → Decidir la regla.
- [ ] **F5-21 · B** — «Vigencia» de la cotización en ISO crudo. `solicitud-detalle-modal.component.html:287`.
- [ ] **F5-22 · B · P25** — «Sustituye a la cotización #id», «Posible duplicado de la solicitud #id», «Solicitada por el usuario #id», «#documentId», «Difunto #id», autorización «#id».
- [ ] **F5-23 · B · P25** — Solicitud de ceremonia: contrato «#contractId» si no hay titular cargado. `solicitud-form-modal.component.ts:205`.
- [ ] **F5-24 · B** — `sha256Hash` max 64 con mensaje de 255; «Snapshot del tarifario aplicado» (jerga).

---

## Fase 6 · Mantenimiento

- [x] **F6-01 · A · P22** — «Registrar ejecución» lista tareas PENDING_ASSIGNMENT (y el hint lo dice), pero el back solo inicia ASSIGNED y revierte todo. `registro-modal.component.ts:278`; `TaskExecutionService.java:189,678-683`. → Solo ASSIGNED/IN_PROGRESS. — ✅ **Corregida 2026-10-11** (B 4e51152 · F 61f60cc).
- [x] **F6-02 · A** — Un borrador de ejecución no se puede retomar: no hay «Continuar»; la tarea IN_PROGRESS sigue listada y se crea otra ejecución (sin índice único). En QA la tarea 1 tiene 4 DRAFT + 1 APPROVED. `ejecucion.component.html:378-399`; `TaskExecutionService.java:153-190`. — ✅ **Corregida 2026-10-11** (B 4e51152, 5bc73e4 · F 61f60cc (4 borradores QA descartados con status=0)).
- [x] **F6-03 · A** — Los ítems «Obligatorio» del checklist no se exigen (el modal descarta `isRequired`; `submit()` solo valida fotos). `registro-modal.component.ts:296-305`; `TaskExecutionService.java:319-354`. — ✅ **Corregida 2026-10-11** (B 4e51152 · F 61f60cc).
- [x] **F6-04 · A · P22** — Unidad de capacidad «Minutos» se ofrece pero se cuenta en tareas: «480 minutos» = 480 tareas/día. `personal-modal.component.ts:208`; `MaintenanceStaffService.java:275-288`. — ✅ **Corregida 2026-10-11** (B 4f296d7 · F a472ca9 (solo tareas/día)).
- [x] **F6-05 · A** — «Pendientes de revisión»: la columna «Operario» muestra la cuadrilla (o «—»); el DTO no trae el operario. `supervision.component.html:377`; `PendingReviewDTO`. — ✅ **Corregida 2026-10-11** (B ecf97c5 · F 90dc0c9).
- [x] **F6-06 · A** — Toda intervención de una tarea preventiva queda marcada «Mantenimiento perpetuo» (se pasa `preventive` como `perpetualCareCovered`). `InterventionService.java:500,517,660`. — ✅ **Corregida 2026-10-11** (B de3550c).
- [x] **F6-07 · A** — Las alertas no se cierran al resolverse: solo `TASK_OVERDUE` se auto-cierra; quedan vivas ESC48, UNASSIGNED y RESCHEDULED de tareas asignadas, aprobadas o canceladas (QA: 26 + 22 + 26 abiertas). `MaintenanceAlertEngineService.java` (autoResolveTaskOverdue). — ✅ **Corregida 2026-10-11** (B 4e51152).
- [x] **F6-08 · A** — Doble alerta por reprogramaciones: `RESCHED-` al Director (TaskService) y `RESCH-` al Capataz (motor). → Una sola regla. — ✅ **Corregida 2026-10-11** (B 030e579).
- [x] **F6-09 · M · guía** — Cualquier usuario registra en nombre del operario: el selector «Operario» ofrece capataces y directores, y «no es tuya» compara el `staffId` enviado, no el usuario conectado. `registro-modal.component.ts:295,323-336`; `TaskExecutionService.java:621-627`. → Decidir la regla. — ✅ **Corregida 2026-10-11** (B 4bc0860 · F 470ee13 (siempre a nombre del asignado)).
- [x] **F6-10 · M** — Las coordenadas tecleadas pisan el GPS (EXIF) de cada foto y la geocerca se evalúa una vez; no hay «Usar mi ubicación». `registro-modal.component.ts:384-389`; `TaskExecutionService.java:232-254,668-675`. — ✅ **Corregida 2026-10-11** (B 87122bb · F c793786).
- [x] **F6-11 · M · P20** — Se sigue diciendo «polígono» (la geocerca es un círculo): `etnMtoEjecucion.tabla.gpsFuera`, `ficha.gpsFuera`, `ficha.gps`, `envio.fueraGeocerca`, `envio.confirmarFuera`. — ✅ **Corregida 2026-10-11** (F 61f60cc).
- [x] **F6-12 · M · P21** — Límites de foto/evidencia copiados en el front (`ejecucion.model.ts:50-52`); el back lee `maintenance.photo_max_bytes` y `task_max_evidence_bytes`, que no están en `MaintenanceSettings`. — ✅ **Corregida 2026-10-11** (B 87122bb · F c793786).
- [x] **F6-13 · M · guía** — «Rechazar» se describe como «tarea correctiva»; el back crea REWORK. Terminología mezclada (Retrabajo / Reproceso / rework). i18n ≈11919/11986/12197; `msg:2752,2756,2962,2970`. — ✅ **Corregida 2026-10-11** (B 030e579 · F 470ee13 («retrabajo»)).
- [x] **F6-14 · M** — Regla de vencidas: Umbrales dice «en un mismo pabellón… alerta al capataz»; Supervisión dice «Sobre 5 vencidas en un sector se avisa al Director». Real: **más de N** en un **pabellón**, al capataz **y** escalada al Director. `MaintenanceAlertEngineService.java` (sectorThresholdRule). → Un solo texto con N del umbral. — ✅ **Corregida 2026-10-11** (F c793786).
- [x] **F6-15 · M · P22** — «Conectividad» y «Reasignar» aparecen en cualquier alerta de tarea (solo valen para OVERDUE / tareas vivas). `supervision.component.ts:800-802`. — ✅ **Corregida 2026-10-11** (F 6094a22).
- [x] **F6-16 · M** — Una tarea vencida y sin asignar genera tres alertas (OVERDUE «sin completarse», ESC48, UNASSIGNED). → Solo UNASSIGNED si no tiene operario. — ✅ **Corregida 2026-10-11** (B 030e579).
- [x] **F6-17 · M · P22** — Asignar/Reprogramar/Cancelar visibles en tareas CANCELLED/COMPLETED/APPROVED (el back rechaza); también en Calendario. `programacion.component.html:578,600,621,855-877`. — ✅ **Corregida 2026-10-11** (F a472ca9).
- [x] **F6-18 · M · P23** — «sin asignar +24 h» marca toda tarea de fecha pasada sin operario (incluidas canceladas), sin mirar `createdAt` ni el umbral. `programacion.component.ts:1359-1363`. — ✅ **Corregida 2026-10-11** (B 4f296d7 · F a472ca9).
- [x] **F6-19 · M** — Cuadrilla «asignadas hoy» muestra el día elegido. `programacion.component.ts:801`. — ✅ **Corregida 2026-10-11** (F c793786).
- [x] **F6-20 · M · P24** — «Si el job falla dos veces seguidas se alerta al administrador»: la generación captura la excepción y `JobRunService` lo ve como éxito; su «alerta» es un `log.error`. `MaintenanceTaskGenerationService.java:152-170`; `common/job/JobRunService.java:96-101`. — ✅ **Corregida 2026-10-11** (B 4f296d7).
- [x] **F6-21 · M · P20** — El panel de la corrida muestra `e.getMessage()` crudo (SQL/Java en inglés). `programacion.component.html:333-336`. — ✅ **Corregida 2026-10-11** (F a472ca9).
- [x] **F6-22 · M · P20** — Códigos crudos en mensajes del back: estado («APPROVED») en `autoResolvedTask`, `verification.notSubmitted`, `workOrders.notOpen`; severidad «HIGH» en `alerts.emailBody`; «Novedad de campo (STRUCTURAL)» en el título de la OT. — ✅ **Corregida 2026-10-11** (B 87122bb · 8a752f9).
- [x] **F6-23 · M** — Supervisión: «Espacio» vacío en tareas de infraestructura común (no se resuelve el elemento). `supervision.component.html:383`. — ✅ **Corregida 2026-10-11** (B 8a752f9 · F 6094a22).
- [x] **F6-24 · M · P23** — Latencia «0 s» cuando no hay dato (el DTO pide «—»), y es de hoy aunque el tablero tenga otra fecha. `supervision.component.ts:292-295`. — ✅ **Corregida 2026-10-11** (F 90dc0c9).
- [x] **F6-25 · M** — La intervención atribuye el trabajo al asignado, no a quien ejecutó; un retrabajo aprobado queda CORRECTIVE. `InterventionService.java:500,517`. — ✅ **Corregida 2026-10-11** (B 87122bb).
- [x] **F6-26 · M · P23** — KPI «Registros del día… de N tareas asignadas» cuenta también las no asignadas; «Pendientes de sync»/«En error» no filtran fecha. `TaskExecutionService.java:565-586`. — ✅ **Corregida 2026-10-11** (B 87122bb · F c793786).
- [x] **F6-27 · B** — Columna «Generada» muestra la fecha programada. `programacion.component.ts:348`. — ✅ **Corregida 2026-10-11** (B 4f296d7 · F a472ca9).
- [x] **F6-28 · B** — Fechas ISO en notificaciones de tareas (ver T-12). — ✅ **Corregida 2026-10-11** (B 4f296d7 · 8a752f9).
- [x] **F6-29 · B · P20** — Promesas de nivel de servicio de los requisitos: «notificación en menos de 30 segundos», «conservado 5 años», «en menos de 10 segundos», «meta 15 s». — ✅ **Corregida 2026-10-11** (F c793786).
- [x] **F6-30 · B · P25** — «TSK-{{id}}» en Supervisión. — ✅ **Corregida 2026-10-11** (F 6094a22).
- [x] **F6-31 · B · P25** — «Orden correctiva #n», «OT #n» con id interno en vez de folio. — ✅ **Corregida 2026-10-11** (B 87122bb · 8a752f9 · F c793786).
- [x] **F6-32 · B · P11** — `registerWeb` admite ejecución sobre tareas canceladas o aprobadas. — ✅ **Corregida 2026-10-11** (B 4e51152).
- [x] **F6-33 · B · P11** — Reprogramar: el back acepta fechas pasadas (solo el front limita). — ✅ **Corregida 2026-10-11** (B 4f296d7).
- [x] **F6-34 · B · P11** — «Costo final» requisito de cierre de OT solo en el front. — ✅ **Corregida 2026-10-11** (B 87122bb).
- [x] **F6-35 · B · P28** — «{{n}} tareas en el checklist» (son ítems), «1 tareas vencidas», «1 seleccionadas». — ✅ **Corregida 2026-10-11** (F c793786 (redacción neutra; el plural ICU global sigue en T-03)).
- [x] **F6-36 · B** — «sincronizado {{hora}}» aunque el estado sea PENDING/ERROR; `novedadEstado.TRIAGED` = «Triada». — ✅ **Corregida 2026-10-11** (F c793786).
- [x] **F6-37 · M** *(reportada por el usuario 2026-10-11)* — Un plan **diario** duplica la carga del lunes: la tarea del domingo (periodo `2026-10-11`) se corre al lunes 12/10 y el lunes además genera la suya (periodo `2026-10-12`) ⇒ 2 tareas por espacio ese día. En QA: tareas 30–34 con `period_key 2026-10-11` y `scheduled_date 2026-10-12`. `MaintenanceBusinessCalendar` + `MaintenanceTaskGenerationService`. → Para frecuencia diaria, saltar el domingo/feriado en vez de correrlo (decidir la regla de negocio). — ✅ **Corregida 2026-10-11** (B 5a25537 · F 762f529 (días de trabajo configurables)).
- [x] **F6-38 · M** *(reportada por el usuario 2026-10-11)* — El Calendario solo muestra tareas **ya generadas**: un plan diario no aparece en los días siguientes, y el usuario espera verlo cada día. Además, si el servidor no corre a las 00:30, ese día queda sin tareas y no se recupera («Ejecutar generación ahora» solo genera el día actual; en QA no hay tareas del 09 y 10/10). → Proyectar en el calendario las tareas previstas de los planes (atenuadas, «se generará el día X»), y que la generación recupere los días perdidos dentro de la vigencia o avisarlo. — ✅ **Corregida 2026-10-11** (B 5a25537 · F 762f529).
- [x] **F6-39 · A** *(usuario 2026-10-11)* — En «Registrar ejecución» no se puede **quitar** una foto o un audio subido por error: se suben al elegirlos y el back no tiene `DELETE` (`MaintenanceExecutionsController` solo POST). → Botón quitar mientras la ejecución esté en DRAFT + `DELETE /executions/{id}/photos/{photoId}` y `/voice-notes/{id}`. — ✅ **Corregida 2026-10-11** (B 4e51152 (V140) · F 61f60cc).
- [x] **F6-40 · M · P11** *(usuario 2026-10-11)* — La **duración del audio se teclea** a mano y el back no la comprueba: un audio de 29 s se guardó como «5 s» (la ficha dice «Nota de voz (5 s)» y el reproductor 0:29). `registro-modal.component.ts:419-426`; `TaskExecutionService.java:279-297`. → Leer la duración del archivo en el navegador (`HTMLAudioElement.duration`), quitar el campo y validar el tope de 60 s con ese valor. — ✅ **Corregida 2026-10-11** (F 61f60cc).
- [x] **F6-41 · M · P23** *(usuario 2026-10-11)* — **Cumplimiento por pabellón** cuenta en el denominador tareas que aún no vencen: «pabellon test 40 % · 2 de 5» en rojo con 0 vencidas, porque 3 tareas PENDING_ASSIGNMENT con vencimiento 14/10 cuentan como no cumplidas. Además una tarea COMPLETED aún sin revisar ya cuenta como cumplida a tiempo. `SupervisionQueryRepository.java:33-38,71-84`. → Denominador = tareas vencidas o terminadas en el periodo; decidir si COMPLETED sin veredicto cuenta. — ✅ **Corregida 2026-10-11** (B 5a25537 · F 762f529).
- [x] **F6-42 · B** *(usuario 2026-10-11)* — «Pendientes de revisión»: el badge «2 fotos» queda descuadrado respecto de «antes y después» en la columna Fotos. `supervision.component.html:385-394`. → Contenedor `flex flex-col items-center`. — ✅ **Corregida 2026-10-11** (F 762f529).
- [x] **F6-43 · M** *(usuario 2026-10-11)* — Checklist parcial: una vez enviada la ejecución, el operario no puede completar el ítem que le faltó; solo el capataz con «Editar como capataz» (con justificación). Como el borrador tampoco se retoma (F6-02), el operario debe marcar todo en un solo envío. → Resolver junto con F6-02 y F6-03 (ítems obligatorios exigidos; borrador retomable). — ✅ **Corregida 2026-10-11** (B 4e51152 · F 61f60cc).

---

## Fase 7 · Portal de Familias

- [x] **F7-01 · A · P20** — Cabecera del portal (público): «HOLDER · DNI ***», «LEGAL_REPRESENTATIVE · PASSPORT». `F/feature-portal/src/layout/portal-layout.component.ts:107`, `.html:84`. → Relación y tipo de documento traducidos en la cabecera. — ✅ **Corregida 2026-10-12** (F 87d29f5).
- [x] **F7-02 · M · P20** — «Mi cuenta» muestra «PASSPORT». `portal-cuenta.component.html:22`. También `e.spaceType` y `s.estado`/`c.estado` en inicio y solicitudes (verificar que lleguen traducidos). → «Pasaporte»/«C.E.» en Mi cuenta; espacio y estados ya llegaban traducidos. — ✅ **Corregida 2026-10-12** (F 87d29f5).
- [x] **F7-03 · A · guía** — El portal solo cobra cuotas ya vencidas (`PortalCheckoutService.java:318,331`): con un plan recién creado no se puede probar el pago. La guía (§8.1, §13 paso 7) no lo dice. → Guía §8.1/§13: solo cuotas vencidas o que vencen hoy; primera cuota con fecha de hoy para probar. — ✅ **Corregida 2026-10-12** (docs).
- [x] **F7-04 · M · P21** — «20 minutos» fijo; el back lee `portal.session_minutes`. i18n ≈13071/13100. → La marca pública trae `sessionMinutes`; los dos textos lo usan. — ✅ **Corregida 2026-10-12** (B 4bbe9c5 · F 87d29f5).
- [x] **F7-05 · M · P23** — «Intentos restantes» baja 1 ante cualquier error (código vencido, bloqueo, red). `portal-acceso.component.ts:337-342`. → Solo baja con PORTAL_OTP_INVALID; bloqueo → 0; vencido/red no lo tocan. — ✅ **Corregida 2026-10-12** (F 87d29f5).
- [x] **F7-06 · B · P28** — «Te quedan 1 intentos». `msg:3055`. → «Intentos restantes: {0}». — ✅ **Corregida 2026-10-12** (B 4bbe9c5).
- [x] **F7-101 · M · P20** — Cuentas del Portal: 5 plantillas de notificación sin traducir ni filtro (`CONTRACT_EXPIRING`, `CONTRACT_TRANSFERRED`, `INTERMENT_EXECUTED`, `EXHUMATION_EXECUTED`, `INVOICE_ISSUED`). `atencion-comun.model.ts:109-113`. → Las 5 plantillas traducidas (es/en/pt) y en el filtro. — ✅ **Corregida 2026-10-12** (F 87d29f5).
- [x] **F7-102 · M · P24** — «Si vence el SLA, la solicitud escala al jefe de atención»: solo si existe `portal.requests.escalation_user_id` (sin pantalla, 0 por defecto). `PortalRequestSlaService.java:111-113`. → Sin jefe configurado avisa a los administradores (respaldo: propietarios); textos alineados. — ✅ **Corregida 2026-10-12** (B 4bbe9c5 · F 87d29f5).
- [x] **F7-103 · M · guía** — Culqi exige el NOMBRE de variables de entorno del servidor: lo debe definir sistemas; la guía no lo dice. → Guía §8.2: nombre de variable de entorno que define sistemas; probar con el simulador. — ✅ **Corregida 2026-10-12** (docs).
- [x] **F7-104 · B** — «Invitar titular»: «Le enviaremos un correo para activar su cuenta»; el correo no activa nada. → Subtítulo: «avisándole que ya puede entrar con su documento»; guía aclarada. — ✅ **Corregida 2026-10-12** (F 87d29f5).
- [x] **F7-105 · B · guía** — El «costo» del tipo de solicitud es texto libre, no un monto. → Guía §8.2: el costo es texto informativo, no genera cobro. — ✅ **Corregida 2026-10-12** (docs).
- [x] **F7-106 · B · P11** — Plazo 0 o > 90 responde con el mensaje «no negativo». → Mensaje propio «entre 1 y 90 días hábiles». — ✅ **Corregida 2026-10-12** (B 4bbe9c5).
- [x] **F7-107 · B** — Ambiente de pasarela desconocido responde «proveedor desconocido». → Mensaje propio de ambiente desconocido. — ✅ **Corregida 2026-10-12** (B 4bbe9c5).

---

## Fase 8 · Análisis

- [ ] **F8-01 · A** — REP-CAR-05 «Cartera»: el rango de fechas del formulario se ignora y el Excel/PDF dice «Periodo X–Y» sobre la foto actual. `FinancialReportDelegateService.java:53,68-71`; `V121__m10_reporting_and_audit.sql` (`filter_schema`).
- [ ] **F8-02 · M · P20** — Auditoría, bitácora: módulo, operación (INSERT/UPDATE) y entidad = clase Java (`FuneralSpace`, `PaymentPlanInstallment`); facetas y placeholder «Contract, Deceased…». `auditoria.component.html:604,717-725`.
- [ ] **F8-03 · M · P20** — Trazabilidad por contrato: «FuneralSpace #12», título «Entidad · UPDATE». `auditoria.component.html:302`; `ContractTraceService.java:176-177`.
- [ ] **F8-04 · M · P20** — Salud de datos: nombres `mv_*`, código del job, estado de integración y último error técnico crudos. `dashboard-ejecutivo.component.html:1095,1132,1174,1182`.

---

## G · Guía (`README.md`) y secciones 10–12

- [ ] **G-01 · M** — §10 «Exhumación (doble validación del director)»: solo con autorización JUDICIAL. `ExhumationService.java:232-238`.
- [ ] **G-02 · M · P27** — §10 «Excepción de cobranza»: el aprobador lo elige el solicitante y queda aprobada al instante; el selector incluye al usuario actual (`approverMustDiffer`). `CollectionExceptionService.java:77-97`; `excepcion-modal.component.ts:180`.
- [ ] **G-03 · B · P27** — Desbloqueo manual de cobranza: aprobador sin validar (otra persona, misma empresa). `CollectionAccountService.java:213`.
- [ ] **G-04 · B** — §11 «Cada 5–15 min … reservas y bloqueos»: reservas y bloqueos de espacio vencen cada 60 s; bloqueos de cobranza cada 10 min. Faltan en la tabla los jobs de 02:30, 03:00, 06:15–06:55 y 07:05/07:10.
- [ ] **G-05 · M** — §12 «El pago no aparece en reportes → esperar al día siguiente»: hay «Actualizar datos» en Reportes Financieros; el refresco es a las 06:00 y el Dashboard cada 15 min.
- [ ] **G-06 · M** — §12 fila `ETN_RATE_PAVILION`: el código ya no existe (ver F1-18).
- [ ] **G-07 · M** — §12 «No se generan tareas… o plan sin checklist»: un plan sin checklist no se puede guardar. Causas reales: plan pausado, falta el plan de pago perpetuo que exige, espacio sin contrato ACTIVE, frecuencia que no toca ese día (bitácora §7.3).
- [ ] **G-08 · B** — §13 paso 9 dice «Generar tareas»: el botón es «Ejecutar generación ahora».

---

## Qué se revisó y estaba bien

- Rutas: todas las que llaman los services del front existen en los controllers del back, en todas las fases.
- i18n: paridad exacta es/en/pt (12 489 claves front; 3 663 líneas `messages*.properties`); las claves usadas existen salvo T-04 y los enums de F5-17/F5-18 y F7-101.
- Los «RN-/RF-/V113» de las plantillas HTML están en comentarios (no se ven), salvo los de T-02.
- Todos los `@Scheduled` tienen `zone = "America/Lima"` y sus horas coinciden con §11.
- Correcciones previas de la bitácora (P1, P4, P5, P8, P17, cobertura y simulador de Programación) se mantienen.
