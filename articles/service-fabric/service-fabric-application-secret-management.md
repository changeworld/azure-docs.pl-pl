---
title: Zarządzanie klucze tajne aplikacji sieci szkieletowej usług Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zabezpieczyć poufne wartości w aplikacji sieci szkieletowej usług.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: 94a67e45-7094-4fbd-9c88-51f4fc3c523a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/21/2018
ms.author: vturecek
ms.openlocfilehash: 931667509a9aa5e898cd01ad26ff046e30acd3fe
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="manage-secrets-in-service-fabric-applications"></a>Zarządzanie kluczy tajnych w aplikacji sieci szkieletowej usług
Ten przewodnik zawiera opis kroków związanych z zarządzaniem kluczy tajnych w aplikacji sieci szkieletowej usług. Klucze tajne można poufne informacje, takie jak parametry połączenia magazynu, hasła lub inne wartości, które nie powinny być traktowane w postaci zwykłego tekstu.

[Usługa Azure Key Vault] [ key-vault-get-started] jest tu używany jako lokalizację bezpiecznego magazynu certyfikatów, a sposobem uzyskania certyfikatów zainstalowanych na klastrów sieci szkieletowej usług platformy Azure. Jeśli nie są wdrażane na platformie Azure, nie trzeba zarządzać kluczy tajnych w aplikacji sieci szkieletowej usług za pomocą usługi Key Vault. Jednak *przy użyciu* kluczy tajnych w aplikacji w chmurze jest niezależny od platformy aby umożliwić aplikacjom można wdrożyć w klastrze hostowane w dowolnym miejscu. 

## <a name="obtain-a-data-encipherment-certificate"></a>Uzyskaj certyfikat Szyfrowanie danych
Certyfikat Szyfrowanie danych jest używane wyłącznie do szyfrowania i odszyfrowywania konfiguracji wartości w pliku Settings.xml usługi i nie jest używany do uwierzytelniania lub podpisywanie tekst zaszyfrowany. Certyfikat musi spełniać następujące wymagania:

* Certyfikat musi zawierać klucz prywatny.
* Certyfikat musi zostać utworzony dla wymiany kluczy, można eksportować do pliku wymiany informacji osobistych (pfx).
* Sposób użycia klucza certyfikatu musi zawierać szyfrowanie danych (10) i nie powinna zawierać uwierzytelniania serwera lub uwierzytelniania klienta. 
  
  Na przykład podczas tworzenia certyfikatu z podpisem własnym za pomocą programu PowerShell `KeyUsage` musi zostać ustawiona flaga `DataEncipherment`:
  
  ```powershell
  New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject mydataenciphermentcert -Provider 'Microsoft Enhanced Cryptographic Provider v1.0'
  ```

## <a name="install-the-certificate-in-your-cluster"></a>Zainstaluj certyfikat w klastrze
Ten certyfikat należy zainstalować na każdym węźle w klastrze. Będzie on używany w czasie wykonywania można odszyfrować wartości przechowywane w pliku Settings.xml usługi. Zobacz [Tworzenie klastra przy użyciu usługi Azure Resource Manager] [ service-fabric-cluster-creation-via-arm] instrukcje instalacji. 

## <a name="encrypt-application-secrets"></a>Szyfrowanie hasła aplikacji
W przypadku wdrażania aplikacji, tajne wartości przy użyciu certyfikatu szyfrowania i wstawić je do pliku konfiguracji Settings.xml usługi. Zestaw SDK sieci szkieletowej usług ma wbudowane funkcje tajny szyfrowania i odszyfrowywania. Tajny wartości może być szyfrowane w czasie zbudowany odszyfrować i programowo odczytać w kodzie usługi. 

Następujące polecenia programu PowerShell jest używany do szyfrowania klucza tajnego. To polecenie szyfruje wartości; robi **nie** zarejestrować tekst zaszyfrowany. Należy użyć tego samego certyfikatu szyfrowanie zainstalowanym w klastrze do produkcji tekstu szyfrowanego tajny wartości:

```powershell
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint "<thumbprint>" -Text "mysecret" -StoreLocation CurrentUser -StoreName My
```

Wynikowy ciąg zakodowany base-64 zawiera zarówno tajny tekstu szyfrowanego oraz informacje o certyfikacie, które zostało użyte do ich zaszyfrowania.  Zaszyfrowanym ciągiem base-64 można wstawiać do parametru w pliku konfiguracyjnym Settings.xml usługi z `IsEncrypted` ustawić atrybutu `true`:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </Section>
</Settings>
```

### <a name="inject-application-secrets-into-application-instances"></a>Wstaw klucze tajne aplikacji do wystąpienia aplikacji
W idealnym przypadku wdrożenia w różnych środowiskach powinna być jak zautomatyzowane, jak to możliwe. Można to osiągnąć, wykonywanie tajny szyfrowania w środowisku kompilacji i dostarczanie zaszyfrowanych kluczy tajnych jako parametry podczas tworzenia wystąpień aplikacji.

#### <a name="use-overridable-parameters-in-settingsxml"></a>Korzystanie z możliwym do zastąpienia parametrów w pliku Settings.xml
Plik konfiguracji w pliku Settings.xml umożliwia możliwością zastępowania parametrów, które można podać podczas tworzenia aplikacji. Użyj `MustOverride` zamiast podawania wartości dla parametru atrybutu:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="" MustOverride="true" />
  </Section>
</Settings>
```

