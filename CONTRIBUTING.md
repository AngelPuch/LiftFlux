# Guía de contribución de LiftFlux

Este documento define la forma estándar de desarrollar, revisar, integrar y publicar cambios en LiftFlux.

Estas reglas aplican a todo el monorepositorio:

- `apps/mobile`: aplicación móvil desarrollada con Flutter.
- `apps/api`: API desarrollada con NestJS.
- `docs`: documentación técnica y funcional.
- `infra`: infraestructura, contenedores y despliegues.

## 1. Principios de trabajo

El desarrollo de LiftFlux seguirá estos principios:

- `main` debe mantenerse estable y ejecutable.
- Cada cambio debe desarrollarse en una rama de corta duración.
- Cada rama debe resolver un solo objetivo.
- Los cambios se integran mediante Pull Request.
- Las validaciones automatizadas deben aprobarse antes de integrar.
- Los commits deben seguir Conventional Commits.
- Las versiones deben seguir Semantic Versioning.
- No deben almacenarse contraseñas, tokens ni secretos en Git.
- Se favorecen cambios pequeños, revisables y fáciles de revertir.

## 2. Estrategia de ramas

LiftFlux utilizará GitHub Flow, siguiendo prácticas de trunk-based development.

La única rama permanente será:

- `main`: contiene la versión estable más reciente del proyecto.

No se utilizarán ramas permanentes como `develop`, `release` o `hotfix`.

Después del commit inicial, no se realizarán commits directamente en `main`. Todos los cambios deben pasar por una rama y un Pull Request.

### 2.1 Nombres de ramas

Formato:

`<tipo>/<descripcion-corta>`

La descripción debe escribirse en inglés, en minúsculas y separada con guiones.

| Tipo        | Uso                                          |
| ----------- | -------------------------------------------- |
| `feat/`     | Nueva funcionalidad                          |
| `fix/`      | Corrección de un error                       |
| `docs/`     | Cambios de documentación                     |
| `refactor/` | Reestructuración sin cambiar comportamiento  |
| `test/`     | Creación o modificación de pruebas           |
| `chore/`    | Mantenimiento general                        |
| `ci/`       | Cambios en integración o despliegue continuo |
| `build/`    | Cambios en compilación o dependencias        |
| `perf/`     | Mejoras de rendimiento                       |

Ejemplos:

- `feat/user-registration`
- `feat/workout-tracking`
- `fix/session-duration`
- `docs/define-development-workflow`
- `refactor/api-validation`
- `ci/add-mobile-checks`

Opcionalmente, una rama puede incluir el número de una Issue:

- `feat/24-workout-history`
- `fix/31-invalid-weight-value`

### 2.2 Ciclo de una rama

1. Actualizar `main`.
2. Crear una rama desde `main`.
3. Implementar un cambio pequeño y enfocado.
4. Ejecutar las validaciones locales.
5. Crear commits con Conventional Commits.
6. Publicar la rama en GitHub.
7. Abrir un Pull Request hacia `main`.
8. Verificar que CI finalice correctamente.
9. Revisar el cambio.
10. Integrar mediante Squash and merge.
11. Eliminar la rama.
12. Actualizar el repositorio local.

Las ramas deben vivir el menor tiempo posible. Si una funcionalidad es demasiado grande, debe dividirse en cambios más pequeños.

## 3. Estándar de commits

LiftFlux utilizará Conventional Commits.

Formato:

`<tipo>(<alcance>): <descripcion>`

El alcance es opcional.

### 3.1 Tipos de commit

| Tipo       | Uso                                   |
| ---------- | ------------------------------------- |
| `feat`     | Nueva funcionalidad                   |
| `fix`      | Corrección de un error                |
| `docs`     | Documentación                         |
| `style`    | Formato sin cambios de comportamiento |
| `refactor` | Reestructuración interna              |
| `test`     | Pruebas                               |
| `perf`     | Rendimiento                           |
| `build`    | Sistema de compilación o dependencias |
| `ci`       | Automatización de CI/CD               |
| `chore`    | Mantenimiento                         |
| `revert`   | Reversión de un cambio                |

### 3.2 Alcances recomendados

- `mobile`
- `api`
- `auth`
- `exercise`
- `routine`
- `workout`
- `history`
- `infra`
- `deps`
- `release`

### 3.3 Reglas para los commits

- Escribir el encabezado en inglés.
- Usar minúsculas.
- Utilizar una descripción breve e imperativa.
- No terminar el encabezado con punto.
- Incluir un solo cambio lógico por commit.
- No utilizar mensajes como `changes`, `update`, `fix stuff` o `avance`.
- Agregar cuerpo al commit cuando sea necesario explicar el motivo.
- Indicar explícitamente los cambios incompatibles.

Ejemplos válidos:

```text
feat(mobile): add workout timer
feat(api): create exercise endpoint
fix(auth): reject expired refresh tokens
docs: define development workflow
test(api): add workout service tests
chore(deps): update api dependencies
ci: validate mobile and api
```

