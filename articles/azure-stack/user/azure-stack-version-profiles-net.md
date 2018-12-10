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
ms.openlocfilehash: cfebbdb9b88a1de6a05f06e6ed72ebc9cddddcf6
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2018
ms.locfileid: "53074455"
---
# <a name="use-api-version-profiles-with-net-in-azure-stack"></a>Profilami wersji interfejsu API za pomocą platformy .NET w usłudze Azure Stack

*Dotyczy: Usługa Azure Stack zintegrowane systemy i usługi Azure Stack Development Kit*

Zestaw .NET SDK usługi Azure Stack Resource Manager oferuje narzędzia ułatwiające tworzenie i zarządzanie infrastrukturą. Dostawcy zasobów w zestawie SDK obejmują obliczeniowych, sieci, magazynu, usługi aplikacji i [KeyVault](../../key-vault/key-vault-whatis.md). Zestaw SDK platformy .NET zawiera 14 pakiety NuGet można pobrać do swojego rozwiązania projekt każdorazowo zawierające informacje o profilu. Jednak specjalnie Pobierz dostawcę zasobów, które będzie używany dla 2018-03-01-hybrydowego lub 2017-03-09-profile, aby zoptymalizować pamięć dla swojej aplikacji. Każdy pakiet składa się z dostawcy zasobów, w odpowiedniej wersji interfejsu API i profil interfejsu API, do której należy. Profile interfejsu API w zestawie SDK .NET Włącz projektowania aplikacji w chmurze hybrydowej, która ułatwia przełączanie między zasobami w usłudze Azure Stack i globalnych zasobów platformy Azure.

## <a name="net-and-api-version-profiles"></a>Profile wersji platformy .NET i interfejsu API

Profil interfejsu API jest kombinacją dostawcy zasobów i wersje interfejsów API. Aby pobrać najnowsze, najbardziej stabilny wersję każdego typu zasobu w pakiecie dostawcy zasobów, można użyć profilu usługi interfejsu API.

-   Aby korzystać z najnowszej wersji wszystkich usług, użyj **najnowsze** profilu pakietów. Ten profil jest częścią **Microsoft.Azure.Management** pakietu NuGet.

-   Aby korzystać z usług, które są zgodne z usługą Azure Stack, należy użyć **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01. *ResourceProvider*. 0.9.0-preview.nupkg** lub **Microsoft.Azure.Management.Profiles.hybrid\_2017\_03\_09. *ResourceProvider*. 0.9.0-preview.nupkg** pakietów.

    -   Istnieją dwa pakiety dla każdego dostawcy zasobów dla każdego profilu.

    -   Upewnij się, że **ResourceProvider** część powyższej pakietu NuGet jest zmieniana na poprawne dostawcy.

-   Aby korzystać z najnowszej wersji interfejsu API usługi, użyj **najnowsze** profilu określonego pakietu NuGet. Na przykład, jeśli chcesz użyć **interfejsu API najnowsze** wersji usługi obliczeniowe samodzielnie, użyj **najnowsze** profil **obliczenia** pakietu. **Najnowsze** profilu jest częścią **Microsoft.Azure.Management** pakietu NuGet.

-   Aby użyć określonej wersji interfejsu API dla typu zasobu w dostawcy określonego zasobu, należy użyć określonej wersji interfejsu API, zdefiniowane wewnątrz pakietu.

Należy pamiętać, że możesz połączyć wszystkie opcje dostępne w tej samej aplikacji.

## <a name="install-the-azure-net-sdk"></a>Zainstaluj zestaw Azure .NET SDK

1.  Zainstaluj oprogramowanie Git. Aby uzyskać instrukcje, zobacz [wprowadzenie — Instalowanie usługi Git][].

2.  Aby zainstalować poprawną pakietów NuGet, zobacz [Znajdowanie i instalowanie pakietu][].

3.  Pakiety, które muszą być zainstalowane, zależy od wersji profilu, którego chcesz użyć. Nazwa pakietu dla wersji profilu są:

    1.  **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01. *ResourceProvider*. 0.9.0-preview.nupkg**

    2.  **Microsoft.Azure.Management.Profiles.hybrid\_2017\_03\_09. *ResourceProvider*. 0.9.0-preview.nupkg**

4.  Aby zainstalować poprawną pakietów NuGet dla programu Visual Studio Code, zobacz następujące łącze, aby pobrać [instrukcje Menedżera pakietów NuGet][].

