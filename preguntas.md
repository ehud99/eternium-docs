# Preguntas pendientes de decisión — QA Eternium (cementerio)

Preguntas surgidas al corregir los hallazgos de la bitácora QA
(https://claude.ai/artifact/1ZJ1UEQf2zqRMZHTDb6Qhz). Cada una trae una propuesta **recomendada** y dos
alternativas. Mientras no haya respuesta, el hallazgo queda sin corregir (o corregido solo en la parte que no
depende de la decisión, como se indica).

Cada sección indica **dónde se ve en la aplicación** (menú del tenant, grupo *Cementerio*). Los fixes `fix/qa-*`
ya están en `uat` y `dev`. La credencial del facturador por empresa está en la rama `chore/integrar-pendientes`
(ambos repos), pendiente de PR a `uat` y `dev`.

---

## 1. Comisiones de vendedores (QA-024, alta)

**Situación hoy.** Existen el modelo de reglas de comisión y el cálculo al cerrar una venta, pero no hay forma de
crear ni editar reglas (solo se pueden leer), así que nunca se devenga ninguna comisión. El requerimiento RF-439 pide
«definir porcentajes o montos fijos de comisión por tipo de espacio funerario, plan de pagos o campaña comercial».

> Ya corregido sin esperar la decisión: el mensaje «Espacio asignado definitivamente; comisión devengada» solo dirá
> que hubo comisión cuando realmente se registre una.

**Dónde se ve en la aplicación (módulo Ventas):**
- **Se consultan:** Ventas → Pipeline Comercial → pestaña **«Comisiones»**. Es una tabla de solo lectura (venta,
  vendedor, base, monto, estado, fecha) con el botón «Disputar». Al lado están las pestañas «Ranking de vendedores»
  y «Metas y tarifario».
- **Se generan:** Ventas → Proceso de Venta → botón **«Asignar definitivamente»**. En ese momento el sistema busca
  una regla vigente y, si la encuentra, registra la comisión.
- **Se configuran: en ninguna pantalla.** Ese es el hallazgo. El backend tiene el modelo de reglas
  (`pipeline/domain/model/CommissionRule.java`: porcentaje o monto fijo, con tipo de espacio opcional) y un
  endpoint de solo lectura (`GET /api/eternium/pipeline/commission-rules`), pero no hay una pantalla para crear o
  editar reglas. Las preguntas 1.1–1.3 definen esa pantalla nueva, que iría como otra pestaña («Reglas de
  comisión») en Ventas → Pipeline Comercial.

### 1.1 ¿Sobre qué base se calcula la comisión?

- **Recomendada — % (o monto fijo) por tipo de espacio.** Una regla por tipo de espacio (nicho, lote, cripta,
  mausoleo…) con un porcentaje sobre el precio final de venta o un monto fijo, y una regla general por defecto para
  los tipos sin regla propia. Es lo que describe RF-439 y encaja con el modelo que ya existe.
- **Alternativa A — % fijo por vendedor.** Cada vendedor tiene su porcentaje sobre el precio final (con un porcentaje
  por defecto de la empresa). Más simple, pero no distingue productos de distinto margen.
- **Alternativa B — Tramos por cumplimiento de meta.** El porcentaje depende del avance de la meta del periodo del
  vendedor (p. ej. < 80 %: 2 %; 80–100 %: 3 %; > 100 %: 4 %). Incentiva metas, pero la comisión de una venta no se
  conoce hasta cerrar el periodo.

### 1.2 ¿Cuándo se devenga?

- **Recomendada — Al asignar definitivamente el espacio.** Cuando la venta se cierra (contrato firmado + pago inicial
  validado + asignación definitiva), que es el momento en que hoy el sistema intenta devengarla. Si la venta se anula
  después, la comisión se revierte.
- **Alternativa A — Proporcional a lo cobrado.** Cada pago aplicado al contrato devenga su parte proporcional de la
  comisión. Protege a la empresa en ventas a cuotas con morosidad.
- **Alternativa B — Al cobrar el 100 %.** Solo cuando el contrato está totalmente pagado. La más conservadora; en
  planes de hasta 60 cuotas el vendedor puede esperar años.

### 1.3 ¿Se admiten además reglas por plan de pagos o por campaña comercial (RF-439)?

- **Recomendada — Sí, como reglas más específicas que ganan a la del tipo de espacio** (campaña > plan de pagos >
  tipo de espacio > general), con vigencia desde/hasta.
- **Alternativa A — Solo tipo de espacio** por ahora; campañas y planes en una fase posterior.
- **Alternativa B — Solo campañas** (con vigencia), sin reglas por plan de pagos.

---

## 2. Documentos del checklist de una transferencia (QA-016, baja)

**Situación hoy.** Para marcar un requisito del checklist de una transferencia (p. ej. «DNI del comprador») el documento
debe estar vinculado exactamente al **contrato** transferido. Si se subió vinculado a la **persona** (lo natural para un
DNI), se rechaza y hay que volver a subirlo.

**Dónde se ve en la aplicación (módulo Contratos):**
- **Qué se exige:** Contratos → Transferencias y Renovaciones → botón **«Requisitos»** (cabecera). Se
  configura por motivo de transferencia.
- **Dónde se marca:** Contratos → Transferencias y Renovaciones → abrir una transferencia → sección
  **«Checklist documental»** → «Vincular documento» (escribir el Id del documento y pulsar «Vincular»).
- **Dónde se sube el documento:** Contratos → **Gestión Documental** → subir. Ahí se elige a qué queda
  vinculado (contrato, espacio o titular). Si se vinculó al titular, el checklist lo rechaza.

- **Recomendada — Aceptar también documentos vinculados al titular saliente o al entrante** de esa transferencia (y
  al contrato, como hoy). Al vincularlo, el documento queda asociado también al contrato para la trazabilidad.
- **Alternativa A — Mantener la regla actual** (solo documentos del contrato) pero avisar en el modal de subida y en el
  checklist de que el documento debe vincularse al contrato.
- **Alternativa B — Permitir subir el archivo directamente desde el checklist** de la transferencia (queda vinculado al
  contrato y a la persona automáticamente), sin pasar por el repositorio de documentos.

---

## 2b. Precio del traslado externo de restos (QA-056, baja)

**Situación hoy.** El catálogo solo tenía «Traslado de restos (interno)» y se usaba también para traslados a otro
cementerio. Ya se añadió el servicio «Traslado de restos (externo)» (categoría funeraria), pero **sin tarifa**: no se
puede cotizar hasta que tenga precio aprobado.

**Dónde se ve en la aplicación (módulo Configuración; se usa en Servicios Funerarios):**
- **El servicio:** Configuración → **Servicios** («Traslado de restos (externo)»).
- **Su precio:** Configuración → **Tarifas** → pestaña **«Servicios»** (crear la tarifa y aprobarla). Tarifas es un
  ítem aparte del menú, no está dentro de Servicios.
- **Dónde se nota:** Servicios Funerarios → **Órdenes de Servicio**, al cotizar una solicitud de traslado. El traslado
  se ejecuta en Servicios Funerarios → **Cremaciones y Traslados**; el externo exige además un permiso de
  Servicios Funerarios → **Permisos Municipales**.

- **Recomendada — Cada cementerio fija su tarifa** en Configuración → Tarifas → pestaña «Servicios» (como cualquier
  servicio nuevo), y el sistema avisa al cotizarlo si no tiene tarifa aprobada.
- **Alternativa A — Tarifa inicial igual a la del traslado interno** (S/ 250 en el catálogo demo) como referencia
  editable.
- **Alternativa B — Recargo porcentual fijo sobre el traslado interno** por el trámite municipal (habría que definir el
  porcentaje y si varía por distancia o destino).

---

## 2c. Motivo de cada versión de un documento (QA-015, baja)

**Situación hoy.** Ya se pueden comparar dos versiones de un documento lado a lado. El modelo de versiones no guarda un
motivo: la comparación muestra «Restaurada desde la versión vN» o «Subida de nueva versión».

**Dónde se ve en la aplicación (módulo Contratos):** Contratos → **Gestión Documental** → abrir un documento →
sección **«Versiones»**. Ahí están «Subir nueva versión», «Restaurar como versión vigente» y «Seleccionar para
comparar» → «Comparar versiones». La pregunta es sobre la columna **«Motivo»** de esa comparación.

- **Recomendada — Motivo opcional** (texto libre) al subir una versión nueva o restaurar una anterior, guardado con la
  versión; las versiones antiguas quedan sin motivo.
- **Alternativa A — Dejarlo como está** (origen genérico: subida o restauración), sin campo nuevo.
- **Alternativa B — Motivo obligatorio** en cada versión nueva: más trazabilidad, pero cambia el flujo de subida para
  cualquier documento, incluidos los triviales.

---

## 3. Para el equipo del facturador (PSE)

Necesarias para cerrar del todo QA-067 y QA-068 (el código ya está preparado):

**Dónde se ve en la aplicación (módulo Pagos):** todo está en Pagos → **Facturación Electrónica**.
- Preguntas 1, 3 y 4: en el detalle de un comprobante, los archivos «XML firmado» y «PDF». Hoy el PDF muestra «El
  PDF lo emitirá el facturador: todavía no está disponible».
- Pregunta 2: la acción **«Comunicación de baja»** (seleccionar comprobantes → «Comunicar baja») y el listado
  **«Comunicaciones de baja»**, con los estados Baja por enviar / enviada / pendiente de SUNAT / aceptada /
  rechazada.

1. **Representación impresa (PDF):** ¿cómo la entregará el facturador? (bytes en la respuesta de emisión, URL
   descargable, endpoint aparte por comprobante). Propuesta: un campo en la respuesta de emisión con el PDF en base64
   o una URL firmada; Eternium la guarda en su almacén.
2. **Consulta de la comunicación de baja:** ¿qué estados devuelve al consultar el ticket de una baja (aceptada,
   rechazada, en proceso, anulado…)? Hoy un estado desconocido se trata como error transitorio y se reintenta.
3. **XML firmado:** ¿el facturador devuelve el UBL firmado por él? Hoy Eternium ofrece para descargar el XML que firma
   Eternium.
4. **Comprobantes ya emitidos:** cuando el PDF esté disponible, ¿se podrá pedir el de comprobantes antiguos (para un
   proceso de relleno)?

---

## 4. Confirmar un cambio ya hecho

- **Renovaciones con política «fecha de firma» (QA-014).** Al completar la firma, la nueva vigencia se calcula ahora el
  día en que se firma (antes, el día en que se creó la renovación). Parece lo correcto; confirmar.
  - **Dónde se ve en la aplicación (módulo Contratos):** Contratos → **Transferencias y Renovaciones** → nueva
    renovación → campo **«Política de fecha base»**. El efecto aparece al completar la firma de la renovación,
    cuando el contrato recibe su nueva fecha de fin.

---

## 5. No es de negocio (para el equipo de desarrollo)

- **QA-006:** la llave del facturador por empresa (rama `feat/facturador-llave-por-empresa`, back y front) quedó
  integrada con `uat` en la rama `chore/integrar-pendientes` (2026-09-28), pendiente de PR a `uat` y `dev`. Su
  migración V123 llega después de la V134, pero se aplica porque `spring.flyway.out-of-order=true`.
  - **Dónde se ve en la aplicación (módulo Configuración):** Configuración → **Configuración General** → pestaña
    **«Datos»** → tarjeta nueva **«Credencial del facturador»** (hasta mergear el PR). Hoy UAT y dev usan una sola
    llave global, configurada en el servidor y no en pantalla.

---

## 6. Integraciones pendientes

Revisado en el código el 2026-09-28. Para cada integración hay que decidir **qué proveedor**, si la cuenta es
**general** (una de la plataforma para todas las empresas) o **por empresa** (cada cementerio con la suya), y
**quién paga** la cuenta del proveedor.

### Estado actual

| Integración | Estado hoy | ¿General o por empresa? | Dónde se configura |
|---|---|---|---|
| Pasarela de pago en línea (portal de familias) | **Culqi real**, solo tarjeta. **Simulador** para pruebas. **Izipay** está en la lista pero sin conector. **Yape/Plin** solo en el simulador. | Por empresa (una pasarela activa por empresa) | Portal de Familias → **Pasarelas de Pago** |
| Pagos en caja (efectivo, transferencia, tarjeta con POS, Yape) | No necesita integración: el cajero registra el pago a mano | — | Pagos → Caja y Pagos |
| Bancos (BCP, BBVA, Interbank, Scotiabank) | Sin conexión con el banco: se importa el extracto en CSV/Excel y se concilia | — | Pagos → Conciliación Bancaria |
| Facturador (PSE) | Hecho (QA-006), en `chore/integrar-pendientes` pendiente de PR; hoy UAT/dev usan una llave global | Por empresa, llave cifrada en la base de datos | Configuración General → Datos → «Credencial del facturador» |
| Correo | Real (SendGrid) | General: una cuenta y un remitente para todas | Variables del servidor |
| SMS / WhatsApp | **Simulado**: Cobranza lo registra como «enviado» pero no sale nada | No existe todavía | — |
| RENIEC (validar DNI) | **Simulado**: solo revisa el formato | No existe todavía | — |
| Padrón RUC | Real vía Factiliza, solo si se desactiva el modo simulado de SUNAT | General (token en el servidor, variable `api_factiliza`) | Variables del servidor |
| Google Maps | **No existe**. El plano es propio (grilla/SVG); el GPS solo valida las fotos de mantenimiento | — | — |
| Google Calendar | **No existe**. La agenda de ceremonias es interna; el calendario de mantenimiento solo exporta a Excel/PDF/CSV | — | — |

**Importante sobre las pasarelas:** la configuración (proveedor, sandbox/producción, métodos, comisiones) es por
empresa, pero la **llave secreta de Culqi no se guarda en la aplicación**. El campo de la pantalla guarda solo el
**nombre de una variable de entorno** (p. ej. `CULQI_SECRET_KEY`) y la llave real tiene que estar en el servidor. Para
que cada cementerio cobre con su propia cuenta, infraestructura debe agregar una variable por empresa y reiniciar;
la empresa no puede hacerlo sola. El facturador, en cambio, guarda la llave cifrada en la base de datos y se
registra desde la pantalla.

### 6.1 Pagos en línea

Qué hace falta:
1. Llevar la llave de Culqi al mismo esquema cifrado del facturador (cambio nuestro, sin API nueva).
2. Si se quiere Yape/Plin en línea, implementarlo con la API de Culqi o de Izipay.
3. Si se quiere Izipay, hace falta su API y una cuenta de comercio para construir el conector.

- **Recomendada — Culqi por empresa, con la llave cifrada y registrada desde Pasarelas de Pago.** Cada cementerio
  usa su propia cuenta Culqi (el dinero le llega directo) y la paga él. Se hace primero el punto 1; Yape/Plin después.
- **Alternativa A — Una sola cuenta Culqi de la plataforma.** PartnerTech cobra y luego liquida a cada cementerio.
  Más simple de configurar, pero implica recaudar dinero de terceros (tema contable y legal).
- **Alternativa B — Izipay en lugar de (o además de) Culqi.** Requiere construir el conector y que cada cementerio
  tenga cuenta de comercio Izipay.

Decidir: proveedor(es) · ¿Yape/Plin en línea en esta fase? · cuenta por empresa o de la plataforma.

### 6.2 SMS y WhatsApp (alertas de cobranza)

Qué hace falta: contratar un proveedor. El resto ya está listo: solo hay que reemplazar el conector simulado.
- WhatsApp exige la API de WhatsApp Business (Meta, o Twilio como intermediario), **plantillas de mensaje
  aprobadas** y un **número verificado**. Si cada cementerio quiere escribir desde su propio número, tiene que ser por
  empresa.
- SMS puede ser una sola cuenta general de la plataforma.

- **Recomendada — SMS general de la plataforma y WhatsApp por empresa.** El SMS lo paga PartnerTech (o se cobra
  por uso); cada cementerio registra su número de WhatsApp Business y sus plantillas.
- **Alternativa A — Todo general** (una cuenta y un número de la plataforma para todos). Más barato y simple, pero
  los mensajes no salen con la identidad del cementerio.
- **Alternativa B — Solo SMS por ahora**, WhatsApp en una fase posterior.

Decidir: proveedor (Twilio, Meta directo, proveedor SMS peruano) · general o por empresa · quién paga los mensajes.

### 6.3 Google Maps

Qué hace falta: una llave de API de Google. Normalmente es **general** (de la plataforma, la paga PartnerTech).

- **Recomendada — Llave general y un primer uso acotado:** ubicar cada sede en un mapa (Configuración → Sedes) y
  mostrar «cómo llegar» en el portal de familias.
- **Alternativa A — Además, llevar a la familia hasta la tumba** (coordenadas por espacio o por pabellón). Requiere
  georreferenciar el cementerio: más trabajo de datos.
- **Alternativa B — Sin Google:** OpenStreetMap/Leaflet (ya está en el proyecto), sin costo por uso.

Decidir: ¿para qué se quiere el mapa? · ¿Google (con costo) u OpenStreetMap (gratis)?

### 6.4 Google Calendar

Hay dos caminos:
- **Sincronización real:** cada cementerio conecta su cuenta Google, así que es por empresa. Necesita credenciales
  OAuth de Google.
- **Alternativa más simple:** un enlace de calendario (.ics) que cualquiera puede suscribir en Google o Outlook, sin API.

- **Recomendada — Enlace .ics** para la agenda de ceremonias y el calendario de mantenimiento. Sin costo ni
  credenciales, y funciona con Google y Outlook (actualización cada pocas horas, solo lectura).
- **Alternativa A — Sincronización real con Google Calendar por empresa** (OAuth). Los eventos aparecen al instante
  y en ambos sentidos, pero requiere verificación de la app ante Google.
- **Alternativa B — Dejarlo fuera** de esta fase.

Decidir: ¿basta con verlo en el calendario (.ics) o hace falta sincronización en tiempo real?

### 6.5 RENIEC (validar DNI)

Qué hace falta: un proveedor de consulta de DNI (Factiliza también la ofrece). Podría ser general, igual que el
padrón RUC.

- **Recomendada — Factiliza, cuenta general de la plataforma.** Ya se usa para el RUC, así que es el mismo token y
  el mismo proveedor; solo se reemplaza el conector simulado.
- **Alternativa A — Convenio directo con RENIEC.** Más oficial, pero con trámite largo.
- **Alternativa B — Mantener solo la validación de formato** (como hoy).

Decidir: proveedor · general o por empresa · quién paga las consultas.

---

## 7. Límites de descuento por rol en cotizaciones

**Situación hoy.** Al crear una cotización con cualquier descuento aparece «El descuento supera el límite de tu rol:
la cotización quedará pendiente de aprobación», **incluso con SUPERADMIN**. No es un error del rol: el límite se
configura por rol, y **un rol sin límite configurado solo puede descontar 0 %** (regla RN-002 del doc 14). Todo
descuento mayor deja la cotización en «Descuento por aprobar» y hay que aprobarla antes de emitir. **Puede
aprobarla el mismo usuario que la creó** (una empresa con un solo usuario no tendría a nadie más); queda registrado
quién aprobó.

El backend ya tiene la API para configurar los límites (`GET/POST/PUT/DELETE
/api/eternium/quotations/discount-limits`: rol, % máximo y vigencia desde/hasta), pero **no existe ninguna pantalla**
para usarla: el frontend solo la consulta al validar el paso 2 del asistente.

**Dónde se ve en la aplicación (módulo Ventas):**
- **Dónde aparece el aviso:** Ventas → **Cotización y Propuesta** → botón «Agregar» (modal «Nueva cotización»)
  → paso **2 «Descuento»**, campo «Descuento (%)».
- **Dónde se aprueba:** Ventas → Cotización y Propuesta → cotización en estado «Descuento por aprobar» → aprobar o
  rechazar (puede hacerlo el mismo usuario que la creó).
- **Dónde se configuraría el límite:** en ninguna pantalla hoy. Lo natural sería un botón **«Límites de
  descuento»** en la cabecera de Ventas → Cotización y Propuesta (como «Requisitos» en Proceso de Venta).

- **Recomendada — Pantalla «Límites de descuento»** en Ventas → Cotización y Propuesta: tabla de roles con su %
  máximo y vigencia (crear, editar, eliminar). Cada empresa decide cuánto puede descontar cada rol; lo que supere
  el límite sigue pidiendo aprobación.
- **Alternativa A — Además, sin límite para SUPERADMIN y el dueño de la empresa (OWNR):** pueden descontar
  cualquier porcentaje sin aprobación. Más ágil, pero el dueño pierde el control de doble firma sobre sus propios
  descuentos.
- **Alternativa B — Un límite por defecto para todos los roles** (p. ej. 5 %) configurable por empresa, sin
  detalle por rol. Más simple, pero no distingue vendedor de gerente.

Decidir: ¿se hace la pantalla? · ¿SUPERADMIN/OWNR quedan exentos? · ¿hace falta un límite por defecto?
