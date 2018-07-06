---
title: Za pomocą profilami wersji interfejsu API za pomocą języka Ruby w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o korzystaniu z profilami wersji interfejsu API za pomocą języka Ruby w usłudze Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: B82E4979-FB78-4522-B9A1-84222D4F854B
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: 4d62c192b4e74980fc8cd8a671a702ba2ddfdbcb
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/06/2018
ms.locfileid: "37866597"
---
# <a name="use-api-version-profiles-with-ruby-in-azure-stack"></a>Profilami wersji interfejsu API za pomocą języka Ruby w usłudze Azure Stack

*Dotyczy: Usługa Azure Stack zintegrowane systemy i usługi Azure Stack Development Kit*

## <a name="ruby-and-api-version-profiles"></a>Profilami wersji interfejsu API i Ruby

Ruby SDK dla usługi Azure Stack Resource Manager oferuje narzędzia ułatwiające tworzenie i zarządzanie infrastrukturą. Dostawcy zasobów w zestawie SDK obejmują obliczeniowych, sieci wirtualnych i magazynu przy użyciu języka Ruby. Profile interfejsu API w zestawie SDK dla języka Ruby Włącz projektowania aplikacji w chmurze hybrydowej, która ułatwia przełączanie między zasobami w usłudze Azure Stack i globalnych zasobów platformy Azure.

Profil interfejsu API jest kombinacją dostawcy zasobów i ich wersji usługi. Łączenie różnych typów zasobów, można użyć profilu usługi interfejsu API.

 - Aby korzystać z najnowszej wersji wszystkich usług, użyj **najnowsze** profilu gem zbiorczego zestawu Azure SDK.
 - Aby korzystać z usług, które są zgodne z usługą Azure Stack, należy użyć **V2017_03_09** profilu gem zbiorczego zestawu Azure SDK.
 - Aby korzystać z najnowszej wersji interfejsu api usługi, użyj **najnowsze** profilu określonego rozwiązania gem. Na przykład, jeśli chcesz używać najnowszej wersji interfejsu api usług obliczeniowych samodzielnie, użyj **najnowsze** profil **obliczenia** gem.
 - Aby użyć określonej wersji interfejsu api dla usługi, należy użyć określonej wersji interfejsu API, zdefiniowane wewnątrz gem.

> [!Note]   
> Możesz połączyć wszystkie opcje dostępne w tej samej aplikacji.

