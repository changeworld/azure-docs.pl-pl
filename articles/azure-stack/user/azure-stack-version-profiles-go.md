---
title: Za pomocą profilami wersji interfejsu API za pomocą języka GO w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o korzystaniu z profilami wersji interfejsu API za pomocą języka GO w usłudze Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: sijuman
ms.openlocfilehash: 30969dcb7549f4107eb72b54d444a639c35b7ba0
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2019
ms.locfileid: "54264786"
---
# <a name="use-api-version-profiles-with-go-in-azure-stack"></a>Profilami wersji interfejsu API za pomocą języka Go w usłudze Azure Stack

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

## <a name="go-and-version-profiles"></a>Profile z rzeczywistym użyciem i wersji

Profil jest kombinacją różnych typów zasobów z różnymi wersjami z różnymi usługami. Przy użyciu profilu pomoże Ci mieszać i dopasowywać między różnych typów zasobów. Profile zapewniają następujące korzyści:

 - Stabilność aplikacji przez blokowanie do konkretnych wersji interfejsu API.
 - Zgodność aplikacji za pomocą usługi Azure Stack i regionalnych centrach danych platformy Azure.

W zestawie SDK Przejdź profile są dostępne w obszarze Profile / ścieżki mają wersję w **RRRR-MM-DD** formatu. Od razu, najnowsze usługi Azure Stack jest wersja profilu **2017-03-09**. Aby zaimportować profil dla danej usługi, należy zaimportować jego odpowiedniego modułu z profilu. Na przykład, aby zaimportować **obliczenia** usługi z **2017-03-09** profilu:

````go
import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/compute/mgmt/compute" 
````

