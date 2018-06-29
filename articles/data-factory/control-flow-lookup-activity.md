---
title: Działania wyszukiwania w fabryce danych Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak działanie wyszukiwania służy do odszukania wartości ze źródła zewnętrznego. Do tych danych wyjściowych mogą także odwoływać się kolejne działania.
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
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37059064"
---
# <a name="lookup-activity-in-azure-data-factory"></a>Działania wyszukiwania w fabryce danych Azure

Działanie wyszukiwania może być używane do pobierania zestawu danych z dowolnego źródła danych obsługiwane ADF.  Mogą być używane w następującym scenariuszu:
- Dynamiczne określanie obiekty, które (plików, tabel, itp.) do działania na kolejne działania, zamiast kodować nazwa obiektu

Działania wyszukiwania może odczytywać i zwrócić zawartość zgodnie z pliku konfiguracji, Tabela konfiguracji lub wynik wykonywania zapytań lub procedurze składowanej.  Dane wyjściowe działania wyszukiwania można używane w kolejnych kopii lub transformacji działania, jeśli jest to wartość singleton lub używany w działaniu ForEach przypadku tablicę z atrybutami.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Następujące źródła danych są obsługiwane dla wyszukiwania. Maksymalna liczba wierszy może być zwracany przez działanie wyszukiwania **5000**, aż do **2MB** rozmiar. I obecnie maksymalny czas trwania działania wyszukiwania przed upływem limitu czasu to jedna godzina.

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
Zestaw danych | Zawiera odwołania do zestawu danych do wyszukiwania. Uzyskiwanie szczegółowych informacji w sekcji "Właściwości zestawu danych" w każdym artykule odpowiedni łącznik. | Para klucza i wartości | Yes
source | Zawiera właściwości specyficzne dla zestawu danych źródła, taki sam jak źródło działania kopiowania. Uzyskiwanie szczegółowych informacji w sekcji "Kopiuj właściwości działania" w każdym artykule odpowiedni łącznik. | Para klucza i wartości | Yes
firstRowOnly | Wskazuje, czy mają być zwracane tylko pierwszy wiersz lub wszystkie wiersze. | Wartość logiczna | Nie. Wartość domyślna to `true`.

**Pamiętaj o następujących kwestiach:**

1. Kolumna źródłowa ByteArray typu nie jest obsługiwane.
2. Struktura nie jest obsługiwana w definicji zestawu danych. W przypadku plików tekstowych format w szczególności służy wiersz nagłówka do określenia nazwy kolumny.
3. Jeśli źródłem wyszukiwania jest pliki JSON, `jsonPathDefinition` ustawienie ponownie kształtowania obiekt JSON nie jest obsługiwane, całą obiekty zostaną pobrane.

## <a name="use-the-lookup-activity-result-in-a-subsequent-activity"></a>Użyj wyniku działania wyszukiwania w następnych działań

Wynik wyszukiwania jest zwracany w `output` sekcji wynik uruchomienia działania.

* **Gdy `firstRowOnly` ustawiono `true` (ustawienie domyślne)**, jest format danych wyjściowych, jak pokazano w poniższym kodzie. Wynik wyszukiwania podlega ustalonego `firstRow` klucza. Aby użyć wyniku w następnych działań, użyj wzorzec `@{activity('MyLookupActivity').output.firstRow.TableName}`.

    ```json
    {
        "firstRow":
        {
            "Id": "1",
            "TableName" : "Table1"
        }
    }
    ```

* **Gdy `firstRowOnly` ustawiono `false`** , jest format danych wyjściowych, jak pokazano w poniższym kodzie. A `count` pole wskazuje liczbę rekordów są zwracane i szczegółowe wartości są wyświetlane w obszarze ustalonego `value` tablicy. W takim przypadku działanie wyszukiwania jest zazwyczaj następuje [działania Foreach](control-flow-for-each-activity.md). Można przekazać `value` tablicy do działania ForEach `items` pole przy użyciu struktury `@activity('MyLookupActivity').output.value`. Do dostępu do elementów w `value` tablicy, należy użyć następującej składni: `@{activity('lookupActivity').output.value[zero based index].propertyname}`. Oto przykład: `@{activity('lookupActivity').output.value[0].tablename}`.

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
W tym przykładzie działanie kopiowania kopiuje dane z tabeli SQL w wystąpieniu bazy danych SQL Azure do magazynu obiektów Blob Azure. Nazwa tabeli SQL jest przechowywana w pliku JSON w magazynie obiektów Blob. Działania wyszukiwania wyszukuje nazwy tabeli w czasie wykonywania. Takie podejście umożliwia JSON można zmodyfikować dynamicznie bez konieczności ponownego wdrażania potoków ani zestawów danych. 

W tym przykładzie przedstawiono wyszukiwania dla pierwszego wiersza. Wyszukiwanie wszystkich wierszy i tworzyć łańcuchy wyniki z działania ForEach, zobacz próbek w [skopiować wielu tabel zbiorczo za pomocą fabryki danych Azure](tutorial-bulk-copy.md).

### <a name="pipeline"></a>Potok
Ten potok zawiera dwa działania: *wyszukiwania* i *kopiowania*. 

- Działania wyszukiwania jest skonfigurowany do używania LookupDataset, która odwołuje się do lokalizacji w magazynie obiektów Blob Azure. Działania wyszukiwania odczytuje nazwę tabeli SQL z pliku JSON w tej lokalizacji. 
- Działanie kopiowania korzysta z danych wyjściowych działania wyszukiwania (nazwa tabeli SQL). Właściwość tableName w źródłowym zestawie danych (SourceDataset) jest skonfigurowany do używania dane wyjściowe działania wyszukiwania. Działanie kopiowania kopiuje dane z tabeli SQL do lokalizacji w magazynie obiektów Blob platformy Azure, który jest określony przez właściwość SinkDataset. 


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
Zestaw danych wyszukiwania odwołuje się do *sourcetable.json* plików w folderze wyszukiwania usługi Azure Storage, który jest określony przez typ AzureStorageLinkedService. 

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

### <a name="source-dataset-for-the-copy-activity"></a>Źródło danych dla działania kopiowania
Dane wyjściowe działania wyszukiwania, czyli nazwa tabeli SQL korzysta z zestawu źródła danych. Działanie kopiowania kopiuje dane z tej tabeli SQL do lokalizacji w magazynie obiektów Blob platformy Azure, określonym przez obiekt sink zestawu danych. 

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

### <a name="sink-dataset-for-the-copy-activity"></a>Obiekt sink zestawu danych dla aktywności kopiowania
Działanie kopiowania kopiuje dane z tabeli SQL *filebylookup.csv* w pliku *csv* folderu w magazynie Azure, który jest określony przez właściwość AzureStorageLinkedService. 

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
To konto magazynu zawiera plik JSON nazw tabel SQL. 

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
To wystąpienie bazy danych SQL Azure zawiera dane, które ma zostać skopiowany do magazynu obiektów Blob. 

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
Zobacz inne działania przepływu sterowania, które są obsługiwane przez fabrykę danych: 

- [Wykonanie działania potoku](control-flow-execute-pipeline-activity.md)
- [Dla każdego działania](control-flow-for-each-activity.md)
- [Uzyskiwanie metadanych działania](control-flow-get-metadata-activity.md)
- [Działanie Web](control-flow-web-activity.md)
