---
title: Przy użyciu profilów wersji interfejsu API z Przejdź w stosie Azure | Dokumentacja firmy Microsoft
description: Informacje o używaniu profile w wersji interfejsu API z Przejdź w stosie Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: dd2d0c46c0829a73d32c96b506b9f2111eda3c84
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
ms.locfileid: "34010068"
---
# <a name="use-api-version-profiles-with-go-in-azure-stack"></a>Profile w wersji interfejsu API za pomocą Przejdź w stosie Azure

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

## <a name="go-and-version-profiles"></a>Profile Przejdź i wersji

Profil jest kombinacją różnych typów zasobów z różnymi wersjami z różnych usług. Przy użyciu profilu pomoże Ci mieszać i dopasowywać między różnych typów zasobów. Profile zapewniają następujące korzyści:

 - Trwałość aplikacji poprzez zablokowanie do określonej wersji interfejsu API.
 - Zgodność aplikacji z stosu Azure i regionalnych centrach danych platformy Azure.

W zestawie SDK Przejdź profile są dostępne w obszarze Profile / ścieżki z ich wersji w **RRRR-MM-DD** format. Teraz, najnowsze Azure stosu jest wersja profilu **2017-03-09**. Aby zaimportować danej usługi z profilu, należy zaimportować jego odpowiedniego modułu z profilu. Na przykład, aby zaimportować **obliczeniowe** usługi **2017-03-09** profilu:

````go
import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/compute/mgmt/compute" 
````

