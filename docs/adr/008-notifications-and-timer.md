# ADR 008 Notificaciones y temporizador

- **Estado:** Definido por delegación; implementación pendiente.
- **Fecha:** 23 de septiembre de 2026

## Contexto

El MVP necesita avisar que termina un descanso, enviar correos técnicos de cuenta y alertar al operador.

## Decisión

No se crea `notification-service` en el MVP. El temporizador y sus avisos son locales de Android; Supabase Auth coordina verificación y recuperación mediante SMTP; Grafana emite alertas operativas. Android guarda el estado del temporizador, usa reloj monotónico mientras el dispositivo está activo y programa avisos con APIs de plataforma. WorkManager reintenta sincronización, no cuenta segundos.

## Consecuencias

La precisión del aviso en segundo plano depende de permisos y restricciones del sistema. No se promete alarma exacta tras forzar la detención de la app. Los correos requieren un proveedor SMTP de producción; el envío predeterminado de desarrollo no cubre ese uso.

## Pendiente

Elegir SMTP/dominio, validar permisos de notificación y alarmas, y probar pausas, reinicios, duplicados y recuperación tras reinicio del dispositivo.
