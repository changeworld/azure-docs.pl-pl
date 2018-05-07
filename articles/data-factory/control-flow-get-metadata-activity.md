---
title: Uzyskiwanie metadanych działania w fabryce danych Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak SQL Server działania dotyczącego procedury składowanej umożliwia wywołanie procedury przechowywanej w bazie danych SQL Azure lub usługi Azure SQL Data Warehouse z potoku fabryki danych.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2018
ms.author: shlo
ms.openlocfilehash: 5c81c73bd563dd75103ed0fcb45cbc2205eed02a
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>Uzyskiwanie metadanych działania w fabryce danych Azure
GetMetadata działanie może być używane do pobierania **metadanych** wszystkich danych z fabryki danych Azure. To działanie jest obsługiwana tylko dla fabryki danych w wersji 2. Mogą być używane w następujących scenariuszach:

- Sprawdź poprawność informacji o metadanych danych
- Wyzwalanie potoku, gdy dane są gotowe / dostępne

Następujące funkcje są dostępne w przepływie sterowania:

- Dane wyjściowe działania GetMetadata można używać w wyrażeniach warunkowego do wykonywania sprawdzania poprawności.
- Potok mogą być wyzwalane, gdy warunek jest spełniony za pośrednictwem czy — do pętli

> [!NOTE]
> Ten artykuł dotyczy wersji 2 usługi Data Factory, która jest obecnie dostępna w wersji zapoznawczej. Jeśli używasz wersji 1 usługi fabryka danych, która jest ogólnie dostępna (GA), zobacz [dokumentacji V1 fabryki danych](v1/data-factory-introduction.md).

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Działanie GetMetadata przyjmuje zestawu danych jako wymagane dane wejściowe i wyświetla metadane dostępne jako dane wyjściowe działania. Obecnie są obsługiwane następujące łączniki z odpowiedniego pobieranie metadane:

### <a name="supported-connectors"></a>Obsługiwane łączniki

**Magazyn plików:**

| Łącznik/metadanych | Nazwa elementu<br>(plik lub folder) | ItemType<br>(plik lub folder) | rozmiar<br>(plik) | utworzone<br>(plik lub folder) | Ostatnia modyfikacja<br>(plik lub folder) |childItems<br>(folder) |contentMD5<br>(plik) | Struktura<br/>(plik) | ColumnCount<br>(plik) | istnieje<br>(plik lub folder) |
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| Obiekt bob Azure | √/√ | √/√ | √ | x/x | √/√ | √ | √ | √ | √ | √/√ |
| Azure Data Lake Store | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| Azure File Storage | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| System plików | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| SFTP | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| FTP | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |

**Relacyjna baza danych:**

| Łącznik/metadanych | Struktura | ColumnCount | istnieje |
|:--- |:--- |:--- |:--- |
| Azure SQL Database | √ | √ | √ |
| Azure SQL Data Warehouse | √ | √ | √ |
| Oprogramowanie SQL Server | √ | √ | √ |

### <a name="metadata-options"></a>Opcje metadanych

Na liście pól GetMetadata działania można pobrać można określić następujące typy metadanych:

| Typ metadanych | Opis |
|:--- |:--- |
| Nazwa elementu | Nazwa pliku lub folderu. |
| ItemType | Typ pliku lub folderu. Wartość wyjściowa to `File` lub `Folder`. |
| rozmiar | Rozmiar pliku w bajcie. Dotyczy tylko plików. |
| utworzone | Utworzone datetime pliku lub folderu. |
| Ostatnia modyfikacja | Ostatnia modyfikacja datetime pliku lub folderu. |
| childItems | Lista podfoldery i pliki znajdujące się w danym folderze. Dotyczy tylko do folderu. Wartość wyjściowa znajduje się lista nazwę i typ każdego elementu podrzędnego. |
| contentMD5 | MD5 pliku. Dotyczy tylko plików. |
| Struktura | Struktura danych wewnątrz pliku lub tabeli relacyjnej bazy danych. Wartość wyjściowa jest listę nazw kolumn i typ kolumny. |
| ColumnCount | Liczba kolumn w pliku lub relacyjne tabeli. |
| istnieje| Określa, czy plik/folder/tabela istnieje, lub nie. Uwaga tak długo, jak "istnieje" określono na liście pól GetaMetadata działania nie zakończyć się niepowodzeniem, nawet wtedy, gdy element (tabeli plików/folderów) nie istnieje; Zamiast tego zwraca `exists: false` w danych wyjściowych. |

## <a name="syntax"></a>Składnia

**GetMetadata działania:**

```json
{
    "name": "MyActivity",
    "type": "GetMetadata",
    "typeProperties": {
        "fieldList" : ["size", "lastModified", "structure"],
        "dataset": {
            "referenceName": "MyDataset",
            "type": "DatasetReference"
        }
    }
}
```

**Zestaw danych:**

```json
{
    "name": "MyDataset",
    "properties": {
    "type": "AzureBlob",
        "linkedService": {
            "referenceName": "StorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath":"container/folder",
            "Filename": "file.json",
            "format":{
                "type":"JsonFormat"
                "nestedSeperator": ","
            }
        }
    }
}
```

## <a name="type-properties"></a>Właściwości typu

Obecnie GetMetadata działania można pobrać następujące typy informacji metadanych.

Właściwość | Opis | Wymagane
-------- | ----------- | --------
listy pól | Wyświetla listę typów wymaganych informacji o metadanych. Zobacz szczegóły w [opcje metadanych](#metadata-options) sekcję na temat obsługiwanych metadanych. | Nie 
Zestaw danych | Zestaw danych odwołania, których działanie metadanych ma być pobrana przez działanie GetMetadata. Zobacz [obsługiwane możliwości](#supported-capabilities) sekcji obsługiwanych łączników i opisane w temacie łącznika w szczegółach składni zestawu danych. | Yes

## <a name="sample-output"></a>Przykładowe dane wyjściowe

Wynik GetMetadata jest wyświetlany w danych wyjściowych działania. Poniżej przedstawiono dwa przykłady z opcjami wyczerpujący metadanych wybrany w oknie Lista pól jako odwołanie:

### <a name="get-a-files-metadata"></a>Pobranie pliku metadanych

```json
{
  "exists": true,
  "itemName": "test.csv",
  "itemType": "File",
  "size": 104857600,
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "contentMD5": "cMauY+Kz5zDm3eWa9VpoyQ==",
  "structure": [
    {
        "name": "id",
        "type": "Int64"
    },
    {
        "name": "name",
        "type": "String"
    }
  ],
  "columnCount": 2
}
```

### <a name="get-a-folders-metadata"></a>Pobranie metadanych folderu

```json
{
  "exists": true,
  "itemName": "testFolder",
  "itemType": "Folder",
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "childItems": [
    {
      "name": "test.avro",
      "type": "File"
    },
    {
      "name": "folder hello",
      "type": "Folder"
    }
  ]
}
```

## <a name="next-steps"></a>Kolejne kroki
Zobacz inne działania przepływu sterowania obsługiwane przez fabrykę danych: 

- [Działanie Execute Pipeline](control-flow-execute-pipeline-activity.md)
- [Dla każdego działania](control-flow-for-each-activity.md)
- [Działanie Lookup](control-flow-lookup-activity.md)
- [Działania w sieci Web](control-flow-web-activity.md)