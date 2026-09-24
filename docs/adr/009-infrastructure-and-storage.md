# ADR 009 Infraestructura y almacenamiento de objetos

- **Estado:** Servidor propio y Supabase Storage confirmados; Compose, Caddy y controles concretos definidos por delegación; capacidad pendiente.
- **Fecha:** 23 de septiembre de 2026

## Contexto

Los datos de negocio deben permanecer en infraestructura propia y los archivos necesitan almacenamiento de objetos. El MVP comienza con presupuesto y equipo limitados.

## Decisión

Linux con Docker Engine y Compose ejecuta servicios, bases independientes, RabbitMQ, Caddy y observabilidad. Solo el gateway publica HTTPS; bases y broker quedan en red privada. Cada servicio puede actualizarse sin reiniciar el conjunto. Secretos se montan desde archivos protegidos fuera de imágenes y Git. Supabase Storage conserva objetos en buckets privados, separados por entorno, bajo autorización y metadatos de `catalog-service`.

El catálogo valida propietario/rol, licencia, tamaño y tipo real antes de publicar. Emite URL firmada breve para lectura autorizada y reconcilia archivos huérfanos. Un respaldo de PostgreSQL no incluye por sí mismo los objetos externos.

## Consecuencias

Compose y un host único no ofrecen alta disponibilidad. Se necesitan respaldos fuera del host, inventario de objetos, restauración, rotación de certificados y límites de recursos. No se introduce Kubernetes en el MVP.

## Pendiente

Definir CPU, RAM, disco, ubicación, presupuesto, dominio, certificados, destino de respaldos, topología de producción y plan/región de Supabase.
