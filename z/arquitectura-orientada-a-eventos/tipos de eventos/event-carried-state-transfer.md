Eventos con Transferencia de Estado (Event-Carried State Transfer)

Aquí el evento es un "paquete completo". El sistema que lo envía incluye toda la información necesaria para que el que lo recibe no tenga que preguntar nada más.

- Qué contiene: El ID y todos los datos relevantes del objeto en ese momento.

- Ejemplo: El evento PedidoPagado ahora incluye { "pedidoId": 123, "cliente": "Juan", "total": 50.00, "items": [...] }.

- Lo bueno: Los sistemas se vuelven muy independientes. El sistema de logística puede anotar el pedido sin hablar con el sistema de ventas.

- Lo malo: Los mensajes son más pesados y hay que tener cuidado con que los datos no se queden obsoletos.
