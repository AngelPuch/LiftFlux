# ADR 006 REST y eventos

- **Estado:** Definido por delegación; contratos e infraestructura pendientes.
- **Fecha:** 23 de septiembre de 2026

## Contexto

Los clientes necesitan respuestas interactivas; los servicios necesitan propagar hechos confirmados sin compartir bases.

## Decisión

REST/JSON sobre HTTPS sirve clientes y consultas internas. Los contratos usan OpenAPI, `/api/v1`, errores estables, paginación, timeout e idempotencia de escrituras. Los enlaces internos se autentican con mTLS. No se introduce gRPC ni WebSocket para el MVP.

RabbitMQ 4 con AMQP/TLS entrega eventos durables. La transacción local registra negocio y outbox; un worker publica con confirms. Cada consumidor registra `event_id` en inbox y aplica su cambio en una transacción. La entrega es al menos una vez y no garantiza orden global. Versiones de agregado y tombstones resuelven duplicados, retrasos y borrados.

## Consecuencias

Las proyecciones son eventualmente consistentes. Un broker detenido no bloquea una confirmación local mientras quede capacidad de outbox. Reintentos, colas de fallos, alerta y reproceso forman parte de la operación.

## Pendiente

Publicar contratos concretos y probar caída entre commit y envío, mensajes duplicados/fuera de orden, mTLS, permisos de broker y restauración.
