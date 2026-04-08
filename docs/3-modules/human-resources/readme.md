# Human Resources (HR)

**Bounded Context:** Gestión del Talento Humano  
**Responsabilidad Principal:** Administrar el ciclo de vida laboral completo del empleado y la estructura organizacional.  
**Versión:** 1.0.0

## 🎯 Objetivo

Gestionar onboarding, contratos, compensaciones, organigrama y offboarding de los empleados de forma centralizada y conforme a la legislación laboral.

## 📋 Entidades Principales

- **`Employee`** — Entidad central (persona con relación laboral).
- **`Contract`** — Acuerdo laboral (puede haber histórico).
- **`Department`** — Unidad organizativa.
- **`Position`** — Puesto de trabajo.

## 🔄 Responsabilidades Clave

- Onboarding y expediente digital
- Gestión de contratos y salarios
- Estructura organizacional y jerarquía
- Seguimiento de ausencias y vacaciones

## 🔗 Integración con Otros Contextos

- **Publica:** `EmployeeOnboarded`, `ContractCreated`, etc.
- **Suscrive a:** Eventos de IAM (ej. User status changes)

## 📍 Enlaces Importantes

- [Domain Model](./domain-model.md)
- [Requirements](./requirements.md)
- [API Specification](./api-spec.md)
- [Use Cases](./use-cases/)

[back](../../readme.md)