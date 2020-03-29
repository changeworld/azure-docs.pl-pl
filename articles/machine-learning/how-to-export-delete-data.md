---
title: Eksportowanie lub usuwanie danych obszaru roboczego
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak wyeksportować lub usunąć obszar roboczy za pomocą studia usługi Azure Machine Learning, interfejsu wiersza polecenia, sdk i uwierzytelnionych interfejsów API REST.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: lobrien
ms.author: laobri
ms.date: 03/06/2020
ms.custom: seodec18
ms.openlocfilehash: 4abef0146b4bf0cfaa254d196b0ca68f0d8ac883
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218284"
---
# <a name="export-or-delete-your-machine-learning-service-workspace-data"></a>Eksportowanie lub usuwanie danych obszaru roboczego usługi uczenia maszynowego

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W usłudze Azure Machine Learning można eksportować lub usuwać dane obszaru roboczego za pomocą uwierzytelnionego interfejsu API REST. W tym artykule dowiesz się, jak to zrobić.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-workspace-data"></a>Sterowanie danymi obszaru roboczego

Dane w produkcie przechowywane przez usługę Azure Machine Learning są dostępne do eksportowania i usuwania za pośrednictwem usługi Azure Machine Learning studio, interfejsu wiersza polecenia, zestawu SDK i uwierzytelnionych interfejsów API REST. Dane telemetryczne są dostępne za pośrednictwem portalu prywatności platformy Azure. 

W usłudze Azure Machine Learning dane osobowe składają się z informacji o użytkowniku w dokumentach historii uruchamiania i rekordach telemetrycznych niektórych interakcji użytkownika z usługą.

## <a name="delete-workspace-data-with-the-rest-api"></a>Usuwanie danych obszaru roboczego za pomocą interfejsu API REST

Aby usunąć dane, można wykonywać następujące wywołania interfejsu API za pomocą zlecenia HTTP DELETE. Są one autoryzowane przez `Authorization: Bearer <arm-token>` nagłówek w żądaniu, gdzie `<arm-token>` jest token `https://management.core.windows.net/` dostępu AAD dla punktu końcowego.  

