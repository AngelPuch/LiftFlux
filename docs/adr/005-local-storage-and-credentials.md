# ADR 005 Persistencia local y credenciales

- **Estado:** Definido por delegación; integración y pruebas pendientes.
- **Fecha:** 23 de septiembre de 2026

## Contexto

Una serie confirmada debe sobrevivir a muerte del proceso o pérdida de red. Las credenciales y el historial privado necesitan protección local.

## Decisión

Android guarda sesión activa, series, notas, temporizador y cola pendiente en Room/SQLite cifrado con SQLCipher. Una clave aleatoria se protege mediante Android Keystore. El refresh token se cifra con AES-256-GCM, nonce nuevo por escritura y persistencia atómica; el access token permanece en memoria mientras sea posible. DataStore guarda solo preferencias pequeñas. Se excluyen tokens, claves y sesión deportiva de respaldos y transferencias automáticas.

Windows mantiene tokens únicamente en memoria y preferencias no sensibles en disco; no dispone de base local ni modo offline. Tras 15 minutos de inactividad se bloquea y exige nueva autenticación con MFA. `auth-service` impone además un máximo de ocho horas a la sesión administrativa.

## Consecuencias

Persistir localmente antes de confirmar en UI reduce pérdida de datos, pero exige migraciones, manejo de almacenamiento lleno y aislamiento al cambiar de cuenta. Cifrar no elimina el riesgo de un dispositivo comprometido mientras la app está abierta.

## Pendiente

Probar Room–SQLCipher y páginas de 16 KB, muerte del proceso, restauración, clave inaccesible, cambio de cuenta y que las reglas de backup Android realmente excluyan datos sensibles.
