# ADR 001 Seis microservicios y propiedad de datos

- **Estado:** Decisión arquitectónica confirmada por el propietario; implementación pendiente.
- **Fecha:** 23 de septiembre de 2026

## Contexto

El diseño anterior describía un monolito modular. La dirección vigente separa dominios, datos y despliegues dentro del mismo monorepo.

## Decisión

Habrá seis servicios: `auth-service`, `profile-service`, `catalog-service`, `routine-service`, `workout-service` y `stats-service`. Cada uno poseerá una instancia PostgreSQL distinta, credenciales, migraciones, volumen, imagen y ciclo de despliegue propios. No habrá tablas, entidades ORM, joins ni usuarios SQL compartidos entre servicios. La comunicación será mediante contratos REST o eventos.

## Consecuencias

Se pueden desplegar versiones compatibles por separado. Aumentan el costo de operación, respaldos, certificados, recursos y fallos parciales. Seis contenedores PostgreSQL en un host siguen compartiendo el fallo de ese host. `auth-service` es una dependencia síncrona de las operaciones privadas.

## Pendiente

Crear los cinco servicios que hoy son directorios reservados; demostrar aislamiento de credenciales y despliegue independiente; medir capacidad antes de producción.
