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
ms.date: 04/02/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 09/28/2018
ms.openlocfilehash: 0a2a42860ad4487f470aea9c4d2be8eba1fbe8ab
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2019
ms.locfileid: "58802851"
---
# <a name="use-api-version-profiles-with-java-in-azure-stack"></a>Profilami wersji interfejsu API za pomocą języka Java w usłudze Azure Stack

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

Zestaw SDK Java usługi Azure Stack Resource Manager oferuje narzędzia ułatwiające tworzenie i zarządzanie infrastrukturą. Dostawcy zasobów w zestawie SDK obejmują obliczeniowych, sieci, magazynu, usługi aplikacji i [KeyVault](../../key-vault/key-vault-whatis.md). Zestaw Java SDK zawiera profilami interfejsu API przez dołączenie zależności w pliku Pom.xml, który ładuje prawidłowe moduły w początku pliku. Jednakże, można dodać wiele profilów jako zależności, takie jak **2018-03-01-hybrydowego**, lub **najnowsze**, jako profil platformy Azure. Za pomocą tych zależności, ładuje prawidłowy moduł, dzięki czemu podczas tworzenia danego typu zasobu, można wybrać wersję interfejsu API z tych profilów, które chcesz użyć. Dzięki temu można używać najnowszej wersji na platformie Azure podczas tworzenia oprogramowania dla najnowszej wersji interfejsu API dla usługi Azure Stack. Przy użyciu zestawu SDK Java włącza środowisko programistyczne chmury hybrydowej. Profile interfejsu API zestawu SDK Java Włącz projektowania aplikacji w chmurze hybrydowej, która ułatwia przełączanie między zasobami w usłudze Azure Stack i globalnych zasobów platformy Azure.

## <a name="java-and-api-version-profiles"></a>Profile wersji języka Java i interfejsu API

Profil interfejsu API jest kombinacją dostawcy zasobów i wersje interfejsów API. Aby pobrać najnowsze, najbardziej stabilny wersję każdego typu zasobu w pakiecie dostawcy zasobów, można użyć profilu usługi interfejsu API.

- Aby korzystać z najnowszej wersji wszystkich usług, użyj **najnowsze** profil jako zależność.

  - Aby korzystać z najnowszych profilu, zależność jest **com.microsoft.azure**.

  - Aby korzystać z usług, które są zgodne z usługą Azure Stack, należy użyć **com.microsoft.azure.profile\_2018\_03\_01\_hybrydowego** profilu.

    - To można określić w pliku Pom.xml jako zależność, która automatycznie ładuje moduły Jeśli wybierz odpowiednie klasę z listy rozwijanej, tak jak przy użyciu platformy .NET.

    - Górnej części każdego modułu wygląda następująco:      `Import com.microsoft.azure.management.resources.v2018_03_01.ResourceGroup`

  - Zależności są wyświetlane w następujący sposób:

     ```xml
     <dependency>
     <groupId>com.microsoft.azure.profile_2018_03_01_hybrid</groupId>
     <artifactId>azure</artifactId>
     <version>1.0.0-beta</version>
     </dependency>
     ```

  - Aby użyć określonej wersji interfejsu API dla typu zasobu w dostawcy określonego zasobu, należy użyć określonej wersji interfejsu API, zdefiniowana za pomocą funkcji Intellisense.

Należy pamiętać, że możesz połączyć wszystkie opcje dostępne w tej samej aplikacji.

## <a name="install-the-azure-java-sdk"></a>Instalacja platformy Azure Java SDK

Aby zainstalować zestaw SDK języka Java, wykonaj następujące kroki:

1. Wykonaj instrukcje oficjalne, aby zainstalować program Git. Aby uzyskać instrukcje, zobacz [wprowadzenie — Instalowanie usługi Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).

