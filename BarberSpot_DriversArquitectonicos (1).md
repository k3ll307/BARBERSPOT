# BarberSpot — Documentación de Arquitectura de Software
Kelly Fernanda Uribe Giraldo y Daniel Henao Londoño

## Contexto del proyecto

BarberSpot es una aplicación web responsive para la gestión y agendamiento de citas en una barbería, pensada para tres tipos de usuarios: clientes, barberos y un administrador. El cliente podrá consultar los barberos disponibles, ver su disponibilidad de horarios y agendar una cita eligiendo el servicio que desee (corte sencillo, corte con barba, limpieza facial, entre otros), sin depender de llamadas o mensajes. El barbero podrá gestionar su propia disponibilidad y visualizar sus citas asignadas, mientras que el administrador tendrá control centralizado del negocio, gestionando barberos, servicios y accediendo a reportes generales. El sistema busca reemplazar la gestión manual de citas por una plataforma, accesible desde cualquier dispositivo, adaptada a las necesidades reales de una barbería local.

---

## 1. Funcionalidades Críticas

| Identificador Requisito Funcional/Historia de Usuario/Caso de Uso | Especificación Requisito Funcional/Historia de Usuario/Caso de Uso | Tipo de funcionalidad significativa | Justificación | Observación |
|---|---|---|---|---|
| HU-03 | Como usuario del sistema (cliente/barbero/administrador), quiero iniciar sesión con mi correo y contraseña, para acceder al sistema según mi rol. | Valor de negocio | Es la puerta de entrada a todo el sistema; sin autenticación, ningún actor (cliente, barbero o administrador) puede operar dentro de BarberSpot. |  |
| HU-05 | Como administrador, quiero registrar un nuevo barbero con su perfil (nombre, foto, especialidad), para que esté disponible en el sistema y los clientes puedan seleccionarlo. | Valor de negocio | Sin barberos registrados no existe oferta que mostrar, es la base del catálogo comercial sobre el que se va a trabajar en la barbería. |  |
| HU-07 | Como administrador, quiero registrar un servicio (nombre, precio y duración), para que los clientes puedan seleccionarlo al agendar su cita. | Valor de negocio | Sin servicios registrados no hay nada que el cliente pueda elegir al agendar; es la base del catálogo comercial de la barberia. |  |
| HU-09 | Como barbero, quiero configurar mi horario base semanal, para que los clientes solo vean horarios en los que realmente trabajo. | Ambos | Habilita al barbero a recibir citas y sostiene todo el flujo de agendamiento, ya que sin esto no hay disponibilidad que ofrecer al cliente. Técnicamente, el horario configurado debe alimentar correctamente el cálculo posterior de disponibilidad, validando que las franjas horarias sean coherentes. |  |
| HU-11 | Como cliente, quiero ver el listado de barberos disponibles y consultar sus horarios libres, para elegir con quién y cuándo agendar mi cita. | Ambos | Es el primer paso del flujo principal del negocio, ya que sin esta consulta el cliente no puede decidir cuándo y con quién agendar y además se necesita que en el lado técnico requiere cruzar en tiempo real el horario base del barbero, sus bloqueos puntuales y las citas ya agendadas para calcular la disponibilidad real | Al ser una consulta muy frecuente, su rendimiento debe probarse bajo carga; una respuesta lenta aquí afecta directamente la experiencia de todo el flujo de agendamiento. |
| HU-12 | Como cliente, quiero seleccionar un barbero, un servicio y un horario disponible, para reservar mi cita sin depender de llamadas telefónicas. | Ambos | Es la funcionalidad central del negocio, ya que reemplaza la gestión manual de citas por llamadas o WhatsApp, que era el problema original a resolver, y además se necesita que en el lado técnico el sistema garantice que dos clientes no reserven el mismo horario con el mismo barbero al mismo tiempo |  |
| HU-14 | Como cliente, quiero cancelar una cita agendada, para liberar el horario si ya no puedo asistir, dentro del tiempo permitido. | Ambos | Evita horarios bloqueados sin uso real, mejorando la relación con el cliente y protegiendo la imagen del negocio, y además se necesita que en el lado técnico se valide el tiempo límite de cancelación y se libere el horario de forma consistente |  |
| HU-15 | Como barbero, quiero ver mis citas asignadas por día, para organizar mi tiempo de trabajo. | Valor de negocio | Es la herramienta diaria de trabajo del barbero; sin esta vista no puede organizar su jornada ni saber a quién va atender. |  |
| HU-18 | Como administrador, quiero ver reportes de ingresos, el servicio más solicitado y el desempeño por barbero, para tomar decisiones informadas sobre el negocio. | Ambos | Genera un valor a la barberia, ya que de esta manera van a estar más contabilizados los clientes que adquieren un servicio en la barberia y adquiere un reto técnico porque  requiere cálculos y seguridad. |  |
| HU-20 | Como cliente, quiero recibir un correo de confirmación al agendar una cita, para tener constancia de la reserva realizada. | Reto técnico | Requiere integrar un servicio externo de correo y manejar posibles fallos de envío sin bloquear ni afectar el proceso de agendamiento. |  |
| HU-21 | Como barbero, quiero recibir un correo de confirmacion cuando me agendan o cuando me cancelan una cita. | Reto técnico | Requiere integrar el mismo servicio externo de correo utilizado en HU-20, adaptando el contenido del mensaje para notificar al barbero sobre nuevas citas o cancelaciones en su agenda |  |

## 2. Restricciones de Negocio

| Tipo | Restricción de Negocio | Justificación | Plan acción |
|---|---|---|---|
| Humano | El administrador de la barbería solo dispone de aproximadamente 1 hora al día, para reunirse con el equipo de desarrollo, validar avances y resolver dudas sobre el negocio. | Al ser el único administrador del negocio, su tiempo está mayormente ocupado atendiendo clientes, por lo que su disponibilidad para el proyecto es limitada y debe aprovecharse de forma eficiente. | Programar reuniones cortas y estructuradas (máximo 60 minutos), con agenda enviada previamente, y consolidar dudas para resolverlas en bloque en cada sesión. |
| Humano | Los barberos, al estar atendiendo clientes durante toda su jornada laboral, solo pueden dedicar tiempo puntual y esporádico entre cortes para probar el sistema y dar retroalimentación sobre su funcionamiento. | La validación de funcionalidades clave como la configuración de disponibilidad y la gestión de citas depende directamente de los barberos, por lo que su poca disponibilidad puede retrasar la retroalimentación necesaria para ajustar el sistema antes del lanzamiento. | Realizar sesiones breves de prueba aprovechando pausas entre citas, y habilitar un canal asincrónico (chat) para que los barberos reporten observaciones sin necesidad de reunirse. |
| Humano | Los clientes de la barbería incluyen personas de distintas edades y niveles de familiaridad con la tecnología, incluyendo clientes de mayor edad que prefieren la atención tradicional y tienen poca experiencia usando páginas web. | Si la interfaz no es lo suficientemente simple e intuitiva, se corre el riesgo de excluir a un segmento importante de clientes habituales del negocio, quienes podrían optar por seguir agendando por llamada o simplemente dejar de asistir si sienten que el proceso digital es complicado. | Implementar capacitaciones cortas y guías prácticas para clientes y barberos, para el manejo del sistema. |
| Tiempo | El proyecto de digitalización de la barbería debe iniciar el 1 de agosto de 2026 y estar completamente en funcionamiento antes del 1 de diciembre de 2026, fecha límite establecida por el dueño del negocio. | El dueño del negocio fijó esta fecha porque diciembre representa el pico de ingresos anual de la barbería, por lo que exige que el sistema esté operativo y probado antes de esa fecha. | Definir un cronograma con entregas parciales cada 2-3 semanas, priorizando primero las funcionalidades críticas de disponibilidad y agendamiento, para asegurar un sistema usable antes de la fecha límite. |
| Tiempo | El negocio necesita mantener el sistema de citas manual (llamadas y WhatsApp) funcionando en paralelo con BarberSpot durante un período inicial, antes de depender por completo del sistema digital, para no perder citas si surgen fallas o ajustes en las primeras semanas de uso. | El dueño del negocio no puede arriesgarse a que un error o una funcionalidad no probada en un escenario real haga que un cliente se quede sin poder agendar; mantener ambos canales simultáneamente durante la transición reduce ese riesgo mientras se gana confianza en el sistema. | Mantener el canal de WhatsApp y llamadas activo como respaldo durante las primeras semanas de uso del sistema, y definir una fecha de cierre gradual una vez se confirme la estabilidad del sistema. |
| Legal | Garantizar el habeas data para todos los usuarios de la plataforma | De acuerdo con las leyes Colombianas, todos los datos personales de los usuarios deben ser protegidos y salvaguardados siguendo unos protocolos estrictos de seguridad. | Incluir un aviso de tratamiento de datos en el registro de usuarios, cifrar las contraseñas almacenadas y restringir el acceso a la información personal únicamente a los roles autorizados. |
| Presupuesto | El negocio cuenta con un presupuesto inicial limitado, cercano a $5.000.000, para invertir en tecnología, al tratarse de una barbería local de un solo local. | Al ser un negocio pequeño, no cuenta con un gran capital destinado a licencias de software costosas ni a plataformas de terceros que cobran suscripción o comisión por cada cita agendada, por lo que el software debe ser adaptado a este presupuesto inicial y ser capaz de no tener una mensualidad costosa que no lleve a perdidas al negocio. | Priorizar funcionalidades críticas y diseñar arquitectura modular que permita entregar MVP escalable sin sobrepasar presupuesto. |
| Proceso | Aunque el proceso de agendamiento y las funcionalidades principales de BarberSpot ya están definidas, es posible que durante el desarrollo surjan nuevas necesidades o ajustes no contemplados inicialmente, a medida que el dueño del negocio y los barberos interactúen con el sistema y identifiquen mejoras sobre su operación real. | A pesar de contar con un levantamiento de requisitos previo, el negocio seguirá operando y evolucionando durante el desarrollo del proyecto, por lo que es natural que surjan necesidades adicionales una vez el dueño y los barberos empiecen a visualizar el sistema en funcionamiento y lo comparen con su forma actual de trabajar. | Mantener reuniones periódicas de seguimiento con el dueño del negocio para validar avances y capturar nuevas necesidades a tiempo, priorizando su incorporación en iteraciones posteriores sin afectar las funcionalidades críticas ya comprometidas. |
| Proceso | El negocio exige que el pago de los servicios se mantenga presencial en el local, sin procesar pagos en línea a través del sistema. | El dueño del negocio prefiere evitar la complejidad legal, financiera y de seguridad asociada al procesamiento de pagos electrónicos (pasarelas de pago, comisiones, fraude), manteniendo el cobro como se ha manejado tradicionalmente. | Diseñar el flujo de agendamiento de forma que el sistema únicamente reserve el horario y el servicio, dejando el cobro como un paso posterior gestionado directamente en el local. |

## 3. Restricciones Técnicas

