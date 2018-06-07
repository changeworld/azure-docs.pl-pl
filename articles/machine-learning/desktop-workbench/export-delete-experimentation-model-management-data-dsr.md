---
title: Eksportowanie lub usunąć eksperymenty lub model danych zarządzania — usługi Azure Machine Learning | Dokumentacja firmy Microsoft
description: W usłudze Azure Machine Learning można wyeksportować lub usuwać dane konta związane z zarządzaniem eksperymenty lub modelu z portalu Azure, interfejsu wiersza polecenia, zestaw SDK i uwierzytelnionego interfejsów API REST. W tym artykule opisano sposób.
services: machine-learning
author: cjgronlund
ms.author: cgronlun
manager: haining
ms.reviewer: jmartens, mldocs
ms.service: machine-learning
ms.topic: conceptual
ms.date: 05/22/2018
ms.openlocfilehash: 234c6c535e8a56d894f9e0f7a2f83602b613f5a5
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655605"
---
# <a name="export-or-delete-your-experimentation-or-model-management-data-in-machine-learning"></a>Eksportowanie lub usunąć własnego eksperymenty lub modelu danych zarządzania w uczeniu maszynowym

W usłudze Azure Machine Learning możesz wyeksportować lub usuwać dane konta związane z zarządzaniem eksperymenty lub modelu z uwierzytelnionego interfejsu API REST. W tym artykule wyjaśniono sposób.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-account-data"></a>Kontrolowanie danych konta
W produkcie dane przechowywane przez usługi Azure Machine Learning eksperymentowania oraz modelu zarządzania jest dostępna dla eksportu i usunięcia za pośrednictwem portalu Azure, interfejsu wiersza polecenia, zestaw SDK i uwierzytelnionego interfejsów API REST. Dane telemetryczne są dostępne za pośrednictwem portalu Azure prywatności. 

W usłudze Azure Machine Learning danych osobowych składa się z informacji o użytkowniku w historię wykonywania dokumentów i rejestruje dane telemetryczne niektórych interakcji użytkowników z usługą.

## <a name="delete-account-data-with-the-rest-api"></a>Usuń dane konta przy użyciu interfejsu API REST 

Aby można było usunąć danych, można wprowadzić następujące wywołania interfejsu API z zlecenie HTTP DELETE. Są one autoryzowane przez `Authorization: Bearer <arm-token>` nagłówka w żądaniu, gdzie `<arm-token>` jest token dostępu usługi AAD dla punktu końcowego `https://management.core.windows.net/` punktu końcowego.  

