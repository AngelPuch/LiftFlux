# LiftFlux

Aplicación multiplataforma para registrar, organizar y consultar entrenamientos de gimnasio.

LiftFlux permitirá crear rutinas, registrar sesiones de entrenamiento, llevar el seguimiento de series, repeticiones y pesos, y consultar el progreso del usuario. Posteriormente incorporará animaciones para explicar la ejecución correcta de los ejercicios.

## Plataformas

- Android
- iOS
- Web

## Stack tecnológico

### Aplicación

- Flutter
- Dart

### Backend

- NestJS
- TypeScript

### Datos e infraestructura

- PostgreSQL
- Prisma ORM
- Supabase
- Docker

## Arquitectura

El sistema utilizará un monolito modular dentro de un monorepositorio.

```text
LiftFlux/
├── apps/
│   ├── mobile/    # Aplicación Flutter
│   └── api/       # API NestJS
├── docs/          # Documentación
└── infra/         # Configuración de infraestructura
```

## Documentation

- [Development workflow](CONTRIBUTING.md)
- [MVP scope](docs/mvp-scope.md)
- [Architecture drivers](docs/architecture-drivers.md)
