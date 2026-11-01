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
- SO: Windows + terminal MSYS2/UCRT64
- Docker version: (salida de `docker version`)
- Docker Compose version: (salida de `docker compose version`)
- Git: (salida de `git --version`)

## 3. Arquitectura
(Esquema: navegador → contenedor web → worker → MySQL / Redis / correo; volúmenes)

| Servicio | Imagen/target | Puerto | Función |
|---|---|---|---|
| web | | 8081 | |
| worker | | | |
| db | | 3306 | |
| redis | | | |
| (correo) | | | |

Volúmenes: ...  Red: ...

## 4. Variables de entorno
| Variable | Valor ficticio | Para qué sirve |
|---|---|---|
| DB_HOST | db | |

## 5. Puesta en marcha desde cero
(clonar, .env, build, up, key:generate, migrate, /install)

## 6. Comprobaciones
Construir · iniciar · comprobar · detener · reiniciar · persistencia.
Datos ficticios usados: ...

## 7. Problemas encontrados y decisiones
### Problema 1: `git push` rechazado (autenticación)
- Fecha:
- Orden:
- Error: `Invalid username or token. Password authentication is not supported...`
- Causa: credencial antigua guardada que Git Credential Manager reutilizaba; GitHub no admite contraseña por HTTPS.
- Solución: eliminar la credencial y reautenticar.
- Commit relacionado:

## 8. Evidencias
(Cada una con fecha, explicación y commit)

## 9. Limitaciones pendientes