Aby zastąpić wartości w pliku Settings.xml, należy zadeklarować parametru override dla usługi w pliku ApplicationManifest.xml:

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

Teraz można określić wartość jako *parametru aplikacji* podczas tworzenia wystąpienia aplikacji. Tworzenie wystąpienia aplikacji mogą być przetwarzane przez skrypty przy użyciu programu PowerShell, lub napisane w języku C# dla Łatwa integracja w procesie kompilacji.

Przy użyciu programu PowerShell, aby podano parametr `New-ServiceFabricApplication` polecenie jako [tablicy skrótów](https://technet.microsoft.com/library/ee692803.aspx):

```powershell
PS C:\Users\vturecek> New-ServiceFabricApplication -ApplicationName fabric:/MyApp -ApplicationTypeName MyAppType -ApplicationTypeVersion 1.0.0 -ApplicationParameter @{"MySecret" = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM="}
```

Przy użyciu języka C#, parametry aplikacji są określone w `ApplicationDescription` jako `NameValueCollection`:

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

## <a name="decrypt-secrets-from-service-code"></a>Odszyfrowywanie hasła z kodem usługi
Zaszyfrowane wartości poza Settings.xml może być odczytany przez ich odszyfrowywania szyfrowanie certyfikat używany do szyfrowania klucza tajnego. Usługi w sieci szkieletowej usług w ramach usługi SIECIOWE są domyślnie uruchamiane w systemie Windows i nie mają dostępu do certyfikatów zainstalowanych na węźle bez dodatkowej konfiguracji.

Po za pomocą certyfikatu szyfrowanie danych, należy upewnić się, że Usługa sieciowa lub niezależnie od użytkownika konta usługi jest uruchomiona w ramach ma dostęp do klucza prywatnego certyfikatu. Sieć szkieletowa usług będzie obsługiwać udzielania dostępu dla usługi automatycznie, jeśli został skonfigurowany w tym celu. Ta konfiguracja może odbywać się w pliku ApplicationManifest.xml przez zdefiniowanie zasad zabezpieczeń dla certyfikatów i użytkowników. W poniższym przykładzie konto Usługa sieciowa otrzymuje dostęp do odczytu do zdefiniowanych przez jego odcisk palca certyfikatu:

```xml
<ApplicationManifest … >
    <Principals>
        <Users>
            <User Name="Service1" AccountType="NetworkService" />
        </Users>
    </Principals>
  <Policies>
    <SecurityAccessPolicies>
      <SecurityAccessPolicy GrantRights=”Read” PrincipalRef="Service1" ResourceRef="MyCert" ResourceType="Certificate"/>
    </SecurityAccessPolicies>
  </Policies>
  <Certificates>
    <SecretsCertificate Name="MyCert" X509FindType="FindByThumbprint" X509FindValue="[YourCertThumbrint]"/>
  </Certificates>
</ApplicationManifest>
```

> [!NOTE]
> Podczas kopiowania odcisku palca certyfikatu z magazynu przystawki certyfikatów w systemie Windows, znaku niewidoczne znajduje się na początku ciąg odcisk palca. Ten znak niewidoczne może spowodować błąd podczas próby znaleźć przez odcisk palca certyfikatu, dlatego należy usunąć ten dodatkowy znak.
> 
> 

### <a name="use-application-secrets-in-service-code"></a>Użyj hasła aplikacji w kodzie usługi
Interfejs API do uzyskiwania dostępu do wartości konfiguracji z pliku Settings.xml w pakiecie konfiguracji umożliwia łatwe odszyfrowywania wartości, które mają `IsEncrypted` ustawić atrybutu `true`. Ponieważ zaszyfrowany tekst zawiera informacje o certyfikat używany do szyfrowania, nie trzeba ręcznie odnaleźć certyfikatu. Tylko ten certyfikat musi być zainstalowany w węźle, czy usługa jest uruchomiona na. Po prostu Wywołaj `DecryptValue()` metoda pobierania oryginalnej wartości tajny:

```csharp
ConfigurationPackage configPackage = this.Context.CodePackageActivationContext.GetConfigurationPackageObject("Config");
SecureString mySecretValue = configPackage.Settings.Sections["MySettings"].Parameters["MySecret"].DecryptValue()
```

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [aplikacji i usług zabezpieczeń](service-fabric-application-and-service-security.md)

<!-- Links -->
[key-vault-get-started]:../key-vault/key-vault-get-started.md
[config-package]: service-fabric-application-and-service-manifests.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md

<!-- Images -->
[overview]:./media/service-fabric-application-secret-management/overview.png
