# Impulsores arquitectónicos de LiftFlux

- **Estado:** Reconciliado con la arquitectura v2; objetivos pendientes de medición
- **Versión:** 2.0
- **Fecha:** 23 de septiembre de 2026
- **Alcance:** MVP `v1.0.0`

## 1. Propósito y estado

Este documento fija las restricciones y los escenarios de calidad del MVP. El alcance funcional está en [mvp-scope.md](mvp-scope.md) y las decisiones técnicas en [adr/](adr/). Los objetivos numéricos son criterios de aceptación para medir en entornos representativos, no resultados demostrados por los proyectos iniciales.

El diseño objetivo tiene dos clientes nativos: Android para la persona que entrena y Windows para administrar contenido global. Seis servicios NestJS poseen datos y despliegues independientes. Supabase Auth gestiona credenciales y sesiones; Supabase Storage conserva archivos. La infraestructura de negocio permanece en servidores Linux propios. iOS y web quedan fuera del MVP.

## 2. Prioridades

| Prioridad | Atributo | Motivo |
| --- | --- | --- |
| Crítica | Seguridad y privacidad | Cuentas, medidas e historial privados; administración con MFA |
| Crítica | Integridad y recuperación | Una serie confirmada no puede perderse ni duplicarse |
| Crítica | Usabilidad durante el entrenamiento | La pantalla y el temporizador deben seguir siendo utilizables |
| Alta | Rendimiento | La autorización en línea añade latencia al recorrido de API |
| Alta | Mantenibilidad y pruebas | Dos clientes y seis servicios requieren contratos claros |
| Media | Disponibilidad y restauración | Un host común y dependencias externas crean fallos compartidos |
| Media | Observabilidad | Fallos distribuidos deben diagnosticarse sin revelar datos privados |
| Media | Escalabilidad | Cada servicio podrá desplegarse y crecer de forma independiente |
| Baja en el MVP | Funcionamiento completamente offline | Solo se garantiza continuar una sesión ya iniciada |

## 3. Escenarios de calidad

### QA-01. Autorización y aislamiento

El 100 % de los endpoints privados valida el JWT, el estado de acceso en `auth-service`, el rol cuando corresponde y la propiedad del recurso. Un usuario A no puede leer ni alterar perfil, archivos privados, rutinas, sesiones, historial o estadísticas de B. El administrador gestiona únicamente contenido global. Las pruebas negativas deben obtener `401` o `403` sin revelar contenido ajeno.

### QA-02. Credenciales y sesiones

Supabase Auth conserva contraseñas, factores y emisión de tokens. Ningún servicio almacena contraseñas, secretos TOTP ni refresh tokens. Android protege el refresh token mediante Android Keystore; Windows mantiene tokens solo en memoria durante el MVP. Los clientes y servicios no registran credenciales ni secretos. Fuera de local se exige HTTPS; el cierre, la revocación y la baja se prueban junto con expiración y renovación.

### QA-03. Recuperación del entrenamiento activo

Tras muerte del proceso, reinicio de la aplicación o pérdida de conexión, Android recupera desde almacenamiento local la sesión y todas las series que ya confirmó. El **límite de aceptación es 5 segundos** desde la apertura con datos representativos; **3 segundos es la meta interna** de optimización. Puede perderse el texto de un campo que aún no se había confirmado. La sincronización reintenta al volver la conexión sin duplicar operaciones. La prueba debe forzar la muerte del proceso, no solo navegar fuera de la pantalla.

### QA-04. Integridad histórica

El 100 % de los entrenamientos finalizados conserva copia de rutina, ejercicios, orden, pesos, repeticiones y notas aunque cambie o falle el catálogo o el servicio de rutinas. El historial finalizado es inmutable; su eliminación autorizada se refleja en estadísticas sin alterar otros históricos.

### QA-05. Unidades

La unidad canónica, escala decimal y redondeo se especificarán antes de cerrar modelos deportivos. Cambios repetidos entre kg y lb no degradan el valor almacenado. Perfil, Android, Windows y backend usarán casos de prueba compartidos. Solo las series completadas aportan volumen.

### QA-06. Rendimiento de API

En staging, con datos representativos y al menos 50 usuarios concurrentes: P95 de consultas comunes ≤500 ms; P95 de escrituras comunes ≤800 ms; ninguna consulta común >3 s en condiciones normales. Se mide el recorrido de backend incluido el gateway y la consulta de acceso a `auth-service`; la latencia de proveedores externos se registra por separado. Se incluyen perfil, búsqueda, rutinas, historial, series y finalización.

### QA-07. Respuesta de Android

Respuesta visual local <100 ms; pantalla con datos locales <1 s; resultado remoto o estado de carga <2 s. Una serie solo se presenta como guardada después de persistencia local exitosa. Si tarda más, la interfaz muestra progreso y permite recuperarse.

### QA-08. Uso durante una sesión

La persona puede registrar, corregir y completar series desde la pantalla activa, distinguir estados, usar el temporizador sin bloqueo y confirmar acciones destructivas. El flujo se valida manualmente en un Android real o emulado con objetivos táctiles utilizables y errores recuperables.

### QA-09. Disponibilidad y recuperación operativa

Objetivo mensual de disponibilidad del recorrido principal de API: 99 %. RPO ≤24 horas y RTO ≤4 horas. Cada PostgreSQL tendrá respaldo propio cifrado fuera del host y se probará una restauración conjunta antes de `v1.0.0`, incluida la reconciliación de bajas y eventos. Un único host Linux no ofrece alta disponibilidad; una caída remota no borra el entrenamiento activo guardado en Android.

