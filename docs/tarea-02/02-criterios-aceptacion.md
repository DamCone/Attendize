# Requisitos y Criterios de Aceptación
**Proyecto:** Modernización Attendize — TechFest  
**Alumno:** Damián Conejos  
**Módulo:** 2.º DAW — Proyecto Intermodular II  
**Ruta:** `docs/tarea-02/02-criterios-aceptacion.md`  

---

## 1. Tabla de Requisitos del Sistema

| Código | Usuario | Necesidad | Prioridad | Datos Afectados | Origen |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **REQ-01** | Organizador | Ver en una tabla o tarjetas todos los tickets del evento TechFest con su precio, cupo total, vendidas, plazas restantes y estado de venta. | Obligatoria | `tickets` (`id`, `title`, `price`, `quantity_available`, `quantity_sold`, `is_paused`) | Panel actual de Attendize v2.8.0 |
| **REQ-02** | Organizador | Crear un nuevo tipo de entrada o taller indicando nombre, precio, plazas totales y plazas máximas por persona. | Obligatoria | `tickets` (`event_id`, `title`, `price`, `quantity_available`, `max_per_person`) | Necesidad de dar de alta la agenda de TechFest |
| **REQ-03** | Organizador | Comprobar que los campos del formulario de creación y edición contienen valores válidos antes de persistirlos. | Obligatoria | Formulario cliente React y validación backend en Laravel | Prevención de registros inconsistentes |
| **REQ-04** | Organizador | Editar el nombre, precio y plazas disponibles de un ticket existente. | Obligatoria | `tickets` (`title`, `price`, `quantity_available`) | Cambios de planificación en el evento |
| **REQ-05** | Organizador | Impedir que el cupo total de un ticket se reduzca por debajo del número de entradas que ya han sido vendidas. | Obligatoria | `tickets.quantity_available`, `tickets.quantity_sold` | Regla de negocio para evitar overbooking |
| **REQ-06** | Coordinador | Cambiar el estado de un ticket entre "En venta" y "Pausado" mediante un clic y sin recargar la página. | Obligatoria | `tickets.is_paused` | Llenado imprevisto de salas en el congreso |
| **REQ-07** | Organizador | Eliminar un ticket del sistema que se haya creado por error, siempre que nadie haya comprado entradas de ese tipo. | Obligatoria | Registro del ticket en la tabla `tickets` | Limpieza de programación |
| **REQ-08** | Organizador | Bloquear la opción de eliminar un ticket si ya tiene al menos una entrada vendida. | Obligatoria | `tickets.quantity_sold` | Integridad referencial con las tablas `orders` y `attendees` |
| **REQ-09** | Organizador | Acceder a la gestión de tickets únicamente si la sesión está iniciada y el usuario tiene permisos sobre el evento TechFest. | Obligatoria | Sesión de usuario, `events.user_id` / `organisers` | Requisito de seguridad y control de acceso |
| **REQ-10** | Coordinador | Escribir en un campo de texto y filtrar al momento los tickets visibles en pantalla por su título sin pedir datos de nuevo al servidor. | Deseable | Estado local de React (`searchQuery`) | Agilidad visual cuando hay muchos talleres |

---

## 2. Criterios de Aceptación (Dado - Cuando - Entonces)

### REQ-01: Listado de tickets
* **Criterio 1.1 (Caso correcto):**  
  **Dado** que el organizador accede a la sección de tickets del evento TechFest que ya tiene entradas dadas de alta,  
  **cuando** la vista en React termina de cargar,  
  **entonces** se muestra la lista de tickets mostrando para cada uno: su nombre, su precio en euros, las plazas vendidas, el aforo total, el cálculo de plazas restantes (`quantity_available - quantity_sold`) y una etiqueta con su estado (*En venta* o *Pausado*).
* **Criterio 1.2 (Estado vacío):**  
  **Dado** que el evento TechFest no tiene todavía ningún ticket creado,  
  **cuando** el organizador entra a la sección de tickets,  
  **entonces** la pantalla muestra un mensaje avisando de que no hay entradas creadas y un botón para «Crear primer ticket».

