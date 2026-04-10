El término CQRS significa Command Query Responsibility Segregation (Segregación de Responsabilidad de Consultas y Comandos). Cuando hablamos de CQRS "Ligero" (o Lightweight CQRS), nos referimos a aplicar la idea principal sin complicarnos la vida con arquitecturas ultra complejas.

Aquí tienes la explicación simple:
El Concepto: Dividir el cerebro en dos

Normalmente, usamos el mismo modelo (las mismas clases o funciones) para escribir datos y para leer datos. El CQRS dice: "Separemos estas dos tareas porque son muy diferentes".

    Comandos (Commands): Son acciones que cambian el estado (Crear, Actualizar, Borrar). Son como las órdenes de un jefe: "Registra este usuario".

    Consultas (Queries): Son acciones que leen datos. Son como preguntas: "¿Cuántos usuarios hay?" o "¿Cuál es el perfil de Juan?".

¿Por qué hacerlo "Ligero"?

En un CQRS completo o pesado, a veces tienes bases de datos distintas para leer y escribir. En el CQRS Ligero, mantenemos la misma base de datos, pero separamos el código en dos caminos distintos.
1. El camino de la Escritura (Comandos)

Aquí el código es estricto. Se asegura de que las reglas de negocio se cumplan. No le importa si la respuesta es bonita, solo le importa que los datos se guarden correctamente y de forma segura.

    Ejemplo: El código para "Realizar Pedido" valida el stock, el saldo del cliente y guarda en 5 tablas diferentes.

2. El camino de la Lectura (Consultas)

Aquí el código es relajado y rápido. No hay reglas de negocio que validar, solo queremos traer los datos lo más rápido posible. A menudo se usan objetos simples (DTOs) que ya vienen con el formato exacto que necesita la pantalla.

    Ejemplo: En lugar de procesar lógica compleja, lanzas una consulta SQL que te devuelve exactamente el nombre y la fecha para mostrar en una lista.

Las 3 Reglas de Oro del CQRS Ligero

    Un método o es un comando o es una consulta: Un comando nunca debe devolver datos (como mucho un ID o un éxito/error). Una consulta nunca debe cambiar nada en la base de datos.

    Modelos diferentes: Usas una clase para la lógica de negocio (Comando) y una estructura simple de datos para lo que muestras en pantalla (Consulta).

    Simplicidad: No necesitas herramientas raras ni servidores extra. Con separar tus funciones en "Carpeta de Comandos" y "Carpeta de Consultas" ya estás haciendo CQRS Ligero.

¿Para qué sirve?

    Claridad: El código de lectura no ensucia la lógica de negocio.

    Rendimiento: Puedes optimizar las consultas SQL para que vuelen, sin miedo a romper la lógica de grabación.

    Mantenimiento: Si la pantalla cambia y necesita un dato extra, solo tocas la parte de "Consultas".

En resumen: Es como una cocina donde el Chef (Comando) se encarga de que la receta sea perfecta y el Camarero (Consulta) se encarga de llevar el plato rápido a la mesa. Usan la misma cocina, pero tienen trabajos totalmente distintos.

¿Te suena a algo que podrías aplicar en tu código actual o te parece que añade demasiada estructura?