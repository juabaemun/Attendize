# 05 · Análisis de riesgos

Escala: Probabilidad e Impacto = Baja / Media / Alta. Responsable: el propio alumno/a.

| Nº | Riesgo | Prob. | Impacto | Prevención | Respuesta | Responsable |
|---|---|---|---|---|---|---|
| R1 | Tecnología antigua: PHP/Laravel y dependencias de Attendize v2.8.0 incompatibles con Inertia/React | Alta | Alta | Probar pronto una dependencia mínima en una rama; documentar versiones | Limitar el alcance a módulos nuevos desacoplados; consultar al profesorado | Alumno/a |
| R2 | Pérdida de datos en MySQL por una migración errónea | Media | Alta | Volcado de la base antes de migrar; volumen Docker de persistencia | Restaurar el volcado y repetir con datos de prueba | Alumno/a |
| R3 | Seguridad: subir `.env` o credenciales a GitHub | Media | Alta | `.gitignore`, revisión con `git status` y `git diff` antes de cada commit | Rotar credenciales y reescribir historial si se publica | Alumno/a |
| R4 | Dependencias npm/composer con vulnerabilidades o que dejan de existir | Media | Media | Fijar versiones con lockfiles; revisar `npm audit` | Sustituir o actualizar la dependencia | Alumno/a |
| R5 | Falta de tiempo para el núcleo obligatorio | Media | Alta | Alcance pequeño; deseables y opcionales separados; hitos semanales | Recortar deseables y priorizar RF-01…RF-10 | Alumno/a |
| R6 | Organización personal: concentrar el trabajo al final | Media | Media | Calendario con fechas por hito; sesiones fijas de trabajo | Replanificar y comunicar al profesorado | Alumno/a |
| R7 | Despliegue: el entorno Docker deja de funcionar al cambiar de equipo | Baja | Alta | README reproducible; probar clonando la etiqueta en limpio | Documentar el fallo y corregir Dockerfile/compose | Alumno/a |
