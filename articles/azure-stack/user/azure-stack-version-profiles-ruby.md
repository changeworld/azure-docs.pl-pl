---
title: Za pomocą profilami wersji interfejsu API za pomocą języka Ruby w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o korzystaniu z profilami wersji interfejsu API za pomocą języka Ruby w usłudze Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: B82E4979-FB78-4522-B9A1-84222D4F854B
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: 226416688fe4f2c48981718e7a2f40b0ba5b0685
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58649615"
---
# <a name="use-api-version-profiles-with-ruby-in-azure-stack"></a>Profilami wersji interfejsu API za pomocą języka Ruby w usłudze Azure Stack

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

## <a name="ruby-and-api-version-profiles"></a>Profilami wersji interfejsu API i Ruby

Ruby SDK dla usługi Azure Stack Resource Manager oferuje narzędzia ułatwiające tworzenie i zarządzanie infrastrukturą. Dostawcy zasobów w zestawie SDK obejmują obliczeniowych, sieci wirtualnych i magazynu przy użyciu języka Ruby. Profile interfejsu API w zestawie SDK dla języka Ruby Włącz projektowania aplikacji w chmurze hybrydowej, która ułatwia przełączanie między zasobami w usłudze Azure Stack i globalnych zasobów platformy Azure.

Profil interfejsu API jest kombinacją dostawcy zasobów i ich wersji usługi. Łączenie różnych typów zasobów, można użyć profilu usługi interfejsu API.

- Aby korzystać z najnowszej wersji wszystkich usług, użyj **najnowsze** profilu gem zbiorczego zestawu Azure SDK.
- Aby korzystać z usług, które są zgodne z usługą Azure Stack, należy użyć **V2018_03_01** profilu gem zbiorczego zestawu Azure SDK.
- Aby użyć najnowszej wersji **parametru api-version** usługi, użyj **najnowsze** profilu określonego rozwiązania gem. Na przykład, jeśli chcesz użyć najnowszej wersji **parametru api-version** usług obliczeniowych samodzielnie, należy użyć **najnowsze** profil **obliczenia** gem.
- Aby użyć określonego **parametru api-version** dla usługi, użyj określonych wersji interfejsu API, zdefiniowane wewnątrz gem.

> [!NOTE]
> Możesz połączyć wszystkie opcje dostępne w tej samej aplikacji.

## <a name="install-the-azure-ruby-sdk"></a>Zainstaluj zestaw Azure Ruby SDK

