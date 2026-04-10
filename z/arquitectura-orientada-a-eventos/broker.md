El Broker: La Autopista

El Broker (como Kafka o RabbitMQ) es la infraestructura. Es el servicio de correos que lleva los mensajes de un microservicio a otro. Sin el Broker, los servicios tendrían que hablar directamente entre ellos, lo cual es frágil (si uno está caído, el otro falla).