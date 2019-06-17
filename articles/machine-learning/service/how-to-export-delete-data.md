---
title: Eksportowanie lub usuwanie danych obszaru roboczego
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak wyeksportować lub usunąć obszar roboczy za pomocą witryny Azure portal, interfejsu wiersza polecenia, zestaw SDK i uwierzytelnionego interfejsów API REST.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: ph-com
ms.author: pahusban
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: 3b55282adf56bedb0724eca578b8eaea00c26074
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65023973"
---
# <a name="export-or-delete-your-machine-learning-service-workspace-data"></a>Eksportowanie lub usuwanie danych obszaru roboczego usługi Machine Learning 

W usłudze Azure Machine Learning można eksportować lub usuwania danych obszaru roboczego za pomocą uwierzytelnionych interfejsu API REST. W tym artykule wyjaśniono sposób.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-workspace-data"></a>Kontrolowanie danych obszaru roboczego
W ramach produktu — dane przechowywane przez usługi Azure Machine Learning jest dostępny dla eksportowania i usuwania za pośrednictwem witryny Azure portal, interfejsu wiersza polecenia, zestaw SDK i uwierzytelniony interfejsów API REST. Dane telemetryczne są dostępne za pośrednictwem portalu Azure prywatności. 

W usługach Azure Machine Learning dane osobowe składa się z informacjami o użytkownikach w dokumentach historii uruchamiania i rejestruje dane telemetryczne niektórych interakcji użytkowników z usługą.

## <a name="delete-workspace-data-with-the-rest-api"></a>Usuwanie danych obszaru roboczego za pomocą interfejsu API REST 

Aby usunąć dane, następujące wywołania interfejsu API może się od zlecenie HTTP DELETE. Są one autoryzowane przez `Authorization: Bearer <arm-token>` nagłówka w żądaniu, gdzie `<arm-token>` jest token dostępu usługi AAD dla `https://management.core.windows.net/` punktu końcowego.  

