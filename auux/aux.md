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

Atributo|Contexto: Identity-Access|Contexto: Human-Resources|
|--------|---------|---------|
Responsabilidad|Autenticación, Seguridad, Sesiones.|Contratos, Nómina, Desempeño.
Entidad Principal|User|Employee
Atributos Clave|Username, HashedPassword, Roles.|Salary, HireDate, Department.
Identificador|account_id (UUID)|employee_number (Legajo)
Lenguaje Ubicuo|Login, Token, Grant, Claim.|Onboarding, Vacaciones, Ascenso.

---------------

# Notifications

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

## Módulos Actuales

1. Identity & Access (IAM)

Encargado de la identidad técnica y el control de acceso.

* Entidades Clave: User, Role, Permission.

* Responsabilidades: Autenticación (JWT), Gestión de RBAC (Role-Based Access Control) y Auditoría de accesos.

1. Human Resources (HR)

Encargado del empleado como entidad de negocio y su relación laboral.

* Entidades Clave: Employee, Contract, Department.

* Responsabilidades: Onboarding de personal, gestión de contratos, organigrama y ausencias.

🗺️ Mapa de Contextos (Context Map)

Para mantener la integridad del sistema, los módulos se comunican mediante eventos.

Ejemplo de flujo de integración:

* HR registra un nuevo Employee.

* Se dispara un evento de dominio EmployeeOnboarded.

* IAM reacciona a este evento creando un User con los permisos base.

```mermaid
flowchart LR
    HR[Human Resources] -->|EmployeeOnboarded| IAM[Identity & Access]
    IAM -->|UserCreatedIntegrationEvent| Notifications[Notifications]
    HR -->|ContractCreated<br>AbsenceRegistered| Notifications
    IAM -->|UserAccountLocked| Notifications
```

Flujo típico de onboarding:

HR registra un nuevo Employee
Se publica EmployeeOnboarded
IAM crea el User y envía UserCreatedIntegrationEvent
Notifications envía el correo de bienvenida con token de activación

# Modelo de Dominio: Identity & Access (IAM)

**Bounded Context:** Gestión de Identidad y Accesos.
**Responsabilidad:** Proteger el ecosistema del ERP, garantizando que solo los usuarios autenticados y autorizados puedan interactuar con los recursos del sistema.

---

## 📖 1. Lenguaje Ubicuo (Glosario)

Términos estrictos que todo el equipo debe usar al referirse a este módulo:

* **User (Usuario):** Un actor técnico (persona o sistema) que tiene credenciales para acceder al ERP. *Nota: No confundir con "Empleado".*
* **Role (Rol):** Una agrupación lógica de permisos (Ej. `Admin`, `HR_Manager`, `Viewer`).
* **Permission / Claim (Permiso):** El derecho específico para ejecutar una acción (Ej. `employees:write`).
* **Credentials (Credenciales):** El conjunto de datos (Email y Contraseña) que un Usuario presenta para demostrar su identidad.
* **Token:** Artefacto de seguridad (JWT) que representa una sesión activa y contiene las Claims del usuario.

---

## 🏗️ 2. Diseño Táctico (Tactical DDD)

### Agregados y Raíz de Agregado (Aggregate Root)

* **`User` (Aggregate Root):** Es la entidad principal. Toda modificación sobre sus roles o credenciales debe pasar por esta entidad para garantizar la consistencia.

### Entidades (Entities)

Tienen identidad única y su estado puede cambiar con el tiempo.

* **`Role`:** * Identificador: `Role_ID`
  * Atributos: `Name`, `Description`, `Permissions` (Lista).

### Objetos de Valor (Value Objects)

No tienen identidad propia, son inmutables y se definen por sus atributos. Si un atributo cambia, es un objeto completamente nuevo.

* **`EmailAddress`:** Encapsula la validación del formato del correo electrónico. No es solo un `string`.
* **`PasswordHash`:** Representa la contraseña ya cifrada. Nunca debe almacenar la contraseña en texto plano.
* **`TokenValue`:** Encapsula la lógica de los tokens JWT y su fecha de expiración.

---

## ⚙️ 3. Reglas de Negocio (Invariantes del Dominio)

Las reglas que el Agregado `User` debe proteger en todo momento:

1. Un `User` no puede existir sin un `EmailAddress` válido y único.
2. Un `User` recién creado debe estar en estado "Pendiente de Activación" hasta que verifique su correo.
3. Un `User` debe tener al menos un `Role` asignado para poder generar un `Token` válido.
4. Las contraseñas (`PasswordHash`) deben cumplir con políticas de complejidad antes de ser aceptadas.

-----------

