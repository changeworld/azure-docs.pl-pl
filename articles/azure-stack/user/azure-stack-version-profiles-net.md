---
title: Za pomocą profilami wersji interfejsu API przy użyciu zestawu .NET SDK usługi Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o korzystaniu z profilami wersji interfejsu API przy użyciu platformy .NET w usłudze Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2018
ms.author: sethm
ms.reviewer: sijuman
ms.openlocfilehash: 20e96ad7a99fdb8c90f3b7990965d7225aef8be0
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/18/2018
ms.locfileid: "53555017"
---
# <a name="use-api-version-profiles-with-net-in-azure-stack"></a>Profilami wersji interfejsu API za pomocą platformy .NET w usłudze Azure Stack

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

Zestaw .NET SDK usługi Azure Stack Resource Manager oferuje narzędzia ułatwiające tworzenie i zarządzanie infrastrukturą. Dostawcy zasobów w zestawie SDK obejmują obliczeniowych, sieci, magazynu, usługi aplikacji i [KeyVault](../../key-vault/key-vault-whatis.md). Zestaw SDK platformy .NET zawiera 14 pakietów NuGet. Te pakiety musi zostać pobrany do swojego rozwiązania projekt za każdym razem, zawiera informacje o profilu. Jednak specjalnie Pobierz dostawcę zasobów, które będzie używany dla 2018-03-01-hybrydowego lub 2017-03-09-profile, aby zoptymalizować pamięć dla swojej aplikacji. Każdy pakiet składa się z dostawcy zasobów, w odpowiedniej wersji interfejsu API i profil interfejsu API, do której należy. Profile interfejsu API w zestawie SDK .NET Włącz projektowania aplikacji w chmurze hybrydowej, która ułatwia przełączanie między zasobami w usłudze Azure Stack i globalnych zasobów platformy Azure.

## <a name="net-and-api-version-profiles"></a>Profile wersji platformy .NET i interfejsu API

Profil interfejsu API jest kombinacją dostawcy zasobów i wersje interfejsów API. Aby pobrać najnowsze, najbardziej stabilny wersję każdego typu zasobu w pakiecie dostawcy zasobów, można użyć profilu usługi interfejsu API.

-   Aby korzystać z najnowszej wersji wszystkich usług, użyj **najnowsze** profilu pakietów. Ten profil jest częścią **Microsoft.Azure.Management** pakietu NuGet.

-   Aby korzystać z usług, które są zgodne z usługą Azure Stack, należy użyć **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01. *ResourceProvider*. 0.9.0-preview.nupkg** lub **Microsoft.Azure.Management.Profiles.hybrid\_2017\_03\_09. *ResourceProvider*. 0.9.0-preview.nupkg** pakietów.

    -   Istnieją dwa pakiety dla każdego dostawcy zasobów dla każdego profilu.

    -   Upewnij się, że **ResourceProvider** część powyższej pakietu NuGet jest zmieniana na poprawne dostawcy.

-   Aby korzystać z najnowszej wersji interfejsu API usługi, użyj **najnowsze** profilu określonego pakietu NuGet. Na przykład, jeśli chcesz użyć **interfejsu API najnowsze** wersji usługi obliczeniowe samodzielnie, użyj **najnowsze** profil **obliczenia** pakietu. **Najnowsze** profilu jest częścią **Microsoft.Azure.Management** pakietu NuGet.

-   Aby użyć określonej wersji interfejsu API dla typu zasobu w dostawcy określonego zasobu, należy użyć określonej wersji interfejsu API, zdefiniowane wewnątrz pakietu.

Możesz połączyć wszystkie opcje dostępne w tej samej aplikacji.

## <a name="install-the-azure-net-sdk"></a>Zainstaluj zestaw Azure .NET SDK

1.  Zainstaluj oprogramowanie Git. Aby uzyskać instrukcje, zobacz [wprowadzenie — Instalowanie usługi Git][].

