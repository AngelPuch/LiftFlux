# Impulsores arquitectónicos de LiftFlux

- **Estado:** Aprobado
- **Versión:** 1.0
- **Última actualización:** 27 de agosto de 2026
- **Alcance:** MVP `v1.0.0`

## 1. Propósito

Este documento define las condiciones que dirigirán las decisiones técnicas de LiftFlux.

Incluye:

- Atributos de calidad.
- Escenarios medibles.
- Restricciones del proyecto.
- Trade-offs aceptados.
- Preocupaciones y riesgos.
- Decisiones pendientes.

El alcance funcional está definido en [mvp-scope.md](mvp-scope.md).

Los valores numéricos aquí establecidos son objetivos iniciales del MVP. Podrán ajustarse cuando existan pruebas realizadas con datos y ambientes representativos, pero cualquier cambio deberá documentarse.

## 2. Contexto del sistema

LiftFlux será inicialmente una aplicación móvil orientada a Android.

Su flujo principal será:

> Autenticarse → configurar el perfil → crear una rutina → registrar un entrenamiento → finalizarlo → consultar historial y estadísticas.

Los principales componentes previstos son:

- Aplicación móvil desarrollada con Flutter.
- API desarrollada con NestJS.
- Base de datos relacional.
- Almacenamiento local para recuperar el entrenamiento activo.
- Proveedor de autenticación.
- Almacenamiento de imágenes y animaciones.
- Infraestructura Linux para la API y servicios asociados.

La selección concreta de la base de datos, proveedor de autenticación, almacenamiento y alojamiento se documentará posteriormente.

## 3. Prioridad de los atributos de calidad

| Prioridad      | Atributo                             | Justificación                                                                              |
| -------------- | ------------------------------------ | ------------------------------------------------------------------------------------------ |
| Crítica        | Seguridad y privacidad               | La aplicación almacena cuentas, peso corporal e historial de entrenamiento                 |
| Crítica        | Integridad y confiabilidad de datos  | Perder o alterar una sesión afecta directamente la utilidad del producto                   |
| Crítica        | Usabilidad                           | La aplicación debe poder utilizarse durante un entrenamiento sin interrumpir al usuario    |
| Alta           | Rendimiento                          | Registrar una serie y navegar durante el entrenamiento debe sentirse inmediato             |
| Alta           | Mantenibilidad y capacidad de prueba | El sistema crecerá por módulos y posiblemente por más de una plataforma                    |
| Media          | Disponibilidad y recuperación        | La API puede fallar, pero el entrenamiento activo no debe perderse                         |
| Media          | Observabilidad                       | Los errores deben poder diagnosticarse sin acceder a información privada                   |
| Media          | Portabilidad                         | Aunque el MVP será Android, el diseño no debe impedir una versión futura para iOS o web    |
| Media          | Escalabilidad                        | El MVP no requiere escala masiva, pero debe permitir crecimiento sin reescribir el sistema |
| Baja en el MVP | Funcionalidad completamente offline  | Solo se garantizará recuperación local del entrenamiento activo                            |

## 4. Escenarios de calidad

### QA-01. Autorización y aislamiento de datos

- **Fuente:** Usuario autenticado.
- **Estímulo:** Intenta consultar o modificar un recurso perteneciente a otro usuario.
- **Ambiente:** Operación normal.
- **Respuesta esperada:** La API rechaza la operación sin revelar el contenido ni confirmar información innecesaria.
- **Medida:** El 100 % de los endpoints privados valida autenticación, propietario y rol. Las pruebas de autorización deben obtener `401` o `403` según corresponda.

Aplica a:

- Perfil.
- Peso corporal.
- Ejercicios personalizados.
- Rutinas.
- Entrenamientos activos.
- Historial.
- Estadísticas.

### QA-02. Protección de credenciales y sesiones

- Las contraseñas no se almacenarán en texto plano.
- Los tokens no se guardarán en almacenamiento inseguro del dispositivo.
- Los secretos no se incluirán en Git, código fuente ni logs.
- El cierre de sesión invalidará o eliminará las credenciales locales.
- Los tokens de acceso tendrán una duración limitada.
- Las comunicaciones entre la aplicación y la API utilizarán HTTPS fuera del ambiente local.

La implementación exacta dependerá del proveedor de autenticación seleccionado.

### QA-03. Recuperación del entrenamiento activo

