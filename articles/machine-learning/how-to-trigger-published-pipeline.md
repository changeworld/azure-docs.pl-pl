---
title: Wyzwalanie uruchomienia potoku ml z aplikacji logiki
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak wyzwolić uruchomienie potoku uczenia maszynowego przy użyciu aplikacji Azure Logic Apps.
services: machine-learning
author: sanpil
ms.author: sanpil
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 6bb976b8b310fb3eb4d0247a8d745599f688d7b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77122859"
---
# <a name="trigger-a-run-of-a-machine-learning-pipeline-from-a-logic-app"></a>Wyzwalanie uruchomienia potoku uczenia maszynowego z aplikacji logiki

Wyzwalanie uruchomienia potoku usługi Azure Machine Learning, gdy pojawią się nowe dane. Na przykład można wyzwolić potoku do szkolenia nowego modelu, gdy nowe dane pojawią się na koncie magazynu obiektów blob. Skonfiguruj wyzwalacz za pomocą [usługi Azure Logic Apps](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Obszar roboczy usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz [Tworzenie obszaru roboczego usługi Azure Machine Learning](how-to-manage-workspace.md).

* Punkt końcowy REST dla opublikowanego potoku usługi Machine Learning. [Utwórz i opublikuj potok](how-to-create-your-first-pipeline.md). Następnie znajdź punkt końcowy REST opublikowanejpipeline przy użyciu identyfikatora potoku:
    
     ```
    # You can find the pipeline ID in Azure Machine Learning studio
    
    published_pipeline = PublishedPipeline.get(ws, id="<pipeline-id-here>")
    published_pipeline.endpoint 
    ```
* [Magazyn obiektów blob platformy Azure](../storage/blobs/storage-blobs-overview.md) do przechowywania danych.
* [Magazyn danych](how-to-access-data.md) w obszarze roboczym, który zawiera szczegóły konta magazynu obiektów blob.

## <a name="create-a-logic-app"></a>Tworzenie aplikacji logiki

Teraz utwórz wystąpienie [aplikacji logiki azure.](../logic-apps/logic-apps-overview.md) Jeśli chcesz, [użyj środowiska usługi integracji (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) i [skonfiguruj klucz zarządzany](../logic-apps/customer-managed-keys-integration-service-environment.md) przez klienta do użycia przez aplikację logiki.

Po zainicjowaniu obsługi administracyjnej aplikacji logiki wykonaj następujące czynności, aby skonfigurować wyzwalacz dla potoku:

1. [Utwórz tożsamości zarządzanej przypisanej przez system,](../logic-apps/create-managed-service-identity.md) aby nadać aplikacji dostęp do obszaru roboczego usługi Azure Machine Learning.

1. Przejdź do widoku Projektant aplikacji logiki i wybierz szablon pustej aplikacji logiki. 
    > [!div class="mx-imgBorder"]
    > ![Pusty szablon](media/how-to-trigger-published-pipeline/blank-template.png)

1. W projektancie wyszukaj **obiekt blob**. Wybierz **wyzwalacz gdy obiekt blob jest dodawany lub modyfikowany (tylko właściwości)** wyzwalacz i dodać ten wyzwalacz do aplikacji logiki.
    > [!div class="mx-imgBorder"]
    > ![Dodawanie wyzwalacza](media/how-to-trigger-published-pipeline/add-trigger.png)

1. Wypełnij informacje o połączeniu dla konta magazynu obiektów Blob, które chcesz monitorować pod kątem dodatków lub modyfikacji obiektów blob. Wybierz kontener do monitorowania. 
 
    Wybierz **interwał** i **częstotliwość,** aby sondować aktualizacje, które działają dla Ciebie.  

    > [!NOTE]
    > Ten wyzwalacz będzie monitorował wybrany kontener, ale nie będzie monitorował podfolderów.

1. Dodaj akcję HTTP, która zostanie uruchomiony po wykryciu nowego lub zmodyfikowanego obiektu blob. Wybierz **+ Nowy krok**, a następnie wyszukaj i wybierz akcję HTTP.

  > [!div class="mx-imgBorder"]
  > ![Wyszukaj akcję HTTP](media/how-to-trigger-published-pipeline/search-http.png)

  Aby skonfigurować akcję, użyj następujących ustawień:

  | Ustawienie | Wartość | 
  |---|---|
  | Akcja HTTP | POST |
  | Identyfikator URI |punktu końcowego do opublikowanego potoku, który został znaleziony jako [warunek wstępny](#prerequisites) |
  | Tryb uwierzytelniania | Tożsamość zarządzana |

1. Skonfiguruj harmonogram, aby ustawić wartość wszystkich [parametrów pipelinepathpath,](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-datapath-and-pipelineparameter.ipynb) które mogą mieć:

    ```json
    "DataPathAssignments": { 
         "input_datapath": { 
                            "DataStoreName": "<datastore-name>", 
                            "RelativePath": "@triggerBody()?['Name']" 
    } 
    }, 
    "ExperimentName": "MyRestPipeline", 
    "ParameterAssignments": { 
    "input_string": "sample_string3" 
    },
    ```

    Użyj `DataStoreName` obszaru roboczego dodanego do obszaru roboczego jako [warunek wstępny](#prerequisites).
     
    > [!div class="mx-imgBorder"]
    > ![Ustawienia HTTP](media/how-to-trigger-published-pipeline/http-settings.png)

1. Wybierz **pozycję Zapisz,** a harmonogram jest już gotowy.