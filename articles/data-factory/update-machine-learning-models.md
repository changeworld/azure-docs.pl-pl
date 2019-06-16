---
title: Aktualizowanie modeli uczenia maszynowego przy użyciu usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób tworzenia tworzyć potoki predykcyjne przy użyciu usługi Azure Data Factory i uczenie maszynowe
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: shlo
ms.openlocfilehash: 8f1320db0af85f6c83a9daf8e17a691336c9b251
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60335496"
---
# <a name="update-azure-machine-learning-models-by-using-update-resource-activity"></a>Aktualizowanie modeli usługi Azure Machine Learning za pomocą działania aktualizacja zasobów
Ten artykuł stanowi uzupełnienie głównej fabryki danych platformy Azure — artykuł integracji usługi Azure Machine Learning: [Tworzenie potoków predykcyjnych przy użyciu usługi Azure Machine Learning i Azure Data Factory](transform-data-using-machine-learning.md). Jeśli nie zostało to jeszcze zrobione, zapoznaj się z artykułem głównego przed odczytaniem za pośrednictwem tego artykułu.

## <a name="overview"></a>Omówienie
W ramach procesu operacjonalizacji modeli usługi Azure Machine Learning model jest uczony i zapisany. Możesz następnie użyć go do utworzenia predykcyjne usługi sieci Web. Usługi sieci Web mogą być następnie używane w witrynach sieci web, pulpity nawigacyjne i aplikacje mobilne.

Modele utworzone za pomocą usługi Machine Learning zwykle nie są statyczne. Nowe dane staje się dostępny, lub gdy konsumenta interfejsu API ma swoje własne dane modelu musi być retrained. Zapoznaj się [Ponowne szkolenie modelu uczenia maszynowego](../machine-learning/machine-learning-retrain-machine-learning-model.md) szczegółowe informacje na temat sposobu mogą przechowywać modelu w usłudze Azure Machine Learning.

Ponowne szkolenie może często występować. Działanie wykonywania wsadowego i działanie aktualizacji zasobu można zoperacjonalizować modelu usługi Azure Machine Learning, ponownego trenowania i aktualizowanie predykcyjne usługi sieci Web przy użyciu usługi fabryka danych.

Poniższy obraz przedstawia relacje między szkolenia i predykcyjnych usług sieci Web.

![Usługi sieci Web](./media/update-machine-learning-models/web-services.png)

## <a name="azure-machine-learning-update-resource-activity"></a>Usługa Azure Machine Learning działanie aktualizacji zasobu w

Poniższy fragment kodu JSON definiuje działania usługi Azure Machine Learning Batch Execution.

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
| description                   | Tekst opisujący, co działanie robi.  | Nie       |
| type                          | Do działania usługi Azure Machine Learning Update Resource, działanie jest **AzureMLUpdateResource**. | Yes      |
| linkedServiceName             | Usługa Azure Machine Learning połączoną usługę, która zawiera właściwość updateResourceEndpoint. | Tak      |
| trainedModelName              | Nazwę modułu Uczonego modelu w doświadczeniu usługi sieci Web do zaktualizowania | Tak      |
| trainedModelLinkedServiceName | Nazwa połączonej usługi Azure Storage zawierający plik ilearner, który jest przekazywany przez operację aktualizacji | Yes      |
| trainedModelFilePath          | Ścieżka względna do pliku w trainedModelLinkedService reprezentujący plik ilearner, który jest przekazywany przez operację aktualizacji | Tak      |

## <a name="end-to-end-workflow"></a>Kompletny przepływ pracy

Cały proces operacjonalizacji ponownego szkolenia modelu i aktualizacji predykcyjnych usług sieci Web obejmuje następujące czynności:

- Wywoływanie **szkolenia usługi sieci Web** przy użyciu **Batch Execution, działanie**. Wywoływanie szkolenia usługi sieci Web jest taka sama jak wywoływania usługi sieci Web predykcyjne opisanego w [tworzyć potoki predykcyjne przy użyciu usługi Azure Machine Learning i Data Factory Batch Execution działania](transform-data-using-machine-learning.md). Dane wyjściowe szkolenia usługi sieci Web jest plik iLearner, który służy do aktualizowania predykcyjne usługi sieci Web.
- Wywoływanie **zaktualizować punkt końcowy zasobu** z **predykcyjne usługi sieci Web** przy użyciu **działanie aktualizacji zasobu** zaktualizować usługę sieci Web przy użyciu nowo trenowanego modelu.

## <a name="azure-machine-learning-linked-service"></a>Usługa Azure Machine Learning połączone

Podane wyżej przepływu pracy end-to-end do pracy musisz utworzyć dwie usługi Azure Machine Learning w przypadku połączonej usługi:

1. Azure Machine Learning połączoną usługę służącą do szkolenia usługi sieci web, ta połączona usługa jest używany przez działanie wykonywania wsadowego w taki sam sposób, jak wspomniano w [tworzyć potoki predykcyjne za pomocą usługi Azure Machine Learning i Batch fabryki danych Działanie wykonywania](transform-data-using-machine-learning.md). Różnica polega na dane wyjściowe usługi sieci web, szkolenie to plik iLearner, który jest następnie używany przez działanie aktualizacji zasobu można zaktualizować predykcyjną usługę sieci web.
2. Usługa Azure Machine Learning połączoną usługę aktualizacji zasobu punktu końcowego predykcyjną usługę sieci web. Ta połączona usługa jest używana przez działanie aktualizacji zasobu można zaktualizować usługi predykcyjne sieci web przy użyciu pliku iLearner zwrócił powyższej kroku.

