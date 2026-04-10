An anemic domain model is a software design anti-pattern where domain objects (entities) contain data (properties) but little or no business logic.

El método FEFO (First Expired, First Out - Primero en Caducar, Primero en Salir) es una técnica de gestión de inventarios que prioriza la salida de productos con la fecha de vencimiento más próxima, independientemente de cuándo llegaron al almacén. Es esencial para perecederos (alimentos, fármacos) para evitar pérdidas y garantizar frescura. 


Sugerencia para tu documentación (ADR):

Deberías crear un ADR titulado: "Data Sovereignty: Database per Bounded Context".

- Contexto: Necesitamos escalar IAM y RRHH de forma independiente y garantizar la seguridad de los datos sensibles de nómina.

- Decisión: Cada contexto tendrá su propio esquema de persistencia.

- Consecuencia: Tendremos que gestionar la consistencia eventual y no podremos realizar JOINs entre dominios a nivel de base de datos (se hará composición en el API Gateway o en el Frontend).

Los Globs son patrones de búsqueda (parecidos a las expresiones regulares, pero mucho más simples) que se utilizan para seleccionar grupos de archivos basados en sus nombres o rutas.