### QA-10. Mantenibilidad

Android organiza código por funcionalidades y separa UI de reglas y datos. Windows separa vistas y ViewModels. Cada servicio posee su modelo, migraciones y contratos; ninguno importa entidades ORM de otro. CI valida Android, Windows y cada servicio implementado. Las pruebas priorizan lógica crítica y casos de error; no se fija una cobertura global como sustituto. Duración ideal de validaciones normales: <10 minutos.

### QA-11. Observabilidad

Los servicios emiten logs JSON con instante, nivel, servicio, ruta, estado, duración y correlation ID; exponen salud y métricas. No registran tokens, contraseñas, correos completos, notas privadas ni URLs firmadas. OpenTelemetry, Loki, Prometheus, Tempo y Grafana son componentes transversales, no un servicio de negocio adicional. Su fallo no bloquea transacciones de negocio.

### QA-12. Plataformas

El MVP se prueba y distribuye para Android y Windows 11 x64. Las integraciones de plataforma se encapsulan detrás de interfaces. Los contratos backend no dependen de una UI concreta. iOS, web y panel web administrativo quedan fuera del MVP.

### QA-13. Escalado y límites

Los seis servicios son procesos y despliegues independientes, sin sesión permanente en memoria. Cada uno posee una instancia PostgreSQL con credenciales, migraciones y respaldo propios. Se paginan consultas y se indexan accesos frecuentes. RabbitMQ distribuye hechos confirmados mediante outbox/inbox; su retraso no bloquea la finalización de un entrenamiento mientras el outbox tenga capacidad.

## 4. Restricciones vigentes

| ID | Restricción |
| --- | --- |
| R-01 | Android nativo con Kotlin y Jetpack Compose; mínimo inicial API 26 |
| R-02 | Administración Windows nativa con C#, WPF y .NET 10 |
| R-03 | Seis servicios TypeScript estricto con NestJS 11 y Node.js 24 |
| R-04 | Una instancia PostgreSQL 18 por servicio y Prisma 7 con migraciones propias |
| R-05 | Supabase Auth para identidad y Supabase Storage para archivos; bases de negocio propias |
| R-06 | Monorepo, servidor Linux, GitHub Flow y ramas cortas |
| R-07 | Conventional Commits, Semantic Versioning y PR con CI aprobado |
| R-08 | Secretos fuera de Git y de clientes; ninguna clave privilegiada en aplicaciones distribuidas |
| R-09 | Solo un entrenamiento activo por usuario; iniciar uno nuevo requiere conexión |
| R-10 | El entrenamiento ya iniciado puede continuar localmente; no hay offline completo |
| R-11 | El administrador necesita rol propio de LiftFlux y MFA; no accede a datos deportivos privados |
| R-12 | El desarrollo inicial es de una persona; cada dependencia operativa debe ser explícita |

## 5. Límites de componentes

| Servicio | Propiedad principal |
| --- | --- |
| `auth-service` | Cuenta de aplicación, rol, revocación y coordinación de baja |
| `profile-service` | Onboarding, username, preferencias y peso corporal |
| `catalog-service` | Ejercicios globales/privados, taxonomía y metadatos multimedia |
| `routine-service` | Plantillas privadas y versiones |
| `workout-service` | Sesión activa, series, finalización e historial autoritativo |
| `stats-service` | Proyecciones reconstruibles del entrenamiento |

Las consultas interactivas usan REST/HTTPS con contratos OpenAPI versionados. Los eventos usan AMQP, outbox e inbox con entrega al menos una vez. Ningún servicio consulta la base de otro. El gateway Caddy termina HTTPS público; la autorización se aplica dentro de cada servicio. Consultar `auth-service` en cada solicitud privada permite aplicar bajas registradas en línea, pero lo convierte en dependencia síncrona crítica y debe incluirse en QA-06.

## 6. Costos y riesgos aceptados

- Seis procesos y seis PostgreSQL elevan recursos, certificados, migraciones, respaldos y fallos parciales. Compartir un host reduce costo y conserva un dominio de fallo común.
- Supabase reduce trabajo de identidad y archivos, pero introduce disponibilidad, límites y costos de proveedor.
- La finalización del entrenamiento se confirma en `workout-service`; estadísticas se actualizan después. La UI debe mostrar cuándo una proyección está atrasada.
- El historial conserva copias intencionales para sobrevivir a cambios de catálogo o rutina.
- Una sesión Android activa se guarda localmente y tiene un solo dispositivo escritor. No se promete edición concurrente entre dispositivos ni crear sesiones nuevas sin conexión.
- Los SDK comunitarios de Supabase, Prisma con NestJS 11 y la combinación Room–SQLCipher necesitan pruebas de compatibilidad antes de fijar versiones exactas.

## 7. Decisiones todavía pendientes

Antes de fijar modelos deportivos: unidad canónica, precisión, redondeo, casos de peso cero o asistido, duración, récords, reglas de username, zona horaria y comienzo de semana. Antes de prometer la baja: plazo, retención de respaldos y auditoría, edad mínima y textos de privacidad. Antes de staging o distribución: capacidad y presupuesto del servidor, dominio, respaldo externo, plan/región de Supabase, SMTP, OAuth de Google, firmas y canales de Android y Windows, procedimiento de recuperación MFA y licencias del contenido inicial.

Estas decisiones pendientes no impiden preparar estructura, CI, contratos y la primera prueba vertical de autenticación. Los ADR registran las elecciones de diseño sin presentarlas como funcionalidad implementada.
