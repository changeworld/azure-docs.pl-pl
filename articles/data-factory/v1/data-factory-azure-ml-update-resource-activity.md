---
title: Aktualizowanie modeli usługi Machine Learning przy użyciu usługi Azure Data Factory
description: W tym artykule opisano sposób tworzenia rurociągów predykcyjnych przy użyciu usługi Azure Data Factory i usługi Azure Machine Learning
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.openlocfilehash: afc79badd19fa180e631f1f8fa9735567a0b1e33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74978717"
---
# <a name="updating-azure-machine-learning-models-using-update-resource-activity"></a>Aktualizowanie modeli usługi Azure Machine Learning przy użyciu działania aktualizacji zasobów

> [!div class="op_single_selector" title1="Działania związane z transformacją"]
> * [Aktywność gałęzi](data-factory-hive-activity.md) 
> * [Aktywność świń](data-factory-pig-activity.md)
> * [Działanie mapreduce](data-factory-map-reduce.md)
> * [Aktywność strumieniowania Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Aktywność iskra](data-factory-spark.md)
> * [Działanie wykonywania wsadowego w usłudze Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> * [Działanie aktualizowania zasobów w usłudze Machine Learning](data-factory-azure-ml-update-resource-activity.md)
> * [Działania procedur składowanych](data-factory-stored-proc-activity.md)
> * [Działania języka U-SQL usługi Data Lake Analytics](data-factory-usql-activity.md)
> * [Działanie niestandardowe platformy .NET](data-factory-use-custom-activities.md)


> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [aktualizowanie modeli uczenia maszynowego w usłudze Data Factory](../update-machine-learning-models.md).

Ten artykuł stanowi uzupełnienie głównego artykułu integracji usługi Azure Data Factory — Azure Machine Learning: [Tworzenie potoków predykcyjnych przy użyciu usługi Azure Machine Learning i usługi Azure Data Factory.](data-factory-azure-ml-batch-execution-activity.md) Jeśli jeszcze tego nie zrobiłeś, zapoznaj się z głównym artykułem przed przeczytaniem tego artykułu. 

## <a name="overview"></a>Omówienie
Z biegiem czasu modele predykcyjne w eksperymentach oceniania usługi Azure ML muszą zostać przeszkolone przy użyciu nowych wejściowych zestawów danych. Po zakończeniu przekwalifikowania chcesz zaktualizować usługę sieci web oceniania za pomocą ponownie przeszkolonego modelu ml. Typowe kroki umożliwiające przekwalifikowanie i aktualizowanie modeli usługi Azure ML za pośrednictwem usług sieci web to:

1. Utwórz eksperyment w [usłudze Azure Machine Learning Studio (klasyczny)](https://studio.azureml.net).
2. Jeśli model jest zadowalający, użyj usługi Azure Machine Learning Studio (classic), aby opublikować usługi sieci web zarówno dla **eksperymentu szkoleniowego,** jak i**eksperymentu oceniania/predykcyjnego.**

W poniższej tabeli opisano usługi sieci web używane w tym przykładzie.  Szczegółowe informacje [można znaleźć w programie w programie przekwalifikowania modeli uczenia maszynowego.](../../machine-learning/machine-learning-retrain-models-programmatically.md)

- **Szkolenie usługi sieci web** — odbiera dane szkoleniowe i tworzy wyszkolonych modeli. Dane wyjściowe ponownego szkolenia jest plik .ilearner w magazynie obiektów Blob platformy Azure. **Domyślny punkt końcowy** jest tworzony automatycznie podczas publikowania eksperymentu szkoleniowego jako usługi sieci web. Można utworzyć więcej punktów końcowych, ale w przykładzie użyto tylko domyślnego punktu końcowego.
- **Ocenianie usługi sieci web** — odbiera przykłady danych bez etykiety i sprawia, że prognozy. Dane wyjściowe przewidywanie może mieć różne formy, takie jak plik csv lub wiersze w bazie danych SQL platformy Azure, w zależności od konfiguracji eksperymentu. Domyślny punkt końcowy jest tworzony automatycznie podczas publikowania eksperymentu predykcyjnego jako usługi sieci web. 

Poniższy obraz przedstawia relację między szkolenia i oceniania punktów końcowych w usłudze Azure ML.

![Usługi sieci Web](./media/data-factory-azure-ml-batch-execution-activity/web-services.png)

**Usługę sieci web szkolenia** można wywołać przy użyciu działania wykonywania **wsadowego usługi Azure ML.** Wywoływanie szkoleniowej usługi sieci web jest taka sama jak wywoływanie usługi sieci web usługi Azure ML (ocenianie usługi sieci web) do oceniania danych. W poprzednich sekcjach opisano, jak wywołać usługę sieci web usługi Azure ML z potoku usługi Azure Data Factory w szczegółach. 

**Usługę sieci web oceniania** można wywołać przy użyciu działania zasobów usługi Azure ML **Update,** aby zaktualizować usługę sieci web za pomocą nowo przeszkolonego modelu. Poniższe przykłady zawierają powiązane definicje usług: 

## <a name="scoring-web-service-is-a-classic-web-service"></a>Scoring web service to klasyczna usługa internetowa
Jeśli usługa sieci web oceniania jest **klasyczną usługą sieci web,** utwórz drugi **nie-domyślny i aktualizowany punkt końcowy** przy użyciu portalu Azure. Aby [zapoznać się z instrukcjami,](../../machine-learning/machine-learning-create-endpoint.md) zobacz Tworzenie punktów końcowych. Po utworzeniu nieobjętego domyślnym punktem końcowym, wykonaj następujące czynności:

* Kliknij **przycisk BATCH EXECUTION,** aby uzyskać wartość URI dla właściwości **mlEndpoint** JSON.
* Kliknij **przycisk AKTUALIZUJ ZASIEW łącze,** aby uzyskać wartość URI dla **właściwości updateResourceEndpoint** JSON. Klucz interfejsu API znajduje się na samej stronie punktu końcowego (w prawym dolnym rogu).

![aktualizowany punkt końcowy](./media/data-factory-azure-ml-batch-execution-activity/updatable-endpoint.png)

Poniższy przykład zawiera przykładową definicję JSON dla usługi połączonej azureml. Usługa połączona używa apiKey do uwierzytelniania.  

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

## <a name="scoring-web-service-is-azure-resource-manager-web-service"></a>Punktowanie usługi sieci web to usługa sieci Web usługi Azure Resource Manager 
Jeśli usługa sieci web jest nowy typ usługi sieci web, która udostępnia punkt końcowy usługi Azure Resource Manager, nie trzeba dodawać drugi punkt końcowy **nie domyślne.** **UpdateResourceEndpoint** w połączonej usłudze jest w formacie: 

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview. 
```

Podczas wykonywania zapytań o usługę web w portalu [Azure Machine Learning Web Services Portal](https://services.azureml.net/)można uzyskać wartości dla posiadaczy miejsc w adresie URL. Nowy typ punktu końcowego zasobu aktualizacji wymaga tokenu usługi AAD (Azure Active Directory). Określ **servicePrincipalId** i **servicePrincipalKey** w usłudze połączonej usługi Azure Machine Learning. Zobacz, [jak utworzyć jednostkę usługi i przypisać uprawnienia do zarządzania zasobem platformy Azure](../../active-directory/develop/howto-create-service-principal-portal.md). Oto przykładowa definicja usługi połączonej z usługą AzureML: 

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

Poniższy scenariusz zawiera więcej szczegółów. Ma przykład ponownego szkolenia i aktualizowania modeli usługi Azure ML z potoku usługi Azure Data Factory.

## <a name="scenario-retraining-and-updating-an-azure-ml-model"></a>Scenariusz: przekwalifikowanie i aktualizowanie modelu usługi Azure ML
Ta sekcja zawiera przykładowy potok, który używa **działania azure ml wykonywanie partii** do ponownego uczenia modelu. Potok używa również **działania usługi Azure ML Update Resource,** aby zaktualizować model w usłudze sieci web oceniania. Sekcja zawiera również fragmenty kodu JSON dla wszystkich połączonych usług, zestawów danych i potoku w przykładzie.

Oto widok diagramu przykładowego potoku. Jak widać, działanie wykonywania wsadowego usługi Azure ML pobiera dane wejściowe szkolenia i tworzy dane wyjściowe szkolenia (plik iLearner). Działanie zasobów usługi Azure ML Update pobiera to dane wyjściowe szkolenia i aktualizuje model w punkcie końcowym usługi sieci web oceniania. Działanie aktualizuj zasobów nie generuje żadnych danych wyjściowych. Symbol zastępczyBlob jest tylko fikcyjny zestaw danych wyjściowych, który jest wymagany przez usługę Azure Data Factory do uruchomienia potoku.

![diagram potoku](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

### <a name="azure-blob-storage-linked-service"></a>Usługa połączona z magazynem obiektów Blob platformy Azure:
Usługa Azure Storage przechowuje następujące dane:

* danych szkoleniowych. Dane wejściowe dla usługi sieci web szkolenia usługi azure ml.  
* iLearner. Dane wyjściowe z usługi sieci web szkolenia usługi azure ml. Ten plik jest również dane wejściowe do działania Aktualizuj zasób.  

Oto przykładowa definicja JSON połączonej usługi:

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

### <a name="training-input-dataset"></a>Zestaw danych wejściowych szkoleniowych:
Poniższy zestaw danych reprezentuje dane szkoleniowe wejściowe dla usługi sieci web szkoleniowej usługi azure machine learning. Działanie azure machine learning wsadowe wykonanie przyjmuje ten zestaw danych jako dane wejściowe.

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

### <a name="training-output-dataset"></a>Zestaw danych wyjściowych szkolenia:
Poniższy zestaw danych reprezentuje wyjściowy plik iLearner z usługi sieci web szkolenia usługi Azure ML. Działanie wykonywania wsadowego usługi Azure ML tworzy ten zestaw danych. Ten zestaw danych jest również dane wejściowe do działania usługi Azure ML Update Resource.

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

### <a name="linked-service-for-azure-machine-learning-training-endpoint"></a>Połączona usługa dla punktu końcowego szkolenia usługi Azure Machine Learning
Poniższy fragment kodu JSON definiuje usługę połączona usługi Azure Machine Learning, która wskazuje domyślny punkt końcowy szkoleniowej usługi sieci web.

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

W **usłudze Azure Machine Learning Studio (klasycznym)** wykonaj następujące czynności, aby uzyskać wartości dla **mlEndpoint** i **apiKey:**

1. W menu po lewej stronie kliknij polecenie **USŁUGI SIECI WEB.**
2. Kliknij **usługę sieci web szkoleniową** na liście usług sieci web.
3. Kliknij pozycję Kopiuj obok pola **tekstowego klucza interfejsu API.** Wklej klucz w schowku do edytora JSON fabryki danych.
4. W **usłudze Azure Machine Learning Studio (klasycznym)** kliknij łącze **WYKONYWANIE WSADOWE.**
5. Skopiuj **identyfikator URI żądania** z sekcji **Żądanie** i wklej go do edytora JSON fabryki danych.   

### <a name="linked-service-for-azure-ml-updatable-scoring-endpoint"></a>Połączona usługa dla punktu końcowego oceniania aktualizowanego w usłudze Azure ML:
Poniższy fragment kodu JSON definiuje usługę połączona usługi Azure Machine Learning, która wskazuje domyślny punkt końcowy aktualizacji usługi sieci web oceniania.  

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

### <a name="placeholder-output-dataset"></a>Wyjściowy zestaw danych wyjściowych:
Działanie usługi Azure ML Update Resource nie generuje żadnych danych wyjściowych. Jednak usługa Azure Data Factory wymaga wyjściowego zestawu danych do kierowania harmonogramem potoku. W związku z tym używamy fikcyjnego/zastępczego zestawu danych w tym przykładzie.  

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
Potok ma dwa działania: **AzureMLBatchExecution** i **AzureMLUpdateResource**. Działanie azure ml wykonywania wsadowego przyjmuje dane szkoleniowe jako dane wejściowe i tworzy plik iLearner jako dane wyjściowe. Działanie wywołuje szkolenia usługi sieci web (eksperyment szkolenia narażone jako usługa sieci web) z danych szkoleniowych wejściowych i odbiera plik ilearner z usługi sieci web. Symbol zastępczyBlob jest tylko fikcyjny zestaw danych wyjściowych, który jest wymagany przez usługę Azure Data Factory do uruchomienia potoku.

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
