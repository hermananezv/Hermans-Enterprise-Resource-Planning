# Conseptos aplicados

A nivel de decicion de arquitectura.

[transactional outbox](./deciciones%20de%20arquitectura/transactional-outbox.md)
[text](./deciciones%20de%20arquitectura/circuit-breaker.md)
[text](./)
[text](./)
[text](./)
[text](./)

¿Qué tienen en común?

- Gestionan la complejidad de los sistemas distribuidos – Donde no hay una base de datos única ni transacciones ACID clásicas.

- Buscan la resiliencia y el desacoplamiento – Evitan que un fallo o una espera bloqueen todo el sistema.

- Trabajan con eventos y asincronía – La mayoría se basa en mensajes o eventos para coordinar acciones.

- Son patrones de nivel arquitectónico – No son simples trucos de código, sino estrategias de diseño.

Relación entre ellos
|Patrón|Función principal|Se relaciona con...|
|---|---|---|
|Optimistic Locking|Evita conflictos de concurrencia en una misma entidad (sin bloqueos pesados).|Suele usarse dentro de un agregado en CQRS o Saga para asegurar que las actualizaciones no pisen cambios de otro proceso.|
|Transactional Outbox|Garantiza que un evento se publique de forma fiable después de cambiar la base de datos.|Es la base para que Saga o CQRS puedan notificar a otros servicios de forma atómica.|
|CQRS ligero|Separa la escritura (comandos) de la lectura (consultas) para escalar y simplificar.|A menudo se combina con Transactional Outbox para publicar eventos desde el lado de escritura.|
|Circuit Breaker|Evita llamadas repetidas a un servicio que está fallando (protege de cascadas de fallos).|Protege a los orquestadores de Saga o a los clientes de CQRS cuando llaman a otros microservicios.|
|Saga Pattern|Gestiona transacciones distribuidas mediante una secuencia de pasos locales + compensaciones.|Utiliza un Broker (coreografía) o un orquestador; necesita Transactional Outbox para enviar eventos fiables; y puede requerir Optimistic Locking en cada paso.|
|Broker|Actúa como intermediario de mensajes (ej. Kafka, RabbitMQ) para comunicación asíncrona.|Es el medio de transporte para los eventos de Outbox, Saga y CQRS; también puede ayudar a implementar Circuit Breaker a nivel de red.|

Área de aplicación

Todos se aplican en arquitecturas de microservicios, sistemas orientados a eventos (EDA) y aplicaciones cloud-native. Por ejemplo:

- En un e-commerce:

- - Saga coordina el flujo “pedido → pago → inventario → envío”.

- - Transactional Outbox asegura que al crear el pedido se publique el evento “PedidoCreado”.

- - Broker (Kafka) distribuye ese evento a los demás servicios.

- - Optimistic Locking evita que dos procesos descuenten el mismo producto del stock.

- - Circuit Breaker detiene los intentos de llamar al servicio de pago si está caído.

- - CQRS ligero permite tener una vista rápida del estado del pedido (lectura) separada de la lógica de negocio (escritura).