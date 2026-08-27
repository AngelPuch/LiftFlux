# Alcance del MVP de LiftFlux

- **Estado:** Aprobado
- **Versión objetivo:** `1.0.0`
- **Última actualización:** 25 de agosto de 2026

## 1. Propósito

LiftFlux es una aplicación móvil para organizar rutinas, registrar entrenamientos de gimnasio y consultar el progreso del usuario.

El MVP debe cerrar completamente el siguiente ciclo:

> Registrarse → configurar el perfil → crear una rutina → iniciar un entrenamiento → registrar series → finalizar → consultar el historial y las estadísticas.

El MVP se considerará funcional cuando un usuario pueda completar este flujo sin intervención manual del administrador ni modificaciones directas en la base de datos.

## 2. Actores

### 2.1 Usuario

Es el actor principal de LiftFlux.

Puede:

- Administrar su cuenta y perfil.
- Consultar ejercicios.
- Crear ejercicios personalizados.
- Crear y administrar rutinas.
- Registrar entrenamientos.
- Consultar su historial.
- Consultar estadísticas básicas.

### 2.2 Administrador de contenido

Es un actor interno encargado de mantener el catálogo global de ejercicios.

Puede:

- Crear ejercicios globales.
- Editar ejercicios.
- Asignar grupos musculares y equipo.
- Administrar instrucciones e imágenes.
- Agregar animaciones cuando estén disponibles.
- Activar o desactivar ejercicios.

El administrador no podrá consultar los entrenamientos privados de los usuarios.

Durante el MVP no se desarrollará un panel administrativo independiente. Las operaciones administrativas podrán realizarse mediante endpoints protegidos de la API y Swagger.

## 3. Alcance funcional

### 3.1 Autenticación y cuenta

El usuario podrá:

- Registrarse con correo electrónico y contraseña.
- Verificar su correo electrónico.
- Iniciar sesión con correo y contraseña.
- Iniciar sesión con Google.
- Recuperar su contraseña.
- Mantener una sesión segura.
- Cerrar sesión.
- Eliminar su cuenta y sus datos.

Reglas:

- Un correo solamente puede pertenecer a una cuenta.
- Cada usuario solamente puede acceder a sus propios datos.
- Existirán los roles `user` y `admin`.
- Las credenciales no serán almacenadas directamente por LiftFlux.
- Los tokens de sesión se almacenarán de forma segura en el dispositivo.

### 3.2 Onboarding

Después del primer inicio de sesión, el usuario deberá configurar:

- Nombre de usuario único.
- Sistema de unidades: kilogramos o libras.
- Peso corporal inicial.
- Objetivo principal.

Objetivos disponibles:

- Ganar masa muscular.
- Ganar fuerza.
- Perder grasa.
- Mejorar condición física.
- Mantenerse activo.

Datos opcionales:

- Nombre.
- Fecha de nacimiento.
- Sexo o género.
- Estatura.

Los datos opcionales no bloquearán el onboarding porque todavía no serán utilizados para recomendaciones inteligentes.

Reglas:

- El nombre de usuario será único sin distinguir mayúsculas y minúsculas.
- El onboarding se mostrará hasta que los campos obligatorios sean completados.
- El usuario podrá cambiar posteriormente esta información.

### 3.3 Perfil y peso corporal

El usuario podrá:

- Consultar su información personal.
- Cambiar su nombre de usuario.
- Cambiar su objetivo.
- Modificar el sistema de unidades.
- Registrar un nuevo peso corporal.
- Consultar sus registros anteriores de peso.
- Cerrar sesión.
- Eliminar su cuenta.

Cada actualización del peso corporal generará un registro con:

- Peso.
- Unidad utilizada.
- Fecha y hora.

El peso anterior no será sobrescrito.

Las gráficas avanzadas de evolución quedan fuera del MVP.

### 3.4 Catálogo de ejercicios

Cada ejercicio global podrá contener:

- Nombre.
- Grupo muscular principal.
- Músculos secundarios.
- Tipo de equipo.
- Instrucciones en texto.
- Imagen opcional.
- Animación opcional.
- Estado activo o inactivo.

El usuario podrá:

- Consultar el catálogo.
- Buscar ejercicios por nombre.
- Filtrar por grupo muscular.
- Filtrar por equipo.
- Consultar los detalles e instrucciones.
- Seleccionar ejercicios para sus rutinas.

Grupos musculares iniciales:

- Pecho.
- Espalda.
- Hombros.
- Bíceps.
- Tríceps.
- Piernas.
- Glúteos.
- Pantorrillas.
- Abdomen.
- Cuerpo completo.
- Cardio.

