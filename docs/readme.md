# ERP Core

**Sistema ERP moderno construido con Domain-Driven Design (DDD) + Clean Architecture**

Un núcleo escalable, mantenible y altamente desacoplado para la gestión integral de la empresa, con énfasis en **identidad segura** y **gestión del talento humano**.

## 📦 Módulos Actuales

### 📦 Módulos Principales

Modulos donde se consentra la logica de negocio

| Módulo                                                                    | Responsabilidad Principal                                             | Estado         |
|---------------------------------------------------------------------------|-----------------------------------------------------------------------|----------------|
|[**Identity & Access (IAM)**](./4-modules/identity-and-access/readme.md)  | Autenticación, autorización (RBAC) y gestión de identidades            |🟡 Documentando |
|[**Human Resources (HR)**](./4-modules/human-resources/readme.md)          | Ciclo de vida del empleado, contratos y estructura organizacional     |🟡 Documentando |
|[**Notifications**](./4-modules/notifications/readme.md)                   |Envío confiable de correos, SMS y notificaciones                       |🟡 Documentando |

### 📦 Módulos de soporte

modulos de apoyo que forman parte de la arquitectura

| Módulo                                   | Responsabilidad Principal                                                            |
|------------------------------------------|--------------------------------------------------------------------------------------|
| **API Gateway / BFF** (Supporting Domain)|punto de entrada para que el front se comunique con los distintos modulos             |
|**service broker** (Supporting Domain)    |encargado de la comunicacion entre modulos y emicion de eventos                       |
|**service orquester** (Supporting Domain) |de la ejecucion de procesos especificos para gratantizar el flujo correcto de acciones|