### REQ-02 y REQ-03: Creación y validación de tickets
* **Criterio 2.1 (Caso correcto):**  
  **Dado** que el organizador abre el modal de creación de ticket,  
  **cuando** rellena el título con *"Taller de Docker"*, el precio con *0.00*, las plazas con *30*, el máximo por persona con *1* y pulsa «Guardar»,  
  **entonces** Inertia.js envía los datos a Laravel, se crea el registro en la tabla `tickets` de MySQL, se cierra el modal y el nuevo ticket aparece en el listado de la pantalla con un aviso de confirmación.
* **Criterio 3.1 (Datos inválidos en cliente y servidor):**  
  **Dado** que el organizador está en el formulario de creación de ticket,  
  **cuando** deja el campo del título en blanco o introduce un número negativo en las plazas disponibles y pulsa «Guardar»,  
  **entonces** el formulario no se procesa, muestra un mensaje de error debajo de cada campo incorrecto indicando qué corregir y no se inserta ninguna fila en MySQL.

### REQ-04 y REQ-05: Modificación de cupos y salvaguarda de ventas
* **Criterio 4.1 (Caso correcto):**  
  **Dado** un ticket *"Entrada General"* con 100 plazas totales y 15 entradas vendidas,  
  **cuando** el organizador pulsa en editar, cambia las plazas a 120 y pulsa «Actualizar»,  
  **entonces** se guarda el nuevo valor en la base de datos y la interfaz pasa a mostrar 105 plazas restantes de forma inmediata.
* **Criterio 5.1 (Datos inválidos por aforo menor a ventas):**  
  **Dado** un ticket *"Taller Kubernetes"* con 20 plazas totales y 18 vendidas,  
  **cuando** el organizador intenta cambiar el aforo disponible a 15,  
  **entonces** el sistema rechaza la petición con un error de validación indicando: *"El cupo total no puede ser inferior a las 18 entradas que ya se han vendido"* y el valor en MySQL permanece en 20.

### REQ-06: Pausar y reanudar venta
* **Criterio 6.1 (Pausar ticket activo):**  
  **Dado** un ticket que se encuentra en estado «En venta» (`is_paused = 0`),  
  **cuando** el coordinador hace clic sobre el botón «Pausar»,  
  **entonces** Inertia.js envía una petición al backend, en MySQL el campo `is_paused` pasa a valer 1 y el botón de la interfaz cambia inmediatamente a color amarillo con el texto «Reanudar».
* **Criterio 6.2 (Reanudar ticket pausado):**  
  **Dado** un ticket que se encuentra en estado «Pausado» (`is_paused = 1`),  
  **cuando** el usuario hace clic sobre el botón «Reanudar»,  
  **entonces** se actualiza `is_paused` a 0 en MySQL y el botón pasa a mostrar «Pausar» con el estilo visual correspondiente a activo.

### REQ-07 y REQ-08: Eliminación y restricciones de integridad
* **Criterio 7.1 (Eliminación permitida):**  
  **Dado** un ticket que se creó por error y tiene 0 plazas vendidas (`quantity_sold = 0`),  
  **cuando** el organizador pulsa el icono de la papelera y confirma la advertencia en pantalla,  
  **entonces** la petición elimina el registro de la tabla `tickets` y la tarjeta desaparece del listado.
* **Criterio 8.1 (Acción bloqueada):**  
  **Dado** un ticket que tiene 1 o más plazas vendidas,  
  **cuando** el organizador observa las opciones del ticket,  
  **entonces** el botón de eliminar aparece desactivado o no disponible, impidiendo borrar registros que tengan asistentes o pedidos relacionados en la base de datos.

### REQ-09: Seguridad y control de permisos (Acción sin permiso)
* **Criterio 9.1 (Usuario no autenticado):**  
  **Dado** una persona que no ha iniciado sesión en la plataforma,  
  **cuando** intenta abrir directamente la URL de gestión de tickets de TechFest,  
  **entonces** Laravel corta el acceso y la redirige a la pantalla de login con un aviso de que debe autenticarse.
* **Criterio 9.2 (Usuario sin permisos sobre el evento):**  
  **Dado** un usuario que ha iniciado sesión pero que pertenece a otra organización ajena a TechFest,  
  **cuando** intenta enviar una petición para editar o pausar un ticket de TechFest,  
  **entonces** Laravel rechaza la operación con una respuesta HTTP 403 (Prohibido) y los datos en MySQL no se modifican.
