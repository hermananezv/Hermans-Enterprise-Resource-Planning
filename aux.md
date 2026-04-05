| Término| Definición| Ejemplo|
|--------|---------|---------|
| .......| ........| ........|
|Employee|Persona con relación laboral formal|Juan Pérez (Legajo 45823)|

Característica|Identity Access (IAM)|Human Resources (HR)
|--------|---------|---------|
Identificador|User_ID (UUID/String)|Employee_ID (Legajo/ID Interno)
Dato clave|Password / Email de login|Salario / Fecha de nacimiento
Cambios|Cambia poco (seguridad)|"Cambia seguido (ascensos, sueldos)"
Frecuencia|Se consulta en cada petición al API|Se consulta para procesos de negocio


Atributo|Contexto: Identity-Access|Contexto: Human-Resources
|--------|---------|---------|
Responsabilidad|Autenticación, Seguridad, Sesiones.|Contratos, Nómina, Desempeño.
Entidad Principal|User|Employee
Atributos Clave|Username, HashedPassword, Roles.|Salary, HireDate, Department.
Identificador|account_id (UUID)|employee_number (Legajo)
Lenguaje Ubicuo|Login, Token, Grant, Claim.|Onboarding, Vacaciones, Ascenso.







---------------
#  Notifications

**Bounded Context:** Gestión de comunicaciones salientes.

---

## 📖 1. Lenguaje Ubicuo
* **Notification (Notificación):** La unidad de mensaje enviada (un email, un SMS).
* **Template (Plantilla):** El cuerpo predefinido del mensaje con variables dinámicas (ej: "Hola {{name}}").
* **Recipient (Destinatario):** El contacto al que se envía el mensaje (Email, ID de usuario).
* **Provider (Proveedor):** El servicio externo que entrega el mensaje (SendGrid, AWS SES, Twilio).

## 🏗️ 2. Diseño Táctico
* **`Notification` (Aggregate Root):**
  * ID, Recipient, TemplateID, Data (JSON con las variables), Status (Pending, Sent, Failed).
* **`Channel` (Value Object):** Define si es EMAIL, SMS o PUSH.

## ⚙️ 3. Reglas de Negocio
1. Toda notificación debe estar asociada a una `Template` existente.
2. Si un envío falla por culpa del proveedor, el sistema debe reintentar hasta 3 veces (Retry Policy).
3. Se debe guardar un log de "Visto/Abierto" si el canal lo permite.
[back](./readme.md)