Aby dowiedzieć się, jak uzyskać ten token i wywołać punkty końcowe systemu Azure, zobacz [dokumentacji interfejsu API REST Azure](https://docs.microsoft.com/rest/api/azure/).  

W przykładach po, Zastąp tekst w {} z nazwami wystąpień określające skojarzonych zasobów.

## <a name="delete-from-a-hosting-account"></a>Usuń konto hostingu

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.MachineLearningModelManagement/accounts/{account-name}?api-version=2017-09-01-preview      

## <a name="delete-from-the-model-management-service"></a>Usuń z modelu usługi zarządzania

### <a name="model-document"></a>Wzór dokumentu
Użyj tego wywołania spowoduje wyświetlenie listy modeli i ich identyfikatorów:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models?api-version=2017-09-01-preview"

Poszczególne modele można usunąć z:  

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models/{modelId}?api-version=2017-09-01-preview

### <a name="manifest-document"></a>Dokument manifestu
Użyj tego wywołania spowoduje wyświetlenie listy wszystkich manifestów i ich identyfikatorów:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests?api-version=2017-09-01-preview

Manifesty poszczególnych można usunąć z:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests/{manifestId}?api-version=2017-09-01-preview

### <a name="service-documents"></a>Usługa dokumentów
Użyj tego wywołania spowoduje wyświetlenie listy wszystkich usług i ich identyfikatorów:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services?api-version=2017-09-01-preview

Usługi można usunąć z:    

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{serviceName}?api-version=2017-09-01-preview

### <a name="image-document"></a>Dokument obrazu
Użyj tego wywołania spowoduje wyświetlenie listy wszystkich obrazów i ich identyfikatorów:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images?api-version=2017-09-01-preview

Poszczególne obrazy można usunąć z:  

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images/{imageId}?api-version=2017-09-01-preview

## <a name="delete-run-history-artifact-and-notification-data"></a>Usuń wykonywania dane historii artefaktów i powiadomienia
Uruchom magazyny historii, artefaktów i powiadomień dla projektu są usuwane po usunięciu odpowiednich dokumentu projektu:

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.MachineLearningExperimentation/accounts/{account-name}/workspaces/{workspace-name}/projects/{project-name}?api-version=2017-05-01-preview
    
## <a name="delete-from-experimentation-account-resource-provider"></a>Usuń od dostawcy zasobów konta eksperymenty
Dokumenty projektu są usuwane z:

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.MachineLearningExperimentation/accounts/{account-name}/workspaces/{workspace-name}/projects/{project-name}?api-version=2017-05-01-preview

Obszar roboczy dokumenty są usuwane z:

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.MachineLearningExperimentation/accounts/{account-name}/workspaces/{workspace-name}?api-version=2017-05-01-preview

Konto całego eksperymenty zostało usunięte z:
    
    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.MachineLearningExperimentation/accounts/{account-name}?api-version=2017-05-01-preview


## <a name="export-service-data-with-the-rest-api"></a>Eksportuj dane usługi z interfejsu API REST
Aby wyeksportować dane, można wprowadzić następujące wywołania interfejsu API z zlecenie HTTP GET. Są one autoryzowane przez `Authorization: Bearer <arm-token>` nagłówka w żądaniu, gdzie `<arm-token>` jest token dostępu usługi AAD dla punktu końcowego `https://management.core.windows.net/`  

Aby dowiedzieć się, jak uzyskać ten token i wywołać punkty końcowe systemu Azure, zobacz [dokumentacji interfejsu API REST Azure](https://docs.microsoft.com/rest/api/azure/).   

W przykładach po, Zastąp tekst w {} z nazwami wystąpień określające skojarzonych zasobów.

## <a name="export-hosting-account-data"></a>Eksportuj dane konta hostingu

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningModelManagement/accounts/{accountName}?api-version=2017-09-01-preview     

## <a name="export-model-management-service-data"></a>Eksportuj dane usługi zarządzania modelu
### <a name="model-document"></a>Wzór dokumentu

Użyj tego wywołania spowoduje wyświetlenie listy modeli i ich identyfikatorów:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models?api-version=2017-09-01-preview"

Poszczególne modele można uzyskać przez:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models/{modelId}?api-version=2017-09-01-preview 

### <a name="manifests"></a>Manifesty
Użyj tego wywołania spowoduje wyświetlenie listy wszystkich manifestów i ich identyfikatorów:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests?api-version=2017-09-01-preview

Manifesty poszczególnych można uzyskać przez:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests/{manifestId}?api-version=2017-09-01-preview

### <a name="services"></a>Usługi
Użyj tego wywołania spowoduje wyświetlenie listy wszystkich usług i ich identyfikatorów:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services?api-version=2017-09-01-preview

Poszczególnych usług można uzyskać przez: 

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{serviceName}?api-version=2017-09-01-preview

### <a name="images"></a>Obrazy
Użyj tego wywołania spowoduje wyświetlenie listy wszystkich obrazów i ich identyfikatorów:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images?api-version=2017-09-01-preview

Poszczególnych usług można uzyskać przez: 

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images/{imageId}?api-version=2017-09-01-preview     

## <a name="export-compute-data"></a>Eksportuj dane obliczeń
### <a name="compute-clusters"></a>Klastrów obliczeniowych
Użyj tego wywołania spowoduje wyświetlenie listy wszystkich klastrów obliczeniowych i nazw:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes?api-version=2018-03-01-preview

Poszczególnych klastrów można uzyskać przez:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes/{compute-name}?api-version=2018-03-01-preview

### <a name="operationalization-clusters"></a>Klastry operationalization
Użyj tego wywołania spowoduje wyświetlenie listy wszystkich klastrów i nazw:

    https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.MachineLearningCompute/operationalizationClusters?api-version=2017-06-01-preview

Poszczególnych klastrów można uzyskać przez:

    https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.MachineLearningCompute/operationalizationClusters/{clusterName}?api-version=2017-06-01-preview

## <a name="export-run-history-data"></a>Dane historii uruchamiania eksportu
Użyj tego wywołania spowoduje wyświetlenie listy wszystkich uruchamia i ich identyfikatorów:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runs

Użyj tego wywołania spowoduje wyświetlenie listy wszystkich eksperymentów i ich identyfikatorów:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/experiments

Uruchom elementy można znaleźć w historii:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runs/{runId}

Uruchom metryk, które elementy można uzyskać przez:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runmetrics

Uruchom eksperymenty można uzyskać przez:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/experiments/{experimentId}    

Uruchom artefakty historii:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runs/{runId}/artifacts

Uruchom artefakty historii identyfikatorów URI:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runs/{runId}/artifacturi?name={artifactName}

## <a name="export-artifacts"></a>Artefakty eksportu
Użyj tego wywołania spowoduje wyświetlenie listy zasobów i ich nazwy:

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/assets

Użyj tego wywołania spowoduje wyświetlenie listy artefakty i ich ścieżek:

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/artifacts/origins/{origin}/containers/{runId}
        
        
### <a name="artifact-contents"></a>Zawartość artefaktów

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/artifacts/contentinfo/ExperimentRun/{runId}/{artifactPath}

### <a name="artifact-documents"></a>Dokumenty artefaktów

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runs/{runId}/artifacts
        
### <a name="assets"></a>Elementy zawartości

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/assets/name/{name}

## <a name="export-notifications"></a>Eksportuj powiadomienia

1. Przejdź do [sekcji użytkownicy portalu Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/), a następnie wybierz użytkownika z **nazwa** kolumny. 
2. Uwaga **obiektu o identyfikatorze**i używać go w następujące wywołanie:     

        https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/users/{objectId}/jobs

## <a name="export-experimentation-account-information"></a>Eksportowanie danych konta eksperymenty
### <a name="experimentation-account-information"></a>Informacje o koncie eksperymenty

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}?api-version=2017-05-01-preview
        
### <a name="workspace-information"></a>Informacje o obszarze roboczym

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}?api-version=2017-05-01-preview  

### <a name="project-information"></a>Informacje o projekcie

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}?api-version=2017-05-01-preview
