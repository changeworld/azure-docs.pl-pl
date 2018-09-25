---
title: Eksportowanie lub usuwania eksperymentowanie w usłudze oraz modelu zarządzania danych — usługa Azure Machine Learning | Dokumentacja firmy Microsoft
description: W usłudze Azure Machine Learning można wyeksportować lub usunąć swoje dane konto związane z zarządzaniem eksperymentowania lub modelu przy użyciu witryny Azure portal, interfejsu wiersza polecenia, zestawu SDK i interfejsów API REST usługi uwierzytelnionego. W tym artykule przedstawiono, jak.
services: machine-learning
author: cjgronlund
ms.author: cgronlun
manager: haining
ms.reviewer: jmartens, mldocs
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.date: 05/22/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 7daa3bdf9fb51fee6b0e190625f07e0d14c3d1f4
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46995146"
---
# <a name="export-or-delete-your-experimentation-or-model-management-data-in-machine-learning"></a>Eksportowanie lub usuwanie usługi eksperymentowanie w usłudze lub model danych zarządzania w usłudze Machine Learning

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)]

W usłudze Azure Machine Learning można wyeksportować lub usunąć swoje dane konto związane z zarządzaniem eksperymentowania lub modelu uwierzytelniony interfejs API REST. W tym artykule wyjaśniono sposób.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-account-data"></a>Kontrolowanie danych konta
W ramach produktu — dane przechowywane przez zarządzanie eksperymentowania i modelu usługi Azure Machine Learning jest dostępna dla eksportowania i usuwania za pośrednictwem witryny Azure portal, interfejsu wiersza polecenia, zestaw SDK i uwierzytelnionego interfejsów API REST. Dane telemetryczne są dostępne za pośrednictwem portalu Azure prywatności. 

W usłudze Azure Machine Learning dane osobowe składa się z informacjami o użytkownikach w dokumentach historii uruchamiania i rejestruje dane telemetryczne niektórych interakcji użytkowników z usługą.

## <a name="delete-account-data-with-the-rest-api"></a>Usuwanie danych konta za pomocą interfejsu API REST 

Aby usunąć dane, następujące wywołania interfejsu API może się od zlecenie HTTP DELETE. Są one autoryzowane przez `Authorization: Bearer <arm-token>` nagłówka w żądaniu, gdzie `<arm-token>` jest token dostępu usługi AAD dla punktu końcowego `https://management.core.windows.net/` punktu końcowego.  

