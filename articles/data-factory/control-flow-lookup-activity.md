---
title: Działanie LOOKUP w usłudze Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak Użyj działania Lookup do wyszukiwania wartości ze źródła zewnętrznego. Te dane wyjściowe mogą być dodatkowo przywoływane przez kolejne działania.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: shlo
ms.openlocfilehash: 4f0662a71ee14af3c2c1aafee210641fc8b51f1b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60768672"
---
# <a name="lookup-activity-in-azure-data-factory"></a>Działanie LOOKUP w usłudze Azure Data Factory

Działanie LOOKUP może pobrać zestawu danych z dowolnego źródła danych obsługiwane przez usługę Azure Data Factory. Użyj go w następującym scenariuszu:
- Dynamiczne określanie obiekty, które do wykonywania operacji w kolejne działania, zamiast twardych kodowania nazwę obiektu. Niektóre przykłady obiektu są pliki i tabel.

Działanie LOOKUP odczytuje i zwraca zawartość pliku konfiguracji lub tabeli. Zwraca wynik wykonania zapytań lub procedurze składowanej. Dane wyjściowe działania Lookup może służyć w kolejnych kopiowania lub działania przekształcania, jeśli jest to pojedyncza wartość. Dane wyjściowe może służyć w działaniu ForEach, jeśli jest to tablica atrybutów.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Następujące źródła danych są obsługiwane dla działania Lookup. Największa liczba wierszy, które mogą być zwrócone przez działanie Lookup wynosi 5000, rozmiar do 2 MB. Obecnie najdłuższy czas trwania dla wyszukiwania działanie przed upływem limitu czasu wynosi godzinę.

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores-for-lookup-activity.md)]

## <a name="syntax"></a>Składnia

```json
{
    "name": "LookupActivity",
    "type": "Lookup",
    "typeProperties": {
        "source": {
            "type": "<source type>"
            <additional source specific properties (optional)>
        },
        "dataset": { 
            "referenceName": "<source dataset name>",
            "type": "DatasetReference"
        },
        "firstRowOnly": false
    }
}
```

## <a name="type-properties"></a>Właściwości typu

Name (Nazwa) | Opis | Typ | Wymagana?
---- | ----------- | ---- | --------
Zestaw danych | Zawiera odwołanie do zestawu danych do wyszukiwania. Uzyskiwanie szczegółowych informacji z **właściwości zestawu danych** sekcji w każdym odpowiedni artykuł dotyczący łącznika. | Pary klucz/wartość | Tak
source | Zawiera właściwości specyficzne dla zestawu danych źródła, taka sama jak źródło działania kopiowania. Uzyskiwanie szczegółowych informacji z **właściwości działania kopiowania** sekcji w każdym odpowiedni artykuł dotyczący łącznika. | Pary klucz/wartość | Tak
firstRowOnly | Wskazuje, czy zwracać tylko pierwszy wiersz lub wszystkie wiersze. | Boolean | Nie. Wartość domyślna to `true`.

> [!NOTE]
> 
> * Źródło kolumny z **ByteArray** typu nie są obsługiwane.
> * **Struktura** nie jest obsługiwane w definicjach zestawów danych. Dla plików w formacie tekstowym Użyj wiersz nagłówka, aby podać nazwę kolumny.
> * Jeśli źródłem wyszukiwania jest plikiem JSON `jsonPathDefinition` ustawienie dla ich modyfikowania obiektu JSON nie jest obsługiwane. Całe obiekty zostaną pobrane.

## <a name="use-the-lookup-activity-result-in-a-subsequent-activity"></a>Użyj działania Lookup doprowadzić kolejne działania

Wynik wyszukiwania jest zwracany w `output` sekcji uruchomienia działania.