Ejemplo de cambio incompatible:

```text
feat(api)!: change workout response schema
```

Aunque el proyecto esté en una versión 0.x, los cambios incompatibles deben marcarse.

## 4. Pull Requests

Todo Pull Request debe:

- Tener como destino la rama `main`.
- Resolver un objetivo claramente definido.
- Tener un título compatible con Conventional Commits.
- Explicar qué se cambió y por qué.
- Indicar cómo se validó.
- Estar libre de secretos y archivos generados innecesarios.
- Tener todas las validaciones de CI aprobadas.
- Recibir una revisión propia antes de integrarse.

Cuando trabaje una sola persona en el proyecto, la revisión propia sigue siendo obligatoria.

### 4.1 Plantilla mínima de descripción

```md
## Summary

- Describe the main change
- Explain any relevant technical decision

## Validation

- [ ] Formatting completed
- [ ] Static analysis completed
- [ ] Tests completed
- [ ] Application tested manually when applicable

## Related issue

Closes #<issue-number>
```

Si no existe una Issue relacionada, se puede omitir la última sección.

### 4.2 Método de integración

Se utilizará Squash and merge.

Esto permite que cada Pull Request se convierta en un solo commit limpio dentro de `main`.
El título final del squash debe respetar Conventional Commits.
Después de integrar el Pull Request, se debe eliminar la rama remota.
No se debe utilizar force push sobre `main`.

## 5. Versionamiento

LiftFlux utilizará Semantic Versioning:

`MAJOR.MINOR.PATCH`

- `MAJOR`: cambio incompatible con versiones anteriores.
- `MINOR`: nueva funcionalidad compatible.
- `PATCH`: corrección compatible.

Durante el desarrollo inicial se utilizarán versiones `0.y.z`.

Plan inicial de versiones:

| Versión | Alcance esperado                 |
| ------- | -------------------------------- |
| `0.0.1` | Estructura inicial del proyecto  |
| `0.1.0` | Autenticación y perfil           |
| `0.2.0` | Catálogo de ejercicios           |
| `0.3.0` | Creación de rutinas              |
| `0.4.0` | Registro de entrenamientos       |
| `0.5.0` | Historial y estadísticas básicas |
| `1.0.0` | MVP estable                      |

Los valores generados inicialmente por Flutter o NestJS no se consideran una publicación oficial. Se alinearán cuando se prepare la primera versión de LiftFlux.

### 5.1 Versiones de Flutter

La aplicación móvil utilizará el formato:

```yaml
version: 0.1.0+1
```

- `0.1.0` es la versión pública.
- `1` es el número interno de compilación.

La versión pública cambia cuando se publica una nueva versión funcional. El número de compilación debe incrementarse para cada artefacto distribuido.

### 5.2 Etiquetas de Git

Las publicaciones se identificarán con etiquetas:

- `v0.1.0`
- `v0.1.1`
- `v0.2.0`
- `v1.0.0`

No se creará una etiqueta por cada Pull Request. Las etiquetas representan publicaciones del producto.
Durante la etapa inicial, el monorepositorio utilizará una sola versión de producto para la aplicación móvil y la API.

### 5.3 Versionamiento de la API

Los endpoints públicos comenzarán con:

```text
/api/v1
```

La versión de la API y la versión del producto son conceptos distintos.
Un cambio incompatible en los contratos públicos podrá requerir una nueva versión de la API, por ejemplo `/api/v2`.

## 6. Integración continua

GitHub Actions ejecutará validaciones:

- Al abrir o actualizar un Pull Request hacia `main`.
- Después de integrar cambios en `main`.

### 6.1 Validaciones de Flutter

La aplicación móvil deberá ejecutar:

```bash
flutter pub get
dart format --output=none --set-exit-if-changed .
flutter analyze
flutter test
```

Posteriormente se agregará la compilación de Android cuando el flujo básico esté estabilizado.

### 6.2 Validaciones de NestJS

La API deberá ejecutar:

```bash
npm ci
npm run lint
npm test
npm run build
```

Posteriormente se agregarán pruebas end-to-end, validaciones de migraciones y construcción de imágenes Docker.
Un Pull Request con validaciones fallidas no debe integrarse.

## 7. Despliegue continuo

LiftFlux utilizará Continuous Delivery.

Esto significa que cada versión estará preparada para publicarse, pero el despliegue a producción requerirá una aprobación manual.

Se utilizarán tres ambientes:

| Ambiente     | Uso                               |
| ------------ | --------------------------------- |
| `local`      | Desarrollo en la computadora      |
| `staging`    | Validación previa a publicación   |
| `production` | Aplicación utilizada por usuarios |

Flujo planeado:

