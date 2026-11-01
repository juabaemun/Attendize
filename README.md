> Este README documenta el despliegue de la Tarea 1. El README original de Attendize
> se conserva en [README.original.md](README.original.md)

# Attendize v2.8.0: despliegue local con Docker y MySQL

Tarea 1 · 2.º DAW · IES Juan de Garay · Alumno: Juan José Baeza Muñoz

## 1. Punto de partida
- Aplicación: Attendize (Laravel), gestión de eventos y entradas
- Repositorio oficial: https://github.com/Attendize/Attendize
- Versión: v2.8.0 (commit 47997d2)
- Mi fork: https://github.com/juabaemun/Attendize
- Rama de trabajo: `tarea-01-despliegue` · Etiquetas: `tarea-01-inicio`, `tarea-01-entrega`

## 2. Requisitos previos
- SO: Windows con WSL2 + terminal 29.9.2
- Docker version: 29.9.2
- Docker Compose version: v5.6.1
- Git: version 2.56.0.windows.4

## 3. Arquitectura

| Servicio | Imagen/target | Puerto | Función |
|---|---|---|---|
| `web` | `attendize_web:latest` (construida localmente) | 8080:80 (HTTP), 8081:443 (HTTPS) | Nginx + PHP-FPM; sirve la aplicación Laravel |
| `worker` | `attendize_worker:latest` (construida localmente) | ninguno publicado | Ejecuta `php artisan queue:work` en segundo plano |
| `db` | `mysql:5.7.23` | 3306:3306 | Base de datos `attendize` |
| `redis` | `redis` | ninguno publicado (6379 interno) | Caché y colas, sin uso activo en esta configuración |


## 4. Variables de entorno
| Variable | Valor ficticio | Para qué sirve |
|---|---|---|
| APP_URL |	https://localhost:8081 | URL base de la aplicación
| APP_KEY | (vacía, la genera key:generate)	| Clave de cifrado de Laravel
| APP_ENV / APP_DEBUG | local / true | Modo de ejecución y errores detallados
| DB_CONNECTION	| mysql | Driver de base de datos
| DB_HOST	| db | Nombre del servicio MySQL en la red de Compose
| DB_PORT	| 3306 | Puerto de MySQL dentro de la red Docker
| DB_DATABASE / DB_USERNAME / DB_PASSWORD | attendize (los tres) | Credenciales de desarrollo, no reales
| QUEUE_CONNECTION | sync | Las colas se ejecutan en línea
| CACHE_DRIVER / SESSION_DRIVER	| file | Almacenamiento en disco del contenedor
| REDIS_HOST | 127.0.0.1 | Servidor Redis (revisar con el compose)
| MAIL_HOST / MAIL_PORT	| maildev / 1025 | Servidor SMTP de pruebas

## 5. Puesta en marcha desde cero
clonar:
    git clone --branch tarea-01-entrega https://github.com/juabaemun/Attendize.git
    cd Attendize

.env: #preparar la configuración
    copy .env.example .env

build: #construir las imágenes
    docker build --tag attendize_base --target base .
    docker build --tag attendize_worker --target worker --cache-from attendize_base:latest .
    docker build --tag attendize_web --target web --cache-from attendize_worker:latest .

up: 
    docker compose up -d
    docker compose ps

migrate: #preparar la base de datos
    docker compose exec web sh -c "wait-for-it db:3306 -t 180 && php artisan migrate"

/install:
    Abrir `https://localhost:8081/install` e instalar con datos por defecto e inventados

## 6. Comprobaciones
Crear un evento, detener docker e inciar para comprobar persitencia en la base de datos

## 7. Problemas encontrados y decisiones
### Problema 1: `git push` rechazado (autenticación)
- Fecha: 1/11/2026
- Orden:
- Error: `Invalid username or token. Password authentication is not supported...`
- Causa: credencial antigua guardada que Git Credential Manager reutilizaba; GitHub no admite contraseña por HTTPS.
- Solución: eliminar la credencial y reautenticar.
- Commit relacionado:

### Problema 2: Al hacer docker build --tag attendize_base --target base . --> apt-get update falla con 404 y EXPKEYSIG en [base 2/5].
- Fecha: 1/11/2026
- Orden: docker build --tag attendize_base --target base .
- Error: `ERROR [base 2/5] RUN apt-get update && apt-get install -y wait-for-it libxrender1`
- Causa: : imagen base Debian 10 (buster) fuera de soporte; repos movidos a archive.debian.org; clave de nginx caducada; repo sury de PHP retirado.
- Solución: reemplazar la línea `RUN apt-get ...`  por `RUN sed -i 's|deb.debian.org|archive.debian.org|g; s|security.debian.org|archive.debian.org|g' /etc/apt/sources.list \
                                && sed -i '/buster-updates/d; /nginx.org/d' /etc/apt/sources.list \
                                && rm -f /etc/apt/sources.list.d/php.list \
                                && apt-get -o Acquire::Check-Valid-Until=false update \
                                && apt-get install -y wait-for-it libxrender1`     
- Commit relacionado: tarea-01-despliegue 5c84a2a

### Problema 3: RUN ./scripts/setup falla con not found (exit 127). --> git ls-files --eol scripts muestra w/crlf.
- Fecha: 1/11/2026
- Orden: RUN ./scripts/setup 
- Error: `RUN ./scripts/setup falla con not found`
- Causa: conversión automática a CRLF de Git en Windows; el shebang queda como sh\r.
- Solución: corregir en el repositorio con .gitattributes en lugar de parchear solo el Dockerfile, porque el compose monta el código del host. --> .gitattributes con eol=lf más renormalización.
- Commit relacionado: tarea-01-despliegue 98f306c
  
### Problema 4: ERROR importing cache manifest from attendize_base:latest en los builds de worker y web.
- Fecha: 1/11/2026
- Orden: Ordenes buid de worker y web
- Error: `ERROR importing cache manifest from attendize_base:latest  `
- Causa: --cache-from busca la imagen en un registro remoto; las imágenes attendize_* solo existen en local.
- Solución: No es necesaria, el build termina correctamente y reutiliza la caché local (CACHED). Mantener las órdenes tal como las declara el Makefile y dejar constancia
- Commit relacionado: tarea-01-despliegue b63c159

### Problema 5: WARNING vendor/autoload.php: No such file or directory dentro de web, y worker ausente en docker compose ps.
- Fecha: 1/11/2026
- Orden: docker compose exec web ls /var/www/vendor/autoload.php.
- Error: `WARN[0000] C:\Users\Juanjo\Attendize\docker-compose.yml: the attribute `version` is obsolete, it will be ignored, please remove it to avoid potential confusion `
- Causa: el bind mount .:/var/www (y .:/usr/share/nginx/html) oculta el vendor/ creado en la imagen, porque el host no lo tiene.
- Solución: ejecutar composer install sobre la carpeta montada en lugar de modificar el compose--> docker compose run --rm --no-deps web composer install.
- Commit relacionado: tarea-01-despliegue b63c159

## 8. Evidencias
(Cada una con fecha, explicación y commit)

## 9. Limitaciones pendientes