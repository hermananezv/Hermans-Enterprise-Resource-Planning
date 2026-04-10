# Patron saga

Imagina que tienes una operación que involucra varios servicios independientes (por ejemplo: reservar un vuelo, un hotel y un coche). Si falla uno, no puedes dejar los otros confirmados porque quedaría todo inconsistente.

El patrón Saga soluciona esto dividiendo la transacción larga en una secuencia de pasos pequeños, cada uno con su acción y su acción compensatoria (lo que hay que hacer para deshacerlo si algo falla).

    Si todos los pasos salen bien → misión cumplida.

    Si un paso falla → se ejecutan hacia atrás las acciones compensatorias de los pasos que ya se hicieron.

Recordatorio rápido

Una saga es una secuencia de transacciones locales. Cada paso tiene una acción (hacer algo) y una compensación (deshacer ese algo). Si falla un paso, se ejecutan las compensaciones de los pasos ya completados en orden inverso.

No hay un bloqueo global (como en 2PC). Cada servicio es autónomo y la consistencia es eventual.
2. Dos formas de coordinar una saga
A. Coreografía (Choreography)

    Sin coordinador central. Cada servicio publica eventos y otros servicios reaccionan a ellos.

    Cada servicio sabe qué hacer después de un evento (éxito o fracaso) y qué compensación lanzar.

Ejemplo: Viaje combinado (vuelo, hotel, coche).

    Servicio de vuelos: reserva asiento → publica evento VueloReservado.

    Servicio de hoteles escucha VueloReservado → reserva habitación → publica HotelReservado.

    Servicio de coches escucha HotelReservado → reserva coche → publica CocheReservado.

    Si el servicio de coches falla, publica CocheFallido.

    Servicio de hoteles escucha CocheFallido → ejecuta compensación (cancelar hotel) → publica HotelCancelado.

    Servicio de vuelos escucha HotelCancelado → cancela vuelo.

Ventajas:

    Bajo acoplamiento, descentralizado.

    Fácil de añadir nuevos participantes si solo escuchan eventos.

Desventajas:

    La lógica de la saga se "dispersa" entre muchos servicios.

    Riesgo de ciclos de eventos o dependencias circulares.

    Difícil de monitorear y depurar.

B. Orquestación (Orchestration)

    Un orquestador central (un servicio o un componente) le dice a cada participante qué hacer.

    El orquestador mantiene el estado de la saga y llama a las acciones o compensaciones según el resultado.

Ejemplo: mismo viaje.

    Orquestador → servicio de vuelos: "Reserva vuelo".

    Vuelos responde éxito → orquestador → hoteles: "Reserva hotel".

    Hoteles éxito → orquestador → coches: "Reserva coche".

    Si coches falla → orquestador → hoteles: "Cancela hotel" y luego → vuelos: "Cancela vuelo".

Ventajas:

    Lógica centralizada: fácil de entender, probar y monitorear.

    Menos eventos dispersos, menos riesgo de loops.

    Se puede implementar como una máquina de estados.

Desventajas:

    El orquestador puede ser un punto único de fallo (aunque se puede replicar).

    Mayor acoplamiento (los participantes solo hablan con el orquestador, no entre ellos).

3. Manejo de fallos: dos estrategias
a) Recuperación hacia atrás (backward recovery)

    Cuando falla un paso, se ejecutan las compensaciones de los pasos ya hechos.

    Útil para transacciones que no se pueden reintentar fácilmente (ej. reservar un asiento único).

    Es la estrategia clásica de Saga.

b) Recuperación hacia adelante (forward recovery)

    No se compensa, sino que se reintenta el paso fallido hasta que funcione.

    Requiere que el paso sea idempotente (repetirlo no cause efectos laterales).

    Útil cuando los pasos son reversibles pero es más fácil reintentar que deshacer.

En la práctica, puedes combinar ambas: si un paso falla por un error transitorio, reintentas (forward); si falla por un error lógico (ej. stock insuficiente), haces backward recovery.
4. Idempotencia: clave para que las sagas funcionen

Los mensajes pueden duplicarse (por reinicios, timeouts, retries). Cada acción y compensación debe ser idempotente.

Ejemplo idempotente:

    "Cancelar reserva con ID = X" se puede ejecutar varias veces; la primera cancela, las siguientes no hacen nada (o devuelven éxito).

    Para reservar: si recibes la misma petición dos veces, puedes detectarlo con un identificador único (idempotency key) y devolver el mismo resultado sin duplicar la reserva.

Sin idempotencia, una saga podría cancelar dos veces un pago o reservar dos veces un hotel.
5. Ejemplo concreto y más realista: compra online

Imagina un e-commerce:

    Pedido (crear pedido en estado PENDIENTE)

    Pago (cobrar tarjeta)

    Inventario (reservar stock)

    Envío (crear etiqueta de envío)

Compensaciones:

    Si falla Pago → cancelar pedido (no hace falta más).

    Si falla Inventario → reversar el pago, cancelar pedido.

    Si falla Envío → liberar stock, reversar pago, cancelar pedido.

Orquestación con estado explícito:
El orquestador guarda en su base de datos: saga_id, estado (INICIADA, PAGO_OK, INVENTARIO_OK, etc.).
Al recibir un fallo, consulta el estado y decide qué compensaciones ejecutar. Si el orquestador muere, al recuperarse puede continuar la saga desde donde quedó.
6. Ventajas y desventajas del patrón Saga
Ventajas Desventajas
Escalable (no bloquea recursos) Consistencia eventual: hay ventanas de inconsistencia
Tolerante a fallos parciales Complejidad de diseñar compensaciones
Ideal para microservicios Puede haber efectos colaterales si una compensación falla
No requiere un gestor de transacciones distribuido Difícil de depurar, sobre todo en coreografía
7. ¿Qué pasa si una compensación falla?

Es el problema más delicado. Estrategias:

    Reintentar la compensación (con backoff y límite de reintentos).

    Registrar en una tabla de "saga fallida" y alertar a un operador humano.

    Diseñar compensaciones que siempre puedan ejecutarse (ej. cancelar una reserva es siempre posible).

    Usar dead letter queue y procesos de reconciliación periódica.

En sistemas críticos, se suele combinar con logs inmutables de la saga (event sourcing) para poder auditar y reparar manualmente.
8. ¿Siempre necesitas Saga? Alternativas ya mencionadas, pero ahora más claras
Alternativa Cuándo usarla
2PC (Two-Phase Commit) Consistencia fuerte, pocos participantes, corta duración, y puedes tolerar bloqueos.
Eventual consistency con reintentos Si nunca necesitas deshacer, solo asegurar que al final se haga todo.
Transacciones locales + colas Si cada paso puede fallar pero se puede reintentar sin límite (ej. enviar emails).
Reserva de recursos (pattern "Try-Cancel/Confirm") Cuando necesitas asegurar disponibilidad antes de comprometer (ej. asientos de avión). Esto es similar a TCC (Try-Confirm-Cancel), que es una variante de Saga más rígida.
