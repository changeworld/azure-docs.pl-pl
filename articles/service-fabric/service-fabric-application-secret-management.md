---
title: Zarządzanie wpisami tajnymi aplikacji usługi Azure Service Fabric | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zabezpieczyć wartościami wpisów tajnych w aplikacji usługi Service Fabric (niezależnie od platformy).
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
ms.openlocfilehash: d151dbf20e68a2152e9d886a74e51786bb8fbfa6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60614479"
---
# <a name="manage-encrypted-secrets-in-service-fabric-applications"></a>Zarządzanie zaszyfrowane klucze tajne w aplikacji usługi Service Fabric
Ten przewodnik przeprowadzi Cię przez kroki zarządzać kluczami tajnymi w aplikacji usługi Service Fabric. Wpisy tajne można poufne informacje, takie jak parametry połączenia magazynu, hasła lub inne wartości, które nie powinny być traktowane w postaci zwykłego tekstu.

Za pomocą zaszyfrowane klucze tajne w aplikacji usługi Service Fabric obejmuje trzy kroki:
* Skonfiguruj certyfikat szyfrowania, a szyfrowanie kluczy tajnych.
* Określ zaszyfrowane klucze tajne w aplikacji.
* Odszyfrować zaszyfrowane klucze tajne z kodu usługi.

## <a name="set-up-an-encryption-certificate-and-encrypt-secrets"></a>Konfigurowanie certyfikatu szyfrowania i szyfrowanie kluczy tajnych
Konfigurowanie certyfikatu szyfrowania i używanie go do szyfrowania kluczy tajnych waha się między Windows i Linux.
* [Skonfiguruj certyfikat szyfrowania, a następnie szyfrować klucze tajne w klastrach Windows.][secret-management-windows-specific-link]
* [Skonfiguruj certyfikat szyfrowania, a następnie szyfrować klucze tajne w klastrach systemu Linux.][secret-management-linux-specific-link]

## <a name="specify-encrypted-secrets-in-an-application"></a>Określ zaszyfrowane klucze tajne w aplikacji
Poprzedniego kroku opisano sposób szyfrowania wpisu tajnego za pomocą certyfikatu i generuje ciąg zakodowany base-64, do użycia w aplikacji. Ten ciąg zakodowany base-64 można określić jako zaszyfrowane [parametru] [ parameters-link] w Settings.xml usługi lub w formie zaszyfrowanej [zmiennej środowiskowej] [ environment-variables-link] w ServiceManifest.xml usługi.

Określ zaszyfrowanych [parametru] [ parameters-link] w pliku konfiguracyjnym Settings.xml usługi za pomocą `IsEncrypted` ustawioną wartość atrybutu `true`:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </Section>
</Settings>
```
Określ zaszyfrowanych [zmiennej środowiskowej] [ environment-variables-link] w pliku ServiceManifest.xml usługi `Type` ustawioną wartość atrybutu `Encrypted`:
```xml
<CodePackage Name="Code" Version="1.0.0">
  <EnvironmentVariables>
    <EnvironmentVariable Name="MyEnvVariable" Type="Encrypted" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </EnvironmentVariables>
</CodePackage>
```

### <a name="inject-application-secrets-into-application-instances"></a>Wstrzyknięcie wpisów tajnych aplikacji do wystąpienia aplikacji
W idealnym przypadku wdrożenia w różnych środowiskach powinna być jak zautomatyzowane, jak to możliwe. Można to osiągnąć przez wykonywanie szyfrowania klucza tajnego w środowisku kompilacji i dostarczanie zaszyfrowanych kluczy tajnych jako parametry podczas tworzenia wystąpień aplikacji.

#### <a name="use-overridable-parameters-in-settingsxml"></a>Użyj parametrów możliwym do zastąpienia w Settings.xml
Plik konfiguracyjny Settings.xml umożliwia możliwym do zastąpienia parametrów, które można podać w czasie tworzenia aplikacji. Użyj `MustOverride` atrybutu zamiast podawać wartości dla parametru:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="" MustOverride="true" />
  </Section>
</Settings>
```

Aby zastąpić wartości w Settings.xml, należy zadeklarować parametr zastąpienia dla usługi w ApplicationManifest.xml:

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

Teraz można wybrać wartość *parametr aplikace* podczas tworzenia wystąpienia aplikacji. Utworzenie wystąpienia aplikacji umożliwia pisanie skryptów przy użyciu programu PowerShell, bądź ustne w C#, łatwą integrację w procesie kompilacji.

Przy użyciu programu PowerShell, aby podano parametr `New-ServiceFabricApplication` polecenia jako [tabeli mieszania](https://technet.microsoft.com/library/ee692803.aspx):

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/MyApp -ApplicationTypeName MyAppType -ApplicationTypeVersion 1.0.0 -ApplicationParameter @{"MySecret" = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM="}
```

Za pomocą C#, parametry aplikacji są określone w `ApplicationDescription` jako `NameValueCollection`:

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

## <a name="decrypt-encrypted-secrets-from-service-code"></a>Odszyfrować zaszyfrowane klucze tajne z kodu usługi
Interfejsy API do uzyskiwania dostępu do [parametry] [ parameters-link] i [zmienne środowiskowe] [ environment-variables-link] umożliwiają łatwe odszyfrowywania zaszyfrowanych wartości. Ponieważ zaszyfrowanego ciągu zawiera informacje dotyczące certyfikatu używanego na potrzeby szyfrowania, nie musisz ręcznie określić certyfikat. Tylko ten certyfikat musi być zainstalowany w węźle, czy usługa jest uruchomiona na.

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

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o [aplikacji i usług zabezpieczeń](service-fabric-application-and-service-security.md)

<!-- Links -->
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-windows-specific-link]: service-fabric-application-secret-management-windows.md
[secret-management-linux-specific-link]: service-fabric-application-secret-management-linux.md
