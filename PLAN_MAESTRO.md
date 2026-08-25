# FacturaFácil — Documento Maestro de Investigación y Planeación

**Versión:** 1.0 · **Fecha:** 25 de agosto de 2026 · **Estado:** Borrador para decisión
**Alcance del entregable:** Documento maestro (sin código). El esqueleto técnico se genera en una sesión posterior, previa autorización.

---

## Contexto

Se quiere construir una webapp que haga la facturación en México extremadamente simple: escanear un QR, capturar un folio o llenar datos a mano, con historial y métricas por día/semana/mes/año, cobrando 10 centavos por factura, empezando en casetas y escalando a nivel nacional e internacional, con blockchain como posible capa de integridad.

El directorio del proyecto (`C:\Users\gioes_hsnigcw\Desktop\facturafacil`) está vacío: es un proyecto greenfield sin código previo.

Esta investigación se hizo con acceso a internet. **Cuatro hallazgos cambian materialmente la premisa original** y por eso este documento no es una validación del plan inicial sino una corrección de rumbo:

1. **El costo real de un timbre PAC es de $0.30 a $1.00 MXN.** Cobrar $0.10 MXN por factura pierde dinero en cada emisión, antes de sumar cloud, cobranza, soporte y cumplimiento. El modelo unitario de 10 centavos no es viable tal como se planteó.
2. **La Ley Federal de Protección de Datos Personales en Posesión de los Particulares fue sustituida** por una nueva ley publicada en el DOF el 20 de marzo de 2025 y vigente desde el 21 de marzo de 2025. El INAI se extinguió y sus funciones pasaron a la Secretaría Anticorrupción y Buen Gobierno. Cualquier plan basado en la ley de 2010 y en lineamientos del INAI está desactualizado.
3. **La reforma al CFF publicada en el DOF el 7 de noviembre de 2025 y vigente desde el 1 de enero de 2026** endurece el régimen del CFDI: exige materialidad de la operación (art. 29-A fracc. IX), crea facultades expeditas de verificación (arts. 49 Bis y 29-A Bis), ata el plazo de cancelación al mes de la declaración anual, y prohíbe exigir la Constancia de Situación Fiscal como requisito para emitir un CFDI.
4. **CAPUFE ya resolvió el escaneo de QR en casetas.** Su portal permite facturar un ticket de peaje escaneando el QR, subiendo una foto o capturando el folio de 18 caracteres. El problema real del peaje no es "no puedo escanear un QR": es que hay decenas de concesionarias, cada una con su portal y su ventana de 30 días.

### Decisiones tomadas por el usuario en esta sesión

| Decisión | Elección |
|---|---|
| Rol fiscal del producto | Las tres modalidades, por fases |
| Modelo de cobro | Cobrar al negocio, no al consumidor final |
| Definición de "caseta" | Casetas de peaje reales |
| Entregable de esta sesión | Solo el documento maestro |

Como la respuesta fue "las tres, por fases", este documento **decide y justifica el orden**. Ver §1.4.

---

## Índice

