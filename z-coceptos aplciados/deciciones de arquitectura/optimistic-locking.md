# Optimistic Locking.

Imagina que tú y un amigo están editando el mismo documento de Google al mismo tiempo. El Optimistic Locking (Bloqueo Optimista) es la estrategia que dice: "Voy a asumir que nadie más va a tocar esto mientras yo lo edito, pero voy a revisar antes de guardar".

Aquí tienes la explicación simple:
El Problema: El choque de ediciones

Imagina que hay un artículo en una base de datos que cuesta 100€.

    Usuario A abre la página para editar el precio a 120€.

    Usuario B abre la misma página (mientras A sigue escribiendo) y decide bajar el precio a 80€.

    Usuario A guarda. El precio ahora es 120€.

    Usuario B guarda. El precio ahora es 80€.

¿El resultado? El cambio del Usuario A se borró por completo y nadie se dio cuenta. Esto se llama "pérdida de actualización".
La Solución: El número de versión

En el Bloqueo Optimista, no "bloqueamos" la fila para que nadie más la lea (eso sería Bloqueo Pesimista). En su lugar, añadimos una columna especial a la tabla, normalmente llamada version.
Cómo funciona el proceso:

    Lectura: Cuando el Usuario A lee el producto, ve que el precio es 100€ y la versión es 1.

    Preparación: El Usuario A cambia el precio a 120€ en su pantalla.

    Intento de Guardado: Al guardar, el sistema hace una pregunta: "Actualiza el precio a 120€ y la versión a 2, PERO SOLO SI la versión actual en la base de datos sigue siendo la 1".

    Validación:

        Si nadie lo tocó: La versión sigue siendo 1. El cambio se guarda y la versión sube a 2.

        Si alguien (Usuario B) lo cambió antes: La versión en la base de datos ya será 2. La condición falla y el sistema le dice al Usuario A: "¡Error! Alguien más cambió esto mientras tú editabas. Por favor, refresca la página".

¿Por qué se llama "Optimista"?

Se llama así porque el sistema es optimista: asume que los choques entre usuarios son raros. En lugar de hacer que todos esperen (poniendo "candados"), deja que todos trabajen libremente y solo actúa si detecta un conflicto al final.
Ventajas:

    Rapidez: No hay esperas ni bloqueos lentos en la base de datos.

    Escalabilidad: Ideal para aplicaciones web con miles de usuarios donde es poco probable que dos personas editen exactamente lo mismo al mismo segundo.

En resumen: Es como decir: "Edita tranquilo, pero si cuando vayas a guardar alguien se te adelantó, te avisaré para que no rompas nada".

¿Te gustaría saber qué pasa cuando ocurre ese error de versión o prefieres que veamos otro concepto?