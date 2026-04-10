# Process E2E 01: Onboarding de Nuevo Empleado

## 1. Descripción del Proceso Global

Este proceso de negocio describe el ciclo de vida completo desde que un candidato es contratado por Recursos Humanos hasta que tiene acceso efectivo al sistema ERP y establece su propia contraseña.

## 2. Participantes (Módulos / Bounded Contexts)

* **Módulo HR (Recursos Humanos):** Dueño del alta legal y contractual.
* **Módulo IAM (Identity & Access):** Dueño de las credenciales y el acceso.
* **Módulo Notifications:** Dueño de la entrega del correo electrónico.

## 3. Flujo Coreografiado (Event-Driven)

### Paso 1: Alta Administrativa (Origen de la Verdad)

* **Actor:** Gestor de Recursos Humanos.
* **Módulo Implicado:** HR.
* **Caso de Uso Extraído:** `UC-HR-01: Contratar Empleado`.
* **Acción:** El Gestor registra los datos personales y el contrato en el módulo HR.
* **Resultado:** Se guarda el empleado y se publica el evento de integración `EmployeeHiredIntegrationEvent`.

### Paso 2: Aprovisionamiento de Identidad (Programado y Diferido)

* **Actor:** Sistema (Automático).
* **Módulo Implicado:** IAM.
* **Casos de Uso Extraídos:
  * * `UC-IAM-00: Aprovisionar Cuenta Automáticamente` (Reacción inmediata al evento de RRHH).
  * * `UC-IAM-07: Procesar Activaciones Programadas` (Tarea programada ej. Delayed Exchanges en RabbitMQ o Scheduled Messages en Service Bus).
* **Acción:** IAM escucha el `EmployeeHiredIntegrationEvent`. Si el contrato del empleado empieza en el futuro, IAM crea el `User` mediante el `UC-IAM-00` dejándolo en estado `Scheduled_For_Activation` y entra en espera.
* **Resultado:** El sistema queda programado de forma segura. El día exacto en que inicia el contrato, el `UC-IAM-07` automáticamente cambia el estado a `Pending_Activation` y dispara el evento para enviar el correo de bienvenida.

### Paso 3: Notificación al Usuario

* **Actor:** Sistema (Automático).
* **Módulo Implicado:** Notifications.
* **Caso de Uso Extraído:** `UC-NOT-01: Enviar Email Transaccional`.
* **Acción:** Notifications escucha el `UserActivationRequiredIntegrationEvent`. Construye el email usando la plantilla de "Bienvenida" e inyecta la URL de activación (ej. `https://erp.empresa.com/activate?token=...`).
* **Resultado:** El email se envía al correo personal o corporativo del empleado.

### Paso 4: Activación por parte del Empleado

* **Actor:** Nuevo Empleado.
* **Módulo Implicado:** IAM.
* **Caso de Uso Extraído:** `UC-IAM-02: Activar Cuenta`.
* **Acción:** El empleado hace clic en el enlace, ingresa su nueva contraseña y el token se valida.
* **Resultado:** El estado del usuario en IAM pasa a `Active`. El proceso global finaliza con éxito.