- **Fuente:** Sistema operativo, usuario o fallo de red.
- **Estímulo:** La aplicación se cierra, reinicia o pierde conexión durante un entrenamiento.
- **Respuesta esperada:** La sesión se recupera desde el almacenamiento local.
- **Medidas:**
  - La sesión reaparece en un máximo de 3 segundos después de abrir la aplicación.
  - No se pierde ninguna serie que ya haya sido marcada como completada.
  - Como máximo podría perderse el valor de un campo que todavía no hubiera sido confirmado.
  - La sincronización se reintenta cuando vuelve la conexión.

### QA-04. Integridad del historial

- **Fuente:** Usuario o administrador.
- **Estímulo:** Se modifica una rutina o se desactiva un ejercicio usado anteriormente.
- **Respuesta esperada:** El entrenamiento histórico conserva su contenido original.
- **Medida:** El 100 % de los entrenamientos finalizados mantiene sus ejercicios, orden, series, peso, repeticiones y nombres históricos.

Los entrenamientos finalizados utilizarán datos históricos independientes de las plantillas actuales.

### QA-05. Consistencia de unidades

- El sistema tendrá una unidad canónica de almacenamiento.
- Cambiar entre kilogramos y libras modificará la presentación, no el valor físico representado.
- Las conversiones usarán una única regla de redondeo.
- Cambiar de unidad repetidamente no debe degradar progresivamente el valor almacenado.
- Las estadísticas utilizarán la unidad canónica antes de presentar el resultado.

La unidad canónica y precisión exacta se definirán con el modelo de datos.

### QA-06. Rendimiento de la API

En un ambiente de prueba equivalente a `staging`, utilizando datos representativos:

- El percentil 95 de las consultas comunes será menor o igual a 500 ms.
- El percentil 95 de las operaciones comunes de escritura será menor o igual a 800 ms.
- Ninguna consulta común excederá 3 segundos en condiciones normales.
- El escenario inicial de prueba será de al menos 50 usuarios concurrentes.

Operaciones comunes:

- Consultar perfil.
- Buscar ejercicios.
- Listar rutinas.
- Consultar historial paginado.
- Registrar o sincronizar una serie.
- Finalizar un entrenamiento.

Las mediciones excluirán el tiempo propio de proveedores externos, pero ese tiempo deberá registrarse por separado.

### QA-07. Respuesta de la aplicación móvil

- Una acción local, como marcar una serie, mostrará respuesta visual en menos de 100 ms.
- Una pantalla con información disponible localmente se mostrará en menos de 1 segundo.
- Una operación remota normal mostrará resultado o estado de carga en menos de 2 segundos.
- Una operación que tarde más deberá mostrar progreso y permitir recuperación ante error.
- El temporizador deberá seguir funcionando aunque el usuario navegue entre pantallas de la aplicación.

### QA-08. Usabilidad durante el entrenamiento

El usuario deberá poder:

- Registrar una serie desde la pantalla activa sin navegar a otro módulo.
- Distinguir claramente una serie pendiente de una completada.
- Corregir peso o repeticiones antes de finalizar.
- Continuar registrando mientras funciona el temporizador.
- Cancelar acciones destructivas.
- Utilizar objetivos táctiles suficientemente grandes.
- Comprender errores mediante mensajes concretos y recuperables.

El flujo principal se validará mediante una prueba manual completa en un dispositivo Android real o emulado.

### QA-09. Disponibilidad y recuperación

Para el MVP desplegado:

- Objetivo de disponibilidad mensual de la API: 99 %.
- Se realizarán respaldos automáticos de la base de datos.
- Objetivo inicial de recuperación de datos, `RPO`: máximo 24 horas.
- Objetivo inicial de recuperación del servicio, `RTO`: máximo 4 horas.
- El procedimiento de restauración deberá probarse antes de publicar `v1.0.0`.
- La indisponibilidad de la API no deberá borrar el entrenamiento activo almacenado localmente.

Estos objetivos podrán mejorar al preparar una versión pública con usuarios reales.

### QA-10. Mantenibilidad

- Flutter y NestJS se dividirán por funcionalidades o módulos de negocio.
- Un módulo no accederá directamente a detalles internos de otro módulo.
- Las reglas de negocio no dependerán directamente de componentes visuales.
- Las migraciones de base de datos serán reproducibles.
- Todo cambio en lógica crítica incluirá pruebas.
- Flutter deberá aprobar formato, análisis estático y pruebas.
- NestJS deberá aprobar lint, pruebas y compilación.
- Los workflows normales de CI deberán finalizar idealmente en menos de 10 minutos.

No se utilizará un porcentaje global de cobertura como único indicador de calidad. Se priorizarán las pruebas de reglas críticas y casos de error.

