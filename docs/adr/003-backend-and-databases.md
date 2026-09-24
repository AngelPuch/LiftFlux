# ADR 003 Backend y acceso a datos

- **Estado:** NestJS, TypeScript y PostgreSQL confirmados; Node.js 24, NestJS 11, PostgreSQL 18 y Prisma 7 definidos por delegación; compatibilidad exacta pendiente de prueba.
- **Fecha:** 23 de septiembre de 2026

## Contexto

Los seis servicios comparten un equipo de desarrollo pequeño, pero no modelo interno ni base. Se necesitan migraciones reproducibles y transacciones locales.

## Decisión

Usar TypeScript estricto, NestJS 11 y Node.js 24 para cada servicio. Cada uno usa PostgreSQL 18 independiente y Prisma 7 con `@prisma/adapter-pg` y `pg`. La configuración de módulos debe ser coherente con la generación del cliente. Se eligen migraciones revisables y SQL parametrizado para restricciones, índices parciales o bloqueos que Prisma no modele.

En staging y producción se exige TLS con validación de CA y nombre de servidor, SCRAM-SHA-256, credenciales exclusivas y pools acotados. `migrate dev` se reserva para desarrollo; despliegues aplican migraciones aprobadas con `migrate deploy`.

## Consecuencias

Prisma ofrece esquema y cliente tipado, pero no reemplaza conocimiento de SQL. El proyecto NestJS 11 movido desde `apps/api` conserva un arranque CommonJS; antes de añadir Prisma 7 debe probarse la configuración ESM prevista. Si se opta por generar Prisma en CommonJS para conservar ese arranque, este ADR se actualizará explícitamente.

## Pendiente

Fijar parches e imágenes exactos, resolver y probar módulos, generación, conexión, transacciones y migraciones con PostgreSQL 18. No se considera validado por el simple arranque del Hello World.