El MVP no dependerá de tener animaciones para todos los ejercicios.

La opción “Cómo ejecutarlo” utilizará inicialmente instrucciones e imágenes. Se podrá incluir un pequeño grupo piloto de animaciones Rive sin bloquear la publicación del MVP.

### 3.5 Ejercicios personalizados

El usuario podrá crear ejercicios privados con:

- Nombre.
- Grupo muscular.
- Tipo de equipo.
- Descripción opcional.
- Imagen opcional.

Reglas:

- Solamente serán visibles para su propietario.
- Podrán utilizarse en rutinas y entrenamientos.
- Podrán editarse o desactivarse.
- No se convertirán automáticamente en ejercicios globales.

### 3.6 Gestión de rutinas

El usuario podrá:

- Crear una rutina.
- Asignarle nombre.
- Agregar una descripción opcional.
- Agregar ejercicios.
- Eliminar ejercicios.
- Reordenar ejercicios.
- Definir series objetivo.
- Definir repeticiones objetivo.
- Configurar un descanso sugerido.
- Editar una rutina.
- Duplicar una rutina.
- Eliminar una rutina.
- Iniciar un entrenamiento desde una rutina.

Las repeticiones objetivo podrán manejarse como:

- Valor fijo: `10`.
- Rango: `8-12`.

Una rutina representa una plantilla. Las modificaciones posteriores no deben alterar entrenamientos finalizados.

### 3.7 Entrenamiento activo

Al iniciar un entrenamiento, LiftFlux creará una sesión basada en una rutina.

El usuario podrá:

- Consultar los ejercicios de la sesión.
- Ver series anteriores como referencia.
- Registrar peso y repeticiones.
- Marcar una serie como completada.
- Agregar series.
- Eliminar series.
- Editar series antes de finalizar.
- Agregar ejercicios durante el entrenamiento.
- Eliminar o sustituir ejercicios.
- Escribir notas generales.
- Escribir notas por ejercicio.
- Utilizar un temporizador de descanso.
- Pausar, reiniciar u omitir el temporizador.
- Finalizar el entrenamiento.
- Cancelar el entrenamiento con confirmación.

Cada serie completada almacenará:

- Ejercicio.
- Orden de la serie.
- Peso utilizado.
- Repeticiones realizadas.
- Fecha y hora.
- Estado completado.
- Nota opcional.

Solo podrá existir un entrenamiento activo por usuario.

### 3.8 Recuperación del entrenamiento

La sesión activa deberá guardarse localmente para evitar pérdida de información.

El usuario podrá recuperarla si:

- Cierra accidentalmente la aplicación.
- Envía la aplicación a segundo plano.
- Pierde temporalmente la conexión.
- Reinicia la aplicación.

El MVP no tendrá sincronización offline completa.

Solamente se garantizará la persistencia local del entrenamiento activo y su sincronización cuando exista conexión.

### 3.9 Finalización del entrenamiento

Antes de guardar, LiftFlux mostrará:

- Nombre de la rutina.
- Fecha.
- Duración.
- Ejercicios realizados.
- Series completadas.
- Volumen total.
- Récords personales obtenidos.
- Notas.

El usuario podrá:

- Confirmar y guardar.
- Regresar para realizar correcciones.
- Descartar el entrenamiento mediante confirmación.

Cuando el entrenamiento sea finalizado, se guardará una copia histórica de sus ejercicios y series.

Editar posteriormente una rutina o un ejercicio no modificará los entrenamientos anteriores.

### 3.10 Historial

Los entrenamientos se mostrarán del más reciente al más antiguo.

Cada elemento del historial mostrará:

- Fecha.
- Nombre de la rutina.
- Duración.
- Número de ejercicios.
- Series completadas.
- Volumen total.

El detalle mostrará:

- Ejercicios realizados.
- Orden de ejecución.
- Peso y repeticiones de cada serie.
- Notas.
- Récords obtenidos.

En el MVP, los entrenamientos finalizados no podrán editarse.

El usuario podrá eliminarlos mediante una confirmación si fueron registrados por error.

### 3.11 Estadísticas básicas

El MVP incluirá:

- Total de entrenamientos.
- Entrenamientos realizados durante la semana.
- Tiempo total entrenado.
- Volumen total levantado.
- Volumen por entrenamiento.
- Último entrenamiento.
- Récord de mayor peso por ejercicio.
- Historial de peso corporal.

