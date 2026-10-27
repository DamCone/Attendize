# Tarea 2: Propuesta y Planificación de la Modernización
**Proyecto:** Modernización de Attendize v2.8.0  
**Caso de trabajo:** Gestión de eventos TechFest  
**Alumno:** Damián Conejos  
**Módulo:** Proyecto Intermodular II — 2.º DAW (IES Juan de Garay)  
**Rama de trabajo:** `tarea-02-planificacion`  
**Punto de partida:** Etiqueta `tarea-01-entrega`  

---

## 1. Resumen Ejecutivo de la Propuesta

* **Recorrido vertical seleccionado:** **Panel de gestión de entradas y control de aforo de TechFest (Back-office del organizador)**.
* **Problema a resolver:** Sustituir la interfaz antigua de Attendize v2.8.0 (construida con plantillas Blade monolíticas, Bootstrap 3 y modales inestables con jQuery que causan bloqueos y recargas continuas) por una interfaz moderna, ágil y accesible para el personal del evento.
* **Stack tecnológico objetivo:** **Laravel + React + TypeScript + Inertia.js + MySQL**.
* **Resultado funcional:** Los organizadores pueden dar de alta pases y talleres, modificar aforos en tiempo real con salvaguarda contra overbooking y pausar o reanudar ventas con respuesta instantánea y sin recarga de pantalla.

---

## 2. Índice de Documentos Entregables

Toda la documentación técnica de la Tarea 2 se organiza en esta carpeta:

| Archivo | Formato | Descripción del contenido |
| :--- | :---: | :--- |
| [01-propuesta-y-alcance.pdf](./01-propuesta-y-alcance.pdf) | PDF | Justificación del problema en TechFest, limitaciones observadas en Attendize v2.8.0, 2 perfiles de usuario y alcance MoSCoW. |
| [02-criterios-aceptacion.md](./02-criterios-aceptacion.md) | Markdown | Catálogo de 9 requisitos obligatorios y criterios de aceptación comprobables en formato Gherkin (*Dado-Cuando-Entonces*). |
| [03-prototipo.pdf](./03-prototipo.pdf) | PDF | Mapa de navegación, wireframes en Canva (versión escritorio y móvil), modal con errores, estado vacío y accesibilidad por teclado. |
| [04-arquitectura-y-datos.pdf](./04-arquitectura-y-datos.pdf) | PDF | Diagrama de 5 capas (Navegador, React+TS, Inertia, Laravel, MySQL), ciclo de la petición y tratamiento/mapeo de datos relacionales. |
| [05-riesgos.md](./05-riesgos.md) | Markdown | Matriz con 6 riesgos técnicos analizados (probabilidad, impacto, prevención, respuesta y responsable). |
| [06-digitalizacion-sostenibilidad.pdf](./06-digitalizacion-sostenibilidad.pdf) | PDF | 2 indicadores comparables de mejora en el proceso y 2 medidas de sostenibilidad (reducción de tráfico y optimización de consultas SQL). |
| [07-planificacion.md](./07-planificacion.md) | Markdown | Plan de trabajo desglosado por requisito obligatorio, estimaciones (28 horas totales), dependencias y 4 hitos secuenciales. |
| [08-validacion.md](./08-validacion.md) | Markdown | Registro oficial de la validación con la profesora Eva María Gómez Abad (revisión prevista: 28/10/2026). |

---

## 3. Síntesis Técnica

### Arquitectura de 5 Elementos
[Navegador Web] ──> [React + TypeScript] ──> [Inertia.js] ──> [Laravel] ──> [MySQL]
code
Code
1. **Navegador:** Mantiene la sesión y ejecuta la aplicación SPA en memoria sin recargas.
2. **React + TypeScript:** Renderiza componentes accesibles y valida datos tipados en tiempo real.
3. **Inertia.js:** Comunica cliente y servidor transportando deltas JSON sin necesidad de montar una API REST separada.
4. **Laravel:** Aplica seguridad (`EventPolicy`), validaciones mediante *Form Requests* y reglas de negocio.
5. **MySQL:** Almacena de forma transaccional las entidades `events` y `tickets`.

### Planificación e Hitos
* **Hito 1 (H1):** Backend y Control de Acceso (3h).
* **Hito 2 (H2):** Interfaz y Listado Reactivo (5h).
* **Hito 3 (H3):** Formularios y Acciones en Tiempo Real (13h).
* **Hito 4 (H4):** Reglas de Integridad y Validación Final (7h).
* **Total estimado:** 28 horas de desarrollo.

---

## 4. Estado de Validación
* **Estado:** Pendiente de revisión técnica con el profesorado.
* **Fecha fijada:** 28/10/2026.
* **Profesora responsable:** Eva María Gómez Abad
