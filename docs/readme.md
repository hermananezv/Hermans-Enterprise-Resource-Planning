# ERP Core

**Sistema ERP moderno construido con Domain-Driven Design (DDD) + Clean Architecture**

Un núcleo escalable, mantenible y altamente desacoplado para la gestión integral de la empresa, con énfasis en **identidad segura** y **gestión del talento humano**.

## 📦 Módulos Actuales

| Módulo                    | Responsabilidad Principal                              | Estado     |
|---------------------------|--------------------------------------------------------|------------|
| [**Identity & Access (IAM)**](./3-modules/identity-and-access/readme.md)  | Autenticación, autorización (RBAC) y gestión de identidades | ✅ Maduro |
|[**Human Resources (HR)**](./3-modules/human-resources/readme.md)| Ciclo de vida del empleado, contratos y estructura organizacional | ✅ Avanzado|
|[**Notifications** ](./3-modules/notifications/readme.md)|Envío confiable de correos, SMS y notificaciones | 🟡 En progreso |

Actualmente, el proyecto se enfoca en dos dominios fundamentales:

    Identity & Access Management (IAM): Seguridad, autenticación y autorización.

    Human Resources (HR): Gestión del ciclo de vida del empleado y estructura organizacional.

## Arquitectura y Patrones

Este proyecto utiliza DDD para separar las preocupaciones técnicas de las reglas de negocio:

* Bounded Contexts: Cada módulo es un contexto independiente con su propio modelo de dominio y base de datos (o esquema).

* Tactical Design: Uso de Entidades, Objetos de Valor (Value Objects), Agregados y Repositorios.

* Comunicación Asíncrona: Integración entre módulos basada en Eventos de Dominio.
   