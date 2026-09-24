# Android

Proyecto nativo inicial de LiftFlux con Kotlin, Jetpack Compose y mínimo API 26. Aún no contiene cuenta, entrenamiento ni almacenamiento local. El alcance técnico está en [ADR 004](../../docs/adr/004-native-clients.md) y [ADR 005](../../docs/adr/005-local-storage-and-credentials.md).

```powershell
.\gradlew.bat lintDebug testDebugUnitTest assembleDebug
```

La aplicación final deberá excluir credenciales y entrenamiento local de respaldos automáticos antes de guardar información real. La configuración generada todavía permite backup; no debe interpretarse como un control de seguridad terminado.
