# ADR 010 Historial y sincronización del entrenamiento

- **Estado:** Derivado de reglas funcionales confirmadas; protocolos concretos definidos por delegación; implementación pendiente.
- **Fecha:** 23 de septiembre de 2026

## Contexto

La persona debe continuar una sesión iniciada durante cortes de red sin duplicar series o perder un historial al confirmar.

## Decisión

Iniciar una sesión requiere conexión para reservar en `workout-service` un único entrenamiento activo por usuario. Android guarda localmente ID, propietario, versión y operaciones estables antes de aceptar series. Una sola instalación escribe la sesión activa. Las operaciones usan `operation_id`, secuencia y versión esperada; un reintento con el mismo contenido devuelve el resultado anterior.

`workout-service` conserva juntos sesión e historial. Al finalizar, guarda copias históricas, volumen, récord logrado y evento outbox en una transacción. La respuesta no espera a `stats-service`. Sin red, Android muestra una finalización local pendiente y conserva el borrador hasta registrar la confirmación remota. `stats-service` proyecta hechos confirmados y puede reconstruirse.

## Consecuencias

No se permite iniciar otra sesión mientras una finalización o cancelación local siga sin confirmarse. No hay fusión concurrente entre dispositivos. El historial finalizado no se edita; borrarlo exige actualizar proyecciones sin reescribir insignias históricas.

## Pendiente

Cerrar reglas decimales y de récords, límites de duración, transferencia de dispositivo y pruebas de respuesta perdida, conflicto, evento duplicado, borrado tardío y muerte del proceso. QA-03 fija 5 s de aceptación y 3 s como meta interna para recuperación local.
