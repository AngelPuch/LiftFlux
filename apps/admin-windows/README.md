# Administración Windows

Proyecto WPF inicial de LiftFlux sobre .NET 10. Aún no contiene login, MFA ni gestión de catálogo. Su alcance está en [ADR 004](../../docs/adr/004-native-clients.md) y en RF-81–RF-88 de [mvp-scope.md](../../docs/mvp-scope.md).

```powershell
dotnet restore LiftFlux.Admin.csproj --locked-mode
dotnet build LiftFlux.Admin.csproj --no-restore --configuration Release -warnaserror
```

El archivo `packages.lock.json` se versiona para restauración bloqueada. La firma y el paquete MSIX se definirán antes de distribuir la aplicación.
