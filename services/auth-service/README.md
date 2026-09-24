# auth-service

Proyecto NestJS inicial para la identidad de aplicación de LiftFlux. Todavía contiene el controlador de plantilla y **no implementa autenticación**. Su alcance objetivo está en [ADR 002](../../docs/adr/002-identity-and-authorization.md).

Supabase Auth custodiará contraseñas, MFA, refresh tokens y emisión de JWT. Este servicio conservará la cuenta de aplicación, rol, estado, revocaciones, auditoría y coordinación de baja. No guardará contraseñas ni firmará un segundo token. Los demás servicios consultarán su decisión de acceso antes de operaciones privadas.

## Desarrollo actual

```powershell
npm.cmd ci
npm.cmd run lint
npm.cmd test -- --runInBand
npm.cmd run build
npm.cmd run start:dev
```

`.env.example` enumera la configuración prevista, pero la plantilla actual aún no la consume. `infra/local` prepara solo la base PostgreSQL de este servicio. Antes de añadir Prisma 7, se debe probar la integración de módulos con este proyecto NestJS 11 y registrar el resultado en [ADR 003](../../docs/adr/003-backend-and-databases.md).
