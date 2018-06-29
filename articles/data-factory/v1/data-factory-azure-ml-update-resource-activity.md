---
title: Aktualizowanie modeli uczenia maszynowego przy użyciu fabryki danych Azure | Dokumentacja firmy Microsoft
description: Opisuje sposób tworzenia tworzenie potoków predykcyjnej przy użyciu fabryki danych Azure i usługi Azure Machine Learning
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 3eb9f765b5ffe1118b3b25aff8ecdde327021a54
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37046483"
---
# <a name="updating-azure-machine-learning-models-using-update-resource-activity"></a>Aktualizowanie modeli uczenia maszynowego Azure przy użyciu działanie aktualizacji zasobu

> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Działanie gałęzi](data-factory-hive-activity.md) 
> * [Działanie pig](data-factory-pig-activity.md)
> * [Działania MapReduce](data-factory-map-reduce.md)
> * [Działaniu przesyłania strumieniowego usługi Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Działanie Spark](data-factory-spark.md)
> * [Działanie wykonywania wsadowego w usłudze Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> * [Działania aktualizowania zasobów w usłudze Machine Learning](data-factory-azure-ml-update-resource-activity.md)
> * [Działania procedur składowanych](data-factory-stored-proc-activity.md)
> * [Działania języka U-SQL usługi Data Lake Analytics](data-factory-usql-activity.md)
> * [Działania niestandardowe .NET](data-factory-use-custom-activities.md)


> [!NOTE]
> Ten artykuł dotyczy wersji 1 fabryki danych. Jeśli używasz bieżącą wersję usługi fabryka danych, zobacz [aktualizowanie modeli uczenia maszyny w fabryce danych](../update-machine-learning-models.md).

Główne fabryki danych Azure - artykułu integracji usługi Azure Machine Learning uzupełnia w tym artykule: [tworzenie potoków predykcyjnej przy użyciu usługi Azure Machine Learning i fabryki danych Azure](data-factory-azure-ml-batch-execution-activity.md). Jeśli nie zostało to jeszcze zrobione, należy przeczytać artykuł głównego przed odczytaniem za pośrednictwem tego artykułu. 

## <a name="overview"></a>Przegląd
Wraz z upływem czasu modeli predykcyjnych w uczenie Maszynowe Azure oceniania eksperymenty konieczne retrained, przy użyciu nowych baz danych wejściowych. Po wykonaniu ponownego trenowania chcesz zaktualizować usługę sieci web oceniania retrained modelu uczenia Maszynowego. Typowe kroki, aby włączyć ponownego trenowania i aktualizowanie modeli uczenia Maszynowego Azure za pośrednictwem usług sieci web są:

1. Tworzenie eksperymentu w [Azure ML Studio](https://studio.azureml.net).
2. Po zakończeniu modelu publikowanie przy użyciu usługi Azure ML Studio usług sieci web dla obu **eksperyment uczenia** i oceniania /**eksperyment predykcyjny**.

W poniższej tabeli opisano usługi sieci web, w tym przykładzie.  Zobacz [Retrain Machine Learning programowo modele](../../machine-learning/machine-learning-retrain-models-programmatically.md) szczegółowe informacje.

- **Usługa sieci web szkolenia** — odbiera dane szkoleniowe i tworzy przeszkolone modeli. Dane wyjściowe ponownego trenowania jest plikiem .ilearner w magazynie obiektów Blob platformy Azure. **Domyślny punkt końcowy** jest tworzona automatycznie dla podczas publikowania szkolenia Eksperymentując jako usługę sieci web. Można utworzyć więcej punktów końcowych, ale w przykładzie użyto tylko domyślny punkt końcowy.
- **Ocenianie usługi sieci web** — odbiera przykłady bez etykiety danych i sprawia, że prognoz. Dane wyjściowe prognozowania może mieć różne formy, takich jak plik CSV lub wierszy w bazie danych Azure SQL, w zależności od konfiguracji eksperymentu. Domyślny punkt końcowy jest utworzony automatycznie po opublikowaniu eksperyment predykcyjny jako usługę sieci web. 

Poniżej przedstawiono relacji między szkolenia i oceniania punktów końcowych w uczenie Maszynowe Azure.

![Usługi sieci Web](./media/data-factory-azure-ml-batch-execution-activity/web-services.png)

Można wywołać **usługi sieci web szkolenia** za pomocą **działanie wykonywania wsadowego usługi Azure ML**. Wywoływanie usługi sieci web szkolenia jest taka sama jak wywoływania usługi sieci web uczenie Maszynowe Azure (oceniania usługi sieci web) dla punktów danych. Poprzednich sekcjach opisano, jak wywołanie usługi sieci web uczenie Maszynowe Azure z potoku fabryki danych Azure szczegółowo. 

Można wywołać **oceniania usługi sieci web** za pomocą **działanie aktualizacji zasobu usługi Azure ML** aktualizowania usługi sieci web przy użyciu nowo trenowanego modelu. Poniższe przykłady zapewniają definicje połączonej usługi: 

## <a name="scoring-web-service-is-a-classic-web-service"></a>Ocenianie usługi sieci web to usługa sieci web klasycznego
Jeśli usługa sieci web oceniania **usługi sieci web klasycznego**, Utwórz drugi **punktu końcowego innych niż domyślne i nadaje się do aktualizacji** przy użyciu portalu Azure. Zobacz [tworzenie punktów końcowych](../../machine-learning/machine-learning-create-endpoint.md) artykułu dla czynności. Po utworzeniu punktu końcowego nadaje się do aktualizacji innych niż domyślne, wykonaj następujące czynności:

* Kliknij przycisk **BATCH EXECUTION** można pobrać wartości identyfikatora URI dla **mlEndpoint** właściwości JSON.
* Kliknij przycisk **aktualizacji zasobów** łącze, aby pobrać wartości identyfikatora URI dla **updateResourceEndpoint** właściwości JSON. Klucz interfejsu API znajduje się na stronie punktu końcowego (w prawym dolnym rogu).

![można aktualizować punktu końcowego](./media/data-factory-azure-ml-batch-execution-activity/updatable-endpoint.png)

W poniższym przykładzie przedstawiono przykład definicji JSON usługi uczenie maszynowe Azure połączone. Połączona usługa używa apiKey do uwierzytelniania.  

```json
{
    "name": "updatableScoringEndpoint2",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--scoring experiment--/jobs",
            "apiKey": "endpoint2Key",
            "updateResourceEndpoint": "https://management.azureml.net/workspaces/xxx/webservices/--scoring experiment--/endpoints/endpoint2"
        }
    }
}
```

## <a name="scoring-web-service-is-azure-resource-manager-web-service"></a>Ocenianie usługi sieci web to usługa sieci web usługi Azure Resource Manager 
Jeśli usługa sieci web jest nowy typ usługi sieci web, która udostępnia punkt końcowy usługi Azure Resource Manager, nie trzeba dodać drugi **innych niż domyślne** punktu końcowego. **UpdateResourceEndpoint** w połączonej usługi jest w formacie: 

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview. 
```

Można uzyskać wartości dla posiadaczy miejsce w adresie URL podczas badania usługi sieci web na [portalu usługi sieci Web Azure Machine Learning](https://services.azureml.net/). Nowy typ punktu końcowego zasobów aktualizacji wymaga tokenu usługi AAD (Azure Active Directory). Określ **servicePrincipalId** i **servicePrincipalKey**w uczenie maszynowe Azure połączonej usługi. Zobacz [jak tworzenie nazwy głównej usługi i przypisywanie uprawnień do zarządzania zasobów platformy Azure](../../azure-resource-manager/resource-group-create-service-principal-portal.md). Oto przykład definicji usługi uczenie maszynowe Azure połączone: 

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "description": "The linked service for AML web service.",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/0000000000000000000000000000000000000/services/0000000000000000000000000000000000000/jobs?api-version=2.0",
            "apiKey": "xxxxxxxxxxxx",
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.MachineLearning/webServices/myWebService?api-version=2016-05-01-preview",
            "servicePrincipalId": "000000000-0000-0000-0000-0000000000000",
            "servicePrincipalKey": "xxxxx",
            "tenant": "mycompany.com"
        }
    }
}
```

Poniższy scenariusz zawiera więcej szczegółowych informacji. Ma przykład ponownego trenowania i aktualizowanie modeli uczenia Maszynowego Azure z potoku fabryki danych Azure.

## <a name="scenario-retraining-and-updating-an-azure-ml-model"></a>Scenariusz: ponownego trenowania i aktualizowanie model usługi uczenie Maszynowe Azure
Ta sekcja zawiera przykładowe potok, który używa **działanie wykonywania wsadowego usługi uczenie Maszynowe Azure** do retrain modelu. Używa również potoku **działania usługi Azure ML aktualizacji zasobów** można zaktualizować modelu oceniania usługi sieci web. Sekcja ta zawiera też fragmenty kodu JSON dla wszystkich połączonych usług, zestawy danych i potoku w przykładzie.

Oto widok diagramu potoku próbki. Jak widać, działanie wykonywania wsadowego uczenia Maszynowego Azure pobiera dane wejściowe szkolenia i szkolenia danych wyjściowych (plik iLearner). Działanie usługi Azure ML aktualizacji zasobów przyjmuje te dane wyjściowe szkolenia i aktualizuje modelu w oceniania punkt końcowy usługi sieci web. Działanie aktualizacji zasobu nie generuje żadnego wyniku. PlaceholderBlob jest tylko fikcyjny wyjściowego zestawu danych, który jest wymagany przez usługi fabryka danych Azure do uruchamiania potoku.

![diagram procesu](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

### <a name="azure-blob-storage-linked-service"></a>Magazyn obiektów Blob Azure połączonej usługi:
Magazyn Azure przechowuje następujące dane:

* danych szkoleniowych. Danych wejściowych dla usługi sieci web uczenie Maszynowe Azure szkolenia.  
* Plik iLearner. Dane wyjściowe z usługi sieci web szkolenia uczenie Maszynowe Azure. Ten plik jest również dane wejściowe działanie aktualizacji zasobu.  

Oto przykład definicji JSON połączonej usługi:

```JSON
{
    "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=name;AccountKey=key"
        }
    }
}
```

### <a name="training-input-dataset"></a>Szkolenie wejściowy zestaw danych:
Następujący zestaw danych reprezentuje dane wejściowe szkolenia dla usługi sieci web uczenie Maszynowe Azure szkolenia. Działanie wykonywania wsadowego usługi uczenie Maszynowe Azure ma tego zestawu danych jako dane wejściowe.

```JSON
{
    "name": "trainingData",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "labeledexamples",
            "fileName": "labeledexamples.arff",
            "format": {
                "type": "TextFormat"
            }
        },
        "availability": {
            "frequency": "Week",
            "interval": 1
        },
        "policy": {          
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

### <a name="training-output-dataset"></a>Szkolenie wyjściowy zestaw danych:
Następujący zestaw danych reprezentuje plik iLearner dane wyjściowe z usługi sieci web szkolenia uczenie Maszynowe Azure. Działanie wykonywania wsadowego usługi Azure ML tworzy tego zestawu danych. Ten zestaw danych jest także dane wejściowe działania usługi Azure ML aktualizacji zasobów.

```JSON
{
    "name": "trainedModelBlob",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "trainingoutput",
            "fileName": "model.ilearner",
            "format": {
                "type": "TextFormat"
            }
        },
        "availability": {
            "frequency": "Week",
            "interval": 1
        }
    }
}
```

### <a name="linked-service-for-azure-ml-training-endpoint"></a>Połączona usługa dla punktu końcowego uczenia uczenie Maszynowe Azure
Poniższy fragment kodu JSON definiuje usługi Azure Machine Learning połączone, która wskazuje domyślny punkt końcowy usługi sieci web szkolenia.

```JSON
{    
    "name": "trainingEndpoint",
      "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--training experiment--/jobs",
              "apiKey": "myKey"
        }
      }
}
```

W **Azure ML Studio**, wykonaj następujące czynności, aby uzyskać wartości dla **mlEndpoint** i **apiKey**:

1. Kliknij przycisk **usług sieci WEB** w menu po lewej stronie.
2. Kliknij przycisk **usługi sieci web szkolenia** na liście usług sieci web.
3. Kliknij przycisk Kopiuj obok **klucz interfejsu API** pola tekstowego. Wklej klucz Schowka do edytora JSON fabryki danych.
4. W **Azure ML studio**, kliknij przycisk **BATCH EXECUTION** łącza.
5. Kopiuj **przez identyfikator URI żądania** z **żądania** sekcji i wklej go w edytorze JSON fabryki danych.   

### <a name="linked-service-for-azure-ml-updatable-scoring-endpoint"></a>Połączonej usługi uczenie Maszynowe Azure punktu końcowego oceniania nadaje się do aktualizacji:
Poniższy fragment kodu JSON definiuje usługi Azure Machine Learning połączone, która wskazuje punkt końcowy nadaje się do aktualizacji innych niż domyślne oceniania usługi sieci web.  

```JSON
{
    "name": "updatableScoringEndpoint2",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/00000000eb0abe4d6bbb1d7886062747d7/services/00000000026734a5889e02fbb1f65cefd/jobs?api-version=2.0",
            "apiKey": "sooooooooooh3WvG1hBfKS2BNNcfwSO7hhY6dY98noLfOdqQydYDIXyf2KoIaN3JpALu/AKtflHWMOCuicm/Q==",
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/myWebService?api-version=2016-05-01-preview",
            "servicePrincipalId": "fe200044-c008-4008-a005-94000000731",
            "servicePrincipalKey": "zWa0000000000Tp6FjtZOspK/WMA2tQ08c8U+gZRBlw=",
            "tenant": "mycompany.com"
        }
    }
}
```

### <a name="placeholder-output-dataset"></a>Symbol zastępczy wyjściowy zestaw danych:
Działanie usługi Azure ML aktualizacji zasobów nie generuje żadnego wyniku. Fabryka danych Azure wymaga jednak wyjściowy zestaw danych do kierowania harmonogram potoku. W związku z tym używamy zestawu manekina/symbol zastępczy danych, w tym przykładzie.  

```JSON
{
    "name": "placeholderBlob",
    "properties": {
        "availability": {
            "frequency": "Week",
            "interval": 1
        },
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "any",
            "format": {
                "type": "TextFormat"
            }
        }
    }
}
```

### <a name="pipeline"></a>Potok
Potok zawiera dwa działania: **AzureMLBatchExecution** i **AzureMLUpdateResource**. Działanie wykonywania wsadowego usługi uczenie Maszynowe Azure przyjmuje jako dane wejściowe dane szkoleniowe i tworzy plik iLearner jako dane wyjściowe. Działanie wywołuje usługę sieci web szkolenia (eksperyment uczenia udostępniony jako usługa sieci web) przy użyciu danych wejściowych szkolenia i odbiera plik ilearner z usługi sieci Web. PlaceholderBlob jest tylko fikcyjny wyjściowego zestawu danych, który jest wymagany przez usługi fabryka danych Azure do uruchamiania potoku.

![diagram procesu](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [
            {
                "name": "retraining",
                "type": "AzureMLBatchExecution",
                "inputs": [
                    {
                        "name": "trainingData"
                    }
                ],
                "outputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "typeProperties": {
                    "webServiceInput": "trainingData",
                    "webServiceOutputs": {
                        "output1": "trainedModelBlob"
                    }              
                 },
                "linkedServiceName": "trainingEndpoint",
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "02:00:00"
                }
            },
            {
                "type": "AzureMLUpdateResource",
                "typeProperties": {
                    "trainedModelName": "Training Exp for ADF ML [trained model]",
                    "trainedModelDatasetName" :  "trainedModelBlob"
                },
                "inputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "outputs": [
                    {
                        "name": "placeholderBlob"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "AzureML Update Resource",
                "linkedServiceName": "updatableScoringEndpoint2"
            }
        ],
        "start": "2016-02-13T00:00:00Z",
           "end": "2016-02-14T00:00:00Z"
    }
}
```
