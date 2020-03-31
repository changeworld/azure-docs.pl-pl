---
title: Konfigurowanie uwierzytelniania
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak skonfigurować i skonfigurować uwierzytelnianie dla różnych zasobów i przepływów pracy w usłudze Azure Machine Learning. Istnieje wiele sposobów konfigurowania i używania uwierzytelniania w ramach usługi, począwszy od prostych uwierzytelniania opartego na interfejsie użytkownika do celów programisty lub testowania, po pełne uwierzytelnianie jednostkowe usługi Azure Active Directory.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: fcaa7a0c44851d6b48b40b01af4c8ec992c330b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283539"
---
# <a name="set-up-authentication-for-azure-machine-learning-resources-and-workflows"></a>Konfigurowanie uwierzytelniania dla zasobów i przepływów pracy usługi Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule dowiesz się, jak skonfigurować i skonfigurować uwierzytelnianie dla różnych zasobów i przepływów pracy w usłudze Azure Machine Learning. Istnieje wiele sposobów uwierzytelniania w usłudze, począwszy od prostych uwierzytelniania opartego na interfejsie użytkownika do celów programisty lub testowania do pełnego uwierzytelniania głównego usługi Active Directory platformy Azure. W tym artykule wyjaśniono również różnice w sposobie uwierzytelniania usługi sieci Web, a także jak uwierzytelnić się w interfejsie API REST usługi Azure Machine Learning.

W tym instrukcje pokazano, jak wykonać następujące zadania:

* Używanie interaktywnego uwierzytelniania interfejsu użytkownika do testowania/tworzenia
* Konfigurowanie uwierzytelniania głównego usługi
* Uwierzytelnianie w obszarze roboczym
* Pobierz tokeny typu nośnika usługi OAuth2.0 dla interfejsu API REST usługi Azure Machine Learning
* Opis uwierzytelniania usługi sieci Web

Zobacz [artykuł koncepcyjny,](concept-enterprise-security.md) aby zapoznać się z ogólnym omówieniem zabezpieczeń i uwierzytelniania w usłudze Azure Machine Learning.

## <a name="prerequisites"></a>Wymagania wstępne

