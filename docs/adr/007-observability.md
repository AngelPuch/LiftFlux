# ADR 007 Observabilidad y auditoría

- **Estado:** Definido por delegación; despliegue pendiente.
- **Fecha:** 23 de septiembre de 2026

## Contexto

Los fallos distribuidos necesitan correlación sin enviar datos privados a logs. Una auditoría de roles, administración y bajas debe durar aunque falle la telemetría.

## Decisión

No habrá un servicio de negocio para logs. Los servicios emiten JSON estructurado con Pino a stdout y trazas/métricas mediante OTLP/HTTP Protobuf. Un OpenTelemetry Collector por host envía logs a Loki y trazas a Tempo; Prometheus recoge métricas; Grafana presenta paneles y alertas. La auditoría duradera vive en la base del servicio dueño.

No se registran tokens, contraseñas, correos completos, notas, series completas ni URLs firmadas. Retención técnica inicial: logs 14 días, trazas 7 y métricas 30, ajustable a capacidad y política de datos.

## Consecuencias

La telemetría usa recursos propios y puede perderse si se agota su buffer. Su caída no bloquea el negocio. Los almacenes de observabilidad necesitan volúmenes, acceso restringido y respaldos de configuración.

## Pendiente

Crear configuración y paneles, medir capacidad y concretar retención de auditoría conforme a la política de privacidad.
