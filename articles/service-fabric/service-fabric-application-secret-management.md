---
title: Zarządzanie wpisami tajnymi aplikacji platformy Azure Service Fabric | Microsoft Docs
description: Dowiedz się, jak zabezpieczyć wartości tajne w aplikacji Service Fabric (platform-niezależny od).
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 94a67e45-7094-4fbd-9c88-51f4fc3c523a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/04/2019
ms.author: vturecek
ms.openlocfilehash: b05473fd9868821285853b089fe711aa48f347fc
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973438"
---
# <a name="manage-encrypted-secrets-in-service-fabric-applications"></a>Zarządzanie zaszyfrowanymi wpisami tajnymi w aplikacjach Service Fabric
Ten przewodnik przeprowadzi Cię przez kroki związane z zarządzaniem wpisami tajnymi w aplikacji Service Fabric. Wpisy tajne mogą być dowolnymi informacjami poufnymi, takimi jak parametry połączenia magazynu, hasła lub inne wartości, które nie powinny być obsługiwane w postaci zwykłego tekstu.

Korzystanie z zaszyfrowanych wpisów tajnych w aplikacji Service Fabric obejmuje trzy kroki:
* Skonfiguruj certyfikat szyfrowania i Szyfruj wpisy tajne.
* Określ zaszyfrowane wpisy tajne w aplikacji.
* Odszyfruj zaszyfrowane klucze tajne z kodu usługi.

## <a name="set-up-an-encryption-certificate-and-encrypt-secrets"></a>Konfigurowanie certyfikatu szyfrowania i szyfrowanie wpisów tajnych
Skonfigurowanie certyfikatu szyfrowania i użycie go do szyfrowania wpisów tajnych zależy od systemów Windows i Linux.
* [Skonfiguruj certyfikat szyfrowania i Szyfruj wpisy tajne w klastrach systemu Windows.][secret-management-windows-specific-link]
* [Skonfiguruj certyfikat szyfrowania i Szyfruj wpisy tajne w klastrach systemu Linux.][secret-management-linux-specific-link]

## <a name="specify-encrypted-secrets-in-an-application"></a>Określanie szyfrowanych wpisów tajnych w aplikacji
W poprzednim kroku opisano sposób szyfrowania wpisu tajnego za pomocą certyfikatu i tworzenia zakodowanego ciągu Base-64 do użycia w aplikacji. Ten ciąg zakodowany Base-64 może być określony jako zaszyfrowany [parametr][parameters-link] w pliku Settings. XML usługi lub jako zaszyfrowanej [zmiennej środowiskowej][environment-variables-link] w elemencie servicemanifest. XML usługi.

Określ zaszyfrowany [parametr][parameters-link] w pliku konfiguracyjnym XML ustawień usługi z atrybutem `IsEncrypted` ustawionym na `true`:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </Section>
</Settings>
```
Określ zaszyfrowaną [zmienną środowiskową][environment-variables-link] w pliku servicemanifest. XML usługi z atrybutem `Type` ustawionym na `Encrypted`:
```xml
<CodePackage Name="Code" Version="1.0.0">
  <EnvironmentVariables>
    <EnvironmentVariable Name="MyEnvVariable" Type="Encrypted" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </EnvironmentVariables>
</CodePackage>
```

Wpisy tajne powinny być również zawarte w aplikacji Service Fabric przez określenie certyfikatu w manifeście aplikacji. Dodaj element **SecretsCertificate** do **ApplicationManifest. XML** i Dołącz odcisk palca żądanego certyfikatu.

```xml
<ApplicationManifest … >
  ...
  <Certificates>
    <SecretsCertificate Name="MyCert" X509FindType="FindByThumbprint" X509FindValue="[YourCertThumbrint]"/>
  </Certificates>
