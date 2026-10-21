# Tarea 1: Despliegue local reproducible de Attendize

- **Alumno:** Damián Conejos
- **Curso:** 2.º DAW - Proyecto Intermodular II
- **Aplicación:** Attendize v2.8.0 ([Repositorio Oficial](https://github.com/Attendize/Attendize))
- **Commit base:** `47997d2` (tag `v2.8.0`)
- **Acceso web:** https://localhost:8081/

---

## 1. Servicios y puertos del entorno

- **web (`attendize_web`):** Nginx y PHP 7.4 (puertos `8080` HTTP y `8081` HTTPS).
- **worker (`attendize_worker`):** Ejecución de colas en segundo plano.
- **db (`mysql:5.7.23`):** Base de datos MySQL (puerto `3306`, volumen `mysql-data`).
- **maildev (`maildev/maildev`):** Servidor SMTP local (puerto web `1080`).
- **redis (`redis`):** Caché y colas en memoria (puerto `6379`).

---

## 2. Pasos para desplegar desde cero

### Paso 1: Clonar el repositorio y situarse en la versión entregada
```bash
git clone https://github.com/DamCone/Attendize.git
cd Attendize
git checkout tarea-01-entrega
```

### Paso 2: Crear el archivo de configuración
```bash
cp .env.example .env
```

### Paso 3: Construir las imágenes Docker
```bash
docker build --tag attendize_base --target base .
docker build --tag attendize_worker --target worker --cache-from attendize_base:latest .
docker build --tag attendize_web --target web --cache-from attendize_worker:latest .
```

### Paso 4: Levantar servicios y preparar Laravel
```bash
docker compose up -d
docker compose exec web composer install
docker compose exec web php artisan key:generate --force
docker compose down
docker compose up -d
docker compose exec web sh -c "wait-for-it db:3306 -t 180 && php artisan migrate"
```

### Paso 5: Acceder a la aplicación
Abre en el navegador:
- Asistente de instalación: https://localhost:8081/install
- Panel principal: https://localhost:8081/

---

## 3. Control del entorno y comprobación de persistencia

- **Detener los contenedores:**
  ```bash
  docker compose stop
  ```
- **Volver a iniciarlos:**
  ```bash
  docker compose start
  ```
- **Verificar el estado:**
  ```bash
  docker compose ps
  ```
Los datos y eventos creados se conservan entre reinicios gracias al volumen `mysql-data`.

---

## 4. Problemas encontrados y soluciones aplicadas

- **Error 404 al construir la imagen base (`apt-get update`):**
  - **Problema:** La imagen base utiliza Debian 10 Buster, una versión obsoleta cuyos repositorios oficiales están cerrados y con firmas caducadas.
  - **Solución:** En el `Dockerfile`, se cambiaron las URLs de los repositorios a `archive.debian.org` y se permitió instalar paquetes con `--allow-unauthenticated`.

- **Error `/bin/sh: 1: ./scripts/setup: not found` al construir:**
  - **Problema:** En Windows, Git descarga los archivos con saltos de línea CRLF (`\r\n`), por lo que el intérprete de Linux dentro del contenedor no reconocía la cabecera del script.
  - **Solución:** En el `Dockerfile`, se añadió `sed -i 's/\r$//'` antes de lanzar el script para eliminar los caracteres de retorno de carro de Windows.

- **Error de PHP por falta de `vendor/autoload.php`:**
  - **Problema:** Al montar el volumen local en el contenedor, se ocultaba la carpeta de dependencias que no existía en el equipo anfitrión.
  - **Solución:** Se ejecutó `docker compose exec web composer install` para descargar las dependencias de Laravel y crear el autoloader.

- **Error en el navegador: `No application encryption key has been specified`:**
  - **Problema:** Docker Compose solo carga las variables de `.env` al crear el contenedor; un simple reinicio no leía la nueva clave y además se habían concatenado claves repetidas en el archivo.
  - **Solución:** Se corrigió la línea `APP_KEY` en el `.env`, se generó con `php artisan key:generate --force` y se aplicó recreando los contenedores con `docker compose down` y `docker compose up -d`.
