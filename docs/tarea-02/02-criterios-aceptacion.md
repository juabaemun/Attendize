# 02 · Requisitos y criterios de aceptación

## Requisitos

| Código | Usuario | Necesidad | Prioridad | Datos afectados | Origen |
|---|---|---|---|---|---|
| RF-01 | Organizador | Crear un evento con título, fecha de inicio, lugar (`venue_name`) y descripción | Obligatoria | Evento | Tarea 1 (flujo actual de creación) + enunciado |
| RF-02 | Organizador | Definir una entrada gratuita con cupo máximo | Obligatoria | Entrada | Enunciado (caso TechFest) |
| RF-03 | Organizador | Editar o despublicar un evento propio | Obligatoria | Evento | Entrevista/validación profesorado |
| RF-04 | Asistente | Ver el listado de eventos publicados, con mensaje si no hay ninguno | Obligatoria | Evento | Perfil asistente |
| RF-05 | Asistente | Ver el detalle del evento y las plazas disponibles | Obligatoria | Evento, entrada | Perfil asistente |
| RF-06 | Asistente | Inscribirse indicando nombre, apellidos y correo, con validación y mensajes de error | Obligatoria | Asistente | Perfil asistente |
| RF-07 | Asistente | Que el sistema impida superar el cupo y duplicar el correo en el mismo evento | Obligatoria | Asistente, entrada | Integridad de datos |
| RF-08 | Asistente | Recibir en pantalla una confirmación con código de inscripción | Obligatoria | Asistente | Perfil asistente |
| RF-09 | Organizador | Consultar la lista paginada de asistentes de su evento | Obligatoria | Asistente | Perfil organizador |
| RF-10 | Organizador / Administrador | Que solo usuarios autenticados gestionen eventos y solo los de su propia cuenta (Attendize no tiene roles; se usa `account_id` e `is_parent`) | Obligatoria | Usuario, evento | Seguridad |
| RF-11 | Organizador | Exportar la lista de asistentes a CSV | Deseable | Asistente | Perfil organizador |
| RF-12 | Organizador | Buscar asistentes por nombre o correo | Deseable | Asistente | Perfil organizador |

## Criterios de aceptación (Dado / cuando / entonces)

**RF-01**
- CA-01.1 (correcto): Dado un organizador autenticado en el formulario de evento, cuando rellena título, fecha futura, lugar y descripción y pulsa «Guardar», entonces el evento aparece en su panel y se conserva tras recargar la página.
- CA-01.2 (inválido): Dado el mismo formulario, cuando deja el título vacío o pone una fecha pasada, entonces no se guarda y se muestra un mensaje de error junto a cada campo afectado.
- CA-01.3 (sin permiso): Dado un visitante sin sesión, cuando accede a la dirección de creación de evento, entonces se le redirige al inicio de sesión y no se crea nada.

**RF-02**
- CA-02.1: Dado un evento recién creado, cuando el organizador define una entrada gratuita con cupo 50, entonces el detalle público muestra «50 plazas disponibles».
- CA-02.2 (inválido): Dado el formulario de entrada, cuando introduce cupo vacío, 0, negativo o texto, entonces se rechaza con un mensaje que indica el rango válido (1–1000); no se admite «sin límite».

**RF-03**
- CA-03.1: Dado un evento propio publicado, cuando el organizador lo despublica, entonces deja de aparecer en el listado público.
- CA-03.2 (sin permiso): Dado un evento de otro organizador, cuando intenta editarlo, entonces recibe un mensaje de acceso denegado y el evento no cambia.

**RF-04**
- CA-04.1: Dado que existen eventos publicados, cuando un asistente abre el listado, entonces ve solo los publicados, ordenados por fecha.
- CA-04.2 (estado vacío): Dado que no hay eventos publicados, cuando abre el listado, entonces ve el mensaje «Todavía no hay eventos disponibles».

**RF-05**
- CA-05.1: Dado un evento con cupo 50 y 12 inscritos, cuando el asistente abre su detalle, entonces ve «38 plazas disponibles».
- CA-05.2 (inválido): Dado un evento inexistente o despublicado, cuando se accede a su dirección, entonces se muestra una página «Evento no encontrado».

**RF-06**
- CA-06.1: Dado el formulario de inscripción, cuando el asistente introduce nombre, apellidos y un correo válido, entonces la inscripción se guarda.
- CA-06.2 (inválido): Dado el formulario, cuando el correo no tiene formato válido o falta algún campo obligatorio, entonces no se guarda y se indica cada error junto al campo.

**RF-07**
- CA-07.1 (cupo): Dado un evento con 0 plazas disponibles, cuando un asistente intenta inscribirse, entonces se informa de que está completo y no se crea la inscripción.
- CA-07.2 (duplicado): Dado un correo ya inscrito en ese evento (inscripción no cancelada), cuando se repite la inscripción, entonces se rechaza con un mensaje y no se crea un segundo registro.
- CA-07.3 (coherencia): Dado un evento con cupo 50 y 12 inscritos, cuando se completa una inscripción, entonces las plazas libres pasan a 37 y existen el pedido, el asistente y el vínculo con la entrada, o no existe ninguno si algo falla.

**RF-08**
- CA-08.1: Dado una inscripción correcta, cuando se guarda, entonces se muestra una pantalla con el nombre del evento y un código de inscripción único que coincide con el almacenado en la base de datos.

**RF-09**
- CA-09.1: Dado un evento propio con 60 inscritos, cuando el organizador abre la lista, entonces ve la primera página de 25 asistentes y puede navegar a las demás.
- CA-09.2 (estado vacío): Dado un evento sin inscritos, cuando abre la lista, entonces ve «Aún no hay inscripciones».
- CA-09.3 (sin permiso): Dado un asistente o visitante, cuando accede a la lista, entonces se deniega el acceso.

**RF-10**
- CA-10.1: Dado un organizador A con eventos, cuando un usuario de otra cuenta B inicia sesión, entonces no ve ni puede abrir los eventos de A.
- CA-10.2: Dado un usuario sin sesión, cuando solicita cualquier página de gestión, entonces se redirige al inicio de sesión.

**Criterios transversales (todas las pantallas)**
- Se puede completar el recorrido solo con teclado, con foco visible y orden lógico.
- En móvil (320 px de ancho) no hay desplazamiento horizontal.