</ApplicationManifest>
```

### <a name="inject-application-secrets-into-application-instances"></a>Wsuń wpisy tajne aplikacji do wystąpień aplikacji
W idealnym przypadku wdrożenie w różnych środowiskach powinno być tak zautomatyzowane jak to możliwe. Można to osiągnąć przez przeprowadzenie tajnego szyfrowania w środowisku kompilacji i dostarczenie zaszyfrowanych kluczy tajnych jako parametrów podczas tworzenia wystąpień aplikacji.

#### <a name="use-overridable-parameters-in-settingsxml"></a>Użyj parametrów, które mają zostać zastąpione w pliku Settings. XML
Plik konfiguracji Settings. XML umożliwia określenie parametrów, które mogą być dostarczane podczas tworzenia aplikacji. Użyj atrybutu `MustOverride` zamiast podawania wartości dla parametru:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="" MustOverride="true" />
  </Section>
</Settings>
```

Aby zastąpić wartości w pliku Settings. XML, zadeklaruj parametr przesłonięcia dla usługi w ApplicationManifest. XML:

```xml
<ApplicationManifest ... >
  <Parameters>
    <Parameter Name="MySecret" DefaultValue="" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides>
      <ConfigOverride Name="Config">
        <Settings>
          <Section Name="MySettings">
            <Parameter Name="MySecret" Value="[MySecret]" IsEncrypted="true" />
          </Section>
        </Settings>
      </ConfigOverride>
    </ConfigOverrides>
  </ServiceManifestImport>
 ```

Teraz można określić wartość jako *parametr aplikacji* podczas tworzenia wystąpienia aplikacji. Tworzenie wystąpienia aplikacji może być inicjowane za pomocą programu PowerShell lub w C#celu ułatwienia integracji w procesie kompilacji.

Przy użyciu programu PowerShell parametr jest dostarczany do `New-ServiceFabricApplication` polecenia jako [tabeli skrótów](https://technet.microsoft.com/library/ee692803.aspx):

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/MyApp -ApplicationTypeName MyAppType -ApplicationTypeVersion 1.0.0 -ApplicationParameter @{"MySecret" = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM="}
```

W C#przypadku używania parametrów aplikacji są określone w `ApplicationDescription` jako `NameValueCollection`:

```csharp
FabricClient fabricClient = new FabricClient();

NameValueCollection applicationParameters = new NameValueCollection();
applicationParameters["MySecret"] = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=";

ApplicationDescription applicationDescription = new ApplicationDescription(
    applicationName: new Uri("fabric:/MyApp"),
    applicationTypeName: "MyAppType",
    applicationTypeVersion: "1.0.0",
    applicationParameters: applicationParameters)
);

await fabricClient.ApplicationManager.CreateApplicationAsync(applicationDescription);
```

## <a name="decrypt-encrypted-secrets-from-service-code"></a>Odszyfruj zaszyfrowane klucze tajne z kodu usługi
Interfejsy API służące do uzyskiwania dostępu do [parametrów][parameters-link] i [zmiennych środowiskowych][environment-variables-link] umożliwiają łatwe odszyfrowywanie szyfrowanych wartości. Ponieważ zaszyfrowany ciąg zawiera informacje o certyfikacie używanym do szyfrowania, nie trzeba ręcznie określać certyfikatu. Certyfikat należy zainstalować tylko w węźle, w którym uruchomiono usługę.

```csharp
// Access decrypted parameters from Settings.xml
ConfigurationPackage configPackage = FabricRuntime.GetActivationContext().GetConfigurationPackageObject("Config");
bool MySecretIsEncrypted = configPackage.Settings.Sections["MySettings"].Parameters["MySecret"].IsEncrypted;
if (MySecretIsEncrypted)
{
    SecureString MySecretDecryptedValue = configPackage.Settings.Sections["MySettings"].Parameters["MySecret"].DecryptValue();
}

// Access decrypted environment variables from ServiceManifest.xml
// Note: you do not have to call any explicit API to decrypt the environment variable.
string MyEnvVariable = Environment.GetEnvironmentVariable("MyEnvVariable");
```

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [zabezpieczeniach aplikacji i usługi](service-fabric-application-and-service-security.md)

<!-- Links -->
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-windows-specific-link]: service-fabric-application-secret-management-windows.md
[secret-management-linux-specific-link]: service-fabric-application-secret-management-linux.md