5.  Jeśli nie jest dostępny, Utwórz subskrypcję i Zapisz identyfikator subskrypcji ma być używany w dalszej części. Aby uzyskać instrukcje utworzyć subskrypcję, zobacz [Tworzenie subskrypcji ofert w usłudze Azure Stack][].

6.  Tworzenie jednostki usługi i Zapisz identyfikator klienta oraz klucz tajny klienta. Aby uzyskać instrukcje dotyczące sposobu tworzenia jednostki usługi dla usługi Azure Stack, zobacz [Zapewnianie dostępu aplikacji do usługi Azure Stack][]. Należy pamiętać, że identyfikator klienta jest również nazywany identyfikator aplikacji podczas tworzenia nazwy głównej usługi.

7.  Upewnij się, że jednostki usługi rola właściciel/Współautor subskrypcji. Aby uzyskać instrukcje dotyczące sposobu przypisywania roli do jednostki usługi, zobacz [Zapewnianie dostępu aplikacji do usługi Azure Stack][].

## <a name="prerequisites"></a>Wymagania wstępne

Za pomocą zestawu Azure .NET SDK usługi Azure Stack, należy podać następujące wartości, a następnie ustaw wartości zmiennych środowiskowych. Aby ustawić zmienne środowiskowe, zobacz instrukcje pod tabelą systemu operacyjnego.

| Wartość                     | Zmienne środowiskowe   | Opis                                                                                                             |
|---------------------------|-------------------------|-------------------------------------------------------------------------------------------------------------------------|
| Identyfikator dzierżawy                 | AZURE_TENANT_ID       | Zalety usługi Azure Stack [ *identyfikator dzierżawy*][].                                                                          |
| Identyfikator klienta                 | AZURE_CLIENT_ID       | Usługa identyfikator podmiotu zabezpieczeń aplikacji zapisywał informacje o jednostkę usługi został utworzony w poprzedniej sekcji tego artykułu. |
| Identyfikator subskrypcji           | AZURE_SUBSCRIPTION_ID | [ *Identyfikator subskrypcji* ][] jest sposób uzyskiwania dostępu do oferty w usłudze Azure Stack.                                                      |
| Wpis tajny klienta             | AZURE_CLIENT_SECRET   | Klucz tajny aplikacji jednostki usługi zapisane podczas tworzenia nazwy głównej usługi.                                      |
| Punkt końcowy usługi Resource Manager | ARM_ENDPOINT           | Zobacz [ *punktu końcowego Menedżera zasobów usługi Azure Stack*][].                                                                    |

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

### <a name="the-azure-stack-resource-manager-endpoint"></a>Punktu końcowego Menedżera zasobów usługi Azure Stack

Menedżer zasobów platformy Azure to platforma zarządzania, która umożliwia administratorom wdrażanie, zarządzanie i monitorowanie zasobów platformy Azure. Usługa Azure Resource Manager może obsługiwać te zadania jako grupę, a nie indywidualnie, w ramach jednej operacji.

Możesz uzyskać informacje o metadanych z punktu końcowego usługi Resource Manager. Punkt końcowy zwraca plik w formacie JSON z informacjami wymaganymi do uruchomienia kodu.

Należy zwrócić uwagę następujące kwestie:

- **ResourceManagerUrl** jest w usłudze Azure Stack Development Kit (ASDK): https://management.local.azurestack.external/

- **ResourceManagerUrl** w systemach zintegrowanych: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/` do pobierania metadanych wymagane: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`

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

1.  **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01. *ResourceProvider*. 0.9.0-preview.nupkg**: najnowsze profilu stworzona z myślą o usłudze Azure Stack. Użyj tego profilu usługi najbardziej zgodnych z usługą Azure Stack, tak długo, jak korzystasz z sygnatury 1808 lub więcej.

2.  **Microsoft.Azure.Management.Profiles.hybrid\_2017\_03\_09. *ResourceProvider*. 0.9.0-preview.nupkg**: Jeśli użytkownik pracuje na sygnaturę niższa niż kompilacji 1808, za pomocą tego profilu.

3.  **Najnowsze**: profil składający się z najnowszymi wersjami programów wszystkich usług. Za pomocą najnowszej wersji wszystkich usług. Ten profil jest częścią **Microsoft.Azure.Management** pakietu NuGet.

Aby uzyskać więcej informacji na temat profilów Azure Stack i interfejsu API, zobacz [Podsumowanie profilami interfejsu API][].

## <a name="azure-net-sdk-api-profile-usage"></a>Użycie platformy Azure profilu interfejsu API zestawu SDK platformy .NET

