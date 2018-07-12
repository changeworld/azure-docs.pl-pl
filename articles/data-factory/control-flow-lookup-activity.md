---
title: Działanie LOOKUP w usłudze Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak Użyj działania lookup do wyszukiwania wartości ze źródła zewnętrznego. Do tych danych wyjściowych mogą także odwoływać się kolejne działania.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: shlo
ms.openlocfilehash: 25ed439674fcf7136e29034eb97e0652ae9ba111
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38237836"
---
# <a name="lookup-activity-in-azure-data-factory"></a>Działanie LOOKUP w usłudze Azure Data Factory

Działanie LOOKUP może służyć do pobierania zestawu danych z dowolnego źródła danych obsługiwane przez usługi ADF.  Może służyć w następującym scenariuszu:
- Dynamiczne określanie obiekty, które (pliki, tabele itp.) do wykonywania operacji w kolejne działania, zamiast kodować nazwa obiektu

Działanie LOOKUP może odczytywać i zwróć treść pliku konfiguracji, Tabela konfiguracji lub wynik wykonania zapytań lub procedurze składowanej.  Dane wyjściowe działania Lookup mogą być używane w kolejnych kopiowania lub działania przekształcania, jeśli jest to pojedyncza wartość lub używane w działaniu ForEach, jeśli jest to tablica atrybutów.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Następujące źródła danych są obsługiwane dla wyszukiwania. Maksymalna liczba wierszy mogą być zwrócone przez wyszukiwanie jest działanie **5000**, aż do **2MB** rozmiar. I obecnie maksymalny czas trwania działania Lookup przed upływem limitu czasu wynosi godzinę.

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
zestaw danych | Zawiera odwołanie do zestawu danych do wyszukiwania. Uzyskaj szczegółowe informacje z sekcji "Właściwości zestawu danych" w każdym odpowiedni artykuł dotyczący łącznika. | Pary klucz/wartość | Yes
source | Zawiera właściwości specyficzne dla zestawu danych źródła, taka sama jak źródło działania kopiowania. Uzyskaj szczegółowe informacje w sekcji "Właściwości działania kopiowania" w każdym odpowiedni artykuł dotyczący łącznika. | Pary klucz/wartość | Yes
firstRowOnly | Wskazuje, czy zwracać tylko pierwszy wiersz lub wszystkie wiersze. | Wartość logiczna | Nie. Wartość domyślna to `true`.

**Pamiętaj o następujących kwestiach:**

1. Źródłowa kolumna o typie ByteArray nie jest obsługiwane.
2. Struktura nie jest obsługiwana w definicji zestawu danych. Dla plików w formacie tekstowym w szczególności służy wiersz nagłówka o podanie nazwy kolumny.
3. Jeśli źródłem wyszukiwania jest pliki JSON, `jsonPathDefinition` ustawienie ponownie kształtowania obiekt JSON nie jest obsługiwane, całą obiektów, zostanie pobrana.

## <a name="use-the-lookup-activity-result-in-a-subsequent-activity"></a>Użyj działania lookup doprowadzić kolejne działania

Wynik wyszukiwania jest zwracany w `output` sekcji uruchomienia działania.

* **Gdy `firstRowOnly` ustawiono `true` (ustawienie domyślne)**, jest format danych wyjściowych, jak pokazano w poniższym kodzie. Wynik wyszukiwania znajduje się w stałym `firstRow` klucza. Aby użyć wyniku kolejne działania, użyj wzorca `@{activity('MyLookupActivity').output.firstRow.TableName}`.

    ```json
    {
        "firstRow":
        {
            "Id": "1",
            "TableName" : "Table1"
        }
    }
    ```

* **Gdy `firstRowOnly` ustawiono `false`** , jest format danych wyjściowych, jak pokazano w poniższym kodzie. A `count` pole wskazuje, ile rekordy są zwracane, a szczegółowe wartości są wyświetlane w obszarze stałym `value` tablicy. W takim przypadku działanie wyszukiwania zwykle następuje [działanie Foreach](control-flow-for-each-activity.md). Możesz przekazać `value` macierz działanie ForEach `items` pola przy użyciu wzorca `@activity('MyLookupActivity').output.value`. Do dostępu do elementów w `value` tablicy, należy użyć następującej składni: `@{activity('lookupActivity').output.value[zero based index].propertyname}`. Oto przykład: `@{activity('lookupActivity').output.value[0].tablename}`.

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

## <a name="example"></a>Przykład
W tym przykładzie działanie kopiowania kopiuje dane z tabeli SQL w wystąpieniu usługi Azure SQL Database do magazynu obiektów Blob platformy Azure. Nazwa tabeli SQL jest przechowywana w pliku JSON w usłudze Blob storage. Działanie lookup wyszukuje nazwy tabeli w czasie wykonywania. Takie podejście umożliwia JSON do zmodyfikowania dynamicznie bez konieczności ponownego wdrażania potoków ani zestawów danych. 

W tym przykładzie pokazano wyszukiwania dla pierwszego wiersza. Wyszukiwanie we wszystkich wierszach, a aby połączyć w łańcuch wyniki za pomocą działania ForEach, zobacz przykłady w [zbiorcze kopiowanie wielu tabel przy użyciu usługi Azure Data Factory](tutorial-bulk-copy.md).

### <a name="pipeline"></a>Potok
Ten potok zawiera dwa działania: *wyszukiwania* i *kopiowania*. 

- Działanie lookup jest skonfigurowany do używania LookupDataset, który odnosi się do lokalizacji w usłudze Azure Blob storage. Działanie lookup odczytuje Nazwa tabeli SQL z pliku JSON w tej lokalizacji. 
- Działanie kopiowania używa danych wyjściowych działania lookup (nazwa tabeli SQL). Właściwość tableName źródłowy zestaw danych (SourceDataset) jest skonfigurowany do używania danych wyjściowych działania lookup. Działanie kopiowania kopiuje dane z tabeli SQL w lokalizacji w usłudze Azure Blob storage, który jest określony przez właściwość SinkDataset. 


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
Zestaw danych wyszukiwania, który odwołuje się do *sourcetable.json* pliku w folderze wyszukiwanie usługi Azure Storage, który jest określony przez typ AzureStorageLinkedService. 

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

### <a name="source-dataset-for-the-copy-activity"></a>Zestaw danych źródłowych dla działania kopiowania
Źródłowy zestaw danych korzysta z danych wyjściowych działania lookup, jest to nazwa tabeli SQL. Działanie kopiowania kopiuje dane z tej tabeli SQL w lokalizacji w usłudze Azure Blob storage, który jest określony przez zestaw danych ujścia. 

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

### <a name="sink-dataset-for-the-copy-activity"></a>Ujścia zestawu danych działanie kopiowania
Działanie kopiowania kopiuje dane z tabeli SQL w celu *filebylookup.csv* w pliku *csv* folderu w usłudze Azure storage, który jest określony przez właściwość AzureStorageLinkedService. 

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

## <a name="next-steps"></a>Kolejne kroki
Zobacz inne działania przepływu sterowania, które są obsługiwane przez usługę Data Factory: 

- [Działanie EXECUTE Pipeline](control-flow-execute-pipeline-activity.md)
- [Dla każdego działania](control-flow-for-each-activity.md)
- [Działanie GetMetaData](control-flow-get-metadata-activity.md)
- [Działanie Web](control-flow-web-activity.md)
