# Transactional Outbox 

Cuando trabajas con sistemas distribuidos o microservicios, el mayor miedo es que una parte del sistema se entere de algo y la otra no.

El Problema: El "Teléfono Descompuesto"

Imagina que tienes una tienda online. Cuando alguien compra algo, pasan dos cosas:

- Guardas el pedido en tu Base de Datos.

- Envías un mensaje a Logística (otro sistema) para que preparen el paquete.

El riesgo: Si guardas el pedido pero el internet falla justo antes de avisar a Logística, el cliente pagó pero su paquete nunca saldrá. O al revés: avisas a Logística, pero la base de datos falla y tú no tienes registro de la venta.

La Solución: La "Bandeja de Salida" (Outbox)

El patrón Transactional Outbox soluciona esto usando una técnica de "todo o nada" dentro de tu propia base de datos.

1. El Bloque Único (La Transacción)

En lugar de intentar hablar con el mundo exterior de inmediato, haces dos cosas dentro de una misma transacción de base de datos:

- Insertas el pedido en la tabla de Pedidos.

- Insertas un mensaje (una nota) en una tabla especial llamada Outbox (Bandeja de Salida).

Como están en la misma transacción, o se guardan las dos, o no se guarda ninguna. No hay punto medio.
2. El Repartidor (Message Relay)

Ahora que el mensaje está seguro en tu base de datos, un proceso separado (como un pequeño robot) revisa la tabla Outbox constantemente.

- Si ve un mensaje nuevo, lo toma y lo envía al sistema de Logística.

- Una vez que Logística confirma que lo recibió, el robot borra el mensaje de la tabla Outbox o lo marca como "Enviado".

¿Por qué es genial?

- Fiabilidad: El mensaje nunca se pierde. Si el sistema de Logística está caído, el "repartidor" seguirá intentando enviar el mensaje que está guardado en la tabla Outbox hasta que lo logre.

- Consistencia: Tu base de datos y el resto del mundo siempre terminan estando de acuerdo.

En resumen: Es como dejar una carta en tu buzón personal (Outbox) al mismo tiempo que anotas el gasto en tu libreta. Aunque el cartero tarde en pasar, la nota ya está segura y el proceso de envío está garantizado.