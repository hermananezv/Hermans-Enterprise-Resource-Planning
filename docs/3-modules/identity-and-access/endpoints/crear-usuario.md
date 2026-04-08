# Crear Usuario (Invitación Administrativa)
POST /users
Descripción: Registra un nuevo usuario y envía evento de integración.
Request Body
JSON{
  "email": "nuevo.empleado@tu-erp.com",
  "firstName": "Juan",
  "lastName": "Pérez",
  "roles": ["USER", "HR_VIEWER"]
}
Response 201 Created
JSON{
  "userId": "usr_987654321",
  "email": "nuevo.empleado@tu-erp.com",
  "status": "PendingActivation",
  "createdAt": "2026-04-08T12:03:00Z"
}
Eventos emitidos: UserCreatedIntegrationEvent

[back](../api-spec.md)