Resumen de los beneficios obtenidos con estos cambios en tu documentación:

    No perderás eventos: Gracias al Transactional Outbox.

    No enviarás correos dobles: Gracias a la Idempotencia.

    No saturarás hilos en caídas externas: Gracias al Circuit Breaker.

    Tus HR Managers no chocarán al editar: Gracias al Optimistic Locking.

    Tu base de datos escalará mejor: Gracias a CQRS Ligero.

----------------------

En resumen

    Programado: Está previsto que ocurra en un momento específico (normalmente en directo o en ese instante exacto).

    Programado y Diferido: Está previsto que ocurra en un momento específico, pero el contenido se grabó o procesará antes y se emitirá más tarde (no es en directo).

Explicación detallada
Aspecto Programado Programado y Diferido
Significado literal "Puesto en programa" (agendado) "Puesto en programa y aplazado/retrasado"
¿Es en directo? Generalmente sí, ocurre en ese momento. No. Se graba o procesa antes y se emite después.
Relación tiempo-real El evento y su emisión son simultáneos. El evento ocurrió antes que su emisión.
Ejemplo común "El noticiero de las 9 PM está programado en directo". "El concierto está programado para las 10 PM, pero será emitido en diferido (se grabó a las 8 PM)".
Ejemplos para entenderlo mejor

1. En Televisión / Streaming

    Programado: Un partido de fútbol. El canal anuncia: "El partido está programado para las 5:00 PM". Lo emiten tal cual ocurre (en vivo).

    Programado y Diferido: La misma cadena dice: "El partido está programado y diferido para las 9:00 PM". Eso significa que el partido ya ocurrió (quizás a las 5:00 PM), pero lo van a pasar más tarde, quizás editado o sin comerciales.

2. En Informática / Tareas Automatizadas

    Programado: Una tarea (ej: hacer una copia de seguridad) configurada para ejecutarse ahora a las 3:00 AM exactamente. Cuando llegan las 3:00 AM, la tarea se ejecuta.

    Programado y Diferido: La tarea debía ejecutarse a las 3:00 AM, pero el sistema estaba ocupado. La "programa" para ejecutarse más tarde (ej: a las 3:15 AM). También puede significar que la tarea se ejecutará pero con un retraso deliberado.

3. En Eventos / Reuniones

    Programado: La reunión es a las 10:00 AM. Todos se conectan a las 10:00 AM y ocurre en ese momento.

    Programado y Diferido: La reunión está agendada para las 10:00 AM, pero alguien avisa: "La veremos en diferido", es decir, grabarán la reunión de las 10:00 AM y los ausentes la verán más tarde.

Conclusión clave

    Usa "Programado" cuando algo va a pasar en el momento previsto (en vivo o ejecución inmediata).

    Usa "Programado y Diferido" cuando algo está agendado pero su contenido no es en tiempo real (es una repetición, una grabación o una ejecución con retraso).

En la práctica, si ves "Diferido" en la descripción, significa "No es en directo, lo verás/ejecutarás más tarde de cuando realmente ocurrió".


----------------------------



 Security warning — please read.                                        │
│                                                                         │
│  OpenClaw is a hobby project and still in beta. Expect sharp edges.     │
│  By default, OpenClaw is a personal agent: one trusted operator         │
│  boundary.                                                              │
│  This bot can read files and run actions if tools are enabled.          │
│  A bad prompt can trick it into doing unsafe things.                    │
│                                                                         │
│  OpenClaw is not a hostile multi-tenant boundary by default.            │
│  If multiple users can message one tool-enabled agent, they share that  │
│  delegated tool authority.                                              │
│                                                                         │
│  If you’re not comfortable with security hardening and access control,  │
│  don’t run OpenClaw.                                                    │
│  Ask someone experienced to help before enabling tools or exposing it   │
│  to the internet.                                                       │
│                                                                         │
│  Recommended baseline:                                                  │
│  - Pairing/allowlists + mention gating.                                 │
│  - Multi-user/shared inbox: split trust boundaries (separate            │
│    gateway/credentials, ideally separate OS users/hosts).               │
│  - Sandbox + least-privilege tools.                                     │
│  - Shared inboxes: isolate DM sessions (`session.dmScope:               │
│    per-channel-peer`) and keep tool access minimal.                     │
│  - Keep secrets out of the agent’s reachable filesystem.                │
│  - Use the strongest available model for any bot with tools or          │
│    untrusted inboxes.                                                   │
│                                                                         │
│  Run regularly:                                                         │
│  openclaw security audit --deep                                         │
│  openclaw security audit --fix                                          │
│                                                                         │
│  Must read: https://docs.openclaw.ai/gateway/security       