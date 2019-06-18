---
title: Pobieranie metadanych działania w usłudze Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można użyć działaniu GetMetadata w potoku usługi fabryka danych.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: ''
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: jingwang
ms.openlocfilehash: 78f63b4f46fe5479d4d0fd5849ad80536d8a137c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61346915"
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>Pobieranie metadanych działania w usłudze Azure Data Factory

Działanie GetMetadata może być używane do pobierania **metadanych** dowolnych danych w usłudze Azure Data Factory. To działanie może być używane w następujących scenariuszach:

- Sprawdź poprawność informacji o metadanych dowolnych danych
- Wyzwalanie potoku, gdy dane są gotowe / dostępne

Następujące funkcje są dostępne w przepływie sterowania:

- Dane wyjściowe z działaniu GetMetadata może służyć w wyrażeniach warunkowych do wykonywania sprawdzania poprawności.
- Potok może być wyzwalane, gdy warunek jest spełniony przy użyciu czy — aż do pętli

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Działanie GetMetadata przyjmuje zestawu danych jako wymagane dane wejściowe i generuje informacje o metadanych dostępne jako dane wyjściowe działania. Obecnie są obsługiwane następujące łączniki za pomocą odpowiedniego Pobieranie metadanych i rozmiar maksymalny metadanych obsługiwanych jest maksymalnie **1MB**.

>[!NOTE]
>Po uruchomieniu działaniu GetMetadata na własne środowisko IR najnowsze możliwości jest obsługiwana w wersji 3.6 lub nowszym. 

### <a name="supported-connectors"></a>Obsługiwane łączniki

**Magazyn plików:**

| Łącznik/metadanych | itemName<br>(plik/folder) | itemType<br>(plik/folder) | size<br>(file) | Utworzone<br>(plik/folder) | lastModified<br>(plik/folder) |childItems<br>(folder) |contentMD5<br>(file) | structure<br/>(file) | columnCount<br>(file) | Istnieje<br>(plik/folder) |
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| Amazon S3 | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| Usługa Google Cloud Storage | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| Obiekt blob platformy Azure | √/√ | √/√ | √ | x/x | √/√* | √ | √ | √ | √ | √/√ |
| Usługa Azure Data Lake Storage 1. generacji | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| Usługa Azure Data Lake Storage 2. generacji | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| Azure File Storage | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| System plików | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| SFTP | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| Protokół FTP | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |

- Dla dostawców Amazon S3 i magazynu Sloud Google `lastModified` ma zastosowanie do zasobnika i klucza, ale nie wirtualnego folderu; i `exists` odnosi się do zasobnika i klucza, ale nie do prefiksu lub folder wirtualny.
- Dla obiektów Blob platformy Azure `lastModified` ma zastosowanie do kontenerów i obiektów blob, ale folder nie jest wirtualny.

**Relacyjna baza danych:**

| Łącznik/metadanych | structure | columnCount | Istnieje |
|:--- |:--- |:--- |:--- |
| Azure SQL Database | √ | √ | √ |
| Wystąpienie zarządzane usługi Azure SQL Database | √ | √ | √ |
| Azure SQL Data Warehouse | √ | √ | √ |
| Oprogramowanie SQL Server | √ | √ | √ |

### <a name="metadata-options"></a>Opcje metadanych

Na liście pól działanie GetMetadata do pobrania można określić następujące typy metadanych:

| Typ metadanych | Opis |
|:--- |:--- |
| itemName | Nazwa pliku lub folderu. |
| itemType | Typ pliku lub folderu. Wartość wyjściowa to `File` lub `Folder`. |
| size | Rozmiar pliku w bajtach. Dotyczy tylko plików. |
| Utworzone | Data i godzina utworzonego pliku lub folderu. |
| lastModified | Data ostatniej modyfikacji daty i godziny pliku lub folderu. |
| childItems | Lista podfoldery i pliki znajdujące się w danym folderze. Dotyczy tylko do folderu. Wartość wyjściowa jest listę nazw i typ każdego elementu podrzędnego. |
| contentMD5 | Algorytm MD5 pliku. Dotyczy tylko plików. |
| structure | Struktura danych wewnątrz pliku lub tabeli w relacyjnej bazie danych. Wartość wyjściowa jest lista nazwa kolumny i typ kolumny. |
| columnCount | Liczba kolumn w pliku lub tabeli. |
| Istnieje| Czy plik/folder/tabeli istnieje, czy nie. Należy pamiętać, jeśli "istnieje" jest określony na liście pól GetaMetadata, działanie nie będą się nie powieść, nawet wtedy, gdy nie istnieje element (plik/folder/table); Zamiast tego zwraca `exists: false` w danych wyjściowych. |

>[!TIP]
>Aby sprawdzić, czy plik/folder/tabela istnieje lub nie, należy określić `exists` na liście pól działanie GetMetadata, można sprawdzić `exists: true/false` wynika z danych wyjściowych działania. Jeśli `exists` nie jest skonfigurowany na liście pól, GetMetadata, działanie zakończy się niepowodzeniem, gdy obiekt nie zostanie znaleziony.

## <a name="syntax"></a>Składnia

**Działanie GetMetadata:**

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
            "filename": "file.json",
            "format":{
                "type":"JsonFormat"
            }
        }
    }
}
```

## <a name="type-properties"></a>Właściwości typu

Obecnie w działaniu GetMetadata można pobrać następujące rodzaje informacji o metadanych.

Właściwość | Opis | Wymagane
-------- | ----------- | --------
fieldList | Zawiera listę typów wymaganych informacji o metadanych. Szczegółowe informacje w [opcje metadanych](#metadata-options) sekcję dotyczącą obsługiwanych metadanych. | Yes 
Zestaw danych | Zestaw danych odwołania, którego działania metadanych ma być pobierane przez działanie GetMetadata. Zobacz [obsługiwane możliwości](#supported-capabilities) sekcji na obsługiwanych łączników i odnoszą się do temat dotyczący łączników na potrzeby szczegółów składni zestawu danych. | Yes

## <a name="sample-output"></a>Przykładowe dane wyjściowe

Wynik GetMetadata jest wyświetlany w danych wyjściowych działania. Poniżej przedstawiono dwa przykłady z opcjami wyczerpujący metadanych wybrany na liście pól jako odwołanie. Aby użyć wyniku kolejne działania, użyj wzorca `@{activity('MyGetMetadataActivity').output.itemName}`.

### <a name="get-a-files-metadata"></a>Pobierz metadane pliku

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

### <a name="get-a-folders-metadata"></a>Pobierz metadane folderu

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
Zobacz inne działania przepływu sterowania obsługiwanych przez usługę Data Factory: 

- [Działanie Execute Pipeline](control-flow-execute-pipeline-activity.md)
- [Dla każdego działania](control-flow-for-each-activity.md)
- [Działanie Lookup](control-flow-lookup-activity.md)
- [Działanie internetowe](control-flow-web-activity.md)