2.  Aby zainstalować poprawną pakietów NuGet, zobacz [Znajdowanie i instalowanie pakietu][].

3.  Pakiety, które muszą być zainstalowane, zależy od wersji profilu, którego chcesz użyć. Nazwy pakietu dla wersji profilu są:

    1.  **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01. *ResourceProvider*. 0.9.0-preview.nupkg**

    2.  **Microsoft.Azure.Management.Profiles.hybrid\_2017\_03\_09. *ResourceProvider*. 0.9.0-preview.nupkg**

4.  Aby zainstalować poprawną pakietów NuGet dla programu Visual Studio Code, zobacz następujące łącze, aby pobrać [instrukcje Menedżera pakietów NuGet][].

5.  Jeśli nie jest dostępny, Utwórz subskrypcję i Zapisz identyfikator subskrypcji ma być używany w dalszej części. Aby uzyskać instrukcje utworzyć subskrypcję, zobacz [Tworzenie subskrypcji ofert w usłudze Azure Stack][].

6.  Tworzenie jednostki usługi i Zapisz identyfikator klienta oraz klucz tajny klienta. Aby uzyskać instrukcje dotyczące sposobu tworzenia jednostki usługi dla usługi Azure Stack, zobacz [Zapewnianie dostępu aplikacji do usługi Azure Stack][]. Identyfikator klienta jest również znany jako identyfikator aplikacji, podczas tworzenia jednostki usługi.

7.  Upewnij się, że jednostki usługi rola właściciel/Współautor subskrypcji. Aby uzyskać instrukcje dotyczące sposobu przypisywania roli do jednostki usługi, zobacz [Zapewnianie dostępu aplikacji do usługi Azure Stack][].

## <a name="prerequisites"></a>Wymagania wstępne

Za pomocą zestawu Azure .NET SDK usługi Azure Stack, należy podać następujące wartości, a następnie ustaw wartości zmiennych środowiskowych. Aby ustawić zmienne środowiskowe, zobacz instrukcje pod tabelą systemu operacyjnego.

| Wartość                     | Zmienne środowiskowe   | Opis                                                                                                             |
|---------------------------|-------------------------|-------------------------------------------------------------------------------------------------------------------------|
| Identyfikator dzierżawy                 | AZURE_TENANT_ID       | Zalety usługi Azure Stack [ *identyfikator dzierżawy*][].                                                                          |
| Identyfikator klienta                 | AZURE_CLIENT_ID       | Usługa identyfikator podmiotu zabezpieczeń aplikacji zapisywał informacje o jednostkę usługi został utworzony w poprzedniej sekcji tego artykułu. |
| Identyfikator subskrypcji           | AZURE_SUBSCRIPTION_ID | [ *Identyfikator subskrypcji* ][] jest sposób uzyskiwania dostępu do oferty w usłudze Azure Stack.                                                      |
| Wpis tajny klienta             | AZURE_CLIENT_SECRET   | Klucz tajny aplikacji jednostki usługi zapisane podczas tworzenia nazwy głównej usługi.                                      |
| Punkt końcowy usługi Resource Manager | ARM_ENDPOINT           | Zobacz [ *punktu końcowego usługi Azure Stack Resource Manager*][].                                                                    |
| Lokalizacja                  | RESOURCE_LOCATION     | Lokalizacja dla usługi Azure Stack.

Aby znaleźć identyfikator dzierżawy usługi Azure Stack, postępuj zgodnie z instrukcjami przedstawionymi [tutaj](../azure-stack-csp-ref-operations.md). Aby ustawić zmienne środowiskowe, wykonaj następujące czynności:

### <a name="microsoft-windows"></a>Microsoft Windows

Aby ustawić zmienne środowiskowe w wierszu polecenia Windows, użyj następującego formatu:

```shell
Set Azure_Tenant_ID=Your_Tenant_ID
```

### <a name="macos-linux-and-unix-based-systems"></a>System macOS, Linux i komputerach z systemem Unix

