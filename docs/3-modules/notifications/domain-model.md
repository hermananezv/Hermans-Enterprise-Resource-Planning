# Modelo de Dominio: Notifications

**Bounded Context:** Gestión de Comunicaciones Salientes  
**Responsabilidad Principal:** Enviar, gestionar y rastrear notificaciones de forma confiable y desacoplada.  
**Versión:** 1.0.0

## 📖 1. Lenguaje Ubicuo

| Término           | Definición                                                      | Ejemplo                     |
|-------------------|-----------------------------------------------------------------|-----------------------------|
| Notification      | Registro de un intento de envío                                 | Welcome email               |
| Template          | Plantilla reusable con variables                                | `welcome_email`             |
| Channel           | Medio de entrega                                                | EMAIL, SMS                  |
| Provider          | Servicio externo                                                | SendGrid, AWS SES           |
| DeliveryStatus    | Estado del envío                                                | PENDING, SENT, FAILED       |

## 🏗️ 2. Diseño Táctico (Tactical DDD)

### Aggregate Roots
- **`Notification`** – Entidad principal que audita cada envío.

### Value Objects
- **`Channel`** – EMAIL | SMS | PUSH
- **`Recipient`** – Dirección validada (email, teléfono, etc.)
- **`TemplateData`** – JSON con variables para renderizar

### Domain Events
- `NotificationSent`
- `NotificationFailed`

## ⚙️ 3. Reglas de Negocio (Invariantes)

1. Toda `Notification` debe estar asociada a una `Template` existente.
2. Máximo 3 reintentos automáticos por fallo técnico.
3. No se permite reenviar notificaciones ya entregadas exitosamente.
4. Se debe registrar siempre el resultado para trazabilidad y auditoría.

## 🔗 Integración con Otros Contextos
- **Suscrive a:** `UserCreatedIntegrationEvent`, `EmployeeOnboarded`, etc.
- **Publica:** `NotificationSent`, `NotificationFailed`

[back](./readme.md)