| Tipo | Categoria | Restricción técnica | Justificación |
|---|---|---|---|
| Impuesta por el cliente | Compatibilidad de dispositivos | El sistema debe implementarse como una aplicación web responsive, con diseño mobile-first, compatible con los navegadores más usados, sin requerir instalación de ninguna aplicación. | El dueño del negocio solicitó explícitamente que sus clientes pudieran agendar desde el celular sin fricciones de instalación, ya que es el dispositivo que más utilizan tanto los clientes como los propios barberos. |
| Propia del proyecto | Arquitectura de software | El sistema debe construirse siguiendo una arquitectura hexagonal, manteniendo las reglas del negocio en el centro y conectando las tecnologías externas mediante puertos y adaptadores. | Separar las reglas de negocio de la tecnología externa facilita que el equipo trabaje en paralelo (frontend y backend) sin bloquearse, y permite cambiar una herramienta externa sin modificar la lógica principal, algo clave dado el tamaño reducido del equipo. |
| Propia del proyecto | Base de datos | El modelo de datos debe implementarse sobre una base de datos relacional (SQL). | Las relaciones entre las entidades del negocio (citas, barberos, servicios, usuarios) son claras y estructuradas, por lo que un motor relacional garantiza integridad referencial y consultas eficientes, frente a una base NoSQL que no aporta ventajas en este caso. |
| Propia del proyecto | Concurrencia y consistencia | El módulo de agendamiento debe implementar control de concurrencia (transacciones atómicas o bloqueo optimista) al registrar una cita. | Es el reto técnico más crítico del sistema: sin este control, dos clientes podrían reservar el mismo horario con el mismo barbero, generando pérdida de confianza y problemas operativos reales para el negocio. |
| Propia del proyecto | Seguridad | El sistema debe implementar control de acceso basado en roles (RBAC) y almacenar las contraseñas cifradas mediante hashing (nunca en texto plano). | Al manejar datos personales de clientes, barberos y administrador, el sistema debe garantizar que cada usuario acceda únicamente a las funciones de su rol, cumpliendo además con lo exigido por la Ley 1581 de 2012 (Habeas Data). |
| Propia del proyecto | Infraestructura y despliegue | El sistema debe operar priorizando servicios de hosting, base de datos y correo de bajo costo o gratuitos, ajustándose al presupuesto de $5.000.000 disponible para desarrollo, puesta en producción y mantenimiento durante 1 año. | El presupuesto es reducido frente a la escala de un proyecto empresarial; debe cubrir tanto la construcción como la operación de todo un año, por lo que no se justifica invertir en infraestructura cloud-native compleja cuando servicios básicos son suficientes para el volumen esperado del negocio. |
| Propia del proyecto | Arquitectura de software | La lógica de negocio debe exponerse mediante una API REST consumida por el frontend. | Desacoplar el backend del frontend permite que, en el futuro, se pueda construir una aplicación móvil nativa u otro cliente sin necesidad de reescribir la lógica de negocio ya implementada. |
| Propia del proyecto | Diseño | Se debe propender por un diseño con bajo acoplamiento y alta cohesión entre los módulos del sistema (usuarios, disponibilidad, agendamiento, reportes). | Permite que cambios o nuevas funcionalidades se integren sin afectar otros módulos, protegiendo especialmente el funcionamiento del agendamiento y la disponibilidad, que son el corazón del negocio. |
| Propia del proyecto | Metodológico | Se debe propender por la aplicación de una metodología ágil (Scrum simplificado), con entregas parciales cada 2-3 semanas. | Permite mostrar avances constantes al dueño del negocio dentro de su disponibilidad limitada, y ajustar el rumbo del proyecto rápidamente si surgen nuevas necesidades durante el desarrollo, tal como se identificó en las restricciones de proceso. |
| Propia del proyecto | Documentación | Se debe propender por documentar de forma clara los endpoints de la API y los módulos principales del sistema. | Facilita que cualquier integrante del equipo entienda y dé mantenimiento al código de un compañero, reduciendo el riesgo de retrasos por la experiencia limitada del equipo y el tamaño reducido del mismo. |
| Propia del proyecto | Control de versiones | El equipo debe utilizar Git y un repositorio remoto (GitHub) con un flujo de ramas definido para el trabajo colaborativo. | Al ser varios integrantes trabajando en distintos módulos del sistema, un flujo de ramas ordenado evita conflictos de código y facilita revisar cambios antes de integrarlos a la versión principal. |
| Propia del proyecto | Prácticas de código limpio | El desarrollo debe seguir principios de código limpio (Clean Code), evitando código desordenado y "code smells". | Facilita que cualquier integrante del equipo entienda y modifique el código de un compañero, algo especialmente importante dado el tamaño reducido del equipo y el tiempo limitado del semestre. |
| Propia del proyecto | Patrones de diseño | Se debe propender por la aplicación de los principios DRY (Don't Repeat Yourself) y KISS (Keep It Simple, Stupid) en el diseño e implementación del sistema. | Evitar duplicación de código y mantener soluciones simples reduce la probabilidad de errores y facilita el mantenimiento del sistema por parte de un equipo con experiencia limitada en proyectos de este tamaño. |
| Propia del proyecto | Pruebas de software | Se deben implementar pruebas unitarias para el módulo de cálculo de disponibilidad y el módulo de agendamiento de citas. | Son los módulos más complejos y propensos a errores del sistema (cálculo de horarios libres y control de concurrencia), por lo que probarlos de forma aislada reduce el riesgo de fallos en producción. |
| Propia del proyecto | Prácticas de desarrollo | Los parámetros de negocio más propensos a cambiar (tiempo límite de cancelación, duración de servicios) deben quedar configurables desde el panel de administración, sin requerir cambios de código. | Ya se identificó que algunas reglas del negocio podrían ajustarse durante el desarrollo; dejar estos valores parametrizables evita despliegues innecesarios y le da autonomía al negocio para ajustar su operación. |

## 4. Trade-off QA (Priorización inicial de Atributos de Calidad)

| Puesto | Atributo de calidad |
|---|---|
| 1 | Disponibilidad |
| 2 | Usabilidad |
| 3 | Confiabilidad |
| 4 | Seguridad |
| 5 | Rendimiento |
| 6 | Capacidad de ser administrado |
| 7 | Costo |
| 8 | Escalabilidad |
| 9 | Capacidad de ser soportado |
| 10 | Accesibilidad |

## 5. Mapa de Empatía (Votación real por rol)

| Atributo de calidad | Cliente | Barbero | Administrador barberia | Total | Ponderador global |
|---|---|---|---|---|---|
| Disponibilidad | 1 | 1 | 3 | 5 | 3% |
| Usabilidad | 2 | 4 | 7 | 13 | 8% |
| Confiabilidad | 3 | 2 | 4 | 9 | 5% |
| Seguridad | 4 | 5 | 8 | 17 | 10% |
| Rendimiento | 5 | 3 | 5 | 13 | 8% |
| Capacidad de ser administrado | 10 | 8 | 1 | 19 | 12% |
| Costo | 9 | 6 | 2 | 17 | 10% |
| Escalabilidad | 8 | 10 | 6 | 24 | 15% |
| Capacidad de ser soportado | 7 | 9 | 9 | 25 | 15% |
| Accesibilidad | 6 | 7 | 10 | 23 | 14% |
|  | 55 | 55 | 55 | 165 | 100% |

## 6. Preguntas de Atributos de Calidad

### Disponibilidad

| Pregunta | Respuesta |
|---|---|
| Deseas que el sistema este siempre disponible para el cliente (todos los dias de la semana, a cualquier hora, 24/7)? | Sí |
| ¿Desea que el sistema permita que varios clientes agenden citas al mismo tiempo sin fallar, soportando al menos 20 a 30 personas conectadas simultáneamente (por ejemplo: un sábado en la mañana, la hora de mayor demanda)? | Sí |
| ¿Desea que el sistema esté disponible desde cualquier dispositivo con navegador web, sin importar la marca (por ejemplo: celular, tablet o computador)? | Sí |
| ¿Desea que el sistema se recupere en menos de 5 minutos después de una falla del servidor, conservando las citas ya registradas (por ejemplo: si el servidor se reinicia, las citas del día no desaparecen)? | Sí |
| ¿Desea que el sistema esté disponible todo el tiempo para sus clientes, incluso durante actualizaciones o mejoras futuras (por ejemplo: que se publique una mejora sin que el cliente note que el sistema estuvo caído)? | Sí |
| ¿Le preocuparía que, por usar un hosting de bajo costo, el sistema tarde algunos segundos en responder tras un período sin uso (por ejemplo: la primera persona que entra en la mañana espera unos segundos más de lo normal)? | No |
| ¿Desea que el sistema le avise al administrador si llega a presentar una caída prolongada (por ejemplo: una notificación si el sistema lleva más de 10 minutos sin responder)? | Sí |
| ¿Desea que la información de las citas se conserve en un respaldo distinto del servidor principal, para no perderla si este falla (por ejemplo: si el servidor donde funciona BarberSpot se daña, las citas de la semana se pueden recuperar desde una copia guardada en otro lugar, en vez de perderse por completo)? | Sí |
| ¿Necesita que el sistema esté disponible el 100% del tiempo sin ninguna excepción, con infraestructura redundante en varios servidores desde el lanzamiento inicial? | No |
### Usabilidad

| Pregunta | Respuesta |
|---|---|
| ¿Desea que un cliente que use el sistema por primera vez pueda completar el agendamiento sin pedir ayuda a nadie (por ejemplo: entender por sí solo dónde elegir el barbero, el servicio y el horario)? | Sí |
| ¿Desea que el sistema muestre mensajes claros y específicos cuando falte un campo o haya un error (por ejemplo: "Selecciona un servicio antes de continuar")? | Sí |
| ¿Desea que el cliente pueda agendar una cita en máximo 4 pasos (por ejemplo: elegir barbero, elegir servicio, elegir horario y confirmar, sin pantallas adicionales)? | Sí |
| ¿Desea que el sistema muestre un resumen con nombre del barbero, servicio, precio, fecha y hora antes de confirmar la cita (por ejemplo: una pantalla final de revisión antes del botón "Confirmar")? | Sí |
| ¿Desea que el sistema pida confirmación antes de acciones importantes como cancelar una cita (por ejemplo: ¿Seguro que quieres cancelar tu cita del viernes)? | Sí |
| ¿Desea que el sistema use un lenguaje sencillo y cercano, evitando términos técnicos (por ejemplo: decir "Tu cita fue guardada" en vez de "Registro exitoso en la base de datos")? | Sí |
| ¿Desea que el proceso de agendar una cita sea igual de sencillo desde el celular que desde un computador (los mismos pasos, solo adaptados al tamaño de pantalla)? | Sí |
| ¿Desea que el sistema recuerde automáticamente el último barbero o servicio elegido por un cliente para agilizar su próxima cita? | No |
| ¿Desea que el sistema esté disponible en más de un idioma desde el lanzamiento inicial (por ejemplo: que un cliente extranjero pueda cambiarlo a inglés)? | No |
### Confiabilidad

| Pregunta | Respuesta |
|---|---|
| ¿Desea que el sistema nunca permita que dos clientes agenden el mismo horario con el mismo barbero (por ejemplo: si Juan y Jose intentan reservar las 3:00 p. m. al mismo tiempo, solo uno de los dos logra la reserva y al otro se le informa de inmediato)? | Sí |
| ¿Desea que una cita cancelada libere correctamente el horario en menos de 1 minuto (por ejemplo: si Juan cancela su cita de las 3:00 p. m., ese horario aparece disponible de inmediato para otro cliente)? | Sí |
| ¿Desea que el cliente conserve su cita original si una reprogramación no puede completarse (por ejemplo: si otra persona toma el nuevo horario elegido, el sistema no borra la cita que el cliente ya tenía)? | Sí |
| ¿Desea que el estado de una cita sea siempre el mismo para los tres actores del sistema (por ejemplo: si el barbero marca una cita como "finalizada", el cliente y el administrador la ven igual, sin retrasos ni inconsistencias)? | Sí |
| ¿Desea que un cambio futuro en el precio de un servicio no modifique el valor de citas ya realizadas (por ejemplo: una cita agendada cuando el corte costaba $25.000 mantiene ese valor en el historial, aunque después el precio suba a $30.000)? | Sí |
| ¿Desea que si el sistema falla justo cuando el cliente va a confirmar la cita, no se genere una reserva a medias (por ejemplo: si se corta la conexión al confirmar, no debe quedar la cita en el sistema)? | Sí |
| ¿Desea que la información de una misma cita sea idéntica para el cliente, el barbero y el administrador, sin versiones distintas entre ellos? | Sí |
| ¿Desea que, si el sistema debe reiniciarse por alguna falla, ninguna cita ya confirmada se pierda o se duplique? | Sí |
### Seguridad

| Pregunta | Respuesta |
|---|---|
| ¿Desea que cada usuario solo pueda acceder a las funciones de su propio rol (por ejemplo: que un cliente no pueda entrar al panel donde se editan los precios y los barberos)? | Sí |
| ¿Desea que los datos personales de cada cliente estén protegidos frente a otros usuarios (por ejemplo: que un cliente no pueda ver el teléfono, correo o historial de citas de otro cliente)? | Sí |
| ¿Desea que ninguna persona, ni siquiera el administrador o el equipo técnico, pueda consultar la contraseña real de un usuario (por ejemplo: que las contraseñas se guarden cifradas y sean imposibles de leer directamente)? | Sí |
| ¿Es consciente de que, al recolectar datos personales, el sistema debe cumplir con la Ley 1581 de 2012 de protección de datos en Colombia (por ejemplo: pedir autorización al cliente antes de guardar su correo y teléfono)? | Sí |
| ¿Desea que el sistema cierre la sesión automáticamente tras 30 minutos de inactividad en un computador compartido del negocio? | Sí |
| ¿Desea que el sistema bloquee temporalmente una cuenta después de varios intentos fallidos de inicio de sesión (por ejemplo: tras 5 intentos incorrectos seguidos)? | Sí |
| ¿Necesita que el sistema exija un código adicional de verificación (autenticación de doble factor) cada vez que el administrador inicia sesión, desde el lanzamiento inicial? | No |
| ¿Necesita que el sistema cumpla con certificaciones internacionales de seguridad, como ISO 27001, desde el lanzamiento inicial? | No |
### Rendimiento

| Pregunta | Respuesta |
|---|---|
| ¿Desea que los horarios disponibles de un barbero aparezcan en menos de 2 segundos después de seleccionarlo (por ejemplo: elegir un barbero y ver de inmediato qué horas están libres esta semana)? | Sí |
| ¿Desea que la confirmación de una cita se muestre en menos de 3 segundos después de presionar "Confirmar" (por ejemplo: saber casi de inmediato si la cita quedó registrada)? | Sí |
| ¿Desea que el sistema mantenga este mismo tiempo de respuesta cuando al menos 30 clientes lo usen al mismo tiempo (por ejemplo: un sábado en la mañana, sin que la página se vuelva notoriamente más lenta)? | Sí |
| ¿Aceptaría un tiempo de espera de hasta 5 segundos al generar reportes con mucha información (por ejemplo: que el administrador espere unos segundos al consultar los ingresos de todo un año)? | Sí |
| ¿Desea que la agenda de un barbero cargue en menos de 2 segundos, incluso si tiene más de 50 citas registradas en la semana (por ejemplo: consultar toda la semana sin una espera larga)? | Sí |
| ¿Desea que cancelar una cita se procese en menos de 3 segundos desde que se confirma la acción? | Sí |
| ¿Necesita que el sistema mantenga estos mismos tiempos de respuesta si en el futuro se manejan varias sedes al mismo tiempo? | No |
| ¿Necesita que el sistema responda en menos de 100 milisegundos, como una plataforma financiera de alta frecuencia (por ejemplo: procesar órdenes de compra y venta de acciones en tiempo real)? | No |
### Capacidad de ser administrado

| Pregunta | Respuesta |
|---|---|
| ¿Desea que el administrador pueda agregar, editar o desactivar un barbero en menos de 5 minutos, sin ayuda de un programador (por ejemplo: registrar un barbero nuevo el mismo día que llega a trabajar)? | Sí |
| ¿Desea que el administrador pueda cambiar el precio de un servicio directamente desde el sistema (por ejemplo: subir el corte de $25.000 a $30.000 sin pedir ayuda externa)? | Sí |
| ¿Desea que cada barbero pueda bloquear sus propios horarios no laborables (por ejemplo: bloquear el lunes en la mañana porque tiene una cita médica)? | Sí |
| ¿Desea que el administrador pueda buscar citas usando filtros específicos, como barbero, fecha o estado (por ejemplo: consultar las citas canceladas de un barbero en una semana puntual)? | Sí |
| ¿Desea recibir un resumen simple de cuántas citas se agendaron y cancelaron en la semana, sin tener que pedirlo a un técnico? | Sí |
| ¿Desea poder activar o desactivar temporalmente un servicio del catálogo (por ejemplo: pausar "tratamientos capilares" mientras no tenga el producto disponible)? | Sí |
| ¿Desea tener distintos niveles de administrador, con permisos diferentes entre ellos (por ejemplo: un encargado que solo vea reportes, pero no pueda cambiar precios)? | No |
### Costo

| Pregunta | Respuesta |
|---|---|
| ¿Desea que el sistema utilice inicialmente herramientas gratuitas o de bajo costo mientras el número de usuarios sea bajo (por ejemplo: un plan gratuito de hosting mientras la barbería tenga pocos clientes registrados)? | Sí |
| ¿Desea que el desarrollo y la puesta en marcha del sistema no superen el presupuesto de $5.000.000 destinado para un año completo (análisis, desarrollo, pruebas y despliegue incluidos)? | Sí |
| ¿Desea evitar el pago de comisiones por cada cita agendada (por ejemplo: no pagar un valor adicional si en un mes se registran 300 citas, a diferencia de plataformas como Booksy que si cobra comisión por cita registrada)? | Sí |
| ¿Desea que los $5.000.000 cubran tanto la construcción del sistema como su funcionamiento durante el primer año (hosting, dominio, correo)? | Sí |
| ¿Estaría dispuesto a asumir un costo mensual fijo, aunque sea bajo, si eso garantizara mayor estabilidad (por ejemplo: $30.000 al mes por un hosting de pago en vez de uno gratuito)? | No |
| ¿Estaría dispuesto a pagar más de 5.000.000 si surgiera un imprevisto técnico durante el desarrollo (por ejemplo: un costo adicional de $500.000 para resolver un problema no previsto)? | No |
### Escalabilidad

| Pregunta | Respuesta |
|---|---|
| ¿Desea que el sistema siga funcionando correctamente si aumenta hasta un 50% el tráfico habitual (por ejemplo: durante diciembre, cuando más personas consultan y agendan citas)? | Sí |
| ¿Desea que el sistema permita pasar de 3 a 10 barberos sin tener que construir un sistema nuevo? | Sí |
| ¿Desea que se puedan agregar nuevos servicios en el futuro sin modificar el sistema completo (por ejemplo: incluir coloración o tratamientos capilares)? | Sí |
| ¿Desea que, en el futuro, BarberSpot pueda administrar otra sede desde la misma aplicación (por ejemplo: gestionar barberos y citas de una segunda barbería en otro municipio)? | No |
| ¿Desea que el sistema pueda adaptarse si en el futuro decide ofrecer combos de servicios (por ejemplo: "corte + barba + cejas" a un precio especial)? | Sí |
| ¿Planea abrir una segunda sede en los próximos 6 meses? | No |
| ¿Planea aumentar significativamente el número de clientes registrados en el próximo año (por ejemplo: pasar de unos pocos cientos a varios miles)? | No |
### Accesibilidad

| Pregunta | Respuesta |
|---|---|
| ¿Desea que el sistema funcione igual en los navegadores más utilizados: Chrome, Safari, Edge y Firefox? | Sí |
| ¿Desea que el texto tenga un tamaño mínimo legible, de al menos 16 píxeles (por ejemplo: que un cliente adulto mayor pueda leer sin acercar mucho la pantalla)? | Sí |
| ¿Desea que los colores usados para mensajes de confirmación, advertencia y error sean claramente distinguibles entre sí (por ejemplo: verde para éxito, rojo para error, con suficiente contraste)? | Sí |
| ¿Desea que los botones y campos sean lo suficientemente grandes para tocarlos con facilidad desde un celular, sin necesidad de hacer zoom? | Sí |
| ¿Le interesa que, desde el lanzamiento inicial, el sistema sea compatible con lectores de pantalla para personas con discapacidad visual? | No |
| ¿Necesita una versión especial y distinta del sistema pensada solo para personas mayores? | No |
### Capacidad de ser soportado

| Pregunta | Respuesta |
|---|---|
| ¿Desea que el sistema registre fecha, hora y módulo exacto en el que ocurrió un error (por ejemplo: "10 de octubre, 3:00 p. m., falló el envío del correo de confirmación")? | Sí |
| ¿Desea que el equipo técnico pueda identificar en menos de 15 minutos cuál parte del sistema está fallando (por ejemplo: saber si el problema es de la página, el servidor, la base de datos o el correo)? | Sí |
| ¿Desea que existan copias de seguridad diarias para recuperar la información en caso de falla (por ejemplo: recuperar las citas y usuarios guardados el día anterior si la base de datos falla)? | Sí |
| ¿Desea que el equipo de desarrollo corrija las fallas reportadas en un plazo máximo de 48 horas? | Sí |
| ¿Necesita soporte técnico disponible las 24 horas, los 7 días de la semana, como un call center? | No |
| ¿Necesita un manual técnico detallado del sistema entregado desde el primer día de lanzamiento? | No |

## 7. Escenarios de Calidad (con votación)

| ID | Atributo | Fuente del estímulo | Estímulo | Ambiente | Artefacto | Respuesta | Medida de la respuesta | Votos Cliente | Votos Barbero | Votos Administrador |
|---|---|---|---|---|---|---|---|---|---|---|
| **Disponibilidad** | | | | | | | | | | |
| ESC-DISP-001 | Disponibilidad | Cliente | Intentar acceder al sistema para agendar una cita en cualquier día u hora | Operación normal, cualquier día de la semana | Aplicación web completa | El sistema permite el acceso y la consulta de disponibilidad sin restricción horaria | Disponible al menos el 95-98% del tiempo cada mes, aceptando interrupciones cortas y ocasionales. | 2 | 0 | 0 |
| ESC-DISP-002 | Disponibilidad | Múltiples clientes | Acceder e intentar agendar citas al mismo tiempo | Hora de mayor demanda (sábado en la mañana) | Módulo de agendamiento | El sistema atiende las solicitudes sin caerse ni degradar el servicio | Soporta al menos 20 a 30 usuarios simultáneos sin fallas | 3 | 2 | 0 |
| ESC-DISP-003 | Disponibilidad | Cliente / Barbero / Administrador | Acceder al sistema desde un dispositivo con navegador web | Operación normal, cualquier dispositivo | Interfaz web responsive | El sistema carga y funciona correctamente sin importar el dispositivo | Funciona en el 100% de los dispositivos con navegador actualizado | 0 | 0 | 0 |
| ESC-DISP-004 | Disponibilidad | Falla del servidor | Dejar de responder temporalmente (condición del servidor, sin actor) | Condición de falla técnica | Servidor / infraestructura de hosting | El sistema se reactiva conservando las citas ya registradas | Recuperación en menos de 5 minutos, sin pérdida de datos | 0 | 0 | 0 |
| ESC-DISP-005 | Disponibilidad | Equipo de desarrollo | Publicar una actualización o mejora del sistema | Mantenimiento planeado | Proceso de despliegue | La actualización se aplica sin que el cliente perciba una caída del servicio | 0 minutos de interrupción notoria durante despliegues | 0 | 0 | 0 |
| ESC-DISP-006 | Disponibilidad | Falla prolongada del sistema | Dejar de responder por más de 10 minutos (condición del sistema, sin actor) | Condición de falla extendida | Módulo de monitoreo/alertas | El sistema notifica al administrador sobre la caída | Notificación enviada antes de cumplirse 10 minutos de inactividad | 0 | 0 | 0 |
| ESC-DISP-007 | Disponibilidad | Falla del servidor principal | Dañarse o dejar de funcionar (condición del servidor, sin actor) | Condición de falla grave | Sistema de respaldo (backup) | Las citas se recuperan desde una copia almacenada en otro lugar | Recuperación completa sin pérdida de citas registradas | 0 | 0 | 1 |
| ESC-DISP-008 | Disponibilidad | Barbero | Consultar su agenda del día mientras el sistema presenta una falla breve | Condición de falla técnica breve | Módulo de agenda del barbero | El sistema se recupera y muestra la agenda sin pérdida de citas | Menos de 5 minutos de indisponibilidad para el barbero | 0 | 4 | 0 |
| ESC-DISP-009 | Disponibilidad | Administrador | Ingresar a los reportes del negocio en horario nocturno | Operación fuera del horario de atención | Panel de administración | El sistema permanece accesible para consulta administrativa en cualquier horario | Acceso disponible dentro del mismo rango de disponibilidad general del sistema (95-98%), sin restricción horaria propia del rol | 0 | 0 | 2 |
| ESC-DISP-010 | Disponibilidad | Cliente | Acceder al sistema desde una conexión de datos móviles inestable | Zona con señal débil | Aplicación web | El sistema carga la información esencial aunque la conexión sea lenta | Página de agendamiento carga en menos de 8 segundos incluso con conexión débil | 0 | 0 | 0 |
| **Usabilidad** | | | | | | | | | | |
| ESC-USA-001 | Usabilidad | Cliente nuevo | Usar el sistema por primera vez para agendar una cita | Primera visita, sin experiencia previa | Módulo de agendamiento | El cliente completa el proceso guiándose solo por la interfaz | Completa el agendamiento sin solicitar ayuda externa | 3 | 0 | 0 |
| ESC-USA-002 | Usabilidad | Cliente | Omitir un campo obligatorio (ej. no seleccionar un servicio) | Proceso de agendamiento en curso | Formulario de agendamiento | El sistema muestra un mensaje específico indicando qué falta | Mensaje entendible, sin términos técnicos | 1 | 0 | 0 |
| ESC-USA-003 | Usabilidad | Cliente | Agendar una cita completa (barbero, servicio, horario, confirmación) | Operación normal | Flujo de agendamiento | El sistema guía el proceso en pasos secuenciales simples | Máximo 4 pasos para completar el agendamiento | 2 | 0 | 0 |
| ESC-USA-004 | Usabilidad | Cliente | Estar a punto de confirmar una cita | Último paso del agendamiento | Pantalla de resumen | El sistema muestra barbero, servicio, precio, fecha y hora antes de guardar | 100% de los campos relevantes visibles antes de confirmar | 0 | 0 | 0 |
| ESC-USA-005 | Usabilidad | Cliente | Presionar el botón "Cancelar cita" | Operación normal | Módulo de gestión de citas del cliente | El sistema solicita confirmación antes de ejecutar la cancelación | Confirmación explícita requerida antes de cancelar | 0 | 0 | 0 |
| ESC-USA-006 | Usabilidad | Cliente / Barbero | Recibir un mensaje del sistema tras una acción | Operación normal | Mensajes del sistema | El sistema usa lenguaje cercano y no técnico | Mensajes en lenguaje natural, sin jerga técnica | 0 | 0 | 0 |
| ESC-USA-007 | Usabilidad | Cliente | Agendar una cita desde celular y luego desde un computador | Operación normal en distintos dispositivos | Interfaz responsive | El sistema mantiene los mismos pasos, solo adapta el diseño | Mismo número de pasos y flujo en ambos dispositivos | 0 | 1 | 0 |
| ESC-USA-008 | Usabilidad | Barbero | Actualizar el estado de una cita entre cortes | Tiempo limitado, pocos minutos disponibles | Panel del barbero | El sistema permite completar la acción con un solo clic | Acción completada en menos de 3 pasos | 0 | 3 | 0 |
| ESC-USA-009 | Usabilidad | Administrador | Necesitar cambiar el precio de un servicio | Operación normal | Panel de administración de servicios | El sistema permite el cambio con un formulario simple y directo | Cambio realizado en menos de 5 minutos sin ayuda externa | 0 | 0 | 0 |
| ESC-USA-010 | Usabilidad | Cliente | Necesitar cancelar una cita ya agendada | Consulta del listado "Mis citas" | Módulo de mis citas | El botón de cancelar es visible y accesible directamente desde el listado, sin entrar a otra pantalla | Acción de cancelar accesible en 1 clic desde el listado | 0 | 0 | 0 |
| **Confiabilidad** | | | | | | | | | | |
| ESC-CONF-001 | Confiabilidad | Dos clientes simultáneos | Intentar reservar el mismo horario con el mismo barbero al mismo tiempo | Alta concurrencia | Módulo de agendamiento / base de datos de citas | El sistema confirma la reserva de uno solo y notifica al otro | 0% de citas duplicadas para el mismo horario y barbero | 4 | 2 | 2 |
| ESC-CONF-002 | Confiabilidad | Cliente | Cancelar una cita agendada | Operación normal | Módulo de gestión de citas | El horario cancelado queda disponible de inmediato para otros clientes | Liberación del horario en menos de 1 minuto | 0 | 0 | 0 |
| ESC-CONF-003 | Confiabilidad | Cliente | Intentar reprogramar su cita a un horario que otro cliente toma primero | Alta concurrencia | Módulo de agendamiento | El sistema conserva la cita original sin eliminarla | 0% de citas eliminadas por reprogramación fallida | 1 | 0 | 0 |
| ESC-CONF-004 | Confiabilidad | Barbero | Marcar una cita como "finalizada" | Operación normal | Módulo de estado de citas | El cliente y el administrador ven el mismo estado de inmediato | 100% de coincidencia del estado entre los tres actores | 0 | 0 | 0 |
| ESC-CONF-005 | Confiabilidad | Administrador | Actualizar el precio de un servicio | Operación normal | Módulo de servicios / historial de citas | Las citas anteriores conservan el precio con el que fueron agendadas | 0% de citas históricas modificadas por cambios de precio | 0 | 0 | 0 |
| ESC-CONF-006 | Confiabilidad | Falla de conexión | Interrumpirse la conexión justo al confirmar una cita (condición de red, sin actor) | Condición de falla de red | Módulo de agendamiento | El sistema no registra una cita incompleta | 0% de citas "fantasma" generadas por fallas de conexión | 0 | 0 | 0 |
| ESC-CONF-007 | Confiabilidad | Cliente, barbero y administrador | Consultar la misma cita desde sus respectivas vistas | Operación normal | Base de datos de citas | Los tres ven exactamente la misma información | 0 diferencias entre las vistas de los tres actores | 0 | 0 | 0 |
| ESC-CONF-008 | Confiabilidad | Falla del sistema | Reiniciarse por una falla técnica (condición del sistema, sin actor) | Condición de falla | Base de datos / servidor | Las citas ya confirmadas se mantienen intactas | 0% de pérdida o duplicación de citas tras el reinicio | 0 | 0 | 0 |
| ESC-CONF-009 | Confiabilidad | Cliente | Llegar a la barbería a la hora de su cita agendada | Operación normal | Sistema de agendamiento | El barbero cuenta con el registro exacto de la cita | 100% de coincidencia entre la cita agendada y la atención real | 0 | 0 | 0 |
| ESC-CONF-010 | Confiabilidad | Barbero | Revisar su agenda del día para saber a quién atender | Operación normal | Módulo de agenda del barbero | La agenda mostrada corresponde exactamente a las citas reales confirmadas | 0 diferencias entre agenda mostrada y citas reales | 0 | 4 | 0 |
| ESC-CONF-011 | Confiabilidad | Administrador | Generar un reporte de ingresos del mes | Operación normal | Módulo de reportes | El reporte refleja únicamente citas realmente completadas y cobradas | 100% de exactitud entre el reporte y las citas finalizadas | 0 | 0 | 3 |
| **Seguridad** | | | | | | | | | | |
| ESC-SEG-001 | Seguridad | Cliente | Intentar acceder al panel de edición de precios y barberos | Operación normal | Módulo de control de acceso (RBAC) | El sistema bloquea el acceso por no corresponder a su rol | 100% de los accesos no autorizados bloqueados | 0 | 0 | 0 |
| ESC-SEG-002 | Seguridad | Cliente | Intentar consultar el teléfono o historial de otro cliente | Operación normal | Módulo de gestión de usuarios | El sistema impide el acceso a datos de terceros | 0% de exposición de datos personales entre clientes | 2 | 0 | 0 |
| ESC-SEG-003 | Seguridad | Administrador o equipo técnico | Intentar consultar la contraseña real de un usuario | Operación normal / mantenimiento | Módulo de autenticación | El sistema almacena las contraseñas cifradas (hash) | 0% de contraseñas visibles en texto plano | 0 | 0 | 0 |
| ESC-SEG-004 | Seguridad | Cliente nuevo | Registrarse en el sistema proporcionando datos personales | Cumplimiento legal (Ley 1581 de 2012) | Módulo de registro de usuarios | El sistema solicita autorización antes de almacenar sus datos | 100% de los registros con consentimiento explícito | 2 | 0 | 0 |
| ESC-SEG-005 | Seguridad | Usuario en computador compartido | Dejar la sesión abierta sin actividad | Computador compartido en el local | Módulo de autenticación | El sistema cierra la sesión automáticamente | Cierre automático tras 30 minutos de inactividad | 0 | 0 | 0 |
| ESC-SEG-006 | Seguridad | Usuario o atacante | Ingresar la contraseña incorrecta varias veces seguidas | Intento de acceso no autorizado | Módulo de autenticación | El sistema bloquea temporalmente la cuenta | Bloqueo tras 5 intentos fallidos consecutivos | 0 | 0 | 0 |
| ESC-SEG-007 | Seguridad | Barbero | Intentar acceder a la información de citas de otro barbero | Operación normal | Módulo de control de acceso | El sistema restringe la vista únicamente a sus propias citas | 100% de restricción de acceso entre barberos | 0 | 2 | 0 |
| ESC-SEG-008 | Seguridad | Cliente | Ingresar su contraseña en el formulario de inicio de sesión | Conexión a través de internet | Canal de comunicación (HTTPS) | Los datos viajan cifrados durante la transmisión | 100% de las comunicaciones bajo protocolo HTTPS | 0 | 0 | 0 |
| ESC-SEG-009 | Seguridad | Administrador | Dar de baja a un barbero que ya no trabaja en el negocio | Operación normal | Módulo de gestión de usuarios | El barbero pierde acceso inmediato al sistema | Revocación de acceso en menos de 1 minuto | 0 | 0 | 0 |
| ESC-SEG-010 | Seguridad | Cliente | Registrarse usando su correo electrónico | Operación normal | Módulo de registro | El sistema valida que el correo tenga un formato válido antes de crear la cuenta | 100% de los registros con correo validado antes de guardarse | 0 | 0 | 0 |
| **Rendimiento** | | | | | | | | | | |
| ESC-REND-001 | Rendimiento | Cliente | Seleccionar un barbero para ver su disponibilidad | Operación normal | Módulo de cálculo de disponibilidad | El sistema muestra los horarios libres | Tiempo de respuesta menor o igual a 2 segundos | 2 | 0 | 0 |
| ESC-REND-002 | Rendimiento | Cliente | Presionar "Confirmar" para agendar una cita | Operación normal | Módulo de agendamiento | El sistema procesa y confirma la solicitud | Tiempo de respuesta menor a 3 segundos | 0 | 0 | 0 |
| ESC-REND-003 | Rendimiento | Múltiples clientes | Consultar horarios al mismo tiempo (30 clientes conectados simultáneamente) | Alta demanda (sábado) | Módulo de disponibilidad | El sistema mantiene el mismo tiempo de respuesta | Sin degradación perceptible con 30 usuarios simultáneos | 0 | 0 | 0 |
| ESC-REND-004 | Rendimiento | Administrador | Generar el reporte de ingresos de todo un año | Consulta con gran volumen de datos | Módulo de reportes | El sistema procesa y muestra el resultado | Tiempo de respuesta de hasta 5 segundos | 0 | 0 | 1 |
| ESC-REND-005 | Rendimiento | Barbero | Consultar su agenda de la semana | Semana con alta carga de citas (+50) | Módulo de agenda del barbero | El sistema muestra la agenda completa | Tiempo de carga menor a 2 segundos | 0 | 3 | 0 |
| ESC-REND-006 | Rendimiento | Cliente | Confirmar la cancelación de una cita | Operación normal | Módulo de gestión de citas | El sistema procesa la cancelación | Tiempo de respuesta menor a 3 segundos | 0 | 0 | 0 |
| ESC-REND-007 | Rendimiento | Barbero | Actualizar el estado de una cita entre un corte y otro | Tiempo limitado del barbero | Panel del barbero | El sistema guarda el cambio de inmediato | Tiempo de respuesta menor a 2 segundos | 0 | 2 | 0 |
| ESC-REND-008 | Rendimiento | Administrador | Filtrar el listado de citas por barbero y fecha | Operación normal | Módulo de listado de citas | El sistema muestra los resultados filtrados | Tiempo de respuesta menor a 3 segundos | 0 | 0 | 0 |
| ESC-REND-009 | Rendimiento | Cliente | Cargar la página principal del sistema por primera vez | Primera visita, sin caché del navegador | Frontend del sistema | La página carga completamente | Tiempo de carga inicial menor a 3 segundos | 0 | 0 | 0 |
| ESC-REND-010 | Rendimiento | Administrador | Consultar el listado completo de citas del mes | Operación normal con volumen mensual de citas | Módulo de listado de citas | El sistema muestra los resultados filtrados o paginados | Tiempo de respuesta menor a 3 segundos | 0 | 0 | 0 |
| **Capacidad de ser administrado** | | | | | | | | | | |
| ESC-ADM-001 | Capacidad de ser administrado | Administrador | Registrar un nuevo barbero que acaba de llegar al negocio | Operación normal | Módulo de administración de barberos | El sistema permite el registro sin ayuda de un programador | Registro completo en menos de 5 minutos | 0 | 0 | 5 |
| ESC-ADM-002 | Capacidad de ser administrado | Administrador | Actualizar el precio de un servicio | Operación normal | Módulo de administración de servicios | El sistema aplica el nuevo precio de inmediato | Cambio reflejado en menos de 1 minuto, sin ayuda externa | 0 | 0 | 0 |
| ESC-ADM-003 | Capacidad de ser administrado | Barbero | Bloquear un horario en el que no trabajará (ej. cita médica) | Operación normal | Módulo de disponibilidad del barbero | El sistema oculta ese horario a los clientes | Bloqueo aplicado y visible de inmediato | 0 | 2 | 3 |
| ESC-ADM-004 | Capacidad de ser administrado | Administrador | Buscar las citas canceladas de un barbero en una semana puntual | Operación normal | Módulo de listado de citas | El sistema filtra y muestra los resultados exactos | Resultados correctos según los filtros aplicados | 0 | 0 | 0 |
| ESC-ADM-005 | Capacidad de ser administrado | Administrador | Consultar cuántas citas se agendaron y cancelaron en la semana | Operación normal | Módulo de reportes | El sistema muestra el resumen sin intervención técnica | Información disponible sin soporte externo | 0 | 0 | 2 |
| ESC-ADM-006 | Capacidad de ser administrado | Administrador | Pausar temporalmente un servicio del catálogo | Operación normal | Módulo de administración de servicios | El servicio deja de estar disponible para agendar | Cambio aplicado de inmediato en el catálogo visible | 0 | 0 | 0 |
| ESC-ADM-007 | Capacidad de ser administrado | Administrador | Desactivar la cuenta de un cliente con historial de inasistencias | Operación normal | Módulo de gestión de usuarios | El cliente pierde la posibilidad de agendar nuevas citas | Restricción aplicada de inmediato | 0 | 0 | 0 |
| ESC-ADM-008 | Capacidad de ser administrado | Administrador | Necesitar ajustar el tiempo límite de cancelación de citas | Operación normal | Panel de configuración de reglas de negocio | El sistema permite modificar este parámetro sin tocar código | Cambio aplicado sin intervención del equipo de desarrollo | 0 | 0 | 0 |
| ESC-ADM-009 | Capacidad de ser administrado | Administrador | Revisar el historial de cambios de precios de un servicio | Operación normal | Módulo de servicios | El sistema conserva un registro de los cambios realizados | Historial disponible para consulta del administrador | 0 | 0 | 0 |
| ESC-ADM-010 | Capacidad de ser administrado | Administrador | Necesitar saber cuál es el barbero con más citas atendidas en el mes | Operación normal | Módulo de reportes | El sistema muestra el ranking de barberos por número de citas | Información disponible sin cálculos manuales | 0 | 0 | 0 |
| **Costo** | | | | | | | | | | |
| ESC-COST-001 | Costo | Equipo de desarrollo | Seleccionar los servicios de hosting, base de datos y correo | Presupuesto limitado | Infraestructura del sistema | Se eligen planes gratuitos o de bajo costo mientras el número de usuarios sea bajo | Costo de infraestructura dentro del presupuesto de $5.000.000 asignado, priorizando servicios gratuitos o de bajo costo | 0 | 0 | 2 |
| ESC-COST-002 | Costo | Equipo de desarrollo | Ejecutar el análisis, desarrollo, pruebas y despliegue del sistema | Presupuesto asignado por el negocio | Proyecto completo | El costo total no supera el presupuesto asignado | Costo total menor o igual a $5.000.000 | 0 | 0 | 4 |
| ESC-COST-003 | Costo | Negocio | Registrarse 300 citas en un mes (condición agregada, sin actor único) | Operación normal | Sistema completo (propiedad del negocio) | El negocio no paga ningún valor adicional por las citas agendadas | $0 de comisión, sin importar el volumen mensual | 0 | 0 | 0 |
| ESC-COST-004 | Costo | Negocio | Operar el sistema durante el primer año completo | Presupuesto de $5.000.000 para construcción y operación | Infraestructura y desarrollo | El presupuesto cubre construcción, hosting, dominio y correo del año | Costo total del año dentro de los $5.000.000 asignados | 0 | 0 | 0 |
| ESC-COST-005 | Costo | Equipo de desarrollo | Alcanzar el límite de uso de un servicio con plan gratuito (condición, sin actor) | Crecimiento moderado del negocio | Infraestructura del sistema | El equipo evalúa migrar a un plan de bajo costo dentro del presupuesto restante | Migración posible sin exceder los $5.000.000 totales | 0 | 0 | 0 |
| ESC-COST-006 | Costo | Equipo de desarrollo | Elegir entre varias opciones de hosting gratuito disponibles | Selección de infraestructura | Infraestructura del sistema | El equipo selecciona la opción que mejor cubra las necesidades sin generar costos | Costo de hosting ajustado al presupuesto disponible, priorizando planes gratuitos cuando sea posible | 0 | 0 | 0 |
| ESC-COST-007 | Costo | Negocio | Solicitar conocer cuánto del presupuesto se ha utilizado en el desarrollo | Seguimiento del proyecto | Reporte de gestión del proyecto | El equipo informa el avance del presupuesto ejecutado | Seguimiento del gasto dentro del límite de $5.000.000 | 0 | 0 | 0 |
| **Escalabilidad** | | | | | | | | | | |
| ESC-ESCA-001 | Escalabilidad | Clientes | Aumentar hasta un 50% el tráfico habitual | Temporada de diciembre | Sistema completo | El sistema sigue funcionando correctamente | Sin degradación del servicio con hasta 50% más de tráfico | 1 | 0 | 0 |
| ESC-ESCA-002 | Escalabilidad | Administrador | Aumentar el número de barberos de 3 a 10 | Crecimiento del negocio | Módulo de administración de barberos | El sistema incorpora los nuevos barberos sin cambios estructurales | Escalamiento sin necesidad de reconstruir el sistema | 0 | 0 | 0 |
| ESC-ESCA-003 | Escalabilidad | Administrador | Agregar un nuevo servicio (ej. coloración) | Crecimiento del catálogo | Módulo de administración de servicios | El sistema lo incorpora sin modificar el resto de la plataforma | Nuevo servicio disponible sin cambios de código | 0 | 0 | 0 |
| ESC-ESCA-004 | Escalabilidad | Administrador | Crear un combo de servicios (ej. corte + barba + cejas) | Evolución del catálogo | Módulo de servicios | El sistema permite adaptar el catálogo a esta nueva necesidad | Incorporación posible sin rediseñar el módulo de servicios | 0 | 0 | 0 |
| ESC-ESCA-005 | Escalabilidad | Equipo de desarrollo | Diseñar el sistema pensando en un eventual crecimiento futuro | Diseño inicial del sistema | Arquitectura en capas | El sistema se construye de forma modular para facilitar cambios futuros | Arquitectura preparada para escalar sin reescritura completa | 0 | 0 | 0 |
| ESC-ESCA-006 | Escalabilidad | Negocio | Operar con el volumen real esperado de una barbería local (3 a 10 barberos, 30 a 50 citas diarias) | Operación normal de un solo local | Arquitectura completa del sistema (base de datos, servidor) | El sistema maneja este volumen sin degradación de rendimiento ni infraestructura adicional | Rendimiento estable con hasta 50 citas diarias y 10 barberos activos | 0 | 0 | 1 |
| ESC-ESCA-007 | Escalabilidad | Negocio | Crecer de forma orgánica mes a mes el número de clientes registrados (condición, sin actor) | Crecimiento moderado esperado | Base de datos de clientes | El sistema almacena y consulta la información de nuevos clientes sin degradar el rendimiento | Sin degradación con hasta unos pocos miles de clientes registrados en el primer año | 0 | 0 | 0 |
| ESC-ESCA-008 | Escalabilidad | Administrador | Decidir ofrecer el servicio de limpieza facial además de los ya existentes | Expansión del catálogo | Módulo de servicios | El sistema permite agregarlo sin afectar los servicios ya configurados | Nuevo servicio operativo en menos de 5 minutos, sin tiempo de inactividad | 0 | 0 | 0 |
| **Accesibilidad** | | | | | | | | | | |
| ESC-ACC-001 | Accesibilidad | Cliente | Acceder usando Chrome, Safari, Edge o Firefox | Operación normal | Interfaz web | El sistema funciona igual en los cuatro navegadores | 0 errores de funcionamiento entre navegadores | 1 | 0 | 0 |
| ESC-ACC-002 | Accesibilidad | Cliente adulto mayor | Leer la información del sistema desde su celular | Operación normal | Interfaz de usuario | El texto se muestra en un tamaño legible sin acercar la pantalla | Tamaño de texto mínimo de 16 píxeles | 0 | 0 | 0 |
| ESC-ACC-003 | Accesibilidad | Cliente | Recibir un mensaje de confirmación, advertencia o error | Operación normal | Sistema de mensajes/notificaciones | Cada tipo de mensaje usa un color claramente distinguible | Contraste suficiente entre verde, rojo y amarillo | 0 | 0 | 0 |
| ESC-ACC-004 | Accesibilidad | Cliente | Presionar un botón desde su celular | Uso móvil | Interfaz responsive | El botón se puede presionar sin necesidad de hacer zoom | Tamaño mínimo de botones adecuado para pantallas táctiles | 2 | 1 | 0 |
| ESC-ACC-005 | Accesibilidad | Cliente | Ingresar al sistema desde un celular de gama media o baja | Dispositivo con pantalla reducida | Interfaz responsive | El sistema ajusta el contenido para verse completo sin desplazamiento horizontal | 0 elementos cortados en resoluciones desde 360px de ancho | 0 | 0 | 0 |
| ESC-ACC-006 | Accesibilidad | Cliente | Necesitar identificar qué campos son obligatorios en un formulario | Proceso de registro o agendamiento | Formularios del sistema | El sistema marca visualmente los campos obligatorios | 100% de los campos obligatorios claramente identificados | 0 | 0 | 0 |
| **Capacidad de ser soportado** | | | | | | | | | | |
| ESC-SOP-001 | Capacidad de ser soportado | Falla del sistema | Ocurrir un error durante el agendamiento (condición del sistema, sin actor) | Operación normal en producción | Sistema de logs | El sistema registra fecha, hora y módulo afectado | 100% de los errores relevantes registrados con esta información | 0 | 0 | 0 |
| ESC-SOP-002 | Capacidad de ser soportado | Equipo técnico | Recibir un reporte de falla del sistema | Soporte post-implementación | Sistema de logs | El equipo identifica el módulo responsable de la falla | Identificación en menos de 15 minutos de revisión | 0 | 0 | 0 |
| ESC-SOP-003 | Capacidad de ser soportado | Falla de la base de datos | Presentar una falla (condición de la base de datos, sin actor) | Condición de falla | Sistema de respaldo (backup) | El sistema permite recuperar la información del día anterior | Copias de seguridad realizadas diariamente | 0 | 0 | 0 |
| ESC-SOP-004 | Capacidad de ser soportado | Cliente o barbero | Reportar una falla del sistema | Soporte post-implementación | Proceso de soporte técnico | El equipo de desarrollo corrige la falla reportada | Corrección aplicada en un plazo máximo de 48 horas | 0 | 0 | 0 |
| ESC-SOP-005 | Capacidad de ser soportado | Equipo técnico | Necesitar revisar el estado general del sistema periódicamente | Mantenimiento preventivo | Panel de monitoreo básico | El equipo verifica que el sistema, la base de datos y el correo funcionen correctamente | Revisión posible en menos de 10 minutos | 0 | 0 | 0 |
| ESC-SOP-006 | Capacidad de ser soportado | Cliente o barbero | Reportar que no puede iniciar sesión | Soporte post-implementación | Canal de soporte (correo o formulario de contacto) | El equipo recibe el reporte y contacta al usuario para resolverlo | Primera respuesta en menos de 24 horas | 0 | 0 | 0 |
| **TOTAL** |  |  |  |  |  |  |  | 26 | 26 | 26 |

## 8. Drivers Arquitectónicos (Escenarios de calidad post-votación)



| ID | Atributo | Prioridad | Dificultad | Estado | Fuente del estímulo | Estímulo | Ambiente | Artefacto | Respuesta | Medida de la respuesta | Justificación de negocio | Tácticas arquitectónicas | Supuestos y riesgos |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| ESC-DISP-001 | Disponibilidad | Baja | Media |  | Cliente | Intentar acceder al sistema para agendar una cita en cualquier día u hora | Operación normal, cualquier día de la semana | Aplicación web completa | El sistema permite el acceso y la consulta de disponibilidad sin restricción horaria | Disponible al menos el 95-98% del tiempo cada mes, aceptando interrupciones cortas y ocasionales. | Es la expectativa base de cualquier sistema en línea; si el servicio no responde con frecuencia razonable, el negocio pierde credibilidad frente al cliente. | Elegir un proveedor de hosting con buen historial de disponibilidad; monitoreo básico periódico (ESC-SOP-005) para detectar caídas rápido. | Depende de un proveedor de hosting gratuito fuera del control del equipo; no hay garantía contractual que respalde el 95-98%. |
| ESC-DISP-002 | Disponibilidad | Alta | Media |  | Múltiples clientes | Acceder e intentar agendar citas al mismo tiempo | Hora de mayor demanda (sábado en la mañana) | Módulo de agendamiento | El sistema atiende las solicitudes sin caerse ni degradar el servicio | Soporta al menos 20 a 30 usuarios simultáneos sin fallas | Los sábados en la mañana concentran la mayor demanda; una caída en ese momento afecta directamente los ingresos del día más importante de la semana. | Manejo eficiente de conexiones a la base de datos; consultas optimizadas en el módulo de disponibilidad. | Se asume que 20-30 usuarios simultáneos es el techo real; si el negocio crece más rápido de lo esperado, este supuesto debe revalidarse. |
| ESC-DISP-008 | Disponibilidad | Media | Media |  | Barbero | Consultar su agenda del día mientras el sistema presenta una falla breve | Condición de falla técnica breve | Módulo de agenda del barbero | El sistema se recupera y muestra la agenda sin pérdida de citas | Menos de 5 minutos de indisponibilidad para el barbero | El barbero depende de su agenda para saber a quién atender; una falla aquí interrumpe directamente la operación diaria del negocio. | Reinicio automático del proceso de la aplicación tras una falla; persistencia de datos en base de datos separada del proceso. | Se asume que la falla es del servidor de aplicación, no de la base de datos; si ambas fallan a la vez, el tiempo de recuperación podría ser mayor. |
| ESC-DISP-009 | Disponibilidad | Baja | Baja |  | Administrador | Ingresar a los reportes del negocio en horario nocturno | Operación fuera del horario de atención | Panel de administración | El sistema permanece accesible para consulta administrativa en cualquier horario | Acceso disponible dentro del mismo rango de disponibilidad general del sistema (95-98%), sin restricción horaria propia del rol | El administrador revisa el negocio en momentos libres, que no siempre coinciden con el horario de atención; debe poder hacerlo sin restricciones artificiales. | Ninguna distinta a la disponibilidad general del sistema; no requiere lógica especial de horario para este rol. | Ninguno adicional; hereda los mismos riesgos de ESC-DISP-001. |
| ESC-USA-001 | Usabilidad | Media | Baja |  | Cliente nuevo | Usar el sistema por primera vez para agendar una cita | Primera visita, sin experiencia previa | Módulo de agendamiento | El cliente completa el proceso guiándose solo por la interfaz | Completa el agendamiento sin solicitar ayuda externa | Si un cliente nuevo no logra agendar solo, es probable que abandone el sistema y vuelva al método manual, fracasando el propósito del proyecto. | Diseño mobile-first con flujo guiado paso a paso; validaciones en tiempo real con mensajes claros. | Se asume un nivel básico de alfabetización digital del cliente; usuarios con cero experiencia en aplicaciones podrían necesitar ayuda igual. |
| ESC-USA-003 | Usabilidad | Baja | Baja |  | Cliente | Agendar una cita completa (barbero, servicio, horario, confirmación) | Operación normal | Flujo de agendamiento | El sistema guía el proceso en pasos secuenciales simples | Máximo 4 pasos para completar el agendamiento | Reducir la fricción del agendamiento es la propuesta de valor central frente a la competencia y frente al método manual actual. | Formulario de un solo flujo con listas desplegables, sin pantallas innecesarias. | Ninguno técnico relevante; depende principalmente de decisiones de diseño de interfaz. |
| ESC-USA-008 | Usabilidad | Media | Baja |  | Barbero | Actualizar el estado de una cita entre cortes | Tiempo limitado, pocos minutos disponibles | Panel del barbero | El sistema permite completar la acción con un solo clic | Acción completada en menos de 3 pasos | El barbero tiene minutos contados entre cortes; una acción lenta o compleja reduce la probabilidad de que actualice el sistema consistentemente. | Botones de acción rápida (un clic) en el panel del barbero; actualización optimista en la interfaz. | Ninguno relevante; riesgo bajo de implementación. |
| ESC-CONF-001 | Confiabilidad | Alta | Alta |  | Dos clientes simultáneos | Intentar reservar el mismo horario con el mismo barbero al mismo tiempo | Alta concurrencia | Módulo de agendamiento / base de datos de citas | El sistema confirma la reserva de uno solo y notifica al otro | 0% de citas duplicadas para el mismo horario y barbero | Es el driver arquitectónico principal del proyecto: una doble reserva genera pérdida de confianza inmediata y daña la reputación del negocio. | Restricción única a nivel de base de datos (barbero+fecha+hora); transacciones atómicas; bloqueo optimista al confirmar. | Se asume una única instancia de base de datos; si se escala a múltiples instancias replicadas, la estrategia de unicidad debe revisarse. |
| ESC-CONF-010 | Confiabilidad | Media | Baja |  | Barbero | Revisar su agenda del día para saber a quién atender | Operación normal | Módulo de agenda del barbero | La agenda mostrada corresponde exactamente a las citas reales confirmadas | 0 diferencias entre agenda mostrada y citas reales | Si la agenda del barbero no refleja la realidad, se generan errores operativos (atender a alguien equivocado, olvidar un cliente). | Consulta directa a la misma fuente de datos que usa el módulo de agendamiento, sin caché desactualizada. | Ninguno relevante si se evita el uso de caché con tiempo de expiración largo. |
| ESC-CONF-011 | Confiabilidad | Media | Media |  | Administrador | Generar un reporte de ingresos del mes | Operación normal | Módulo de reportes | El reporte refleja únicamente citas realmente completadas y cobradas | 100% de exactitud entre el reporte y las citas finalizadas | El administrador toma decisiones financieras con base en este reporte; un dato incorrecto puede llevar a decisiones de negocio erróneas. | Cálculo del reporte filtrando únicamente citas en estado "finalizada"; consultas agregadas con filtros por fecha. | Depende de que los barberos marquen consistentemente el estado de cada cita; si no lo hacen, el reporte pierde exactitud aunque el sistema funcione bien. |
| ESC-SEG-002 | Seguridad | Baja | Baja |  | Cliente | Intentar consultar el teléfono o historial de otro cliente | Operación normal | Módulo de gestión de usuarios | El sistema impide el acceso a datos de terceros | 0% de exposición de datos personales entre clientes | Es una obligación legal (Habeas Data) y también de confianza: un cliente no debe poder ver datos de otro bajo ninguna circunstancia. | Control de acceso basado en roles (RBAC); validación de propiedad de datos en cada consulta. | Ninguno externo relevante; el riesgo principal es un error de implementación (omitir el filtro en alguna consulta). |
| ESC-SEG-004 | Seguridad | Baja | Baja |  | Cliente nuevo | Registrarse en el sistema proporcionando datos personales | Cumplimiento legal (Ley 1581 de 2012) | Módulo de registro de usuarios | El sistema solicita autorización antes de almacenar sus datos | 100% de los registros con consentimiento explícito | Cumplimiento directo de la Ley 1581 de 2012; evita sanciones legales y protege la confianza del cliente. | Casilla de consentimiento obligatoria en el formulario de registro, con registro de la aceptación. | Ninguno técnico; riesgo principal es de proceso (omitir el aviso legal en el diseño final de la interfaz). |
| ESC-SEG-007 | Seguridad | Baja | Baja |  | Barbero | Intentar acceder a la información de citas de otro barbero | Operación normal | Módulo de control de acceso | El sistema restringe la vista únicamente a sus propias citas | 100% de restricción de acceso entre barberos | Cada barbero maneja información sensible de sus propios clientes; verla entre barberos podría generar conflictos internos innecesarios. | Filtrado por identificador de barbero en cada consulta del módulo de citas. | Ninguno relevante. |
| ESC-REND-001 | Rendimiento | Baja | Media |  | Cliente | Seleccionar un barbero para ver su disponibilidad | Operación normal | Módulo de cálculo de disponibilidad | El sistema muestra los horarios libres | Tiempo de respuesta menor o igual a 2 segundos | Es la operación más frecuente del sistema; una demora aquí afecta la percepción de calidad de todo el flujo de agendamiento. | Índices en la base de datos sobre las columnas de fecha y barbero; cálculo de disponibilidad optimizado. | El tiempo de respuesta depende del hosting gratuito elegido; podría variar según el proveedor. |
| ESC-REND-005 | Rendimiento | Media | Media |  | Barbero | Consultar su agenda de la semana | Semana con alta carga de citas (+50) | Módulo de agenda del barbero | El sistema muestra la agenda completa | Tiempo de carga menor a 2 segundos | El barbero necesita revisar su semana rápidamente entre clientes; una carga lenta interrumpe su flujo de trabajo diario. | Paginación o carga diferida de citas antiguas; consultas indexadas por barbero y semana. | Ninguno externo relevante; riesgo técnico bajo dado el volumen esperado (no más de 50 citas por semana). |
| ESC-REND-007 | Rendimiento | Baja | Baja |  | Barbero | Actualizar el estado de una cita entre un corte y otro | Tiempo limitado del barbero | Panel del barbero | El sistema guarda el cambio de inmediato | Tiempo de respuesta menor a 2 segundos | El barbero actualiza el estado varias veces al día; cualquier demora perceptible reduce la adopción real del sistema en el día a día. | Actualización directa sin pasos intermedios; respuesta optimista en la interfaz. | Ninguno relevante. |
| ESC-ADM-001 | Capacidad de ser administrado | Alta | Baja |  | Administrador | Registrar un nuevo barbero que acaba de llegar al negocio | Operación normal | Módulo de administración de barberos | El sistema permite el registro sin ayuda de un programador | Registro completo en menos de 5 minutos | Es la funcionalidad que le da autonomía real al negocio frente a depender de un programador para cada cambio operativo. | Formulario CRUD simple en el panel de administración, sin lógica compleja adicional. | Ninguno relevante. |
| ESC-ADM-003 | Capacidad de ser administrado | Alta | Media |  | Barbero | Bloquear un horario en el que no trabajará (ej. cita médica) | Operación normal | Módulo de disponibilidad del barbero | El sistema oculta ese horario a los clientes | Bloqueo aplicado y visible de inmediato | Le da al barbero control sobre su propio tiempo, una necesidad real identificada en las restricciones de negocio (disponibilidad limitada del barbero). | El bloqueo se almacena como excepción sobre el horario base y se resta automáticamente al calcular disponibilidad. | Debe validarse que no exista ya una cita agendada en ese horario antes de permitir el bloqueo (dependencia directa con el módulo de agendamiento). |
| ESC-ADM-005 | Capacidad de ser administrado | Baja | Baja |  | Administrador | Consultar cuántas citas se agendaron y cancelaron en la semana | Operación normal | Módulo de reportes | El sistema muestra el resumen sin intervención técnica | Información disponible sin soporte externo | Le permite al administrador tener una visión operativa semanal sin depender de nadie más para obtenerla. | Consulta agregada simple (conteo de citas por estado y semana). | Ninguno relevante. |
| ESC-COST-001 | Costo | Baja | Baja |  | Equipo de desarrollo | Seleccionar los servicios de hosting, base de datos y correo | Presupuesto limitado | Infraestructura del sistema | Se eligen planes gratuitos o de bajo costo mientras el número de usuarios sea bajo | Costo de infraestructura dentro del presupuesto de $5.000.000 asignado, priorizando servicios gratuitos o de bajo costo | El presupuesto de $5.000.000 es una restricción dura del proyecto; elegir mal la infraestructura desde el inicio compromete la viabilidad financiera de todo el año. | Uso de planes gratuitos documentados (hosting, base de datos, correo) con límites de uso conocidos de antemano. | Los planes gratuitos pueden cambiar sus condiciones en cualquier momento sin previo aviso (riesgo externo fuera de control del equipo). |
| ESC-COST-002 | Costo | Media | Media |  | Equipo de desarrollo | Ejecutar el análisis, desarrollo, pruebas y despliegue del sistema | Presupuesto asignado por el negocio | Proyecto completo | El costo total no supera el presupuesto asignado | Costo total menor o igual a $5.000.000 | Sobrepasar el presupuesto pondría en riesgo la viabilidad completa del proyecto para el negocio real. | Priorización estricta de funcionalidades críticas (MVP) antes de invertir tiempo en funcionalidades secundarias; seguimiento de costos por entrega. | Riesgo de que surja un imprevisto técnico no contemplado (ya identificado como restricción de negocio) que presione el presupuesto. |
| ESC-ACC-004 | Accesibilidad | Media | Baja |  | Cliente | Presionar un botón desde su celular | Uso móvil | Interfaz responsive | El botón se puede presionar sin necesidad de hacer zoom | Tamaño mínimo de botones adecuado para pantallas táctiles | La mayoría de clientes y barberos usan el celular como dispositivo principal; un botón difícil de presionar genera frustración y abandono. | Uso de tamaños táctiles mínimos recomendados (44x44 píxeles) en el diseño de la interfaz responsive. | Ninguno relevante; es una decisión de diseño, no depende de infraestructura externa. |

---

## 9. Decisiones Arquitectónicas (ADR)

Total de decisiones documentadas: 22    
Decisiones con prueba de concepto ejecutada: 6

---

### ADR-001 — Selección de la arquitectura hexagonal.

**Fecha de propuesta:** 30/08/2026  
**Fecha de aprobación:** 01/09/2026  
**Estado:** Aprobado  
**Participantes:** Arquitecto

**Contexto:**
BarberSpot debe gestionar reglas importantes como consultar la disponibilidad, agendar, cancelar y reprogramar citas. Estas reglas deben mantenerse separadas de elementos externos como la interfaz, la base de datos, la autenticación y el servicio de correo. También se necesita una arquitectura que facilite las pruebas y permita realizar cambios sin afectar todo el sistema.

**Alternativas consideradas:**
- Arquitectura tradicional por capas: divide el sistema en presentación, lógica del negocio y acceso a datos.
- Arquitectura hexagonal: mantiene las reglas del negocio en el centro y conecta las tecnologías externas mediante puertos y adaptadores.
- Arquitectura de microservicios: divide el sistema en varios servicios independientes que se comunican mediante la red.

**Decisión:**
Implementar una arquitectura hexagonal. Las reglas del negocio estarán organizadas en las capas de dominio y aplicación, mientras que PostgreSQL, Auth0, Brevo y otros servicios externos se conectarán mediante interfaces y adaptadores ubicados en infraestructura.

**Consecuencias:**
Positivas: Las reglas de las citas quedan separadas de la tecnología usada en cada servicio externo (autenticación, notificaciones, almacenamiento, seguridad, etc.). Esto facilita probar el sistema y cambiar cualquier herramienta en el futuro sin afectar la lógica de negocio.

Negativas: Se deben crear más interfaces y organizar más carpetas y clases. El equipo debe cuidar que cada parte tenga una responsabilidad clara.

**Justificación:**
Por qué se selecciona: la arquitectura hexagonal mantiene las reglas de disponibilidad, agendamiento, cancelación y reprogramación independientes de las tecnologías externas. Esto reduce el acoplamiento, facilita las pruebas y permite cambiar una herramienta sin modificar la lógica principal.

Por qué no se selecciona la arquitectura tradicional por capas: aunque es más sencilla inicialmente, puede generar dependencias directas entre la lógica del negocio, Spring Boot y PostgreSQL. Esto dificultaría las pruebas y los cambios futuros si las responsabilidades no se separan correctamente.

Por qué no se seleccionan microservicios: requieren administrar varios despliegues, comunicaciones entre servicios, fallas distribuidas y herramientas adicionales de monitoreo. Esa complejidad y costo no se justifican para el alcance inicial de BarberSpot.

---

### ADR-002 — Selección de PostgreSQL como base de datos.

**Fecha de propuesta:** 30/08/2026  
**Fecha de aprobación:** 01/09/2026  
**Estado:** Aprobado  
**Participantes:** Arquitecto Cliente

**Contexto:**
BarberSpot debe almacenar usuarios, barberos, servicios, horarios, bloqueos y citas. Esta información tiene relaciones claras y debe mantenerse consistente. Además, el sistema necesita conservar el historial, generar reportes y responder correctamente cuando varios clientes intenten reservar simultáneamente.

**Alternativas consideradas:**
- PostgreSQL: base de datos relacional con transacciones, claves foráneas, índices y manejo de rangos.
- MySQL: motor relacional muy usado en aplicaciones web, pero con menor soporte nativo que PostgreSQL para restricciones sobre rangos de tiempo y horarios superpuestos.
- MongoDB: base de datos documental con una estructura flexible.
- SQLite: base de datos relacional almacenada en un archivo local.

**Decisión:**
Utilizar PostgreSQL como fuente principal de información de BarberSpot. Los usuarios, servicios, horarios, bloqueos y citas se organizarán en tablas relacionadas mediante claves foráneas, restricciones, índices y transacciones.

**Consecuencias:**
Positivas: Permite guardar la información relacionada de manera ordenada y protege la consistencia de las citas. También soporta varios usuarios usando el sistema al mismo tiempo.

Negativas: El equipo debe administrar cambios en las tablas, copias de seguridad e índices. También debe aprender las funciones propias de PostgreSQL.

**Justificación:**
Por qué se selecciona: PostgreSQL se adapta al modelo relacional de BarberSpot y permite proteger la integridad de los datos mediante claves foráneas y transacciones. También cuenta con herramientas para trabajar con rangos de tiempo, optimizar consultas de disponibilidad y generar reportes.

Por qué no se selecciona MySQL: también es una base de datos relacional válida, pero PostgreSQL proporciona mecanismos más completos para representar intervalos y establecer restricciones sobre horarios superpuestos.

Por qué no se selecciona MongoDB: su flexibilidad resulta útil cuando los datos cambian constantemente de estructura. En BarberSpot, los datos tienen relaciones definidas, por lo que MongoDB obligaría a controlar más relaciones e integridad desde el backend sin aportar una ventaja necesaria.

Por qué no se selecciona SQLite: funciona correctamente para aplicaciones locales y con poca concurrencia, pero BarberSpot será una aplicación web utilizada simultáneamente por clientes, barberos y administradores.

---

### ADR-003 — Selección de Java y Spring Boot para el backend.

**Fecha de propuesta:** 30/08/2026  
**Fecha de aprobación:** 01/09/2026  
**Estado:** Aprobado  
**Participantes:** Arquitecto

**Contexto:**
BarberSpot necesita un backend que exponga una API para gestionar usuarios, barberos, servicios, horarios, citas, reportes y notificaciones. También debe aplicar validaciones, controlar permisos, conectarse con PostgreSQL y ejecutar transacciones para conservar la consistencia de las reservas.

**Alternativas consideradas:**
- Java con Spring Boot: plataforma para construir API REST con herramientas de seguridad, validación, persistencia y transacciones.
- TypeScript con NestJS: framework del ecosistema Node.js que permite organizar el backend mediante módulos.
- Python con Django REST Framework: framework que proporciona un ORM, autenticación y herramientas para construir API.
- C# con ASP.NET Core: plataforma de Microsoft para desarrollar servicios web y API.

**Decisión:**
Desarrollar el backend de BarberSpot con Java y Spring Boot. Las funcionalidades se expondrán mediante una API REST y se organizarán respetando las dependencias de la arquitectura hexagonal.

**Consecuencias:**
Positivas: Spring Boot ofrece herramientas listas para crear la API, validar datos, proteger funciones y trabajar con PostgreSQL.

Negativas: La aplicación puede consumir más memoria que opciones ligeras. El equipo necesita conocer Java y la forma de trabajar de Spring Boot.

**Justificación:**
Por qué se selecciona: Spring Boot proporciona herramientas integradas para validar datos, aplicar seguridad, implementar API REST y controlar transacciones. Su integración con Spring Data JPA facilita el acceso a PostgreSQL y su sistema de inyección de dependencias permite conectar los puertos y adaptadores de la arquitectura hexagonal.

Por qué no se selecciona NestJS: es una alternativa modular y adecuada, pero obligaría a configurar el acceso a datos, la seguridad y las transacciones utilizando herramientas diferentes del ecosistema Node.js. Para BarberSpot no ofrece una ventaja que justifique cambiar el conjunto de tecnologías definido para el backend.

Por qué no se selecciona Django REST Framework: permite construir funcionalidades rápidamente gracias a su ORM incorporado (Django ORM), pero esa integración mezcla la lógica de negocio con el acceso a datos, dificultando mantener el dominio independiente de la persistencia, tal como lo exige la arquitectura hexagonal (ADR-001).

Por qué no se selecciona ASP.NET Core: proporciona seguridad, rendimiento e inyección de dependencias comparables, pero introduciría el ecosistema .NET sin ofrecer una ventaja necesaria para las funciones y el alcance de BarberSpot.

---

### ADR-004 — Selección de React y Vite para desarrollar el frontend.

**Fecha de propuesta:** 30/08/2026  
**Fecha de aprobación:** 01/09/2026  
**Estado:** Aprobado  
**Participantes:** Cliente Arquitecto

**Contexto:**
BarberSpot necesita una interfaz web responsive que funcione en celulares, tabletas y computadores. Los clientes deben consultar barberos, servicios y horarios, mientras que los barberos y administradores necesitan gestionar citas e información del negocio. La interfaz debe actualizar los datos sin recargar toda la página y permitir completar las acciones en pocos pasos.

**Alternativas consideradas:**
- React con Vite: construcción de interfaces dinámicas mediante componentes reutilizables.
- Angular: framework completo con estructura, herramientas y convenciones incorporadas.
- Vue con Vite: framework progresivo y ligero basado en componentes.
- HTML, CSS y JavaScript sin framework: desarrollo mediante las herramientas nativas del navegador.

**Decisión:**
Desarrollar el frontend con React y Vite como una aplicación web responsive. React gestionará los componentes y cambios de estado de la interfaz, mientras que Vite se utilizará para el desarrollo y la generación de los archivos desplegables.

**Consecuencias:**
Positivas: Permite reutilizar elementos como calendarios, formularios y tarjetas. La interfaz puede actualizar horarios sin recargar toda la página.

Negativas: Se deben controlar bien los datos que cambian en pantalla y mantener actualizadas las librerías utilizadas.

**Justificación:**
Por qué se selecciona: React permite crear componentes reutilizables para calendarios, formularios, tarjetas de barberos, botones y tablas de citas. También facilita actualizar los horarios y estados después de consultar la API, sin recargar completamente la página. Su ecosistema ofrece herramientas maduras para navegación, formularios y autenticación. Vite simplifica la configuración y la construcción del frontend.

Por qué no se selecciona Angular: incluye una estructura completa, inyección de dependencias, formularios, enrutamiento y otras herramientas. Aunque esto resulta útil en sistemas empresariales grandes, agrega configuraciones y convenciones que aumentan la complejidad para el alcance actual de BarberSpot.

Por qué no se selecciona Vue: es una alternativa válida, ligera y basada en componentes. Sin embargo, no proporciona una ventaja funcional determinante frente a React para las pantallas y operaciones requeridas, mientras que React cuenta con un ecosistema más amplio de integraciones disponibles para el proyecto.

Por qué no se selecciona JavaScript sin framework: requeriría administrar manualmente el estado, la navegación, la actualización de elementos y la reutilización de componentes. Esto dificultaría el mantenimiento cuando aumenten las vistas de clientes, barberos y administradores.

---

### ADR-005 — Selección de Auth0 para la autenticación y autorización de los usuarios.

**Fecha de propuesta:** 30/08/2026  
**Fecha de aprobación:** 01/09/2026  
**Estado:** Aceptado  
**Participantes:** Arquitecto Cliente

**Contexto:**
BarberSpot tendrá clientes, barberos y administradores con permisos diferentes. El sistema debe proteger las cuentas, impedir el acceso a funciones de otros roles y evitar almacenar contraseñas de manera insegura.

**Alternativas consideradas:**
- Auth0: servicio administrado compatible con OAuth 2.0, OpenID Connect y JWT.
- Autenticación propia con Spring Security: almacenar usuarios y gestionar contraseñas, sesiones o tokens directamente desde BarberSpot.
- Firebase Authentication: servicio de identidad administrado por Google.
- Keycloak: plataforma de identidad de código abierto instalada y administrada por el equipo.

**Decisión:**
Utilizar Auth0 como proveedor de identidad. El frontend realizará el inicio de sesión y recibirá un token JWT, mientras que el backend validará el token y aplicará los permisos de cliente, barbero y administrador en cada endpoint. Las validaciones también comprobarán que cada usuario solamente pueda acceder a sus propios datos.

**Consecuencias:**
Positivas: BarberSpot no guarda directamente las contraseñas y puede controlar el acceso de clientes, barberos y administradores.

Negativas: El sistema depende de un servicio externo y de los límites de su plan. Los roles y permisos deben configurarse cuidadosamente.

**Justificación:**
Por qué se selecciona: Auth0 permite delegar funciones sensibles como el inicio de sesión, la protección de credenciales, la recuperación de cuentas y la emisión de tokens. Utiliza estándares compatibles con React y Spring Security. Esto reduce la cantidad de funciones de seguridad que BarberSpot debe implementar y permite centralizar la identidad de los usuarios.

Por qué no se selecciona autenticación propia: ofrecería mayor control, pero el equipo tendría que implementar y mantener el almacenamiento seguro de contraseñas, la recuperación de cuentas, la expiración de tokens, la protección contra intentos repetidos y otras medidas sensibles. Un error podría comprometer los datos de los usuarios.

Por qué no se selecciona Firebase Authentication: también es un servicio administrado, pero el manejo de los roles y permisos específicos de BarberSpot requeriría configurar reclamaciones personalizadas y adaptar su integración con Spring Security.

Por qué no se selecciona Keycloak: ofrece control completo y evita depender de un servicio externo, pero requiere instalar, actualizar, proteger, monitorear y respaldar un servidor adicional.

**Prueba de concepto:** [`/pocs/auth0`](../pocs/auth0) — Se generó un token real de Auth0 y se validó su firma (SHA256withRSA) contra las llaves públicas JWKS, sin almacenar secretos compartidos.

---

### ADR-006 — Selección de Brevo como proveedor exclusivo para el envío de notificaciones por correo electrónico.

**Fecha de propuesta:** 30/08/2026  
**Fecha de aprobación:** 01/09/2026  
**Estado:** Aceptado  
**Participantes:** Cliente Arquitecto

**Contexto:**
BarberSpot necesita notificar automáticamente a los clientes y a los barberos cuando una cita sea agendada, cancelada o reprogramada. Se requiere un servicio externo confiable que permita enviar correos transaccionales mediante plantillas personalizadas, que no afecte el presupuesto del proyecto y que sea fácil de integrar con la API de Spring Boot sin acoplar la lógica del negocio a una plataforma específica.

**Alternativas consideradas:**
- Brevo (Antes Sendinblue):
- Plataforma en la nube especializada en envío de correos transaccionales mediante API REST. Ofrece un nivel gratuito amplio (hasta 300 correos al día), editor visual de plantillas con variables dinámicas, seguimiento de entrega (webhooks) y fácil integración con Spring Boot mediante un adaptador HTTP.
- SendGrid:
- Proveedor de correo transaccional en la nube ampliamente utilizado en la industria. Ofrece API REST y gestión de plantillas. Sin embargo, sus planes gratuitos actuales son más restringidos en el tiempo (periodos de prueba de 30 días) o requieren ingresar medios de pago desde el inicio.
- Amazon SES (Simple Email Service):
- Servicio de correo masivo y transaccional de Amazon Web Services (AWS). Es una opción extremadamente económica a gran escala, pero requiere configurar infraestructura dentro del ecosistema de AWS (verificación de dominios en Route53, gestión de políticas IAM) y no ofrece un creador visual de plantillas tan amigable.
- Servidor SMTP Convencional (Gmail / Outlook):
- Enviar los correos utilizando directamente la cuenta de correo del negocio mediante el protocolo SMTP tradicional. No requiere servicios externos de correo, pero tiene límites diarios muy estrictos (riesgo de bloqueo de la cuenta por spam), latencia alta en las peticiones y no permite manejar plantillas avanzadas de HTML.

**Decisión:**
Utilizar Brevo como proveedor de notificaciones por correo electrónico en BarberSpot.
El backend en Spring Boot interactuará con Brevo mediante un puerto genérico (NotificationGateway) y un adaptador específico (BrevoNotificationAdapter), garantizando que la lógica del negocio no dependa directamente de la API de Brevo. Las credenciales de acceso se protegerán en Vault o variables de entorno.

**Consecuencias:**
Positivas:Envío de correos confiable y con diseño profesional mediante plantillas.
Cero costo de infraestructura en la etapa inicial del negocio.
Si en el futuro se desea cambiar de proveedor, solo se reemplaza el adaptador sin tocar las reglas del agendamiento.
NegativasEl sistema depende de la disponibilidad de la API de Brevo para que los correos lleguen a tiempo.
Si la barbería supera las 300 citas/notificaciones diarias en el futuro, se deberá contratar un plan de pago de Brevo.

**Justificación:**
Por qué se selecciona: Brevo ofrece un plan gratuito permanente (300 correos diarios, sin tarjeta de crédito), un editor visual de plantillas para personalizar los correos sin tocar el código, y una API REST sencilla de conectar con Spring Boot mediante el adaptador BrevoNotificationAdapter, incluyendo seguimiento del estado de cada envío (enviado, entregado, rebotado).

Por qué no se selecciona SendGrid: en un principio parecía una opción sólida, pero al validarlo se confirmó que ya no ofrece un plan gratuito permanente, solo una prueba de 60 días con 100 correos diarios; al finalizar, exige contratar un plan de pago para seguir enviando correos.

Por qué no se selecciona Amazon SES: aunque es económico para grandes volúmenes, exige configurar varias herramientas adicionales de Amazon solo para empezar a enviar correos, y no tiene un editor visual de plantillas, por lo que cada correo tocaría diseñarlo escribiendo código.

Por qué no se selecciona Servidor SMTP Convencional (Gmail, Outlook o Correo Tradicional):

Intentar enviar las notificaciones automáticas desde una cuenta de correo tradicional fue descartado porque los proveedores como Gmail u Outlook aplican límites diarios de envío muy estrictos que marcarían los mensajes como *spam* o bloquearían la cuenta rápidamente. Además, este método es mucho más lento y haría que la página web se quede cargando varios segundos mientras se envía el correo. Finalmente, un correo común no permite utilizar plantillas de diseño dinámicas para que los mensajes se vean profesionales, ni nos proporciona reportes para saber si el correo realmente le llegó al cliente o rebotó.

**Prueba de concepto:** [`/pocs/brevo`](../pocs/brevo) — Se envió un correo transaccional real con mecanismo de reintentos (3 intentos controlados sin bloquear el flujo).

---

### ADR-007 — Publicación de BarberSpot en Cloudflare Pages, Railway y Supabase.

**Fecha de propuesta:** 30/08/2026  
**Fecha de aprobación:** 04/09/2026  
**Estado:** Aprobado  
**Participantes:** Cliente Arquitecto

**Contexto:**
BarberSpot debe estar disponible para clientes, barberos y administradores desde cualquier lugar y horario. El equipo cuenta con un presupuesto limitado y no dispone de personal dedicado a administrar servidores. La infraestructura también debe permitir actualizar el frontend, el backend y la base de datos de manera controlada.

**Alternativas consideradas:**
- Cloudflare Pages, Railway y Supabase: publicar cada parte de BarberSpot en una plataforma administrada.
- Servidor VPS: instalar el frontend, el backend y PostgreSQL en un servidor virtual.
- AWS o Microsoft Azure: utilizar diferentes servicios empresariales de computación, almacenamiento y bases de datos.Servidor local: ejecutar BarberSpot desde un computador ubicado en la barbería.

**Decisión:**
Publicar el frontend desarrollado con React y Vite en Cloudflare Pages, ejecutar el backend de Spring Boot en Railway y alojar la base de datos PostgreSQL en Supabase. Las tres plataformas se configurarán para comunicarse de manera segura.

Recorrido de una solicitud: el frontend se sirve directo desde Cloudflare Pages (no pasa por Railway). Las peticiones a la API siguen esta ruta: Cliente -> Cloudflare (WAF) -> API Gateway (Kong) -> Backend (Railway) -> Base de datos (Supabase). El WAF y el Gateway no son parte del hosting (este ADR) — son capas de seguridad y enrutamiento ubicadas delante del backend ya publicado.

**Consecuencias:**
Positivas: La aplicación puede estar disponible sin depender de un computador de la barbería. Cada parte puede publicarse y actualizarse por separado.

Negativas: La configuración queda repartida entre varias plataformas. Los límites y precios pueden cambiar cuando crezca el uso.

**Justificación:**
Por qué se selecciona: Cloudflare Pages permite publicar el frontend sin administrar un servidor web. Railway facilita la ejecución del backend de Spring Boot y Supabase ofrece PostgreSQL como un servicio administrado. Esta combinación reduce el trabajo de instalación y mantenimiento y permite comenzar con costos bajos, aunque debe aclararse que Railway ya no ofrece un nivel completamente gratuito: su plan básico tiene un costo aproximado de $5 USD al mes, que se cubre dentro del presupuesto total de $5.000.000 asignado al proyecto.

Por qué no se selecciona un VPS: el equipo tendría que instalar y actualizar el sistema operativo, configurar la seguridad, administrar PostgreSQL, renovar certificados y realizar copias de seguridad. También concentraría todos los componentes en un único servidor.

Por qué no se seleccionan AWS o Azure: ofrecen una infraestructura más amplia y escalable, pero requieren mayor configuración, administración y control de costos. Esa complejidad no es necesaria para el alcance inicial de BarberSpot.

Por qué no se selecciona un servidor local: el funcionamiento dependería de la energía, la conexión a Internet y el computador de la barbería. Una falla local impediría que los clientes accedieran al sistema.

---

### ADR-008 — Automatización de pruebas y despliegues mediante GitHub Actions.

**Fecha de propuesta:** 31/08/2026  
**Fecha de aprobación:** 04/09/2026  
**Estado:** Aprobado  
**Participantes:** Arquitecto

**Contexto:**
BarberSpot será modificado frecuentemente durante el desarrollo y después de su publicación. Cada cambio debe compilarse, probarse y verificarse antes de llegar a producción. El equipo necesita un procedimiento uniforme que reduzca errores y evite que los despliegues dependan del computador de una sola persona.

**Alternativas consideradas:**
- Proceso manual: ejecutar las pruebas y desplegar la aplicación desde el computador de un integrante.
- GitHub Actions: automatizar compilación, pruebas y despliegues desde el repositorio de GitHub.
- Jenkins: instalar y administrar un servidor propio para ejecutar los procesos de automatización.
- GitLab CI/CD: utilizar los pipelines proporcionados por GitLab.

**Decisión:**
Implementar un flujo de CI/CD con GitHub Actions. Cada cambio enviado al repositorio ejecutará la compilación y las pruebas del backend y del frontend. El despliegue se realizará únicamente si las validaciones finalizan correctamente.

**Consecuencias:**
Positivas: Todos los cambios pasan por las mismas verificaciones antes de publicarse. Esto reduce errores y hace que el proceso sea repetible.

Negativas: Los flujos y permisos deben mantenerse actualizados. Algunas ejecuciones pueden consumir tiempo incluido en el plan.

**Justificación:**
Por qué se selecciona: GitHub Actions se integra directamente con el repositorio y permite definir un único procedimiento para todo el equipo. Puede compilar Spring Boot, ejecutar las pruebas, construir el frontend y publicar las nuevas versiones utilizando credenciales protegidas.

Por qué no se selecciona el proceso manual: depende de que cada integrante recuerde y ejecute correctamente todos los pasos. También puede producir diferencias entre computadores y permitir que una versión con errores llegue a producción.

Por qué no se selecciona Jenkins: ofrece muchas posibilidades de personalización, pero requiere instalar, proteger, actualizar y monitorear un servidor adicional.

Por qué no se selecciona GitLab CI/CD: también permite automatizar el proceso, pero requeriría trasladar o duplicar el repositorio en otra plataforma sin ofrecer una ventaja necesaria para BarberSpot.

---

### ADR-009 — Actualización automática: automática de citas mediante Server-Sent Events (SSE).

**Fecha de propuesta:** 31/08/2026  
**Fecha de aprobación:** 04/09/2026  
**Estado:** Aceptado  
**Participantes:** Cliente Arquitecto

**Contexto:**
Cuando un cliente agenda, cancela o reprograma una cita, tanto el barbero como otros clientes consultando el mismo horario necesitan conocer el cambio oportunamente — por ejemplo, si un horario deja de estar disponible mientras alguien más lo estaba seleccionando. Las operaciones seguirán realizándose mediante la API REST, pero el backend necesita informar los cambios a la interfaz sin exigir una recarga manual.

**Alternativas consideradas:**
- Actualización manual: el barbero debe recargar la página para consultar cambios.
- Polling: el frontend consulta periódicamente la API para buscar novedades.
- WebSocket: mantener una comunicación bidireccional permanente entre frontend y backend.
- Server-Sent Events (SSE): mantener un canal unidireccional desde el servidor para enviar eventos al navegador.

**Decisión:**
Utilizar SSE para notificar a los frontends de clientes y barberos los cambios de las citas y conservar la API REST para crear, cancelar, reprogramar o modificar información.

**Consecuencias:**
Positivas: El barbero puede ver cambios en su agenda sin actualizar manualmente la página. Se utiliza una comunicación sencilla desde el servidor hacia el navegador.

Negativas: Se deben manejar las reconexiones cuando se pierde internet y comprobar que el servicio de despliegue mantenga la conexión.

**Justificación:**
Por qué se selecciona: SSE es apropiado porque la información debe viajar principalmente desde el servidor hacia el navegador. Permite actualizar la agenda sin recargar la página y utiliza una conexión HTTP más sencilla que una comunicación bidireccional completa.

Por qué no se selecciona la actualización manual: el barbero podría olvidar recargar la página y continuar viendo una agenda desactualizada.

Por qué no se selecciona polling: realiza solicitudes repetidas incluso cuando no existen cambios. Además, la actualización solamente ocurre cuando se cumple el intervalo configurado.

Por qué no se selecciona WebSocket: permite enviar información en ambas direcciones, pero BarberSpot ya utilizará la API REST para las acciones del usuario. Agregaría complejidad sin una necesidad concreta.

**Prueba de concepto:** [`/pocs/sse`](../pocs/sse) — Se comprobó que el servidor puede enviar eventos al navegador de forma continua sin que el cliente los solicite (a diferencia de polling).

---

### ADR-010 — Registro y seguimiento de errores mediante logs estructurados y traceId.

**Fecha de propuesta:** 31/08/2026  
**Fecha de aprobación:** 04/09/2026  
**Estado:** Aceptado  
**Participantes:** Arquitecto

**Contexto:**
Cuando ocurra una falla durante el agendamiento u otra operación, el equipo debe identificar qué solicitud presentó el problema, en qué módulo ocurrió y cuál fue la causa. Al usuario se le debe mostrar un mensaje comprensible sin revelar información técnica ni datos sensibles.

**Alternativas consideradas:**
- Mensajes básicos en consola: registrar textos sin una estructura ni un identificador común.
- Logs estructurados con MDC y traceId: asignar un código único a cada solicitud y conservarlo en todos sus registros.
- OpenTelemetry con una plataforma completa: recopilar logs, métricas y trazas mediante herramientas especializadas.
- Servicio externo de errores: enviar las excepciones a una plataforma externa de seguimiento.

**Decisión:**
Implementar logs estructurados con MDC y asignar un traceId a cada solicitud. También se utilizará un manejador global de errores y un endpoint de salud. Cuando ocurra una falla inesperada, el usuario recibirá el código de seguimiento para que el equipo pueda localizarla en los registros.

**Consecuencias:**
Positivas: El equipo puede encontrar más fácilmente el error relacionado con una solicitud y darle al usuario un código de seguimiento.

Negativas: Se generan más registros y se debe evitar guardar contraseñas, tokens u otros datos sensibles.

**Justificación:**
Por qué se selecciona: los logs estructurados y el traceId permiten relacionar el error mostrado al usuario con los registros del backend. Esto ayuda a localizar la petición, el módulo y la causa sin exponer detalles técnicos. El endpoint de salud permitirá comprobar si la aplicación está funcionando.

Por qué no se seleccionan mensajes básicos en consola: los registros quedarían desorganizados y sería difícil seguir una misma solicitud cuando pasa por diferentes componentes.

Por qué no se selecciona inicialmente una plataforma completa con OpenTelemetry: proporciona información más detallada, pero requiere herramientas adicionales de almacenamiento, visualización y mantenimiento que aumentan la complejidad y el costo.

Por qué no se selecciona únicamente un servicio externo: generaría dependencia de otro proveedor y podría enviar información sensible fuera del sistema si no se configura correctamente.

**Prueba de concepto:** [`/pocs/logs-traceid`](../pocs/logs-traceid) — Se comprobó la correlación de logs entre módulos mediante un traceId único, y que el usuario final solo recibe el código de seguimiento sin detalles técnicos.

---

### ADR-011 — Documentación de la API con OpenAPI/Swagger y versionamiento por URL.

**Fecha de propuesta:** 31/08/2026  
**Fecha de aprobación:** 04/09/2026  
**Estado:** Aprobado  
**Participantes:** Arquitecto

**Contexto:**
El frontend y el backend serán desarrollados como componentes separados. Ambos necesitan conocer los endpoints, parámetros, respuestas, errores y requisitos de autenticación. Además, los cambios futuros no deben romper inmediatamente a los clientes existentes.

**Alternativas consideradas:**
- Documentación informal: explicar los endpoints mediante mensajes o notas.
- Documento manual: registrar las operaciones en un archivo o tabla.
- Colecciones de Postman o Bruno: guardar solicitudes de ejemplo para probar la API.
- OpenAPI y Swagger: definir formalmente los endpoints, parámetros, respuestas, errores y requisitos de seguridad.

**Decisión:**
Documentar la API REST de BarberSpot mediante OpenAPI y utilizar Swagger UI para consultar y probar los endpoints en los ambientes de desarrollo y pruebas. El acceso a Swagger será restringido o deshabilitado en producción.

**Consecuencias:**
Positivas: El frontend conoce claramente los endpoints, datos, errores y permisos de la API. También facilita realizar pruebas.

Negativas: La documentación debe actualizarse cuando cambie la API y su acceso debe limitarse en producción.

**Justificación:**
Por qué se selecciona: OpenAPI crea un contrato estructurado para la comunicación entre frontend y backend. Swagger UI permite consultar y probar los endpoints, visualizar los formatos de los datos y conocer los códigos de respuesta y requisitos de autenticación.

Por qué no se selecciona documentación informal: la información puede quedar dispersa, incompleta o depender de lo que recuerde cada integrante.

Por qué no se selecciona únicamente un documento manual: tendría que actualizarse separadamente del código y podría quedar desactualizado cuando cambie un endpoint.

Por qué no se seleccionan únicamente colecciones de Postman o Bruno: son útiles para ejecutar pruebas y conservar ejemplos, pero no describen de manera completa y estandarizada todos los esquemas, errores y reglas de seguridad de la API.

---

### ADR-012 — Protección de credenciales mediante HashiCorp Vault y variables de entorno.

**Fecha de propuesta:** 31/08/2026  
**Fecha de aprobación:** 04/09/2026  
**Estado:** Aceptado  
**Participantes:** Arquitecto

**Contexto:**
BarberSpot utilizará información sensible como la contraseña de PostgreSQL, la clave privada de Brevo, los tokens de despliegue y otras credenciales. Estos valores no deben aparecer en el código ni quedar publicados en el repositorio. También deben poder cambiarse según el ambiente sin modificar la aplicación.

**Alternativas consideradas:**
- Credenciales dentro del código: escribir las claves directamente en los archivos de la aplicación.
- Archivos de configuración versionados: guardar las claves en archivos .env o de propiedades incluidos en el repositorio.
- Variables de entorno y secretos de cada plataforma: configurar las credenciales separadamente en Railway, Cloudflare y GitHub.
- HashiCorp Vault con variables de entorno: centralizar los secretos en Vault y utilizar variables de entorno para configurar el acceso desde la aplicación.
- Azure Key Vault o AWS Secrets Manager: almacenar las credenciales en un servicio asociado a una nube específica.

**Decisión:**
Utilizar HashiCorp Vault como baúl central para almacenar las credenciales sensibles. El backend accederá a Vault durante su ejecución y las variables de entorno se utilizarán para indicar la dirección y configuración necesaria para conectarse. El token inicial de acceso a Vault se almacenará en el mecanismo de secretos de Railway. Los archivos locales .env estarán excluidos del repositorio y solamente se entregará un .env.example sin valores reales.

**Consecuencias:**
Consecuencias positivas: Mantiene las credenciales fuera del código y permite configuraciones distintas por ambiente.

Consecuencias negativas: Los secretos quedan distribuidos y deben rotarse y protegerse correctamente.

**Justificación:**
Por qué se selecciona: HashiCorp Vault centraliza las credenciales, permite controlar quién puede consultarlas, registrar los accesos y cambiar los secretos sin modificar el código. Las variables de entorno permiten configurar la conexión según el ambiente sin publicar información sensible.

Por qué no se incluyen las credenciales en el código: cualquier persona con acceso al repositorio podría obtenerlas y cambiar una clave exigiría modificar y desplegar nuevamente la aplicación.

Por qué no se utilizan archivos versionados: aunque facilitan la configuración, podrían exponer accidentalmente las credenciales en GitHub y no proporcionan control de acceso ni auditoría.

Por qué no se utilizan únicamente los secretos de las plataformas: las credenciales quedarían distribuidas entre varios proveedores, dificultando su administración, rotación y seguimiento desde un lugar central.

Por qué no se seleccionan Azure Key Vault o AWS Secrets Manager: generarían dependencia de nubes que no fueron elegidas para publicar BarberSpot.

**Prueba de concepto:** [`/pocs/vault`](../pocs/vault) — Se comprobó el guardado y la lectura de una credencial cifrada mediante la API REST de Vault (modo desarrollo local).

---

### ADR-013 — Protección de la información mediante copias de seguridad automáticas de PostgreSQL.

**Fecha de propuesta:** 31/08/2026  
**Fecha de aprobación:** 05/09/2026  
**Estado:** Aprobado  
**Participantes:** Cliente Arquitecto

**Contexto:**
BarberSpot almacenará usuarios, servicios, horarios, citas e historial en PostgreSQL. Una eliminación accidental, un error durante una actualización o una falla del proveedor podría ocasionar la pérdida de información. El sistema necesita un procedimiento que permita recuperar los datos sin depender de que una persona recuerde hacer copias manualmente.

**Alternativas consideradas:**
- Sin copias adicionales: depender únicamente de la base de datos activa en Supabase.
- Copias manuales: exportar la información cuando un integrante lo considere necesario.
- Copias automáticas programadas: generar respaldos periódicos y conservar varias versiones.
- Replicación en tiempo real: mantener una segunda base de datos sincronizada permanentemente.

**Decisión:**
Configurar copias de seguridad automáticas diarias de PostgreSQL y conservar varias versiones. También se realizará periódicamente una prueba de restauración para comprobar que los respaldos puedan recuperarse. Se utilizarán las copias ofrecidas por Supabase si el plan seleccionado las incluye; de lo contrario, se programarán exportaciones automáticas hacia un almacenamiento separado.

**Consecuencias:**
Positivas: Permite recuperar usuarios, horarios y citas después de una falla o eliminación accidental.

Negativas: Las copias ocupan espacio y deben revisarse. Con una copia diaria podrían perderse hasta 24 horas de cambios.

**Justificación:**
Por qué se selecciona: las copias automáticas reducen la dependencia de tareas humanas y permiten recuperar una versión anterior después de una eliminación, corrupción o falla. Probar la restauración confirma que el respaldo realmente puede utilizarse.

Por qué no se selecciona trabajar sin copias adicionales: si la base de datos activa se elimina o corrompe, BarberSpot podría perder definitivamente las citas y el historial.

Por qué no se seleccionan únicamente copias manuales: pueden olvidarse, realizarse con poca frecuencia o quedar almacenadas de manera insegura.

Por qué no se selecciona replicación en tiempo real: requiere más infraestructura y costo. Además, una eliminación accidental también podría replicarse inmediatamente, por lo que no reemplaza los respaldos históricos.

---

### ADR-014 — Selección de una API REST para la comunicación entre frontend y backend.

**Fecha de propuesta:** 31/08/2026  
**Fecha de aprobación:** 05/09/2026  
**Estado:** Aprobado  
**Participantes:** Cliente Arquitecto

**Contexto:**
el frontend de BarberSpot necesita comunicarse con el backend para consultar barberos, servicios y horarios, además de agendar, cancelar y reprogramar citas. La comunicación debe ser compatible con navegadores, utilizar formatos conocidos y permitir aplicar autenticación y permisos.

**Alternativas consideradas:**
- API REST: utiliza HTTP, direcciones por recursos, métodos como GET, POST, PUT y DELETE, y normalmente intercambia datos en JSON.
- GraphQL: permite que el frontend solicite exactamente los campos que necesita mediante consultas.
- SOAP: utiliza contratos formales y mensajes principalmente en XML.
- gRPC: utiliza Protocol Buffers y comunicación binaria de alto rendimiento.

**Decisión:**
Construir una API REST con Spring Boot, utilizando HTTPS y JSON. Los endpoints representarán recursos como usuarios, barberos, servicios, horarios y citas, y emplearán los métodos y códigos de respuesta estándar de HTTP.

**Consecuencias:**
Positivas: permite una comunicación sencilla entre React y Spring Boot, funciona directamente en los navegadores y facilita probar los endpoints con Swagger, Postman o Bruno.

Negativas: se deben organizar y documentar correctamente los endpoints. Algunos cambios futuros pueden exigir crear una nueva versión de la API y REST no actualiza la agenda en tiempo real por sí solo, por lo que se complementará con SSE.

**Justificación:**
Por qué se selecciona: REST se adapta a las operaciones de BarberSpot, que consisten principalmente en consultar, registrar y actualizar recursos. Es compatible directamente con React, navegadores y Spring Boot, y facilita las pruebas mediante Swagger, Postman o Bruno.

Por qué no se selecciona GraphQL: ofrece consultas flexibles, pero requiere definir esquemas, resolutores y controles adicionales para limitar consultas complejas. BarberSpot no necesita que el frontend combine grandes cantidades de información variable.

Por qué no se selecciona SOAP: sus mensajes XML y contratos agregan complejidad y mayor cantidad de información transmitida. Sus características son más apropiadas para integraciones empresariales que requieren estándares específicos.

Por qué no se selecciona gRPC: ofrece alto rendimiento entre servicios, pero su uso desde navegadores requiere configuraciones o intermediarios adicionales. BarberSpot necesita una comunicación web sencilla y no posee múltiples servicios internos que justifiquen esta tecnología.

---

### ADR-015 — Distribución de contenido estático mediante Cloudflare CDN.

**Fecha de propuesta:** 31/08/2026  
**Fecha de aprobación:** 05/09/2026  
**Estado:** Aprobado  
**Participantes:** Cliente Arquitecto

**Contexto:**
BarberSpot debe cargar rápidamente el frontend, estilos, scripts e imágenes en celulares y computadores. Servir todos los archivos desde un único servidor puede aumentar la latencia y el consumo del backend.

**Alternativas consideradas:**
- Sin CDN: servir todos los archivos directamente desde el servidor de origen.
- Cloudflare CDN: distribuir y almacenar en caché el contenido estático desde ubicaciones cercanas a los usuarios.
- Amazon CloudFront: utilizar la red de distribución de AWS.
- Fastly: utilizar una CDN administrada con funciones avanzadas de entrega y caché.

**Decisión:**
Utilizar Cloudflare CDN junto con Cloudflare Pages para distribuir el frontend y los archivos estáticos de BarberSpot.

**Consecuencias:**
Positivas: El sitio y sus imágenes pueden cargar más rápido y el servidor principal recibe menos trabajo.

Negativas: Se deben controlar los archivos guardados temporalmente para que los usuarios reciban las versiones actualizadas.

**Justificación:**
Por qué se selecciona: Cloudflare CDN ya se integra con Cloudflare Pages, ofrece HTTPS y distribuye el contenido sin añadir otra plataforma. Esta capacidad se activa automáticamente al usar Cloudflare Pages (ADR-007), pero se documenta como una decisión independiente para dejar explícito su rol dentro de la arquitectura.

Por qué no se selecciona esta opción (trabajar sin CDN): todas las solicitudes llegarían al mismo origen, aumentando el tiempo de carga y el trabajo del servidor.

Por qué no se selecciona esta opción (Amazon CloudFront): es una alternativa adecuada, pero agregaría una cuenta y configuración de AWS que el proyecto no necesita.

Por qué no se selecciona esta opción (Fastly): también distribuye contenido, pero añade otro proveedor sin una ventaja necesaria para el volumen inicial.

---

### ADR-016 — Filtrado de tráfico malicioso mediante Cloudflare WAF

**Fecha de propuesta:** 31/08/2026  
**Fecha de aprobación:** 05/09/2026  
**Estado:** Aprobado  
**Participantes:** Cliente Arquitecto

**Contexto:**
La API y el sitio estarán expuestos a internet y pueden recibir solicitudes maliciosas, automatizadas o excesivas. BarberSpot necesita filtrar tráfico antes de que llegue al backend.

**Alternativas consideradas:**
- Sin WAF: depender únicamente de las validaciones del backend.
- Cloudflare WAF: aplicar reglas administradas, restricciones y controles de tráfico en el perímetro.
- AWS WAF: proteger los recursos mediante el servicio de AWS.
- ModSecurity: instalar y administrar reglas de firewall en infraestructura propia.

**Decisión:**
Utilizar Cloudflare WAF con reglas administradas y limitación de solicitudes para filtrar el tráfico entrante antes de que llegue al API Gateway, que es el único punto de entrada hacia el frontend y el backend de BarberSpot.

**Consecuencias:**
Positivas: Ayuda a bloquear solicitudes peligrosas o repetitivas antes de que lleguen al backend.

Negativas: Una regla incorrecta puede bloquear a un usuario legítimo y algunas funciones pueden tener costo.

**Justificación:**
Por qué se selecciona: Cloudflare WAF se integra con la plataforma usada para publicar el frontend y permite aplicar reglas de protección desde un mismo lugar.

Por qué no se selecciona esta opción (trabajar sin WAF): dejaría toda la protección en el backend y permitiría que más solicitudes dañinas consumieran sus recursos.

Por qué no se selecciona esta opción (AWS WAF): la infraestructura principal no está alojada en AWS y se añadiría otro proveedor.

Por qué no se selecciona esta opción (ModSecurity): obligaría al equipo a instalar, actualizar y mantener manualmente las reglas de protección.

---

### ADR-017 — Almacenamiento de imágenes mediante Supabase Storage como Blob Storage.

**Fecha de propuesta:** 31/08/2026  
**Fecha de aprobación:** 05/09/2026  
**Estado:** Aprobado  
**Participantes:** Cliente Arquitecto

**Contexto:**
BarberSpot puede almacenar fotografías de barberos, imágenes de servicios y otros archivos. Guardar estos objetos directamente en PostgreSQL aumentaría el tamaño de la base de datos y dificultaría su entrega.

**Alternativas consideradas:**
- Guardar archivos en PostgreSQL: almacenar los bytes dentro de la base de datos relacional.
- Supabase Storage: utilizar buckets y políticas de acceso integradas con el entorno de Supabase.
- Amazon S3: almacenar objetos en el servicio de AWS.
- Cloudflare R2: utilizar almacenamiento de objetos integrado con Cloudflare.

**Decisión:**
Utilizar Supabase Storage como Blob Storage, con buckets separados y políticas que controlen quién puede cargar, modificar o consultar los archivos.

**Consecuencias:**
Positivas: Las fotografías se guardan separadas de las citas y pueden crecer sin aumentar innecesariamente la base de datos.

Negativas: Se deben controlar los permisos y eliminar los archivos que ya no estén relacionados con un barbero o servicio.

**Justificación:**
Por qué se selecciona: Supabase Storage se administra junto con la base de datos elegida y permite crear espacios separados con reglas de acceso.

Por qué no se selecciona esta opción (guardar las imágenes dentro de PostgreSQL): aumentaría el tamaño de la base de datos y haría más pesadas las copias de seguridad.

Por qué no se selecciona esta opción (Amazon S3): es un servicio sólido, pero agregaría una plataforma diferente para una necesidad que Supabase ya puede cubrir.

Por qué no se selecciona esta opción (Cloudflare R2): también podría funcionar, pero repartiría la información entre más servicios y aumentaría la administración.

---

### ADR-018 — Centralización del acceso a la API mediante Kong API Gateway.

**Fecha de propuesta:** 31/08/2026  
**Fecha de aprobación:** 05/09/2026  
**Estado:** Aprobado  
**Participantes:** Arquitecto

**Contexto:**
El frontend necesita un punto único para acceder al backend. También se deben centralizar tareas de soporte como enrutamiento, CORS, límites de solicitudes y políticas de acceso sin repetirlas en todos los endpoints.

**Alternativas consideradas:**
- Acceso directo al backend: publicar Spring Boot sin un intermediario.
- Kong API Gateway: centralizar rutas y políticas mediante plugins y configuración declarativa.
- Spring Cloud Gateway: construir el gateway dentro del ecosistema Spring.
- Cloudflare como proxy: utilizar únicamente las capacidades de proxy y seguridad del proveedor perimetral.

**Decisión:**
Utilizar Kong como API Gateway entre el frontend y el backend, configurando rutas, CORS, límites de solicitudes y validaciones comunes.

**Consecuencias:**
Positivas: La API tiene un único punto de entrada donde se controlan rutas, permisos y cantidad de solicitudes.

Negativas: Se agrega otro componente que debe configurarse, publicarse y mantenerse disponible.

**Justificación:**
Por qué se selecciona: Kong permite organizar las rutas y aplicar controles comunes sin repetirlos en cada parte del backend.

Por qué no se selecciona esta opción (el acceso directo al backend): expondría la aplicación y repartiría los controles entre diferentes endpoints.

Por qué no se selecciona esta opción (Spring Cloud Gateway): obligaría a desarrollar y mantener otro componente en Spring cuando Kong ya ofrece esas funciones preparadas.

Por qué no se selecciona esta opción (usar solamente Cloudflare como proxy): protege el acceso externo, pero no organiza con el mismo detalle las reglas propias de la API.

---

### ADR-019 — Redis como caché de catálogos

**Fecha de propuesta:** 31/08/2026  
**Fecha de aprobación:** 05/09/2026  
**Estado:** Aceptado  
**Participantes:** Arquitecto

**Contexto:**
BarberSpot necesita consultar frecuentemente los catálogos de mensajes, parámetros y notificaciones durante sus operaciones. Realizar todas estas consultas directamente a PostgreSQL puede generar lecturas repetitivas y aumentar el tiempo de respuesta cuando varios usuarios utilizan el sistema simultáneamente. Se requiere reducir esas consultas sin comprometer la integridad de la información. Los cambios en los catálogos deben reflejarse oportunamente y una falla de la caché no debe impedir el funcionamiento del sistema. PostgreSQL debe continuar siendo la fuente principal de los datos.

**Alternativas consideradas:**
- Consulta directa a PostgreSQL: Obtener los mensajes, parámetros y definiciones de notificación desde la base de datos en cada consulta. Simplifica la infraestructura, pero mantiene las lecturas repetitivas.
- Caché local con Caffeine:Conservar temporalmente los catálogos en la memoria del backend. Ofrece acceso rápido sin otro servidor, pero cada instancia mantiene una copia independiente y pierde la caché al reiniciarse.
- Caché compartida con Redis:Conservar temporalmente los catálogos en un servicio accesible por las instancias del backend, manteniendo PostgreSQL como fuente principal. Permite compartir la caché, pero requiere administrar conexión, expiración, invalidación y recuperación ante fallas.
- Upstash Redis (Servicio de caché administrado en la nube):Es una plataforma que ofrece bases de datos Redis alojadas en la nube bajo un modelo Serverless (pago por uso según el número de peticiones). Se conecta mediante el protocolo estándar de Redis y no requiere instalar ni administrar un servidor propio.

**Decisión:**
Utilizar Redis como caché compartida para los catálogos de mensajes, parámetros y notificaciones, manteniendo PostgreSQL como fuente principal. Aplicar el patrón cache-aside, con tiempo de expiración e invalidación después de confirmar cambios en la base de datos. Si Redis no está disponible, consultar directamente PostgreSQL. La caché no se utilizará como autoridad para confirmar reservas ni permisos.

**Consecuencias:**
Positivas: Reduce las consultas repetitivas a PostgreSQL y puede mejorar el tiempo de respuesta. Permite compartir la caché entre instancias del backend. Si Redis falla, el sistema puede continuar consultando PostgreSQL.

Negativas: Agrega un componente que requiere configuración, recursos y mantenimiento. Una invalidación incorrecta puede devolver datos desactualizados. Cuando Redis no esté disponible, aumentarán las consultas a PostgreSQL y podría disminuir el rendimiento.

**Justificación:**
Por qué se selecciona Redis:Permite almacenar temporalmente los catálogos y reducir consultas repetitivas a PostgreSQL. También permite compartir la caché si el backend utiliza varias instancias, manteniendo PostgreSQL como fuente principal.

Por qué no se selecciona únicamente la consulta directa a PostgreSQL:Es una opción más sencilla, pero conserva todas las lecturas repetitivas y no incorpora la reducción de carga buscada.

Por qué no se selecciona Caffeine: Cada instancia mantiene su propia copia, lo que exige coordinación adicional para reflejar cambios entre instancias. Es una alternativa válida si el backend permanece en una sola instancia.

Por qué no se selecciona Upstash Redis: aunque es económico y fácil de configurar, al ser un servicio externo agrega una pequeña demora en cada consulta por viajar a través de internet, y su plan gratuito tiene un límite diario de peticiones que podría agotarse en un día de mucho movimiento, generando fallas o costos inesperados.

**Prueba de concepto:** [`/pocs/redis`](../pocs/redis) — Se comprobó el patrón cache-aside (cache miss -> guarda, cache hit -> lee) y el comportamiento de respaldo si Redis no está disponible.

---

### ADR-020 — Catálogo centralizado de mensajes

**Fecha de propuesta:** 31/08/2026  
**Fecha de aprobación:** 05/09/2026  
**Estado:** Aprobado  
**Participantes:** Arquitecto

**Contexto:**
BarberSpot debe mostrar mensajes claros al confirmar operaciones, validar información o informar errores durante el agendamiento, la cancelación y la reprogramación de citas. Mantener estos textos dispersos en diferentes partes del código puede producir mensajes repetidos, contradictorios o difíciles de actualizar. Se necesita una definición centralizada que permita identificar cada mensaje mediante un código estable y mantener una comunicación uniforme con los usuarios. Los textos deben poder modificarse de manera controlada, sin revelar detalles técnicos ni información sensible.

**Alternativas consideradas:**
- Mensajes directamente en el código:Escribir los textos dentro de controllers, servicios o componentes. Es sencillo inicialmente, pero dificulta mantener uniformidad y exige modificar el código para actualizar los mensajes.
- Archivos de recursos versionados:Organizar los códigos y textos en archivos de propiedades o JSON incluidos con la aplicación. Centraliza los mensajes, pero los cambios requieren publicar una nueva versión.
- Catálogo de mensajes en PostgreSQL:Almacenar códigos estables, textos, plantillas y estado en tablas administradas mediante funciones autorizadas. Permite cambios controlados sin recompilar, pero requiere validación, permisos y mensajes de respaldo ante indisponibilidad.

**Decisión:**
Implementar un catálogo de mensajes en PostgreSQL con código estable, texto o plantilla y estado activo. El backend resolverá los mensajes mediante este catálogo y permitirá su modificación únicamente a administradores autorizados. Validar las variables de las plantillas y conservar mensajes mínimos de respaldo para responder cuando el catálogo no esté disponible.

**Consecuencias:**
Positivas:Mantiene mensajes uniformes y reutilizables. Permite actualizar los textos sin recompilar la aplicación, conservando sus códigos estables. Facilita ofrecer respuestas comprensibles sin exponer detalles técnicos.

Negativas:Requiere administrar permisos, validar plantillas y mantener los códigos utilizados por la aplicación. Un cambio incorrecto puede generar mensajes confusos o incompletos. Se necesitan mensajes de respaldo cuando el catálogo no esté disponible.

**Justificación:**
Por qué se selecciona PostgreSQL:Permite centralizar códigos, textos y plantillas, y modificarlos mediante funciones administrativas autorizadas sin recompilar, siempre que se mantenga el contrato de códigos utilizado por la aplicación.

Por qué no se seleccionan mensajes directamente en el código:Dispersan los textos, favorecen inconsistencias y obligan a modificar y publicar la aplicación para actualizarlos.

Por qué no se seleccionan archivos de recursos versionados:Organizan los mensajes, pero sus cambios requieren publicar una nueva versión y no permiten la administración durante la operación prevista para BarberSpot.

---

### ADR-021 — Catálogo de parámetros de negocio

**Fecha de propuesta:** 31/08/2026  
**Fecha de aprobación:** 05/09/2026  
**Estado:** Aprobado  
**Participantes:** Arquitecto

**Contexto:**
BarberSpot necesita aplicar reglas de negocio que pueden cambiar durante su operación, como la anticipación mínima permitida para cancelar o reprogramar una cita. Mantener estos valores directamente en el código obligaría a modificar y publicar la aplicación cada vez que el negocio solicite un ajuste. Se requiere administrar los parámetros de manera centralizada y controlada, verificando sus tipos, rangos y coherencia antes de aceptar cambios. Solo los usuarios autorizados deben modificarlos y las credenciales de los servicios no deben almacenarse como parámetros de negocio.

**Alternativas consideradas:**
- Valores fijos en el código:Definir directamente las reglas, como la anticipación mínima para cancelar una cita. Facilita la implementación inicial, pero cada ajuste requiere modificar, probar y publicar la aplicación.
- Archivos de configuración o variables de entorno: Mantener los valores fuera del código y configurarlos por ambiente. Evita recompilar para cambiar valores, pero normalmente requiere reiniciar o redesplegar y depende de personal técnico.
- Catálogo de parámetros en PostgreSQL:Almacenar código, tipo, valor, unidad y restricciones de cada parámetro, con edición administrativa autorizada. Permite ajustes durante la operación, pero requiere validar coherencia y controlar cuándo entran en vigor.

**Decisión:**
Implementar un catálogo de parámetros de negocio en PostgreSQL con código, tipo, valor, unidad, restricciones y versión. Permitir cambios mediante funciones administrativas autorizadas, validando su formato, rango y coherencia antes de guardarlos. Los casos de uso aplicarán los valores vigentes y los cambios invalidarán la caché correspondiente. Las credenciales permanecerán en Vault, fuera de este catálogo.

**Consecuencias:**
Positivas:Permite ajustar las reglas configurables del negocio sin modificar el código. Centraliza sus valores y facilita comprobar tipos, rangos y versiones. Limita los cambios a administradores autorizados.

Negativas: Un valor incorrecto puede afectar operaciones como la cancelación o reprogramación. Requiere validaciones de coherencia y control de entrada en vigor. Si se utiliza caché, debe evitarse que las operaciones apliquen valores desactualizados.

**Justificación:**
Por qué se selecciona PostgreSQL: Permite administrar reglas configurables mediante valores tipados, restricciones y versiones. El administrador puede realizar ajustes autorizados sin modificar el código, conservando controles sobre su validez.

Por qué no se seleccionan valores fijos en el código: Cada cambio de regla exige desarrollo, pruebas y publicación, incluso cuando solo cambia un valor permitido.

Por qué no se seleccionan archivos de configuración o variables de entorno: Son adecuados para configuración técnica, pero normalmente requieren intervención técnica y reinicio o redespliegue. No ofrecen por sí solos la administración de reglas de negocio prevista.

---

### ADR-022 — Catálogo de notificaciones

**Fecha de propuesta:** 31/08/2026  
**Fecha de aprobación:** 05/09/2026  
**Estado:** Aprobado  
**Participantes:** Arquitecto

**Contexto:**
BarberSpot debe notificar al cliente y al barbero cuando una cita sea agendada, cancelada o reprogramada. Cada evento requiere definir el contenido del aviso, sus destinatarios y los datos necesarios para construirlo. Mantener estas definiciones dispersas en el código dificulta su actualización y puede generar comunicaciones inconsistentes. Se necesita organizar los tipos de notificación y sus plantillas de manera centralizada, con cambios controlados. Esta definición debe mantenerse separada del mecanismo de envío y del registro de las notificaciones pendientes, enviadas o fallidas.

**Alternativas consideradas:**
- Definiciones dentro del código: Construir el asunto, contenido y reglas de cada aviso directamente en los servicios.
- Plantillas en archivos versionados: Mantener las plantillas de correo en archivos separados del código.
- Plantillas administradas en Brevo: Gestionar el contenido en el proveedor y referenciar sus identificadores desde el backend.
- Catálogo de notificaciones en PostgreSQL: Centralizar tipos de evento, canal, plantilla o referencia de Brevo, versión y variables permitidas.

**Decisión:**
Implementar un catálogo de notificaciones en PostgreSQL para los eventos de agendamiento, cancelación y reprogramación de citas. Registrar el canal de correo, la referencia de plantilla de Brevo, su versión, las variables permitidas y los roles destinatarios. El backend determinará los destinatarios concretos y utilizará el Notification Gateway para el envío.

**Consecuencias:**
Positivas:Centraliza los tipos de aviso y mantiene coherencia en contenido, variables y destinatarios. Facilita actualizar plantillas de manera controlada y separa su definición del mecanismo de envío.

Negativas:Requiere coordinar las versiones del catálogo con las plantillas de Brevo. Una plantilla inexistente o incompatible puede impedir el envío, aunque la cita permanezca registrada.

**Justificación:**
Por qué se selecciona PostgreSQL: Permite organizar los tipos de aviso, referencias de plantillas, versiones, variables y roles destinatarios. Mantiene estas definiciones bajo control de BarberSpot y separadas del transporte de correo.

Por qué no se seleccionan definiciones dentro del código: Dificultan mantener uniformidad y requieren modificar y publicar la aplicación para actualizar las definiciones.

Por qué no se seleccionan únicamente archivos versionados: Facilitan revisión, pero sus cambios requieren una nueva publicación y no ofrecen la administración durante la operación prevista.

Por qué no se selecciona depender únicamente de Brevo:Las plantillas de Brevo se utilizarán para el diseño visual del correo HTML, pero no sustituyen el catálogo local que relaciona qué evento del negocio activa la notificación, cuáles variables son obligatorias y qué rol debe recibir el mensaje.

---

## 10. Diagramas de arquitectura

### 10.1 Diagramas de componentes

![Diagrama de componentes de backend](diagrams/components/components-backend.png)

![Diagrama de componentes de frontend](diagrams/components/components-frontend.png)

### 10.2 Diagramas de paquetes

![Diagrama de paquetes de appointments](diagrams/packages/packages-appointments.png)

![Diagrama de paquetes de services](diagrams/packages/packages-services.png)

![Diagrama de paquetes de barbers](diagrams/packages/packages-barbers.png)

![Diagrama de paquetes de shared](diagrams/packages/packages-shared.png)

![Diagrama de paquetes de frontend](diagrams/packages/packages-frontend.png)

### 10.3 Diagrama de secuencia

![Diagrama de secuencia](diagrams/sequence/sequence-book-appointment.png)
