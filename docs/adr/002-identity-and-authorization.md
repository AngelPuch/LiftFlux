# ADR 002 Identidad y autorización

- **Estado:** Supabase Auth confirmado por el propietario; controles concretos definidos por delegación; implementación pendiente.
- **Fecha:** 23 de septiembre de 2026

## Contexto

Los clientes necesitan correo, Google, recuperación y MFA administrativo sin mantener contraseñas propias. El JWT del proveedor no contiene por sí solo toda la política de LiftFlux.

## Decisión

Supabase Auth custodia contraseñas, factores, refresh tokens y emisión de JWT. Se configura firma asimétrica ES256 y verificación mediante JWKS. Cada servicio comprueba firma, emisor, audiencia, expiración y sujeto. `auth-service` conserva cuenta de aplicación, rol, revocación y baja, referidas por el `sub` de Supabase; no firma un segundo JWT. El alta pública asigna `user`; el primer `admin` se asigna por procedimiento autenticado y auditado.

Cada solicitud privada consulta por canal interno autenticado el estado y permiso en `auth-service` y verifica localmente la propiedad del recurso. Las operaciones administrativas exigen rol `admin` y nivel MFA `aal2`. La consulta falla cerrada.

## Consecuencias

Una baja o revocación registrada en LiftFlux puede bloquear la API antes de vencer el JWT. La latencia y disponibilidad de `auth-service` afectan todas las operaciones privadas. Revocar solo el refresh token de Supabase no invalida de inmediato un access token ya emitido.

## Pendiente

Configurar proyecto, claves, emisores y audiencias por entorno; probar correo, Google, JWKS, renovación, TOTP y alcance de cierre; documentar recuperación de MFA y provisión inicial de admin.
