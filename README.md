# LiftFlux

LiftFlux permite organizar rutinas, registrar entrenamientos y consultar el progreso. El MVP incluye una aplicación Android nativa para usuarios y una aplicación Windows nativa para administrar el catálogo global.

## Estado del repositorio

Esta rama contiene proyectos iniciales de Android, Windows y NestJS. Todavía no implementa autenticación, reglas de negocio, bases de datos ni despliegues. Los otros cinco servicios son directorios reservados; no son servicios ejecutables.

## Arquitectura objetivo

| Componente | Tecnología | Responsabilidad |
| --- | --- | --- |
| Android | Kotlin, Jetpack Compose | Cuenta, perfil, rutinas, entrenamiento e historial |
| Administración Windows | C#, WPF, .NET 10 | Gestión del catálogo global con rol admin y MFA |
| Seis servicios | TypeScript, NestJS 11, Node.js 24 | Identidad de aplicación, perfil, catálogo, rutinas, entrenamiento y estadísticas |
| Datos | PostgreSQL 18 y Prisma 7 | Una instancia y migraciones propias por servicio |
| Identidad y archivos | Supabase Auth y Storage | Contraseñas, sesiones y objetos; los datos de negocio quedan en las bases propias |
| Integración | REST/HTTPS y RabbitMQ | Consultas síncronas y eventos con outbox/inbox |
| Operación | Linux, Docker Compose, Caddy y observabilidad | Despliegue y diagnóstico de cada servicio |

El MVP no incluye cliente iOS o web. Los objetivos de rendimiento y disponibilidad son criterios por medir, no resultados actuales.

## Estructura

```text
apps/
  android/                # Proyecto Android nativo
  admin-windows/          # Proyecto WPF
services/
  auth-service/           # Proyecto NestJS inicial
  profile-service/        # Pendiente de crear
  catalog-service/        # Pendiente de crear
  routine-service/        # Pendiente de crear
  workout-service/        # Pendiente de crear
  stats-service/          # Pendiente de crear
docs/
  adr/                    # Decisiones arquitectónicas
infra/
  local/                  # Preparación del entorno local
```

Cada servicio tendrá su propia configuración, base, migraciones, contratos y despliegue. No se comparten tablas ni entidades ORM entre servicios. Las interfaces públicas se versionarán bajo `/api/v1`.

## Documentación

- [Alcance funcional del MVP](docs/mvp-scope.md)
- [Impulsores y objetivos de calidad](docs/architecture-drivers.md)
- [Decisiones arquitectónicas](docs/adr/)
- [Guía de contribución](CONTRIBUTING.md)

El documento de arquitectura v2 es la referencia de la transición. Los ADR indican qué decisiones están confirmadas, cuáles son propuestas técnicas y qué asuntos siguen pendientes.
