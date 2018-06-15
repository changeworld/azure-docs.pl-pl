---
title: Przy użyciu profilów wersji interfejsu API z Ruby w stosie Azure | Dokumentacja firmy Microsoft
description: Informacje o używaniu profile w wersji interfejsu API z Ruby w stosie Azure.
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
ms.openlocfilehash: dd8130ac12f9c7c2095f9329dc4ce8a34187cf62
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
ms.locfileid: "34011218"
---
# <a name="use-api-version-profiles-with-ruby-in-azure-stack"></a>Profile w wersji interfejsu API za pomocą Ruby w stosie Azure

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

## <a name="ruby-and-api-version-profiles"></a>Profile w wersji Ruby i interfejsu API

Ruby zestawu SDK dla usługi Azure stosu Resource Manager udostępnia narzędzia umożliwiające tworzenie i zarządzanie infrastrukturą. Dostawcy zasobów w zestawie SDK obejmują obliczeniowych, sieci wirtualnych i magazynu w języku Ruby. Profile interfejsu API w zestawie SDK Ruby włączyć programowanie chmura hybrydowa pomaga przełączać się między globalne zasobów platformy Azure i zasoby na stosie Azure.

Profil interfejsu API jest kombinacją dostawców zasobów i wersji usługi. Profil interfejsu API umożliwia łączenie różnych typów zasobów.

 - Aby korzystać z najnowszej wersji wszystkich usług **najnowsze** profilu gem zbiorczego zestawu Azure SDK.
 - Aby użyć usługi zgodne z stosu Azure, użyj **V2017_03_09** profilu gem zbiorczego zestawu Azure SDK.
 - Aby korzystać z najnowszą wersją interfejsu api usługi, użyj **najnowsze** profilu określonego gem. Na przykład, jeśli chcesz używać najnowszej wersji interfejsu api usługi obliczeniowe tylko, użyj **najnowsze** profilu **obliczeniowe** gem.
 - Aby użyć określonej wersji interfejsu api dla usługi, użyj określonych wersji interfejsu API, zdefiniowane wewnątrz gem.

> [!Note]   
> Możesz łączyć wszystkie opcje w tej samej aplikacji.