Aby dowiedzieć się, jak uzyskać ten token i wywołać punkty końcowe platformy Azure, zobacz [dokumentację interfejsu API REST usługi Azure](https://docs.microsoft.com/rest/api/azure/).  

W przykładach następujące, Zastąp tekst umieszczony w {} przy użyciu nazwy wystąpień, które określają skojarzonego zasobu.

## <a name="delete-from-a-hosting-account"></a>Usuwanie konta hostingu

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.MachineLearningModelManagement/accounts/{account-name}?api-version=2017-09-01-preview      

## <a name="delete-from-the-model-management-service"></a>Usuń z Usługa zarządzania modelami

### <a name="model-document"></a>Dokument modelu
Użyj tego wywołania można pobrać listy modeli i ich identyfikatorów:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models?api-version=2017-09-01-preview"

Poszczególne modele można usunąć za pomocą:  

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models/{modelId}?api-version=2017-09-01-preview

### <a name="manifest-document"></a>Manifest dokumentu
Aby uzyskać listę wszystkich manifestów i ich identyfikatorów, należy użyć tego wywołania:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests?api-version=2017-09-01-preview

Manifesty poszczególnych można usunąć za pomocą:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests/{manifestId}?api-version=2017-09-01-preview

### <a name="service-documents"></a>Dokumenty usługi
Aby uzyskać listę wszystkich usług i ich identyfikatorów, należy użyć tego wywołania:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services?api-version=2017-09-01-preview

Poszczególne usługi można usunąć za pomocą:    

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{serviceName}?api-version=2017-09-01-preview

### <a name="image-document"></a>Dokument obrazu
Aby uzyskać listę wszystkich obrazów i ich identyfikatorów, należy użyć tego wywołania:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images?api-version=2017-09-01-preview

Pojedyncze obrazy można usunąć za pomocą:  

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images/{imageId}?api-version=2017-09-01-preview

## <a name="delete-run-history-artifact-and-notification-data"></a>Usuwanie danych historii, artefaktów i powiadomień uruchomienia
Uruchom magazynów historii, artefaktów i powiadomień dla projektu są usuwane po usunięciu odpowiednich dokument projektu:

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.MachineLearningExperimentation/accounts/{account-name}/workspaces/{workspace-name}/projects/{project-name}?api-version=2017-05-01-preview
    
## <a name="delete-from-experimentation-account-resource-provider"></a>Usuwanie dostawcy zasobów konta eksperymentowania
Dokumenty projektu zostaną usunięte za pomocą:

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.MachineLearningExperimentation/accounts/{account-name}/workspaces/{workspace-name}/projects/{project-name}?api-version=2017-05-01-preview

Obszar roboczy zostaną one usunięte za pomocą:

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.MachineLearningExperimentation/accounts/{account-name}/workspaces/{workspace-name}?api-version=2017-05-01-preview

Konto eksperymentowania całego zostało usunięte za pomocą:
    
    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.MachineLearningExperimentation/accounts/{account-name}?api-version=2017-05-01-preview


## <a name="export-service-data-with-the-rest-api"></a>Eksportowanie danych usługi za pomocą interfejsu API REST
Aby wyeksportować dane, następujące wywołania interfejsu API może się od zlecenie HTTP GET. Są one autoryzowane przez `Authorization: Bearer <arm-token>` nagłówka w żądaniu, gdzie `<arm-token>` jest token dostępu usługi AAD dla punktu końcowego `https://management.core.windows.net/`  

Aby dowiedzieć się, jak uzyskać ten token i wywołać punkty końcowe platformy Azure, zobacz [dokumentację interfejsu API REST usługi Azure](https://docs.microsoft.com/rest/api/azure/).   

W przykładach następujące, Zastąp tekst umieszczony w {} przy użyciu nazwy wystąpień, które określają skojarzonego zasobu.

## <a name="export-hosting-account-data"></a>Eksportuj dane konta hostingu

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningModelManagement/accounts/{accountName}?api-version=2017-09-01-preview     

## <a name="export-model-management-service-data"></a>Eksportowanie modelu zarządzania usługi danych
### <a name="model-document"></a>Dokument modelu

Użyj tego wywołania można pobrać listy modeli i ich identyfikatorów:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models?api-version=2017-09-01-preview"

Poszczególne modele można uzyskać przez:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models/{modelId}?api-version=2017-09-01-preview 

### <a name="manifests"></a>Manifesty
Aby uzyskać listę wszystkich manifestów i ich identyfikatorów, należy użyć tego wywołania:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests?api-version=2017-09-01-preview

Manifesty poszczególnych można uzyskać przez:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests/{manifestId}?api-version=2017-09-01-preview

### <a name="services"></a>Usługi
Aby uzyskać listę wszystkich usług i ich identyfikatorów, należy użyć tego wywołania:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services?api-version=2017-09-01-preview

Poszczególne usługi można uzyskać przez: 

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{serviceName}?api-version=2017-09-01-preview

### <a name="images"></a>Obrazy
Aby uzyskać listę wszystkich obrazów i ich identyfikatorów, należy użyć tego wywołania:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images?api-version=2017-09-01-preview

Poszczególne usługi można uzyskać przez: 

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images/{imageId}?api-version=2017-09-01-preview     

## <a name="export-compute-data"></a>Eksportowanie danych obliczeń
### <a name="compute-clusters"></a>Klastrów obliczeniowych
Użyj tego wywołania, aby uzyskać listę wszystkich klastrów obliczeniowych i ich nazwy:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes?api-version=2018-03-01-preview

Poszczególnych klastrów można uzyskać przez:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes/{compute-name}?api-version=2018-03-01-preview

### <a name="operationalization-clusters"></a>Operacjonalizacja klastrów
Użyj tego wywołania, aby uzyskać listę wszystkich klastrów i ich nazwy:

    https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.MachineLearningCompute/operationalizationClusters?api-version=2017-06-01-preview

Poszczególnych klastrów można uzyskać przez:

    https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.MachineLearningCompute/operationalizationClusters/{clusterName}?api-version=2017-06-01-preview

## <a name="export-run-history-data"></a>Eksportowanie danych historii uruchamiania
Aby uzyskać listę wszystkich przebiegów i ich identyfikatorów, należy użyć tego wywołania:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runs

Aby uzyskać listę wszystkich eksperymentów oraz ich identyfikatorów, należy użyć tego wywołania:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/experiments

Uruchom historii, które elementy można uzyskać przez:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runs/{runId}

Uruchom metryk, które elementy można uzyskać przez:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runmetrics

Wykonywania eksperymentów, można uzyskać przez:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/experiments/{experimentId}    

Uruchom artefaktów historii:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runs/{runId}/artifacts

Uruchom artefaktów Historia identyfikatorów URI:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runs/{runId}/artifacturi?name={artifactName}

## <a name="export-artifacts"></a>Eksportuj artefaktów
Użyj tego wywołania można pobrać listy zasobów i ich nazwy:

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/assets

Użyj tego wywołania, aby uzyskać listę artefaktów i ich ścieżek:

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/artifacts/origins/{origin}/containers/{runId}
        
        
### <a name="artifact-contents"></a>Zawartość artefaktu

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/artifacts/contentinfo/ExperimentRun/{runId}/{artifactPath}

### <a name="artifact-documents"></a>Dokumenty artefaktu

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runs/{runId}/artifacts
        
### <a name="assets"></a>Elementy zawartości

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/assets/name/{name}

## <a name="export-notifications"></a>Eksportowanie powiadomień

1. Przejdź do [sekcji użytkownicy w witrynie Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/), a następnie wybierz użytkownika z **nazwa** kolumny. 
2. Uwaga **obiektu o identyfikatorze**i używać go w następujące wywołanie:     

        https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/users/{objectId}/jobs

## <a name="export-experimentation-account-information"></a>Eksportowanie danych konta eksperymentowania
### <a name="experimentation-account-information"></a>Informacje o koncie eksperymentowania

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}?api-version=2017-05-01-preview
        
### <a name="workspace-information"></a>Informacje o obszarze roboczym

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}?api-version=2017-05-01-preview  

### <a name="project-information"></a>Informacje o projekcie

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}?api-version=2017-05-01-preview
