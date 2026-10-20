# Tarea 1: Despliegue local reproducible de Attendize

- **Alumno:** Damián Conejos
- **Curso:** 2.º DAW - Proyecto Intermodular II
- **Aplicación:** Attendize v2.8.0 ([Repositorio Oficial](https://github.com/Attendize/Attendize))
- **Commit base:** `47997d2` (tag `v2.8.0`)

### Pasos para desplegar desde cero

1. Clonar el repositorio y situarse en la versión entregada:
   ```bash
   git clone https://github.com/DamCone/Attendize.git
   cd Attendize
   git checkout tarea-01-entrega

2. Generar el archivo de configuración a partir de la plantilla:
   cp .env.example .env

3. Construir las imágenes Docker:
   docker build --tag attendize_base --target base .
   docker build --tag attendize_worker --target worker --cache-from attendize_base:latest .
   docker build --tag attendize_web --target web --cache-from attendize_worker:latest .

4. Instalar librerías PHP y preparar la clave de la aplicación:
   # Iniciar contenedores
   docker compose up -d

   # Instalar dependencias con Composer y generar clave de Laravel
   docker compose exec web composer install
   docker compose exec web php artisan key:generate --force

   # Reiniciar contenedores para cargar la clave en el entorno
   docker compose down
   docker compose up -d

   # Ejecutar migraciones de la base de datos
   docker compose exec web sh -c "wait-for-it db:3306 -t 180 && php artisan migrate"

5. Acceder a la aplicación:
   Abrir en el navegador: https://localhost:8081/

### Problemas encontrados y soluciones aplicadas

- **Error 404 al construir la imagen base (`apt-get update`):**
  - **Problema:** La imagen base utiliza Debian 10 Buster, una versión obsoleta cuyos repositorios oficiales están cerrados y con firmas caducadas.
  - **Solución:** En el `Dockerfile`, se cambiaron las URLs de los repositorios a `archive.debian.org` y se permitió instalar con `--allow-unauthenticated`.

- **Error `/bin/sh: 1: ./scripts/setup: not found` al construir:**
  - **Problema:** En Windows, Git descarga los archivos con saltos de línea CRLF (`\r\n`), por lo que el intérprete de Linux dentro del contenedor no reconocía la cabecera del script.
  - **Solución:** En el `Dockerfile`, se añadió `sed -i 's/\r$//'` antes de lanzar el script para eliminar los caracteres de retorno de carro de Windows.

- **Error de PHP por falta de `vendor/autoload.php`:**
  - **Problema:** Al montar el volumen local en el contenedor, se ocultaba la carpeta de dependencias que no existía en el equipo anfitrión.
  - **Solución:** Se ejecutó `docker compose exec web composer install` para descargar las dependencias de Laravel y crear el autoloader.

- **Error en el navegador: `No application encryption key has been specified`:**
  - **Problema:** Docker Compose solo carga las variables de `.env` al crear el contenedor; un simple reinicio no leía la nueva clave y además se habían concatenado claves repetidas en el archivo.
  - **Solución:** Se corrigió la línea `APP_KEY` en el `.env`, se generó con `php artisan key:generate --force` y se aplicó recreando los contenedores con `docker compose down` y `docker compose up -d`.