## <a name="install-the-azure-ruby-sdk"></a>Zainstaluj zestaw Azure SDK dopisków fonetycznych

 - Postępuj zgodnie z oficjalnego instrukcje dotyczące instalacji [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
 - Postępuj zgodnie z oficjalnego instrukcje dotyczące instalacji [Ruby](https://www.ruby-lang.org/en/documentation/installation/).
    - Podczas instalacji wybierz **Dodaj element do zmiennej ścieżki**
    - Zainstaluj zestaw deweloperów podczas instalacji dopisków fonetycznych po wyświetleniu monitu.
    - Następnie zainstaluj program instalujący niezamówione pakiety przy użyciu następującego polecenia:  
      `Gem install bundler`
 - Jeśli nie jest dostępny, Utwórz subskrypcję i Zapisz identyfikator subskrypcji do użycia później. Instrukcje dotyczące tworzenia subskrypcji są [tutaj](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm). 
 - Tworzenie nazwy głównej usługi, a następnie zapisz jego identyfikator i klucz tajny. Instrukcje dotyczące tworzenia nazwy głównej usługi dla stosu Azure są [tutaj](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals). 
 - Upewnij się, że nazwy głównej usługi ma rolę współautora/właściciela na subskrypcję. Instrukcje dotyczące sposobu przypisywania roli do nazwy głównej usługi są [tutaj](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals).

## <a name="install-the-rubygem-packages"></a>Zainstaluj pakiety rubygem

Można zainstalować te pakiety azure rubygem bezpośrednio.

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

Należy pamiętać, zestaw SDK usługi Azure Resource Manager Ruby jest w wersji zapoznawczej i prawdopodobnie będzie mieć fundamentalne zmiany interfejsu w kolejnych wersjach. Zwiększenie liczby w wersji pomocniczej może wskazywać na istotne zmiany.

## <a name="usage-of-the-azuresdk-gem"></a>Użycie azure_sdk gem

Gem, azure_sdk, to zbiór wszystkich obsługiwanych gems w zestawie SDK Ruby. Ta gem składa się z **najnowsze** profil, który obsługuje najnowszą wersję wszystkich usług. Podaj profil numerów wersji **V2017_03_09** profilu jest skompilowany dla platformy Azure stosu.

Gem zbiorczy azure_sdk można zainstalować przy użyciu następującego polecenia:  

````Ruby  
  gem install 'azure_sdk
````

## <a name="prerequisite"></a>Wymagania wstępne

Aby użyć zestawu SDK usługi Azure Ruby stosu Azure, należy podać następujące wartości, a następnie ustaw wartości zmiennych środowiskowych. Zapoznaj się z instrukcjami pod tabelą systemu operacyjnego na temat ustawiania zmiennych środowiskowych. 

| Wartość | Zmienne środowiskowe | Opis | 
| --- | --- | --- | --- |
| Identyfikator dzierżawy | AZURE_TENANT_ID | Wartość stosu Azure [Identyfikatorem dzierżawy](https://docs.microsoft.com/azure/azure-stack/azure-stack-identity-overview). |
| Identyfikator klienta | AZURE_CLIENT_ID | Usługa identyfikator podmiotu zabezpieczeń aplikacji zapisane podczas nazwy głównej usługi został utworzony w poprzedniej sekcji tego dokumentu.  |
| Identyfikator subskrypcji | AZURE_SUBSCRIPTION_ID | [Identyfikator subskrypcji](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview#subscriptions) jest dostęp do oferty Azure stosu. |
| Wpis tajny klienta | AZURE_CLIENT_SECRET | Klucz tajny aplikacji głównej usługi zapisane podczas tworzenia nazwy głównej usługi. |
| Punkt końcowy Menedżera zasobów | ARM_ENDPOINT | Zobacz [stosu Azure resource manager endpoin](#The-azure-stack-resource-manager-endpoint).  |

### <a name="the-azure-stack-resource-manager-endpoint"></a>Punkt końcowy Menedżera zasobów Azure stosu

Microsoft Azure Resource Manager to platforma zarządzania, która umożliwia administratorom wdrażanie, zarządzanie i monitorowanie zasobów platformy Azure. Usługa Azure Resource Manager może obsługiwać te zadania w grupie, a nie pojedynczo, w ramach jednej operacji.

Z punktu końcowego usługi Resource Manager można uzyskać informacji o metadanych. Punkt końcowy zwraca plik JSON o informacje wymagane do uruchomienia kodu.

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

### <a name="set-environmental-variables"></a>Ustaw zmienne środowiskowe

**Microsoft Windows**  
Aby ustawić zmienne środowiskowe w wierszu polecenia systemu Windows, użyj następującego formatu:  
`set AZURE_TENANT_ID=<YOUR_TENANT_ID>`

**System macOS, Linux i systemy oparte na systemie Unix**  
W systemach Unix, na podstawie można użyć polecenia takie jak:  
`export AZURE_TENANT_ID=<YOUR_TENANT_ID>`

## <a name="existing-api-profiles"></a>Istniejące profile interfejsu API

Gem zbiorczy azure_sdk ma następujące dwa profile:

1. **V2017_03_09**  
  Profil skompilowany dla platformy Azure stosu. Za pomocą tego profilu usługi do najbardziej zgodna ze stosu Azure.
2. **najnowsze**  
  Profil zawiera najnowsze wersje wszystkich usług. Za pomocą najnowszej wersji wszystkich usług.

Aby uzyskać więcej informacji o profilach stosu Azure i interfejsu API, zobacz [profile podsumowanie interfejsu API](azure-stack-version-profiles.md#summary-of-api-profiles).

## <a name="azure-ruby-sdk-api-profile-usage"></a>Użycie Ruby interfejsu API zestawu SDK platformy Azure w profilu

Następujące wiersze należy używać do tworzenia wystąpienia klienta profilu. Ten parametr jest tylko wymagane przez stos Azure lub innych chmur prywatnych. Globalne Azure ma już te ustawienia domyślne.

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

Profil klienta można uzyskać dostępu do dostawcy pojedynczego zasobu, na przykład obliczeniowych, magazynu i sieci.

````Ruby  
# To access the operations associated with Compute
profile_client.compute.virtual_machines.get 'RESOURCE_GROUP_NAME', 'VIRTUAL_MACHINE_NAME'

# Option 1: To access the models associated with Compute
purchase_plan_obj = profile_client.compute.model_classes.purchase_plan.new

# Option 2: To access the models associated with Compute
# Notice Namespace: Azure::Profiles::<Profile Name>::<Service Name>::Mgmt::Models::<Model Name>
purchase_plan_obj = Azure::Profiles::V2017_03_09::Compute::Mgmt::Models::PurchasePlan.new
````

## <a name="define-azurestack-environment-setting-functions"></a>Zdefiniuj funkcje ustawienie środowiska AzureStack

W celu uwierzytelnienia nazwy głównej usługi do środowiska Azure stosu zdefiniować punkty końcowe przy użyciu **get_active_directory_settings()**. Ta metoda używa **ARM_Endpoint** zmiennej środowiskowej, która jest ustawiany podczas ustanawiania zmiennych środowiskowych.

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

Można użyć w GitHub repositoreis znaleziono następujące przykłady jako odwołanie tworzenie rozwiązań z profilami Ruby i interfejsu API Azure stosu:

 - [Zarządzanie zasobami i grupami zasobów platformy Azure przy użyciu języka Ruby](https://github.com/Azure-Samples/resource-manager-ruby-resources-and-groups/tree/master/Hybrid)
 - [Zarządzanie maszynami wirtualnymi przy użyciu Ruby](https://github.com/Azure-Samples/compute-ruby-manage-vm/tree/master/Hybrid)
 - [Wdrażanie SSH włączone maszyny Wirtualnej z szablonu w języku Ruby](https://github.com/Azure-Samples/resource-manager-ruby-template-deployment/tree/master/Hybrid)

### <a name="sample-resource-manager-and-groups"></a>Przykładowe Resource Manager i grupy

Aby uruchomić przykład, upewnij się, że zainstalowano Ruby. Jeśli używasz programu Visual Studio Code, Pobierz zestaw SDK Ruby, jak również rozszerzenie. 

> [!Note]  
> Możesz też uzyskać repozytorium próbki w "[zasobów zarządzania Azure i grup zasobów z Ruby](https://github.com/Azure-Samples/resource-manager-ruby-resources-and-groups/tree/master/Hybrid)".

1. Klonowanie repozytorium.

    ````Bash
    git clone https://github.com/Azure-Samples/resource-manager-ruby-resources-and-groups.git
    ````

2. Zainstaluj zależności przy użyciu pakietu.

    ````Bash
    cd resource-manager-ruby-resources-and-groups\Hybrid\
    bundle install
    ````

3. Tworzenie nazwy głównej usługi Azure przy użyciu programu PowerShell i pobrać wymagane wartości. 

  Aby uzyskać instrukcje dotyczące tworzenia nazwy głównej usługi, zobacz [użycia programu Azure PowerShell do tworzenia nazwy głównej usługi przy użyciu certyfikatu](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals).

  Wartości potrzebne są następujące:
  - Identyfikator dzierżawy
  - Identyfikator klienta
  - Wpis tajny klienta
  - Identyfikator subskrypcji
  - Punkt końcowy Menedżera zasobów

  Ustaw następujące zmienne środowiskowe, korzystając z informacji podanych pobierane z usługi podmiot zabezpieczeń został utworzony.

  - Eksportuj AZURE_TENANT_ID = {identyfikator dzierżawy}
  - Eksportuj AZURE_CLIENT_ID = {identyfikator klienta}
  - Eksportuj AZURE_CLIENT_SECRET = {klucz tajny klienta}
  - Eksportuj AZURE_SUBSCRIPTION_ID = {identyfikator subskrypcji}
  - Eksportuj ARM_ENDPOINT = {adres url Menedżera zasobów AzureStack}

  > [!Note]  
  > W systemie Windows należy użyć zestawu zamiast eksportu.

4. Upewnij się, że ustawiono zmienną lokalizacji do lokalizacji AzureStack. Na przykład lokalnego = "local"

5. Dodaj w następującym wierszu kodu, jeśli używasz stosu Azure lub innych chmur prywatnych pod kątem punktów końcowych prawo active directory.

  ````Ruby  
  active_directory_settings = get_active_directory_settings(ENV['ARM_ENDPOINT'])
  ````

6. Wewnątrz zmiennej opcji Dodaj ustawienia usługi active directory i podstawowy adres URL do pracy z stosu Azure. 

  ````Ruby  
  options = {
    credentials: credentials,
    subscription_id: subscription_id,
    active_directory_settings: active_directory_settings,
    base_url: ENV['ARM_ENDPOINT']
  }
  ````

7. Tworzenie profilu klienta, przeznaczonego dla profilu Azure stosu:

  ````Ruby  
    client = Azure::Resources::Profiles::V2017_03_09::Mgmt::Client.new(options)
  ````

8. Do uwierzytelnienia nazwy głównej usługi Azure stosu, należy zdefiniować punkty końcowe przy użyciu **get_active_directory_settings()**. Ta metoda używa **ARM_Endpoint** zmiennej środowiskowej, która jest ustawiany podczas ustanawiania zmiennych środowiskowych.

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
* [Konfigurowanie środowiska PowerShell użytkownika Azure stosu](azure-stack-powershell-configure-user.md)  