2. Postępuj zgodnie z instrukcjami, aby zainstalować [zestawu Java SDK](https://zulu.org/download/) i [Maven](https://maven.apache.org/). Poprawna wersja jest wersja 8 Java Developer Kit. Poprawne narzędzia Apache Maven jest w wersji 3.0 lub nowszej. Zmienna środowiskowa JAVA_HOME musi być równa lokalizacji instalacji zestawu Java Development Kit do ukończenia tego przewodnika Szybki Start. Aby uzyskać więcej informacji, zobacz [tworzenie pierwszej funkcji przy użyciu języka Java i Maven](../../azure-functions/functions-create-first-java-maven.md).

3. Aby zainstalować pakiety zależności poprawne, otwórz plik Pom.xml w aplikacji Java. Dodaj zależność, jak pokazano w poniższym kodzie:

   ```xml  
   <dependency>
   <groupId>com.microsoft.azure.profile_2018_03_01_hybrid</groupId>
   <artifactId>azure</artifactId>
   <version>1.0.0-beta</version>
   </dependency>
   ```

4. Zestaw pakietów, które muszą być zainstalowane, zależy od wersji profilu, którego chcesz użyć. Nazwy pakietu dla wersji profilu są:

   - **com.microsoft.azure.profile\_2018\_03\_01\_hybrid**
   - **com.microsoft.azure**
     - **latest**

5. Jeśli nie jest dostępny, Utwórz subskrypcję i Zapisz identyfikator subskrypcji do późniejszego użycia. Aby uzyskać instrukcje dotyczące sposobu tworzenia subskrypcji, zobacz [Tworzenie subskrypcji ofert w usłudze Azure Stack](../azure-stack-subscribe-plan-provision-vm.md).

6. Tworzenie jednostki usługi, a następnie zapisz klienta, identyfikator i klucz tajny klienta. Aby uzyskać instrukcje dotyczące sposobu tworzenia jednostki usługi dla usługi Azure Stack, zobacz [zapewniają dostęp aplikacji do usługi Azure Stack](../azure-stack-create-service-principals.md). Należy pamiętać, że identyfikator klienta jest również nazywany identyfikator aplikacji podczas tworzenia nazwy głównej usługi.

7. Upewnij się, że jednostki usługi rola właściciel/Współautor subskrypcji. Aby uzyskać instrukcje dotyczące sposobu przypisywania roli do jednostki usługi, zobacz [zapewniają dostęp aplikacji do usługi Azure Stack](../azure-stack-create-service-principals.md).

## <a name="prerequisites"></a>Wymagania wstępne

Za pomocą zestawu Azure Java SDK usługi Azure Stack, należy podać następujące wartości, a następnie ustaw wartości zmiennych środowiskowych. Aby ustawić zmienne środowiskowe, zobacz instrukcje pod tabelą systemu operacyjnego.

| Wartość                     | Zmienne środowiskowe | Opis                                                                                                                                                                                                          |
| ------------------------- | --------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Identyfikator dzierżawy                 | AZURE_TENANT_ID            | Zalety usługi Azure Stack [identyfikator dzierżawy](../azure-stack-identity-overview.md).                                                          |
| Identyfikator klienta                 | AZURE_CLIENT_ID             | Usługa identyfikator podmiotu zabezpieczeń aplikacji zapisywał informacje o jednostkę usługi został utworzony w poprzedniej sekcji.                                                                                              |
| Identyfikator subskrypcji           | AZURE_SUBSCRIPTION_ID      | [Identyfikator subskrypcji](../azure-stack-plan-offer-quota-overview.md#subscriptions) jest sposób uzyskiwania dostępu do oferty w usłudze Azure Stack.                |
| Wpis tajny klienta             | AZURE_CLIENT_SECRET        | Klucz tajny aplikacji jednostki usługi zapisane podczas tworzenia nazwy głównej usługi.                                                                                                                                   |
| Punkt końcowy usługi Resource Manager | ARM_ENDPOINT              | Zobacz [punktu końcowego usługi Azure Stack Resource Manager](../user/azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint). |
| Lokalizacja                  | RESOURCE_LOCATION    | **Lokalne** dla usługi Azure Stack.                                                                                                                                                                                                |

Aby znaleźć identyfikator dzierżawy usługi Azure Stack, zapoznaj się z instrukcjami [tutaj](../azure-stack-csp-ref-operations.md). Aby ustawić zmienne środowiskowe, wykonaj następujące czynności:

### <a name="microsoft-windows"></a>Microsoft Windows

Aby ustawić zmienne środowiskowe w wierszu polecenia Windows, użyj następującego formatu:

```shell
Set AZURE_TENANT_ID=<Your_Tenant_ID>
```

### <a name="macos-linux-and-unix-based-systems"></a>System macOS, Linux i komputerach z systemem Unix

W systemach Unix, na podstawie Użyj następującego polecenia:

```shell
Export AZURE_TENANT_ID=<Your_Tenant_ID>
```

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Traktować jako zaufany certyfikat główny urzędu usługi Azure Stack

Jeśli używasz ASDK muszą ufać certyfikat główny urzędu certyfikacji na komputerze zdalnym. Nie należy to zrobić przy użyciu zintegrowanych systemów.

#### <a name="windows"></a>Windows

1. Wyeksportuj certyfikat z podpisem własnym usługi Azure Stack na pulpicie.

1. W wierszu polecenia Zmień katalog % JAVA_HOME%\bin.

1. Uruchom następujące polecenie:

   ```shell
   .\keytool.exe -importcert -noprompt -file <location of the exported certificate here> -alias root -keystore %JAVA_HOME%\lib\security\cacerts -trustcacerts -storepass changeit
   ```

### <a name="the-azure-stack-resource-manager-endpoint"></a>Punkt końcowy usługi Azure Stack Resource Manager

Menedżer zasobów platformy Azure to platforma zarządzania, która umożliwia administratorom wdrażanie, zarządzanie i monitorowanie zasobów platformy Azure. Usługa Azure Resource Manager może obsługiwać te zadania jako grupę, a nie indywidualnie, w ramach jednej operacji.

Możesz uzyskać informacje o metadanych z punktu końcowego usługi Resource Manager. Punkt końcowy zwraca plik w formacie JSON z informacjami wymaganymi do uruchomienia kodu.

Należy zwrócić uwagę następujące kwestie:

- **ResourceManagerUrl** w usłudze Azure Stack Development Kit (ASDK) to: https://management.local.azurestack.external/.

- **ResourceManagerUrl** w systemach zintegrowanych: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`.

Aby pobrać metadane wymagane: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`.

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

- **com.microsoft.azure.profile\_2018\_03\_01\_hybrid**: Profil najnowsze stworzona z myślą o usłudze Azure Stack. Użyj tego profilu usługi najbardziej zgodnych z usługą Azure Stack, tak długo, jak korzystasz z sygnatury 1808 lub więcej.

- **com.microsoft.azure**: Profil składający się z najnowszymi wersjami programów wszystkich usług. Za pomocą najnowszej wersji wszystkich usług.

Aby uzyskać więcej informacji na temat profilów Azure Stack i interfejsu API, zobacz [profilami podsumowanie interfejsu API](../user/azure-stack-version-profiles.md#summary-of-api-profiles).

## <a name="azure-java-sdk-api-profile-usage"></a>Użycie interfejsu API zestawu SDK języka Java platformy Azure w profilu

Poniższy kod uwierzytelnia nazwę główną usługi w usłudze Azure Stack. Tworzy token przy użyciu Identyfikatora dzierżawy i base uwierzytelniania, który jest specyficzny dla usługi Azure Stack:

```java
AzureTokenCredentials credentials = new ApplicationTokenCredentials(client, tenant, key, AZURE_STACK)
                    .withDefaultSubscriptionID(subscriptionID);
Azure azureStack = Azure.configure()
                    .withLogLevel(com.microsoft.rest.LogLevel.BASIC)
                    .authenticate(credentials, credentials.defaultSubscriptionID());
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
                    put("activeDirectoryResourceID", settings.get("audience"));
                    put("activeDirectoryGraphResourceID", settings.get("graphEndpoint"));
                    put("storageEndpointSuffix", armEndpoint.substring(armEndpoint.indexOf('.')));
                    put("keyVaultDnsSuffix", ".vault" + armEndpoint.substring(armEndpoint.indexOf('.')));
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

// Add additional header to getRequest which accepts application/xml data
getRequest.addHeader("accept", "application/xml");

// Execute request and catch response
HttpResponse response = httpClient.execute(getRequest);
```

## <a name="samples-using-api-profiles"></a>Przykłady korzystania z profilów interfejsu API

Do tworzenia rozwiązań przy użyciu profilów platformy .NET i interfejsu API usługi Azure Stack, można użyć następujących przykładów usługi GitHub jako odniesienia:

- [Zarządzanie grupami zasobów](https://github.com/Azure-Samples/Hybrid-resources-java-manage-resource-group)

- [Zarządzanie kontami magazynu](https://github.com/Azure-Samples/hybrid-storage-java-manage-storage-accounts)

- [Zarządzanie maszyną wirtualną](https://github.com/Azure-Samples/hybrid-compute-java-manage-vm)

### <a name="sample-unit-test-project"></a>Przykładowy projekt testu jednostki

1. Sklonuj repozytorium przy użyciu następującego polecenia:

   `git clone https://github.com/Azure-Samples/Hybrid-resources-java-manage-resource-group.git`

2. Tworzenie jednostki usługi platformy Azure i przypisywanie roli dostęp do subskrypcji. Aby uzyskać instrukcje dotyczące tworzenia jednostki usługi, zobacz [użyciu programu Azure PowerShell utworzyć nazwę główną usługi za pomocą certyfikatu](../azure-stack-create-service-principals.md).

3. Pobierz następujące wartości zmiennych środowiskowych wymagane:

   - AZURE_TENANT_ID
   - AZURE_CLIENT_ID
   - AZURE_CLIENT_SECRET
   - AZURE_SUBSCRIPTION_ID
   - ARM_ENDPOINT
   - RESOURCE_LOCATION

4. Ustaw następujące zmienne środowiskowe, korzystając z informacji pobrane z usługi jednostki, zostały utworzone za pomocą wiersza polecenia:

   - Eksportuj AZURE_TENANT_ID = {swój identyfikator dzierżawy}
   - Eksportuj AZURE_CLIENT_ID = {Identyfikatora klienta}
   - Eksportuj AZURE_CLIENT_SECRET = {klucz tajny klienta}
   - Eksportuj AZURE_SUBSCRIPTION_ID = {identyfikator subskrypcji}
   - Eksportuj ARM_ENDPOINT = {URL Azure Stack Resource Manager}
   - Eksportuj RESOURCE_LOCATION = {lokalizacji usługi Azure Stack}

   W Windows, użyj **ustaw** zamiast **wyeksportować**.

5. Użyj `getactivedirectorysettings` kod, aby pobrać punktu końcowego metadanych usługi arm i użyj klienta HTTP, aby ustawić informacje o punkcie końcowym.

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

6. W pliku Pom.xml, dodaj następującą zależność do użycia **2018-03-01-hybrydowego** profilu dla usługi Azure Stack. Ta zależność instaluje moduły skojarzony z tym profilem dla dostawców zasobów obliczeniowych, sieci, magazynu, KeyVault i usług aplikacji:

   ```xml
   <dependency>
   <groupId>com.microsoft.azure.profile_2018_03_01_hybrid</groupId>
   <artifactId>azure</artifactId>
   <vers1s.0.0-beta</version>
   </dependency>
   ```

7. W wierszu polecenia, który został otwarty, aby ustawić zmienne środowiskowe wprowadź następujące polecenie:

   ```shell
   mvn clean compile exec:java
   ```

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji o profilach interfejsu API Zobacz:

- Profile wersji w Stack](azure-stack-version-profiles.md) platformy Azure
- [Wersje interfejsu API dostawcy zasobów obsługiwane przez profile](azure-stack-profiles-azure-resource-manager-versions.md)
