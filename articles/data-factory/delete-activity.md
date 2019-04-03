---
title: Usuń działanie w usłudze Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak usuwać pliki w różnych magazynach plików za pomocą działania usuwania usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/25/2019
ms.openlocfilehash: 00658b650cdc0b1752bb9f2f205420018c1d6edd
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58881787"
---
# <a name="delete-activity-in-azure-data-factory"></a>Usuń działanie w usłudze Azure Data Factory

Usuń działanie usługi Azure Data Factory służy do usuwania plików lub folderów z magazynu lokalnego przechowywania lub magazynie można przechowywać w chmurze. Użyj tego działania, aby wyczyścić lub archiwizować pliki, które są już potrzebne.

> [!WARNING]
> Nie można przywrócić usunięte pliki lub foldery. Należy zachować ostrożność podczas używania działania usuwania można usunąć plików lub folderów.

## <a name="best-practices"></a>Najlepsze praktyki

Poniżej przedstawiono niektóre zalecenia dotyczące używania działania usuwania:

-   Tworzenie kopii zapasowych plików przed usunięciem ich za pomocą działania usuwania w przypadku, gdy trzeba przywrócić je w przyszłości.

-   Upewnij się, że fabryka danych ma uprawnienia do zapisu na usuwanie folderów lub plików z magazynu magazynu.

-   Upewnij się, że nie powoduje usunięcia plików, które są zapisywane w tym samym czasie. 

-   Jeśli chcesz usunąć plików lub folderów z systemu lokalnego, upewnij się, że używasz własnego środowiska integration runtime z nieco większą niż 3,14.

## <a name="supported-data-stores"></a>Magazyny danych obsługiwanych

-   [Azure Blob Storage](connector-azure-blob-storage.md)
-   [Usługa Azure Data Lake Storage 1. generacji](connector-azure-data-lake-store.md)
-   [Usługa Azure Data Lake Storage 2. generacji](connector-azure-data-lake-storage.md)

### <a name="file-system-data-stores"></a>Magazyny danych systemu plików

-   [System plików](connector-file-system.md)
-   [FTP](connector-ftp.md)
-   [SFTP](connector-sftp.md)
-   [Amazon S3](connector-amazon-simple-storage-service.md)

## <a name="syntax"></a>Składnia

```json
{
    "name": "DeleteActivity",
    "type": "Delete",
    "typeProperties": {
        "dataset": {
            "referenceName": "<dataset name>",
            "type": "DatasetReference"
        },
        "recursive": true/false,
        "maxConcurrentConnections": <number>,
        "enableLogging": true/false,
        "logStorageSettings": {
            "linkedServiceName": {
                "referenceName": "<name of linked service>",
                "type": "LinkedServiceReference"
            },
            "path": "<path to save log file>"
        }
    }
}
```