Aby dowiedzieć się, jak uzyskać ten token i wywołać punkty końcowe platformy Azure, zobacz [Zarządzanie zasobami ml oraz](how-to-manage-rest.md) [dokumentacja interfejsu API usługi Azure REST.](https://docs.microsoft.com/rest/api/azure/)  

W poniższych przykładach {} zastąp tekst nazwami instancji, które określają skojarzony zasób.

### <a name="delete-an-entire-workspace"></a>Usuwanie całego obszaru roboczego

To wywołanie służy do usuwania całego obszaru roboczego.  
> [!WARNING]
> Wszystkie informacje zostaną usunięte, a obszar roboczy nie będzie już użyteczny.

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2019-11-01

### <a name="delete-models"></a>Usuwanie modeli

Użyj tego wywołania, aby uzyskać listę modeli i ich identyfikatory:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2019-11-01

Poszczególne modele można usunąć za pomocą:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2019-11-01

### <a name="delete-assets"></a>Usuwanie zasobów

Użyj tego wywołania, aby uzyskać listę zasobów i ich identyfikatory:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2019-11-01

Poszczególne zasoby można usunąć za pomocą:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2019-11-01

### <a name="delete-images"></a>Usuwanie obrazów

Użyj tego połączenia, aby uzyskać listę obrazów i ich identyfikatory:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2019-11-01

Poszczególne obrazy można usunąć za pomocą:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2019-11-01

### <a name="delete-services"></a>Usuwanie usług

Użyj tego połączenia, aby uzyskać listę usług i ich identyfikatory:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2019-11-01

Poszczególne usługi można usunąć za pomocą:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2019-11-01

## <a name="export-service-data-with-the-rest-api"></a>Eksportowanie danych usługi za pomocą interfejsu API REST

W celu wyeksportowania danych można wykonać następujące wywołania interfejsu API za pomocą zlecenia HTTP GET. Są one autoryzowane przez `Authorization: Bearer <arm-token>` nagłówek w żądaniu, gdzie `<arm-token>` jest token dostępu AAD dla punktu końcowego`https://management.core.windows.net/`  

Aby dowiedzieć się, jak uzyskać ten token i wywołać punkty końcowe platformy Azure, zobacz [Zarządzanie zasobami ml i dokumentacją](how-to-manage-rest.md) [interfejsu API usługi Azure REST](https://docs.microsoft.com/rest/api/azure/)..   

W poniższych przykładach {} zastąp tekst nazwami instancji, które określają skojarzony zasób.

### <a name="export-workspace-information"></a>Eksportowanie informacji o obszarze roboczym

Użyj tego wywołania, aby uzyskać listę wszystkich obszarów roboczych:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces?api-version=2019-11-01

Informacje o poszczególnych obszarach roboczych można uzyskać za pomocą:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2019-11-01

### <a name="export-compute-information"></a>Eksportowanie informacji o obliczeniach

Wszystkie obiekty docelowe obliczeń dołączone do obszaru roboczego można uzyskać za pomocą:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes?api-version=2019-11-01

Informacje o pojedynczym celu obliczeniowym można uzyskać poprzez:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes/{computeName}?api-version=2019-11-01

### <a name="export-run-history-data"></a>Eksportowanie danych historii uruchamiania

Użyj tego wywołania, aby uzyskać listę wszystkich eksperymentów i ich informacji:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments 

Wszystkie przebiegi dla danego eksperymentu można uzyskać poprzez:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs 

Elementy historii uruchamiania można uzyskać poprzez:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs/{runId} 

Wszystkie dane dotyczące przebiegu eksperymentu można uzyskać za pomocą:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics 

Metrykę pojedynczego uruchomienia można uzyskać za pomocą:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics/{metricId}

### <a name="export-artifacts"></a>Eksportowanie artefaktów

Użyj tego wywołania, aby uzyskać listę artefaktów i ich ścieżki:

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/artifacts/origins/ExperimentRun/containers/{runId}

### <a name="export-notifications"></a>Powiadomienia o wywozie

Użyj tego wywołania, aby uzyskać listę przechowywanych zadań:

    https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/tasks

Powiadomienia o jednym zadaniu można uzyskać poprzez:

    https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}tasks/{taskId}

### <a name="export-data-stores"></a>Eksportowanie magazynów danych

Użyj tego wywołania, aby uzyskać listę magazynów danych:

    https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores

Poszczególne magazyny danych można uzyskać poprzez:

    https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores/{name}

### <a name="export-models"></a>Eksportowanie modeli

Użyj tego wywołania, aby uzyskać listę modeli i ich identyfikatory:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2019-11-01

Poszczególne modele można uzyskać poprzez:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2019-11-01

### <a name="export-assets"></a>Eksportowanie zasobów

Użyj tego wywołania, aby uzyskać listę zasobów i ich identyfikatory:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2019-11-01

Poszczególne aktywa można uzyskać poprzez:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2019-11-01

### <a name="export-images"></a>Eksportowanie obrazów

Użyj tego połączenia, aby uzyskać listę obrazów i ich identyfikatory:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2019-11-01

Poszczególne obrazy można uzyskać poprzez:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2019-11-01

### <a name="export-services"></a>Usługi eksportowe

Użyj tego połączenia, aby uzyskać listę usług i ich identyfikatory:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2019-11-01

Poszczególne usługi można uzyskać poprzez:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2019-11-01

### <a name="export-pipeline-experiments"></a>Eksportowanie eksperymentów potoku

Indywidualne eksperymenty można uzyskać poprzez:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Experiments/{experimentId}

### <a name="export-pipeline-graphs"></a>Eksportowanie wykresów potoku

Poszczególne wykresy można uzyskać poprzez:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Graphs/{graphId}

### <a name="export-pipeline-modules"></a>Eksportowanie modułów rurociągów

Moduły można uzyskać poprzez:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Modules/{id}

### <a name="export-pipeline-templates"></a>Eksportowanie szablonów potoku

Szablony można uzyskać poprzez:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Templates/{templateId}

### <a name="export-pipeline-data-sources"></a>Eksportowanie źródeł danych potoku

Źródła danych można uzyskać poprzez:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/DataSources/{id}

## <a name="delete-assets-in-the-designer"></a>Usuwanie zasobów w projektancie

W projektancie, w którym utworzono eksperyment, usuń poszczególne zasoby:

1. Przejdź do projektanta

    ![Usuwanie zasobów](./media/how-to-export-delete-data/delete-experiment.png)

1. Na liście wybierz indywidualną wersji roboczej potoku do usunięcia.

1. Wybierz pozycję **Usuń**.

### <a name="delete-datasets-in-the-designer"></a>Usuwanie zestawów danych w projektancie

Aby usunąć zestawy danych w projektancie, użyj witryny Azure portal lub Eksploratora magazynu, aby przejść do połączonych kont magazynu i usunąć tam zestawy danych. Wyrejestrowanie zestawów danych w projektancie usuwa tylko punkt odniesienia w magazynie.

## <a name="export-data-in-the-designer"></a>Eksportowanie danych w projektancie

W projektancie, w którym utworzono eksperyment, eksportuj dodane dane:

1. Po lewej stronie wybierz **pozycję Zestawy danych**.

1. Na liście wybierz zestaw danych do wyeksportowania

    ![Pobieranie danych](./media/how-to-export-delete-data/unregister-dataset.png)
