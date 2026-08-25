# Lean Canvas

**Equipo:** FlashReceipt
**Vertical:** Logística y transporte de carga — recuperación de CFDI de peaje

## 1 · Segmentos de clientes
- Cliente: empresa manufacturera socia de CANACINTRA con flota propia o dedicada, 10 a 50 unidades, corredor Bajío – Monterrey.
- Usuario: el operador de la unidad y el auxiliar administrativo que persigue los tickets.
- Pagador: el gerente de administración o el contralor, porque el IVA no acreditado sale de su presupuesto.
- Primera empresa: una socia de CANACINTRA del Bajío con flota propia y gasto en casetas mayor a 100 mil pesos al mes.

## 2 · Problema
1. El ticket de caseta se pierde en la cabina y nadie lo factura antes de que cierre la ventana.
2. Cada concesionaria tiene su propio portal: CAPUFE, Arco Norte, CONMEX, Autopistas del Sureste y las estatales. No hay un solo lugar donde facturar.
3. La mayoría de los operadores cierra el folio a los 30 días naturales. Después de esa fecha el gasto ya no se puede facturar.

Alternativa actual: un auxiliar entrando portal por portal, con hoja de cálculo, o simplemente asumir la pérdida.

Qué le cuesta: cerca de **12 mil pesos al mes** en una flota de 20 unidades. Desglose del ejemplo, con supuestos que el piloto debe validar:

| Concepto | Cálculo | Monto |
|---|---|---|
| Gasto en casetas | 20 unidades × 6 mil pesos al mes | 120,000 |
| Tickets sin facturar (hipótesis 25 por ciento) | 120,000 × 0.25 | 30,000 |
| IVA que no se acredita | 30,000 × 16/116 | 4,138 |
| ISR por la base no deducible | 25,862 × 30 por ciento | 7,759 |
| **Pérdida mensual** | | **11,897** |
| **Pérdida anual** | | **142,764** |

El 25 por ciento es una hipótesis, no un dato medido. Medirlo en la primera empresa es el objetivo del piloto.

## 3 · Propuesta única de valor
El lunes, el operador toma una foto del ticket y se acabó su trabajo: el contador recibe el XML timbrado antes de que cierre la ventana de la concesionaria.

## 4 · Solución
1. Escanear: el operador fotografía el ticket o lee su QR desde el celular, en la caseta o al final del turno.
2. Rastrear: la plataforma sabe qué concesionaria es, qué ventana tiene y cuántos días quedan, y avisa antes de que venza.
3. Entregar: gestiona el CFDI ante el portal de la concesionaria y deja el XML en el buzón del contador, ya conciliado contra el gasto.

## 5 · Canales
Delegaciones de CANACINTRA y cámaras de transporte para la primera decena; después, el despacho contable de cada empresa, que es quien sufre el faltante y quien recomienda.

## 6 · Flujos de ingreso
- Quién paga: la empresa de la flota. Nunca el operador.
- Modelo: comisión de éxito por ticket recuperado, con saldo prepagado. Si no se recupera, no se cobra.
- Precio: 2.00 pesos por ticket efectivamente facturado.
- Contra qué se compara: Notu cobra 2.97 pesos por ticket, Fotofacturas desde 99 pesos al mes, Clara alrededor de 299 pesos al mes. Y contra las horas del auxiliar, que hoy cuestan más que las tres.

## 7 · Estructura de costos (una operación)
Costo de recuperar **un** ticket. No emitimos el CFDI: lo emite la concesionaria, así que no pagamos timbre.

| Concepto | Pesos |
|---|---|
| Lectura del ticket (OCR o QR) | 0.15 |
| Gestión ante el portal o API de la concesionaria | 0.10 |
| Almacenamiento y entrega del XML | 0.02 |
| Liquidación del cobro en Stellar | 0.0002 |
| Soporte prorrateado | 0.40 |
| **Costo total** | **≈ 0.67** |
| Precio | 2.00 |
| **Margen de contribución** | **≈ 1.33** |

Aquí entra Stellar y solo aquí. Cobrar 2 pesos por evento con tarjeta es imposible: Stripe cobra 3.6 por ciento más 3 pesos, es decir más que el propio cobro. Stellar liquida cada recuperación contra el saldo prepagado por fracciones de centavo, en segundos, y deja un registro que la empresa y nosotros vemos igual. La cobranza deja de ser el cuello de botella del modelo.

**El riesgo está en el ancla, no en la red.** Convertir pesos a token y de regreso cuesta mucho más que la comisión de Stellar. Si el ancla cobra más de 1 por ciento de spread, el margen se cae y la respuesta honesta es prepago por SPEI contra una base de datos. La red se queda solo si el ancla sale barata. Eso se mide en el piloto, no se supone.

## 8 · Métricas clave
1. Porcentaje de tickets facturados dentro de la ventana de la concesionaria. Meta del piloto: pasar de la línea base medida a 95 por ciento o más.
2. Pesos de IVA acreditado recuperados por unidad al mes.

## 9 · Ventaja injusta
El mapa vivo de qué concesionaria acepta qué, con qué ventana y por qué portal, más los convenios firmados con ellas. La aplicación se copia en 30 días. Quince convenios y saber que un portal cambió su flujo el martes pasado, no.
