# Guía de contribución de LiftFlux

Esta guía aplica al monorepo. El código de usuarios está en `apps/android`, el de administración en `apps/admin-windows` y cada backend independiente en `services/<nombre>`. `docs` contiene alcance y ADR; `infra` contiene configuración de entornos. Los directorios de servicios con solo `.gitkeep` todavía no contienen aplicaciones.

## 1. Ramas y Pull Requests

`main` debe permanecer estable. Crea una rama corta desde `main` actualizado, realiza un cambio lógico y abre un Pull Request. Usa nombres en inglés y minúsculas como `feat/auth-provisioning`, `fix/android-session-recovery`, `docs/identity-adr` o `ci/windows-build`. No mantengas ramas permanentes `develop` o `release`, ni hagas force push a `main`.

Cada PR explica qué cambió, por qué y cómo se validó. Revisa el diff, confirma que no contiene secretos, espera los checks requeridos y usa **Squash and merge**. El título final sigue Conventional Commits, por ejemplo `feat(auth): provision application account` o `docs: reconcile native architecture`. La revisión propia sigue siendo necesaria cuando trabaja una sola persona.

## 2. Versiones y contratos

El producto usa Semantic Versioning con etiquetas `v0.1.0`, `v0.2.0` y así sucesivamente. Una etiqueta representa una publicación del producto, no cada PR. El plan es: 0.1 cuenta, onboarding y perfil; 0.2 catálogo y administración; 0.3 rutinas; 0.4 entrenamiento activo; 0.5 historial y estadísticas; 1.0 MVP validado.

- **Android:** `versionName` refleja la versión pública y `versionCode` aumenta en cada artefacto distribuido. El proyecto generado aún muestra `1.0` y `1`; esos valores no son una publicación de LiftFlux.
- **Windows:** la versión del paquete MSIX firmado seguirá la publicación del producto; la identidad de firma y el canal se definen antes de distribuir. El proyecto WPF actual aún no incluye empaquetado.
- **Servicios:** cada servicio puede tener versión de despliegue propia. Solo se reconstruye el que cambió. Los contratos REST comienzan en `/api/v1`; los eventos incluyen `schema_version`. Una actualización compatible permite convivir a productores y consumidores durante el despliegue.
- **Base de datos:** cada servicio mantiene su historial de migraciones. Nunca edites una migración aplicada en un entorno compartido. Usa una migración nueva y conserva compatibilidad durante despliegues y rollback.

Marca cambios incompatibles con `!` o `BREAKING CHANGE`, también durante 0.x. Registra decisiones importantes en `docs/adr/NNN-descripcion.md` con contexto, decisión, consecuencias, estado y pendientes.

## 3. Validación local

Los tres workflows actuales se ejecutan en todos los PR hacia `main` y en pushes a `main`. Esto mantiene checks requeridos presentes incluso en PR de documentación y hace que un cambio de contrato compartido ejecute todos los consumidores. Cuando haya más servicios, agrega su check antes de hacerlo obligatorio.

### Auth service

```powershell
cd services/auth-service
npm.cmd ci
npm.cmd run lint
npm.cmd test -- --runInBand
npm.cmd run build
```

`lint` es de solo lectura; `lint:fix` se usa explícitamente para aplicar cambios. Cuando existan esquema y base, añade pruebas de integración con PostgreSQL real, validación de contratos y migraciones.

### Android

```powershell
cd apps/android
.\gradlew.bat lintDebug testDebugUnitTest assembleDebug
```

El wrapper Gradle se versiona. La CI usa Android SDK 37 y Java 25 conforme al proyecto generado. Agrega pruebas instrumentadas de persistencia y UI cuando existan reglas y pantallas reales; las pruebas de plantilla no demuestran recuperación del entrenamiento.

### Administración Windows

```powershell
dotnet restore apps/admin-windows/LiftFlux.Admin.csproj --locked-mode
dotnet build apps/admin-windows/LiftFlux.Admin.csproj --no-restore --configuration Release -warnaserror
```

El restore usa `packages.lock.json` versionado. Añade pruebas de ViewModel y contratos al crear lógica. La firma y validación de MSIX se incorporan antes de distribuir.

## 4. Cambios de contratos compartidos

El productor es dueño de su OpenAPI o esquema de evento. Un cambio debe incluir ejemplos y pruebas de compatibilidad. No importes entidades Prisma, repositorios o lógica de dominio de otro servicio. Actualiza y prueba los consumidores afectados: Android y Windows para REST público, y servicios suscritos para eventos. Los workflows de los tres proyectos existentes se ejecutan en cada PR para evitar que un filtro de rutas omita un check requerido.

## 5. Secretos y configuración

No agregues contraseñas, tokens, claves administrativas de Supabase, certificados privados ni archivos `.env` a Git. Versiona solo `.env.example` sin valores sensibles. Las aplicaciones distribuidas usan únicamente la clave publicable de Supabase; los secretos de administración pertenecen al backend y se inyectan por entorno. Nunca uses `JWT_SECRET` propio: Supabase firma los tokens y los servicios verifican JWKS, emisor, audiencia y expiración. Si se filtra una clave, revócala y rótala.

Los archivos de bloqueo, el wrapper Gradle y las migraciones sí se versionan. Las salidas `node_modules`, `build`, `bin`, `obj` y configuraciones locales se excluyen mediante `.gitignore`.

## 6. Calidad y Definition of Done

Cada cambio incluye validación de entradas, errores recuperables, autorización por propietario o rol, pruebas de lógica crítica y documentación cuando corresponde. El PR está listo cuando los checks aplicables pasan, no contiene secretos, mantiene contratos compatibles o documenta la ruptura y permite una revisión razonable.

Antes de publicar `v1.0.0`, además del recorrido Android y la administración Windows, deben verificarse recuperación local, baja distribuida, aislamiento entre usuarios, carga, respaldos y restauración. Un servicio que arranca o una CI verde de plantillas no demuestra que el MVP esté completo.