Dla usługi Azure Machine Learning połączone druga Konfiguracja różni się po klasycznej usługi sieci Web lub nowej usługi sieci Web usługi sieci Web Azure Machine Learning. Różnice są omówione w poniższych sekcjach.

## <a name="web-service-is-new-azure-resource-manager-web-service"></a>Usługa sieci Web jest nową usługę sieci web usługi Azure Resource Manager

Jeśli usługa sieci web jest nowy typ usługi sieci web, który uwidacznia punkt końcowy usługi Azure Resource Manager, nie trzeba dodać drugi **innych niż domyślne** punktu końcowego. **UpdateResourceEndpoint** w połączonej usłudze ma format:

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview
```

Można uzyskać wartości dla posiadaczy miejsce w adresie URL podczas wykonywania zapytań względem usługi sieci web na [Azure maszyny Learning sieci Web Services Portal](https://services.azureml.net/).

Nowy typ punkt końcowy aktualizacji zasobu wymaga uwierzytelniania jednostki usługi. Do używania uwierzytelniania jednostki usługi, Zarejestruj aplikację jednostki w usłudze Azure Active Directory (Azure AD) i przyznania **Współautor** lub **właściciela** roli subskrypcję lub zasób grupy where Usługa sieci web należy do. Zobacz [Tworzenie jednostki usługi i przypisywanie uprawnień do zarządzania usługi Azure resource](../active-directory/develop/howto-create-service-principal-portal.md). Zanotuj następujące wartości, które służą do definiowania połączonej usługi:

- Identyfikator aplikacji
- Klucz aplikacji
- Identyfikator dzierżawy

Poniżej przedstawiono przykładowe połączonej definicji usługi:

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
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview",
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

Poniższy scenariusz zawiera więcej szczegółowych informacji. Ma przykład do ponownego trenowania i aktualizowania usługi Azure Machine Learning studio modeli z potoku usługi Azure Data Factory.


## <a name="sample-retraining-and-updating-an-azure-machine-learning-model"></a>Przykład: Ponowne szkolenie i aktualizowanie modelu usługi Azure Machine Learning

Ta sekcja zawiera przykładowy potok, który używa **studio usługi Azure Machine Learning Batch Execution, działanie** doskonalenie modelu. Potok używa również **usługi Azure Machine Learning studio działanie aktualizacji zasobu** do aktualizacji modelu oceniania usługi sieci web. Ta sekcja zawiera również fragmentów kodu JSON dla wszystkich połączonych usług, zestawów danych i potoku w przykładzie.

### <a name="azure-blob-storage-linked-service"></a>Połączona usługa Azure Blob storage:
Usługi Azure Storage zawiera następujące dane:

* dane szkoleniowe. Dane wejściowe dla usługi Azure Machine Learning studio, szkolenia usługi sieci web.
* Plik iLearner. Dane wyjściowe z usługi Azure Machine Learning studio, szkolenia usługi sieci web. Ten plik jest również dane wejściowe działanie aktualizacji zasobu.

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

### <a name="linked-service-for-azure-machine-learning-studio-training-endpoint"></a>Połączona usługa Azure Machine Learning studio szkolenia z punktu końcowego
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

W **usługi Azure Machine Learning studio**, wykonaj następujące czynności, aby uzyskać wartości dla **mlEndpoint** i **apiKey**:

1. Kliknij przycisk **usług sieci WEB** w menu po lewej stronie.
2. Kliknij przycisk **szkolenia usługi sieci web** na liście usług sieci web.
3. Kliknij przycisk kopiowania obok **klucz interfejsu API** pola tekstowego. Wklej klucz Schowka do edytora JSON usługi Data Factory.
4. W **usługi Azure Machine Learning studio**, kliknij przycisk **wykonywanie WSADOWE** łącza.
5. Kopiuj **żądanie identyfikatora URI** z **żądania** sekcji i wklej go w edytorze JSON usługi Data Factory.

### <a name="linked-service-for-azure-machine-learning-studio-updatable-scoring-endpoint"></a>Połączona usługa Azure Machine Learning studio można aktualizować oceniania punktu końcowego:
Poniższy fragment kodu JSON definiuje usługę Azure Machine Learning połączone, wskazujący można zaktualizować punktu końcowego usługi internetowej przyznawania ocen.

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
Potok ma dwa działania: **AzureMLBatchExecution** i **AzureMLUpdateResource**. Batch Execution, działanie trwa danych szkoleniowych jako dane wejściowe i tworzy plik iLearner jako dane wyjściowe. Działanie aktualizacji zasobu następnie pobiera ten plik iLearner i używać go do aktualizacji predykcyjną usługę sieci web.

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
Zobacz następujące artykuły, które wyjaśniają, jak przekształcać dane w inny sposób:

* [Działanie U-SQL](transform-data-using-data-lake-analytics.md)
* [Działanie technologii hive](transform-data-using-hadoop-hive.md)
* [Działania technologii pig](transform-data-using-hadoop-pig.md)
* [Działania technologii MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Działania przesyłania strumieniowego usługi Hadoop](transform-data-using-hadoop-streaming.md)
* [Działania platformy Spark](transform-data-using-spark.md)
* [Niestandardowe działanie platformy .NET](transform-data-using-dotnet-custom-activity.md)
* [Działania procedur składowanych](transform-data-using-stored-procedure.md)