### QA-11. Observabilidad

La API deberá registrar:

- Fecha y hora.
- Nivel del evento.
- Identificador de la solicitud.
- Ruta o módulo.
- Código de respuesta.
- Duración.
- Error técnico cuando corresponda.

No se registrarán:

- Contraseñas.
- Tokens.
- Contenido completo de encabezados de autenticación.
- Datos personales innecesarios.
- Notas privadas del entrenamiento.

La API tendrá al menos un endpoint de salud para comprobar su disponibilidad.

### QA-12. Portabilidad

- El MVP se probará y distribuirá inicialmente para Android.
- La lógica de negocio de Flutter evitará dependencias innecesarias de Android.
- Cualquier integración nativa se encapsulará detrás de una interfaz.
- El backend no dependerá de una plataforma móvil específica.
- La futura compatibilidad con iOS o web no obliga a implementar esas plataformas durante el MVP.

### QA-13. Escalabilidad

El MVP utilizará una arquitectura sencilla, pero deberá permitir:

- Ejecutar más de una instancia de la API.
- Mantener la API sin estado de sesión local permanente.
- Paginar catálogos e historial.
- Indexar campos utilizados en búsqueda y relaciones.
- Mover imágenes y animaciones a almacenamiento de objetos.
- Separar tareas costosas en procesos asíncronos cuando sea necesario.

No se implementarán microservicios ni autoescalado durante la primera etapa sin evidencia que los justifique.

## 5. Restricciones

Las siguientes condiciones ya están establecidas y deberán respetarse:

| ID   | Restricción                                                                                                     |
| ---- | --------------------------------------------------------------------------------------------------------------- |
| R-01 | La aplicación móvil se desarrollará con Flutter                                                                 |
| R-02 | La API se desarrollará con NestJS y TypeScript                                                                  |
| R-03 | El código móvil y de API permanecerá inicialmente en el monorepo LiftFlux                                       |
| R-04 | El MVP tendrá Android como plataforma objetivo                                                                  |
| R-05 | El entorno del servidor será compatible con Linux                                                               |
| R-06 | El proyecto utilizará GitHub Flow                                                                               |
| R-07 | Los commits seguirán Conventional Commits                                                                       |
| R-08 | Las publicaciones seguirán Semantic Versioning                                                                  |
| R-09 | Todo Pull Request deberá aprobar las validaciones aplicables de CI                                              |
| R-10 | Las credenciales, secretos y archivos de ambiente no se almacenarán en Git                                      |
| R-11 | El MVP será desarrollado inicialmente por una persona, por lo que debe evitar complejidad operativa innecesaria |
| R-12 | El administrador usará inicialmente endpoints protegidos y Swagger; no habrá panel administrativo completo      |
| R-13 | El MVP no tendrá funcionamiento completamente offline                                                           |
| R-14 | Solo podrá existir un entrenamiento activo por usuario                                                          |
| R-15 | El presupuesto inicial de infraestructura debe mantenerse bajo y crecer con el uso                              |

## 6. Decisiones arquitectónicas iniciales

### 6.1 Backend modular

La API comenzará como un monolito modular de NestJS.

Módulos iniciales previstos:

- Autenticación.
- Usuarios y perfiles.
- Peso corporal.
- Ejercicios.
- Rutinas.
- Entrenamientos.
- Historial.
- Estadísticas.
- Administración.

Cada módulo tendrá responsabilidades y dependencias explícitas.

La separación en microservicios solamente se considerará si existen problemas medidos de:

- Escalabilidad independiente.
- Despliegue independiente.
- Carga operativa.
- Aislamiento de fallos.
- Organización de equipos.

### 6.2 Aplicación organizada por funcionalidades

Flutter se organizará por funcionalidades, evitando una carpeta global excesivamente dividida solo por tipos técnicos.

Ejemplo conceptual:

- `auth`
- `onboarding`
- `profile`
- `exercises`
- `routines`
- `workouts`
- `history`
- `statistics`

Cada funcionalidad podrá contener sus componentes de presentación, dominio y acceso a datos.

### 6.3 API sin estado

La API no dependerá de la memoria de una instancia para conservar la sesión del usuario.

Esto permitirá:

- Reiniciar la API sin perder sesiones de entrenamiento.
- Ejecutar varias instancias posteriormente.
- Facilitar despliegues y escalamiento.

### 6.4 Datos históricos protegidos

Al finalizar un entrenamiento se conservará una representación histórica de los datos necesarios.

No se dependerá únicamente del nombre o configuración actual del ejercicio o de la rutina.

