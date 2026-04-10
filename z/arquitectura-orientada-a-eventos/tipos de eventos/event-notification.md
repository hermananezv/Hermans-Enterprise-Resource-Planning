Eventos de Notificación (Event Notification)

Es el tipo más simple. Es como un "toque de atención". El evento solo dice que algo pasó, pero no da muchos detalles.

- Qué contiene: Normalmente solo el ID de lo que cambió y el nombre del evento.

- Ejemplo: Un evento llamado PedidoPagado con el contenido { "pedidoId": 123 }.

- Lo bueno: Son muy ligeros.

- Lo malo: El que recibe el evento tiene que "llamar de vuelta" al sistema de origen para preguntar: "¿Y qué productos tiene ese pedido?".