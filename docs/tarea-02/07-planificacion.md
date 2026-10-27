# Plan de Trabajo y Estimación de Tareas
**Proyecto:** Modernización Attendize — TechFest  
**Alumno / Responsable:** Damián Conejos  
**Módulo:** 2.º DAW — Proyecto Intermodular II  
**Ruta:** `docs/tarea-02/07-planificacion.md`  

---

## 1. Hitos del Proyecto

* **Hito 1 (H1) — Backend y Control de Acceso:** Preparación de rutas con Inertia, políticas de autorización de Laravel y consultas optimizadas a MySQL.
* **Hito 2 (H2) — Interfaz y Listado Reactivo:** Componentes de React en TypeScript para el listado de tickets, métricas de aforo y estado vacío.
* **Hito 3 (H3) — Formularios y Acciones en Tiempo Real:** Modal de creación/edición, validación de datos en cliente/servidor y botón toggle para pausar o reanudar.
* **Hito 4 (H4) — Reglas de Integridad y Validación Final:** Salvaguarda para impedir aforos inferiores a ventas, bloqueo de borrado de tickets vendidos y pruebas de aceptación.

---

## 2. Tabla de Planificación de Requisitos Obligatorios

| Código | Tarea a realizar | Hito | Dependencia | Estimación | Orden | Responsable | Estado | Condición de cierre (Criterio de éxito) |
| :---: | :--- | :---: | :---: | :---: | :---: | :---: | :---: | :--- |
| **REQ-09** | Configurar middleware de autenticación y política de acceso (*Policy*) en Laravel para proteger las rutas del evento TechFest. | H1 | Ninguna | 3 horas | 1 | Damián Conejos | Pendiente | Usuarios anónimos son redirigidos a login y usuarios sin permisos sobre el evento reciben un error HTTP 403. |
| **REQ-01** | Crear el controlador en Laravel que obtenga los tickets del evento en MySQL y la vista principal en React (`Tickets/Index.tsx`) con sus tarjetas y estado vacío. | H2 | REQ-09 | 5 horas | 2 | Damián Conejos | Pendiente | La pantalla muestra todos los tickets con sus datos (título, precio, aforo, vendidas, restantes) o el estado vacío si no hay registros. |
| **REQ-03** | Diseñar los tipos en TypeScript (`Ticket`, `TicketFormData`) y las reglas de validación en el cliente y en el *Form Request* de Laravel. | H3 | REQ-01 | 3 horas | 3 | Damián Conejos | Pendiente | Los campos vacíos o con números negativos muestran avisos en rojo y el formulario bloquea el botón de envío. |
| **REQ-02** | Desarrollar el componente modal en React para dar de alta un ticket y conectar el envío mediante `useForm` de Inertia.js hacia Laravel y MySQL. | H3 | REQ-03 | 4 horas | 4 | Damián Conejos | Pendiente | Al rellenar los datos y guardar, se inserta la fila en la tabla `tickets` de MySQL y la nueva entrada aparece en pantalla sin recargar. |
| **REQ-06** | Implementar la acción de Pausar / Reanudar (*toggle*) con una petición PATCH vía Inertia.js. | H3 | REQ-01 | 2 horas | 5 | Damián Conejos | Pendiente | Al hacer clic en el botón, el campo `is_paused` cambia en MySQL y el botón actualiza su color y texto al instante. |
| **REQ-04** | Desarrollar la funcionalidad de edición de nombre, precio y plazas disponibles de un ticket existente. | H3 | REQ-02 | 4 horas | 6 | Damián Conejos | Pendiente | Al abrir el modal de edición sobre un ticket, se precargan sus datos y al guardar se actualiza el registro en la base de datos. |
| **REQ-05** | Implementar la regla de salvaguarda de aforo en la edición (backend y frontend) para que el aforo nunca sea menor a `quantity_sold`. | H4 | REQ-04 | 3 horas | 7 | Damián Conejos | Pendiente | Si se intenta poner un cupo inferior a las entradas vendidas, el sistema rechaza la petición con código 422 y muestra el mensaje de error. |
| **REQ-07** | Añadir botón de eliminación para tickets sin ventas previas (`quantity_sold == 0`) con modal de confirmación. | H4 | REQ-01 | 2 horas | 8 | Damián Conejos | Pendiente | El ticket se borra de MySQL tras confirmarlo el usuario y desaparece del listado con una notificación de éxito. |
| **REQ-08** | Aplicar el bloqueo de eliminación si el ticket tiene al menos una entrada vendida (`quantity_sold > 0`). | H4 | REQ-07 | 2 horas | 9 | Damián Conejos | Pendiente | El botón de eliminar aparece deshabilitado y el backend rechaza cualquier petición directa de borrado sobre ese registro. |

---

## 3. Resumen de Tiempos y Recursos

* **Horas totales estimadas:** 28 horas de desarrollo y pruebas.
* **Secuencia de trabajo:** Se construyen primero los cimientos de seguridad y datos en Laravel (H1), luego la estructura visual en React (H2), a continuación los formularios reactivos (H3) y por último el blindaje de integridad y borrado (H4).
