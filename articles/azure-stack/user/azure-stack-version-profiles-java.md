---
title: Za pomocą profilami wersji interfejsu API za pomocą języka Java w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o korzystaniu z profilami wersji interfejsu API za pomocą języka Java w usłudze Azure Stack.
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
ms.date: 09/25/2018
ms.author: sethm
ms.reviewer: sijuman
ms.openlocfilehash: b5a876ea8b5cc70ee0ca0dcac8628c12dc2b009b
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2018
ms.locfileid: "47413989"
---
# <a name="use-api-version-profiles-with-java-in-azure-stack"></a>Profilami wersji interfejsu API za pomocą języka Java w usłudze Azure Stack

*Dotyczy: Usługa Azure Stack zintegrowane systemy i usługi Azure Stack Development Kit*

Zestaw SDK Java usługi Azure Stack Resource Manager oferuje narzędzia ułatwiające tworzenie i zarządzanie infrastrukturą. Dostawcy zasobów w zestawie SDK obejmują obliczeniowych, sieci, magazynu, usługi aplikacji i [KeyVault](../../key-vault/key-vault-whatis.md). Zestaw Java SDK zawiera profilami interfejsu API przez dołączenie zależności w pliku Pom.xml, który ładuje prawidłowe moduły w początku pliku. Jednakże, można dodać wiele profilów jako zależności, takie jak **2018-03-01-hybrydowego**, lub **najnowsze** jako profil platformy Azure. Za pomocą tych zależności, ładuje prawidłowy moduł, dzięki czemu podczas tworzenia danego typu zasobu, można wybrać wersję interfejsu API z tych profilów, które chcesz użyć. Dzięki temu można używać najnowszej wersji na platformie Azure podczas tworzenia oprogramowania dla najnowszej wersji interfejsu API dla usługi Azure Stack. Przy użyciu zestawu SDK Java włącza środowisko programistyczne chmury hybrydowej. Profile interfejsu API zestawu SDK Java Włącz projektowania aplikacji w chmurze hybrydowej, która ułatwia przełączanie między zasobami w usłudze Azure Stack i globalnych zasobów platformy Azure.

## <a name="java-and-api-version-profiles"></a>Profile wersji języka Java i interfejsu API

Profil interfejsu API jest kombinacją dostawcy zasobów i wersje interfejsów API. Aby pobrać najnowsze, najbardziej stabilny wersję każdego typu zasobu w pakiecie dostawcy zasobów, można użyć profilu usługi interfejsu API.

- Aby korzystać z najnowszej wersji wszystkich usług, użyj **najnowsze** profil jako zależność.
    
   - Aby korzystać z najnowszych profilu, zależność jest **com.microsoft.azure**.

   - Aby korzystać z usług, które są zgodne z usługą Azure Stack, należy użyć **com.microsoft.azure.profile\_2018\_03\_01\_hybrydowego** profilu.
    
      - To można określić w pliku Pom.xml jako zależność, która automatycznie ładuje moduły Jeśli wybierz odpowiednie klasę z listy rozwijanej, tak jak przy użyciu platformy .NET.
        
          - Górnej części każdego modułu wygląda następująco:         
           `Import com.microsoft.azure.management.resources.v2018_03_01.ResourceGroup`
             

  - Zależności są wyświetlane w następujący sposób:
     ```xml
     <dependency>
     <groupId>com.microsoft.azure.profile_2018_03_01_hybrid</groupId>
     <artifactId>azure</artifactId>
     <version>1.0.0-beta</version>
     </dependency>
     ```

  - Aby użyć określonej wersji interfejsu API dla typu zasobu w dostawcy określonego zasobu, należy użyć określonej wersji interfejsu API, zdefiniowana za pomocą funkcji intellisense.

Należy pamiętać, że możesz połączyć wszystkie opcje dostępne w tej samej aplikacji.

## <a name="install-the-azure-java-sdk"></a>Instalacja platformy Azure Java SDK

Aby zainstalować zestaw SDK języka Java, wykonaj następujące kroki:

1.  Wykonaj instrukcje oficjalne, aby zainstalować program Git. Aby uzyskać instrukcje, zobacz [wprowadzenie — Instalowanie usługi Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).