* **Gdy `firstRowOnly` ustawiono `true` (ustawienie domyślne)** , jest format danych wyjściowych, jak pokazano w poniższym kodzie. Wynik wyszukiwania znajduje się w stałym `firstRow` klucza. Aby użyć wyniku kolejne działania, użyj wzorca `@{activity('MyLookupActivity').output.firstRow.TableName}`.

    ```json
    {
        "firstRow":
        {
            "Id": "1",
            "TableName" : "Table1"
        }
    }
    ```

* **Gdy `firstRowOnly` ustawiono `false`** , jest format danych wyjściowych, jak pokazano w poniższym kodzie. A `count` pole wskazuje, ile rekordy są zwracane. Szczegółowe wartości są wyświetlane w obszarze stałym `value` tablicy. W takim przypadku następuje działanie Lookup [działanie Foreach](control-flow-for-each-activity.md). Możesz przekazać `value` macierz działanie ForEach `items` pola przy użyciu wzorca `@activity('MyLookupActivity').output.value`. Do dostępu do elementów w `value` tablicy, należy użyć następującej składni: `@{activity('lookupActivity').output.value[zero based index].propertyname}`. Może to być na przykład `@{activity('lookupActivity').output.value[0].tablename}`.

    ```json
    {
        "count": "2",
        "value": [
            {
                "Id": "1",
                "TableName" : "Table1"
            },
            {
                "Id": "2",
                "TableName" : "Table2"
            }
        ]
    } 
    ```

### <a name="copy-activity-example"></a>Przykład działania kopiowania
W tym przykładzie działanie kopiowania kopiuje dane z tabeli SQL w wystąpieniu usługi Azure SQL Database do magazynu obiektów Blob platformy Azure. Nazwa tabeli SQL jest przechowywana w pliku JSON w usłudze Blob storage. Działanie Lookup wyszukuje nazwy tabeli w czasie wykonywania. JSON jest modyfikowana dynamicznie przy użyciu tej metody. Nie ma potrzeby ponownego wdrażania potoków ani zestawów danych. 

W tym przykładzie pokazano wyszukiwania dla pierwszego wiersza. Wyszukiwanie we wszystkich wierszach, a aby połączyć w łańcuch wyniki za pomocą działania ForEach, zobacz przykłady w [zbiorcze kopiowanie wielu tabel przy użyciu usługi Azure Data Factory](tutorial-bulk-copy.md).

### <a name="pipeline"></a>Potok
Ten potok zawiera dwa działania: Wyszukiwanie i kopiowania. 

- Działanie Lookup jest skonfigurowany do używania **LookupDataset**, która odnosi się do lokalizacji w usłudze Azure Blob storage. Działanie Lookup odczytuje Nazwa tabeli SQL z pliku JSON w tej lokalizacji. 
- Działanie kopiowania używa danych wyjściowych działania Lookup, jest to nazwa tabeli SQL. **TableName** właściwość **SourceDataset** jest skonfigurowany do korzystania z danych wyjściowych działania Lookup. Skopiuj działanie kopiuje dane z tabeli SQL w lokalizacji w usłudze Azure Blob storage. Lokalizacja jest określona przez **SinkDataset** właściwości. 

```json
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "LookupActivity",
                "type": "Lookup",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "dataset": { 
                        "referenceName": "LookupDataset", 
                        "type": "DatasetReference" 
                    }
                }
            },
            {
                "name": "CopyActivity",
                "type": "Copy",
                "typeProperties": {
                    "source": { 
                        "type": "SqlSource", 
                        "sqlReaderQuery": "select * from @{activity('LookupActivity').output.firstRow.tableName}" 
                    },
                    "sink": { 
                        "type": "BlobSink" 
                    }
                },                
                "dependsOn": [ 
                    { 
                        "activity": "LookupActivity", 
                        "dependencyConditions": [ "Succeeded" ] 
                    }
                 ],
                "inputs": [ 
                    { 
                        "referenceName": "SourceDataset", 
                        "type": "DatasetReference" 
                    } 
                ],
                "outputs": [ 
                    { 
                        "referenceName": "SinkDataset", 
                        "type": "DatasetReference" 
                    } 
                ]
            }
        ]
    }
}
```

