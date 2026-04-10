patrón Database per Service (una base de datos por servicio). Este patrón es fundamental en la arquitectura de microservicios y está muy relacionado con los conceptos que vimos antes (Saga, CQRS, Transactional Outbox, etc.).
¿En qué consiste?

En lugar de que todos los servicios compartan una única base de datos central (como suele hacerse en una aplicación monolítica), cada microservicio tiene su propia base de datos privada. Otros servicios no pueden acceder directamente a esa base de datos; solo lo hacen a través de la API pública del servicio propietario.
Relación con los patrones anteriores

|Patrón|Relación con Database per Service|
|------|---------------------------------|
|Saga Pattern|Como no hay una transacción distribuida ACID, Saga coordina los pasos entre servicios con sus bases de datos independientes.|
|Transactional Outbox|Necesario para publicar eventos de forma atómica cuando se escribe en la base de datos local de un servicio.|
|CQRS ligero|Puede tener su propia base de datos de lectura separada de la de escritura, pero siempre dentro del mismo servicio o en un servicio de consultas dedicado.|
|Optimistic Locking|Se aplica dentro de la base de datos de cada servicio para manejar concurrencia local.|
|Circuit Breaker|Protege las llamadas entre servicios cuando una base de datos privada está lenta o caída.|
|Broker|Es el medio por el cual los servicios se notifican cambios en sus bases de datos (eventos).|

Ventajas principales

* Desacoplamiento fuerte – Cada equipo puede elegir la base de datos que mejor se adapte a su problema (SQL, NoSQL, clave-valor, etc.). Políglota persistencia.

* Escalabilidad independiente – Se escala el servicio y su base de datos según su propia carga.

* Tolerancia a fallos aislada – Si la base de datos de un servicio falla, los demás pueden seguir funcionando.

* Seguridad por encapsulamiento – No hay acceso directo a los datos de otro servicio, se respeta la frontera de dominio.

Desventajas y desafíos

* Consultas entre servicios – Una simple consulta que requiera datos de varios servicios se vuelve compleja. Se resuelve con:

* * API composition (el cliente o un gateway llama a cada servicio)

* * CQRS con vistas materializadas (tablas de lectura que escuchan eventos)

* Consistencia eventual – No hay transacciones distribuidas. Se usan Sagas y eventos para mantener la consistencia final.

* Complejidad operativa – Hay que gestionar múltiples bases de datos (backups, réplicas, migraciones, etc.).

* Dificultad para reportes – Los reportes analíticos que cruzan todos los datos requieren un data warehouse o un servicio de consultas aparte.

¿Dónde se aplica?

* En microservicios (es el estándar de facto).

* En sistemas que necesitan alta escalabilidad y desacoplamiento.

* Cuando los equipos de desarrollo son independientes y quieren elegir su propia tecnología de persistencia.

* En proyectos que ya han asumido la complejidad de los patrones de consistencia eventual (Saga, Outbox, etc.).

¿Cuándo NO usarlo?

* En un monolito pequeño o una aplicación sencilla (añade complejidad innecesaria).

* Si los requisitos de consistencia fuerte y transacciones distribuidas son obligatorios (por ejemplo, sistemas financieros muy estrictos, aunque aún así a veces se usa con Sagas).

* Si el equipo no tiene experiencia en sistemas distribuidos.

En resumen

Database per Service es la base física que permite que los otros patrones (Saga, Outbox, CQRS) tengan sentido. Sin esta separación de bases de datos, muchos de los problemas que resuelven esos patrones no existirían (porque podrías usar una transacción ACID clásica). Por eso, cuando eliges microservicios, casi inevitablemente adoptas este patrón junto con los demás para manejar la consistencia y la comunicación.

-----

1. El concepto de "Database per Service"

Tener bases de datos separadas (física o lógicamente) permite que el contexto de IAM y el de RRHH evolucionen de forma independiente.

* En IAM: Tu base de datos estará optimizada para la lectura rápida y la seguridad. Probablemente uses tablas para users, roles, permissions y sessions.

* En RRHH: Tu base de datos estará optimizada para relaciones complejas de negocio. Tendrás employees, contracts, payroll_info, departments, etc.

1. ¿Cómo se relacionan si están en bases de datos distintas?

Aquí es donde muchos arquitectos cometen el error de hacer un "JOIN" entre bases de datos, lo cual rompe el desacoplamiento. La forma correcta es mediante Identificadores Correlativos (IDs):

* La DB de RRHH guarda el registro del empleado con un employee_id (ej. UUID-123).

* La DB de IAM guarda el registro del usuario con su propio user_id, pero incluye una columna llamada external_identity_id donde guarda el UUID-123.

El secreto: El microservicio de IAM no sabe qué es un "Sueldo" o un "Departamento", solo sabe que el usuario con ese ID tiene permiso para entrar.
3. El reto de la Integridad Referencial

Al no tener una única base de datos, pierdes las "Foreign Keys" (claves foráneas) tradicionales del motor SQL. Para solucionar esto, tu implementación debe considerar:

* Consistencia Eventual: Cuando cambias el nombre de un empleado en RRHH, se lanza un evento (EmployeeUpdated) y el servicio de IAM actualiza el nombre en su base de datos si lo necesita para el perfil del usuario.

* Arquitectura de Eventos: Como vi en tu documentación que ya estás usando esquemas de eventos, esto encaja perfectamente. El "pegamento" entre las dos bases de datos no es un cable directo, sino el Event Bus (Kafka, RabbitMQ, etc.).

1. ¿Cuándo NO separarlas físicamente?

Si tu proyecto es pequeño o estás en una fase muy temprana (MVP), puedes usar una Separación Lógica:

* Misma instancia de base de datos (PostgreSQL, por ejemplo).

* Diferentes Schemas: Un esquema llamado iam y otro llamado hr.

* Regla de Oro: El código del módulo de RRHH tiene prohibido consultar tablas del esquema iam, y viceversa. Solo se comunican por APIs o Eventos.