Poniższy kod powinien służyć do tworzenia wystąpienia klienta profilu. Ten parametr jest tylko wymagane dla usługi Azure Stack lub innych chmur prywatnych. Global Azure jest już te ustawienia domyślne.

Poniższy kod jest potrzebne do uwierzytelnienia nazwy głównej usługi w usłudze Azure Stack. Identyfikator i base uwierzytelniania, który jest specyficzny dla usługi Azure Stack, tworzy token przez dzierżawcę.

```csharp
public class CustomLoginCredentials : ServiceClientCredentials
{
    private string clientId;
    private string clientSecret;
    private string resourceId;
    private string tenantId;

    private const string authenticationBase = "https://login.windows.net/{0}";

    public CustomLoginCredentials(string servicePrincipalId, string servicePrincipalSecret, string azureEnvironmentResourceId, string azureEnvironmentTenandId)
    {
        clientId = servicePrincipalId;
        clientSecret = servicePrincipalSecret;
        resourceId = azureEnvironmentResourceId;
        tenantId = azureEnvironmentTenandId;
    }
```

Spowoduje to włączenie przy użyciu pakietów NuGet profilu interfejsu API, aby wdrożyć aplikację pomyślnym do usługi Azure Stack.

## <a name="define-azure-stack-environment-setting-functions"></a>Zdefiniuj funkcje ustawienie środowiska Azure Stack

Na potrzeby uwierzytelniania jednostki usługi do środowiska usługi Azure Stack, użyj następującego kodu:

```csharp
private string AuthenticationToken { get; set; }
public override void InitializeServiceClient<T>(ServiceClient<T> client)
{
    var authenticationContext = new AuthenticationContext(String.Format(authenticationBase, tenantId));
    var credential = new ClientCredential(clientId, clientSecret);
    var result = authenticationContext.AcquireTokenAsync(resource: resourceId,
    clientCredential: credential).Result;
    if (result == null)
    {
        throw new InvalidOperationException("Failed to obtain the JWT token");
    }
    AuthenticationToken = result.AccessToken;
}
```

Ustawienie to zastępuje zainicjować klienta usługi do uwierzytelniania w usłudze Azure Stack.

## <a name="samples-using-api-profiles"></a>Przykłady korzystania z profilów interfejsu API

Można użyć poniższych przykładów, znaleziono w repozytoriach usługi GitHub jako odwołanie do tworzenia rozwiązań przy użyciu profilów platformy .NET i interfejsu API usługi Azure Stack.

-   [Projekt testu do maszyny wirtualnej, sieci wirtualnej, grupy zasobów i konta magazynu][]
-   Zarządzanie maszynami wirtualnymi przy użyciu platformy .NET

### <a name="sample-unit-test-project"></a>Przykładowy projekt testu jednostki 

1.  Sklonuj repozytorium przy użyciu następującego polecenia:

    ```shell
    git clone https://github.com/Azure-Samples/hybrid-compute-dotnet-manage-vm.git
    ```

2.  Tworzenie jednostki usługi platformy Azure i przypisywanie roli dostęp do subskrypcji. Aby uzyskać instrukcje dotyczące tworzenia jednostki usługi, zobacz [Tworzenie jednostki usługi przy użyciu certyfikatu za pomocą programu Azure PowerShell][].

3.  Pobrać wymagane następujące wartości:

    1.  Identyfikator dzierżawy
    2.  Identyfikator klienta
    3.  Wpis tajny klienta
    4.  Identyfikator subskrypcji
    5.  Punkt końcowy usługi Resource Manager

4.  Ustaw następujące zmienne środowiskowe, korzystając z informacji pobrane z usługi jednostki, zostały utworzone za pomocą wiersza polecenia:

    1.  Eksportuj AZURE_TENANT_ID = {swój identyfikator dzierżawy}
    2.  Eksportuj AZURE_CLIENT_ID = {identyfikatora klienta}
    3.  Eksportuj AZURE_CLIENT_SECRET = {klucz tajny klienta}
    4.  Eksportuj AZURE_SUBSCRIPTION_ID = {identyfikator subskrypcji}
    5.  Eksportuj ARM_ENDPOINT = {adres URL Menedżer usłudze Azure Stack Resource}

   W Windows, użyj **ustaw** zamiast **wyeksportować**.

5.  Upewnij się, że ustawiono zmienną lokalizacji dla danej lokalizacji usługi Azure Stack. Na przykład lokalnego = "local".