## 7. Trade-offs aceptados

### T-01. Monolito modular frente a microservicios

**Decisión:** usar un monolito modular.

**Beneficios:**

- Menor complejidad de desarrollo y despliegue.
- Transacciones más sencillas.
- Depuración y pruebas más fáciles.
- Adecuado para un desarrollador y una primera versión.

**Costo aceptado:**

- Los módulos no podrán escalarse o desplegarse de forma independiente al inicio.

La separación interna conservará la posibilidad de extraer un módulo posteriormente.

### T-02. Persistencia local frente a sistema offline completo

**Decisión:** guardar localmente el entrenamiento activo, sin replicar toda la aplicación.

**Beneficios:**

- Evita perder el entrenamiento.
- Reduce considerablemente la complejidad de sincronización.
- Permite entregar antes el flujo principal.

**Costo aceptado:**

- Catálogo, rutinas, historial y estadísticas pueden requerir conexión.
- No se garantiza edición simultánea desde varios dispositivos.

### T-03. Un entrenamiento activo frente a sincronización concurrente

**Decisión:** permitir un solo entrenamiento activo por usuario.

**Beneficios:**

- Reduce conflictos y duplicados.
- Simplifica recuperación y estadísticas.
- Hace predecible la sincronización.

**Costo aceptado:**

- No se soportará ejecutar sesiones simultáneas en varios dispositivos.

### T-04. Copias históricas frente a normalización total

**Decisión:** conservar datos históricos necesarios al finalizar una sesión.

**Beneficios:**

- Editar una rutina no altera el pasado.
- Los nombres e instrucciones históricas permanecen consistentes.
- Las estadísticas pueden reproducirse.

**Costo aceptado:**

- Existirá cierta duplicación controlada de datos.
- Se requerirán reglas claras para definir qué campos se copian.

### T-05. Unidad canónica frente a almacenar la preferencia directamente

**Decisión:** almacenar valores físicos en una unidad canónica y convertirlos para mostrarlos.

**Beneficios:**

- Estadísticas consistentes.
- Evita mezclar kg y lb.
- Permite cambiar la preferencia sin reescribir el historial.

**Costo aceptado:**

- Se necesita conversión y redondeo en presentación.

### T-06. Swagger frente a panel administrativo

**Decisión:** utilizar Swagger y endpoints protegidos para administrar ejercicios.

**Beneficios:**

- Reduce el alcance visual del MVP.
- Permite mantener el catálogo desde la primera versión.

**Costo aceptado:**

- La administración será menos amigable.
- Solo deberá utilizarla personal técnico autorizado.

### T-07. Instrucciones e imágenes frente a animaciones completas

**Decisión:** las instrucciones en texto serán obligatorias y los recursos visuales opcionales.

**Beneficios:**

- El catálogo puede completarse antes.
- Reduce costos de producción y almacenamiento.
- Las animaciones pueden incorporarse gradualmente.

**Costo aceptado:**

- La experiencia inicial de “Cómo ejecutarlo” será menos visual para algunos ejercicios.

### T-08. Android primero frente a lanzamiento multiplataforma

**Decisión:** desarrollar y validar el MVP en Android.

**Beneficios:**

- Reduce pruebas, configuración y publicación simultánea.
- Permite concentrarse en el flujo de entrenamiento.

**Costo aceptado:**

- iOS y web no estarán disponibles en `v1.0.0`.

Flutter y la separación de responsabilidades conservarán la posibilidad de agregarlos posteriormente.

### T-09. Seguridad frente a fricción

**Decisión:** aplicar verificación, sesiones limitadas y confirmaciones en operaciones sensibles.

**Beneficios:**

- Protege cuentas y datos privados.
- Reduce eliminación accidental.

**Costo aceptado:**

- Algunas operaciones necesitarán un paso adicional.

No se agregarán controles que interrumpan innecesariamente el registro de series.

### T-10. Estadísticas calculadas frente a infraestructura analítica

**Decisión:** calcular inicialmente estadísticas a partir de los entrenamientos almacenados, usando consultas e índices adecuados.

**Beneficios:**

- Menor infraestructura.
- Una sola fuente de verdad.
- Correcciones más sencillas durante el MVP.

**Costo aceptado:**

- Algunas consultas podrían volverse costosas al crecer el historial.

Solo se agregarán agregados precalculados o procesos asíncronos cuando las mediciones lo justifiquen.

## 8. Preocupaciones y riesgos

