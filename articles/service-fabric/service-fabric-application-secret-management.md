---
title: Zarządzanie wpisami tajnymi aplikacji usługi Azure
description: Dowiedz się, jak zabezpieczyć tajne wartości w aplikacji sieci szkieletowej usług (niezależny od platformy).
author: vturecek
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: vturecek
ms.openlocfilehash: 4a489993f982993d5703a9b46d42fffaa6134038
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259060"
---
# <a name="manage-encrypted-secrets-in-service-fabric-applications"></a>Zarządzanie zaszyfrowanymi wpisami tajnymi w aplikacjach sieci szkieletowej usług
W tym przewodniku przeprowadzi Cię przez kroki zarządzania wpisami tajnymi w aplikacji sieci szkieletowej usług. Wpisy tajne mogą być wszelkie poufne informacje, takie jak parametry połączenia magazynu, hasła lub inne wartości, które nie powinny być obsługiwane w postaci zwykłego tekstu.

Używanie zaszyfrowanych wpisów tajnych w aplikacji sieci szkieletowej usług obejmuje trzy kroki:
* Konfigurowanie certyfikatu szyfrowania i szyfrowanie wpisów tajnych.
* Określ zaszyfrowane wpisy tajne w aplikacji.
* Odszyfruj zaszyfrowane wpisy tajne z kodu usługi.

## <a name="set-up-an-encryption-certificate-and-encrypt-secrets"></a>Konfigurowanie certyfikatu szyfrowania i szyfrowanie wpisów tajnych
Konfigurowanie certyfikatu szyfrowania i używanie go do szyfrowania wpisów tajnych różni się w zależności od systemu Windows i linux.
* [Konfigurowanie certyfikatu szyfrowania i szyfrowanie wpisów tajnych w klastrach systemu Windows.][secret-management-windows-specific-link]
* [Konfigurowanie certyfikatu szyfrowania i szyfrowanie wpisów tajnych w klastrach systemu Linux.][secret-management-linux-specific-link]

## <a name="specify-encrypted-secrets-in-an-application"></a>Określanie zaszyfrowanych wpisów tajnych w aplikacji
W poprzednim kroku opisano sposób szyfrowania klucza tajnego za pomocą certyfikatu i tworzenia ciągów zakodowanych w podstawowej 64 do użycia w aplikacji. Ten ciąg zakodowany base-64 można określić jako [zaszyfrowany parametr][parameters-link] w pliku Settings.xml usługi lub jako zaszyfrowaną [zmienną środowiskową][environment-variables-link] w pliku ServiceManifest.xml usługi.

Określ zaszyfrowany [parametr][parameters-link] w pliku konfiguracyjnym `IsEncrypted` Settings.xml `true`usługi z atrybutem ustawionym na:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </Section>
</Settings>
```
Określ zaszyfrowaną [zmienną środowiskową][environment-variables-link] w pliku ServiceManifest.xml usługi z atrybutem `Type` ustawionym na: `Encrypted`
```xml
<CodePackage Name="Code" Version="1.0.0">
  <EnvironmentVariables>
    <EnvironmentVariable Name="MyEnvVariable" Type="Encrypted" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </EnvironmentVariables>
</CodePackage>
```

Wpisy tajne powinny być również uwzględnione w aplikacji sieci szkieletowej usług, określając certyfikat w manifeście aplikacji. Dodaj **SecretsCertificate** element do **ApplicationManifest.xml** i dołącz odcisk palca żądanego certyfikatu.

```xml
<ApplicationManifest … >
  ...
  <Certificates>
    <SecretsCertificate Name="MyCert" X509FindType="FindByThumbprint" X509FindValue="[YourCertThumbrint]"/>
  </Certificates>
</ApplicationManifest>
```

### <a name="inject-application-secrets-into-application-instances"></a>Wstrzyknąć wpisy tajne aplikacji do wystąpień aplikacji
W idealnym przypadku wdrożenie w różnych środowiskach powinno być tak zautomatyzowane, jak to możliwe. Można to osiągnąć, wykonując tajne szyfrowanie w środowisku kompilacji i zapewniając zaszyfrowane wpisy tajne jako parametry podczas tworzenia wystąpień aplikacji.

#### <a name="use-overridable-parameters-in-settingsxml"></a>Używanie zastępowalnych parametrów w pliku Settings.xml
Plik konfiguracyjny Settings.xml umożliwia zastępowanie parametrów, które mogą być dostarczane w czasie tworzenia aplikacji. Użyj `MustOverride` atrybutu zamiast podaj wartość parametru:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="" MustOverride="true" />
  </Section>
</Settings>
```

Aby zastąpić wartości w pliku Settings.xml, zadeklaruj parametr zastępowania usługi w pliku ApplicationManifest.xml:

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

Teraz wartość można określić jako *parametr aplikacji* podczas tworzenia wystąpienia aplikacji. Tworzenie wystąpienia aplikacji może być skryptowane przy użyciu programu PowerShell lub napisane w języku C#, dla łatwej integracji w procesie kompilacji.

Za pomocą programu PowerShell parametr `New-ServiceFabricApplication` jest dostarczany do polecenia jako [tabela mieszania:](https://technet.microsoft.com/library/ee692803.aspx)

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/MyApp -ApplicationTypeName MyAppType -ApplicationTypeVersion 1.0.0 -ApplicationParameter @{"MySecret" = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM="}
```

Za pomocą języka C#, `ApplicationDescription` parametry `NameValueCollection`aplikacji są określone w:

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

## <a name="decrypt-encrypted-secrets-from-service-code"></a>Odszyfrowywane zaszyfrowane wpisy tajne z kodu usługi
Interfejsy API dostępu do [parametrów][parameters-link] i [zmiennych środowiskowych][environment-variables-link] umożliwiają łatwe odszyfrowywanie zaszyfrowanych wartości. Ponieważ zaszyfrowany ciąg zawiera informacje o certyfikacie używanym do szyfrowania, nie trzeba ręcznie określać certyfikatu. Certyfikat musi być zainstalowany w węźle, na który jest uruchomiona usługa.

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
* Sklep [z tajemnicami](service-fabric-application-secret-store.md) tkaniny usługowej 
* Dowiedz się więcej o [zabezpieczeniach aplikacji i usług](service-fabric-application-and-service-security.md)

<!-- Links -->
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-windows-specific-link]: service-fabric-application-secret-management-windows.md
[secret-management-linux-specific-link]: service-fabric-application-secret-management-linux.md
[service fabric secrets store]: service-fabric-application-secret-store.md