## <a name="install-the-azure-ruby-sdk"></a>Zainstaluj zestaw Azure Ruby SDK

 - Postępuj zgodnie z instrukcjami oficjalne zainstalował [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
 - Postępuj zgodnie z instrukcjami oficjalne zainstalował [Ruby](https://www.ruby-lang.org/en/documentation/installation/).
    - Podczas instalowania wybrać **Dodawanie języka Ruby do zmiennej PATH**
    - Podczas instalacji języka Ruby, po wyświetleniu monitu, należy zainstalować zestaw deweloperski.
    - Następnie zainstaluj program instalujący niezamówione pakiety przy użyciu następującego polecenia:  
      `Gem install bundler`
 - Jeśli nie jest dostępny, Utwórz subskrypcję i Zapisz identyfikator subskrypcji ma być używany w dalszej części. Instrukcje, aby utworzyć subskrypcję [tutaj](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm). 
 - Tworzenie jednostki usługi, a następnie zapisz jego identyfikator i klucz tajny. Instrukcje dotyczące tworzenia jednostki usługi dla usługi Azure Stack to [tutaj](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals). 
 - Upewnij się, że jednostki usługi rola właściciel/Współautor subskrypcji. Instrukcje dotyczące sposobu przypisywania roli do jednostki usługi są [tutaj](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals).

## <a name="install-the-rubygem-packages"></a>Instalowanie pakietów rubygem

Można bezpośrednio zainstalować pakiety rubygem platformy azure.

````Ruby  
gem install azure_mgmt_compute
gem install azure_mgmt_storage
gem install azure_mgmt_resources
gem install azure_mgmt_network
Or use them in your Gemfile.
gem 'azure_mgmt_storage'
gem 'azure_mgmt_compute'
gem 'azure_mgmt_resources'
gem 'azure_mgmt_network'
````

Należy pamiętać, zestaw SDK usługi Azure Resource Manager języka Ruby jest w wersji zapoznawczej i prawdopodobnie będzie mieć istotne zmiany w interfejsie w przyszłych wydaniach. Zwiększonej ilości w wersji pomocniczej może wskazywać istotne zmiany.

## <a name="usage-of-the-azuresdk-gem"></a>Użycie rozwiązania gem azure_sdk

Rozwiązania gem, azure_sdk, stanowi zbiór wszystkich obsługiwanych Klejnoty w zestawie SDK dla języka Ruby. Obejmuje to rozwiązania gem **najnowsze** profil, który obsługuje najnowszą wersję wszystkich usług. Podaj profil numerów wersji **V2017_03_09** profil, który zaprojektowano pod kątem usługi Azure Stack.

Azure_sdk gem zbiorczego można zainstalować za pomocą następującego polecenia:  

````Ruby  
  gem install 'azure_sdk
````

## <a name="prerequisite"></a>Wymagania wstępne

Aby można było używać zestawu Azure SDK dla języka Ruby przy użyciu usługi Azure Stack, należy podać następujące wartości, a następnie ustaw wartości zmiennych środowiskowych. Zapoznaj się z instrukcjami pod tabelą systemu operacyjnego na temat ustawiania zmiennych środowiskowych. 

| Wartość | Zmienne środowiskowe | Opis | 
| --- | --- | --- | --- |
| Identyfikator dzierżawy | AZURE_TENANT_ID | Zalety usługi Azure Stack [identyfikator dzierżawy](https://docs.microsoft.com/azure/azure-stack/azure-stack-identity-overview). |
| Identyfikator klienta | AZURE_CLIENT_ID | Usługa identyfikator podmiotu zabezpieczeń aplikacji zapisywał informacje o nazwę główną usługi został utworzony w poprzedniej sekcji niniejszego dokumentu.  |
| Identyfikator subskrypcji | AZURE_SUBSCRIPTION_ID | [Identyfikator subskrypcji](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview#subscriptions) jest sposób uzyskiwania dostępu do oferty w usłudze Azure Stack. |
| Wpis tajny klienta | AZURE_CLIENT_SECRET | Klucz tajny aplikacji nazwy głównej usługi zapisane podczas tworzenia nazwy głównej usługi. |
| Punkt końcowy usługi Resource Manager | ARM_ENDPOINT | Zobacz [endpoin Menedżera zasobów usługi Azure Stack](#The-azure-stack-resource-manager-endpoint).  |

### <a name="the-azure-stack-resource-manager-endpoint"></a>Punktu końcowego Menedżera zasobów usługi Azure Stack

Menedżer zasobów platformy Azure to platforma zarządzania, która umożliwia administratorom wdrażanie, zarządzanie i monitorowanie zasobów platformy Azure. Usługa Azure Resource Manager może obsługiwać te zadania jako grupę, a nie indywidualnie, w ramach jednej operacji.

Możesz uzyskać informacje o metadanych z punktu końcowego usługi Resource Manager. Punkt końcowy zwraca plik w formacie JSON z informacjami wymaganymi do uruchomienia kodu.

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

### <a name="set-environmental-variables"></a>Ustawianie zmiennych środowiskowych

**Program Microsoft Windows**  
Aby ustawić zmienne środowiskowe w wierszu polecenia Windows, użyj następującego formatu:  
`set AZURE_TENANT_ID=<YOUR_TENANT_ID>`

**System macOS, Linux i komputerach z systemem Unix**  
W systemach Unix, na podstawie można użyć polecenia takie jak:  
`export AZURE_TENANT_ID=<YOUR_TENANT_ID>`

## <a name="existing-api-profiles"></a>Istniejące profile interfejsu API

Gem zbiorczy azure_sdk ma następujące dwa profile:

1. **V2017_03_09**  
  Profil stworzona z myślą o usłudze Azure Stack. Użyj tego profilu usługi ma być najbardziej zgodna z usługą Azure Stack.
2. **Najnowsze**  
  Profil zawiera najnowsze wersje wszystkich usług. Za pomocą najnowszej wersji wszystkich usług.

Aby uzyskać więcej informacji na temat profilów Azure Stack i interfejsu API, zobacz [profilami podsumowanie interfejsu API](azure-stack-version-profiles.md#summary-of-api-profiles).

## <a name="azure-ruby-sdk-api-profile-usage"></a>Użycie profilów usługi platformy Azure interfejs API zestawu SDK języka Ruby

Następujące wiersze powinny służyć do tworzenia wystąpienia klienta profilu. Ten parametr jest tylko wymagane dla usługi Azure Stack lub innych chmur prywatnych. Global Azure jest już te ustawienia domyślne.

````Ruby  
active_directory_settings = get_active_directory_settings(ENV['ARM_ENDPOINT'])

provider = MsRestAzure::ApplicationTokenProvider.new(
    ENV['AZURE_TENANT_ID'],
    ENV['AZURE_CLIENT_ID'],
    ENV['AZURE_CLIENT_SECRET'],
    active_directory_settings
)
credentials = MsRest::TokenCredentials.new(provider)
options = {
    credentials: credentials,
    subscription_id: subscription_id,
    active_directory_settings: active_directory_settings,
    base_url: ENV['ARM_ENDPOINT']
}

# Target profile built for Azure Stack
client = Azure::Resources::Profiles::V2017_03_09::Mgmt::Client.new(options)
````

Profil klienta może służyć do dostępu do dostawców poszczególnych zasobów, takich jak obliczenia, Magazyn i sieć.

````Ruby  
# To access the operations associated with Compute
profile_client.compute.virtual_machines.get 'RESOURCE_GROUP_NAME', 'VIRTUAL_MACHINE_NAME'

# Option 1: To access the models associated with Compute
purchase_plan_obj = profile_client.compute.model_classes.purchase_plan.new

# Option 2: To access the models associated with Compute
# Notice Namespace: Azure::Profiles::<Profile Name>::<Service Name>::Mgmt::Models::<Model Name>
purchase_plan_obj = Azure::Profiles::V2017_03_09::Compute::Mgmt::Models::PurchasePlan.new
````

## <a name="define-azurestack-environment-setting-functions"></a>Zdefiniuj AzureStack środowiska ustawienia funkcji

Na potrzeby uwierzytelniania jednostki usługi do środowiska usługi Azure Stack, zdefiniuj punktów końcowych przy użyciu **get_active_directory_settings()**. Ta metoda używa **ARM_Endpoint** zmiennej środowiskowej, który został ustawiony podczas ustanawiania zmiennych środowiskowych.

````Ruby  
# Get Authentication endpoints using Arm Metadata Endpoints
def get_active_directory_settings(armEndpoint)
    settings = MsRestAzure::ActiveDirectoryServiceSettings.new
    response = Net::HTTP.get_response(URI("#{armEndpoint}/metadata/endpoints?api-version=1.0"))
    status_code = response.code
    response_content = response.body
    unless status_code == "200"
        error_model = JSON.load(response_content)
        fail MsRestAzure::AzureOperationError.new("Getting Azure Stack Metadata Endpoints", response, error_model)
    end
    result = JSON.load(response_content)
    settings.authentication_endpoint = result['authentication']['loginEndpoint'] unless result['authentication']['loginEndpoint'].nil?
    settings.token_audience = result['authentication']['audiences'][0] unless result['authentication']['audiences'][0].nil?
    settings
end
````

## <a name="samples-using-api-profiles"></a>Przykłady korzystania z profilów interfejsu API

Można użyć poniższych przykładów, znaleziono w usłudze GitHub repositoreis dotyczącym tworzenia rozwiązań przy użyciu profilów Ruby i interfejsu API usługi Azure Stack:

 - [Zarządzanie zasobami i grupami zasobów platformy Azure przy użyciu języka Ruby](https://github.com/Azure-Samples/resource-manager-ruby-resources-and-groups/tree/master/Hybrid)
 - [Zarządzanie maszynami wirtualnymi za pomocą języka Ruby](https://github.com/Azure-Samples/compute-ruby-manage-vm/tree/master/Hybrid)
 - [Wdrażanie protokołu SSH maszyny Wirtualnej przy użyciu szablonu w języku Ruby z obsługą](https://github.com/Azure-Samples/resource-manager-ruby-template-deployment/tree/master/Hybrid)

### <a name="sample-resource-manager-and-groups"></a>Przykładowe Resource Manager i grupy

Do uruchomienia przykładu, upewnij się, że zainstalowano język Ruby. Jeśli używasz programu Visual Studio Code, należy pobrać zestaw SDK języka Ruby, jako rozszerzenie także. 

> [!Note]  
> Możesz uzyskać repozytorium na potrzeby przykładu u "[zarządzanie zasobami platformy Azure i grup zasobów za pomocą języka Ruby](https://github.com/Azure-Samples/resource-manager-ruby-resources-and-groups/tree/master/Hybrid)".

1. Sklonuj repozytorium.

    ````Bash
    git clone https://github.com/Azure-Samples/resource-manager-ruby-resources-and-groups.git
    ````

2. Zainstalowanie zależności za pomocą pakietu.

    ````Bash
    cd resource-manager-ruby-resources-and-groups\Hybrid\
    bundle install
    ````

3. Tworzenie jednostki usługi platformy Azure przy użyciu programu PowerShell i pobieranie wartości wymagane. 

  Aby uzyskać instrukcje dotyczące tworzenia jednostki usługi, zobacz [użyciu programu Azure PowerShell utworzyć nazwę główną usługi za pomocą certyfikatu](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals).

  Wartości wymagane są następujące:
  - Identyfikator dzierżawy
  - Identyfikator klienta
  - Wpis tajny klienta
  - Identyfikator subskrypcji
  - Punkt końcowy usługi Resource Manager

  Ustaw następujące zmienne środowiskowe, korzystając z informacji pobrane z nazwy głównej usługi został utworzony.

  - Eksportuj AZURE_TENANT_ID = {swój identyfikator dzierżawy}
  - Eksportuj AZURE_CLIENT_ID = {identyfikatora klienta}
  - Eksportuj AZURE_CLIENT_SECRET = {klucz tajny klienta}
  - Eksportuj AZURE_SUBSCRIPTION_ID = {identyfikator subskrypcji}
  - Eksportuj ARM_ENDPOINT = {adres url Menedżera zasobów AzureStack}

  > [!Note]  
  > W Windows należy użyć zestawu, zamiast eksportowania.

4. Upewnij się, że ustawiono zmienną lokalizacji do lokalizacji AzureStack. Na przykład lokalnego = "local"

5. Dodać następujący wiersz kodu, jeśli używasz usługi Azure Stack lub innych chmur prywatnych docelowych punktów końcowych bezpośrednio active directory.

  ````Ruby  
  active_directory_settings = get_active_directory_settings(ENV['ARM_ENDPOINT'])
  ````

6. Wewnątrz zmiennej opcji należy dodać ustawienia usługi active directory i podstawowy adres URL do pracy z usługą Azure Stack. 

  ````Ruby  
  options = {
    credentials: credentials,
    subscription_id: subscription_id,
    active_directory_settings: active_directory_settings,
    base_url: ENV['ARM_ENDPOINT']
  }
  ````

7. Utwórz profil klienta, który jest przeznaczony dla profilu usługi Azure Stack:

  ````Ruby  
    client = Azure::Resources::Profiles::V2017_03_09::Mgmt::Client.new(options)
  ````

8. Na potrzeby uwierzytelniania jednostki usługi z usługą Azure Stack, należy zdefiniować punktów końcowych przy użyciu **get_active_directory_settings()**. Ta metoda używa **ARM_Endpoint** zmiennej środowiskowej, który został ustawiony podczas ustanawiania zmiennych środowiskowych.

  ````Ruby  
  def get_active_directory_settings(armEndpoint)
    settings = MsRestAzure::ActiveDirectoryServiceSettings.new
    response = Net::HTTP.get_response(URI("#{armEndpoint}/metadata/endpoints?api-version=1.0"))
    status_code = response.code
    response_content = response.body
    unless status_code == "200"
      error_model = JSON.load(response_content)
      fail MsRestAzure::AzureOperationError.new("Getting Azure Stack Metadata Endpoints", response, error_model)
    end
    result = JSON.load(response_content)
    settings.authentication_endpoint = result['authentication']['loginEndpoint'] unless result['authentication']['loginEndpoint'].nil?
    settings.token_audience = result['authentication']['audiences'][0] unless result['authentication']['audiences'][0].nil?
    settings
  end
  ````

9. Uruchom przykład.

  ````Ruby
    bundle exec ruby example.rb
  ````

## 

## <a name="next-steps"></a>Kolejne kroki

* [Install PowerShell for Azure Stack](azure-stack-powershell-install.md) (Instalowanie programu PowerShell dla usługi Azure Stack)
* [Konfigurowanie środowiska PowerShell użytkownika usługi Azure Stack](azure-stack-powershell-configure-user.md)  
