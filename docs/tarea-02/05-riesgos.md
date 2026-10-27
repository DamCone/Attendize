# Gestión de Riesgos del Proyecto
**Proyecto:** Modernización Attendize — TechFest  
**Alumno / Responsable:** Damián Conejos  
**Módulo:** 2.º DAW — Proyecto Intermodular II  
**Ruta:** `docs/tarea-02/05-riesgos.md`  

---

## Matriz de Riesgos Identificados

| ID | Categoría | Descripción del Riesgo | Probabilidad | Impacto | Prevención | Plan de Respuesta (Mitigación) | Responsable |
| :---: | :--- | :--- | :---: | :---: | :--- | :--- | :---: |
| **RSK-01** | Tecnología antigua | Incompatibilidad al integrar librerías modernas de React 18 / Inertia con la base heredada de Attendize v2.8.0 y su entorno de ejecución en Docker. | Media | Alto | Mantener la nueva interfaz aislada en sus propias rutas y componentes sin mezclar scripts antiguos de jQuery con los componentes de React. | Si una librería falla, recurrir a las versiones LTS estables soportadas por la versión de Node y PHP configuradas en el contenedor. | Damián Conejos |
| **RSK-02** | Pérdida de datos | Pérdida accidental de los datos de prueba de TechFest al recrear o reiniciar los contenedores de Docker de MySQL. | Baja | Alto | Mantener siempre configurado el volumen persistente `mysql-data` en `docker-compose.yml` verificado en la Tarea 1. | Disponer de un script SQL con datos semilla (*seeders*) para regenerar el evento TechFest y los tickets de prueba en un solo comando. | Damián Conejos |
| **RSK-03** | Tiempo y organización | Retrasos en el desarrollo debido a la curva de aprendizaje inicial con TypeScript e Inertia.js al no haberse visto en profundidad aún en clase. | Alta | Medio | Acotar el alcance estrictamente a los 5 requisitos obligatorios del panel de tickets sin añadir funcionalidades secundarias complejas. | Dedicar las primeras sesiones a montar un ejemplo mínimo funcional (CRUD básico) antes de maquetar el resto de pantallas. | Damián Conejos |
| **RSK-04** | Seguridad | Acceso no autorizado a la modificación de precios o aforos mediante peticiones directas a los endpoints de Laravel sin pasar por la interfaz. | Media | Alto | Implementar validación en servidor con *Form Requests* y políticas de acceso de Laravel (*Policies*) comprobando la sesión del organizador. | Bloquear con código HTTP 403 cualquier petición que no provenga del usuario propietario del evento TechFest y registrar el intento. | Damián Conejos |
| **RSK-05** | Integridad de datos | Inconsistencias al reducir el cupo de un ticket por debajo del número de entradas que ya han reservado los asistentes. | Media | Alto | Aplicar una regla de validación condicional estricta tanto en TypeScript (cliente) como en la regla del Form Request de Laravel (servidor). | Si la base de datos detecta `quantity_available < quantity_sold`, rechazar la transacción con un error 422 informando del conflicto. | Damián Conejos |
| **RSK-06** | Despliegue local | Fallos en la compilación de assets de Vite / React dentro del contenedor de Docker por problemas de permisos de archivos en Windows (CRLF vs LF). | Media | Medio | Mantener el archivo `.gitattributes` configurado correctamente y mapear los volúmenes de desarrollo excluyendo la carpeta `node_modules`. | Ejecutar la compilación en local o limpiar la caché de Vite y regenerar dependencias con `npm run build` en un entorno limpio. | Damián Conejos |

---

## Resumen del Análisis

* **Riesgo crítico principal:** El mayor desafío es el **RSK-03** (gestión del tiempo y tecnologías nuevas). Para neutralizarlo, la planificación del proyecto se ha dividido en tareas pequeñas con condiciones de cierre muy claras, garantizando que el núcleo obligatorio esté operativo antes de abordar mejoras opcionales.
* **Seguridad y persistencia:** Con el volumen de Docker consolidado desde la Tarea 1 y la doble barrera de validación (TypeScript + Laravel Form Requests), la integridad de MySQL y la protección frente a accesos indebidos quedan completamente cubiertas.
