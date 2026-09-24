# ADR 004 Clientes nativos

- **Estado:** Android de usuario y Windows de administración confirmados; bibliotecas y versiones concretas definidas por delegación; implementación funcional pendiente.
- **Fecha:** 23 de septiembre de 2026

## Contexto

Flutter, iOS y web pertenecen al planteamiento anterior. El MVP necesita una experiencia de entrenamiento Android y una herramienta de catálogo Windows.

## Decisión

Android usa Kotlin, Jetpack Compose y Material 3, con mínimo inicial API 26, funcionalidades separadas, ViewModel/StateFlow y dependencias construidas por Hilt. REST usa Ktor y serialización tipada. Windows 11 x64 usa C#, WPF y .NET 10, MVVM, `HttpClientFactory` y JSON; su distribución prevista es MSIX firmado.

Ambos clientes encapsulan el SDK comunitario de Supabase tras interfaces y consumen contratos REST versionados. Android puede continuar una sesión iniciada sin red; Windows administra solo en línea y no accede a bases ni claves privilegiadas.

## Consecuencias

Hay dos cadenas de compilación y pruebas. Los proyectos generados actualmente son esqueletos: no prueban Auth, MFA ni recuperación.

## Pendiente

Validar SDK de identidad, Google/PKCE en Android, TOTP en Windows, navegación, accesibilidad, versiones exactas y canales/firma de distribución.
