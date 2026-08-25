# Pitch

**Equipo:** FlashReceipt · **Duración:** 5 minutos · **Al frente:** 2 personas

| Tiempo | Sección | Guion |
|---|---|---|
| 0:00–1:00 | Problema | Viernes, nueve de la noche, restaurante lleno. Un cliente pide factura. El cajero deja la fila esperando y captura a mano el RFC, el uso de CFDI, el código postal. Cinco minutos. Si se equivoca en una letra, hay que cancelar y volver a emitir. Ese comercio hace 240 facturas al mes: **20 horas de caja, 1,200 pesos mensuales** que se van en teclear datos que el cliente ya se sabe. Y del otro lado, el cliente entra a un portal distinto por cada comercio y llena los mismos datos cada vez. Nadie está contento y todos están pagando. |
| 1:00–1:45 | Cliente | Comercios con muchos tickets y facturación manual: restaurantes, gasolineras, farmacias, refaccionarias. De una a diez sucursales, corredor Bajío – Monterrey. **Usan** dos personas: el consumidor que quiere deducir y el cajero que hoy captura. **Paga** el comercio, porque el tiempo de caja es suyo y la queja también. El consumidor nunca paga. Si le cobramos al consumidor, no lo usa. |
| 1:45–3:00 | Solución | Tres verbos. **Escanear:** el cliente lee el QR impreso en su ticket con su propio celular. El cajero no toca nada, la fila no se detiene. **Recordar:** sus datos fiscales se capturan una sola vez y quedan guardados. La segunda factura, en cualquier comercio de la red, son dos toques. **Timbrar:** emitimos el CFDI con el sello digital del comercio, a través de su PAC, porque el comercio nos contrata y nos autoriza. Esto es importante y lo decimos claro: nadie puede facturar a nombre de un negocio sin su sello. Nosotros no inventamos esa vuelta. Operamos dentro de la regla. |
| 3:00–4:00 | Modelo | **2 pesos por factura emitida**, con saldo prepagado. Si no se emite, no se cobra. Nos cuesta 1.21: margen de **79 centavos**. Ese restaurante nos paga 480 pesos al mes y se ahorra 1,200. Y ojo, el costo que manda no es el timbre del SAT, son 60 centavos: es el soporte, 50. Por eso la interfaz es la palanca del margen, no un adorno. Mercado del primer año, chico y honesto: **200 comercios en una zona**. Son 48 mil facturas al mes, 96 mil pesos de ingreso y unos 38 mil de margen. No es un mercado de mil millones. Es uno que podemos ganar completo antes de que alguien voltee. Aquí entra Stellar, en un solo lugar: cobrarle 480 pesos al mes a cada taquería con tarjeta cuesta 3.6 por ciento más 3 pesos por cobro, y conciliar miles de cobros chicos es su propio problema. Stellar descuenta cada factura del prepago por fracciones de centavo, en segundos, con un registro que el comercio y nosotros leemos igual. |
| 4:00–5:00 | Piloto | Empresa piloto: una cadena local de 3 a 5 sucursales, socia de CANACINTRA, con al menos 200 facturas al mes. **Lo que pedimos el lunes:** que impriman un QR en su ticket, que es algo que su punto de venta ya sabe hacer, y dos semanas de medición con un cronómetro en la caja. Con eso comprobamos la única cifra que hoy es supuesto: cuánto tiempo de caja se va realmente en facturar. Dijimos cinco minutos. Si son dos, el ahorro es la mitad y lo diremos. **Riesgo número uno: el ancla.** La comisión de Stellar es despreciable, pero convertir pesos a token y de regreso no lo es. Si el ancla nos cobra más de 1 por ciento de spread, el micropago se cae y la respuesta honesta es prepago por SPEI contra una base de datos. La red se queda solo si el ancla sale barata. Eso se mide, no se supone. |

---

## Notas de entrega

**Reparto:** persona A abre con problema y cliente (0:00–1:45). Persona B toma solución, modelo y piloto (1:45–5:00).

**La cifra que se repite tres veces:** 1,200 pesos al mes de tiempo de caja. Se dice al inicio, se contrasta contra los 480 que cobramos, y se cierra diciendo que hay que medirla.

**Si preguntan por qué no lo hace el comercio solo:** algunos lo intentan y montan su portal. Lo mantienen seis meses. El problema no es montar el portal, es actualizarlo cada vez que el SAT cambia un catálogo, y atender al cliente que no entiende qué es el uso de CFDI.

**Si preguntan por qué Stellar y no SPEI:** por el tamaño y la frecuencia del cobro, no por la tecnología. A 2 pesos por evento, la comisión tradicional se come el cobro. Si el ancla resulta cara, SPEI con corte mensual es la respuesta correcta y no nos vamos a enamorar de la red.

**Si preguntan por blockchain para evitar doble facturación:** no hace falta. El SAT ya da un folio fiscal único por comprobante, el UUID. Un ticket ya facturado se detecta con una base de datos. Poner eso en una cadena sería resolver dos veces el mismo problema.

**Si preguntan por los datos personales:** los datos fiscales del consumidor no se escriben en la cadena. En Stellar solo viaja el cobro entre el comercio y nosotros. La factura vive donde debe vivir, que es el XML timbrado.
