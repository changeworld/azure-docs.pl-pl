---
title: Aktualizowanie modeli uczenia komputera przy użyciu fabryki danych Azure | Dokumentacja firmy Microsoft
description: Opisuje sposób tworzenia tworzenie potoków predykcyjnej przy użyciu fabryki danych Azure oraz uczenia maszynowego
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: shlo
ms.openlocfilehash: 931c6b2cc0230e4605089dfc1eb0764aa61ec7b8
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34620466"
---
# <a name="update-azure-machine-learning-models-by-using-update-resource-activity"></a>Aktualizowanie modeli uczenia maszynowego Azure za pomocą działania aktualizacji zasobów
Główne fabryki danych Azure - artykułu integracji usługi Azure Machine Learning uzupełnia w tym artykule: [tworzenie potoków predykcyjnej przy użyciu usługi Azure Machine Learning i fabryki danych Azure](transform-data-using-machine-learning.md). Jeśli nie zostało to jeszcze zrobione, należy przeczytać artykuł głównego przed odczytaniem za pośrednictwem tego artykułu. 

## <a name="overview"></a>Przegląd
W ramach procesu operacyjnych modeli uczenia maszynowego Azure modelu są uczone i zapisać. Następnie należy go utworzyć predicative usługi sieci Web. Usługi sieci Web mogą być następnie używane w witrynach sieci web, pulpity nawigacyjne i aplikacji mobilnych.

Modele utworzone za pomocą uczenia maszynowego zwykle nie są statyczne. Wraz ze wzrostem dostępności nowych danych lub gdy konsumenta interfejsu API ma własne dane modelu musi być retrained. Zapoznaj się [Retrain modelu uczenia maszynowego](../machine-learning/machine-learning-retrain-machine-learning-model.md) szczegółowe informacje na temat sposobu można ponownie ucz modelu w usłudze Azure Machine Learning. 

Ponownego trenowania może występować często. Działanie wykonywania wsadowego i działanie aktualizacji zasobu aby operacjonalizować model uczenia maszynowego Azure ponownego trenowania i aktualizowanie predykcyjnej usługi sieci Web przy użyciu fabryki danych. 

Poniżej przedstawia relacje między szkolenia i predykcyjnej usług sieci Web. 

![Usługi sieci Web](./media/update-machine-learning-models/web-services.png)

## <a name="azure-machine-learning-update-resource-activity"></a>Działanie zasobów aktualizacji w usłudze Azure Machine Learning 

Poniższy fragment kodu JSON definiuje działania usługi Azure Machine Learning wsadowe.

```json
{
    "name": "amlUpdateResource",
    "type": "AzureMLUpdateResource",
    "description": "description",
    "linkedServiceName": {
        "type": "LinkedServiceReference",
        "referenceName": "updatableScoringEndpoint2"
    },
    "typeProperties": {
        "trainedModelName": "ModelName",
        "trainedModelLinkedServiceName": {
                    "type": "LinkedServiceReference",
                    "referenceName": "StorageLinkedService"
                },
        "trainedModelFilePath": "ilearner file path"
    }
}
```




| Właściwość                      | Opis                              | Wymagane |
| :---------------------------- | :--------------------------------------- | :------- |
| name                          | Nazwa działania w potoku     | Yes      |
| description                   | Tekst opisujący działanie robi.  | Nie       |
| type                          | Dla działania Azure Machine Learning aktualizacji zasobów jest typ działania **AzureMLUpdateResource**. | Yes      |
| linkedServiceName             | Usługa Azure Machine Learning połączonej usługi, który zawiera właściwości updateResourceEndpoint. | Yes      |
| trainedModelName              | Nazwa modułu Uczonego modelu w eksperymencie usługi sieci Web do zaktualizowania | Yes      |
| trainedModelLinkedServiceName | Nazwa połączoną usługą magazynu Azure zawierający plik ilearner, który jest przekazywany przez operację aktualizacji | Yes      |
| trainedModelFilePath          | Ścieżka względna w trainedModelLinkedService reprezentujący plik ilearner, który jest przekazywany przez operację aktualizacji | Yes      |


## <a name="end-to-end-workflow"></a>Kompletny przepływ pracy