2.  Postępuj zgodnie z instrukcjami oficjalne zainstalował [zestawu Java SDK](http://zulu.org/download/)) i [Maven](https://maven.apache.org/). Poprawna wersja jest wersja 8 Java Developer Kit. Poprawne narzędzia Apache Maven jest w wersji 3.0 lub nowszej. Zmienna środowiskowa JAVA_HOME musi być równa lokalizacji instalacji zestawu Java Development Kit do ukończenia tego przewodnika Szybki Start. Aby uzyskać więcej informacji, zobacz [tworzenie pierwszej funkcji przy użyciu języka Java i Maven](../../azure-functions/functions-create-first-java-maven.md).

3.  Aby zainstalować pakiety zależności poprawne, otwórz plik Pom.xml w aplikacji Java. Dodaj zależność, jak pokazano w poniższym kodzie:

   ```xml  
   <dependency>
   <groupId>com.microsoft.azure.profile_2018_03_01_hybrid</groupId>
   <artifactId>azure</artifactId>
   <version>1.0.0-beta</version>
   </dependency>
   ```

4.  Pakiety, które muszą być zainstalowane, zależy od wersji profilu, którego chcesz użyć. Nazwy pakietu dla wersji profilu są:
    
   - **COM.microsoft.Azure.profile\_2018\_03\_01\_hybrydowe**
   - **COM.microsoft.Azure**
      - **latest**

5.  Jeśli nie jest dostępny, Utwórz subskrypcję i Zapisz identyfikator subskrypcji do późniejszego użycia. Aby uzyskać instrukcje dotyczące sposobu tworzenia subskrypcji, zobacz [Tworzenie subskrypcji ofert w usłudze Azure Stack](../azure-stack-subscribe-plan-provision-vm.md).

6.  Tworzenie jednostki usługi i Zapisz identyfikator klienta oraz klucz tajny klienta. Aby uzyskać instrukcje dotyczące sposobu tworzenia jednostki usługi dla usługi Azure Stack, zobacz [zapewniają dostęp aplikacji do usługi Azure Stack](../azure-stack-create-service-principals.md). Należy pamiętać, że identyfikator klienta jest również nazywany identyfikator aplikacji podczas tworzenia nazwy głównej usługi.

7.  Upewnij się, że jednostki usługi rola właściciel/Współautor subskrypcji. Aby uzyskać instrukcje dotyczące sposobu przypisywania roli do jednostki usługi, zobacz [zapewniają dostęp aplikacji do usługi Azure Stack](../azure-stack-create-service-principals.md).

## <a name="prerequisites"></a>Wymagania wstępne

Za pomocą zestawu Azure .NET SDK usługi Azure Stack, należy podać następujące wartości, a następnie ustaw wartości zmiennych środowiskowych. Aby ustawić zmienne środowiskowe, zobacz instrukcje pod tabelą systemu operacyjnego.

| Wartość                     | Zmienne środowiskowe | Opis                                                                                                                                                                                                          |
| ------------------------- | --------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Identyfikator dzierżawy                 | TENANT_ID            | Zalety usługi Azure Stack [ <span class="underline">identyfikator dzierżawy</span>](../azure-stack-identity-overview.md).                                                          |
| Identyfikator klienta                 | CLIENT_ID             | Usługa identyfikator podmiotu zabezpieczeń aplikacji zapisywał informacje o nazwę główną usługi został utworzony w poprzedniej sekcji niniejszego dokumentu.                                                                                              |
| Identyfikator subskrypcji           | SUBSCRIPTION_ID      | [ <span class="underline">Identyfikator subskrypcji</span> ](../azure-stack-plan-offer-quota-overview.md#subscriptions) jest sposób uzyskiwania dostępu do oferty w usłudze Azure Stack.                |
| Wpis tajny klienta             | WARTOŚĆ CLIENT_SECRET        | Klucz tajny aplikacji nazwy głównej usługi zapisane podczas tworzenia nazwy głównej usługi.                                                                                                                                   |
| Punkt końcowy usługi Resource Manager | PUNKT KOŃCOWY              | Zobacz [ <span class="underline">punktu końcowego Menedżera zasobów usługi Azure Stack</span>](../user/azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint). |
| Lokalizacja                  | RESOURCE_LOCATION    | Lokalny dla usługi Azure Stack                                                                                                                                                                                                |

Aby znaleźć identyfikator dzierżawy usługi Azure Stack, należy postępować zgodnie z instrukcjami [tutaj](../azure-stack-csp-ref-operations.md). Aby ustawić zmienne środowiskowe, wykonaj następujące czynności:

### <a name="microsoft-windows"></a>Program Microsoft Windows

Aby ustawić zmienne środowiskowe w wierszu polecenia Windows, użyj następującego formatu:

```shell
Set Azure_Tenant_ID=<Your_Tenant_ID>
```

### <a name="macos-linux-and-unix-based-systems"></a>System macOS, Linux i komputerach z systemem Unix

W systemach Unix, na podstawie można użyć następującego polecenia:

```shell
Export Azure_Tenant_ID=<Your_Tenant_ID>
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
      "audiences": ["https://management.<yourtenant>.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
      }
}
```

## <a name="existing-api-profiles"></a>Istniejące profile interfejsu API

1.  **COM.microsoft.Azure.profile\_2018\_03\_01\_hybrydowego**: najnowsze profilu stworzona z myślą o usłudze Azure Stack. Użyj tego profilu usługi najbardziej zgodnych z usługą Azure Stack, tak długo, jak korzystasz z sygnatury 1808 lub więcej.

2.  **COM.microsoft.Azure.profile\_2017\_03\_09\_profilu**: Jeśli użytkownik pracuje na sygnaturę niższa niż kompilacji 1808, za pomocą tego profilu.

3.  **COM.microsoft.Azure**: profil składający się z najnowszymi wersjami programów wszystkich usług. Za pomocą najnowszej wersji wszystkich usług.

Aby uzyskać więcej informacji na temat profilów Azure Stack i interfejsu API, zobacz [profilami podsumowanie interfejsu API](../user/azure-stack-version-profiles.md#summary-of-api-profiles).

## <a name="azure-java-sdk-api-profile-usage"></a>Użycie interfejsu API zestawu SDK języka Java platformy Azure w profilu

Poniższy kod uwierzytelnia nazwę główną usługi w usłudze Azure Stack. Identyfikator i base uwierzytelniania, który jest specyficzny dla usługi Azure Stack, tworzy token przez dzierżawcę, który:

```java
AzureTokenCredentials credentials = new ApplicationTokenCredentials(client, tenant, key, AZURE_STACK)
                    .withDefaultSubscriptionId(subscriptionId);
            Azure azureStack = Azure.configure().withLogLevel(com.microsoft.rest.LogLevel.BASIC)
                    .authenticate(credentials, credentials.defaultSubscriptionId());
```

Dzięki temu można wdrożyć aplikację pomyślnym do usługi Azure Stack przy użyciu interfejsu API zależności profilu.

## <a name="define-azure-stack-environment-setting-functions"></a>Zdefiniuj funkcje ustawienie środowiska Azure Stack

Aby zarejestrować chmurą usługi Azure Stack z poprawne punkty końcowe, użyj następującego kodu:

```java
AzureEnvironment AZURE_STACK = new AzureEnvironment(new HashMap<String, String>() {
                {
                    put("managementEndpointUrl", settings.get("audience"));
                    put("resourceManagerEndpointUrl", armEndpoint);
                    put("galleryEndpointUrl", settings.get("galleryEndpoint"));
                    put("activeDirectoryEndpointUrl", settings.get("login_endpoint"));
                    put("activeDirectoryResourceId", settings.get("audience"));
                    put("activeDirectoryGraphResourceId", settings.get("graphEndpoint"));
                    put("storageEndpointSuffix", armEndpoint.substring(armEndpoint.indexOf('.')));
                    put("keyVaultDnsSuffix", ".adminvault" + armEndpoint.substring(armEndpoint.indexOf('.')));
                }
            });
```

`getActiveDirectorySettings` Wywołanie poniższy kod pobiera punktów końcowych z punktów końcowych metadanych. Stwierdza, zmienne środowiskowe z wykonanego wywołania:

```java
public static HashMap<String, String>
getActiveDirectorySettings(String armEndpoint) {

HashMap<String, String> adSettings = new HashMap<String, String>();

try {

// create HTTP Client
HttpClient httpClient = HttpClientBuilder.create().build();

// Create new getRequest with below mentioned URL
HttpGet getRequest = new
HttpGet(String.format("%s/metadata/endpoints?api-version=1.0",
armEndpoint));

// Add additional header to getRequest which accepts application/xml
data
getRequest.addHeader("accept", "application/xml");

// Execute request and catch response
HttpResponse response = httpClient.execute(getRequest);
```

## <a name="samples-using-api-profiles"></a>Przykłady korzystania z profilów interfejsu API

Do tworzenia rozwiązań przy użyciu profilów platformy .NET i interfejsu API usługi Azure Stack, można użyć następujących przykładów usługi GitHub jako odniesienia:

  - [Zarządzanie grupami zasobów](https://github.com/viananth/resources-java-manage-resource-group/tree/stack/Hybrid)

  - [Zarządzanie kontami magazynu](https://github.com/viananth/storage-java-manage-storage-accounts/tree/stack/Hybrid)

  - [Zarządzanie maszyną wirtualną](https://github.com/viananth/compute-java-manage-vm/tree/stack/Hybrid)

### <a name="sample-unit-test-project"></a>Przykładowy projekt testu jednostki 

1.  Sklonuj repozytorium przy użyciu następującego polecenia:
    
    `git clone https://github.com/viananth/resources-java-manage-resource-group/tree/stack/Hybrid`

2.  Tworzenie jednostki usługi platformy Azure i przypisywanie roli dostęp do subskrypcji. Aby uzyskać instrukcje dotyczące tworzenia jednostki usługi, zobacz [użyciu programu Azure PowerShell utworzyć nazwę główną usługi za pomocą certyfikatu](../azure-stack-create-service-principals.md).

3.  Pobrać wymagane następujące wartości:
    
   1.  Identyfikator dzierżawy
   2.  Identyfikator klienta
   3.  Wpis tajny klienta
   4.  Identyfikator subskrypcji
   5.  Punkt końcowy usługi Resource Manager
   6.  Lokalizacja zasobu

4.  Ustaw następujące zmienne środowiskowe, korzystając z informacji pobrane z nazwy głównej usługi zostały utworzone za pomocą wiersza polecenia:
    
   1. Eksportuj TENANT_ID = {swój identyfikator dzierżawy}
   2. Eksportuj CLIENT_ID = {identyfikatora klienta}
   3. Eksportuj CLIENT_SECRET = {klucz tajny klienta}
   4. Eksportuj SUBSCRIPTION_ID = {identyfikator subskrypcji}
   5. Eksportuj ARM_ENDPOINT = {adres URL Menedżer usłudze Azure Stack Resource}
   6. Eksportuj RESOURCE_LOCATION = {lokalizacji usługi Azure Stack}

   W Windows, użyj **ustaw** zamiast **wyeksportować**.

5.  Użyj `getactivedirectorysettings` kod, aby pobrać punktu końcowego metadanych usługi arm i użyj klienta HTTP, aby ustawić informacje o punkcie końcowym.

   ```java
   public static HashMap<String, String> getActiveDirectorySettings(String armEndpoint) {
   HashMap<String, String> adSettings = new HashMap<String,> String>();

   try {

   // create HTTP Client
   HttpClient httpClient = HttpClientBuilder.create().build();

   // Create new getRequest with below mentioned URL
   HttpGet getRequest = new
   HttpGet(String.format("%s/metadata/endpoints?api-version=1.0", armEndpoint));

   // Add additional header to getRequest which accepts application/xml data
   getRequest.addHeader("accept", "application/xml");

   // Execute request and catch response
   HttpResponse response = httpClient.execute(getRequest);
   ```

7.  W pliku pom.xml Dodaj zależność poniżej, aby używać profilu 2018-03-01-hybrydowych usługi Azure Stack. Ta zależność zainstaluje modułów skojarzony z tym profilem dla dostawców zasobów obliczeniowych, sieci, magazynu, KeyVault i usług aplikacji.
    
   Pamiętaj, że można używać najnowszych profilu do platformy Azure:
        
   ```xml
   <dependency>
   <groupId>com.microsoft.azure.profile_2018_03_01_hybrid</groupId>
   <artifactId>azure</artifactId>
   <version>1.0.0-beta</version>
   </dependency>
   ```

8.  W wierszu polecenia, który został otwarty, aby ustawić zmienne środowiskowe wpisz następujący wiersz:
    
   ```shell
   mvn clean compile exec:java
   ```

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji o profilach interfejsu API Zobacz:

- [Zarządzanie profilami wersji interfejsu API w usłudze Azure Stack](azure-stack-version-profiles.md)
- [Wersje interfejsu API dostawcy zasobów obsługiwane przez profile](azure-stack-profiles-azure-resource-manager-versions.md)