## <a name="install-azure-sdk-for-go"></a>Zainstaluj zestaw Azure SDK dla przejdź

  1. Zainstaluj usługę Git. Aby uzyskać instrukcje, zobacz [wprowadzenie — instalowanie Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
  2. Zainstaluj [Przejdź język programowania](https://golang.org/dl).  
  Profile interfejsu API platformy Azure będzie wymagać Przejdź wersji 1,9 lub nowszej.
  3. Zainstaluj zestaw Azure SDK Przejdź i jego zależności, uruchamiając następujące polecenie bash:
  ```
    go get -u -d github.com/Azure/azure-sdk-for-go/...
  ```

### <a name="the-go-sdk"></a>Wybierz zestaw SDK

Można znaleźć więcej informacji o zestawie SDK Azure przejdź na:
- Azure Przejdź SDK w [Instalowanie zestawu Azure SDK dla Przejdź](https://docs.microsoft.com/go/azure/azure-sdk-go-install).
- Przejdź zestaw SDK usługi Azure jest ogólnie dostępna w witrynie GitHub pod [azure sdk dla Przejdź](https://github.com/Azure/azure-sdk-for-go).

### <a name="go-autorest-dependencies"></a>Przejdź AutoRest zależności

Zestaw SDK Przejdź zależy od modułów Azure Przejdź-AutoRest do wysyłania żądań REST do punktów końcowych usługi Azure Resource Manager. Musisz zaimportować zależności modułu Azure Przejdź-AutoRest z [Azure Przejdź-AutoRest w serwisie GitHub](https://github.com/Azure/go-autorest). Można znaleźć polecenia install bash w **zainstalować** sekcji.

## <a name="how-to-use-go-sdk-profiles-on-azure-stack"></a>Sposobu używania profilów SDK przejdź na stosie Azure

Aby uruchomić przykładowy kod przejdź na stosie Azure:
  1. Zainstaluj zestaw Azure SDK dla Przejdź i jego zależności. Instrukcje zawiera Poprzednia sekcja, [Zainstaluj zestaw Azure SDK for Przejdź](#install-azure-sdk-for-go).
  2. Pobierz informacje metadanych z punktu końcowego Menedżera zasobów. Punkt końcowy zwraca informacje wymagane do uruchomienia kodu Przejdź pliku JSON.

  > [!Note]  
  > **ResourceManagerUrl** jest w Azure stosu Development Kit (ASDK): `https://management.local.azurestack.external/`  
  > **ResourceManagerUrl** w systemach zintegrowane jest: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`  
  > Aby pobrać metadane wymagane: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`
  
  Przykładowy plik JSON:

  ```json
  { "galleryEndpoint": "https://portal.local.azurestack.external:30015/",  
    "graphEndpoint": "https://graph.windows.net/",  
    "portal Endpoint": "https://portal.local.azurestack.external/", 
    "authentication": {
      "loginEndpoint": "https://login.windows.net/", 
      "audiences": ["https://management.<yourtenant>.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
    }
  }
  ```

  3. Jeśli nie jest dostępny, Utwórz subskrypcję i Zapisz identyfikator subskrypcji do użycia później. Aby uzyskać informacje na temat tworzenia subskrypcji, zobacz [tworzyć subskrypcje ofert w stosie Azure](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm). 
  4. Tworzenie nazwy głównej usługi z zakresem "Subskrypcji" i **właściciela** roli. Zapisz nazwy główne usług, identyfikator i klucz tajny. Informacje dotyczące tworzenia nazwy głównej usługi dla stosu Azure, zobacz [Tworzenie nazwy głównej usługi](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#create-service-principal-for-azure-ad). Konfigurowanie środowiska Azure stosu.
  5. Zaimportuj moduł usługi z profilu SDK Przejdź w kodzie. Bieżąca wersja profilu stosu Azure to **2017-03-09**. Na przykład, aby zaimportować moduł sieci z **2017-03-09** typ profilu: 

  ````go
    package main 
    import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
  ````
  
  6. W funkcji, Utwórz i uwierzytelniania klienta z **nowy** wywołanie funkcji klienta. Aby utworzyć klienta sieci wirtualnej, można użyć poniższego kodu:  

  ````go
  package main 

  import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network" 

  func main() { 
      vnetClient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
      vnetClient.Authorizer = autorest.NewBearerAuthorizer(token)
  ````

  Ustaw `<baseURI>` do **ResourceManagerUrl** wartość używana w drugim kroku.
  Ustaw `<subscriptionID>` do **SubscriptionID** wartości zapisane w kroku 3.
  Aby utworzyć token, zobacz Uwierzytelnianie poniższej sekcji.  

  7. Wywołanie metody interfejsu API przy użyciu klienta, który został utworzony w poprzednim kroku. Na przykład, aby utworzyć sieć wirtualną przy użyciu naszego klienta w poprzednim kroku: 
  
````go
package main

import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network" 
func main() { 
  vnetC1ient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
  vnetClient .Authorizer = autorest.NewBearerAuthorizer(token)

  vnetClient .CreateOrUpdate( ) 
````
  
  Pełny przykład tworzenia sieci wirtualnej na stosie Azure przy użyciu profilu przejdź zestawu SDK, zobacz [przykład](#example).

## <a name="authentication"></a>Authentication

Można pobrać właściwości autoryzujący z usługi Azure Active Directory przy użyciu Przejdź zestawu SDK, należy zainstalować moduły Go AutoRest. Te moduły powinna już zainstalowany z instalacją "Przejdź zestawu SDK"; Jeśli nie, zainstaluj [pakiet uwierzytelniania w usłudze GitHub](https://github.com/Azure/go-autorest/tree/master/autorest/adal).

Obiekt autoryzujący musi być ustawiona jako autoryzujący klienta zasobów. Dostępnych jest kilka metod, aby uzyskać obiekt autoryzujący; Aby uzyskać pełną listę można znaleźć tutaj.

W tej sekcji przedstawiono typowy sposób uzyskać tokeny autoryzujący na stosie Azure przy użyciu poświadczeń klienta:

  1. Jeśli nazwy głównej usługi z rolą właściciela subskrypcji jest dostępna, Pomiń ten krok. W przeciwnym razie tworzenie nazwy głównej usługi [instrukcje]( https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals) i przypisz go w roli "właściciela" ograniczone do subskrypcji [instrukcje]( https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#assign-role-to-service-principal). Zapisz identyfikator podmiotu zabezpieczeń aplikacji usługi i klucz tajny. 

  2. Importuj **adal** pakietu z AutoRest Przejdź w kodzie. 
  
  ````go
  package main
  import "github.com/Azure/go-autorest/autorest/adal" 
  ````

  3. Utwórz oauthConfig przy użyciu metody NewOAuthConfig z **adal** modułu. 
  
  ````go
  package main 

  import "github.com/Azure/go-autorest/autorest/ada1" 

  func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
  ````
   
  Ustaw `<activeDirectoryEndpoint>` na wartość "loginEndpoint" właściwości z metadanych ResourceManagerUrl pobrany w poprzedniej sekcji tego dokumentu.
  Ustaw `<tenantID>` wartość identyfikatora dzierżawy usługi Azure stosu 

  4. Na koniec Utwórz token główna usługi przy użyciu metody NewServicePrincipalToken z modułu biblioteki adal. 

  ````go
  package main 

  import "github.com/Azure/go-autorest/autorest/adal" 

  func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
      token, err = adal.NewServicePrincipalToken(
          *oauthConfig,
          clientID,
          clientSecret,
          activeDirectoryResourceID)
      return token, err
  ````
  
  Ustaw `<activeDirectoryResourceID>` na jedną z wartości "odbiorców" pobrać listy z metadanych ResourceManagerUrl w poprzedniej sekcji tego dokumentu.  
  Ustaw `<clientID>` do aplikacji głównej usługi zapisać nazwy głównej usługi został utworzony w poprzedniej sekcji tego dokumentu Identyfikator.  
  Ustaw `<clientSecret>` do aplikacji głównej usługi zapisać nazwy głównej usługi został utworzony w poprzedniej sekcji tego dokumentu klucz tajny.  

## <a name="example"></a>Przykład

W tej sekcji przedstawiono przykładowe kod Przejdź w celu utworzenia sieci wirtualnej Azure stosu. Przykłady pełną Przejdź zestawu SDK można znaleźć [repozytorium przykłady Azure SDk Przejdź](https://github.com/Azure-Samples/azure-sdk-for-go-samples). Stos przykładów dla platformy Azure są dostępne w ramach hybrydowego / ścieżce wewnątrz usługi folderów repozytorium.

> [!Note]  
> Aby uruchomić kod w tym przykładzie, sprawdź, czy z subskrypcją użytą ma **sieci** dostawcy zasobów wymienionym **zarejestrowanej**. Aby go zweryfikować, wyszukaj subskrypcję w portalu Azure stosu, a następnie kliknij polecenie **dostawców zasobów.**

1. Zaimportuj wymagane pakiety w kodzie. Aby zaimportować moduł sieci należy używać najnowszej profilu na stosie Azure. 
  
  ````go
  package main

  import (
      "context"
      "fmt"
      "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
      "github.com/Azure/go-autorest/autorest"
      "github.com/Azure/go-autorest/autorest/adal"
      "github.com/Azure/go-autorest/autorest/to"
  )
  ````

2. Zdefiniuj zmienne środowiskowe. Aby utworzyć sieć wirtualną, musisz mieć grupę zasobów. 

  ````go
  var (
      activeDirectoryEndpoint = "yourLoginEndpointFromResourceManagerUrlMetadata"
      tenantID = "yourAzureStackTenantID"
      clientID = "yourServicePrincipalApplicationID"
      clientSecret = "yourServicePrincipalSecret"
      activeDirectoryResourceID = "yourAudienceFromResourceManagerUrlMetadata"
      subscriptionID = "yourSubscriptionID"
      baseURI = "yourResourceManagerURL"
      resourceGroupName = "existingResourceGroupName"
  )
  ````

3. Teraz, gdy zdefiniowano zmienne środowiskowe, Dodaj metodę, aby utworzyć token uwierzytelniania przy użyciu **adal** pakietu. Zobacz szczegółowe informacje o uwierzytelnianiu w poprzedniej sekcji.
  
  ````go
  //CreateToken creates a service principal token
  func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
      token, err = adal.NewServicePrincipalToken(
          *oauthConfig,
          clientID,
          clientSecret,
          activeDirectoryResourceID)
      return token, err
  }
  ````

4. Dodaj metodę main. Metoda główna najpierw pobiera tokenu przy użyciu metody, która jest zdefiniowana w poprzednim kroku. Następnie tworzy klienta przy użyciu modułu sieciowej z profilu. Na koniec tworzy sieć wirtualną. 
  
````go
package main

import (
    "context"
    "fmt"
    "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
    "github.com/Azure/go-autorest/autorest"
    "github.com/Azure/go-autorest/autorest/adal"
    "github.com/Azure/go-autorest/autorest/to"
)

var (
    activeDirectoryEndpoint = "yourLoginEndpointFromResourceManagerUrlMetadata"
    tenantID = "yourAzureStackTenantID"
    clientID = "yourServicePrincipalApplicationID"
    clientSecret = "yourServicePrincipalSecret"
    activeDirectoryResourceID = "yourAudienceFromResourceManagerUrlMetadata"
    subscriptionID = "yourSubscriptionID"
    baseURI = "yourResourceManagerURL"
    resourceGroupName = "existingResourceGroupName"
)

//CreateToken creates a service principal token
func CreateToken() (adal.OAuthTokenProvider, error) {
    var token adal.OAuthTokenProvider
    oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
    token, err = adal.NewServicePrincipalToken(
        *oauthConfig,
        clientID,
        clientSecret,
        activeDirectoryResourceID)
    return token, err
}

func main() {
    token, _ := CreateToken()
    vnetClient := network.NewVirtualNetworksClientWithBaseURI(baseURI, subscriptionID)
    vnetClient.Authorizer = autorest.NewBearerAuthorizer(token)
    future, _ := vnetClient.CreateOrUpdate(
        context.Background(),
        resourceGroupName,
        "sampleVnetName",
        network.VirtualNetwork{
            Location: to.StringPtr("local"),
            VirtualNetworkPropertiesFormat: &network.VirtualNetworkPropertiesFormat{
                AddressSpace: &network.AddressSpace{
                    AddressPrefixes: &[]string{"10.0.0.0/8"},
                },
                Subnets: &[]network.Subnet{
                    {
                        Name: to.StringPtr("subnetName"),
                        SubnetPropertiesFormat: &network.SubnetPropertiesFormat{
                            AddressPrefix: to.StringPtr("10.0.0.0/16"),
                        },
                    },
                },
            },
        })
    err := future.WaitForCompletion(context.Background(), vnetClient.Client)
    if err != nil {
        fmt.Printf(err.Error())
        return
    }
}

````

## <a name="next-steps"></a>Kolejne kroki
* [Install PowerShell for Azure Stack](azure-stack-powershell-install.md) (Instalowanie programu PowerShell dla usługi Azure Stack)
* [Konfigurowanie środowiska PowerShell użytkownika Azure stosu](azure-stack-powershell-configure-user.md)  