Cały proces operacyjnych ponownego trenowania modelu i aktualizacji predykcyjnej usług sieci Web obejmuje następujące kroki: 

- Wywołanie **usługi sieci Web szkolenia** za pomocą **działanie wykonywania wsadowego**. Wywoływanie szkolenia usługi sieci Web jest taki sam jak wywoływania usługi sieci Web predykcyjnej opisanego w [tworzenie potoków predykcyjnej za pomocą działania uczenie maszynowe Azure i wykonywania wsadowego usługi fabryka danych](transform-data-using-machine-learning.md). Dane wyjściowe szkolenia usługi sieci Web jest plik iLearner, który służy do aktualizowania predykcyjnej usługi sieci Web. 
- Wywołanie **aktualizacja zasobu punktu końcowego** z **predykcyjnej usługi sieci Web** za pomocą **działanie aktualizacji zasobu** aktualizowania usługi sieci Web przy użyciu nowo trenowanego modelu. 

## <a name="azure-machine-learning-linked-service"></a>Usługa Azure Machine Learning połączone

Wyżej przepływu pracy na trasie do pracy musisz utworzyć dwie usługi Azure Machine Learning połączone: 

1. Azure Machine Learning połączonej usługi z usługą sieci web szkolenia, tej połączonej usługi jest używany przez działanie wykonywania wsadowego w taki sam sposób jak wspomniano w [tworzenie potoków predykcyjnej przy użyciu usługi Azure Machine Learning i partii fabryki danych Działanie wykonywania](transform-data-using-machine-learning.md). Różnica polega na wyjście usługi sieci web szkolenia jest plik iLearner, który jest następnie używany przez działanie aktualizacji zasobu do usługi sieci web predykcyjnej aktualizacji. 
2. Usługa Azure Machine Learning połączony do aktualizacji zasobów punkt końcowy usługi sieci web predykcyjnej. Tej połączonej usługi jest używany przez działanie aktualizacji zasobu do aktualizowania usługi sieci web predykcyjnej przy użyciu pliku iLearner zwrócił powyższego kroku. 

Dla usługi Azure Machine Learning połączone drugi konfiguracji różni się w przypadku usługi sieci Web Azure Machine Learning klasycznym usługi sieci Web lub nową usługę sieci Web. Różnice zostały omówione w poniższych sekcjach. 

## <a name="web-service-is-new-azure-resource-manager-web-service"></a>Usługa sieci Web jest nową usługę sieci web usługi Azure Resource Manager 

Jeśli usługa sieci web jest nowy typ usługi sieci web, która udostępnia punkt końcowy usługi Azure Resource Manager, nie trzeba dodać drugi **innych niż domyślne** punktu końcowego. **UpdateResourceEndpoint** w połączonej usługi jest w formacie: 

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview. 
```

Można uzyskać wartości dla posiadaczy miejsce w adresie URL podczas badania usługi sieci web na [portalu usługi sieci Web Azure Machine Learning](https://services.azureml.net/). 

Nowy typ punktu końcowego zasobów aktualizacji wymaga uwierzytelniania główną usługi. Uwierzytelnianie głównej usługi, Zarejestruj podmiot aplikacji w usłudze Azure Active Directory (Azure AD) i przyznać jej **współautora** lub **właściciela** roli subskrypcja lub zasób grupy where Usługa sieci web należy do. Zobacz [jak tworzenie nazwy głównej usługi i przypisywanie uprawnień do zarządzania zasobów platformy Azure](../azure-resource-manager/resource-group-create-service-principal-portal.md). Zwróć uwagę na następujące wartości, które służą do definiowania połączonej usługi:

- Identyfikator aplikacji
- Klucz aplikacji 
- Identyfikator dzierżawy

Oto przykładowe połączone definicji usługi: 

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "description": "The linked service for AML web service.",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/0000000000000000  000000000000000000000/services/0000000000000000000000000000000000000/jobs?api-version=2.0",
            "apiKey": {
            "type": "SecureString",
            "value": "APIKeyOfEndpoint1"
            },
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview. ",
            "servicePrincipalId": "000000000-0000-0000-0000-0000000000000",
            "servicePrincipalKey": {
            "type": "SecureString",
            "value": "servicePrincipalKey"
            },
            "tenant": "mycompany.com"
        }
    }
}
```