6.  Ustaw poświadczenia logowania niestandardowych, umożliwiające uwierzytelnianie w usłudze Azure Stack. Należy pamiętać, że ta część kodu znajduje się w tym przykładzie w folderze autoryzacji.

   ```csharp
   public class CustomLoginCredentials : ServiceClientCredentials
   {
       private string clientId;
       private string clientSecret;
       private string resourceId;
       private string tenantId;
       private const string authenticationBase = "https://login.windows.net/{0}";
       public CustomLoginCredentials(string servicePrincipalId, string servicePrincipalSecret, string azureEnvironmentResourceId, string azureEnvironmentTenandId)
       {
           clientId = servicePrincipalId;
           clientSecret = servicePrincipalSecret;
           resourceId = azureEnvironmentResourceId;
           tenantId = azureEnvironmentTenandId;
       }
   private string AuthenticationToken { get; set; }
   ```

7.  Dodaj następujący kod, korzystając z usługi Azure Stack do zastąpienia zainicjować klienta usługi do uwierzytelniania w usłudze Azure Stack. Należy pamiętać, że część kodu jest już zawarty w tym przykładzie, w folderze autoryzacji.

   ```csharp
   public override void InitializeServiceClient<T>(ServiceClient<T> client)
   {
      var authenticationContext = new AuthenticationContext(String.Format(authenticationBase, tenantId));
      var credential = new ClientCredential(clientId, clientSecret);
      var result = authenticationContext.AcquireTokenAsync(resource: resourceId,
                clientCredential: credential).Result;
      if (result == null)
      {
          throw new InvalidOperationException("Failed to obtain the JWT token");
      }
      AuthenticationToken = result.AccessToken;
   }
   ```
 
8.  Przy użyciu Menedżera pakietów NuGet, wyszukaj "2018-03-01-hybrydowe" i zainstaluj pakiety skojarzone z tym profilem dla dostawców zasobów obliczeniowych, sieci, magazynu, KeyVault i usług aplikacji.

2.  Wewnątrz każdego zadania w pliku CS należy ustawić parametry, które są wymagane do pracy z usługą Azure Stack. Przykład znajduje się w następujący sposób zadania `CreateResourceGroupTest`:

   ```csharp
   var location = Environment.GetEnvironmentVariable("AZURE_LOCATION");
   var baseUriString = Environment.GetEnvironmentVariable("AZURE_BASE_URL");
   var resourceGroupName = Environment.GetEnvironmentVariable("AZURE_RESOURCEGROUP");
   var servicePrincipalId = Environment.GetEnvironmentVariable("AZURE_CLIENT_ID");
   var servicePrincipalSecret = Environment.GetEnvironmentVariable("AZURE_CLIENT_SECRET");
   var azureResourceId = Environment.GetEnvironmentVariable("AZURE_RESOURCE_ID");
   var tenantId = Environment.GetEnvironmentVariable("AZURE_TENANT_ID");
   var subscriptionId = Environment.GetEnvironmentVariable("AZURE_SUBSCRIPTION_ID");
   var credentials = new CustomLoginCredentials(servicePrincipalId, servicePrincipalSecret, azureResourceId, tenantId);
   ```

1.  Kliknij prawym przyciskiem myszy na poszczególnych zadań i wybierz **Uruchom test**.

    1.  Zielone znaczniki wyboru na stronie okno okienka alert, że każde zadanie podrzędne został utworzony pomyślnie zależnie od parametrów podanych. Sprawdź, czy w Twojej subskrypcji usługi Azure Stack, aby upewnić się, że zasoby zostały pomyślnie utworzone.

    2.  Aby uzyskać więcej informacji o sposobach uruchamiania testów jednostkowych, zobacz [Uruchamianie testów jednostkowych w Eksploratorze testów.][]

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
  [* tekst usługa Azure Stack resource manager punktu końcowego *]: ../user/azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint
  [Podsumowanie profilami interfejsu API]: ../user/azure-stack-version-profiles.md#summary-of-api-profiles
  [Projekt testu do maszyny wirtualnej, sieci wirtualnej, grupy zasobów i konta magazynu]: https://github.com/seyadava/azure-sdk-for-net-samples/tree/master/TestProject
  [Tworzenie jednostki usługi przy użyciu certyfikatu za pomocą programu Azure PowerShell]: ../azure-stack-create-service-principals.md
  [Uruchamianie testów jednostkowych w Eksploratorze testów.]: /visualstudio/test/run-unit-tests-with-test-explorer?view=vs-2017
