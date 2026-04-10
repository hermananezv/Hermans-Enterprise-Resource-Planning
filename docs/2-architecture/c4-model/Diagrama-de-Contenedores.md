# Infraestructura - C4 Model (Containers)

Para orquestar la comunicación entre las aplicaciones cliente y nuestros Bounded Contexts, implementamos un patrón de **API Gateway**.

## Diagrama de Contenedores

```plantuml
@startuml C4_Elements
!include [https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Container.puml](https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Container.puml)

Person(employee, "Empleado", "Usuario del sistema")
Container(spa, "Web Application", "React / TypeScript", "Interfaz principal del ERP Core")
Container(api_gateway, "API Gateway / BFF", "Node.js / Nginx", "Enrutamiento, validación de Tokens JWT e inyección de encabezados de seguridad (Latencia < 50ms)")

System_Boundary(erp, "ERP Core Backend") {
    Container(iam, "IAM Context", "Go / Java", "Autenticación, autorización (RBAC)")
    Container(hr, "HR Context", "Go / Java", "Ciclo de vida del empleado y contratos")
    Container(notifications, "Notifications Context", "Python / Node", "Envío confiable de correos y SMS")
    
    ContainerDb(msg_bus, "Message Broker", "RabbitMQ / Kafka", "Bus de Eventos de Integración (Pub/Sub)")
}

Rel(employee, spa, "Interactúa mediante HTTPS")
Rel(spa, api_gateway, "Llamadas REST / GraphQL", "HTTPS")
Rel(api_gateway, iam, "Rutea Auth y valida sesión", "HTTP interna / gRPC (a futuro) ")
Rel(api_gateway, hr, "Rutea operaciones de dominio", "HTTP interna / gRPC (a futuro) ")

Rel(hr, msg_bus, "Publica EmployeeHiredIntegrationEvent", "AMQP")
Rel(iam, msg_bus, "Escucha y publica eventos", "AMQP")
Rel(msg_bus, notifications, "Consume eventos para enviar emails", "AMQP")
@enduml
```