1. Un Pull Request valida el código.
2. Un merge a `main` podrá desplegar automáticamente a `staging`.
3. Una etiqueta `vX.Y.Z` generará una versión candidata.
4. El despliegue a producción requerirá aprobación manual.
5. La API se distribuirá mediante una imagen Docker.
6. La app móvil se distribuirá primero mediante canales de pruebas internas.
7. El despliegue automático se implementará cuando la infraestructura y los ambientes estén disponibles.

## 8. Variables de entorno y secretos

Los archivos con secretos no deben agregarse al repositorio.

Ejemplos que deben permanecer ignorados:

- `.env`
- `.env.local`
- `.env.staging`
- `.env.production`

Cada aplicación que requiera variables de entorno debe incluir un archivo `.env.example` sin valores sensibles.

Ejemplo:

```env
DATABASE_URL=
JWT_SECRET=
API_PORT=
```

Los secretos de CI/CD deben almacenarse en GitHub Secrets o en el proveedor de despliegue.
Si un secreto se agrega accidentalmente a Git, debe considerarse comprometido y reemplazarse inmediatamente.

## 9. Issues y planificación

El trabajo se organizará mediante GitHub Issues.

Cada Issue debe describir:

- Problema u objetivo.
- Alcance.
- Criterios de aceptación.
- Dependencias conocidas.
- Elementos que quedan fuera del alcance.

Etiquetas iniciales recomendadas:

- `feature`
- `bug`
- `documentation`
- `technical-debt`
- `infrastructure`
- `mobile`
- `api`
- `priority:high`
- `priority:medium`
- `priority:low`

Las versiones importantes podrán organizarse mediante Milestones.

## 10. Calidad y pruebas

Todo código debe respetar las herramientas de formato y análisis configuradas en cada proyecto.
Una funcionalidad debe incluir pruebas cuando contenga lógica de negocio relevante.
La corrección de un error debe incluir una prueba de regresión cuando sea posible.
Al inicio no se impondrá un porcentaje mínimo de cobertura. Primero se priorizarán pruebas útiles sobre métricas artificiales.

Las pruebas deben ser:

- Repetibles.
- Independientes.
- Comprensibles.
- Rápidas cuando sean pruebas unitarias.
- Enfocadas en comportamiento observable.

## 11. Dependencias y migraciones

Los archivos de bloqueo deben almacenarse en Git:

- `package-lock.json`
- `pubspec.lock`

Las actualizaciones importantes de dependencias deben realizarse en Pull Requests separados.
No se debe actualizar una dependencia principal sin revisar:

- Cambios incompatibles.
- Requisitos de plataforma.
- Vulnerabilidades conocidas.
- Resultado de las pruebas.

Las modificaciones de base de datos deben realizarse mediante migraciones versionadas.
Una migración que ya fue aplicada en un ambiente compartido no debe editarse. Debe crearse una nueva migración correctiva.
Los cambios de esquema, migración y código dependiente deben incluirse en el mismo Pull Request cuando formen una sola unidad funcional.

## 12. Decisiones de arquitectura

Las decisiones técnicas importantes se documentarán como Architecture Decision Records en:

`docs/adr/`

Formato de nombre:

`NNNN-descripcion-corta.md`

Ejemplo:

`0001-use-postgresql-as-primary-database.md`

Cada ADR debe incluir:

- Contexto.
- Decisión.
- Alternativas consideradas.
- Consecuencias.
- Estado de la decisión.

No es necesario crear un ADR para decisiones pequeñas o fácilmente reversibles.

## 13. Definition of Done

Una tarea se considera terminada cuando:

- Cumple sus criterios de aceptación.
- El código está formateado.
- El análisis estático no presenta errores.
- Las pruebas relevantes fueron creadas o actualizadas.
- Todas las pruebas pasan.
- La documentación fue actualizada cuando corresponde.
- No contiene secretos ni archivos generados innecesarios.
- El Pull Request es pequeño y comprensible.
- CI finaliza correctamente.
- El cambio fue integrado mediante Squash and merge.
- La rama utilizada fue eliminada.

Que el código funcione solamente en la computadora del desarrollador no es suficiente para considerarlo terminado.

## 14. Procedimiento de publicación

Para preparar una publicación:

1. Verificar que `main` esté actualizada y estable.
2. Confirmar que CI esté aprobada.
3. Actualizar las versiones correspondientes.
4. Actualizar las notas de cambios.
5. Crear un commit de publicación:

```bash
chore(release): prepare v0.1.0
```

6. Integrar el Pull Request de publicación.
7. Crear una etiqueta anotada:

```bash
git tag -a v0.1.0 -m "LiftFlux v0.1.0"
git push origin v0.1.0
```

8. Generar la publicación correspondiente en GitHub.
9. Desplegar primero en `staging`.
10. Aprobar manualmente el despliegue a producción.

## 15. Referencias

- GitHub Flow: https://docs.github.com/en/get-started/using-github/github-flow
- Trunk-Based Development: https://trunkbaseddevelopment.com/
- Conventional Commits: https://www.conventionalcommits.org/
- Semantic Versioning: https://semver.org/
