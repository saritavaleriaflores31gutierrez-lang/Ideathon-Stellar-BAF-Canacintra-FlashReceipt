# Pitch

**Equipo:** FlashReceipt · **Duración:** 5 minutos · **Al frente:** 2 personas

| Tiempo | Sección | Guion |
|---|---|---|
| 0:00–1:00 | Problema | Una flota de 20 camiones gasta 120 mil pesos al mes en casetas. Una cuarta parte de esos tickets nunca se factura: se quedan en la cabina, o el auxiliar no alcanzó a entrar a los seis portales distintos antes de que cerrara la ventana de 30 días. Ese gasto sigue siendo real, pero deja de ser deducible y su IVA no se acredita. Son cerca de **12 mil pesos al mes**, **143 mil al año**, que la empresa paga y no recupera. No es un problema de dinero perdido en la carretera. Es dinero perdido en un cajón. |
| 1:00–1:45 | Cliente | Empresas manufactureras socias de CANACINTRA, con flota propia o dedicada, de 10 a 50 unidades, en el corredor Bajío – Monterrey. **Usa** el operador, que hoy junta papelitos. **Sufre** el auxiliar administrativo, que persigue portales. **Paga** el contralor, porque el IVA no acreditado sale de su presupuesto y lo ve cada cierre de mes. Vamos por el contralor. |
| 1:45–3:00 | Solución | Tres verbos. **Escanear:** el operador toma una foto del ticket o lee su QR desde el celular, en la caseta o al final del turno. Se acabó su trabajo. **Rastrear:** nosotros sabemos qué concesionaria es, qué ventana tiene y cuántos días quedan. Avisamos antes de que venza, no después. **Entregar:** gestionamos el CFDI ante la concesionaria y dejamos el XML en el buzón del contador, ya conciliado contra el gasto. Nosotros no emitimos la factura: la emite la concesionaria, como debe ser. Nosotros nos aseguramos de que exista y llegue a tiempo. |
| 3:00–4:00 | Modelo | Cobramos **2 pesos por ticket facturado**, con saldo prepagado. Si no lo recuperamos, no se cobra. Nos cuesta 67 centavos: margen de **1.33 por ticket**. Esa flota de 20 unidades genera 800 tickets al mes: nos paga 1,600 pesos y recupera cerca de 12 mil. Siete a uno. Mercado del primer año, chico y honesto: **50 flotas del corredor Bajío**. Son 40 mil tickets al mes, 80 mil pesos de ingreso mensual y unos 53 mil de margen. No es un mercado de mil millones. Es uno que sí podemos ganar completo. Y aquí entra Stellar, en un solo lugar: cobrar 2 pesos por evento con tarjeta es imposible, Stripe cobra 3.6 por ciento más 3 pesos, más que el propio cobro. Stellar liquida cada recuperación contra el prepago por fracciones de centavo, en segundos, con un registro que la empresa y nosotros vemos igual. |
| 4:00–5:00 | Piloto | Empresa piloto: una socia de CANACINTRA del Bajío con flota propia y más de 100 mil pesos al mes en casetas. **Lo que pedimos el lunes:** tres meses de sus tickets de caseta y una hora de su auxiliar administrativo. Con eso medimos la única cifra que hoy es hipótesis: qué porcentaje de sus tickets realmente se pierde. Dijimos 25 por ciento. Si son 5, no hay negocio y lo diremos. **Riesgo número uno: el ancla.** La comisión de Stellar es despreciable, pero convertir pesos a token y de regreso no lo es. Si el ancla nos cobra más de 1 por ciento de spread, el modelo de micropago se cae y la respuesta honesta es prepago por SPEI contra una base de datos. La red se queda solo si el ancla sale barata. Eso se mide en el piloto. No lo vamos a suponer desde aquí. |

---

## Notas de entrega

**Reparto:** persona A abre con problema y cliente (0:00–1:45). Persona B toma solución, modelo y piloto (1:45–5:00).

**La cifra que se repite tres veces:** 143 mil pesos al año perdidos por flota. Se dice al inicio, se contrasta contra los 1,600 pesos que cobramos, y se cierra con que hay que medirla.

**Si preguntan por qué no lo hace la concesionaria:** ya lo hace, cada una por su lado. CAPUFE tiene su portal, Arco Norte el suyo, las estatales el suyo. El problema no es que falte un portal. Es que sobran, y ninguno tiene incentivo para resolver el de al lado.

**Si preguntan por qué Stellar y no SPEI:** por el tamaño del cobro, no por la tecnología. A 2 pesos por evento, la comisión tradicional se come el cobro. Si el ancla resulta cara, SPEI con corte mensual es la respuesta correcta y no nos vamos a enamorar de la red.

**Si preguntan por el scraping:** no raspamos portales. El camino es convenio con la concesionaria. Sin convenio no hay integración, y eso también es parte de lo que el piloto tiene que probar.