| # | Sección |
|---|---|
| 1 | [Resumen ejecutivo](#1-resumen-ejecutivo) |
| 2 | [Supuestos explícitos e incertidumbres](#2-supuestos-explícitos-e-incertidumbres) |
| 3 | [Glosario](#3-glosario-técnico-fiscal-y-legal) |
| 4 | [Investigación fiscal mexicana](#4-investigación-fiscal-mexicana) |
| 5 | [Datos personales y fiscales requeridos](#5-datos-personales-y-fiscales-requeridos) |
| 6 | [Cumplimiento de protección de datos](#6-cumplimiento-de-protección-de-datos) |
| 7 | [Integración con SAT / PAC](#7-integración-con-sat--pac) |
| 8 | [Arquitectura técnica](#8-arquitectura-técnica) |
| 9 | [Stack tecnológico](#9-stack-tecnológico-comparativas-y-recomendación) |
| 10 | [Seguridad, secretos y ciberseguridad](#10-seguridad-secretos-y-ciberseguridad) |
| 11 | [Blockchain y tokenización](#11-blockchain-y-tokenización-evaluación-crítica) |
| 12 | [Historial y analítica](#12-historial-de-facturas-y-analítica) |
| 13 | [Modelo de negocio](#13-modelo-de-negocio-el-destino-de-los-10-centavos) |
| 14 | [Implementación en casetas y embebidos](#14-implementación-en-casetas-y-sistemas-embebidos) |
| 15 | [Reportes descargables](#15-reportes-fiscales-descargables) |
| 16 | [Producto y UX](#16-producto-y-ux) |
| 17 | [Escalabilidad nacional](#17-escalabilidad-a-todo-méxico) |
| 18 | [Expansión internacional](#18-expansión-internacional) |
| 19 | [Repositorio y control de versiones](#19-repositorio-y-control-de-versiones) |
| 20 | [Desarrollo local seguro](#20-desarrollo-local-seguro) |
| 21 | [Plan de investigación pendiente](#21-plan-de-investigación-pendiente) |
| 22 | [Matriz de riesgos](#22-matriz-de-riesgos) |
| 23 | [Roadmap por fases](#23-roadmap-por-fases) |
| 24 | [Equipo y roles](#24-equipo-y-roles) |
| 25 | [Métricas de éxito](#25-métricas-de-éxito) |
| 26 | [Checklists pre-producción](#26-checklists-pre-producción) |
| 27 | [Preguntas críticas sin resolver](#27-preguntas-críticas-sin-resolver) |
| 28 | [Recomendación final y próximos pasos](#28-recomendación-final-y-próximos-pasos) |
| 29 | [Fuentes](#29-fuentes-consultadas) |

---

## 1. Resumen ejecutivo

### 1.1 Qué es el producto

Una plataforma de facturación electrónica mexicana con tres capas de producto que comparten un mismo motor de CFDI:

- **Autofacturación (B2B2C):** un negocio contrata la plataforma; sus clientes escanean el QR de su ticket y obtienen su CFDI sin hablar con nadie. El CFDI se emite con el CSD del negocio a través de un PAC.
- **Emisión completa (B2B):** el negocio usa la plataforma como su sistema de facturación: ingresos, facturas globales, complementos de pago, cancelaciones, reportes.
- **Recuperación de gastos (B2B, lado receptor):** una empresa sube tickets y la plataforma le consigue las facturas correspondientes. Aquí no se emite CFDI: se gestiona ante terceros.

### 1.2 Qué problema resuelve

En el nicho elegido (peaje), el problema documentado no es la falta de un portal: es la **fragmentación**. No existe un portal único para facturar peajes. CAPUFE opera 42 autopistas y 32 puentes, pero el resto de la red está en manos de concesionarias privadas y estatales (Aleatica, IDEAL, CONMEX, Arco Norte, Autopistas del Sureste, autopistas estatales), cada una con su propio sistema y sus propias reglas. La ventana estándar para facturar en la mayoría de operadores es de 30 días naturales desde el cruce; pasado ese plazo el folio se cierra. Un transportista con 40 cruces al mes en 6 concesionarias distintas tiene que entrar a 6 portales, cada uno con su flujo, y no puede perder la ventana.

En el nicho ampliado (comercios), el problema es que emitir un CFDI correcto exige conocer catálogos, regímenes, usos de CFDI y reglas de cancelación que un operador de mostrador no domina, y que la reforma de 2026 castiga con más dureza que antes.

### 1.3 Por qué puede ser valioso

- **Volumen concentrado.** Una caseta con 20,000 cruces diarios y 5% de solicitud de factura genera ~30,000 CFDI al mes desde un solo punto de venta. Diez casetas equivalen comercialmente a cientos de comercios pequeños, con una fracción del costo de adquisición y de soporte.
- **Obligatoriedad de fondo.** La facturación electrónica en México no es opcional ni cíclica: es infraestructura fiscal permanente con demanda inelástica.
- **Endurecimiento regulatorio como viento de cola.** La reforma 2026 aumenta el costo de equivocarse, lo que aumenta la disposición a pagar por una herramienta que reduzca el error.
- **Reutilización real.** El motor de CFDI construido para la Fase 1 es el mismo activo de la Fase 2 y la base de la expansión internacional.

### 1.4 Recomendación de secuencia (decisión de este documento)

La respuesta "las tres, por fases" deja abierto el orden. Este es el orden recomendado y su justificación:

| Fase | Modalidad | Por qué en este orden |
|---|---|---|
| **1** | **Autofacturación B2B2C** | Construye el motor de CFDI, que es el activo compartido por todo lo demás. No requiere scraping de terceros. El cliente que paga es el negocio, que es exactamente el modelo de cobro elegido. |
| **2** | **Emisión completa B2B** | Es un superset natural de la Fase 1: mismo motor, más tipos de comprobante (globales, egreso, complementos de pago) y más reportes. Sube el ticket promedio sobre la misma base de clientes. |
| **3** | **Recuperación de gastos** | Se deja al final deliberadamente: no reutiliza el motor de emisión, depende de automatizar portales ajenos (riesgo de términos de servicio y de fragilidad técnica), y el mercado ya tiene competidores con precio agresivo. Solo debe hacerse vía convenio o API con los operadores, nunca por scraping no consentido. |

**Advertencia sobre el nicho de peaje.** El comprador de la Fase 1 en una caseta de peaje real es una concesionaria o CAPUFE. CAPUFE es un organismo público descentralizado del Gobierno Federal: venderle implica procesos de contratación pública, tiempos largos y requisitos de proveedor. Y ya tiene su portal funcionando, así que el argumento de venta tendría que ser sustitución, no cobertura de un hueco. **Recomendación de entrada: concesionarias privadas y estatales medianas**, cuyos portales son más débiles, cuyo ciclo de compra es comercial y no de licitación, y a quienes sí les falta la capacidad. CAPUFE, si acaso, después de tener tres casos de éxito demostrables.

### 1.5 Principales riesgos

| # | Riesgo | Severidad |
|---|---|---|
| 1 | El modelo de $0.10 MXN por factura tiene margen negativo estructural | Crítico |
| 2 | Ciclo de venta a concesionarias de peaje mucho más largo que el runway típico de un MVP | Crítico |
| 3 | Custodiar CSD de clientes convierte a la plataforma en objetivo de alto valor y en responsable de facturación no autorizada | Crítico |
| 4 | Reforma CFF 2026: un CFDI que no ampare una operación real se considera falso y pierde efectos fiscales | Alto |
| 5 | Nueva ley de datos personales con autoridad distinta y régimen de sanciones actualizado | Alto |
| 6 | Dependencia de un solo PAC (caída = cero facturación) | Alto |
| 7 | Competencia establecida y bien financiada en los tres segmentos | Alto |
| 8 | Doble timbrado por reintentos mal diseñados | Alto |

### 1.6 Viabilidad inicial

| Componente | Veredicto |
|---|---|
| Facturación electrónica como producto | **Viable.** Mercado real, obligatorio, con dolor documentado. |
| Escaneo de QR / folio / captura manual | **Viable y bien entendido.** No es el diferenciador; es requisito de entrada. |
| Historial y analítica por día/semana/mes/año | **Viable.** Trabajo directo, sin riesgo técnico. |
| Modelo de $0.10 MXN por factura | **No viable como precio unitario.** Ver §13. |
| Casetas de peaje reales como primer cliente | **Viable pero lento.** El GTM es el cuello de botella, no la tecnología. |
| Blockchain / tokenización | **No recomendado ahora.** No aporta valor probatorio sobre el timbre del SAT y choca con el derecho de cancelación. Ver §11. |
| Expansión internacional | **Viable a 24–36 meses**, no antes. |

### 1.7 Recomendación estratégica

**Reducir alcance y validar primero.** Concretamente:

1. **Matar el precio de $0.10 MXN por factura** como precio de lista. Mantenerlo, si se quiere, como métrica interna de costo objetivo.
2. **Un solo nicho, un solo tipo de comprobante en la Fase 1:** autofacturación de CFDI de ingreso con un piloto en una concesionaria mediana o un parador carretero de alto volumen.
3. **No custodiar CSD en la Fase 1.** Delegar el sellado y la custodia al PAC.
4. **Aplazar blockchain indefinidamente** y sustituirlo por un log append-only con hash encadenado y sellado de tiempo, que da el mismo valor de auditoría a costo casi cero.
5. **Antes de escribir código:** conseguir una carta de intención firmada de un operador. Sin eso, se estaría construyendo un motor de facturación sin comprador identificado, en un mercado con incumbentes.

---

## 2. Supuestos explícitos e incertidumbres

### 2.1 Supuestos que asumo (corrígeme si alguno es falso)

| # | Supuesto | Impacto si es falso |
|---|---|---|
| S1 | La moneda es MXN, no USD | Si fueran 10 centavos de dólar (~$1.85 MXN), el modelo unitario sí sería viable. Cambia toda la §13. |
| S2 | No serás PAC en el corto plazo; te integras con uno autorizado | Alto: ser PAC exige persona moral, fianza a favor de la TESOFE y cumplir el Anexo 1-A de la RMF |
| S3 | En la Fase 1 **no** se custodian CSD ni e.firma de clientes; el PAC los custodia | Alto: cambia todo el modelo de seguridad y la responsabilidad legal |
| S4 | Las casetas tienen conectividad estable (son instalaciones con sistemas de cobro en línea), pero se diseña degradación elegante | Medio: define si offline-first es requisito o cortesía |
| S5 | La plataforma **no** custodia ni transmite fondos de terceros | Crítico: custodiar fondos podría caracterizar la actividad como servicio financiero regulado |
| S6 | El usuario final es mayoritariamente persona física con RFC que quiere deducir un peaje o consumo | Medio: define la UX y qué campos se piden |
| S7 | Equipo inicial pequeño (1–3 personas técnicas) | Alto: descarta microservicios y Kubernetes en el MVP |
| S8 | Presupuesto de arranque limitado; sin capital institucional confirmado | Alto: descarta convertirse en PAC y competir por precio |

### 2.2 Qué está verificado y qué no

**Verificado con fuentes en esta sesión** (fechas y cifras contrastadas, ver §29):

- CFDI 4.0 es la única versión válida en 2026; los PAC rechazan la 3.3.
- El Anexo 20 se actualizó el 1 de enero de 2026, con nuevas claves de productos y servicios y tres nuevos códigos de régimen para el sector primario.
- Los cuatro motivos de cancelación (01–04) y su semántica.
- El receptor tiene 3 días hábiles para responder una solicitud de cancelación; el silencio se toma como aceptación.
- Existen supuestos de cancelación sin aceptación del receptor, entre ellos CFDI menores a $1,000 MXN y facturas al público en general dentro de las 24 horas.
- La reforma al art. 29-A CFF vigente en 2026 ata el plazo de cancelación al mes de la declaración anual del ejercicio de emisión.
- Nueva LFPDPPP: DOF 20 de marzo de 2025, vigente 21 de marzo de 2025; INAI extinto; competencia a la Secretaría Anticorrupción y Buen Gobierno; el medio de defensa pasa a juicio de amparo; las sanciones se calculan en UMA.
- Reforma CFF: DOF 7 de noviembre de 2025, vigente 1 de enero de 2026.
- Conservación: 5 años como regla general (art. 30 CFF), 10 años en materia mercantil (art. 38 Código de Comercio), caducidad de 10 años en ciertos supuestos (art. 67 CFF).
- Estructura del QR de verificación del CFDI: `id`, `re`, `rr`, `tt`, `fe`.
- Complemento Carta Porte 3.1 vigente desde el 17 de julio de 2024.
- Rango de precio de timbre PAC: $0.30–$1.00 MXN según volumen.
- RESICO PF: límite de $3.5M anuales; tasas de 1.00% a 2.50%; factura global únicamente mensual.
- Ley Fintech art. 30 y Circular 4/2019: operar con activos virtuales requiere autorización de Banxico; a mayo de 2026 Banxico no ha otorgado autorizaciones públicas bajo ese mecanismo.

**Requiere verificación antes de escribir una línea de código de producción:**

| # | Punto | Por qué está en duda |
|---|---|---|
| V1 | Número exacto de la regla de la RMF 2026 para factura global | Las fuentes citan 2.7.1.21, 2.7.1.22 y 2.7.1.24 de forma contradictoria. Hay que leer el texto publicado en el DOF. |
| V2 | Monto vigente de la fianza para ser PAC | Históricamente ligado al salario mínimo, hoy a la UMA; el monto está en la RMF vigente |
| V3 | Versión vigente del Complemento de Recepción de Pagos | No se confirmó documentalmente que 2.0 siga siendo la vigente en 2026 |
| V4 | Umbral exacto y condiciones de cancelación sin aceptación | El umbral de $1,000 MXN debe confirmarse contra la RMF 2026, no contra blogs |
| V5 | Reglamento de la nueva LFPDPPP | La ley es de marzo de 2025; hay que verificar si ya existe reglamento y qué pasó con los lineamientos previos del INAI |
| V6 | Obligación de plataformas digitales de dar acceso en línea y en tiempo real al SAT (vigente 1 abr 2026) | Hay que determinar si la plataforma cae en el supuesto y qué implica técnicamente |
| V7 | Tarifas reales de PAC a volumen | Los precios públicos son de lista; las tarifas reales se negocian |
| V8 | Si la autofacturación de tickets tiene requisitos formales específicos en la RMF | Una fuente afirma que "está regulado por el SAT"; hay que localizar la regla exacta |

> **Regla de trabajo:** ninguna afirmación de este documento marcada como pendiente de verificación debe convertirse en lógica de producto sin que un contador público o fiscalista la confirme contra el DOF o el portal del SAT. No se debe inventar ni asumir un requisito oficial.

---

## 3. Glosario técnico, fiscal y legal

### Fiscal mexicano

| Término | Definición simple |
|---|---|
| **CFDI** | Comprobante Fiscal Digital por Internet. El archivo XML que legalmente es "la factura" en México. Versión vigente: 4.0. |
| **Folio fiscal / UUID** | Identificador único de 36 caracteres que el SAT asigna al timbrar. Es la llave universal del comprobante. |
| **RFC** | Registro Federal de Contribuyentes. 12 caracteres para persona moral, 13 para persona física. |
| **CURP** | Clave Única de Registro de Población, 18 caracteres. Identifica personas, no contribuyentes. Rara vez necesaria para facturar. |
| **Régimen fiscal** | Categoría bajo la que tributa el contribuyente (601 General de Ley PM, 605 Sueldos y Salarios, 612 Actividades Empresariales PF, 616 Sin obligaciones fiscales, 626 RESICO, etc.). Va en emisor y receptor. |
| **Uso de CFDI** | Para qué usará el receptor la factura (G01 Adquisición de mercancías, G03 Gastos en general, D01 Honorarios médicos, S01 Sin efectos fiscales, etc.). Debe ser compatible con el régimen del receptor. |
| **Forma de pago** | *Con qué* se pagó: 01 Efectivo, 03 Transferencia, 04 Tarjeta de crédito, 28 Tarjeta de débito, 99 Por definir. |
| **Método de pago** | *Cuándo* se paga: PUE (una sola exhibición) o PPD (parcialidades o diferido). PPD obliga a emitir complemento de pago después. |
| **Emisor** | Quien expide el comprobante. Es el titular del CSD que lo sella. |
| **Receptor** | A quien se le expide. |
| **PAC** | Proveedor Autorizado de Certificación. Persona moral autorizada por el SAT para validar y timbrar CFDI fuera del domicilio fiscal del contribuyente. |
| **Timbrado** | El acto por el que el PAC valida el XML, lo certifica y le incorpora el Timbre Fiscal Digital con el UUID. Sin timbre no hay factura. |
| **Sello digital** | Firma criptográfica del emisor sobre la cadena original del comprobante. |
| **CSD** | Certificado de Sello Digital. Par de llaves emitido por el SAT que sirve *solo* para sellar CFDI. |
| **e.firma** | Firma electrónica avanzada. Sirve para trámites, no para sellar facturas de forma rutinaria. Es más sensible que el CSD y no debe usarse para facturar. |
| **Cadena original** | Cadena de texto derivada del XML según una transformación oficial (XSLT), sobre la cual se calcula el sello. |
| **QR del CFDI** | Código impreso en la representación gráfica que codifica una URL al verificador del SAT con `id` (UUID), `re` (RFC emisor), `rr` (RFC receptor), `tt` (total) y `fe` (últimos 8 caracteres del sello). **Es un QR de verificación, no de autofacturación.** |
| **Cancelación de CFDI** | Proceso para dejar sin efectos un comprobante, con motivo (01–04) y, en muchos casos, aceptación del receptor. |
| **Complemento de pago (REP)** | Comprobante que se emite al recibir un pago de una operación PPD. |
| **Complemento Carta Porte** | Complemento obligatorio para acreditar el traslado de mercancías. Versión 3.1 desde el 17 de julio de 2024. |
| **Nota de crédito** | CFDI de tipo Egreso (E) que documenta devoluciones, descuentos o bonificaciones. |
| **Factura global** | CFDI que agrupa las operaciones del periodo con el público en general que no pidieron comprobante individual. |
| **Público en general** | Receptor genérico: RFC `XAXX010101000`, nombre "Público en General", régimen 616, y **código postal del domicilio fiscal del emisor**. |
| **RESICO** | Régimen Simplificado de Confianza. Personas físicas hasta $3.5M anuales, ISR de 1.00% a 2.50% sobre ingresos cobrados. |
| **EFOS / EDOS** | Empresas que Facturan Operaciones Simuladas / que Deducen Operaciones Simuladas. Listas negras del SAT. |

### Técnico, de seguridad y de negocio

| Término | Definición simple |
|---|---|
| **Blockchain** | Registro distribuido, replicado y prácticamente inmutable, sin operador central. |
| **Blockchain pública / privada / permisionada** | Cualquiera participa / un solo operador controla / un consorcio identificado controla quién escribe. |
| **Hash** | Huella digital de longitud fija de un archivo (p. ej. SHA-256). Cambia por completo si el archivo cambia un byte. |
| **Anclaje de datos (hash anchoring)** | Publicar solo el hash de un documento en un registro externo para poder probar después que el documento no cambió. |
| **Tokenización (dos sentidos)** | (a) Seguridad: sustituir un dato sensible por un sustituto sin valor. (b) Cripto: representar un activo como token en una cadena. **No confundir.** |
| **NFT** | Token no fungible: identificador único en cadena. No sustituye a un comprobante fiscal. |
| **Smart contract** | Código que se ejecuta en una cadena cuando se cumplen condiciones. |
| **Stablecoin** | Token diseñado para mantener paridad con una moneda fiat. En México su tratamiento cae bajo la Ley Fintech. |
| **Wallet** | Software que custodia llaves privadas para firmar transacciones en cadena. |
| **KYC / AML** | Conoce a tu cliente / prevención de lavado de dinero. |
| **Datos personales** | Cualquier información sobre una persona física identificada o identificable. |
| **Datos personales sensibles** | Los que pueden originar discriminación o riesgo grave (salud, origen étnico, creencias, orientación sexual, biométricos). |
| **Datos patrimoniales o financieros** | Bienes, ingresos, cuentas, historial crediticio. En México su tratamiento exige consentimiento expreso. |
| **Derechos ARCO** | Acceso, Rectificación, Cancelación y Oposición sobre los propios datos personales. |
| **Multi-tenant** | Una sola instancia de software sirve a muchos clientes con sus datos estrictamente aislados. |
| **Offline-first** | Diseño donde la app funciona sin red y sincroniza al reconectar. |
| **Idempotencia** | Repetir la misma operación produce el mismo resultado sin efectos duplicados. Crítica para no timbrar dos veces. |
| **Auditoría** | Registro verificable de quién hizo qué, cuándo y desde dónde. |
| **Trazabilidad** | Poder reconstruir el ciclo de vida completo de un documento. |
| **Envelope encryption** | Cifrar el dato con una llave de datos, y cifrar esa llave con una llave maestra que vive en un KMS o HSM. |
| **RLS (Row Level Security)** | Mecanismo de PostgreSQL que aplica el filtro de tenant en el motor, no en el código de la aplicación. |

---

## 4. Investigación fiscal mexicana

### 4.1 Cómo funciona hoy el CFDI 4.0

El flujo canónico de emisión:

```
1. El emisor arma un XML conforme al Anexo 20 v4.0
2. Genera la cadena original aplicando la transformación XSLT oficial
3. Sella la cadena con la llave privada de su CSD → atributo Sello
4. Envía el XML al PAC
5. El PAC valida estructura, catálogos, reglas de negocio y vigencia del CSD
6. El PAC certifica y agrega el Timbre Fiscal Digital (UUID, fecha, sello del SAT)
7. El PAC entrega el XML timbrado y envía copia al SAT
8. El emisor entrega el XML (y opcionalmente su representación impresa) al receptor
```

**Consecuencia arquitectónica clave:** los pasos 2 y 3 son los más delicados de implementar bien (la canonicalización XML y el orden de los nodos son notoriamente frágiles). La mayoría de las APIs comerciales de facturación los absorben. Delegarlos es una decisión de riesgo, no de pereza.

### 4.2 Tipos de comprobante

| Clave | Tipo | Uso |
|---|---|---|
| **I** | Ingreso | Venta de bienes o servicios. **El único necesario en la Fase 1.** |
| **E** | Egreso | Notas de crédito: devoluciones, descuentos, bonificaciones |
| **T** | Traslado | Movimiento de mercancía sin transmisión de propiedad |
| **N** | Nómina | Pago de sueldos y salarios (requiere complemento de nómina) |
| **P** | Pago | Recepción de pagos de operaciones PPD (requiere complemento REP) |

### 4.3 Campos obligatorios de un CFDI de ingreso (resumen operativo)

**Nivel Comprobante:** Version (`4.0`), Fecha, Sello, NoCertificado, Certificado, SubTotal, Moneda, Total, TipoDeComprobante, Exportacion, LugarExpedicion (CP del emisor), y condicionalmente Serie, Folio, FormaPago, MetodoPago, TipoCambio, Descuento, CondicionesDePago.

**Emisor:** Rfc, Nombre (exactamente como está en la Constancia de Situación Fiscal), RegimenFiscal.

**Receptor:** Rfc, Nombre, DomicilioFiscalReceptor (código postal), RegimenFiscalReceptor, UsoCFDI. Y condicionalmente ResidenciaFiscal y NumRegIdTrib para extranjeros.

**Conceptos (uno o más):** ClaveProdServ, Cantidad, ClaveUnidad, Descripcion, ValorUnitario, Importe, ObjetoImp, y condicionalmente NoIdentificacion, Unidad, Descuento, Impuestos por concepto.

**Impuestos:** traslados y retenciones por concepto con Base, Impuesto (001 ISR, 002 IVA, 003 IEPS), TipoFactor (Tasa, Cuota, Exento), TasaOCuota, Importe; más el resumen a nivel comprobante.

**Timbre Fiscal Digital (lo agrega el PAC):** UUID, FechaTimbrado, RfcProvCertif, SelloCFD, NoCertificadoSAT, SelloSAT.

### 4.4 Reglas duras que la app debe respetar

Estas son verificadas y no negociables:

1. **No se permiten números negativos en ningún campo.** Los descuentos y devoluciones se manejan con el campo Descuento o con un CFDI de egreso, nunca con importes negativos.
2. **Los decimales no pueden exceder lo que permite el estándar** para cada campo y moneda.
3. **La combinación Régimen del receptor × Uso de CFDI está validada.** No todo uso es válido para todo régimen; el PAC rechaza combinaciones inválidas.
4. **El nombre debe coincidir exactamente con la Constancia de Situación Fiscal**, incluyendo o excluyendo el régimen societario según corresponda. Es la causa número uno de rechazo en CFDI 4.0.
5. **El código postal del receptor es el de su domicilio fiscal**, no el de entrega. En factura global es el del emisor.
6. **Usar claves genéricas de ClaveProdServ (`01010101`, `84111506`) de forma sistemática es un patrón que el SAT detecta** y eleva el riesgo de auditoría. La app no debe ofrecerlas como valor por defecto cómodo.
7. **Desde 2026, el CFDI debe amparar una operación real y verídica** (art. 29-A fracc. IX CFF). Un comprobante que no corresponde a un acto jurídico real se considera falso y pierde efectos fiscales.
8. **Exigir la Constancia de Situación Fiscal como condición para emitir un CFDI es una infracción** conforme al art. 83 CFF reformado. La UX no debe bloquear la emisión por no tenerla.

### 4.5 Cancelación: el área de mayor riesgo operativo

**Motivos:**

| Clave | Motivo | ¿Requiere sustitución? | Cuándo usarlo |
|---|---|---|---|
| **01** | Comprobante emitido con errores con relación | **Sí.** Se emite primero el CFDI correcto y se relaciona el UUID cancelado | La operación sí ocurrió pero hay datos mal (monto, RFC, concepto) |
| **02** | Comprobante emitido con errores sin relación | No | Duplicados, o ajustes que se resuelven con un CFDI de egreso |
| **03** | No se llevó a cabo la operación | No | La transacción nunca sucedió |
| **04** | Operación nominativa relacionada en una factura global | No | El cliente pidió factura nominativa después de estar amparado en la global |

**Plazos (reforma vigente 2026):** los comprobantes solo pueden cancelarse a más tardar en el mes en que deba presentarse la declaración anual del ISR del ejercicio en que fueron emitidos, con aceptación del receptor. En la práctica, para comprobantes de un ejercicio: personas morales hasta marzo del siguiente año, personas físicas hasta abril. Vencido el plazo, solo procede por autorización especial del SAT. Las multas por cancelar fuera de plazo van del 5% al 10% del valor de cada factura (art. 81 fracc. XLVI CFF).

**Aceptación del receptor:** la solicitud llega por Buzón Tributario; el receptor tiene 3 días hábiles para responder y el silencio se toma como aceptación. Hay supuestos de cancelación sin aceptación: montos menores a $1,000 MXN, CFDI de nómina, facturas al público en general dentro de las 24 horas, y cancelación el mismo día de emisión *(umbral y supuestos pendientes de verificar contra RMF 2026 — V4)*.

**Casos donde una factura no se cancela fácilmente:**
- Ya venció el plazo ligado a la declaración anual.
- El receptor rechaza expresamente la solicitud.
- Existen CFDI relacionados vivos (complementos de pago, egresos, sustituciones) que deben cancelarse primero.
- El emisor tiene el CSD restringido. El SAT restringió sellos a más de 7,300 contribuyentes en 2026 por presuntos vínculos con EFOS; sin CSD vigente no se puede emitir ni cancelar.

**Regla de producto derivada:** *no se puede cancelar una factura porque el cliente no pagó.* La UI debe impedir explícitamente ese razonamiento y ofrecer la ruta correcta.

### 4.6 Factura global y público en general

- Puede emitirse **diaria, semanal o mensual**. Los contribuyentes en RESICO **solo pueden emitirla mensualmente**.
- Plazo de timbrado: hasta 24 horas después del cierre del periodo.
- Datos del receptor: RFC `XAXX010101000`, nombre "Público en General", régimen 616, código postal del domicilio fiscal **del emisor**.
- Debe separarse el monto del IVA y del IEPS.
- **Error crítico:** incluir en la global operaciones ya facturadas individualmente duplica ingresos ante el SAT.
- No agrupar en un mismo concepto productos con tasas distintas (16%, 0%, exento).

> **Insight arquitectónico.** La factura global es el **mecanismo de contingencia fiscal nativo** del sistema mexicano. El negocio ampara todas las ventas del periodo en la global; el cliente que quiere factura nominativa la solicita después y esa operación se extrae con el motivo de cancelación 04. Esto significa que **la app no necesita timbrar en tiempo real en el mostrador**: puede aceptar la solicitud, entregar un acuse provisional no fiscal y timbrar en cuanto haya red, dentro del periodo. Esto reduce muchísimo la presión sobre la arquitectura offline.

### 4.7 Casos especiales que la app debe contemplar

| Caso | Qué implica |
|---|---|
| **Extranjeros / exportación** | RFC genérico `XEXX010101000`, `ResidenciaFiscal`, `NumRegIdTrib`, atributo `Exportacion` con clave distinta de `01` |
| **IVA 0% vs exento vs no objeto** | Son tres cosas distintas: 0% lleva TipoFactor `Tasa` con TasaOCuota `0.000000`; exento lleva TipoFactor `Exento` sin importe; no objeto se marca con `ObjetoImp = 01` a nivel concepto |
| **Retenciones de IVA e ISR** | Comunes en servicios profesionales, arrendamiento y fletes. Van en el nodo de retenciones por concepto y en el resumen |
| **PPD y complemento de pago** | Si MetodoPago es PPD, hay obligación posterior de emitir el REP al cobrar. La app debe rastrear el saldo insoluto |
| **RESICO** | Tasas de 1.00%–2.50%; global solo mensual; sin contabilidad electrónica (art. 113-G LISR); el IVA no se simplifica |
| **Plataformas tecnológicas** | Retenciones específicas; y desde el 1 de abril de 2026, obligación de dar al SAT acceso en línea y en tiempo real a datos operativos *(requiere análisis de si la plataforma cae en el supuesto — V6)* |
| **Nómina, arrendamiento, servicios profesionales, sector primario, donatarias, gobierno** | Cada uno tiene combinaciones válidas de régimen, uso de CFDI y complementos. **Ninguno debe entrar al alcance de la Fase 1.** |

### 4.8 Errores frecuentes de usuarios no técnicos

Esta lista es la especificación real de la UX (§16):

1. Escribir mal el nombre o razón social respecto de la Constancia de Situación Fiscal.
2. Poner el código postal de entrega en vez del fiscal.
3. Elegir un Uso de CFDI incompatible con su régimen.
4. Confundir forma de pago con método de pago.
5. Marcar PUE cuando en realidad se pagará después (o al revés), generando obligación de REP no cumplida.
6. Pedir cancelación por falta de pago.
7. Facturar fuera de la ventana del operador (30 días en la mayoría de concesionarias de peaje).
8. Facturar dos veces el mismo ticket.
9. Elegir motivo de cancelación 02 cuando corresponde 01, dejando la operación sin comprobante válido.

---

## 5. Datos personales y fiscales requeridos

**Leyenda:** O = obligatorio · C = condicional · Op = opcional
**Clasificación:** P = dato personal · PF = patrimonial/financiero · S = sensible · N = no personal

| Dato | Oblig. | Lo aporta | Para qué sirve | Dónde va en el CFDI | Clasif. | ¿Cifrar? | ¿Enmascarar en UI? | Retención sugerida | Riesgo si se maneja mal |
|---|---|---|---|---|---|---|---|---|---|
| RFC receptor | O | Usuario | Identifica al contribuyente | `Receptor@Rfc` | P (PF si es PF) | Sí, en reposo | Parcial (`XAXX****1000`) | 10 años | Suplantación; facturación no autorizada a nombre de un tercero |
| Nombre / razón social | O | Usuario | Debe coincidir con la CSF | `Receptor@Nombre` | P | Sí | No | 10 años | Rechazo de timbrado; exposición de identidad |
| Régimen fiscal receptor | O | Usuario | Valida el Uso de CFDI | `Receptor@RegimenFiscalReceptor` | P | No | No | 10 años | Rechazo; inferencia de perfil económico |
| Código postal fiscal receptor | O | Usuario | Domicilio fiscal | `Receptor@DomicilioFiscalReceptor` | P | Sí | No | 10 años | Geolocalización aproximada |
| Uso de CFDI | O | Usuario | Destino fiscal del gasto | `Receptor@UsoCFDI` | P | No | No | 10 años | Revela naturaleza del gasto |
| Domicilio fiscal completo | Op | Usuario | No requerido por CFDI 4.0 | — | P | Sí | Sí | **No recopilar** | Recolección innecesaria: violación del principio de minimización |
| CURP | Op | Usuario | **No se requiere para facturar** | — | P | Sí | Sí | **No recopilar** | Identificador nacional único; alto valor para suplantación |
| Correo electrónico | C | Usuario | Entregar XML y PDF | Fuera del CFDI | P | Sí | Parcial | Mientras dure la relación + 1 año | Spam, phishing, correlación entre servicios |
| Teléfono / WhatsApp | Op | Usuario | Canal alterno de entrega | Fuera del CFDI | P | Sí | Parcial | Igual que correo | Requiere consentimiento específico para mensajería |
| Forma de pago | C | Negocio | Con qué se pagó | `Comprobante@FormaPago` | PF | No | No | 10 años | Revela hábitos de pago |
| Método de pago | C | Negocio | PUE o PPD | `Comprobante@MetodoPago` | PF | No | No | 10 años | — |
| Monto / SubTotal / Total | O | Negocio | Base del comprobante | `Comprobante@Total` | PF | Sí | No | 10 años | Perfilado económico del titular |
| Impuestos (IVA/ISR/IEPS) | C | Sistema | Cálculo fiscal | Nodo `Impuestos` | PF | No | No | 10 años | — |
| UUID / folio fiscal | O | PAC | Llave universal del CFDI | `TimbreFiscalDigital@UUID` | P (vinculante) | No (es índice) | No | 10 años | Con RFC y total permite verificar el CFDI ante el SAT |
| Fecha de emisión | O | Sistema | Momento del acto | `Comprobante@Fecha` | N | No | No | 10 años | — |
| Serie y folio interno | Op | Negocio | Control interno | `Comprobante@Serie/@Folio` | N | No | No | 10 años | Fuga de volumen de operación |
| XML timbrado completo | O | PAC | **Documento fiscal oficial** | — | P + PF | **Sí, obligatorio** | N/A | 10 años | Contiene todos los datos juntos: es el activo más sensible |
| CSD (certificado público) | O | Negocio | Verificar el sello | `Comprobante@Certificado` | N | No | No | Vigencia + 10 años | Es público, no es secreto |
| **Llave privada del CSD** | C | Negocio | **Sellar el CFDI** | Nunca va en el CFDI | **Crítico** | **KMS/HSM. Nunca en BD ni en repo** | **Nunca visible** | Vigencia | **Robo = facturación no autorizada a nombre del cliente. Riesgo máximo del sistema.** |
| e.firma | **No** | — | **No debe pedirse** | — | Crítico | N/A | N/A | **No recopilar** | Da acceso a trámites fiscales completos. No hay caso de uso legítimo en la Fase 1 |
| ClaveProdServ / ClaveUnidad | O | Negocio | Clasificación SAT | Nodo `Concepto` | N | No | No | 10 años | Claves genéricas elevan riesgo de auditoría |
| Acuse de cancelación | C | SAT/PAC | Prueba de cancelación | Documento aparte | P | Sí | No | 10 años | Sin él no se puede probar la cancelación |
| Historial de facturas | — | Sistema | Consulta y reportes | Base de datos | P + PF | Sí | Parcial | 10 años | Agregado = perfil económico completo del titular |
| Datos de tarjeta / cuenta | Op | Pasarela | Cobro de la suscripción | Fuera del CFDI | PF | **Nunca almacenar. Tokenizar en la pasarela** | Solo últimos 4 | Token, no PAN | Alcance PCI-DSS; nunca deben tocar los servidores propios |
| Contraseña de usuario | O | Usuario | Autenticación | — | P | **Argon2id, nunca reversible** | Nunca | Mientras dure la cuenta | Reutilización de credenciales |
| Dirección IP | — | Sistema | Seguridad, rate limiting | Logs | P | Sí | Sí | 90 días | Es dato personal; retención larga es desproporcionada |
| Device fingerprint | Op | Sistema | Antifraude | Logs | P | Sí | Sí | 90 días | Requiere aviso; rastreo persistente |
| Consentimientos y preferencias | O | Usuario | Prueba de cumplimiento | Base de datos | P | Sí | No | Mientras dure la relación + 5 años | **Sin registro de consentimiento no hay defensa ante una queja** |
| Metadatos de auditoría | O | Sistema | Trazabilidad | Log append-only | P | Sí | Parcial | 10 años | Deben ser inmutables o con verificación de integridad |

### Principios derivados de esta tabla

1. **No pedir CURP, ni domicilio completo, ni e.firma.** No son necesarios para emitir un CFDI de ingreso. Pedirlos viola el principio de minimización y multiplica el daño de una fuga.
2. **El XML timbrado es el activo más sensible del sistema.** Contiene identidad, domicilio postal, monto y naturaleza del gasto en un solo archivo. Debe ir cifrado en reposo, con acceso auditado y URLs firmadas de vigencia corta.
3. **La llave privada del CSD es el riesgo existencial.** Su robo permite emitir facturas a nombre del cliente. Por eso la Fase 1 no la custodia.
4. **La retención sugerida es de 10 años, no 5.** El art. 30 CFF pide 5, pero el art. 38 del Código de Comercio pide 10 en materia mercantil y el art. 67 CFF extiende la caducidad a 10 años en ciertos supuestos. Diez años es el número seguro. *Confirmar con abogado.*
5. **Correo, teléfono, IP y fingerprint se retienen mucho menos que los datos fiscales.** Su finalidad se agota antes. Retenerlos 10 años sería desproporcionado.

---

## 6. Cumplimiento de protección de datos

### 6.1 Marco legal vigente (cambió en 2025)

| Norma | Estatus |
|---|---|
| **LFPDPPP nueva** | Publicada en el DOF el **20 de marzo de 2025**, vigente desde el **21 de marzo de 2025**. Abroga la LFPDPPP de 2010. |
| **LFPDPPP de 2010** | **Abrogada.** No citar como fundamento. |
| **LGPDPPSO (2017)** | Abrogada por el mismo decreto |
| **INAI** | **Extinto.** Sus funciones en materia de particulares pasaron a la **Secretaría Anticorrupción y Buen Gobierno** |
| Secretaría de Economía | **Ya no** es autoridad reguladora en la materia |
| Medio de defensa | **Juicio de amparo** ante jueces y tribunales especializados, en sustitución del juicio de nulidad ante el TFJA |
| Sanciones | Calculadas en **UMA** (antes salario mínimo). Se añadió como infracción actuar con negligencia o dolo en la sustanciación de solicitudes ARCO |
| Reglamento | **Pendiente de verificar (V5).** No se confirmó si ya existe reglamento nuevo ni la vigencia de los lineamientos previos del INAI |

> **Acción inmediata:** todo material del proyecto (aviso de privacidad, política interna, contratos) debe redactarse contra la ley de 2025. Cualquier plantilla que mencione al INAI o la ley de 2010 es obsoleta y expone a la empresa.

### 6.2 Clasificación de datos

| Categoría | Definición | En este proyecto | Consentimiento |
|---|---|---|---|
| **Personales** | Sobre persona física identificada o identificable | RFC de PF, nombre, correo, CP, IP | Tácito válido como regla general bajo la nueva ley |
| **Sensibles** | Pueden originar discriminación o riesgo grave | **Ninguno. El diseño debe garantizar que nunca entren.** | Expreso y por escrito |
| **Patrimoniales / financieros** | Bienes, ingresos, cuentas | Montos, impuestos, formas de pago, historial completo | Expreso (criterio conservador recomendado) |
| **No personales** | Persona moral | RFC de PM, razón social | No aplica |

**Punto crítico:** el corazón del producto es el historial de facturación de personas físicas. Agregado, es un retrato patrimonial. **Recomendación conservadora: tratar todo el historial de facturación de personas físicas como dato patrimonial y recabar consentimiento expreso**, aun cuando la nueva ley admita el consentimiento tácito como regla general. Confirmar con abogado.

### 6.3 Obligaciones del responsable

- [ ] Aviso de privacidad integral (sitio) y simplificado (punto de captura: pantalla de la caseta, QR).
- [ ] Registro de consentimiento con marca de tiempo, versión del aviso aceptada y evidencia. **Sin esto no hay defensa ante una queja.**
- [ ] Distinguir finalidades primarias (emitir y entregar el CFDI, conservarlo por obligación legal) de secundarias (marketing, analítica de producto, mejora del servicio) con opción de negarse a las secundarias **sin perder el servicio**.
- [ ] Mecanismo funcional de derechos ARCO con plazos y responsable designado.
- [ ] Departamento o persona de datos personales designada y publicada.
- [ ] Medidas de seguridad administrativas, técnicas y físicas documentadas.
- [ ] Procedimiento de notificación de vulneraciones.
- [ ] Inventario de transferencias y remisiones a terceros.
- [ ] Contratos de encargado con cada proveedor que trate datos por cuenta de la plataforma.

### 6.4 Transferencias y remisiones

| Destinatario | Naturaleza | Base | Nota |
|---|---|---|---|
| **PAC** | Remisión (encargado) | Necesaria para cumplir la obligación de timbrar | Contrato de encargado obligatorio |
| **SAT** | Transferencia por disposición legal | Obligación legal | No requiere consentimiento |
| **Proveedor cloud** | Remisión | Prestación del servicio | Contrato + cláusulas de seguridad. Definir residencia de datos |
| **Pasarela de pago** | Transferencia | Necesaria para el cobro | Solo datos del negocio, nunca del consumidor final |
| **Correo transaccional** | Remisión | Entrega del CFDI | Contrato de encargado |
| **Herramientas de analítica** | Transferencia | **Finalidad secundaria** | Requiere consentimiento separado. Nunca enviar RFC ni montos |

### 6.5 Retención y supresión

```
Datos fiscales (XML, PDF, acuses, metadatos)   → 10 años   [obligación legal, no suprimible antes]
Registro de consentimientos                     → relación + 5 años
Correo y teléfono                               → relación + 1 año
IP, user-agent, device fingerprint              → 90 días
Logs de aplicación con datos personales         → 90 días, luego anonimizar
Logs de auditoría de acciones                   → 10 años (pseudonimizados)
Sesiones y tokens                               → según expiración
```

**Cómo responder a una solicitud de cancelación (supresión) sobre datos fiscales:** no se suprime el CFDI, porque su conservación es una obligación legal que prevalece. Se explica la excepción al titular, se suprimen los datos accesorios que sí se pueden borrar (correo, teléfono, preferencias, telemetría) y se documenta la respuesta. **Este flujo debe estar diseñado en el producto, no improvisado.**

### 6.6 Blockchain y datos personales: el conflicto estructural

Poner datos personales en una cadena pública crea un conflicto que **no tiene solución técnica limpia**:

| Derecho / principio | Conflicto con blockchain pública |
|---|---|
| Cancelación / supresión | La cadena es inmutable por diseño. No se puede borrar. |
| Rectificación | No se puede modificar un registro escrito. |
| Limitación de finalidad | Cualquiera puede leer y reutilizar los datos. |
| Transferencias internacionales | Los nodos están en jurisdicciones indeterminadas. |
| Identificación del responsable | En una red sin operador, ¿quién responde ante la autoridad? |

**Un hash no siempre resuelve el problema.** Si el conjunto de datos de entrada es pequeño y predecible (por ejemplo, un RFC), el hash es reversible por fuerza bruta y sigue siendo dato personal. Si se usa hash, **debe ser sobre el documento completo con sal secreta**, nunca sobre campos individuales.

**Regla no negociable: ningún dato personal ni fiscal identificable se escribe en una cadena pública. Ni en claro, ni cifrado, ni hasheado campo por campo.**

---

## 7. Integración con SAT / PAC

### 7.1 Comparación de opciones

| | **A. Ser PAC** | **B. Integrar con PAC** | **C. API de facturación** | **D. Emisión directa con CSD del cliente** | **E. Híbrido** |
|---|---|---|---|---|---|
| **Qué es** | Obtener autorización del SAT para certificar | Conectarse a un PAC autorizado; se arma y sella el XML propio | Usar un proveedor que abstrae XML, sellado y timbre | Custodiar el CSD y sellar en infraestructura propia | Capa de abstracción sobre 2+ PAC |
| **Req. técnicos** | Infra certificada, DRP, SLA con el SAT, auditorías | Cliente SOAP/REST, XSLT de cadena original, sellado, catálogos | Cliente HTTP y webhooks | Todo lo de B más gestión de llaves | Todo lo de B más enrutamiento y conciliación |
| **Req. legales** | Persona moral, al corriente fiscal, requisitos del Anexo 1-A RMF, **fianza a favor de la TESOFE dentro de 30 días naturales**, convenio de confidencialidad, SLA firmado | Contrato con el PAC | Contrato comercial | Autorización expresa del cliente para usar su CSD | Contratos con cada PAC |
| **Costo** | Muy alto (fianza, infra, cumplimiento continuo) | Bajo: $0.30–$1.00 por timbre | Medio-bajo | Bajo en licencias, alto en seguridad | Medio |
| **Tiempo** | 12–24 meses (estimado, **verificar**) | 2–6 semanas | 3–10 días | 6–12 semanas | 8–16 semanas |
| **Complejidad** | Extrema | Media-alta | Baja | Alta | Alta |
| **Riesgo principal** | Perder la autorización = perder el negocio | Dependencia de un proveedor | Menor control y márgenes | **Robo de llaves privadas** | Complejidad operativa |
| **¿MVP?** | **No** | Sí | **Sí** | **No** | No |
| **¿Producción seria?** | Solo a gran escala | Sí | Sí, con reservas de margen | Solo con HSM y auditoría | Sí, en Fase 3 |

### 7.2 Recomendación

- **Fase 1 (MVP):** **Opción C** — una API de facturación con sandbox abierto. Sandbox sin costo, sin manejo de llaves privadas, integración en días. El objetivo de la Fase 1 es validar la venta y el flujo de autofacturación, no demostrar dominio del XSLT del SAT.
- **Fase 2:** migrar a **Opción B** con un PAC directo, para recuperar margen y control. Diseñar desde el día uno una **interfaz `TimbradoProvider`** para que el cambio sea de configuración, no de reescritura.
- **Fase 3:** **Opción E** con dos PAC y failover automático, cuando la caída de un proveedor sea un riesgo de negocio material.
- **Opción D:** solo si un cliente grande lo exige contractualmente, y solo con HSM o KMS gestionado, MFA para cada uso y auditoría por operación.

**Proveedores identificados con ambiente de pruebas** (verificar términos y precios directamente con cada uno): Facturama (sandbox abierto), SW sapien, Factura.com, Facturación Moderna, Fiscalapi (con SDK de Go), Finkok, gigstack.

### 7.3 Operaciones críticas de integración

**Validar un folio fiscal / verificar un CFDI.** El SAT expone un servicio de consulta que recibe versión, RFC emisor, RFC receptor, total, UUID y sello, y devuelve estado (`Vigente`/`Cancelado`), si es cancelable, estatus de cancelación y validación EFOS. *Nota importante: el SAT dejó de publicar el WSDL de ese servicio en octubre de 2018, aunque el servicio sigue operando. Diseñar el cliente con tolerancia a cambios no anunciados y con caché.*

**Idempotencia contra doble timbrado** — el requisito técnico más importante de todo el sistema:

```
1. El cliente envía Idempotency-Key (UUIDv4) en el header
2. INSERT en tabla idempotency_keys con UNIQUE(tenant_id, key)
   → si hay conflicto: devolver la respuesta previamente almacenada
3. Estado inicial: PENDIENTE. Se persiste ANTES de llamar al PAC
4. Se llama al PAC con timeout agresivo (10–15 s)
5. Resultado:
   - Éxito                → estado TIMBRADO, se guarda el UUID
   - Rechazo de negocio   → estado RECHAZADO, no se reintenta
   - Timeout / red        → estado INCIERTO
6. Un INCIERTO NUNCA se reintimbra ciegamente.
   Se consulta primero al PAC por la referencia propia
   para saber si el timbre ya existe.
7. Solo si se confirma que no existe, se reintenta con la MISMA clave
```

**Manejo de errores y contingencias:**

| Escenario | Respuesta |
|---|---|
| Error de validación del PAC (400) | No reintentar. Traducir el código a lenguaje humano y mostrarlo. |
| Timeout del PAC | Marcar INCIERTO, encolar reconciliación, informar "en proceso" al usuario. Nunca decir "falló". |
| PAC caído (5xx sostenido) | Circuit breaker. Encolar solicitudes. Informar demora. Fase 3: failover al PAC secundario. |
| CSD del cliente restringido o vencido | Bloquear emisión con mensaje claro. No es un error de la plataforma. |
| Catálogo desactualizado | Job diario de sincronización + alerta. Nunca fallar silenciosamente. |

**Almacenamiento y retención:** XML timbrado (fuente oficial), representación impresa PDF (regenerable, no es la fuente), acuses de cancelación, metadatos y el request/response completo del PAC. Todo en object storage con versionado, cifrado en reposo, y hash SHA-256 del XML persistido en la base para verificación de integridad. Retención de 10 años con lifecycle a almacenamiento frío.

**Validaciones que la app debe hacer *antes* de llamar al PAC** (cada una que se atrape localmente es un timbre no desperdiciado y un usuario no frustrado): formato y dígito verificador del RFC; existencia del CP en el catálogo; compatibilidad Régimen × Uso de CFDI; claves de producto y unidad existentes; suma de conceptos igual al subtotal; suma de impuestos correcta; decimales dentro de límite; sin números negativos; combinación válida de forma y método de pago; fecha dentro de las 72 horas de tolerancia.

---

## 8. Arquitectura técnica

### 8.1 MVP (Fase 1) — monolito modular en Go

Con un equipo de 1–3 personas, los microservicios son un impuesto sin beneficio. La recomendación es **un monolito modular**: un solo binario con fronteras internas limpias, del que se puedan extraer servicios después si hace falta.

```
                    ┌─────────────────────────────┐
   Navegador  ──────│  Caddy (TLS, HTTP/3)        │
   (PWA)            └──────────────┬──────────────┘
                                   │
                    ┌──────────────▼──────────────┐
                    │   API Go (monolito modular) │
                    │  ┌───────────────────────┐  │
                    │  │ auth · tenants        │  │
                    │  │ facturacion (motor)   │  │
                    │  │ catalogos SAT         │  │
                    │  │ reportes              │  │
                    │  │ auditoria             │  │
                    │  │ archivos              │  │
                    │  └───────────────────────┘  │
                    └───┬──────────┬──────────┬───┘
                        │          │          │
              ┌─────────▼──┐  ┌────▼────┐  ┌──▼────────┐
              │ PostgreSQL │  │  Redis  │  │  MinIO/S3 │
              │  (RLS)     │  │ (cache) │  │ (XML/PDF) │
              └────────────┘  └─────────┘  └───────────┘
                        │
              ┌─────────▼──────────┐
              │ Workers (River)    │──────► PAC (timbrado)
              │ colas en Postgres  │──────► Correo transaccional
              └────────────────────┘
```

**Por qué colas en PostgreSQL (River) y no Redis o Kafka en el MVP:** un job de timbrado debe encolarse **en la misma transacción** que crea la factura. Con una cola en Postgres eso es trivial y no hay ventana donde la factura exista sin su job (o al revés). Con Redis hay que resolver el problema de dos fases. Kafka en esta escala es infraestructura sin propósito.

### 8.2 Producción nacional (Fase 3)

Cambios sobre el MVP:
- Réplicas de lectura de PostgreSQL para reportes; particionado de `facturas` por rango de fecha.
- Extracción de los workers a su propio deployment con escalado independiente.
- ClickHouse o TimescaleDB para analítica, alimentado por CDC — **no se hacen reportes pesados contra la base transaccional**.
- Multi-AZ, backups con recuperación a punto en el tiempo, DRP probado trimestralmente.
- Dos PAC con failover.
- WAF, CDN y rate limiting distribuido.

### 8.3 Multi-tenant

**Modelo recomendado: base compartida con Row Level Security de PostgreSQL.**

```sql
ALTER TABLE facturas ENABLE ROW LEVEL SECURITY;
CREATE POLICY tenant_isolation ON facturas
  USING (tenant_id = current_setting('app.tenant_id')::uuid);
```

La aplicación fija `app.tenant_id` al tomar la conexión. **La ventaja decisiva: si un desarrollador olvida el `WHERE tenant_id = ?`, el motor no devuelve datos de otro tenant.** Es defensa en profundidad contra la clase de bug más peligrosa en un sistema multi-tenant. Para clientes enterprise que exijan aislamiento físico se ofrece esquema o base dedicada en la Fase 3.

### 8.4 Modo degradado (no "offline-first")

**No se puede emitir un CFDI válido sin conexión**: el timbre lo pone el PAC. Lo honesto es:

| Estado | Qué hace la app | Qué ve el usuario |
|---|---|---|
| En línea | Timbra y entrega XML + PDF | "Factura lista" con descarga |
| Degradado | Guarda la solicitud, encola, entrega acuse **no fiscal** | "Recibimos tu solicitud. Te enviamos tu factura por correo en cuanto se procese." |
| Sin red | Persiste local (IndexedDB), sincroniza al reconectar | Mismo mensaje + indicador de pendiente |

**Nunca se debe llamar "factura" a un acuse no timbrado.** Esa confusión genera un problema fiscal para el usuario y un problema de confianza para el producto.

Y como se explicó en §4.6, la factura global cubre fiscalmente al negocio durante la ventana, así que el modo degradado no tiene consecuencias fiscales si el periodo se respeta.

### 8.5 Auditoría con integridad (sustituto de blockchain)

```
Cada evento de auditoría:
  { id, tenant_id, actor, accion, recurso, timestamp, payload_hash, prev_hash }

  prev_hash = SHA-256(registro anterior de ese tenant)
```

Esto crea una cadena de hashes: alterar un registro pasado rompe la cadena de forma detectable. Cierre diario con raíz de Merkle, sellada con timestamping RFC 3161 de una autoridad de tiempo reconocida. **Da la propiedad de integridad verificable que se le pide a blockchain, sin costo de red, sin latencia, sin exponer datos y sin conflicto con el derecho de supresión.**

---

## 9. Stack tecnológico: comparativas y recomendación

### 9.1 Lenguaje de backend

| | **Go** | Node/TS | Rust | Java | C# |
|---|---|---|---|---|---|
| Rendimiento | Muy alto | Medio | Máximo | Alto | Alto |
| Concurrencia | Excelente (goroutines) | Event loop | Excelente | Buena | Buena |
| Superficie de ataque | **Baja** (binario estático, stdlib amplia) | Alta (árbol npm profundo) | Baja | Media | Media |
| Velocidad de desarrollo | Alta | Muy alta | Baja | Media | Alta |
| Despliegue | **Binario único, sin runtime** | Requiere runtime | Binario único | JVM | Runtime |
| Talento en México | Bueno y creciendo | Muy abundante | Escaso y caro | Abundante | Abundante |
| Ecosistema CFDI | Escaso pero suficiente | Amplio | Casi nulo | Amplio | **El más amplio** |

**Recomendación: Go, confirmado — pero por las razones correctas.** No por benchmarks: por que un binario estático sin runtime reduce drásticamente la superficie de ataque y la complejidad operativa, porque el manejo explícito de errores encaja con un dominio donde tragarse un error significa una factura mal emitida, y porque la estabilidad del lenguaje evita reescrituras en un sistema que debe vivir una década.

**El contrapeso honesto:** el ecosistema de facturación mexicana está mucho mejor cubierto en C# y PHP. Go implicará escribir más código de dominio propio. Con la Opción C de §7 (API de facturación) eso deja de importar en la Fase 1; en la Fase 2 será trabajo real.

### 9.2 Framework HTTP en Go

| | net/http (1.22+) | **Chi** | Gin | Echo | Fiber |
|---|---|---|---|---|---|
| Compatible `http.Handler` | Sí | **Sí** | No (Context propio) | No | **No (fasthttp)** |
| Middleware del ecosistema | Todo | **Todo** | Solo el propio | Solo el propio | Solo el propio |
| Dependencias | 0 | **~0** | Varias | Varias | Varias |
| Riesgo de lock-in | Ninguno | **Mínimo** | Alto | Alto | Muy alto |
| HTTP/2, HTTP/3 | Nativo | Nativo | Nativo | Nativo | **Limitado** |

**Recomendación: Chi.** Es `net/http` con un router mejor y middlewares idiomáticos. Cualquier middleware estándar de Go (OpenTelemetry, CORS, compresión) funciona sin adaptadores. Gin y Echo introducen un `Context` propietario que acopla todo el código de handlers al framework. **Fiber queda descartado explícitamente**: usa `fasthttp`, no es compatible con `net/http`, y en un sistema fiscal la compatibilidad, la auditabilidad y el soporte completo de HTTP importan más que los microbenchmarks.

### 9.3 Acceso a datos

| | pgx (directo) | **sqlc** | GORM | Ent | Bun |
|---|---|---|---|---|---|
| SQL visible y auditable | Sí | **Sí** | **No** | Parcial | Sí |
| Type-safety | Manual | **Generada** | Runtime | Compilación | Parcial |
| Curva | Media | Baja | Baja al inicio | Alta | Baja |
| Compatible con RLS | Sí | **Sí** | Frágil | Sí | Sí |

**Recomendación: sqlc sobre pgx.** Se escribe SQL puro, `sqlc` genera código Go tipado. En un sistema fiscal, **poder leer exactamente la consulta que se ejecuta no es un lujo, es un requisito de auditoría**. GORM queda descartado: oculta las consultas, complica el uso de RLS y su comportamiento con `soft delete` es peligroso en un dominio donde la retención es una obligación legal.

### 9.4 Stack completo recomendado

**Backend**
| Componente | Elección | Por qué |
|---|---|---|
| Lenguaje | Go 1.23+ | §9.1 |
| Router | Chi | §9.2 |
| Datos | pgx + sqlc | §9.3 |
| Migraciones | goose | SQL puro, embebible en el binario |
| Colas | River (Postgres) | Encolado transaccional |
| Cache / rate limit | Redis | Contadores distribuidos, sesiones |
| Logs | `log/slog` (stdlib) | Estructurado, sin dependencia |
| Config | envconfig o koanf | Variables de entorno, sin archivos en producción |
| Validación | go-playground/validator | Estándar de facto |
| Observabilidad | OpenTelemetry + Prometheus | Estándar abierto, sin lock-in |
| Errores | Sentry | Agregación y alertas |
| Tests | stdlib + testify + testcontainers | Postgres real en tests de integración |

**Frontend**
| Componente | Elección | Alternativa |
|---|---|---|
| Framework | Next.js (App Router) + TypeScript | SvelteKit si el equipo es pequeño |
| Estilos | Tailwind CSS | — |
| Componentes | shadcn/ui (Radix) | Accesibilidad seria de fábrica |
| PWA | Service worker + manifest | Requisito para el modo degradado |
| QR | `BarcodeDetector` nativo con fallback a `html5-qrcode` / `@zxing/browser` | La API nativa es mucho más rápida donde existe |
| Formularios | React Hook Form + Zod | Validación compartida con el backend |

**Sobre server-side rendering vs. SPA:** para el panel del negocio (autenticado, interactivo) va SPA o React Server Components. Para la **pantalla pública de autofacturación** — la que abre un conductor con mala señal en su celular — conviene el camino más ligero posible: HTML server-rendered con JavaScript mínimo, cargando el escáner de QR solo bajo demanda. Es la pantalla con más impacto en la conversión y la que menos JavaScript debe cargar.

**Generación de documentos**
| Formato | Librería | Nota |
|---|---|---|
| Excel | `excelize` | Maduro y completo |
| CSV | `encoding/csv` | stdlib |
| ZIP | `archive/zip` | stdlib |
| QR | `skip2/go-qrcode` | Simple y estable |
| PDF | **HTML → PDF vía Gotenberg o chromedp** | La representación impresa del CFDI tiene un layout complejo; hacerlo con primitivas de PDF es doloroso. `gofpdf` está archivado; `maroto` sirve para reportes tabulares simples |

**Infraestructura local:** Docker Compose, Makefile, Caddy, MinIO, PostgreSQL, Redis, mkcert.

---

## 10. Seguridad, secretos y ciberseguridad

### 10.1 Decisión fundacional

**En la Fase 1 la plataforma no custodia llaves privadas de CSD ni e.firma.** Se delega al PAC. Esto elimina de un plumazo la clase de riesgo más grave del sistema. Cuando en la Fase 2 haya que custodiar CSD:

| Control | Requisito |
|---|---|
| Almacenamiento | KMS gestionado o HSM. **Nunca** en la base de datos, en el filesystem, en variables de entorno ni en el repositorio |
| Cifrado | Envelope encryption: llave de datos por tenant, envuelta por la llave maestra del KMS |
| Uso | La llave privada **nunca sale del KMS/HSM**; se envía el dato a firmar, se recibe la firma |
| Autorización | MFA obligatorio para cargar o rotar un CSD |
| Auditoría | Cada operación de firma se registra en el log append-only con actor, motivo y UUID resultante |
| Alertas | Volumen anómalo de firmas, firmas fuera de horario, desde IP nueva |
| Rotación | Recordatorio automático 60 días antes del vencimiento |

### 10.2 Cifrado

| Capa | Implementación |
|---|---|
| En tránsito | **TLS 1.3 obligatorio.** HSTS con preload. Sin TLS < 1.2. Certificados automáticos con Caddy/ACME |
| En reposo (BD) | Cifrado a nivel volumen + cifrado a nivel columna para RFC, correo, teléfono y montos |
| En reposo (archivos) | SSE-KMS en el bucket; URLs firmadas con expiración de 5 minutos |
| Backups | Cifrados con llave distinta de la de producción. **Restauración probada trimestralmente — un backup no probado no es un backup** |
| Contraseñas | **Argon2id** (memoria 64 MB, 3 iteraciones, paralelismo 4). bcrypt solo si hay restricción de plataforma |
| Webhooks | HMAC-SHA256 con timestamp y ventana de 5 minutos contra replay |
| Integridad de documentos | SHA-256 del XML persistido y verificado en cada descarga |

### 10.3 Gestión de secretos

```
Desarrollo local  → .env (en .gitignore) + .env.example con valores ficticios
Repositorio       → SOPS + age para configuración cifrada versionada
CI/CD             → Secretos del proveedor, nunca en logs
Producción        → KMS del proveedor cloud o HashiCorp Vault
```

**Prohibiciones absolutas:**
- Nunca subir `.env`, `.key`, `.cer`, `.pfx`, `.p12` al repositorio.
- Nunca usar CSD ni e.firma reales en desarrollo. Solo sellos de prueba del sandbox del PAC.
- Nunca registrar en logs: RFC completo, montos, correos completos, tokens, XML íntegro.
- Nunca guardar el PAN de una tarjeta. Tokenizar en la pasarela.

### 10.4 OWASP Top 10 y amenazas específicas del dominio

| Amenaza | Mitigación |
|---|---|
| Inyección SQL | Consultas parametrizadas vía sqlc. Sin concatenación de strings. Nunca |
| XSS | React escapa por defecto; CSP estricta; prohibido `dangerouslySetInnerHTML` |
| CSRF | Cookies `SameSite=Strict` + token anti-CSRF en operaciones que mutan |
| SSRF | Lista blanca de destinos salientes; bloqueo de rangos privados y metadata del cloud |
| **IDOR** | **RLS de PostgreSQL** + verificación de autorización por recurso. Es la amenaza número uno en multi-tenant |
| **XXE** | `encoding/xml` de Go no resuelve entidades externas por defecto. Aun así: nunca procesar XML de origen no confiable con resolución de DTD, y limitar el tamaño de entrada |
| **Suplantación de RFC** | Un usuario no puede facturar a nombre de un RFC que no controla. En autofacturación el *receptor* lo elige libremente (correcto), pero el *emisor* siempre es el negocio autenticado |
| **Doble timbrado** | Idempotency keys, restricción UNIQUE, máquina de estados explícita (§7.3) |
| **QR malicioso** | El contenido del QR es entrada no confiable: validar formato estricto, longitud máxima, sin redirección automática, nunca ejecutar ni navegar a lo que contenga |
| **Manipulación de montos** | El monto **nunca** viene del cliente en autofacturación: se lee del ticket registrado del lado del servidor |
| **Manipulación de callbacks de pago** | Verificar firma HMAC del webhook y reconsultar el estado contra la API de la pasarela antes de acreditar |
| **Fuga de XML** | URLs firmadas de vigencia corta, cifrado en reposo, sin listado público de buckets, registro de cada descarga |
| **Enumeración de folios** | UUID v4 o v7 en URLs públicas; nunca IDs secuenciales; rate limiting por IP y por sesión |
| Fuerza bruta | Rate limiting, bloqueo progresivo, CAPTCHA tras N fallos, alertas |
| Fugas en logs | Middleware de redacción obligatorio; pruebas automáticas que fallan si un patrón de RFC aparece en logs |

### 10.5 Ciclo de vida seguro

| Práctica | Herramienta |
|---|---|
| SAST | `gosec`, CodeQL |
| Análisis de dependencias | `govulncheck`, Dependabot, `npm audit` |
| Detección de secretos | `gitleaks` en pre-commit y en CI |
| SBOM | `syft` en cada build |
| Escaneo de imágenes | `trivy` |
| DAST | OWASP ZAP contra staging |
| Pentest | Externo antes de producción y anualmente |
| Separación de entornos | dev / QA / **sandbox fiscal** / producción, sin datos reales fuera de producción |

---

## 11. Blockchain y tokenización: evaluación crítica

### 11.1 La pregunta central

**¿El SAT reconoce blockchain como sustituto del CFDI?** **No.** El comprobante fiscal en México es el XML timbrado por un PAC autorizado, con el Timbre Fiscal Digital y el sello del SAT. Ningún registro en cadena tiene efectos fiscales. No se identificó ningún marco normativo mexicano que reconozca blockchain como medio de comprobación fiscal. *(Ausencia de evidencia, no prueba de ausencia: verificar antes de comunicarlo como definitivo. Ver V-blockchain en §21.)*

### 11.2 Qué problemas resolvería blockchain aquí — y por qué ya están resueltos

| Problema que se le atribuye | ¿Existe? | Cómo ya está resuelto |
|---|---|---|
| Probar que la factura no fue alterada | Sí | **Ya lo resuelve el sello digital del SAT.** El timbre es una firma de la autoridad fiscal sobre el contenido |
| Probar la fecha de emisión | Sí | **Ya lo resuelve el `FechaTimbrado`** del Timbre Fiscal Digital |
| Historial inmutable de emisiones | Sí | Log append-only con hash encadenado + Merkle + timestamping RFC 3161 (§8.5). Costo cercano a cero |
| Auditoría externa verificable | Sí | Publicar la raíz de Merkle diaria; un auditor verifica la inclusión sin ver los datos |
| Trazabilidad de la emisión | Sí | Log de auditoría interno |
| Tokenizar cuentas por cobrar | Parcial | **Riesgo regulatorio serio.** Ver §11.4 |

**No hay ningún problema en la lista que blockchain resuelva mejor que la alternativa.** Y el sello del SAT es, en un litigio fiscal mexicano, más fuerte que cualquier prueba criptográfica de origen privado, porque proviene de la propia autoridad.

### 11.3 Riesgos de usarlo

| Riesgo | Detalle |
|---|---|
| **Datos personales en cadena** | Conflicto estructural sin solución con el derecho de cancelación (§6.6) |
| **Costo y latencia** | Costo por transacción variable e impredecible; confirmación en segundos o minutos vs. milisegundos |
| **Complejidad regulatoria** | Ley Fintech art. 30: operar con activos virtuales requiere autorización de Banxico. A mayo de 2026, **Banxico no ha otorgado autorizaciones públicas** bajo ese mecanismo |
| **Caracterización como valor** | Tokenizar cuentas por cobrar podría caer bajo la Ley del Mercado de Valores. La CNBV y Banxico han sido cautelosos y hay ambigüedad sobre el tratamiento de security tokens |
| **Riesgo AML** | Manejar activos virtuales activa obligaciones de la Ley Antilavado y reporte a la UIF |
| **Falsa percepción legal** | El riesgo más subestimado: que un usuario crea que su "factura en blockchain" tiene validez fiscal. **Eso sería un daño real al usuario y un pasivo para la empresa** |
| **Adopción** | Ningún operador de peaje, contador o autoridad lo está pidiendo |

### 11.4 Veredicto

**No implementar blockchain ni tokenización. Ni en el MVP, ni en la Fase 2, ni en la Fase 3.**

Lo que sí se implementa desde la Fase 1, con el mismo beneficio de auditoría:

```
✓ Log de auditoría append-only con hash encadenado
✓ Cierre diario con raíz de Merkle
✓ Sellado de tiempo RFC 3161 con autoridad reconocida
✓ Publicación de la raíz diaria en un canal público (blog, repo)
   → cualquiera puede verificar inclusión sin acceder a los datos
✓ Hash SHA-256 de cada XML persistido y verificado en cada descarga
```

Esto entrega integridad verificable, fecha cierta y auditabilidad externa, a costo casi nulo, sin conflicto de privacidad, sin exposición regulatoria y sin latencia.

**Si en el futuro se decide anclar hashes en una cadena pública** (Fase 4, opcional, solo si un cliente lo exige por escrito): anclar **únicamente la raíz de Merkle diaria** — un hash por día, cero datos personales, cero identificadores fiscales — con dictamen legal previo. Nunca hashes de facturas individuales, porque el conjunto de entrada es predecible y correlacionable.

---

## 12. Historial de facturas y analítica

### 12.1 Dimensiones y filtros

**Dimensiones:** usuario, empresa (tenant), caseta o sucursal, régimen fiscal del receptor, método de pago, forma de pago, producto o servicio, receptor, canal (QR / folio / manual / API), estatus.

**Estatus (máquina de estados explícita):**
```
BORRADOR → PENDIENTE → TIMBRANDO → TIMBRADA → [CANCELACION_SOLICITADA → CANCELADA]
                            ↓
                    ERROR / INCIERTA → (reconciliación) → TIMBRADA o ERROR
```

**Filtros temporales:** hoy, ayer, esta semana, este mes, este año, rango personalizado, comparativa contra el periodo anterior.

### 12.2 Métricas

**Para el dueño del negocio (pantalla principal, 5 números):** total facturado del periodo · número de facturas · IVA trasladado · total cancelado · comparativa contra el periodo anterior.

**Para el contador:** desglose por régimen y uso de CFDI · IVA trasladado y retenido · ISR retenido · IEPS · facturas con complemento de pago pendiente · conciliación de la global contra las nominativas · listado de canceladas con motivo.

**Para el administrador de la plataforma:** facturas por minuto · latencia p50/p95/p99 de timbrado · tasa de error por PAC · tasa de cancelación por tenant (señal de anomalía) · disponibilidad del PAC · disponibilidad propia · consumo de timbres contra el plan.

**De negocio:** facturas totales · ingreso por suscripciones · costo por factura · margen de contribución · CAC · LTV · churn · retención por cohorte · volumen por caseta · volumen por región.

### 12.3 Detección de anomalías

Cada una debe generar una alerta, porque cada una puede ser fraude, error o una señal fiscal preocupante:

- Tasa de cancelación de un tenant por encima de un umbral (bandera de EFOS).
- Facturación fuera del horario operativo de la caseta.
- Muchas facturas al mismo RFC receptor en poco tiempo desde canales distintos.
- Montos que se repiten exactamente de forma sistemática.
- Uso masivo de claves genéricas de ClaveProdServ.
- Facturas emitidas y canceladas el mismo día de forma recurrente.
- Intentos de facturar el mismo ticket más de una vez.

### 12.4 Arquitectura de datos

- **MVP:** consultas agregadas contra PostgreSQL con vistas materializadas refrescadas cada 15 minutos. Suficiente hasta ~1M de facturas.
- **Escala:** particionado de `facturas` por rango mensual + réplica de lectura dedicada + ClickHouse alimentado por CDC.
- **Regla:** los reportes nunca corren contra la base transaccional en producción. Un reporte anual pesado no puede degradar el timbrado.

---

## 13. Modelo de negocio: el destino de los 10 centavos

### 13.1 El problema aritmético

| Concepto | Costo estimado por factura (MXN) |
|---|---|
| Timbre PAC (volumen medio) | $0.60 (rango verificado: $0.30–$1.00) |
| Cloud (cómputo, BD, red) | $0.08 |
| Almacenamiento 10 años (XML+PDF, ~250 KB) | $0.01 |
| Correo transaccional | $0.02 |
| **Soporte** (3% genera ticket × $25 por ticket) | **$0.75** |
| Cumplimiento, legal y contable (amortizado) | $0.10 |
| Antifraude y observabilidad | $0.04 |
| **Costo total realista, escala temprana** | **≈ $1.60** |
| **Costo optimizado** (PAC a $0.30 + soporte a 0.5%) | **≈ $0.60** |

**Cobrar $0.10 MXN por factura implica perder entre $0.50 y $1.50 en cada emisión.** Un millón de facturas al mes serían entre $500,000 y $1.5M MXN de pérdida mensual. **El volumen empeora el problema, no lo arregla.** Y el soporte —no el timbre— es el costo dominante: cada punto porcentual de tickets de soporte cuesta más que el timbre completo. Esto convierte la calidad de la UX en una palanca financiera de primer orden, no en un detalle estético.

Además, cobrar $0.10 por transacción es **operativamente imposible**: Stripe cobra 3.6% + $3 MXN, Mercado Pago ~3.99% + $4 + IVA, Conekta ~3.4% + $3 + IVA. La comisión de cobrar 10 centavos es 30–40 veces el monto cobrado.

### 13.2 Modelo recomendado (consistente con "cobrar al negocio")

**Suscripción B2B por volumen, con el consumidor final facturando gratis.**

| Plan | Precio mensual | Facturas incluidas | Costo efectivo/factura | Excedente | Perfil |
|---|---|---|---|---|---|
| Starter | $349 MXN | 200 | $1.75 | $1.80 | Comercio pequeño |
| Growth | $999 MXN | 1,000 | $1.00 | $1.20 | Restaurante, gasolinera |
| Scale | $2,999 MXN | 5,000 | $0.60 | $0.75 | Cadena, parador |
| **Caseta / Enterprise** | Convenio | 25,000+ | **$0.35–$0.50** | Negociado | **Concesionaria de peaje** |

*Precios de trabajo, a validar con entrevistas de disposición a pagar. Todos más IVA.*

**Aquí es donde los 10 centavos sí sobreviven, con honestidad:** como **objetivo interno de costo marginal**. Si en la Fase 3, con PAC negociado a $0.25, soporte casi totalmente autoservicio e infraestructura amortizada, el costo marginal baja a ~$0.10 por factura, entonces "10 centavos por factura" se vuelve la métrica de eficiencia operativa de la empresa. Es una meta de ingeniería excelente. **Es un pésimo precio de lista.**

### 13.3 Por qué la caseta de peaje es el mejor cliente de este modelo

```
Caseta: 20,000 cruces/día · 5% pide factura = 1,000 CFDI/día = 30,000/mes
Ingreso a $0.45/CFDI                            = $13,500 MXN/mes
Costo marginal a $0.60/CFDI (escala temprana)   = $18,000 MXN/mes  ← NEGATIVO
Costo marginal a $0.35/CFDI (PAC negociado)     = $10,500 MXN/mes
Margen de contribución                          =  $3,000 MXN/mes por caseta
```

**Esto revela algo incómodo y necesario: incluso el mejor cliente tiene margen delgado si el precio unitario es bajo.** El modelo solo funciona si se cumple al menos una de estas condiciones:

1. **Tarifa PAC agresiva desde el inicio** — negociable a volumen, pero requiere comprometer volumen antes de tenerlo.
2. **Precio de convenio con mínimo mensual garantizado**, no puro pago por uso. Ejemplo: $25,000 MXN/mes fijos por caseta con 30,000 CFDI incluidos.
3. **Vender valor, no timbres:** panel de conciliación, reportes fiscales para la concesionaria, reducción de su carga de atención al usuario, SLA. Eso se cobra en miles de pesos al mes, no en centavos por factura.

**Recomendación de precio para casetas: cuota mensual fija por punto de cobro con volumen incluido**, no precio por factura. Convierte un margen delgado y variable en un ingreso predecible, y alinea el precio con el valor entregado (disponibilidad y cumplimiento) en lugar de con el costo (timbres).

### 13.4 Punto de equilibrio

```
Costos fijos mínimos para operar con seriedad (mensual):
  1 ingeniero senior                 $80,000
  0.5 soporte                        $15,000
  Infraestructura                    $12,000
  Legal, contable, cumplimiento      $15,000
  Herramientas y servicios            $8,000
                                    ────────
                                    $130,000 MXN/mes

Con margen de contribución de $0.50/factura → 260,000 facturas/mes
Con 9 casetas a convenio de $25,000/mes     → $225,000 MXN  ✓
Con 130 clientes Growth a $999               → $129,870      ✗ (justo debajo)
```

**Conclusión: nueve casetas grandes equivalen a más de ciento treinta comercios medianos.** El argumento del volumen concentrado es correcto — pero exige cerrar ventas B2B con concesionarias, que es el cuello de botella real del proyecto.

### 13.5 Obligaciones fiscales de la propia plataforma

- Los ingresos por comisión y suscripción son **servicios gravados a la tasa del 16% de IVA**.
- La plataforma debe emitir su propio CFDI por cada cobro. **Con cobro mensual agregado esto es un CFDI por cliente por mes** — trivial. Con microcobros de $0.10 sería un absurdo operativo y una razón adicional para descartarlo.
- Régimen probable: persona moral, régimen 601. **Confirmar con contador.**
- Analizar si la plataforma cae en el supuesto de "plataformas digitales" con obligación de dar al SAT acceso en línea y en tiempo real, vigente desde el 1 de abril de 2026 (V6). **Esta es una pregunta de arquitectura, no solo legal.**
- **Nunca custodiar fondos de terceros.** Si el consumidor final pagara y la plataforma retuviera dinero antes de entregarlo al negocio, se abriría un debate sobre servicios financieros regulados y obligaciones antilavado. El modelo de suscripción B2B evita esto por completo — otra razón para preferirlo.

### 13.6 Manejo de casos límite en cobro

| Escenario | Manejo |
|---|---|
| Cobro exitoso, timbrado fallido | El timbre no se consume. Se acredita al saldo del cliente y se le notifica. **Nunca se cobra por un timbre no entregado** |
| Timbrado exitoso, cobro fallido | El CFDI ya existe: no se puede deshacer. Se registra la deuda, se reintenta el cobro y se suspende el servicio tras N días con aviso previo |
| Reembolso | Emitir CFDI de egreso (nota de crédito) contra la factura original |
| Disputa de tarjeta | Evidencia: logs de emisión, UUIDs, acuses. La trazabilidad es la defensa |
| Abuso (facturación masiva automatizada) | Rate limiting por tenant, cuotas duras, alerta de anomalía, cláusula contractual de uso aceptable |

---

## 14. Implementación en casetas y sistemas embebidos

### 14.1 Flujo de usuario recomendado

**El conductor escanea, no el operador.** Detener la fila de una caseta para que un operador capture datos fiscales es inaceptable operativamente y sería la causa número uno de rechazo de la concesionaria.

```
1. El ticket de peaje ya trae un QR impreso (los operadores ya lo hacen)
2. El conductor escanea el QR con su propio celular, en el momento
   que quiera dentro de la ventana (típicamente 30 días naturales)
3. Se abre la pantalla de autofacturación:
   ┌─────────────────────────────────┐
   │  Caseta X · $XXX.XX · dd/mm     │  ← datos ya cargados del ticket
   │                                 │
   │  RFC:    [_______________]      │  ← único campo obligatorio real
   │  Correo: [_______________]      │
   │                                 │
   │      [ Generar mi factura ]     │
   └─────────────────────────────────┘
4. Autocompletado: si el RFC ya facturó antes, se rellena todo
5. Vista previa → confirmar → timbrado
6. Pantalla de éxito con descarga de XML y PDF + envío por correo
```

**Menos de tres toques para quien ya facturó antes.** Esto solo se logra guardando el perfil fiscal contra el RFC, con consentimiento explícito.

### 14.2 Manejo de casos límite

| Situación | Respuesta |
|---|---|
| Sin internet en el celular | El conductor factura después: la ventana es de días, no de segundos |
| QR dañado o ilegible | Captura manual del folio impreso. **Este fallback es obligatorio, no opcional** |
| Folio inexistente | Mensaje claro: "No encontramos este ticket. Verifica el folio o contáctanos" + botón de soporte con el folio precargado |
| Folio ya facturado | "Este ticket ya tiene factura" + opción de reenviarla al correo. **Nunca permitir facturar dos veces** |
| Ventana vencida | Explicar el plazo del operador con claridad y sin culpar al usuario |
| Usuario sin RFC | Explicar que sin RFC no hay factura nominativa, y que su consumo ya está amparado en la factura global del operador. No es un error del usuario |
| Usuario que no entiende los términos fiscales | Valores por defecto inteligentes: Uso de CFDI G03 (Gastos en general) preseleccionado, régimen inferido del RFC, forma y método de pago fijados por el operador. **El usuario nunca debería tener que elegir una clave del SAT** |

### 14.3 Hardware

**Para la Fase 1 el hardware es el celular del conductor. No se instala nada en la caseta.** Esto es una ventaja competitiva enorme: cero CAPEX, cero instalación, cero mantenimiento, cero negociación de espacio físico. La caseta solo necesita imprimir un QR en su ticket, algo que su sistema de punto de venta ya hace.

Si más adelante se requiere hardware:

| Opción | MVP | Producción | Nota |
|---|---|---|---|
| Celular del usuario | **✓✓✓** | ✓✓✓ | Cero costo, cero instalación |
| Tableta Android en kiosco | ✗ | ✓ | Solo si el operador lo exige. Requiere kiosk mode y MDM |
| Mini PC Linux | ✗ | ✓ | Si se integra con el sistema de cobro del operador |
| Raspberry Pi | ✗ | ~ | Ecosistema débil de soporte de campo; difícil de mantener a escala |
| POS Android | ✗ | ✓ | Si hay cobro presencial |
| Impresora térmica | ✗ | ~ | Solo si el operador no imprime QR (entonces hay que darle la capacidad) |

Si hay dispositivo propio: kiosk mode, MDM, actualizaciones firmadas, arranque verificado, TPM o Secure Element donde exista, sin credenciales de plataforma en el dispositivo, telemetría de salud, reinicio automático ante fallo, y cola local cifrada para operaciones pendientes.

### 14.4 Roles

| Rol | Permisos |
|---|---|
| Conductor / consumidor | Facturar su propio ticket. Sin cuenta obligatoria |
| Operador de caseta | Consultar estatus, reimprimir QR, escalar incidencias. **Sin acceso a datos fiscales del consumidor** |
| Supervisor | Métricas del punto, resolución de incidencias |
| Administrador del negocio | Configuración, usuarios, facturación global, reportes |
| Contador | Reportes y descargas masivas, sin permiso de emisión |
| Soporte de plataforma | Acceso mínimo, temporal, con justificación registrada y auditado |

### 14.5 Requerimientos de red y contingencia

- Latencia aceptable de timbrado: p95 < 3 s, p99 < 8 s.
- Timeout al PAC: 15 s, luego a cola.
- Sin red del lado del usuario: PWA con cola local en IndexedDB.
- **Contingencia fiscal:** la factura global del operador ampara el periodo. El modo degradado no tiene consecuencias fiscales si se timbra dentro de la ventana (§4.6).

---

## 15. Reportes fiscales descargables

### 15.1 Advertencia fiscal que debe estar en el producto

> **El único documento con validez fiscal en México es el XML timbrado, junto con el acuse de cancelación cuando aplique.** El PDF es una representación impresa. Excel y CSV son reportes informativos sin valor fiscal por sí mismos. Todo reporte generado por la plataforma debe llevar impresa la leyenda: *"Documento informativo. No es un comprobante fiscal. El comprobante fiscal es el XML timbrado."*

Qué acepta la autoridad en un requerimiento específico debe verificarse caso por caso; no se debe prometer que un reporte propio satisface una obligación.

### 15.2 Catálogo de reportes

| Reporte | Formatos | Filtros | ¿Validez fiscal? |
|---|---|---|---|
| Facturas emitidas | PDF, XLSX, CSV, ZIP(XML+PDF) | Periodo, estatus, receptor, caseta | **No** (el ZIP sí contiene los XML válidos) |
| Facturas canceladas | PDF, XLSX, ZIP(acuses) | Periodo, motivo | No (los acuses sí) |
| Impuestos (IVA/ISR/IEPS) | PDF, XLSX | Periodo, régimen, impuesto | No |
| Retenciones | PDF, XLSX | Periodo, tipo | No |
| Conciliación global vs. nominativas | PDF, XLSX | Periodo | No |
| Comisiones cobradas | PDF, XLSX | Periodo | No |
| Errores de timbrado | XLSX, CSV | Periodo, tipo de error | No |
| Auditoría de acciones | CSV firmado | Periodo, actor, recurso | No |
| Por caseta / régimen / forma de pago / receptor / producto | XLSX, CSV | Múltiples | No |
| Diario / semanal / mensual / anual / rango | Todos | — | No |

### 15.3 Contenido y anti-manipulación

Cada reporte incluye: UUID, RFC emisor y receptor, fecha, subtotal, impuestos, total, estatus, motivo de cancelación cuando aplique, serie y folio.

Cada archivo generado lleva un pie con:
```
Generado: 2026-08-25T14:30:00-06:00
Solicitado por: usuario@ejemplo.mx
Periodo: 2026-08-01 a 2026-08-31 · Registros: 1,847
SHA-256: a3f5...9c2b
Verificar en: https://app.ejemplo.mx/v/{token}     [QR]
Documento informativo. No es un comprobante fiscal.
```

El hash se persiste en la base; el QR apunta a una página de verificación que confirma que ese hash corresponde a un reporte realmente emitido, con esos parámetros, en esa fecha. Un reporte alterado deja de verificar.

### 15.4 Generación asíncrona

```
POST /api/v1/reportes  →  202 Accepted + { job_id }
GET  /api/v1/reportes/{job_id}  →  { estado, progreso, url_descarga? }
```

Reportes de más de 1,000 filas van siempre a cola. Escritura en streaming a object storage (nunca el archivo completo en memoria). URL firmada con vigencia de 15 minutos. Notificación por correo al terminar. Un reporte anual de una caseta puede tener 400,000 filas: **jamás debe generarse dentro de un request HTTP**.

---

## 16. Producto y UX

### 16.1 Principios

1. **El usuario no debe aprender fiscalidad para facturar.** Cada clave del SAT expuesta al usuario es un fallo de diseño.
2. **Menos de tres toques para quien ya facturó antes.**
3. **Nunca decir "error" cuando el sistema puede resolverlo.** Un timeout del PAC no es un error del usuario.
4. **Confirmación explícita antes de timbrar.** Un CFDI mal emitido es difícil y caro de deshacer.
5. **Lenguaje de persona, no de contador.**

### 16.2 Traducción de mensajes de error

| Lo que dice el PAC | Lo que debe leer el usuario |
|---|---|
| `CFDI40147: El campo Nombre no coincide con el registrado en el RFC` | "El nombre no coincide con el registrado en el SAT para ese RFC. Revisa que esté escrito exactamente igual que en tu Constancia de Situación Fiscal." |
| `CFDI40158: UsoCFDI no válido para el régimen` | "Ese uso de factura no aplica para tu régimen fiscal. Te sugerimos: Gastos en general." (con la opción ya preseleccionada) |
| `Timeout` | "Estamos procesando tu factura. Te la enviamos por correo en unos minutos." |
| `CSD restringido` | "El negocio tiene un problema con su sello digital ante el SAT. Ya le avisamos. Tu ticket sigue vigente hasta el dd/mm." |

### 16.3 Pantallas principales

**Autofacturación (pública, la más importante del producto):**
```
┌──────────────────────────────────────┐
│  Tu factura de caseta                │
│  ─────────────────────────────────   │
│  📍 Caseta Palmillas                 │
│  💰 $ 168.00      📅 24/ago/2026    │
│  ─────────────────────────────────   │
│  RFC                                 │
│  [ XAXX010101000            ] ✓      │  ← validación en vivo
│  ┌────────────────────────────────┐  │
│  │ ✓ JUAN PÉREZ LÓPEZ             │  │  ← si ya facturó antes
│  │   RESICO · CP 76000            │  │
│  └────────────────────────────────┘  │
│                                      │
│  Correo                              │
│  [ juan@ejemplo.mx          ]        │
│                                      │
│  ▸ Opciones avanzadas                │  ← colapsado por defecto
│                                      │
│  □ Acepto el aviso de privacidad     │
│                                      │
│  [    Generar mi factura    ]        │  ← botón grande, alto contraste
└──────────────────────────────────────┘
```

**Éxito:**
```
┌──────────────────────────────────────┐
│              ✓                       │
│      Tu factura está lista           │
│  ─────────────────────────────────   │
│  Folio fiscal                        │
│  A1B2C3D4-...-9F0A     [copiar]      │
│                                      │
│  [ Descargar PDF ] [ Descargar XML ] │
│                                      │
│  Te la enviamos a juan@ejemplo.mx    │
│                                      │
│  ⓘ El XML es tu comprobante fiscal.  │
│    Guárdalo: lo necesitas para       │
│    deducir.                          │
└──────────────────────────────────────┘
```

**Panel del negocio:** cinco números arriba (total facturado, número de facturas, IVA, canceladas, comparativa), gráfica de tendencia, tabla filtrable, botón de descarga de reportes visible sin buscarlo.

### 16.4 Accesibilidad y compatibilidad

- WCAG 2.1 AA: contraste ≥ 4.5:1, navegación por teclado, foco visible, `aria-label` en todo control.
- Área táctil mínima de 44×44 px (se usa con una mano, a veces en movimiento).
- Modo oscuro con `prefers-color-scheme`.
- Diseño para pantallas de 320 px en adelante.
- Funcional en conexiones 3G lentas: **presupuesto de rendimiento de 100 KB de JavaScript en la pantalla pública**.
- El escáner de QR se carga bajo demanda, no en el bundle inicial.
- Sin cámara o sin permiso: la captura manual del folio siempre visible como alternativa igual de válida.

---

## 17. Escalabilidad a todo México

| Área | Qué se necesita |
|---|---|
| Multi-tenant | RLS desde el día uno (§8.3). Reacondicionar aislamiento después es un proyecto de meses |
| Onboarding | Autoservicio con validación de RFC, verificación de correo, aceptación de contrato y conexión con el PAC |
| Verificación de identidad | Validar que quien registra un RFC lo controla. Sin esto se habilita facturación no autorizada |
| API pública | REST versionada (`/api/v1`), documentada con OpenAPI, con claves por tenant, rate limiting y webhooks firmados |
| Portal de contadores | Un contador atiende decenas de clientes: acceso multi-tenant delegado y descargas masivas |
| **Versionado de reglas fiscales** | **Crítico.** Ver abajo |
| Entornos | dev / QA / **sandbox fiscal** / producción |
| Pruebas de carga | k6 o Gatling contra staging antes de cada temporada alta |
| DRP | RPO ≤ 5 min, RTO ≤ 1 h. Probado trimestralmente. Un DRP no probado no existe |

### Versionado de reglas fiscales — el requisito arquitectónico más subestimado

El SAT cambia catálogos, reglas y versiones periódicamente: el Anexo 20 se actualiza al menos una vez al año, típicamente el 1 de enero, y en 2026 incorporó 847 nuevas claves de productos y servicios más tres códigos de régimen para el sector primario.

Una factura de 2026 debe seguir siendo verificable y reimprimible en 2036 con las reglas de 2026, no con las de 2036. Por eso:

```
1. Los catálogos SAT se versionan con vigencia (desde / hasta), nunca se
   sobrescriben
2. Cada factura persiste la versión de catálogo con la que se emitió
3. Las reglas de validación viven en un motor de reglas versionado,
   no en condicionales dispersos por el código
4. Job diario que compara catálogos publicados contra los locales
   y alerta ante diferencias
5. Suite de regresión con facturas históricas: cada cambio de reglas
   debe seguir validando lo que ya se emitió
```

---

## 18. Expansión internacional

### 18.1 Diferencias de modelo

| Modelo | Países | Cómo funciona |
|---|---|---|
| **Clearance** (autorización previa) | México, Brasil, Chile, Colombia, Perú, Argentina, Turquía | La autoridad autoriza *antes* de que la factura sea válida. México y Brasil usan intermediario autorizado |
| **Post-audit** | Mayoría de la UE (histórico) | Se emite libremente; la autoridad audita después |
| **Peppol / CTC** | UE en transición, Australia, Singapur | Red descentralizada de intercambio con formato común |

**Implicación:** el modelo de negocio de "escanear QR y facturar" es esencialmente latinoamericano. En Europa el problema es la interoperabilidad B2B, no la autofacturación de tickets de consumidor. **No es el mismo producto.**

### 18.2 Estándares

| Estándar | Qué es | Relevancia |
|---|---|---|
| **UBL 2.1** | Formato XML universal | Base de Peppol |
| **EN 16931** | Norma europea de factura electrónica | Obligatoria en contratación pública de la UE |
| **Peppol BIS Billing 3.0** | Perfil de UBL para la red Peppol | Camino de entrada a Europa |
| **Factur-X / ZUGFeRD** | PDF/A-3 con XML incrustado | Franco-alemán, híbrido |
| **CFDI 4.0** | Estándar mexicano | Sin equivalencia directa con ninguno de los anteriores |

### 18.3 Arquitectura multi-país

```
┌──────────────────────────────────────────┐
│  Núcleo (reutilizable ~60%)              │
│  auth · tenants · archivos · reportes    │
│  auditoría · analítica · UI · pagos      │
└──────────────┬───────────────────────────┘
               │
    ┌──────────┼──────────┬──────────┐
    ▼          ▼          ▼          ▼
┌───────┐ ┌────────┐ ┌────────┐ ┌────────┐
│  MX   │ │   CO   │ │   CL   │ │  UE    │
│ CFDI  │ │  DIAN  │ │  SII   │ │ Peppol │
│ + PAC │ │        │ │        │ │  + UBL │
└───────┘ └────────┘ └────────┘ └────────┘
   Adaptadores por autoridad: modelo canónico interno
   → transformación a formato local → firma local → envío
```

| Reutilizable | Se reconstruye por país |
|---|---|
| Autenticación, tenants, roles | Formato del documento fiscal |
| Almacenamiento y ciclo de vida de archivos | Motor de reglas y validaciones |
| Motor de reportes | Catálogos locales |
| Auditoría y analítica | Firma electrónica local |
| Componentes de UI | Reglas de cancelación |
| Cobros y suscripciones | Adaptador de la autoridad |
| **~60%** | **~40%** |

### 18.4 Privacidad por región

| Región | Norma | Diferencia clave con México |
|---|---|---|
| México | LFPDPPP 2025 | Autoridad: Secretaría Anticorrupción y Buen Gobierno |
| UE | GDPR | Bases de licitud, DPO, evaluaciones de impacto, multas de hasta 4% de facturación global |
| Brasil | LGPD | Similar al GDPR; autoridad ANPD |
| California | CCPA/CPRA | Derecho de opt-out de venta de datos |

**Requisitos de residencia de datos:** varios países exigen que los datos fiscales residan localmente. **Esto debe estar contemplado en la arquitectura desde el inicio** (tenants ligados a región) porque migrar datos entre regiones después es doloroso.

### 18.5 Estrategia recomendada

1. **México, 24–36 meses.** No expandir antes de ser rentable y estable localmente.
2. **Después: Colombia o Chile.** Modelo clearance similar, mismo idioma, mismo perfil de usuario.
3. **Brasil solo con equipo local.** La complejidad fiscal brasileña es de otro orden de magnitud.
4. **Europa vía Peppol solo con producto B2B distinto.** No es el mismo producto ni el mismo cliente.
5. **Estados Unidos no aplica:** no hay factura electrónica fiscal federal. El problema allá es sales tax, otro negocio por completo.

---

## 19. Repositorio y control de versiones

### 19.1 Estructura (monorepo)

Un monorepo, porque con un equipo pequeño la coordinación entre repositorios cuesta más que cualquier beneficio de separación.

```
facturafacil/
├── cmd/
│   ├── api/main.go              # servidor HTTP
│   ├── worker/main.go           # procesador de colas
│   └── cli/main.go              # migraciones, semillas, tareas
├── internal/                    # no importable desde fuera
│   ├── auth/  tenant/  facturacion/  catalogos/
│   ├── reportes/  auditoria/  almacenamiento/
│   └── pac/                     # interfaz TimbradoProvider + adaptadores
├── pkg/                         # solo si algo es realmente reutilizable
├── api/openapi.yaml
├── web/                         # frontend
├── migrations/                  # SQL versionado (goose)
├── scripts/
├── docs/
│   ├── ARQUITECTURA.md  SEGURIDAD.md  PRIVACIDAD.md  FISCAL.md
├── deploy/
├── .github/workflows/
├── docker-compose.yml
├── Makefile
├── .env.example                 # solo valores ficticios
├── .gitignore
└── README.md
```

### 19.2 .gitignore mínimo obligatorio

```gitignore
.env
.env.*
!.env.example
*.key
*.pem
*.cer
*.pfx
*.p12
*.der
certs/
secrets/
/tmp/
/bin/
node_modules/
coverage.out
*.sqlite
*.db
.DS_Store
```

### 19.3 Ramas, commits y protecciones

- `main` (siempre desplegable, protegida) ← `develop` ← `feature/*`, `fix/*`, `chore/*`.
- Commits convencionales: `feat:`, `fix:`, `docs:`, `refactor:`, `test:`, `chore:`, `sec:`.
- Protección de `main`: PR obligatorio, 1 aprobación, CI en verde, sin force push, historial lineal.
- GitHub: secret scanning con push protection, Dependabot, CodeQL.

### 19.4 CI (GitHub Actions)

```yaml
# Pipeline propuesto — a implementar en la sesión de código
jobs:
  lint:      golangci-lint · eslint · prettier
  test:      go test -race -cover (con testcontainers) · vitest
  security:  gosec · govulncheck · gitleaks · trivy · npm audit
  sbom:      syft
  migrate:   aplicar migraciones sobre BD limpia + verificar rollback
  build:     compilación de binarios + build de imagen Docker
```

**Regla no negociable: el pipeline falla si `gitleaks` encuentra cualquier secreto. Sin excepciones ni bypass.**

### 19.5 Sobre publicar en GitHub

**No se hará push a ningún repositorio remoto sin autorización explícita.** Cuando se autorice, el orden seguro es: (1) inicializar git local, (2) verificar que `.gitignore` está en su sitio *antes* del primer `git add`, (3) ejecutar `gitleaks detect` sobre el árbol completo, (4) crear el repositorio **privado**, (5) push. **Un secreto en el historial de git sobrevive al borrado del archivo**; si eso ocurre, hay que rotar el secreto, no solo reescribir el historial.

---

## 20. Desarrollo local seguro

### 20.1 Regla de seguridad local

**Por defecto el servidor escucha únicamente en `127.0.0.1`.** Exponerlo a la red local requiere una decisión consciente.

Antes de cambiar el bind a `0.0.0.0`, esto es lo que hay que saber:

| Riesgo | Detalle |
|---|---|
| Alcance | Cualquier dispositivo de la red local podrá acceder. En una red compartida (oficina, café, edificio) eso incluye equipos que no controlas |
| Sin autenticación en dev | Los entornos de desarrollo suelen tener autenticación relajada |
| Bases de datos | **PostgreSQL, Redis y MinIO nunca deben exponerse.** Solo el puerto de la aplicación |
| Datos | Solo datos ficticios. **Nunca RFC reales, ni CSD reales, ni e.firma** |
| Firewall | Regla acotada al puerto de la app y a la subred local, no a `any` |
| Internet | **No exponer el entorno local a internet.** Si hace falta probar desde fuera, usar un túnel autenticado (Cloudflare Tunnel, Tailscale) con autorización explícita y por tiempo limitado |

**Cuando se llegue a este punto se pedirá confirmación explícita antes de cambiar cualquier configuración de red o firewall.**

### 20.2 Docker Compose (esbozo)

```yaml
services:
  postgres:
    image: postgres:16-alpine
    ports: ["127.0.0.1:5432:5432"]     # solo localhost
  redis:
    image: redis:7-alpine
    ports: ["127.0.0.1:6379:6379"]     # solo localhost
  minio:
    image: minio/minio
    ports: ["127.0.0.1:9000:9000", "127.0.0.1:9001:9001"]
  mailpit:                              # captura correos, no envía
    image: axllent/mailpit
    ports: ["127.0.0.1:8025:8025"]
  api:
    build: .
    ports: ["127.0.0.1:8080:8080"]     # cambiar a 0.0.0.0 SOLO tras autorización
```

### 20.3 Makefile propuesto

```makefile
make setup        # deps + .env desde .env.example + certs locales
make dev          # levanta todo en modo desarrollo con recarga
make db-up        # solo la base de datos
make db-down      # detiene la base de datos
make db-reset     # DESTRUCTIVO: recrea la BD (pide confirmación)
make migrate      # aplica migraciones
make migrate-down # revierte la última
make seed         # datos ficticios
make test         # go test -race -cover
make test-int     # tests de integración con testcontainers
make lint         # golangci-lint + eslint
make sec          # gosec + govulncheck + gitleaks
make build        # binarios
make certs        # mkcert para HTTPS local
make ip           # muestra la IP local de la máquina
make qr           # QR con la URL local (requiere LAN habilitada)
make logs         # logs de todos los servicios
make clean        # limpia artefactos de build
```

### 20.4 Probar desde otro dispositivo de la red

Procedimiento, **solo tras confirmación explícita**:

1. Explicar el riesgo y obtener confirmación.
2. Obtener la IP local (`make ip`).
3. Cambiar **solo** el bind del servicio `api` a `0.0.0.0:8080`. Las bases de datos siguen en `127.0.0.1`.
4. Regla de firewall acotada al puerto 8080 y a la subred local.
5. Generar certificado local con `mkcert` para evitar advertencias en el celular.
6. `make qr` genera un QR con `https://192.168.x.x:8443`.
7. **Al terminar la prueba, revertir el bind y la regla de firewall.**

---

## 21. Plan de investigación pendiente

| # | Tema | Pregunta concreta | Búsqueda / fuente | Qué validar | Prioridad | Estatus |
|---|---|---|---|---|---|---|
| R1 | Anexo 20 | ¿Cuál es el texto vigente y sus últimos cambios? | `omawww.sat.gob.mx` → Anexo 20 Guía de llenado | XSD, catálogos, reglas de validación | Crítica | Parcial |
| R2 | Factura global | ¿Cuál es el número y texto exacto de la regla en RMF 2026? | DOF, RMF 2026 | Periodicidad, plazo, datos del receptor | **Crítica** | **Contradictorio (V1)** |
| R3 | Cancelación | ¿Cuáles son los supuestos exactos sin aceptación y el umbral? | RMF 2026, CFF art. 29-A | Umbral de $1,000 MXN, plazos | **Crítica** | **Sin verificar (V4)** |
| R4 | Autofacturación | ¿Hay requisitos formales específicos en la RMF? | RMF, portal SAT | Reglas del modelo B2B2C | **Crítica** | **Sin verificar (V8)** |
| R5 | Nueva LFPDPPP | ¿Existe reglamento? ¿Qué pasó con los lineamientos del INAI? | DOF, Secretaría Anticorrupción y Buen Gobierno | Reglamento, criterios, avisos | **Crítica** | **Sin verificar (V5)** |
| R6 | Plataformas digitales | ¿Cae la plataforma en el supuesto de acceso en tiempo real al SAT (1 abr 2026)? | CFF reformado, RMF 2026 | Definición del supuesto, implementación técnica | **Crítica** | **Sin verificar (V6)** |
| R7 | Tarifas PAC | ¿Cuál es el precio real a 30k, 100k y 1M timbres/mes? | Cotización directa: Finkok, SW sapien, Facturama, Facturapi, Ecodex | Precio, SLA, vigencia de folios, costo de cancelación | **Crítica** | **Pendiente (V7)** |
| R8 | Conservación | ¿5 o 10 años? ¿Qué prevalece? | CFF art. 30 y 67; Código de Comercio art. 38 | Plazo defendible; **requiere opinión legal** | Alta | Parcial |
| R9 | Ser PAC | ¿Monto vigente de la fianza y requisitos completos? | Anexo 1-A RMF; trámite en portal SAT | Fianza, capital, infraestructura, tiempos | Media | **Parcial (V2)** |
| R10 | Complementos | ¿Versión vigente del REP? | Portal SAT, sección complementos | Versión y esquema | Alta | **Sin verificar (V3)** |
| R11 | Verificación de CFDI | ¿Estatus y contrato del servicio de consulta del SAT? | `consultaqr.facturaelectronica.sat.gob.mx` | Sigue operando sin WSDL publicado desde 2018 | Alta | Parcial |
| R12 | Blockchain y SAT | ¿Existe algún marco que reconozca DLT en materia fiscal? | DOF, SAT, CFF | Confirmar la ausencia; no afirmarla sin buscar | Media | Parcial |
| R13 | Activos virtuales | ¿Tokenizar cuentas por cobrar requiere licencia? | Ley Fintech art. 30, Circular 4/2019, LMV, CNBV | **Requiere opinión legal, no búsqueda** | Media | Parcial |
| R14 | Peaje | ¿Qué concesionarias hay, con qué portal y qué ventana? | Portales de operadores, SICT | Lista, capacidades, huecos reales | **Crítica (GTM)** | Parcial |
| R15 | Competencia | ¿Precios, financiamiento y clientes de los incumbentes? | Sitios de gigstack, Facturama, Facturapi, Ticket Factura, Notu | Posicionamiento y hueco defendible | Alta | Parcial |
| R16 | Peppol | ¿Qué exige ser Access Point? | peppol.org, OpenPeppol | Certificación, costos | Baja | Pendiente |

---

## 22. Matriz de riesgos

**P** = Probabilidad · **I** = Impacto

| # | Riesgo | P | I | Categoría | Mitigación | Responsable | Alerta temprana |
|---|---|---|---|---|---|---|---|
| 1 | Margen negativo por precio de $0.10 | **Alta** | **Alto** | Financiero | Suscripción B2B (§13.2); convenio con mínimo mensual | Fundador | Margen de contribución < $0.30/factura |
| 2 | Ciclo de venta a concesionarias > runway | **Alta** | **Alto** | Negocio | Carta de intención antes de codificar; segmento secundario en paralelo | Fundador | 60 días sin avanzar de etapa comercial |
| 3 | Robo de CSD de clientes | Baja | **Crítico** | Seguridad | **No custodiar en Fase 1.** Después: KMS/HSM, MFA, auditoría | CISO | Volumen o patrón anómalo de firmas |
| 4 | Facturación no autorizada (suplantación de RFC) | Media | **Alto** | Fiscal/Legal | Verificación de control del RFC; límites; anomalías | Producto | Muchos RFC nuevos desde una misma IP |
| 5 | Doble timbrado | Media | **Alto** | Técnico | Idempotency keys + máquina de estados + reconciliación (§7.3) | Backend | UUIDs duplicados por referencia interna |
| 6 | Caída del PAC | Media | **Alto** | Técnico | Cola + circuit breaker; segundo PAC en Fase 3 | SRE | Tasa de error del PAC > 5% en 5 min |
| 7 | Cambio regulatorio del SAT | **Alta** | Medio | Fiscal | Catálogos versionados; motor de reglas; job de monitoreo | Fiscal | Diferencia entre catálogo publicado y local |
| 8 | CFDI sin materialidad (reforma 2026) | Media | **Crítico** | Fiscal/Legal | Términos claros; detección de anomalías; **no facilitar facturación sin operación real** | Compliance | Tasa de cancelación anómala; claves genéricas |
| 9 | Fuga de datos personales | Baja | **Crítico** | Privacidad | Cifrado, RLS, minimización, pentest, plan de respuesta | CISO | Accesos anómalos; volumen de descargas |
| 10 | Incumplimiento de la nueva LFPDPPP | Media | **Alto** | Legal | Aviso conforme a la ley de 2025; registro de consentimientos; flujo ARCO | Legal | Solicitud ARCO sin responder en plazo |
| 11 | Competencia con mejor precio y financiamiento | **Alta** | Medio | Negocio | Nicho específico; integración profunda; no competir por precio | Fundador | Pérdida de deals por precio |
| 12 | Pérdida de XML o acuses | Baja | **Crítico** | Técnico/Fiscal | Versionado, réplica multi-región, verificación de hash, restauración probada | SRE | Fallo de verificación de integridad |
| 13 | Fuera de plazo de cancelación | Media | Medio | Fiscal | Alertas automáticas antes del vencimiento; calendario por tipo de contribuyente | Producto | Facturas próximas a vencer sin gestionar |
| 14 | Soporte no escala | **Alta** | Medio | Operativo | Autoservicio, mensajes claros, base de conocimiento | Soporte | Tickets por cada 100 facturas al alza |
| 15 | Cobro exitoso sin timbre entregado | Media | Medio | Financiero | Estados desacoplados; nunca cobrar sin entregar (§13.6) | Backend | Discrepancia en conciliación diaria |
| 16 | Blockchain mal usado con datos personales | Baja | **Alto** | Privacidad | **Decisión de no implementarlo** (§11.4) | CTO | Cualquier propuesta de anclar datos identificables |
| 17 | Caracterización como servicio financiero | Baja | **Alto** | Legal | **No custodiar fondos de terceros.** Suscripción B2B | Legal | Cualquier propuesta de retener dinero de terceros |
| 18 | Saturación por alto volumen | Media | Medio | Técnico | Pruebas de carga; autoescalado; particionado | SRE | p95 de timbrado > 5 s |
| 19 | CSD del cliente restringido por el SAT | Media | Medio | Fiscal | Detección y comunicación proactiva | Soporte | Rechazos de timbrado por CSD |
| 20 | Dependencia de una sola persona técnica | **Alta** | **Alto** | Operativo | Documentación, revisión de código, evitar el bus factor de 1 | Fundador | Todo el conocimiento en una cabeza |

---

## 23. Roadmap por fases

### Fase 0 — Validación (4–6 semanas) · **No escribir código de producción todavía**

**Objetivo:** confirmar que existe un comprador antes de construir.

| Entregable | Criterio de aceptación |
|---|---|
| Verificación fiscal de V1–V8 con contador o fiscalista | Documento firmado con las respuestas |
| Cotizaciones de 3 PAC con volumen | Precio en firme a 30k, 100k y 1M timbres/mes |
| Mapa de concesionarias de peaje | Lista con portal, capacidad y hueco real por operador |
| **Carta de intención de un operador** | **Firmada. Sin esto, no se avanza a la Fase 1** |
| Modelo financiero con cifras reales | Punto de equilibrio calculado con la tarifa PAC real |
| Aviso de privacidad conforme a la ley de 2025 | Revisado por abogado |
| Decisión de proveedor de timbrado | Contrato de sandbox firmado |

**Riesgos:** que ningún operador firme. **Ese es exactamente el resultado que se quiere descubrir en la semana 6 y no en el mes 12.**
**Equipo:** fundador + asesoría fiscal y legal por horas.

---

### Fase 1 — MVP de autofacturación (8–12 semanas)

**Objetivo:** un operador piloto emitiendo CFDI reales a sus usuarios.

**Alcance (deliberadamente estrecho):**
- ✅ CFDI de ingreso, PUE, moneda MXN, sin complementos
- ✅ Autofacturación por QR, folio manual y captura
- ✅ Validación de RFC, régimen y uso de CFDI
- ✅ Catálogos SAT versionados
- ✅ Historial y reportes básicos (día, semana, mes, año)
- ✅ Exportación CSV, XLSX y PDF
- ✅ Auditoría con hash encadenado
- ✅ Panel del negocio
- ❌ Nómina, carta porte, complementos de pago, egresos
- ❌ Cancelación desde la app (se hace en el portal del PAC)
- ❌ Multi-PAC, API pública, blockchain
- ❌ Custodia de CSD

**Criterios de aceptación:** 1,000 CFDI reales timbrados sin incidente fiscal · p95 de timbrado < 3 s · cero casos de doble timbrado · tickets de soporte < 3% de facturas · pentest básico sin hallazgos críticos.

**Riesgos:** subestimar el rechazo por nombre no coincidente con la CSF (es el error número uno de CFDI 4.0).
**Equipo:** 1 backend Go, 1 frontend, fundador en producto y ventas, asesor fiscal por horas.

---

### Fase 2 — Estabilización y emisión completa (10–14 semanas)

**Objetivo:** producto vendible a un segundo y tercer cliente sin heroísmo.

Cancelación con motivos y flujo de aceptación · Factura global · CFDI de egreso · Complemento de pago · Reconciliación automática de estados inciertos · Roles y permisos completos · MFA para administradores · Reportes avanzados · Base de conocimiento y soporte · Migración a PAC directo (Opción B) · Pruebas de carga.

**Criterios:** 3 clientes en producción · 50,000 CFDI/mes · disponibilidad ≥ 99.5% · tickets < 1.5% · DRP probado.

---

### Fase 3 — Escalamiento nacional (6–9 meses)

Multi-tenant robustecido · API pública con OpenAPI y webhooks · Portal de contadores · Integraciones contables · Segundo PAC con failover · ClickHouse para analítica · Alta disponibilidad multi-AZ · Pentest externo completo · Camino a ISO 27001 · Onboarding autoservicio.

**Criterios:** 30+ clientes · 500,000 CFDI/mes · disponibilidad ≥ 99.9% · **rentabilidad operativa**.

---

### Fase 4 — Integridad avanzada (opcional, 4–6 semanas)

**Solo si un cliente lo exige por escrito.** Publicación de raíces de Merkle diarias, verificador público de reportes, sellado de tiempo con autoridad reconocida. **Sin blockchain.** Si se anclara alguna vez, únicamente la raíz diaria y con dictamen legal previo.

---

### Fase 5 — Internacional (12+ meses después de rentabilidad)

Refactor a modelo canónico con adaptadores por país · Primer país: Colombia o Chile · Cumplimiento local de privacidad · Residencia de datos · Piloto con un cliente ancla.

**Precondición dura:** no iniciar antes de ser rentable y estable en México.

---

## 24. Equipo y roles

| Rol | Fase 0 | Fase 1 | Fase 2 | Fase 3 |
|---|---|---|---|---|
| Fundador / Producto | 1 | 1 | 1 | 1 |
| Backend Go | — | 1 | 2 | 3 |
| Frontend / PWA | — | 1 | 1 | 2 |
| Asesor fiscal (CP) | Por horas | Por horas | 0.5 | 1 |
| Abogado / privacidad | Por horas | Por horas | Por horas | 0.5 |
| DevOps / SRE | — | — | 0.5 | 1 |
| Seguridad | — | Auditoría externa | 0.5 | 1 |
| QA | — | — | 1 | 1 |
| Soporte | — | Fundador | 1 | 2 |
| Ventas / Ops | — | Fundador | 1 | 2 |
| Ingeniero de datos | — | — | — | 1 |
| Arquitecto blockchain | **No** | **No** | **No** | **No** |

**Perfil crítico de la Fase 1:** una persona backend con criterio, capaz de decir que no al alcance. El fallo más probable de este proyecto no es técnico: es intentar cubrir los 22 casos de facturación de §4 en el MVP.

---

## 25. Métricas de éxito

**Producto:** facturas emitidas por día · tasa de éxito de timbrado (objetivo > 98%) · tiempo mediano para emitir (objetivo < 45 s) · tasa de abandono en la pantalla de autofacturación · errores de captura por campo · CSAT.

**Negocio:** MRR · costo por factura · margen de contribución · facturas por cliente · usuarios activos · retención por cohorte · churn · CAC · LTV/CAC (objetivo > 3).

**Técnica:** latencia p50/p95/p99 de timbrado · disponibilidad (objetivo 99.9%) · tasa de error del PAC · MTTR · incidentes de seguridad (objetivo: 0 críticos) · cobertura de tests (objetivo > 70% en el módulo de facturación).

**Cumplimiento:** quejas fiscales de usuarios (objetivo: 0) · cancelaciones anómalas detectadas · solicitudes ARCO atendidas en plazo (objetivo: 100%) · incidentes de privacidad (objetivo: 0) · cambios normativos implementados dentro de plazo (objetivo: 100%).

---

## 26. Checklists pre-producción

### 26.1 Seguridad

- [ ] TLS 1.3 obligatorio, HSTS con preload
- [ ] Todos los secretos en KMS o Vault, ninguno en el repositorio
- [ ] `gitleaks` en CI y en pre-commit, sin bypass
- [ ] Argon2id para contraseñas
- [ ] MFA obligatorio para administradores
- [ ] RLS activo y **probado con un test que intenta cruzar tenants**
- [ ] Rate limiting por IP, por usuario y por tenant
- [ ] Idempotency keys en toda operación de emisión
- [ ] CSP estricta, `SameSite=Strict`, tokens anti-CSRF
- [ ] `gosec`, `govulncheck`, `trivy` en verde
- [ ] Pentest externo sin hallazgos críticos ni altos
- [ ] Middleware de redacción de logs con test que falla si aparece un RFC
- [ ] Backups cifrados con **restauración probada**
- [ ] Plan de respuesta a incidentes escrito y con responsables asignados
- [ ] Principio de menor privilegio en IAM y en base de datos
- [ ] Entornos separados, **sin datos reales fuera de producción**

### 26.2 Privacidad

- [ ] Aviso de privacidad integral y simplificado conforme a la **LFPDPPP de 2025**
- [ ] Ninguna referencia al INAI ni a la ley de 2010 en ningún documento
- [ ] Registro de consentimiento con marca de tiempo y versión del aviso
- [ ] Finalidades primarias y secundarias separadas, con opción de negarse a las secundarias
- [ ] Flujo ARCO funcional y probado extremo a extremo
- [ ] **Flujo de respuesta a solicitud de supresión sobre datos fiscales** documentado
- [ ] Responsable de datos personales designado y publicado
- [ ] Inventario de datos con clasificación, finalidad y retención
- [ ] Contratos de encargado firmados con PAC, cloud, correo y analítica
- [ ] Política de retención implementada **en código**, no solo en un documento
- [ ] Verificación de que no se recolecta CURP, domicilio completo ni e.firma
- [ ] Procedimiento de notificación de vulneraciones
- [ ] Confirmación de que ningún dato personal va a blockchain

### 26.3 Fiscal — antes de emitir la primera factura real

- [ ] Contador público validó la lógica de emisión con casos reales
- [ ] Catálogos SAT actualizados y versionados
- [ ] Matriz Régimen × Uso de CFDI validada contra el Anexo 20
- [ ] Validación de RFC con dígito verificador
- [ ] Cálculo de impuestos verificado contra casos conocidos
- [ ] Prohibición de números negativos aplicada
- [ ] Decimales dentro de límite por moneda
- [ ] Pruebas completas en sandbox del PAC **con sellos de prueba, nunca reales**
- [ ] Flujo de cancelación probado con los 4 motivos
- [ ] Plazo de cancelación implementado conforme a la reforma 2026
- [ ] Almacenamiento de XML, PDF y acuses con retención de 10 años
- [ ] Verificación de integridad por hash operando
- [ ] Idempotencia probada con inyección de fallos
- [ ] Términos que prohíben el uso para operaciones simuladas
- [ ] Detección de anomalías activa
- [ ] Plan de contingencia ante caída del PAC probado
- [ ] Contrato de encargado con el PAC firmado
- [ ] Obligaciones fiscales de la propia plataforma resueltas con contador

---

## 27. Preguntas críticas sin resolver

**Fiscales / legales** — requieren contador público o fiscalista, no búsqueda web:

1. ¿Cuál es el número y texto exacto de la regla de la RMF 2026 para factura global? (V1)
2. ¿Cuáles son los supuestos exactos de cancelación sin aceptación y el umbral vigente? (V4)
3. ¿La autofacturación B2B2C tiene requisitos formales específicos en la RMF? (V8)
4. ¿La plataforma cae en el supuesto de "plataformas digitales" con acceso en tiempo real al SAT desde el 1 de abril de 2026? (V6)
5. ¿Retención de 5 o 10 años? ¿Qué prevalece entre CFF y Código de Comercio?
6. ¿Qué responsabilidad tiene la plataforma si un cliente emite CFDI sin materialidad bajo la reforma 2026?
7. ¿Existe reglamento de la nueva LFPDPPP? ¿Qué vigencia tienen los lineamientos del INAI? (V5)
8. ¿El historial de facturación de una persona física es dato patrimonial que exige consentimiento expreso?
9. ¿Ofrecer entrega por WhatsApp requiere consentimiento específico adicional?

**De negocio:**

10. ¿Existe realmente un operador de peaje dispuesto a contratar? **Esta es la pregunta que decide el proyecto.**
11. ¿Cuál es la tarifa real de PAC a 30k, 100k y 1M timbres/mes? (V7)
12. ¿Cuál es la disposición a pagar real de una concesionaria?
13. ¿Cuál es el hueco defendible frente a gigstack, Facturama, Facturapi y Ticket Factura?
14. ¿Cuánto capital hay disponible y para cuántos meses?

**Técnicas:**

15. ¿Cuál es la versión vigente del Complemento de Recepción de Pagos? (V3)
16. ¿Cuál es el estatus real del servicio de consulta del SAT sin WSDL publicado?
17. ¿Qué SLA ofrecen los PAC candidatos y con qué penalización?
18. ¿Los operadores de peaje pueden modificar su ticket para incluir un QR de autofacturación?

---

## 28. Recomendación final y próximos pasos

### 28.1 Respuestas directas

**¿El proyecto es viable?**
Parcialmente. La facturación electrónica es un mercado real y obligatorio. **Pero el proyecto tal como se planteó no es viable**: el precio de $0.10 MXN por factura tiene margen negativo estructural, y el nicho elegido (casetas de peaje) ya tiene al incumbente principal con solución propia. Con las correcciones de este documento —cobro B2B por suscripción, entrada por concesionarias medianas, alcance estrecho— sí es viable.

**¿Qué parte es viable primero?**
La autofacturación B2B2C de CFDI de ingreso con un operador piloto. Es el mínimo que construye el activo técnico central y valida la venta.

**¿Qué debería descartarse por ahora?**
Blockchain y tokenización (indefinidamente). El precio de $0.10 por factura como precio de lista. La expansión internacional. Nómina, carta porte y complementos. La custodia de CSD. Convertirse en PAC. El modo offline completo. La recuperación de gastos por scraping.

**¿Blockchain conviene?**
**No.** El sello del SAT ya provee integridad y fecha cierta con más fuerza probatoria que cualquier registro privado. Blockchain añade costo, latencia, exposición regulatoria y un conflicto irresoluble con el derecho de cancelación. El log append-only con hash encadenado y sellado de tiempo entrega el mismo beneficio de auditoría a costo casi nulo.

**¿El modelo de 10 centavos es viable?**
**No como precio.** El costo del timbre solo es de 3 a 10 veces esa cifra, la comisión de cobrar 10 centavos es 30–40 veces el monto, y el soporte cuesta más que el timbre. **Sí como meta interna de costo marginal** en la Fase 3.

**¿Cuál debería ser el MVP mínimo real?**
Una pantalla web pública donde alguien escanea el QR de su ticket, captura su RFC y su correo, y recibe su CFDI de ingreso timbrado, más un panel donde el negocio ve su historial y descarga reportes. Nada más. Con un cliente pagando.

**¿Cuál es el mayor riesgo?**
No es técnico. Es que **no exista un operador dispuesto a contratar**. El segundo mayor es construir para los 22 casos de facturación en vez de para uno.

**¿Cuál es la mayor oportunidad?**
La fragmentación del peaje mexicano: decenas de concesionarias, cada una con su portal y su ventana de 30 días, sin nadie que resuelva el problema de forma transversal. Y el volumen concentrado: nueve casetas grandes valen comercialmente más que ciento treinta comercios medianos.

### 28.2 Qué hacer en los próximos 7, 30 y 90 días

**Próximos 7 días — validar la premisa, sin escribir código**

- [ ] Listar todas las concesionarias de peaje de México con su portal actual y sus huecos
- [ ] Identificar 10 operadores medianos y conseguir el contacto del responsable de tecnología o administración
- [ ] Pedir cotización a 3 PAC con volumen estimado de 30k, 100k y 1M timbres/mes
- [ ] Agendar 1 hora con un contador público para revisar V1–V8
- [ ] Descargar el Anexo 20 vigente y leer completo el apartado de CFDI de ingreso
- [ ] Abrir cuenta en el sandbox de un PAC y timbrar un CFDI de prueba **a mano**, sin código, para entender el flujo real

**Próximos 30 días — conseguir el compromiso**

- [ ] Sostener 10 conversaciones con operadores de peaje
- [ ] Construir el modelo financiero con la tarifa PAC real
- [ ] Redactar la propuesta comercial: cuota mensual fija por punto de cobro, no precio por factura
- [ ] **Conseguir una carta de intención firmada.** Sin esto, no pasar a la Fase 1
- [ ] Redactar el aviso de privacidad conforme a la ley de 2025 y hacerlo revisar
- [ ] Definir el alcance congelado del MVP y escribirlo en un documento de una página
- [ ] Decidir la estructura legal de la empresa con contador y abogado

**Próximos 90 días — construir lo mínimo**

- [ ] Semanas 1–2: esqueleto del proyecto, Docker Compose, CI, migraciones, datos ficticios
- [ ] Semanas 3–5: motor de facturación con integración al sandbox del PAC, catálogos, validaciones
- [ ] Semanas 6–8: pantalla pública de autofacturación (QR, folio, manual) y panel del negocio
- [ ] Semanas 9–10: reportes, auditoría con hash encadenado, observabilidad
- [ ] Semana 11: pruebas de seguridad, carga y validación fiscal con el contador
- [ ] Semana 12: **piloto en producción con el operador ancla y facturas reales**

### 28.3 Lo que no se debe hacer bajo ninguna circunstancia

1. Usar CSD, e.firma o datos fiscales reales en desarrollo. Solo sandbox y sellos de prueba.
2. Subir secretos, certificados o llaves al repositorio.
3. Guardar llaves privadas en la base de datos, en el filesystem o en variables de entorno.
4. Escribir datos personales o fiscales identificables en una cadena pública.
5. Permitir facturar sin operación real, ni facilitar la simulación de operaciones.
6. Cancelar una factura por falta de pago.
7. Llamar "factura" a un acuse no timbrado.
8. Exponer la base de datos, Redis o MinIO fuera de localhost.
9. Exponer el entorno local a internet sin autenticación y sin autorización explícita.
10. Hacer push a un repositorio remoto sin autorización explícita.
11. Prometer que un PDF o un Excel tiene validez fiscal.
12. Emitir un CFDI sin confirmación explícita del usuario.

---

## 29. Fuentes consultadas

Fuentes usadas en esta investigación. **Las fuentes secundarias (blogs de proveedores y despachos) se usaron para orientación; toda cifra o requisito que se convierta en lógica de producto debe confirmarse contra el DOF, el portal del SAT o un profesional.**

**Oficiales / primarias**
- [SAT — Anexo 20, Guía de llenado de los CFDI](http://omawww.sat.gob.mx/tramitesyservicios/Paginas/documentos/Anexo_20_Guia_de_llenado_CFDI.pdf)
- [SAT — Preguntas y respuestas sobre el Anexo 20 versión 4.0](http://omawww.sat.gob.mx/tramitesyservicios/Paginas/documentos/PregFrecCFDIVer4_0.pdf)
- [SAT — Complemento Carta Porte](http://omawww.sat.gob.mx/tramitesyservicios/Paginas/complemento_carta_porte.htm)
- [SAT — Solicita autorización para operar como proveedor de certificación](https://www.sat.gob.mx/tramites/31454/solicita-autorizacion-para-operar-como-proveedor-de-certificacion)
- [SAT — Proveedores autorizados de certificación](https://www.sat.gob.mx/aplicacion/30796/proveedor-de-certificacion-de-factura-electronica-)
- [Cámara de Diputados — Ley Federal de Protección de Datos Personales en Posesión de los Particulares](https://www.diputados.gob.mx/LeyesBiblio/pdf/LFPDPPP.pdf)

**Protección de datos (reforma 2025)**
- [Garrigues — Nueva LFPDPPP y eliminación del INAI](https://www.garrigues.com/es_ES/noticia/mexico-nueva-ley-federal-proteccion-datos-personales-posesion-particulares-introduce)
- [EY México — Entrada en vigor de la nueva LFPDPPP](https://www.ey.com/es_mx/technical/tax/boletines-fiscales/nueva-ley-federal-proteccion-datos-personal-posesion-particulares)
- [Basham — Nueva LFPDPPP publicada en el DOF](https://basham.com.mx/en/nueva-ley-federal-de-proteccion-de-datos-personales-en-posesion-de-los-particulares-publicada-en-el-diario-oficial-de-la-federacion/)
- [IDC — Publican nuevas leyes sobre acceso a información y protección de datos](https://idconline.mx/corporativo/2025/03/21/publican-nuevas-leyes-sobre-acceso-a-informacion-publica-y-proteccion-de-datos-personales)

**Reforma fiscal 2026 y cancelación**
- [ContadorMx — Reformas al CFF 2026: CFDI, materialidad y facultades del SAT](https://contadormx.com/reformas-cff-2026/)
- [DMG Consultores — Reformas al CFF 2026 en materia de comprobantes fiscales](https://dmgconsultores.mx/reformas-al-cff-2026-puntos-clave-en-materia-de-comprobantes-fiscales/)
- [Sovos — RMF 2026: cambios en CFDI y cumplimiento](https://sovos.com/mx/blog/iva/rmf-2026-cambios-cfdi/)
- [Consolidé — CFF 2026: nuevas adiciones y modificaciones](https://consolide.com/blog/cff-2026-reforma-fiscal/)
- [Facturando — Motivos de cancelación CFDI 01, 02, 03 y 04](https://www.facturando.mx/blog/index.php/2026/08/11/motivos-de-cancelacion-cfdi/)
- [Alegra — Cancelación de CFDI en México 2026](https://blog.alegra.com/mexico/cancelacion-de-cfdi-paso-a-paso/)

**Conservación, factura global, RESICO**
- [ContadorMx — Plazos para conservación de la contabilidad](https://contadormx.com/plazos-para-conservacion-de-la-contabilidad-y-documentacion-del-cff/)
- [CSNSC — Análisis del artículo 30 del CFF](https://csnsc.com.mx/analisis-del-articulo-30-del-cff-obligaciones-de-conservacion/)
- [Alegra — Cómo hacer una factura global CFDI 4.0](https://blog.alegra.com/mexico/como-emitir-factura-global-cfdi/)
- [IDC — RESICO 2026: requisitos para personas físicas](https://idconline.mx/fiscal-contable/2026/01/14/resico-2026-requisitos-para-que-personas-fisicas-se-incorporen-o-cambien-de-regimen)

**QR, verificación y aspectos técnicos**
- [CryptoSys — QR code for CFDi](https://cryptosys.net/firmasat/qrcode-for-cfdi.html)
- [CfdiUtils — Consulta del estado de un CFDI en el WebService del SAT](https://cfdiutils.readthedocs.io/es/latest/componentes/estado-sat.html)
- [Facturama — API de facturación electrónica CFDI 4.0](https://facturama.mx/api-facturacion-electronica)
- [Facturama — Sandbox de API](https://apisandbox.facturama.mx/)

**Casetas de peaje**
- [Expansión — Paso a paso para facturar casetas de CAPUFE](https://expansion.mx/finanzas-personales/2025/04/01/paso-paso-facturar-casetas-capufe)
- [casetas.com.mx — Facturación de casetas en México por operador y TAG](https://casetas.com.mx/facturacion)
- [Gastos de Viaje — Directorio de páginas para facturas de casetas](https://www.gastosdeviaje.mx/blog/directorio-de-paginas-para-obtener-facturas-electronicas-cfdi-de-las-casetas-de-cobro-en-mexico/)

**Costos y competencia**
- [EdiFactMx — Planes de timbrado](https://www.edifact.com.mx/planes-de-timbrado)
- [Facturama — Paquetes de folios y timbres fiscales](https://facturama.mx/planes-facturacion)
- [Atempora — Stripe vs Mercado Pago vs Conekta en México 2026](https://atempora.studio/blog/stripe-vs-mercado-pago-vs-conekta)
- [Notu — Procesamiento automático de tickets](https://www.notu.mx/)
- [Fotofacturas — Facturación automática de tickets](https://fotofacturas.ai/)
- [gigstack — Facturación automática CFDI](https://gigstack.pro/facturacion-automatica)

**Activos virtuales**
- [Legal Paradox — Ley Fintech México 2026](https://www.legalparadox.com/es/ley-fintech)
- [AbogadoBlockchain — Licencia cripto CNBV en México](https://abogadoblockchain.com/licencia-cripto-cnbv-mexico/)
- [Mercado Pago — Criptomonedas en México: regulación y Ley Fintech](https://www.mercadopago.com.mx/blog/regulacion-criptomonedas-mexico)

---

## 30. Verificación de este entregable

Este documento es de investigación y planeación: no hay código que ejecutar ni tests que correr. Se verifica así:

1. **Verificación fiscal.** Llevar §4, §5 y §7 a un contador público con práctica en CFDI. Objetivo: que confirme o corrija cada afirmación, con especial atención a V1–V8 de §2.2.
2. **Verificación legal de privacidad.** Llevar §5 y §6 a un abogado especializado. Objetivo: confirmar el marco de la ley de 2025, la clasificación del historial de facturación y la política de retención.
3. **Verificación económica.** Ejecutar el bloque de "Próximos 7 días" de §28.2 y sustituir las estimaciones de §13 por cotizaciones reales. Objetivo: recalcular el punto de equilibrio con cifras en firme.
4. **Verificación comercial.** La prueba definitiva: una carta de intención firmada por un operador. Si en 30 días no hay ninguna, el plan debe revisarse antes de escribir código de producción.
5. **Verificación técnica.** Abrir una cuenta de sandbox y timbrar un CFDI de prueba manualmente, sin código. Objetivo: validar que el flujo de §7.1 corresponde a la realidad del proveedor elegido.

---

### Qué sigue después de aprobar este documento

Al salir del modo de planeación se puede, si lo autorizas:

1. Copiar este documento a `C:\Users\gioes_hsnigcw\Desktop\facturafacil\docs\PLAN_MAESTRO.md` para que viva junto al proyecto.
2. Generar el esqueleto técnico (backend Go, PWA, Docker Compose, Makefile, migraciones, datos ficticios, health check, demo de QR, reportes mock). **Sin instalar dependencias, sin abrir puertos, sin tocar el firewall, sin levantar servidores y sin crear repositorio remoto** hasta que lo confirmes en cada caso.

**Mi recomendación es no generar el esqueleto todavía.** Las semanas 1 y 2 del plan de 90 días son código; las semanas anteriores son conversaciones con operadores de peaje y con un contador. El código escrito antes de esas conversaciones tiene alta probabilidad de tirarse.
