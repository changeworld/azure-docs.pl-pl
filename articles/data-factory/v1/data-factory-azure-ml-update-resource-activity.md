---
title: Aktualizowanie modeli uczenia maszynowego przy użyciu usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób tworzenia tworzyć potoki predykcyjne przy użyciu usługi Azure Data Factory i Azure Machine Learning
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 0c0e0e3983344bba76f5f305ecaf73f91110f3bc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60567319"
---
# <a name="updating-azure-machine-learning-models-using-update-resource-activity"></a>Aktualizowanie modeli usługi Azure Machine Learning, za pomocą działania aktualizowania zasobów

> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Działanie technologii hive](data-factory-hive-activity.md) 
> * [Działania technologii pig](data-factory-pig-activity.md)
> * [Działania technologii MapReduce](data-factory-map-reduce.md)
> * [Działania przesyłania strumieniowego usługi Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Działania platformy Spark](data-factory-spark.md)
> * [Działanie wykonywania wsadowego w usłudze Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> * [Działania aktualizowania zasobów w usłudze Machine Learning](data-factory-azure-ml-update-resource-activity.md)
> * [Działania procedur składowanych](data-factory-stored-proc-activity.md)
> * [Działania języka U-SQL usługi Data Lake Analytics](data-factory-usql-activity.md)
> * [Niestandardowe działanie platformy .NET](data-factory-use-custom-activities.md)


> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącą wersję usługi Data Factory, zobacz [aktualizacji modeli uczenia maszynowego w usłudze Data Factory](../update-machine-learning-models.md).

Ten artykuł stanowi uzupełnienie głównej fabryki danych platformy Azure — artykuł integracji usługi Azure Machine Learning: [Tworzenie potoków predykcyjnych przy użyciu usługi Azure Machine Learning i Azure Data Factory](data-factory-azure-ml-batch-execution-activity.md). Jeśli nie zostało to jeszcze zrobione, zapoznaj się z artykułem głównego przed odczytaniem za pośrednictwem tego artykułu. 

## <a name="overview"></a>Omówienie
Wraz z upływem czasu modele predykcyjne w usługi Azure ML oceniania eksperymentów konieczne retrained, przy użyciu nowych danych wejściowych zestawów danych. Po zakończeniu ponownego trenowania, chcesz zaktualizować usługi internetowej przyznawania ocen retrained modelu uczenia Maszynowego. Dostępne są następujące typowe czynności, aby umożliwić ponownego trenowania i aktualizowanie modeli usługi uczenie Maszynowe Azure za pośrednictwem usług sieci web:

1. Tworzenie eksperymentu w [usługi Azure ML Studio](https://studio.azureml.net).
2. Gdy jesteś zadowolony z modelem, użyj usługi Azure ML Studio, aby publikować usługi sieci web dla obu **eksperymentu szkolenia** i oceniania /**eksperyment predykcyjny**.

W poniższej tabeli opisano usługi sieci web, w tym przykładzie.  Zobacz [Retrain Machine Learning models programowo](../../machine-learning/machine-learning-retrain-models-programmatically.md) Aby uzyskać szczegółowe informacje.

- **Usługa sieci web do szkoleń** — otrzymuje dane szkoleniowe i tworzy przeszkolone modele. Dane wyjściowe z ponownym szkoleniem jest plikiem .ilearner w usłudze Azure Blob storage. **Domyślny punkt końcowy** są tworzone automatycznie dla publikowania szkolenia przeprowadzanie eksperymentów jako usługę sieci web. Można utworzyć dodatkowe punkty końcowe, ale w przykładzie użyto tylko domyślny punkt końcowy.
- **Ocenianie usługi sieci web** — odbiera przykłady bez etykiety danych i wykonuje prognozy. Dane wyjściowe prognozowania może mieć różne formy, np. plik CSV lub wierszy w bazie danych Azure SQL, w zależności od konfiguracji doświadczenia. Domyślny punkt końcowy jest tworzone dla Ciebie automatycznie, podczas publikowania eksperyment predykcyjny jako usługę sieci web. 

Poniższy obraz przedstawia relację między szkolenia i oceniania punktów końcowych w usłudze Azure ML.

![Usługi sieci Web](./media/data-factory-azure-ml-batch-execution-activity/web-services.png)

Możesz wywołać **szkolenia usługi sieci web** przy użyciu **działanie wykonywania wsadowego usługi Azure ML**. Wywoływanie usługi sieci web szkolenia jest taka sama jak wywoływania usługi sieci web Azure ML (oceniania usługi sieci web) dla danych oceniania. Poprzednich sekcjach opisano, jak wywołania usługi sieci web Azure ML z potoku usługi Azure Data Factory szczegółowo. 

Możesz wywołać **oceniania usługi sieci web** przy użyciu **działanie aktualizacji zasobu usługi Azure ML** zaktualizować usługę sieci web przy użyciu nowo trenowanego modelu. W poniższych przykładach udostępniono definicji połączonej usługi: 

## <a name="scoring-web-service-is-a-classic-web-service"></a>Ocenianie usługi sieci web jest klasyczna usługa sieci web
W przypadku usługi internetowej przyznawania ocen **klasyczna usługa sieci web**, Utwórz drugi **innych niż domyślne i nadaje się do aktualizacji punkt końcowy** przy użyciu witryny Azure portal. Zobacz [tworzenie punktów końcowych](../../machine-learning/machine-learning-create-endpoint.md) artykuł, aby kroki. Po utworzeniu punktu końcowego nadaje się do aktualizacji innych niż domyślne, wykonaj następujące czynności:

* Kliknij przycisk **wykonywanie WSADOWE** można pobrać wartość identyfikatora URI **mlEndpoint** właściwość JSON.
* Kliknij przycisk **aktualizacja zasobów** łącze, aby uzyskać wartość identyfikatora URI **updateResourceEndpoint** właściwość JSON. Klucz interfejsu API znajduje się na stronie punktu końcowego (w prawym dolnym rogu).

![można zaktualizować punktu końcowego](./media/data-factory-azure-ml-batch-execution-activity/updatable-endpoint.png)

W poniższym przykładzie przedstawiono Przykładowa definicja JSON usługi połączonej usługi Azure ml. Połączona usługa używa apiKey do uwierzytelniania.  

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
Jeśli usługa sieci web jest nowy typ usługi sieci web, który uwidacznia punkt końcowy usługi Azure Resource Manager, nie trzeba dodać drugi **innych niż domyślne** punktu końcowego. **UpdateResourceEndpoint** w połączonej usłudze ma format: 

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview. 
```

Można uzyskać wartości dla posiadaczy miejsce w adresie URL podczas wykonywania zapytań względem usługi sieci web na [Azure maszyny Learning sieci Web Services Portal](https://services.azureml.net/). Nowy typ punkt końcowy aktualizacji zasobu wymaga tokenu AAD (Azure Active Directory). Określ **servicePrincipalId** i **servicePrincipalKey**w usłudze AzureML połączoną usługę. Zobacz [Tworzenie jednostki usługi i przypisywanie uprawnień do zarządzania usługi Azure resource](../../active-directory/develop/howto-create-service-principal-portal.md). Oto przykład definicji usługi połączonej usługi Azure ml: 

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

Poniższy scenariusz zawiera więcej szczegółowych informacji. Ma przykład do ponownego trenowania i aktualizowanie modeli usługi uczenie Maszynowe Azure z potoku usługi Azure Data Factory.

## <a name="scenario-retraining-and-updating-an-azure-ml-model"></a>Scenariusz: ponownego trenowania i aktualizowanie modelu usługi uczenie Maszynowe Azure
Ta sekcja zawiera przykładowy potok, który używa **Azure ML Batch Execution, działanie** doskonalenie modelu. Potok używa również **działanie usługi Azure ML aktualizacji zasobu** do aktualizacji modelu oceniania usługi sieci web. Ta sekcja zawiera również fragmentów kodu JSON dla wszystkich połączonych usług, zestawów danych i potoku w przykładzie.

Poniżej przedstawiono przykładowy potok w widoku diagramu. Jak widać, działanie wykonywania wsadowego usługi Azure ML pobiera szkolenia dane wejściowe i generuje dane wyjściowe szkolenia (plik iLearner). Działanie usługi Azure ML aktualizacji zasobu przyjmuje następujące dane wyjściowe szkolenia i aktualizacji modelu w oceniania punkt końcowy usługi sieci web. Działanie aktualizacji zasobu nie generuje żadnych danych wyjściowych. PlaceholderBlob jest po prostu fikcyjne dane wyjściowe zestawu danych, który jest wymagany przez usługę Azure Data Factory, aby uruchomić potok.

![diagram potoku](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

### <a name="azure-blob-storage-linked-service"></a>Połączona usługa Azure Blob storage:
Usługi Azure Storage zawiera następujące dane:

* dane szkoleniowe. Dane wejściowe dla usługi sieci web Azure ML szkolenia.  
* Plik iLearner. Dane wyjściowe z usługi sieci web Azure ML szkolenia. Ten plik jest również dane wejściowe działanie aktualizacji zasobu.  

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

### <a name="training-input-dataset"></a>Wejściowy zestaw danych szkoleniowych:
Poniższy zestaw danych reprezentuje dane wejściowe szkoleniowe usługi sieci web Azure ML szkolenia. Działanie wykonywania wsadowego usługi Azure ML ma tego zestawu danych jako dane wejściowe.

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
Poniższy zestaw danych reprezentuje plik iLearner dane wyjściowe z usługi sieci web Azure ML szkolenia. Działanie wykonywania wsadowego usługi Azure ML tworzy tego zestawu danych. Ten zestaw danych jest także dane wejściowe dla działania usługi Azure ML aktualizacji zasobu.

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

### <a name="linked-service-for-azure-ml-training-endpoint"></a>Połączoną usługę Azure ML szkolenia z punktu końcowego
Poniższy fragment kodu JSON definiuje usługi Azure Machine Learning w przypadku połączonej, który wskazuje na domyślny punkt końcowy usługi sieci web szkolenia.

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

W **usługi Azure ML Studio**, wykonaj następujące czynności, aby uzyskać wartości dla **mlEndpoint** i **apiKey**:

1. Kliknij przycisk **usług sieci WEB** w menu po lewej stronie.
2. Kliknij przycisk **szkolenia usługi sieci web** na liście usług sieci web.
3. Kliknij przycisk kopiowania obok **klucz interfejsu API** pola tekstowego. Wklej klucz Schowka do edytora JSON usługi Data Factory.
4. W **Azure ML studio**, kliknij przycisk **wykonywanie WSADOWE** łącza.
5. Kopiuj **żądanie identyfikatora URI** z **żądania** sekcji i wklej go w edytorze JSON usługi Data Factory.   

### <a name="linked-service-for-azure-ml-updatable-scoring-endpoint"></a>Połączoną usługę Azure ML punktu końcowego oceniania nadaje się do aktualizacji:
Poniższy fragment kodu JSON definiuje usługę Azure Machine Learning połączone, wskazujący innych niż domyślne można zaktualizować punktu końcowego usługi internetowej przyznawania ocen.  

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
Działanie usługi Azure ML aktualizacja zasobu nie generuje żadnych danych wyjściowych. Usługi Azure Data Factory wymaga jednak wyjściowy zestaw danych do harmonogramu potoku. W związku z tym używamy zestawu dummy/symbol zastępczy danych, w tym przykładzie.  

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
Potok ma dwa działania: **AzureMLBatchExecution** i **AzureMLUpdateResource**. Działanie wykonywania wsadowego usługi Azure ML przyjmuje dane szkoleniowe jako dane wejściowe i tworzy plik iLearner jako dane wyjściowe. Działania wywołuje usługę sieci web szkolenia (udostępniane jako usługi sieci web eksperymentu szkolenia) przy użyciu danych wejściowych szkolenia i odbiera plik ilearner z usługi sieci Web. PlaceholderBlob jest po prostu fikcyjne dane wyjściowe zestawu danych, który jest wymagany przez usługę Azure Data Factory, aby uruchomić potok.

![diagram potoku](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

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