W systemach Unix, na podstawie można użyć następującego polecenia:

```shell
Export Azure_Tenant_ID=Your_Tenant_ID
```

### <a name="the-azure-stack-resource-manager-endpoint"></a>Punkt końcowy usługi Azure Stack Resource Manager

Menedżer zasobów platformy Azure to platforma zarządzania, która umożliwia administratorom wdrażanie, zarządzanie i monitorowanie zasobów platformy Azure. Usługa Azure Resource Manager może obsługiwać te zadania jako grupę, a nie indywidualnie, w ramach jednej operacji.

Możesz uzyskać informacje o metadanych z punktu końcowego usługi Resource Manager. Punkt końcowy zwraca plik w formacie JSON z informacjami wymaganymi do uruchomienia kodu.

Należy zwrócić uwagę następujące kwestie:

- **ResourceManagerUrl** jest w usłudze Azure Stack Development Kit (ASDK): https://management.local.azurestack.external/

- **ResourceManagerUrl** w systemach zintegrowanych jest: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/` Aby pobrać metadane wymagane: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`

Przykładowy plik JSON:

```json
{ 
   "galleryEndpoint": "https://portal.local.azurestack.external:30015/",
   "graphEndpoint": "https://graph.windows.net/",
   "portal Endpoint": "https://portal.local.azurestack.external/",
   "authentication": 
      {
      "loginEndpoint": "https://login.windows.net/",
      "audiences": ["https://management.yourtenant.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
      }
}
```

## <a name="existing-api-profiles"></a>Istniejące profile interfejsu API

1.  **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01. *ResourceProvider*. 0.9.0-preview.nupkg**: Profil najnowsze stworzona z myślą o usłudze Azure Stack. Użyj tego profilu usługi najbardziej zgodnych z usługą Azure Stack, tak długo, jak korzystasz z sygnatury 1808 lub więcej.

2.  **Microsoft.Azure.Management.Profiles.hybrid\_2017\_03\_09. *ResourceProvider*. 0.9.0-preview.nupkg**: Jeśli używany jest niższa niż kompilacji 1808 sygnatury, za pomocą tego profilu.

3.  **Najnowsze**: Profil składający się z najnowszymi wersjami programów wszystkich usług. Za pomocą najnowszej wersji wszystkich usług. Ten profil jest częścią **Microsoft.Azure.Management** pakietu NuGet.

Aby uzyskać więcej informacji na temat profilów Azure Stack i interfejsu API, zobacz [Podsumowanie profilami interfejsu API][].

## <a name="azure-net-sdk-api-profile-usage"></a>Użycie platformy Azure profilu interfejsu API zestawu SDK platformy .NET

Poniższy kod powinien służyć do tworzenia wystąpienia klienta zarządzania zasobów. Podobny kod może służyć do tworzenia wystąpienia inni dostawcy zasobów (na przykład obliczeniowych, sieciowych i magazynowych) klientów. 

```csharp
var client = new ResourceManagementClient(armEndpoint, credentials)
{
    SubscriptionId = subscriptionId
};
```

`credentials` Parametr w powyższym kodzie jest wymagany do utworzenia wystąpienia klienta. Poniższy kod generuje token uwierzytelniania według Identyfikatora dzierżawy i jednostki usługi.

```csharp
var azureStackSettings = getActiveDirectoryServiceSettings(armEndpoint);
var credentials = ApplicationTokenProvider.LoginSilentAsync(tenantId, servicePrincipalId, servicePrincipalSecret, azureStackSettings).GetAwaiter().GetResult();
```
`getActiveDirectoryServiceSettings` Wywołania w kodzie pobiera punktów końcowych usługi Azure Stack z punktem końcowym metadanych. Stwierdza, zmienne środowiskowe z wykonanego wywołania: 

