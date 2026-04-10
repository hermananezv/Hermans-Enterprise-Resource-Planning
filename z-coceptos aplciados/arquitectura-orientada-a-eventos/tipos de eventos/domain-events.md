
# Eventos de Dominio (Domain Events)

Estos son los más importantes para el negocio. No hablan de "cambios en la base de datos", sino de cosas que tienen significado para la empresa.

- Qué contiene: Información sobre un cambio de estado importante en el proceso de negocio.

- Ejemplo: En lugar de UsuarioActualizado, tendríamos algo como DireccionDeEnvioCambiada o SuscripciónPremiumExpirada.

- Lo bueno: Ayudan a que los programadores y los dueños del negocio hablen el mismo idioma.

- Lo malo: Requieren pensar mucho más en la lógica antes de programar.