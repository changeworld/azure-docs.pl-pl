---
title: Zarządzanie zasobami ml za pomocą funkcji REST
titleSuffix: Azure Machine Learning
description: Jak tworzyć, uruchamiać i usuwać zasoby usługi Azure ML za pomocą interfejsów API REST
author: lobrien
ms.author: laobri
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 01/31/2020
ms.openlocfilehash: 419dbd998abc5cbd2da64a990e13d46f3fb2efbe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77580632"
---
# <a name="create-run-and-delete-azure-ml-resources-using-rest"></a>Tworzenie, uruchamianie i usuwanie zasobów usługi Azure ML przy użyciu funkcji REST

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Istnieje kilka sposobów zarządzania zasobami usługi Azure ML. Można użyć [portalu](https://portal.azure.com/), [interfejsu wiersza polecenia](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)lub [SDK języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). Można też wybrać interfejs API REST. Interfejs API REST używa zleceń HTTP w standardowy sposób do tworzenia, pobierania, aktualizowania i usuwania zasobów. Interfejs API REST działa z dowolnym językiem lub narzędziem, które może tworzyć żądania HTTP. Prosta struktura REST często sprawia, że jest to dobry wybór w środowiskach skryptów i automatyzacji MLOps. 

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Pobieranie tokenu autoryzacji
> * Tworzenie poprawnie sformatowanego żądania REST przy użyciu uwierzytelniania głównego usługi
> * Pobieranie informacji o hierarchicznych zasobach usługi Azure ML za pomocą żądań GET
> * Tworzenie i modyfikowanie zasobów za pomocą żądań PUT i POST
> * Oczyszczanie zasobów za pomocą żądań DELETE 
> * Używanie autoryzacji opartej na kluczach do oceniania wdrożonych modeli

## <a name="prerequisites"></a>Wymagania wstępne

- **Subskrypcja platformy Azure,** dla której masz prawa administracyjne. Jeśli nie masz takiej subskrypcji, wypróbuj [bezpłatną lub płatną subskrypcję osobistą](https://aka.ms/AMLFree)
- [Obszar roboczy usługi Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace)
- Żądania REST administracyjne używają uwierzytelniania głównego usługi. Wykonaj kroki opisane w obszarze [Konfigurowanie uwierzytelniania dla zasobów i przepływów pracy usługi Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-setup-authentication#set-up-service-principal-authentication) w celu utworzenia jednostki usługi w obszarze roboczym
- Narzędzie **curl.** Program **curl** jest dostępny w [podsystemie Windows dla systemu Linux](https://aka.ms/wslinstall/) lub w dowolnej dystrybucji SYSTEMU UNIX. W programie PowerShell **curl** jest aliasem **invoke-WebRequest** i `curl -d "key=val" -X POST uri` staje się `Invoke-WebRequest -Body "key=val" -Method POST -Uri uri`. 

## <a name="retrieve-a-service-principal-authentication-token"></a>Pobieranie tokenu uwierzytelniania jednostki usługi

Administracyjne żądania REST są uwierzytelniane z przepływem niejawnym OAuth2. Ten przepływ uwierzytelniania używa tokenu dostarczonego przez jednostkę usługi subskrypcji. Aby pobrać ten token, musisz:

- Identyfikator dzierżawy (identyfikujący organizację, do której należy twoja subskrypcja)
- Identyfikator klienta (który będzie skojarzony z utworzonym tokenem)
- Twój sekret klienta (który powinieneś chronić)

Te wartości powinny być dostępne od odpowiedzi na utworzenie jednostki usługi, zgodnie z omówieniem w [artykule Konfigurowanie uwierzytelniania dla zasobów i przepływów pracy usługi Azure Machine Learning.](https://docs.microsoft.com/azure/machine-learning/how-to-setup-authentication#set-up-service-principal-authentication) Jeśli używasz subskrypcji firmy, możesz nie mieć uprawnień do tworzenia jednostki usługi. W takim przypadku należy skorzystać z [bezpłatnej lub płatnej subskrypcji osobistej.](https://aka.ms/AMLFree)

Aby pobrać token:

1. Otwórz okno terminalu
1. Wprowadź następujący kod w wierszu polecenia
1. Zastąp `{your-tenant-id}`własne `{your-client-id}`wartości `{your-client-secret}`dla , i . W tym artykule ciągi otoczone nawiasami klamrowymi są zmiennymi, które musisz zastąpić własnymi odpowiednimi wartościami.
1. Uruchamianie polecenia

```bash
curl -X POST https://login.microsoftonline.com/{your-tenant-id}/oauth2/token \
-d "grant_type=client_credentials&resource=https%3A%2F%2Fmanagement.azure.com%2F&client_id={your-client-id}&client_secret={your-client-secret}" \
```

Odpowiedź powinna zapewnić token dostępu dobry dla jednej godziny:

```json
{
    "token_type": "Bearer",
    "expires_in": "3599",
    "ext_expires_in": "3599",
    "expires_on": "1578523094",
    "not_before": "1578519194",
    "resource": "https://management.azure.com/",
    "access_token": "your-access-token"
}
```

Zanotuj token, ponieważ użyjesz go do uwierzytelnienia wszystkich kolejnych żądań administracyjnych. Zrobisz to, ustawiając nagłówek autoryzacji we wszystkich żądaniach:

```bash
curl -h "Authentication: Bearer {your-access-token}" ...more args...
```

Należy zauważyć, że wartość zaczyna się od ciągu "Bearer", w tym pojedynczego miejsca przed dodaniem tokenu.

## <a name="get-a-list-of-resource-groups-associated-with-your-subscription"></a>Wyświetlanie listy grup zasobów skojarzonych z subskrypcją

Aby pobrać listę grup zasobów skojarzonych z subskrypcją, uruchom:

```bash
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups?api-version=2019-11-01 -H "Authorization:Bearer {your-access-token}"
```

Na platformie Azure publikowanych jest wiele interfejsów API REST. Każdy dostawca usług aktualizuje swój interfejs API na własną kadencję, ale robi to bez przerywania istniejących programów. Dostawca usług używa `api-version` argumentu, aby zapewnić zgodność. Argument `api-version` różni się w zależności od usługi. Na przykład w przypadku usługi uczenia maszynowego `2019-11-01`bieżąca wersja interfejsu API to . W przypadku kont magazynu `2019-06-01`jest to . W przypadku magazynów kluczy jest `2019-09-01`to . Wszystkie wywołania REST `api-version` należy ustawić argument na wartość oczekiwaną. Można polegać na składni i semantyki określonej wersji, nawet w interfejsie API w dalszym ciągu ewoluować. Jeśli wyślesz żądanie do dostawcy `api-version` bez argumentu, odpowiedź będzie zawierać czytelną dla człowieka listę obsługiwanych wartości. 

Powyższe wywołanie spowoduje zagęszczenie odpowiedzi JSON formularza: 

```json
{
    "value": [
        {
            "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/RG1",
            "name": "RG1",
            "type": "Microsoft.Resources/resourceGroups",
            "location": "westus2",
            "properties": {
                "provisioningState": "Succeeded"
            }
        },
        {
            "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/RG2",
            "name": "RG2",
            "type": "Microsoft.Resources/resourceGroups",
            "location": "eastus",
            "properties": {
                "provisioningState": "Succeeded"
            }
        }
    ]
}
```


## <a name="drill-down-into-workspaces-and-their-resources"></a>Przechodzenie do szczegółów obszarów roboczych i ich zasobów

Aby pobrać zestaw obszarów roboczych w grupie zasobów, uruchom następujące `{your-subscription-id}` `{your-resource-group}`czynności, `{your-access-token}`zastępując , i: 

```
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Ponownie otrzymasz listę JSON, tym razem zawierającą listę, z których każdy element zawiera szczegółowe informacje o obszarze roboczym:

```json
{
    "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/DeepLearningResourceGroup/providers/Microsoft.MachineLearningServices/workspaces/my-workspace",
    "name": "my-workspace",
    "type": "Microsoft.MachineLearningServices/workspaces",
    "location": "centralus",
    "tags": {},
    "etag": null,
    "properties": {
        "friendlyName": "",
        "description": "",
        "creationTime": "2020-01-03T19:56:09.7588299+00:00",
        "storageAccount": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.storage/storageaccounts/myworkspace0275623111",
        "containerRegistry": null,
        "keyVault": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.keyvault/vaults/myworkspace2525649324",
        "applicationInsights": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.insights/components/myworkspace2053523719",
        "hbiWorkspace": false,
        "workspaceId": "cba12345-abab-abab-abab-ababab123456",
        "subscriptionState": null,
        "subscriptionStatusChangeTimeStampUtc": null,
        "discoveryUrl": "https://centralus.experiments.azureml.net/discovery"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "abcdef1-abab-1234-1234-abababab123456",
        "tenantId": "1fedcba-abab-1234-1234-abababab123456"
    },
    "sku": {
        "name": "Basic",
        "tier": "Basic"
    }
}
```

Aby pracować z zasobami w obszarze roboczym, przełączysz się z ogólnego serwera **management.azure.com** na serwer interfejsu API REST specyficzny dla lokalizacji obszaru roboczego. Zanotuj `discoveryUrl` wartość klucza w powyższej odpowiedzi JSON. Jeśli otrzymasz ten adres URL, otrzymasz odpowiedź w stylu:

```json
{
  "api": "https://centralus.api.azureml.ms",
  "catalog": "https://catalog.cortanaanalytics.com",
  "experimentation": "https://centralus.experiments.azureml.net",
  "gallery": "https://gallery.cortanaintelligence.com/project",
  "history": "https://centralus.experiments.azureml.net",
  "hyperdrive": "https://centralus.experiments.azureml.net",
  "labeling": "https://centralus.experiments.azureml.net",
  "modelmanagement": "https://centralus.modelmanagement.azureml.net",
  "pipelines": "https://centralus.aether.ms",
  "studiocoreservices": "https://centralus.studioservice.azureml.com"
}
```

Wartość `api` odpowiedzi jest adres URL serwera, który będzie używany do dodatkowych żądań. Aby wyświetlić listę eksperymentów, na przykład wyślij następujące polecenie. Zastąp `regional-api-server` wartością `api` odpowiedzi (na przykład ). `centralus.api.azureml.ms` Również `your-subscription-id`zastąpić `your-resource-group` `your-workspace-name`, `your-access-token` , i jak zwykle:

```bash
curl https://{regional-api-server}/history/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Podobnie, aby pobrać zarejestrowane modele w obszarze roboczym, wyślij:

```bash
curl https://{regional-api-server}/modelmanagement/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/models?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Należy zauważyć, że do listy `history/v1.0` eksperymentów ścieżka zaczyna się `modelmanagement/v1.0`podczas listy modeli, ścieżka zaczyna się od . Interfejs API REST jest podzielony na kilka grup operacyjnych, z których każda ma odrębną ścieżkę. Dokumenty odwołania interfejsu API w poniższych łączach wyświetlają listę operacji, parametrów i kodów odpowiedzi dla różnych operacji.

|Obszar|Ścieżka|Tematy pomocy|
|-|-|-|
|Artefakty|artefakt/v2.0/|[Dokumentacja interfejsu API REST](https://docs.microsoft.com/rest/api/azureml/artifacts)|
|Magazyny danych|magazyn danych/v1.0/|[Dokumentacja interfejsu API REST](https://docs.microsoft.com/rest/api/azureml/datastores)|
|Dostrajanie hiperparametrów|hyperdrive/v1.0/|[Dokumentacja interfejsu API REST](https://docs.microsoft.com/rest/api/azureml/hyperparametertuning)|
|Modele|modelmanagement/v1.0/|[Dokumentacja interfejsu API REST](https://docs.microsoft.com/rest/api/azureml/modelsanddeployments/mlmodels)|
|Historia uruchamiania|wykonanie/v1.0/ i historia/v1.0/|[Dokumentacja interfejsu API REST](https://docs.microsoft.com/rest/api/azureml/runs)|

Interfejs API REST można eksplorować przy użyciu ogólnego wzorca:

|Składnik adresu URL|Przykład|
|-|-|
| https://| |
| regional-api-server/ | centralus.api.azureml.ms/ |
| operations-path/ | historia/v1.0/ |
| subskrypcje/{twój identyfikator subskrypcji}/ | subskrypcje/abcde123-abab-abab-1234-0123456789abc/ |
| resourceGroups/{your-resource-group}/ | resourceGroups/MyResourceGroup/ |
| dostawców/dostawcy operacji/ | dostawców/Microsoft.MachineLearningServices/ |
| provider-resource-path/ | obszary robocze/MLWorkspace/MyWorkspace/FirstExperiment/runs/1/ |
| operacyjno-punkt końcowy/ | artefakty/metadane/ |


## <a name="create-and-modify-resources-using-put-and-post-requests"></a>Tworzenie i modyfikowanie zasobów przy użyciu żądań PUT i POST

Oprócz pobierania zasobów za pomocą zlecenia GET interfejs API REST obsługuje tworzenie wszystkich zasobów niezbędnych do uczenia, wdrażania i monitorowania rozwiązań ML. 

Szkolenia i uruchamianie modeli uczenia maszynowego wymagają zasobów obliczeniowych. Zasoby obliczeniowe obszaru roboczego można wyświetlić za pomocą: 

```bash
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/computes?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Aby utworzyć lub zastąpić nazwany zasób obliczeniowy, użyjesz żądania PUT. Poniżej dalsza część, oprócz znanych już `your-subscription-id`zamienników `your-resource-group` `your-workspace-name`, `your-access-token`, `your-compute-name`i , `location`zastąpić `vmSize` `vmPriority`, `scaleSettings` `adminUserName`i `adminUserPassword`wartości , , , , , i . Jak określono w odwołaniu w [machine learning compute — tworzenie lub aktualizowanie odwołania SDK,](https://docs.microsoft.com/rest/api/azureml/workspacesandcomputes/machinelearningcompute/createorupdate)następujące polecenie tworzy dedykowany, jednowęzłowy Standard_D1 (podstawowy zasób obliczeniowy procesora CPU), który będzie skalowany w dół po 30 minutach:

```bash
curl -X PUT \
  'https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/computes/{your-compute-name}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}' \
  -H 'Content-Type: application/json' \
  -d '{
    "location": "{your-azure-location}",
    "properties": {
        "computeType": "AmlCompute",
        "properties": {
            "vmSize": "Standard_D1",
            "vmPriority": "Dedicated",
            "scaleSettings": {
                "maxNodeCount": 1,
                "minNodeCount": 0,
                "nodeIdleTimeBeforeScaleDown": "PT30M"
            }
        }
    },
    "userAccountCredentials": {
        "adminUserName": "{adminUserName}",
        "adminUserPassword": "{adminUserPassword}"
    }
}'
```

> [!Note]
> W terminalach systemu Windows może być trzeba uciec symbole podwójnego cudzysłowu podczas wysyłania danych JSON. Oznacza to, że `"location"` `\"location\"`tekst, taki jak staje się . 

Pomyślne żądanie otrzyma `201 Created` odpowiedź, ale należy pamiętać, że ta odpowiedź oznacza po prostu, że rozpoczął się proces inicjowania obsługi administracyjnej. Aby potwierdzić jego pomyślne ukończenie, musisz sondować (lub korzystać z portalu).

### <a name="create-an-experimental-run"></a>Tworzenie przebiegu eksperymentalnego

Aby rozpocząć uruchamianie w ramach eksperymentu, potrzebny jest folder zip zawierający skrypt szkoleniowy i powiązane pliki oraz plik JSON definicji uruchamiania. Folder zip musi mieć plik wpisu Języka Python w katalogu głównym. Na przykład skompresuj trywialny program Pythona, taki jak następujący, do folderu o nazwie **train.zip**.

```python
# hello.py
# Entry file for run
print("Hello, REST!")
```

Zapisz ten następny fragment kodu jako **definition.json**. Potwierdź wartość "Skrypt" pasuje do nazwy pliku Pythona, który właśnie spakowałeś. Potwierdź, że wartość "Target" jest zgodna z nazwą dostępnego zasobu obliczeniowego. 

```json
{
    "Configuration":{  
       "Script":"hello.py",
       "Arguments":[  
          "234"
       ],
       "SourceDirectoryDataStore":null,
       "Framework":"Python",
       "Communicator":"None",
       "Target":"cpu-compute",
       "MaxRunDurationSeconds":1200,
       "NodeCount":1,
       "Environment":{  
          "Python":{  
             "InterpreterPath":"python",
             "UserManagedDependencies":false,
             "CondaDependencies":{  
                "name":"project_environment",
                "dependencies":[  
                   "python=3.6.2",
                   {  
                      "pip":[  
                         "azureml-defaults"
                      ]
                   }
                ]
             }
          },
          "Docker":{  
             "BaseImage":"mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04"
          }
      },
       "History":{  
          "OutputCollection":true
       }
    }
}
```

Opublikuj te pliki na `multipart/form-data` serwerze przy użyciu zawartości:

```bash
curl https://{regional-api-server}/execution/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments/{your-experiment-name}/startrun?api-version=2019-11-01 \
  -X POST \
  -H "Content-Type: multipart/form-data" \
  -H "Authorization:Bearer {your-access-token}" \
  -F projectZipFile=@train.zip \
  -F runDefinitionFile=@runDefinition.json
```

Pomyślne żądanie POST `200 OK` wygeneruje stan, z treścią odpowiedzi zawierającą identyfikator utworzonego przebiegu:

```json
{
  "runId": "my-first-experiment_1579642222877"
}
```

Można monitorować przebieg przy użyciu rest-ful wzorca, który powinien być teraz znany:

```bash
curl 'https://{regional-api-server}/history/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments/{your-experiment-names}/runs/{your-run-id}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}'
```

### <a name="delete-resources-you-no-longer-need"></a>Usuwanie zasobów, które nie są już potrzebne

Niektóre, ale nie wszystkie, zasoby obsługują czasownik DELETE. Sprawdź [odwołanie interfejsu API](https://docs.microsoft.com/rest/api/azureml/) przed zatwierdzeniem do interfejsu API REST dla przypadków użycia usuwania. Aby usunąć model, można na przykład użyć:

```bash
curl
  -X DELETE \
'https://{regional-api-server}/modelmanagement/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/models/{your-model-id}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}' 
```

## <a name="use-rest-to-score-a-deployed-model"></a>Użyj restunij, aby uzyskać wdrożony model

Chociaż możliwe jest wdrożenie modelu, tak aby uwierzytelnił się za pomocą jednostki usługi, większość wdrożeń opartych na kliencie używać uwierzytelniania opartego na kluczach. Odpowiedni klucz można znaleźć na stronie wdrożenia na karcie **Punkty końcowe** programu Studio. W tej samej lokalizacji zostanie wyświetlenie identyfikatora URI oceniania punktu końcowego. Dane wejściowe modelu muszą być modelowane jako `data`tablica JSON o nazwie:

```bash
curl 'https://{scoring-uri}' \
 -H 'Authorization:Bearer {your-key}' \
 -H 'Content-Type: application/json' \
  -d '{ "data" : [ {model-specific-data-structure} ] }
```

## <a name="create-a-workspace-using-rest"></a>Tworzenie obszaru roboczego przy użyciu funkcji REST 

Każdy obszar roboczy usługi Azure ML ma zależność od czterech innych zasobów platformy Azure: rejestru kontenerów z włączoną administracją, magazynu kluczy, zasobu usługi Application Insights i konta magazynu. Nie można utworzyć obszaru roboczego, dopóki te zasoby nie istnieją. Szczegółowe informacje na temat tworzenia każdego takiego zasobu można znaleźć w odwołaniu do interfejsu API REST.

Aby utworzyć obszar roboczy, umieść wywołanie `management.azure.com`podobne do następującego do . Podczas tego wywołania wymaga, aby ustawić dużą liczbę zmiennych, jest strukturalnie identyczne z innymi wywołaniami, które w tym artykule omówione. 

```bash
curl -X PUT \
  'https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}\
/providers/Microsoft.MachineLearningServices/workspaces/{your-new-workspace-name}?api-version=2019-11-01' \
  -H 'Authorization: Bearer {your-access-token}' \
  -H 'Content-Type: application/json' \
  -d '{
    "location": "{desired-region}",
    "properties": {
        "friendlyName" : "{your-workspace-friendly-name}",
        "description" : "{your-workspace-description}",
        "containerRegistry" : "/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.ContainerRegistry/registries/{your-registry-name}",
        keyVault" : "/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}\
/providers/Microsoft.Keyvault/vaults/{your-keyvault-name}",
        "applicationInsights" : "subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.insights/components/{your-application-insights-name}",
        "storageAccount" : "/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.Storage/storageAccounts/{your-storage-account-name}"
    },
    "identity" : {
        "type" : "systemAssigned"
    }
}'
```

Powinieneś otrzymać `202 Accepted` odpowiedź i, w zwracanych `Location` nagłówkach, identyfikator URI. Można uzyskać ten identyfikator URI dla informacji na temat wdrożenia, w tym przydatne informacje debugowania, jeśli występuje problem z jednym z zasobów zależnych (na przykład, jeśli zapomniałeś włączyć dostęp administratora w rejestrze kontenerów). 

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="resource-provider-errors"></a>Błędy dostawcy zasobów

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>Przenoszenie obszaru roboczego

> [!WARNING]
> Przenoszenie obszaru roboczego usługi Azure Machine Learning do innej subskrypcji lub przenoszenie subskrypcji właścicielskiej do nowej dzierżawy nie jest obsługiwane. Może to spowodować błędy.

### <a name="deleting-the-azure-container-registry"></a>Usuwanie rejestru kontenerów platformy Azure

Obszar roboczy usługi Azure Machine Learning używa usługi Azure Container Registry (ACR) dla niektórych operacji. Automatycznie utworzy wystąpienie ACR, gdy po raz pierwszy będzie potrzebne.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z pełnym [odwołaniem do interfejsu API REST usługi AzureML.](https://docs.microsoft.com/rest/api/azureml/)
- Dowiedz się, jak używać Studio & Designer do [przewidywania ceny samochodu z projektantem (wersja zapoznawcza)](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-train-score).
- Poznaj [usługę Azure Machine Learning za pomocą notesów Jupyter.](https://docs.microsoft.com/azure//machine-learning/samples-notebooks)