```csharp
public static ActiveDirectoryServiceSettings getActiveDirectoryServiceSettings(string armEndpoint)
{
    var settings = new ActiveDirectoryServiceSettings();
    try
    {
        var request = (HttpWebRequest)HttpWebRequest.Create(string.Format("{0}/metadata/endpoints?api-version=1.0", armEndpoint));
        request.Method = "GET";
        request.UserAgent = ComponentName;
        request.Accept = "application/xml";
        using (HttpWebResponse response = (HttpWebResponse)request.GetResponse())
        {
            using (StreamReader sr = new StreamReader(response.GetResponseStream()))
            {
                var rawResponse = sr.ReadToEnd();
                var deserialized = JObject.Parse(rawResponse);
                var authenticationObj = deserialized.GetValue("authentication").Value<JObject>();
                var loginEndpoint = authenticationObj.GetValue("loginEndpoint").Value<string>();
                var audiencesObj = authenticationObj.GetValue("audiences").Value<JArray>();
                settings.AuthenticationEndpoint = new Uri(loginEndpoint);
                settings.TokenAudience = new Uri(audiencesObj[0].Value<string>());
                settings.ValidateAuthority = loginEndpoint.TrimEnd('/').EndsWith("/adfs", StringComparison.OrdinalIgnoreCase) ? false : true;
            }
        }
    }
    catch (Exception ex)
    {
        Console.WriteLine(String.Format("Could not get AD service settings. Exception: {0}", ex.Message));
    }
    return settings;
}
```
Spowoduje to włączenie przy użyciu pakietów NuGet profilu interfejsu API, aby wdrożyć aplikację pomyślnym do usługi Azure Stack.

## <a name="samples-using-api-profiles"></a>Przykłady korzystania z profilów interfejsu API

Poniższe przykłady może służyć jako odwołanie do tworzenia rozwiązań przy użyciu profilów platformy .NET i interfejsu API usługi Azure Stack.
- [Zarządzanie grupami zasobów](https://github.com/Azure-Samples/hybrid-resources-dotnet-manage-resource-group)
- [Zarządzanie kontami magazynu](https://github.com/Azure-Samples/hybird-storage-dotnet-manage-storage-accounts)
- [Zarządzanie maszyną wirtualną](https://github.com/Azure-Samples/hybrid-compute-dotnet-manage-vm)

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji o profilach interfejsu API Zobacz:

- [Zarządzanie profilami wersji interfejsu API w usłudze Azure Stack](azure-stack-version-profiles.md)
- [Wersje interfejsu API dostawcy zasobów obsługiwane przez profile](azure-stack-profiles-azure-resource-manager-versions.md)

  [Wprowadzenie — Instalowanie usługi Git]: https://git-scm.com/book/en/v2/Getting-Started-Installing-Git
  [Znajdowanie i instalowanie pakietu]: /nuget/tools/package-manager-ui
  [Instrukcje Menedżera pakietów NuGet]: https://marketplace.visualstudio.com/items?itemName=jmrog.vscode-nuget-package-manager
  [Tworzenie subskrypcji ofert w usłudze Azure Stack]: ../azure-stack-subscribe-plan-provision-vm.md
  [Zapewnianie dostępu aplikacji do usługi Azure Stack]: ../azure-stack-create-service-principals.md
  [* dzierżawy identyfikator *]: ../azure-stack-identity-overview.md
  [* subskrypcji identyfikator *]: ../azure-stack-plan-offer-quota-overview.md#subscriptions
  [* Usługa Azure Stack Resource Manager endpoint *]: ../user/azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint
  [Podsumowanie profilami interfejsu API]: ../user/azure-stack-version-profiles.md#summary-of-api-profiles
  [Test Project to Virtual Machine, vNet, resource groups, and storage account]: https://github.com/seyadava/azure-sdk-for-net-samples/tree/master/TestProject
  [Use Azure PowerShell to create a service principal with a certificate]: ../azure-stack-create-service-principals.md
  [Run unit tests with Test Explorer.]: /visualstudio/test/run-unit-tests-with-test-explorer?view=vs-2017