- Postępuj zgodnie z instrukcjami oficjalne zainstalował [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
- Postępuj zgodnie z instrukcjami oficjalne zainstalował [Ruby](https://www.ruby-lang.org/en/documentation/installation/).
  - Podczas instalowania, wybrać **Dodawanie języka Ruby do zmiennej PATH**.
  - Podczas instalacji języka Ruby, po wyświetleniu monitu, należy zainstalować zestaw development kit.
  - Następnie zainstaluj program instalujący niezamówione pakiety przy użyciu następującego polecenia:  
    `Gem install bundler`
- Jeśli nie jest dostępny, Utwórz subskrypcję i Zapisz identyfikator subskrypcji ma być używany w dalszej części. Instrukcje, aby utworzyć subskrypcję [tutaj](../azure-stack-subscribe-plan-provision-vm.md).
- Tworzenie jednostki usługi, a następnie zapisz jego identyfikator i klucz tajny. Instrukcje dotyczące tworzenia jednostki usługi dla usługi Azure Stack to [tutaj](../azure-stack-create-service-principals.md).
- Upewnij się, że jednostki usługi rola właściciel/Współautor subskrypcji. Instrukcje dotyczące sposobu przypisywania roli do jednostki usługi są [tutaj](../azure-stack-create-service-principals.md).

## <a name="install-the-rubygem-packages"></a>Instalowanie pakietów Rubygem

Można bezpośrednio zainstalować pakiety Rubygem platformy Azure.

```Ruby  
gem install azure_mgmt_compute
gem install azure_mgmt_storage
gem install azure_mgmt_resources
gem install azure_mgmt_network
Or use them in your Gemfile.
gem 'azure_mgmt_storage'
gem 'azure_mgmt_compute'
gem 'azure_mgmt_resources'
gem 'azure_mgmt_network'
```

Zestaw SDK usługi Azure Resource Manager języka Ruby jest w wersji zapoznawczej i prawdopodobnie będzie mieć istotne zmiany w interfejsie w przyszłych wydaniach. Zwiększonej ilości w wersji pomocniczej może wskazywać istotne zmiany.

## <a name="use-the-azuresdk-gem"></a>Użyj rozwiązania gem azure_sdk

Rozwiązania gem, **azure_sdk**, stanowi zbiór wszystkich obsługiwanych Klejnoty w zestawie SDK dla języka Ruby. Obejmuje to rozwiązania gem **najnowsze** profil, który obsługuje najnowszą wersję wszystkich usług. Wprowadza dwa numerów wersji profilu **V2017_03_09** i **V2018_03_01** profilów, które są tworzone dla usługi Azure Stack.

Azure_sdk gem zbiorczego można zainstalować za pomocą następującego polecenia:  

```Ruby  
gem install 'azure_sdk'
```

## <a name="prerequisites"></a>Wymagania wstępne

Aby można było używać zestaw Azure Ruby SDK przy użyciu usługi Azure Stack, należy podać następujące wartości, a następnie ustaw wartości za pomocą zmiennych środowiskowych. Zapoznaj się z instrukcjami pod tabelą systemu operacyjnego na temat ustawiania zmiennych środowiskowych.

| Wartość | Zmienne środowiskowe | Opis |
| --- | --- | --- |
| Identyfikator dzierżawy | AZURE_TENANT_ID | Zalety usługi Azure Stack [identyfikator dzierżawy](https://docs.microsoft.com/azure/azure-stack/azure-stack-identity-overview). |
| Identyfikator klienta | AZURE_CLIENT_ID | Usługa identyfikator podmiotu zabezpieczeń aplikacji zapisywał informacje o nazwę główną usługi został utworzony w poprzedniej sekcji niniejszego dokumentu.  |
| Identyfikator subskrypcji | AZURE_SUBSCRIPTION_ID | [Identyfikator subskrypcji](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview#subscriptions) jest sposób uzyskiwania dostępu do oferty w usłudze Azure Stack. |
| Wpis tajny klienta | AZURE_CLIENT_SECRET | Klucz tajny aplikacji nazwy głównej usługi zapisane podczas tworzenia nazwy głównej usługi. |
| Punkt końcowy usługi Resource Manager | ARM_ENDPOINT | Zobacz [punktu końcowego Menedżera zasobów usługi Azure Stack](#the-azure-stack-resource-manager-endpoint).  |

### <a name="the-azure-stack-resource-manager-endpoint"></a>Punktu końcowego Menedżera zasobów usługi Azure Stack

Menedżer zasobów platformy Azure to platforma zarządzania, która umożliwia administratorom wdrażanie, zarządzanie i monitorowanie zasobów platformy Azure. Usługa Azure Resource Manager może obsługiwać te zadania jako grupę, a nie indywidualnie, w ramach jednej operacji.

Możesz uzyskać informacje o metadanych z punktu końcowego usługi Resource Manager. Punkt końcowy zwraca plik w formacie JSON z informacjami wymaganymi do uruchomienia kodu.

 > [!NOTE]  
 > **ResourceManagerUrl** jest w usłudze Azure Stack Development Kit (ASDK): `https://management.local.azurestack.external/` **ResourceManagerUrl** w systemach zintegrowanych jest: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`  
 > Aby pobrać metadane wymagane: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`
  
 Przykładowy plik JSON:

 ```json
 {
   "galleryEndpoint": "https://portal.local.azurestack.external:30015/",  
   "graphEndpoint": "https://graph.windows.net/",  
   "portal Endpoint": "https://portal.local.azurestack.external/",
   "authentication": {
     "loginEndpoint": "https://login.windows.net/",
     "audiences": ["https://management.<yourtenant>.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
   }
 }
```

### <a name="set-environmental-variables"></a>Ustawianie zmiennych środowiskowych

**Microsoft Windows**  
Aby ustawić zmienne środowiskowe w wierszu polecenia Windows, użyj następującego formatu:  
`set AZURE_TENANT_ID=<YOUR_TENANT_ID>`

**macOS i Linux, systemów Unix i** systemów opartych na w systemie Unix, można użyć następującego polecenia:  
`export AZURE_TENANT_ID=<YOUR_TENANT_ID>`

## <a name="existing-api-profiles"></a>Istniejące profile interfejsu API

Gem zbiorczy Azure_sdk ma następujące trzy profile:

1. **V2018_03_01** profilu stworzona z myślą o usłudze Azure Stack. Za pomocą tego profilu dla wykorzystujących najnowsze wersje usług dostępnych w usłudze Azure Stack.
2. **V2017_03_09**  
  Profil stworzona z myślą o usłudze Azure Stack. Użyj tego profilu usługi ma być najbardziej zgodna z usługą Azure Stack.
3. **najnowsze**  
  Profil zawiera najnowsze wersje wszystkich usług. Za pomocą najnowszej wersji wszystkich usług.

Aby uzyskać więcej informacji na temat profilów Azure Stack i interfejsu API, zobacz [profilami podsumowanie interfejsu API](azure-stack-version-profiles.md#summary-of-api-profiles).

## <a name="azure-ruby-sdk-api-profile-usage"></a>Użycie profilów usługi platformy Azure interfejs API zestawu SDK języka Ruby

Poniższy kod służy do tworzenia wystąpienia klienta profilu. Ten parametr jest tylko wymagane dla usługi Azure Stack lub innych chmur prywatnych. Global Azure jest już te ustawienia domyślne.

```Ruby  
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
client = Azure::Resources::Profiles::V2018_03_01::Mgmt::Client.new(options)
```

Profil klienta może służyć do dostępu do dostawców poszczególnych zasobów, takich jak wystąpienia obliczeniowe, Magazyn i sieci:

```Ruby  
# To access the operations associated with Compute
profile_client.compute.virtual_machines.get 'RESOURCE_GROUP_NAME', 'VIRTUAL_MACHINE_NAME'

# Option 1: To access the models associated with Compute
purchase_plan_obj = profile_client.compute.model_classes.purchase_plan.new

# Option 2: To access the models associated with Compute
# Notice Namespace: Azure::Profiles::<Profile Name>::<Service Name>::Mgmt::Models::<Model Name>
purchase_plan_obj = Azure::Profiles::V2018_03_01::Compute::Mgmt::Models::PurchasePlan.new
```

## <a name="define-azure-stack-environment-setting-functions"></a>Zdefiniuj funkcje ustawienie środowiska Azure Stack

Na potrzeby uwierzytelniania jednostki usługi do środowiska usługi Azure Stack, zdefiniuj punktów końcowych przy użyciu `get_active_directory_settings()`. Ta metoda używa **ARM_Endpoint** zmienną środowiskową, która jest ustawiany podczas ustanawiania zmiennych środowiska:

```Ruby  
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
```

## <a name="samples-using-api-profiles"></a>Przykłady korzystania z profilów interfejsu API

Można użyć następujących przykładów znaleźć w witrynie GitHub jako odniesienie do tworzenia rozwiązań przy użyciu profilów Ruby i interfejsu API usługi Azure Stack:

- [Zarządzanie zasobami i grupami zasobów platformy Azure przy użyciu języka Ruby](https://github.com/Azure-Samples/Hybrid-Resource-Manager-Ruby-Resources-And-Groups)
- [Zarządzanie maszynami wirtualnymi za pomocą języka Ruby](https://github.com/Azure-Samples/Hybrid-Compute-Ruby-Manage-VM)
- [Wdrażanie protokołu SSH maszyny Wirtualnej przy użyciu szablonu w języku Ruby z obsługą](https://github.com/Azure-Samples/Hybrid-Resource-Manager-Ruby-Template-Deployment)

### <a name="sample-resource-manager-and-groups"></a>Przykładowe Resource Manager i grupy

Do uruchomienia przykładu, upewnij się, że zainstalowano język Ruby. Jeśli używasz programu Visual Studio Code, Pobierz również rozszerzenie SDK dla języka Ruby.

> [!NOTE]  
> Możesz uzyskać repozytorium na potrzeby przykładu u "[zarządzanie zasobami platformy Azure i grup zasobów za pomocą języka Ruby](https://github.com/Azure-Samples/Hybrid-Resource-Manager-Ruby-Resources-And-Groups)".

1. Sklonuj repozytorium:

   ```bash
   git clone https://github.com/Azure-Samples/Hybrid-Resource-Manager-Ruby-Resources-And-Groups.git
   ```

2. Instalowanie zależności za pomocą pakietu:

   ```Bash
   cd Hybrid-Resource-Manager-Ruby-Resources-And-Groups
   bundle install
   ```

3. Tworzenie jednostki usługi przy użyciu programu PowerShell platformy Azure i pobierać wartości wymagane.

   Aby uzyskać instrukcje dotyczące tworzenia jednostki usługi, zobacz [użyciu programu Azure PowerShell utworzyć nazwę główną usługi za pomocą certyfikatu](../azure-stack-create-service-principals.md).

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

   > [!NOTE]  
   > W Windows należy użyć zestawu, zamiast eksportowania.

4. Upewnij się, że ustawiono zmienną lokalizacji do Twojej lokalizacji usługi Azure Stack. na przykład `LOCAL="local"`.

5. Dodaj następujący wiersz kodu, jeśli używasz usługi Azure Stack lub innych chmur prywatnych docelowych punktów końcowych bezpośrednio active directory:

   ```Ruby  
   active_directory_settings = get_active_directory_settings(ENV['ARM_ENDPOINT'])
   ```

6. W opcji zmiennej Dodaj ustawienia usługi Active Directory i podstawowy adres URL do pracy z usługą Azure Stack:

   ```ruby  
   options = {
   credentials: credentials,
   subscription_id: subscription_id,
   active_directory_settings: active_directory_settings,
   base_url: ENV['ARM_ENDPOINT']
   }
   ```

7. Tworzenie klienta profilu, który jest przeznaczony dla profilu usługi Azure Stack:

   ```ruby  
   client = Azure::Resources::Profiles::V2018_03_01::Mgmt::Client.new(options)
   ```

8. Na potrzeby uwierzytelniania jednostki usługi z usługą Azure Stack, należy zdefiniować punktów końcowych przy użyciu **get_active_directory_settings()**. Ta metoda używa **ARM_Endpoint** zmienną środowiskową, która jest ustawiany podczas ustanawiania zmiennych środowiska:

   ```ruby  
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
   ```

9. Uruchom przykład.

   ```ruby
   bundle exec ruby example.rb
   ```

## <a name="next-steps"></a>Kolejne kroki

- [Install PowerShell for Azure Stack](azure-stack-powershell-install.md) (Instalowanie programu PowerShell dla usługi Azure Stack)
- [Konfigurowanie środowiska PowerShell użytkownika usługi Azure Stack](azure-stack-powershell-configure-user.md)  