Aby dowiedzieć się, jak uzyskać ten token i wywołać punkty końcowe platformy Azure, zobacz [dokumentację interfejsu API REST usługi Azure](https://docs.microsoft.com/rest/api/azure/).  

W przykładach następujące, Zastąp tekst umieszczony w {} przy użyciu nazwy wystąpień, które określają skojarzonego zasobu.

### <a name="delete-an-entire-workspace"></a>Usuń całego obszaru roboczego

To wywołanie służy do usuwania całego obszaru roboczego.  
> [!WARNING]
> Wszystkie informacje zostaną usunięte i obszar roboczy nie będzie już można używać.

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2018-03-01-preview

### <a name="delete-models"></a>Usuń modele

Użyj tego wywołania można pobrać listy modeli i ich identyfikatorów:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2018-03-01-preview

Poszczególne modele można usunąć za pomocą:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2018-03-01-preview

### <a name="delete-assets"></a>Usuń zasoby

Użyj tego wywołania można pobrać listy zasobów i ich identyfikatorów:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2018-03-01-preview

Poszczególne zasoby można usunąć za pomocą:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2018-03-01-preview

### <a name="delete-images"></a>Usuwanie obrazów

Użyj tego wywołania można pobrać listy obrazów i ich identyfikatorów:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2018-03-01-preview

Pojedyncze obrazy można usunąć za pomocą:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2018-03-01-preview

### <a name="delete-services"></a>Usuwanie usług

Użyj tego wywołania, aby pobrać listę usług i ich identyfikatorów:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2018-03-01-preview

Poszczególne usługi można usunąć za pomocą:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2018-03-01-preview

## <a name="export-service-data-with-the-rest-api"></a>Eksportowanie danych usługi za pomocą interfejsu API REST

Aby wyeksportować dane, następujące wywołania interfejsu API może się od zlecenie HTTP GET. Są one autoryzowane przez `Authorization: Bearer <arm-token>` nagłówka w żądaniu, gdzie `<arm-token>` jest token dostępu usługi AAD dla punktu końcowego `https://management.core.windows.net/`  

Aby dowiedzieć się, jak uzyskać ten token i wywołać punkty końcowe platformy Azure, zobacz [dokumentację interfejsu API REST usługi Azure](https://docs.microsoft.com/rest/api/azure/).   

W przykładach następujące, Zastąp tekst umieszczony w {} przy użyciu nazwy wystąpień, które określają skojarzonego zasobu.

### <a name="export-workspace-information"></a>Informacje o obszarze roboczym eksportu

Użyj tego wywołania w celu uzyskania listy wszystkich obszarów roboczych:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces?api-version=2018-03-01-preview

Informacje o danym obszarze roboczym można uzyskać przez:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2018-03-01-preview

### <a name="export-compute-information"></a>Eksportowanie informacji obliczeń

Wszystkie zasoby obliczeniowe że będzie można uzyskać przez obiekty docelowe, dołączone do obszaru roboczego:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes?api-version=2018-03-01-preview

Informacje o jednym obliczeniowego elementu docelowego można uzyskać przez:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes/{computeName}?api-version=2018-03-01-preview

### <a name="export-run-history-data"></a>Eksportowanie danych historii uruchamiania
Użyj tego wywołania w celu uzyskania listy wszystkich eksperymentów oraz informacjami:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments 

Wszystkie przebiegi do konkretnego eksperymentu można uzyskać przez:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs 

Uruchom historii, które elementy można uzyskać przez:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs/{runId} 

Wszystkie metryki uruchamiania eksperymentu można uzyskać przez:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics 

Pojedynczą metryką przebiegu można uzyskać przez:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics/{metricId}
    
### <a name="export-artifacts"></a>Eksportuj artefaktów

Użyj tego wywołania, aby uzyskać listę artefaktów i ich ścieżek:

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/artifacts/origins/ExperimentRun/containers/{runId}
    
### <a name="export-notifications"></a>Eksportowanie powiadomień

Użyj tego wywołania w celu uzyskania listy zadań przechowywanych:     

    https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/tasks

Powiadomienia dla pojedynczego zadania można uzyskać przez:

    https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}tasks/{taskId}

### <a name="export-data-stores"></a>Magazyny danych eksportu

Aby uzyskać listę magazynów danych, użyj tego wywołania:

    https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores

Magazyny danych można uzyskać przez:

    https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores/{name}

### <a name="export-models"></a>Eksportuj modeli

Użyj tego wywołania można pobrać listy modeli i ich identyfikatorów:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2018-03-01-preview

Poszczególne modele można uzyskać przez:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2018-03-01-preview

### <a name="export-assets"></a>Eksportowanie zasobów

Użyj tego wywołania można pobrać listy zasobów i ich identyfikatorów:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2018-03-01-preview

Poszczególne zasoby można uzyskać przez:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2018-03-01-preview

### <a name="export-images"></a>Eksportowanie obrazów

Użyj tego wywołania można pobrać listy obrazów i ich identyfikatorów:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2018-03-01-preview

Pojedyncze obrazy można uzyskać przez:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2018-03-01-preview

### <a name="export-services"></a>Eksportuj usług

Użyj tego wywołania, aby pobrać listę usług i ich identyfikatorów:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2018-03-01-preview

Poszczególne usługi można uzyskać przez:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2018-03-01-preview

### <a name="export-pipeline-experiments"></a>Eksportuj eksperymentów potoku

Poszczególne eksperymenty można uzyskać przez:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Experiments/{experimentId}

### <a name="export-pipeline-graphs"></a>Eksportuj wykresy potoku

Poszczególne wykresy można uzyskać przez:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Graphs/{graphId}

### <a name="export-pipeline-modules"></a>Eksportuj modułów potoku

Moduły można uzyskać przez:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Modules/{id}

### <a name="export-pipeline-templates"></a>Eksportowanie szablonów procesu

Szablony można uzyskać przez:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Templates/{templateId}

### <a name="export-pipeline-data-sources"></a>Eksportowanie źródła danych w potoku

Źródła danych można uzyskać przez:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/DataSources/{id}

## <a name="delete-visual-interface-assets"></a>Usuń interfejs graficzny zasobów

W interfejs graficzny, w której utworzono eksperymentu usuwać poszczególne zasoby:

1. Po lewej stronie wybierz typ zasobów, które chcesz usunąć.

    ![Usuń zasoby](media/how-to-export-delete-data.md/delete-experiment.png)

1. Na liście wybierz poszczególne zasoby można usunąć.

1. Na dole, zaznacz **Usuń**.

## <a name="export-visual-interface-data"></a>Eksportowanie danych interfejsu wizualnego

W interfejs graficzny, w której utworzono eksperymentu należy wyeksportować dane, które zostały dodane:

1. Po lewej stronie, wybierz **danych**.

1. Na górze, wybierz **Moje zestawy danych** lub **przykłady** do lokalizowania danych, którą chcesz wyeksportować.

    ![Pobieranie danych](media/how-to-export-delete-data.md/download-data.png)

1. Na liście wybierz poszczególne zestawy danych do wyeksportowania.

1. Na dole, zaznacz **Pobierz**.