* Utwórz [obszar roboczy usługi Azure Machine Learning](how-to-manage-workspace.md).
* [Skonfiguruj środowisko programistyczne,](how-to-configure-environment.md) aby zainstalować zestaw SDK usługi Azure Machine Learning lub użyj [maszyny wirtualnej usługi Azure Machine Learning notebook](concept-azure-machine-learning-architecture.md#compute-instance) z już zainstalowanym zestawem SDK.

## <a name="interactive-authentication"></a>Uwierzytelnianie interakcyjne

Większość przykładów w dokumentacji dla tej usługi używać uwierzytelniania interaktywnego w notesach Jupyter jako prosta metoda testowania i demonstracji. Jest to lekki sposób, aby sprawdzić, co budujesz. Istnieją dwa wywołania funkcji, które automatycznie monitują o przepływ uwierzytelniania opartego na interfejsie użytkownika.

Wywołanie `from_config()` funkcji spowoduje wyświetlenie monitu.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

Funkcja `from_config()` wyszukuje plik JSON zawierający informacje o połączeniu obszaru roboczego. Można również określić szczegóły połączenia jawnie przy użyciu `Workspace` konstruktora, który będzie również monitować o uwierzytelnianie interaktywne. Oba wywołania są równoważne.

```python
ws = Workspace(subscription_id="your-sub-id",
               resource_group="your-resource-group-id",
               workspace_name="your-workspace-name"
              )
```

Jeśli masz dostęp do wielu dzierżaw, może być konieczne zaimportowanie klasy i jawnie zdefiniować, co dzierżawy są kierowane. Wywołanie konstruktora dla `InteractiveLoginAuthentication` będzie również monit o zalogowanie się podobne do wywołań powyżej.

```python
from azureml.core.authentication import InteractiveLoginAuthentication
interactive_auth = InteractiveLoginAuthentication(tenant_id="your-tenant-id")
```

Podczas testowania i uczenia się, interaktywne uwierzytelnianie nie pomoże w tworzeniu zautomatyzowanych lub bezgłowych przepływów pracy. Konfigurowanie uwierzytelniania jednostkowego usługi jest najlepszym rozwiązaniem dla zautomatyzowanych procesów, które używają sdk.

## <a name="set-up-service-principal-authentication"></a>Konfigurowanie uwierzytelniania głównego usługi

Ten proces jest niezbędny do włączenia uwierzytelniania, które jest oddzielone od określonego logowania użytkownika, co pozwala na uwierzytelnianie do usługi Azure Machine Learning Python SDK w zautomatyzowanych przepływów pracy. Uwierzytelnianie jednostkowe usługi umożliwia również [uwierzytelnianie w interfejsie API REST](#azure-machine-learning-rest-api-auth).

Aby skonfigurować uwierzytelnianie jednostkowe usługi, należy najpierw utworzyć rejestrację aplikacji w usłudze Azure Active Directory, a następnie udzielić aplikacji dostępu opartego na rolach do obszaru roboczego ml. Najprostszym sposobem ukończenia tej konfiguracji jest za pośrednictwem [usługi Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) w witrynie Azure portal. Po zalogowaniu się do `>_` portalu kliknij ikonę w prawym górnym rogu strony w pobliżu swojego imienia i nazwiska, aby otworzyć powłokę.

Jeśli powłoka w chmurze nie była wcześniej używana na koncie platformy Azure, należy utworzyć zasób konta magazynu do przechowywania wszystkich zapisywanych plików. Ogólnie rzecz biorąc, to konto magazynu poniesie nieznaczny miesięczny koszt. Ponadto zainstaluj rozszerzenie uczenia maszynowego, jeśli nie był używany wcześniej z następującym poleceniem.

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!NOTE]
> Musisz być administratorem w subskrypcji, aby wykonać następujące kroki.

Następnie uruchom następujące polecenie, aby utworzyć jednostkę usługi. Nadaj mu nazwę, w tym przypadku **ml-auth**.

```azurecli-interactive
az ad sp create-for-rbac --sdk-auth --name ml-auth
```

Dane wyjściowe będą JSON podobne do następujących. Zanotuj `clientId` `clientSecret`pola `tenantId` , i , ponieważ będą potrzebne do innych kroków w tym artykule.

```json
{
    "clientId": "your-client-id",
    "clientSecret": "your-client-secret",
    "subscriptionId": "your-sub-id",
    "tenantId": "your-tenant-id",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com",
    "activeDirectoryGraphResourceId": "https://graph.windows.net",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:5555",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net"
}
```

Następnie uruchom następujące polecenie, aby uzyskać szczegółowe informacje na temat `clientId` jednostki usługi, który `--id` właśnie utworzono, używając wartości z góry jako danych wejściowych do parametru.

```azurecli-interactive
az ad sp show --id your-client-id
```

Poniżej przedstawiono uproszczony przykład danych wyjściowych JSON z polecenia. Zanotuj `objectId` pole, ponieważ będziesz potrzebować jego wartości dla następnego kroku.

```json
{
    "accountEnabled": "True",
    "addIns": [],
    "appDisplayName": "ml-auth",
    ...
    ...
    ...
    "objectId": "your-sp-object-id",
    "objectType": "ServicePrincipal"
}
```

Następnie użyj następującego polecenia, aby przypisać dostęp jednostki usługi do obszaru roboczego uczenia maszynowego. Nazwa obszaru roboczego i jego nazwa grupy `-w` zasobów `-g` będą odpowiednio potrzebne do wykonania i parametrów. Dla `--user` parametru należy `objectId` użyć wartości z poprzedniego kroku. Parametr `--role` umożliwia ustawienie roli dostępu dla jednostki usługi i ogólnie będzie używany **albo właściciela** lub **współautora.** Oba mają dostęp do zapisu do istniejących zasobów, takich jak klastry obliczeniowe i magazyny danych, ale tylko **właściciel** może aprowizować te zasoby. 

```azurecli-interactive
az ml workspace share -w your-workspace-name -g your-resource-group-name --user your-sp-object-id --role owner
```

To wywołanie nie powoduje żadnych danych wyjściowych, ale teraz skonfigurowane jest uwierzytelnianie jednostkowe usługi dla obszaru roboczego.

## <a name="authenticate-to-your-workspace"></a>Uwierzytelnij się w obszarze roboczym

Teraz, gdy masz włączoną uwierzytelniania jednostki usługi, można uwierzytelnić się w obszarze roboczym w SDK bez fizycznego logowania jako użytkownik. Użyj `ServicePrincipalAuthentication` konstruktora klasy i użyj wartości, które zostały dodane z poprzednich kroków jako parametry. Parametr `tenant_id` jest `tenantId` mapowana `service_principal_id` z `clientId`góry, `service_principal_password` mapuje do , i mapuje do `clientSecret`.

```python
from azureml.core.authentication import ServicePrincipalAuthentication

sp = ServicePrincipalAuthentication(tenant_id="your-tenant-id", # tenantID
                                    service_principal_id="your-client-id", # clientId
                                    service_principal_password="your-client-secret") # clientSecret
```

Zmienna `sp` zawiera teraz obiekt uwierzytelniania, którego używasz bezpośrednio w SDK. Ogólnie rzecz biorąc jest dobrym pomysłem do przechowywania identyfikatorów/wpisów tajnych używanych powyżej w zmiennych środowiskowych, jak pokazano w poniższym kodzie.

```python
import os 

sp = ServicePrincipalAuthentication(tenant_id=os.environ['AML_TENANT_ID'],
                                    service_principal_id=os.environ['AML_PRINCIPAL_ID'],
                                    service_principal_password=os.environ['AML_PRINCIPAL_PASS'])
```

W przypadku zautomatyzowanych przepływów pracy, które są uruchamiane w języku Python i używają głównie sdk, można użyć tego obiektu w stanie takim, w jakim jest w większości przypadków do uwierzytelniania. Poniższy kod uwierzytelnia się w obszarze roboczym przy użyciu właśnie utworzonego obiektu uwierzytelniania.

```python
from azureml.core import Workspace

ws = Workspace.get(name="ml-example", 
                   auth=sp,
                   subscription_id="your-sub-id")
ws.get_details()
```

## <a name="azure-machine-learning-rest-api-auth"></a>Auth interfejsu API rest usługi Azure Machine Learning

Podmiot usługi utworzony w powyższych krokach może również służyć do uwierzytelniania w [interfejsie API REST](https://docs.microsoft.com/rest/api/azureml/)usługi Azure Machine Learning. Przepływ [poświadczeń klienta usługi](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)Azure Active Directory umożliwia wykonywanie połączeń między usługami dla uwierzytelniania bezgłowego w zautomatyzowanych przepływach pracy. Przykłady są implementowane za pomocą [biblioteki ADAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) w pythonie i node.js, ale można również użyć dowolnej biblioteki open source, która obsługuje OpenID Connect 1.0. 

> [!NOTE]
> MSAL.js jest nowszą biblioteką niż biblioteka ADAL, ale nie można wykonać uwierzytelniania usługi do usługi przy użyciu poświadczeń klienta za pomocą pliku MSAL.js, ponieważ jest to przede wszystkim biblioteka po stronie klienta przeznaczona do uwierzytelniania interaktywnego/interfejsu użytkownika powiązana z określonym użytkownikiem. Zalecamy użycie biblioteki ADAL, jak pokazano poniżej, aby tworzyć zautomatyzowane przepływy pracy za pomocą interfejsu API REST.

### <a name="nodejs"></a>Node.js

Poniższe kroki można wykonać, aby wygenerować token eru przy użyciu pliku Node.js. W swoim środowisku uruchom program `npm install adal-node`. Następnie użyj `tenantId`programu `clientId`, `clientSecret` i jednostki usługi utworzonej w powyższych krokach jako wartości pasujących zmiennych w poniższym skrypcie.

```javascript
const adal = require('adal-node').AuthenticationContext;

const authorityHostUrl = 'https://login.microsoftonline.com/';
const tenantId = 'your-tenant-id';
const authorityUrl = authorityHostUrl + tenantId;
const clientId = 'your-client-id';
const clientSecret = 'your-client-secret';
const resource = 'https://management.azure.com/';

const context = new adal(authorityUrl);

context.acquireTokenWithClientCredentials(
  resource,
  clientId,
  clientSecret,
  (err, tokenResponse) => {
    if (err) {
      console.log(`Token generation failed due to ${err}`);
    } else {
      console.dir(tokenResponse, { depth: null, colors: true });
    }
  }
);
```

Zmienna `tokenResponse` jest obiektem, który zawiera token i skojarzone metadane, takie jak czas wygaśnięcia. Tokeny są prawidłowe przez 1 godzinę i można odświeżyć, uruchamiając to samo wywołanie ponownie, aby pobrać nowy token. Poniżej przedstawiono przykładową odpowiedź.

```javascript
{ 
    tokenType: 'Bearer',
    expiresIn: 3599,
    expiresOn: 2019-12-17T19:15:56.326Z,
    resource: 'https://management.azure.com/',
    accessToken: "random-oauth-token",
    isMRRT: true,
    _clientId: 'your-client-id',
    _authority: 'https://login.microsoftonline.com/your-tenant-id' 
}
```

Użyj `accessToken` właściwości, aby pobrać token eru. Zobacz [dokumentację interfejsu API REST,](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API) aby zapoznać się z przykładami dotyczącymi używania tokenu do nawiązywać wywołania interfejsu API.

### <a name="python"></a>Python 

Poniższe kroki można wykonać, aby wygenerować token eru przy użyciu języka Python. W swoim środowisku uruchom program `pip install adal`. Następnie użyj `tenantId`programu `clientId`, `clientSecret` i jednostki usługi utworzonej w powyższych krokach jako wartości dla odpowiednich zmiennych w poniższym skrypcie.

```python
from adal import AuthenticationContext

client_id = "your-client-id"
client_secret = "your-client-secret"
resource_url = "https://login.microsoftonline.com"
tenant_id = "your-tenant-id"
authority = "{}/{}".format(resource_url, tenant_id)

auth_context = AuthenticationContext(authority)
token_response = auth_context.acquire_token_with_client_credentials("https://management.azure.com/", client_id, client_secret)
print(token_response)
```

Zmienna `token_response` jest słownikiem, który zawiera token i skojarzone metadane, takie jak czas wygaśnięcia. Tokeny są prawidłowe przez 1 godzinę i można odświeżyć, uruchamiając to samo wywołanie ponownie, aby pobrać nowy token. Poniżej przedstawiono przykładową odpowiedź.

```python
{
    'tokenType': 'Bearer', 
    'expiresIn': 3599, 
    'expiresOn': '2019-12-17 19:47:15.150205', 
    'resource': 'https://management.azure.com/', 
    'accessToken': 'random-oauth-token', 
    'isMRRT': True, 
    '_clientId': 'your-client-id', 
    '_authority': 'https://login.microsoftonline.com/your-tenant-id'
}
```

Służy `token_response["accessToken"]` do pobierania tokenu eru. Zobacz [dokumentację interfejsu API REST,](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API) aby zapoznać się z przykładami dotyczącymi używania tokenu do nawiązywać wywołania interfejsu API.

## <a name="web-service-authentication"></a>Uwierzytelnianie usługi sieci Web

Usługi sieci Web w usłudze Azure Machine Learning używają innego wzorca uwierzytelniania niż opisano powyżej. Najprostszym sposobem uwierzytelniania w wdrożonych usługach internetowych jest użycie **uwierzytelniania opartego na kluczach,** które generuje statyczne klucze uwierzytelniania typu okaziciela, które nie muszą być odświeżane. Jeśli wystarczy uwierzytelnić się tylko w wdrożonej usłudze sieci web, nie trzeba konfigurowani uwierzytelniania zasady usługi, jak pokazano powyżej.

Usługi sieci Web wdrożone w usłudze Azure Kubernetes mają domyślnie *włączoną* auth opartą na kluczach. Wdrożone usługi wystąpienia kontenerów platformy Azure mają domyślnie *wyłączono* erę `auth_enabled=True`opartą na kluczach, ale można ją włączyć, ustawiając ją podczas tworzenia usługi sieci Web usługi ACI. Poniżej przedstawiono przykład tworzenia konfiguracji wdrażania usługi ACI z włączoną erą opartą na kluczach.

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(cpu_cores = 1,
                                                memory_gb = 1,
                                                auth_enabled=True)
```

Następnie można użyć niestandardowej konfiguracji ACI `Model` we wdrożeniu przy użyciu klasy.

```python
from azureml.core.model import Model, InferenceConfig


inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
aci_service = Model.deploy(workspace=ws,
                       name="aci_service_sample",
                       models=[model],
                       inference_config=inference_config,
                       deployment_config=aci_config)
aci_service.wait_for_deployment(True)
```

Aby pobrać klucze eru, użyj pliku `aci_service.get_keys()`. Aby ponownie wygenerować `regen_key()` klucz, użyj funkcji i przekaż **podstawowy** lub **pomocniczy**.

```python
aci_service.regen_key("Primary")
# or
aci_service.regen_key("Secondary")
```

Usługi sieci Web obsługują również uwierzytelnianie oparte na tokenach, ale tylko dla wdrożeń usługi Azure Kubernetes. Zobacz [instrukcje dotyczące](how-to-consume-web-service.md) korzystania z usług internetowych, aby uzyskać dodatkowe informacje na temat uwierzytelniania.

### <a name="token-based-web-service-authentication"></a>Uwierzytelnianie usługi sieci Web oparte na tokenach

Po włączeniu uwierzytelniania tokenu dla usługi sieci web użytkownicy muszą przedstawić token sieci Web JSON usługi Azure Machine Learning do usługi sieci web, aby uzyskać do niego dostęp. Token wygasa po określonym przedziale czasu i musi zostać odświeżony, aby kontynuować wykonywanie połączeń.

* Uwierzytelnianie tokenu jest **domyślnie wyłączone** podczas wdrażania w usłudze Azure Kubernetes.
* Uwierzytelnianie **tokenu nie jest obsługiwane** podczas wdrażania w wystąpieniach kontenera platformy Azure.

Aby kontrolować uwierzytelnianie `token_auth_enabled` tokenu, należy użyć parametru podczas tworzenia lub aktualizowania wdrożenia.

Jeśli uwierzytelnianie tokenu jest włączone, można użyć `get_token` tej metody do pobrania tokenu JSON Web Token (JWT) i czasu wygaśnięcia tego tokenu:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Musisz zażądać nowego tokenu po czasie `refresh_by` tokenu. Jeśli trzeba odświeżyć tokeny poza zestawem SDK języka Python, jedną z opcji jest `service.get_token()` użycie interfejsu API REST z uwierzytelnianiem jednostkowym usługi, aby okresowo nawiązać połączenie, jak wspomniano wcześniej.
>
> Zdecydowanie zaleca się utworzenie obszaru roboczego usługi Azure Machine Learning w tym samym regionie co klaster usługi Azure Kubernetes. 
>
> Aby uwierzytelnić się za pomocą tokenu, usługa sieci web nawędnie do regionu, w którym jest tworzony obszar roboczy usługi Azure Machine Learning. Jeśli region obszaru roboczego jest niedostępny, nie będzie można pobrać tokenu dla usługi sieci web, nawet jeśli klaster znajduje się w innym regionie niż obszar roboczy. W rezultacie uwierzytelnianie usługi Azure AD jest niedostępne, dopóki region obszaru roboczego nie będzie ponownie dostępny. 
>
> Ponadto im większa odległość między regionem klastra a regionem obszaru roboczego, tym dłużej trwa pobieranie tokenu.

## <a name="next-steps"></a>Następne kroki

* [Szkolenie i wdrażanie modelu klasyfikacji obrazów](tutorial-train-models-with-aml.md).
* [Korzystanie z modelu usługi Azure Machine Learning wdrożonego jako usługa sieci web.](how-to-consume-web-service.md)