El volumen se calculará utilizando solamente series completadas:

`volumen = suma de (peso × repeticiones)`

En el MVP, un récord personal será el mayor peso completado en un ejercicio.

Los récords avanzados se implementarán posteriormente.

### 3.12 Administración de ejercicios

El administrador podrá:

- Crear ejercicios globales.
- Editar ejercicios.
- Agregar instrucciones.
- Asignar músculos.
- Asignar equipo.
- Agregar imágenes o animaciones.
- Activar o desactivar ejercicios.

Un ejercicio utilizado en un entrenamiento no podrá eliminarse permanentemente. Se marcará como inactivo para conservar la integridad del historial.

## 4. Reglas de negocio principales

1. Cada nombre de usuario debe ser único.
2. Cada usuario solamente puede acceder a sus datos.
3. Solo puede existir un entrenamiento activo por usuario.
4. Solamente las series completadas cuentan para estadísticas.
5. El volumen se calcula con peso y repeticiones completadas.
6. Cambiar de kg a lb modifica la presentación, no el valor físico almacenado.
7. Los datos históricos no cambian al editar una rutina.
8. Los ejercicios personalizados son privados.
9. Los administradores administran el catálogo, no los entrenamientos privados.
10. Los ejercicios globales utilizados se desactivan en lugar de eliminarse.
11. El temporizador no impide registrar o modificar series.
12. Cancelar o eliminar información requiere confirmación.

## 5. Fuera del MVP

Las siguientes funcionalidades quedan explícitamente fuera:

- Generación de rutinas mediante inteligencia artificial.
- Chatbot o entrenador virtual.
- Recomendaciones personalizadas.
- Funciones sociales.
- Seguidores y publicaciones.
- Rutinas públicas o compartidas.
- Competencias y clasificaciones.
- Perfiles para entrenadores o gimnasios.
- Superseries y circuitos.
- Drop sets.
- RIR y RPE.
- Calculadora de discos.
- Integración con relojes inteligentes.
- Fotos de progreso.
- Exportación avanzada de datos.
- Gráficas avanzadas.
- Sistema offline completo.
- Panel web administrativo completo.
- Animaciones para todo el catálogo.
- Notificaciones distintas al temporizador.
- Estadísticas y récords avanzados.

Una funcionalidad incluida en esta lista necesita una nueva decisión de alcance antes de incorporarse al MVP.

## 6. Criterios de éxito

El MVP estará completo cuando un usuario pueda:

1. Crear y verificar una cuenta.
2. Completar el onboarding.
3. Consultar o crear ejercicios.
4. Crear una rutina.
5. Iniciar un entrenamiento.
6. Registrar peso y repeticiones.
7. Cerrar y recuperar la sesión activa.
8. Finalizar el entrenamiento.
9. Consultarlo en el historial.
10. Ver volumen y récords básicos.

También deberá cumplirse que:

- Otro usuario no pueda consultar sus datos.
- Editar una rutina no modifique el historial.
- Una interrupción no elimine el entrenamiento activo.
- Los administradores puedan mantener el catálogo.
- Las validaciones automáticas estén aprobadas.
- El flujo principal funcione en un dispositivo Android real o emulado.

## 7. Etapas de entrega

| Versión | Entregable                                           |
| ------- | ---------------------------------------------------- |
| `0.1.0` | Autenticación, onboarding y perfil                   |
| `0.2.0` | Catálogo, ejercicios personalizados y administración |
| `0.3.0` | Creación y gestión de rutinas                        |
| `0.4.0` | Entrenamiento activo, persistencia y temporizador    |
| `0.5.0` | Historial, peso corporal y estadísticas              |
| `1.0.0` | Integración, seguridad, pruebas y MVP estable        |

Cada versión debe ser funcional dentro de su alcance y aprobar las validaciones de CI.

## 8. Definition of Done del MVP

El MVP podrá etiquetarse como `v1.0.0` cuando:

- El flujo principal funcione de principio a fin.
- Los criterios de aceptación estén cubiertos.
- No existan errores críticos conocidos.
- Las pruebas unitarias y de integración relevantes estén aprobadas.
- Flutter pase formato, análisis y pruebas.
- NestJS pase lint, pruebas y compilación.
- Los datos de diferentes usuarios estén aislados.
- Las sesiones activas puedan recuperarse.
- La documentación esté actualizada.
- La aplicación haya sido probada en Android.
- La API se encuentre desplegada en un ambiente de prueba.
- La base de datos tenga migraciones reproducibles.
- Los secretos no estén almacenados en Git.