Poniższy scenariusz zawiera więcej szczegółowych informacji. Ma przykład ponownego trenowania i aktualizowanie modeli uczenia Maszynowego Azure z potoku fabryki danych Azure.


## <a name="sample-retraining-and-updating-an-azure-machine-learning-model"></a>Przykład: Ponownego trenowania i aktualizowanie modelu uczenia maszynowego Azure

Ta sekcja zawiera przykładowe potok, który używa **działanie wykonywania wsadowego usługi uczenie Maszynowe Azure** do retrain modelu. Używa również potoku **działania usługi Azure ML aktualizacji zasobów** można zaktualizować modelu oceniania usługi sieci web. Sekcja ta zawiera też fragmenty kodu JSON dla wszystkich połączonych usług, zestawy danych i potoku w przykładzie.

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
Poniższy fragment kodu JSON definiuje usługę Azure Machine Learning połączone, wskazujące aktualizowalnych punktu końcowego oceniania usługi sieci web.  

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

### <a name="pipeline"></a>Potok
Potok zawiera dwa działania: **AzureMLBatchExecution** i **AzureMLUpdateResource**. Działanie wykonywania wsadowego przyjmuje jako dane wejściowe dane szkoleniowe i tworzy plik iLearner jako dane wyjściowe. Działanie aktualizacji zasobu następnie pobiera ten plik iLearner i używać go do usługi sieci web predykcyjnej aktualizacji. 

```JSON
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "amlBEGetilearner",
                "description": "Use AML BES to get the ileaner file from training web service",
                "type": "AzureMLBatchExecution",
                "linkedServiceName": {
                    "referenceName": "trainingEndpoint",
                    "type": "LinkedServiceReference"
                },
                "typeProperties": {
                    "webServiceInputs": {
                        "input1": {
                            "LinkedServiceName":{
                                "referenceName": "StorageLinkedService",
                                "type": "LinkedServiceReference"
                            }, 
                            "FilePath":"azuremltesting/input"
                        }, 
                        "input2": {
                            "LinkedServiceName":{
                                "referenceName": "StorageLinkedService",
                                "type": "LinkedServiceReference" 
                            }, 
                            "FilePath":"azuremltesting/input"
                        }        
                    },
                    "webServiceOutputs": {
                        "output1": {
                            "LinkedServiceName":{
                                "referenceName": "StorageLinkedService",
                                "type": "LinkedServiceReference"   
                            }, 
                            "FilePath":"azuremltesting/output"
                        }        
                    }
                }
            },
            {
                "name": "amlUpdateResource",
                "type": "AzureMLUpdateResource",
                "description": "Use AML Update Resource to update the predict web service",
                "linkedServiceName": {
                    "type": "LinkedServiceReference",
                    "referenceName": "updatableScoringEndpoint2"
                },
                "typeProperties": {
                    "trainedModelName": "ADFV2Sample Model [trained model]",
                    "trainedModelLinkedServiceName": {
                                "type": "LinkedServiceReference",
                                "referenceName": "StorageLinkedService"
                            },
                    "trainedModelFilePath": "azuremltesting/output/newModelForArm.ilearner"
                },
                "dependsOn": [ 
                    { 
                        "activity": "amlbeGetilearner", 
                        "dependencyConditions": [ "Succeeded" ] 
                    }
                 ]

            }
        ]
    }
}
```
## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące artykuły, które opisują sposób przekształcania danych w inny sposób: 

* [Działanie U-SQL](transform-data-using-data-lake-analytics.md)
* [Działanie gałęzi](transform-data-using-hadoop-hive.md)
* [Działanie pig](transform-data-using-hadoop-pig.md)
* [Działania MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Działaniu przesyłania strumieniowego usługi Hadoop](transform-data-using-hadoop-streaming.md)
* [Działanie Spark](transform-data-using-spark.md)
* [Niestandardowe działanie platformy .NET](transform-data-using-dotnet-custom-activity.md)
* [Działania procedury składowanej](transform-data-using-stored-procedure.md)