| ID   | Preocupación                                              | Impacto | Tratamiento inicial                                                         |
| ---- | --------------------------------------------------------- | ------- | --------------------------------------------------------------------------- |
| P-01 | Proveedor de autenticación todavía no seleccionado        | Alto    | Comparar soporte de correo, Google, Flutter, NestJS, costos y portabilidad  |
| P-02 | Base de datos no seleccionada formalmente                 | Alto    | Evaluar integridad relacional, migraciones, respaldos, alojamiento y costos |
| P-03 | Conflictos de sincronización entre dispositivo y servidor | Alto    | Un entrenamiento activo, versión de sesión e idempotencia                   |
| P-04 | Pérdida de una sesión por cierre de la aplicación         | Alto    | Persistencia local después de cada serie confirmada                         |
| P-05 | Acceso horizontal a datos de otro usuario                 | Crítico | Autorización por recurso y pruebas negativas                                |
| P-06 | Eliminación o edición que altere estadísticas pasadas     | Alto    | Historial inmutable y desactivación lógica                                  |
| P-07 | Definición ambigua de récord y volumen                    | Medio   | Centralizar fórmulas y documentar reglas                                    |
| P-08 | Errores acumulados al convertir kg y lb                   | Medio   | Unidad canónica y precisión fija                                            |
| P-09 | Uso de imágenes o animaciones sin derechos                | Alto    | Utilizar contenido propio, autorizado o con licencia compatible             |
| P-10 | Crecimiento descontrolado del alcance                     | Alto    | Usar `mvp-scope.md` como criterio de entrada                                |
| P-11 | Costos de almacenamiento de recursos multimedia           | Medio   | Comprimir, limitar formatos y cargar recursos bajo demanda                  |
| P-12 | Eliminación de cuenta y datos asociados                   | Alto    | Definir política de borrado, anonimización y retención                      |
| P-13 | Respaldos existentes pero no restaurables                 | Alto    | Probar restauración antes de `v1.0.0`                                       |
| P-14 | Logs con información privada                              | Alto    | Sanitización centralizada y revisión de logs                                |
| P-15 | Catálogo inconsistente de músculos y equipo               | Medio   | Catálogos controlados y validaciones                                        |
| P-16 | Configuración distinta entre desarrollo y producción      | Medio   | Variables de ambiente documentadas y validación al iniciar                  |
| P-17 | Dependencia excesiva de un proveedor                      | Medio   | Encapsular autenticación, archivos y notificaciones                         |
| P-18 | Errores por fecha, duración o zona horaria                | Medio   | Guardar instantes en UTC y presentar en hora local                          |
| P-19 | Creación inicial y protección del administrador           | Alto    | Procedimiento seguro de asignación de rol; nunca desde registro público     |
| P-20 | Estadísticas lentas al crecer el historial                | Medio   | Paginación, índices, medición y optimización progresiva                     |

## 9. Decisiones pendientes

Las siguientes decisiones todavía no están aprobadas:

1. Proveedor de autenticación.
2. Base de datos y ORM.
3. Estrategia exacta de tokens y renovación.
4. Almacenamiento local de Flutter.
5. Mecanismo de sincronización e idempotencia.
6. Proveedor de alojamiento.
7. Almacenamiento de imágenes y animaciones.
8. Librería de administración de estado de Flutter.
9. Unidad canónica, precisión y redondeo.
10. Política de eliminación de cuenta.
11. Nivel mínimo de Android.
12. Herramientas de monitoreo y reporte de errores.

Cada decisión relevante deberá documentarse mediante un Architecture Decision Record, ADR.

## 10. Validación durante el desarrollo

Una historia no se considerará terminada únicamente porque funcione visualmente.

Según corresponda, deberá comprobar:

- Autenticación.
- Autorización y propiedad del recurso.
- Validación de entradas.
- Manejo de errores.
- Persistencia.
- Recuperación.
- Rendimiento.
- Accesibilidad básica.
- Logs seguros.
- Pruebas automatizadas.
- Comportamiento sin conexión temporal.

Las historias del backlog deberán referenciar los identificadores de calidad aplicables, por ejemplo:

- `QA-01` para aislamiento de datos.
- `QA-03` para recuperación del entrenamiento.
- `QA-04` para integridad histórica.
- `QA-08` para usabilidad durante la sesión.

## 11. Revisión del documento

Este documento se revisará cuando:

- Cambie el alcance del MVP.
- Se seleccione infraestructura o un proveedor externo.
- Una medición demuestre que un objetivo no es realista.
- Se detecte un riesgo arquitectónico nuevo.
- Se prepare una publicación mayor.
- Se incorpore otra plataforma.