### <a name="lookup-dataset"></a>Zestaw danych wyszukiwania
**Wyszukiwania** zestaw danych jest **sourcetable.json** pliku w folderze wyszukiwanie usługi Azure Storage, określony przez **AzureStorageLinkedService** typu. 

```json
{
    "name": "LookupDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "lookup",
            "fileName": "sourcetable.json",
            "format": {
                "type": "JsonFormat",
                "filePattern": "SetOfObjects"
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="source-dataset-for-copy-activity"></a>**Źródło** zestawu danych działanie kopiowania
**Źródła** zestaw danych korzysta z danych wyjściowych działania Lookup, jest to nazwa tabeli SQL. Skopiuj działanie kopiuje dane z tej tabeli SQL w lokalizacji w usłudze Azure Blob storage. Lokalizacja jest określona przez **ujścia** zestawu danych. 

```json
{
    "name": "SourceDataset",
    "properties": {
        "type": "AzureSqlTable",
        "typeProperties":{
            "tableName": "@{activity('LookupActivity').output.firstRow.tableName}"
        },
        "linkedServiceName": {
            "referenceName": "AzureSqlLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="sink-dataset-for-copy-activity"></a>**Obiekt sink** zestawu danych działanie kopiowania
Działanie kopiowania kopiuje dane z tabeli SQL w celu **filebylookup.csv** w pliku **csv** folderu w usłudze Azure Storage. Plik jest określona przez **AzureStorageLinkedService** właściwości. 

```json
{
    "name": "SinkDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "csv",
            "fileName": "filebylookup.csv",
            "format": {
                "type": "TextFormat"                                                                    
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Połączona usługa Azure Storage
To konto magazynu zawiera plik JSON o nazwach tabel SQL. 

```json
{
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "value": "DefaultEndpointsProtocol=https;AccountName=<StorageAccountName>;AccountKey=<StorageAccountKey>",
                "type": "SecureString"
            }
        }
    },
        "name": "AzureStorageLinkedService"
}
```

### <a name="azure-sql-database-linked-service"></a>Połączona usługa Azure SQL Database
To wystąpienie usługi Azure SQL Database zawiera dane, które mają być kopiowane do magazynu obiektów Blob. 

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": {
                "value": "Server=<server>;Initial Catalog=<database>;User ID=<user>;Password=<password>;",
                "type": "SecureString"
            }
        }
    }
}
```

### <a name="sourcetablejson"></a>sourcetable.json

#### <a name="set-of-objects"></a>Zestaw obiektów

```json
{
  "Id": "1",
  "tableName": "Table1"
}
{
   "Id": "2",
  "tableName": "Table2"
}
```

#### <a name="array-of-objects"></a>Tablica obiektów

```json
[ 
    {
        "Id": "1",
        "tableName": "Table1"
    },
    {
        "Id": "2",
        "tableName": "Table2"
    }
]
```

## <a name="limitations-and-workarounds"></a>Ograniczenia i rozwiązania

Poniżej przedstawiono pewne ograniczenia, działanie Lookup i sugerowane rozwiązania problemu.

| Ograniczenia | Obejście |
|---|---|
| Działanie Lookup ma więcej niż 5000 wierszy, a maksymalny rozmiar wynoszący 2 MB. | Zaprojektuj potoku dwupoziomowej, gdzie zewnętrzne potok wykonuje iterację na wewnętrzny potoku, który pobiera dane, które nie może przekraczać maksymalna liczba wierszy lub rozmiaru. |
| | |

## <a name="next-steps"></a>Kolejne kroki
Zobacz inne działania przepływu sterowania obsługiwanych przez usługę Data Factory: 

- [Działanie EXECUTE Pipeline](control-flow-execute-pipeline-activity.md)
- [Działanie ForEach](control-flow-for-each-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)
- [Działanie Web](control-flow-web-activity.md)