## <a name="type-properties"></a>Właściwości typu

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| Zestaw danych | Zawiera dokumentację zestawu danych, aby określić, które pliki lub folder do usunięcia | Yes |
| cykliczne | Wskazuje, czy pliki są rekursywnie usuniętych z podfolderów lub tylko z określonego folderu.  | Nie. Wartość domyślna to `false`. |
| MaxConcurrentConnections | Liczba połączeń, aby połączyć się z magazynem magazynu jednocześnie związanych z usuwaniem folder lub wybrane pliki.   |  Nie. Wartość domyślna to `1`. |
| EnableLogging | Wskazuje, czy należy on do rejestrowania nazw folderów lub plików, które zostały usunięte. W przypadku opcji true należy dodatkowo podać konto magazynu, aby zapisać plik dziennika tak, aby można było śledzić zachowania działania usuwania, zapoznając się w pliku dziennika. | Nie |
| logStorageSettings | Dotyczy tylko kiedy enablelogging = true.<br/><br/>Grupy właściwości magazynu, które można określić, gdzie chcesz zapisać plik dziennika, zawierającą nazwy folderu lub pliku, które zostały usunięte przez działanie usuwania. | Nie |
| linkedServiceName | Dotyczy tylko kiedy enablelogging = true.<br/><br/>Połączona usługa [usługi Azure Storage](connector-azure-blob-storage.md#linked-service-properties), [usługi Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#linked-service-properties), lub [usługi Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) do przechowywania plików dziennika, który zawiera folder i nazwę pliku został usunięty przez działanie Delete. | Nie |
| ścieżka | Dotyczy tylko kiedy enablelogging = true.<br/><br/>Ścieżki, który ma zostać zapisany plik dziennika na koncie magazynu. Jeśli ścieżka nie zostanie określona, usługa utworzy kontener. | Nie |

## <a name="monitoring"></a>Monitorowanie

Istnieją dwa miejsca, w którym można wyświetlić i monitorować wyniki działania usuwania: 
-   Z danych wyjściowych działania usuwania.
-   Z pliku dziennika.

### <a name="sample-output-of-the-delete-activity"></a>Przykładowe dane wyjściowe działania Delete

```json
{ 
  "datasetName": "AmazonS3",
  "type": "AmazonS3Object",
  "prefix": "test",
  "bucketName": "adf",
  "recursive": true,
  "isWildcardUsed": false,
  "maxConcurrentConnections": 2,  
  "filesDeleted": 4,
  "logPath": "https://sample.blob.core.windows.net/mycontainer/5c698705-a6e2-40bf-911e-e0a927de3f07",
  "effectiveIntegrationRuntime": "MyAzureIR (West Central US)",
  "executionDuration": 650
}
```

### <a name="sample-log-file-of-the-delete-activity"></a>Przykładowy plik dziennika aktywności Delete

| Name (Nazwa) | Kategoria | Stan | Błąd |
|:--- |:--- |:--- |:--- |
| test1/yyy.json | Plik | Usunięte |  |
| test2/hello789.txt | Plik | Usunięte |  |
| Test2/test3/hello000.txt | Plik | Usunięte |  |
| test2/test3/zzz.json | Plik | Usunięte |  |

## <a name="examples-of-using-the-delete-activity"></a>Przykłady użycia działania Delete

### <a name="delete-specific-folders-or-files"></a>Usuń foldery lub pliki

Magazyn ma następującą strukturę folderów:

Główny /<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1 /<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1. txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_2 /<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4 txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_1 /<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;6.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_2 /<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;8.txt

Obecnie używasz, działanie Delete, aby usunąć folder lub wybrane pliki za pomocą kombinacji wartości różnych właściwości z zestawu danych i działań usuwania:

| folderPath (z zestawu danych) | Nazwa pliku (z zestawu danych) | cykliczne (z działania usuwania) | Dane wyjściowe |
|:--- |:--- |:--- |:--- |
| Główny / Folder_A_2 | NULL | False | Główny /<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1 /<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1. txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_2 /<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>4.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>5.csv</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_1 /<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;6.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_2 /<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;8.txt |
| Główny / Folder_A_2 | NULL | True | Główny /<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1 /<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1. txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;<strike>Folder_A_2/</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>4.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>5.csv</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>Folder_B_1 /</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>6.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>7.csv</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>Folder_B_2/</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>8.txt</strike> |
| Główny / Folder_A_2 | *.txt | False | Główny /<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1 /<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1. txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_2 /<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>4.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_1 /<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;6.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_2 /<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;8.txt |
| Główny / Folder_A_2 | *.txt | True | Główny /<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1 /<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1. txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_2 /<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>4.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_1 /<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>6.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_2 /<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>8.txt</strike> |

### <a name="periodically-clean-up-the-time-partitioned-folder-or-files"></a>Okresowo oczyszczać podzielona na partycje czasu folder lub wybrane pliki

Można utworzyć potoku, aby okresowo oczyszczać czasu partycjonowane folder lub wybrane pliki.  Na przykład struktura folderów jest podobna: `/mycontainer/2018/12/14/*.csv`.  Możesz korzystać z usługi ADF zmiennej systemowej z wyzwalacza harmonogramu, aby zidentyfikować, które folder lub wybrane pliki należy usunąć w każdym uruchomieniem potoku. 

#### <a name="sample-pipeline"></a>Przykładowy potok

```json
{
    "name": "cleanup_time_partitioned_folder",
    "properties": {
        "activities": [
            {
                "name": "DeleteOneFolder",
                "type": "Delete",
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "typeProperties": {
                    "dataset": {
                        "referenceName": "PartitionedFolder",
                        "type": "DatasetReference",
                        "parameters": {
                            "TriggerTime": {
                                "value": "@formatDateTime(pipeline().parameters.TriggerTime, 'yyyy/MM/dd')",
                                "type": "Expression"
                            }
                        }
                    },
                    "recursive": true,
                    "logStorageSettings": {
                        "linkedServiceName": {
                            "referenceName": "BloblinkedService",
                            "type": "LinkedServiceReference"
                        },
                        "path": "mycontainer/log"
                    },
                    "enableLogging": true
                }
            }
        ],
        "parameters": {
            "TriggerTime": {
                "type": "String"
            }
        }
    },
    "type": "Microsoft.DataFactory/factories/pipelines"
}
```

#### <a name="sample-dataset"></a>Przykładowy zestaw danych

```json
{
    "name": "PartitionedFolder",
    "properties": {
        "linkedServiceName": {
            "referenceName": "BloblinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "TriggerTime": {
                "type": "String"
            }
        },
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": {
                "value": "@concat('mycontainer/',dataset().TriggerTime)",
                "type": "Expression"
            }
        }
    },
    "type": "Microsoft.DataFactory/factories/datasets"
}
```

#### <a name="sample-trigger"></a>Przykładowy wyzwalacz

```json
{
    "name": "DailyTrigger",
    "properties": {
        "runtimeState": "Started",
        "pipelines": [
            {
                "pipelineReference": {
                    "referenceName": "cleanup_time_partitioned_folder",
                    "type": "PipelineReference"
                },
                "parameters": {
                    "TriggerTime": "@trigger().scheduledTime"
                }
            }
        ],
        "type": "ScheduleTrigger",
        "typeProperties": {
            "recurrence": {
                "frequency": "Day",
                "interval": 1,
                "startTime": "2018-12-13T00:00:00.000Z",
                "timeZone": "UTC",
                "schedule": {
                    "minutes": [
                        59
                    ],
                    "hours": [
                        23
                    ]
                }
            }
        }
    }
}
```

### <a name="clean-up-the-expired-files-that-were-last-modified-before-201811"></a>Wyczyścić wygasłe pliki, które zostały ostatnio zmodyfikowane przed 2018.1.1

Można utworzyć potoku, aby wyczyścić stare lub wygasłe plików przy użyciu filtru atrybutu pliku: "LastModified" w zestawie danych.  

#### <a name="sample-pipeline"></a>Przykładowy potok

```json
{
    "name": "CleanupExpiredFiles",
    "properties": {
        "activities": [
            {
                "name": "DeleteFilebyLastModified",
                "type": "Delete",
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "typeProperties": {
                    "dataset": {
                        "referenceName": "BlobFilesLastModifiedBefore201811",
                        "type": "DatasetReference"
                    },
                    "recursive": true,
                    "logStorageSettings": {
                        "linkedServiceName": {
                            "referenceName": "BloblinkedService",
                            "type": "LinkedServiceReference"
                        },
                        "path": "mycontainer/log"
                    },
                    "enableLogging": true
                }
            }
        ]
    }
}
```

#### <a name="sample-dataset"></a>Przykładowy zestaw danych

```json
{
    "name": "BlobFilesLastModifiedBefore201811",
    "properties": {
        "linkedServiceName": {
            "referenceName": "BloblinkedService",
            "type": "LinkedServiceReference"
        },
        "type": "AzureBlob",
        "typeProperties": {
            "fileName": "*",
            "folderPath": "mycontainer",
            "modifiedDatetimeEnd": "2018-01-01T00:00:00.000Z"
        }
    }
}
```

### <a name="move-files-by-chaining-the-copy-activity-and-the-delete-activity"></a>Przenieś pliki przez łańcuch działanie kopiowania i działanie Delete

Można przenieść pliku za pomocą działania kopiowania, aby skopiować plik, a następnie działanie delete, aby usunąć plik w potoku.  Gdy chcesz przenieść wiele plików, może użyć działaniu GetMetadata + działanie filtru + działanie Foreach + działania kopiowania i usuwania działań, jak w poniższym przykładzie:

> [!NOTE]
> Jeśli chcesz przenieść cały folder definiujący zestaw danych zawierający ścieżkę folderu, a następnie za pomocą działania kopiowania i działanie Delete, aby odwoływać się do tego samego zestawu danych reprezentująca folderem, należy zwrócić szczególną uwagę. Jest to spowodowane musisz upewnić się, że nie będą nowych plików przychodzących do folderu między operacji kopiowania i operacji usuwania.  W przypadku nowych plików otrzymywanych z folderu w tej chwili, gdy działania kopiowania ukończony zadanie kopiowania, ale działanie Delete nie została stared jest możliwe, że działanie usuwania spowoduje usunięcie tego nowego pliku nadchodzących, który nie został skopiowany do destinati na jeszcze przez usunięcie całego folderu. 

#### <a name="sample-pipeline"></a>Przykładowy potok

```json
{
    "name": "MoveFiles",
    "properties": {
        "activities": [
            {
                "name": "GetFileList",
                "type": "GetMetadata",
                "typeProperties": {
                    "dataset": {
                        "referenceName": "OneSourceFolder",
                        "type": "DatasetReference"
                    },
                    "fieldList": [
                        "childItems"
                    ]
                }
            },
            {
                "name": "FilterFiles",
                "type": "Filter",
                "dependsOn": [
                    {
                        "activity": "GetFileList",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    }
                ],
                "typeProperties": {
                    "items": {
                        "value": "@activity('GetFileList').output.childItems",
                        "type": "Expression"
                    },
                    "condition": {
                        "value": "@equals(item().type, 'File')",
                        "type": "Expression"
                    }
                }
            },
            {
                "name": "ForEachFile",
                "type": "ForEach",
                "dependsOn": [
                    {
                        "activity": "FilterFiles",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    }
                ],
                "typeProperties": {
                    "items": {
                        "value": "@activity('FilterFiles').output.value",
                        "type": "Expression"
                    },
                    "batchCount": 20,
                    "activities": [
                        {
                            "name": "CopyAFile",
                            "type": "Copy",
                            "policy": {
                                "timeout": "7.00:00:00",
                                "retry": 0,
                                "retryIntervalInSeconds": 30,
                                "secureOutput": false,
                                "secureInput": false
                            },
                            "typeProperties": {
                                "source": {
                                    "type": "BlobSource",
                                    "recursive": false
                                },
                                "sink": {
                                    "type": "BlobSink"
                                },
                                "enableStaging": false,
                                "dataIntegrationUnits": 0
                            },
                            "inputs": [
                                {
                                    "referenceName": "OneSourceFile",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "path": "myFolder",
                                        "filename": {
                                            "value": "@item().name",
                                            "type": "Expression"
                                        }
                                    }
                                }
                            ],
                            "outputs": [
                                {
                                    "referenceName": "OneDestinationFile",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "DestinationFileName": {
                                            "value": "@item().name",
                                            "type": "Expression"
                                        }
                                    }
                                }
                            ]
                        },
                        {
                            "name": "DeleteAFile",
                            "type": "Delete",
                            "dependsOn": [
                                {
                                    "activity": "CopyAFile",
                                    "dependencyConditions": [
                                        "Succeeded"
                                    ]
                                }
                            ],
                            "policy": {
                                "timeout": "7.00:00:00",
                                "retry": 0,
                                "retryIntervalInSeconds": 30,
                                "secureOutput": false,
                                "secureInput": false
                            },
                            "typeProperties": {
                                "dataset": {
                                    "referenceName": "OneSourceFile",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "path": "myFolder",
                                        "filename": {
                                            "value": "@item().name",
                                            "type": "Expression"
                                        }
                                    }
                                },
                                "logStorageSettings": {
                                    "linkedServiceName": {
                                        "referenceName": "BloblinkedService",
                                        "type": "LinkedServiceReference"
                                    },
                                    "path": "Container/log"
                                },
                                "enableLogging": true
                            }
                        }
                    ]
                }
            }
        ]
    }
}
```

#### <a name="sample-datasets"></a>Przykładowe zestawy danych

Zestaw danych używany przez działanie GetMetadata wyliczyć listy plików.

```json
{
    "name": "OneSourceFolder",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "type": "AzureBlob",
        "typeProperties": {
            "fileName": "",
            "folderPath": "myFolder"
        }
    }
}
```

Zestaw danych dla źródła danych używanego przez działanie kopiowania i działanie Delete.

```json
{
    "name": "OneSourceFile",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "path": {
                "type": "String"
            },
            "filename": {
                "type": "String"
            }
        },
        "type": "AzureBlob",
        "typeProperties": {
            "fileName": {
                "value": "@dataset().filename",
                "type": "Expression"
            },
            "folderPath": {
                "value": "@{dataset().path}",
                "type": "Expression"
            }
        }
    }
}
```

Zestaw danych jako miejsce docelowe danych używane przez działanie kopiowania.

```json
{
    "name": "OneDestinationFile",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "DestinationFileName": {
                "type": "String"
            }
        },
        "type": "AzureBlob",
        "typeProperties": {
            "fileName": {
                "value": "@dataset().DestinationFileName",
                "type": "Expression"
            },
            "folderPath": "mycontainer/dest"
        }
    }
}
```
## <a name="known-limitation"></a>Znane ograniczenia

-   Usuń działanie nie obsługuje usuwania listy folderów, opisanego przez symbol wieloznaczny.

-   Korzystając z filtru atrybutu pliku: modifiedDatetimeStart i modifiedDatetimeEnd, aby wybrać pliki do usunięcia, upewnij się ustawić "nazwa_pliku": "*" w zestawie danych.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat przenoszenia plików w usłudze Azure Data Factory.

-   [Narzędzie do kopiowania danych w usłudze Azure Data Factory](copy-data-tool.md)