## <a name="install-azure-sdk-for-go"></a>Zainstaluj zestaw Azure SDK dla języka Go

  1. Zainstaluj oprogramowanie Git. Aby uzyskać instrukcje, zobacz [wprowadzenie — Instalowanie usługi Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
  2. Zainstaluj [Przejdź język programowania](https://golang.org/dl).  
  Profile interfejsu API dla platformy Azure będzie wymagać Go w wersji 1.9 lub nowsza.
  3. Zainstaluj zestaw Azure SDK Go i jego zależności, uruchamiając poniższe polecenie powłoki bash:
  ```
    go get -u -d github.com/Azure/azure-sdk-for-go/...
  ```

### <a name="the-go-sdk"></a>Zestaw GO SDK

Można znaleźć więcej informacji na temat Przejdź zestawu SDK usługi Azure na:
- Azure Przejdź SDK [Instalowanie zestawu Azure SDK dla języka Go](https://docs.microsoft.com/go/azure/azure-sdk-go-install).
- Przejdź zestawu SDK usługi Azure jest ogólnie dostępna w witrynie GitHub pod [azure sdk dla go](https://github.com/Azure/azure-sdk-for-go).

### <a name="go-autorest-dependencies"></a>Zależności AutoRest z rzeczywistym użyciem

Zestaw SDK języka GO, zależy od modułów platformy Azure z rzeczywistym użyciem — AutoRest wysyłać żądania REST z punktami końcowymi usługi Azure Resource Manager. Musisz zaimportować zależności modułów platformy Azure z rzeczywistym użyciem — AutoRest z [platformy Azure z rzeczywistym użyciem — AutoRest w serwisie GitHub](https://github.com/Azure/go-autorest). Możesz znaleźć polecenia powłoki bash instalacji w **zainstalować** sekcji.

## <a name="how-to-use-go-sdk-profiles-on-azure-stack"></a>Jak używać profilów zestaw SDK języka GO w usłudze Azure Stack

Aby uruchomić próbkę kodu języka Go w usłudze Azure Stack:
  1. Zainstaluj zestaw Azure SDK dla języka Go i jego zależności. Aby uzyskać instrukcje, zobacz poprzednią sekcję, [Zainstaluj zestaw Azure SDK dla języka Go](#install-azure-sdk-for-go).
  2. Uzyskaj informacje o metadanych z punktu końcowego usługi Resource Manager. Punkt końcowy zwraca plik w formacie JSON za pomocą informacji wymaganych do uruchomienia kodu języka Go.

  > [!Note]  
  > **ResourceManagerUrl** jest w usłudze Azure Stack Development Kit (ASDK): `https://management.local.azurestack.external/`  
  > **ResourceManagerUrl** w systemach zintegrowanych jest: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`  
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

  3. Jeśli nie jest dostępny, Utwórz subskrypcję i Zapisz identyfikator subskrypcji ma być używany w dalszej części. Aby uzyskać informacje na temat tworzenia subskrypcji, zobacz [Tworzenie subskrypcji ofert w usłudze Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm). 
  4. Tworzenie jednostki usługi z zakresem "Subskrypcja" i **właściciela** roli. Zapisz jednostki usługi, identyfikator i klucz tajny. Aby uzyskać informacje na temat tworzenia jednostki usługi dla usługi Azure Stack, zobacz [Tworzenie jednostki usługi](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#create-service-principal-for-azure-ad). Konfigurowanie środowiska usługi Azure Stack.
  5. Zaimportuj moduł usługi z Przejdź profilu zestawu SDK w kodzie. Bieżąca wersja profilu usługi Azure Stack to **2017-03-09**. Na przykład w celu zaimportowania modułu sieciowego na podstawie **2017-03-09** typ profilu: 

  ````go
    package main 
    import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
  ````
  
  6. W funkcji, należy utworzyć i uwierzytelnić klienta z **New** wywołanie funkcji klienta. Można utworzyć klienta sieci wirtualnej, można użyć następującego kodu:  

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

  7. Wywoływanie metod interfejsu API przy użyciu klienta, który został utworzony w poprzednim kroku. Na przykład, aby utworzyć sieć wirtualną przy użyciu naszego klienta z poprzedniego kroku: 
  
````go
package main

import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network" 
func main() { 
  vnetClient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
  vnetClient .Authorizer = autorest.NewBearerAuthorizer(token)

  vnetClient .CreateOrUpdate( ) 
````
  
  Aby uzyskać kompletny przykład tworzenia sieci wirtualnej w usłudze Azure Stack przy użyciu profilu zestaw SDK języka Go, zobacz [przykład](#example).

## <a name="authentication"></a>Authentication

Można pobrać właściwości Autoryzatora z usługi Azure Active Directory przy użyciu zestaw SDK języka Go, należy zainstalować moduły AutoRest z rzeczywistym użyciem. Tych modułów powinny zostały już zainstalowane z instalacją "Zestaw SDK języka Go"; Jeśli nie, zainstaluj [pakietów uwierzytelniania w usłudze GitHub](https://github.com/Azure/go-autorest/tree/master/autorest/adal).

Obiekt autoryzujący musi być ustawiona jako obiekt autoryzujący dla klienta zasobów. Dostępnych jest kilka metod, aby uzyskać obiekt autoryzujący; Aby uzyskać pełną listę, zobacz tutaj.

W tej sekcji przedstawiono typową metodą uzyskiwać tokeny autoryzujący w usłudze Azure Stack przy użyciu poświadczeń klienta:

  1. Jeśli jednostka usługi przy użyciu roli właściciela subskrypcji jest dostępny, Pomiń ten krok. W przeciwnym razie utwórz jednostkę usługi [instrukcje]( https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals) i przypisać ją w roli "właściciel" ograniczone do subskrypcji [instrukcje]( https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#assign-role-to-service-principal). Zapisz identyfikator aplikacji nazwy głównej usługi i klucza tajnego. 

  2. Importuj **adal** pakiet z Go AutoRest w kodzie. 
  
  ````go
  package main
  import "github.com/Azure/go-autorest/autorest/adal" 
  ````

  3. Tworzenie oauthConfig przy użyciu metody NewOAuthConfig z **adal** modułu. 
  
  ````go
  package main 

  import "github.com/Azure/go-autorest/autorest/ada1" 

  func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
  ````
   
  Ustaw `<activeDirectoryEndpoint>` na wartość "loginEndpoint" właściwości z metadanych ResourceManagerUrl odtworzona w poprzedniej sekcji niniejszego dokumentu.
  Ustaw `<tenantID>` wartość identyfikatora dzierżawy usługi Azure Stack. 

  4. Na koniec Utwórz token jednostki usługi przy użyciu metody NewServicePrincipalToken z modułu biblioteki adal. 

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
  
  Ustaw `<activeDirectoryResourceID>` na jedną z wartości w "audience" pobrać listy z metadanych ResourceManagerUrl w poprzedniej sekcji niniejszego dokumentu.  
  Ustaw `<clientID>` identyfikator aplikacji nazwy głównej usługi zapisany podczas tworzenia nazwy głównej usługi w poprzedniej sekcji niniejszego dokumentu.  
  Ustaw `<clientSecret>` do aplikacji nazwy głównej usługi klucz tajny zapisywana, gdy nazwa główna usługi została utworzona w poprzedniej sekcji niniejszego dokumentu.  

## <a name="example"></a>Przykład

W tej sekcji przedstawiono przykładowy kod języka Go, aby utworzyć sieć wirtualną w usłudze Azure Stack. Kompletne przykłady Go SDK można znaleźć [repozytorium przykładów usługi Azure zestaw SDk dla języka Go](https://github.com/Azure-Samples/azure-sdk-for-go-samples). Przykłady usługi Azure Stack są dostępne w ramach hybrydowego / ścieżce wewnątrz usługi folderów w repozytorium.

> [!Note]  
> Aby uruchomić kod, w tym przykładzie, należy sprawdzić, czy subskrypcja używana jest **sieci** dostawcy zasobów wymieniony jako **zarejestrowanej**. W celu zweryfikowania, Szukaj w ramach subskrypcji w portalu usługi Azure Stack, a następnie kliknij polecenie **dostawców zasobów.**

1. Zaimportuj wymagane pakiety w kodzie. Aby zaimportować moduł sieci, należy używać najnowszej profilu w usłudze Azure Stack. 
  
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

2. Zdefiniuj zmienne środowiskowe. Aby utworzyć sieć wirtualną, musi być grupą zasobów. 

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

3. Skoro zdefiniowano zmienne środowiskowe, Dodaj metodę, aby utworzyć token uwierzytelniania za pomocą **adal** pakietu. Szczegółowe informacje o uwierzytelnianiu w poprzedniej sekcji.
  
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

4. Dodaj metodę głównego. Metoda główna najpierw pobiera token przy użyciu metody, która jest zdefiniowana w poprzednim kroku. Następnie tworzy klienta przy użyciu modułu sieciowego na podstawie profilu. Na koniec tworzy sieć wirtualną. 
  
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
* [Konfigurowanie środowiska PowerShell użytkownika usługi Azure Stack](azure-stack-powershell-configure-user.md)  
