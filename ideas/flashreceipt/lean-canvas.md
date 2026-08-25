# Lean Canvas

**Equipo:** FlashReceipt
**Vertical:** Pagos y cumplimiento fiscal — autofacturación en punto de venta

## 1 · Segmentos de clientes
- Cliente: comercio con alto volumen de tickets y facturación manual. Restaurantes, gasolineras, farmacias y refaccionarias de 1 a 10 sucursales, en el corredor Bajío – Monterrey.
- Usuario: dos. El consumidor que quiere su factura, y el cajero que hoy la captura a mano.
- Pagador: el comercio. El consumidor nunca paga.
- Primera empresa: una cadena local de 3 a 5 sucursales, socia de CANACINTRA, con al menos 200 facturas al mes.

## 2 · Problema
1. El cajero captura el RFC a mano, en hora pico, con la fila esperando. Cinco minutos por factura y un error de dedo que después hay que cancelar y volver a emitir.
2. El consumidor entra a un portal distinto por cada comercio, llena los mismos datos cada vez y a veces se le pasa el plazo.
3. El comercio o paga un portal propio, o manda al cliente a uno que no controla y se queda con la queja.

Alternativa actual: capturar en caja, mandar el ticket por WhatsApp al contador, o un portal propio que casi nadie mantiene.

Qué le cuesta al comercio: cerca de **1,200 pesos al mes** solo en tiempo de caja. Supuestos a la vista, todos a validar en el piloto:

| Concepto | Cálculo | Resultado |
|---|---|---|
| Facturas al mes | 3,000 tickets, 8 por ciento pide factura | 240 |
| Tiempo de caja | 240 × 5 minutos | 20 horas |
| Costo cargado del cajero | 20 horas × 60 pesos | **1,200 pesos** |

No incluye el costo de las facturas mal capturadas que hay que cancelar, ni al cliente que se va molesto. Esos dos también se miden en el piloto.

## 3 · Propuesta única de valor
El lunes tu cliente escanea el QR de su ticket y tiene su factura en menos de un minuto. Tu cajero no vuelve a capturar un RFC.

## 4 · Solución
1. Escanear: el consumidor lee el QR impreso en su ticket con su propio celular. El cajero no toca nada.
2. Recordar: sus datos fiscales se capturan una sola vez y quedan en su bóveda. La segunda factura, en cualquier comercio de la red, son dos toques.
3. Timbrar: emitimos el CFDI **con el sello digital del comercio, a través de su PAC**, porque el comercio nos contrata y nos autoriza. No facturamos a nombre de nadie que no nos haya contratado.

## 5 · Canales
Integración con los puntos de venta que los comercios ya usan: Clip, Mercado Pago, Soft Restaurant. Ahí está la distribución. En paralelo, delegaciones de CANACINTRA y los despachos contables, que recomiendan porque reciben contabilidad limpia.

## 6 · Flujos de ingreso
- Quién paga: el comercio, con saldo prepagado.
- Modelo: por CFDI timbrado. Si no se emite, no se cobra.
- Precio: 2.00 pesos por factura emitida.
- Contra qué se compara: contra los 1,200 pesos al mes de tiempo de caja. Ese comercio nos paga 480 y se ahorra 1,200. Contra portales propios, que cuestan desarrollo y mantenimiento todo el año.

## 7 · Estructura de costos (una operación)
Costo de emitir **una** factura.

| Concepto | Pesos |
|---|---|
| Timbre del PAC | 0.60 |
| Infraestructura y entrega del XML y PDF | 0.11 |
| Liquidación del cobro en Stellar | 0.0002 |
| Soporte prorrateado (2 por ciento de facturas genera ticket, a 25 pesos) | 0.50 |
| **Costo total** | **≈ 1.21** |
| Precio | 2.00 |
| **Margen de contribución** | **≈ 0.79** |

El costo que manda no es el timbre: es el soporte. Cada punto porcentual de facturas que genera una llamada cuesta casi lo mismo que timbrar. Por eso la interfaz no es un adorno, es la palanca del margen.

Aquí entra Stellar y solo aquí. Una taquería con 240 facturas debe 480 pesos al mes. Cobrarlos con tarjeta cuesta 3.6 por ciento más 3 pesos, y conciliar miles de cobros chicos cada mes es su propio problema. Stellar descuenta cada factura del saldo prepagado por fracciones de centavo, en segundos, y deja un registro que el comercio y nosotros leemos igual, sin tener que creernos.

**El riesgo está en el ancla, no en la red.** Convertir pesos a token y de regreso cuesta mucho más que la comisión de Stellar. Si el ancla cobra más de 1 por ciento de spread, el micropago deja de tener sentido y la respuesta honesta es prepago por SPEI contra una base de datos. Eso se mide en el piloto, no se supone.

## 8 · Métricas clave
1. Porcentaje de facturas que el consumidor completa solo, sin que intervenga el cajero. Meta del piloto: 90 por ciento o más.
2. Facturas por comercio al mes, sostenidas al tercer mes. Si cae, fue novedad y no hábito.

## 9 · Ventaja injusta
Dos cosas, y ninguna es la tecnología. Primero, estar dentro del punto de venta que el comercio ya usa: salir de ahí le cuesta cambiar de sistema. Segundo, la red de dos lados: cada consumidor con sus datos guardados le pide a su siguiente comercio que se integre, y cada comercio integrado hace la bóveda más útil. Un competidor copia la aplicación en 30 días. No copia 200 comercios ya conectados ni los convenios con los proveedores de caja.
