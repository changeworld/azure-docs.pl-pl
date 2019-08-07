---
title: Działanie pobierania metadanych w Azure Data Factory | Microsoft Docs
description: Dowiedz się, jak używać działania GetMetadata w potoku Data Factory.
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
ms.date: 08/06/2019
ms.author: jingwang
ms.openlocfilehash: b819a990b9f607aaf70bf2e16a5857de3f7306cc
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68827496"
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>Działanie pobierania metadanych w Azure Data Factory

Działanie GetMetadata może służyć do pobierania **metadanych** dowolnych danych w Azure Data Factory. To działanie może być używane w następujących scenariuszach:

- Weryfikowanie informacji o metadanych dowolnych danych
- Wyzwalanie potoku, gdy dane są gotowe/dostępne

W przepływie sterowania jest dostępna następująca funkcja:

- Dane wyjściowe z działania GetMetadata mogą być używane w wyrażeniach warunkowych do sprawdzenia poprawności.
- Potok może być wyzwalany, gdy warunek jest spełniony przez pętlę do-until

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Działanie GetMetadata przyjmuje zestaw danych jako wymaganą wartość wejściową, a informacje wyjściowe metadanych są dostępne jako dane wyjściowe działania. Obecnie obsługiwane są następujące łączniki z odpowiednimi metadanymi do pobierania, a maksymalny obsługiwany rozmiar metadanych to **1 MB**.

>[!NOTE]
>W przypadku uruchamiania działania GetMetadata na Integration Runtime samoobsługowym, najnowsza funkcja jest obsługiwana w wersji 3,6 lub nowszej. 

### <a name="supported-connectors"></a>Obsługiwane łączniki

**Magazyn plików:**

| Łącznik/metadane | itemName<br>(plik/folder) | itemType<br>(plik/folder) | size<br>rozszerzeniem | utworzone<br>(plik/folder) | lastModified<br>(plik/folder) |childItems<br>system32\drivers\etc |contentMD5<br>rozszerzeniem | structure<br/>rozszerzeniem | Kolumn<br>rozszerzeniem | istniejący<br>(plik/folder) |
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| [Amazon S3](connector-amazon-simple-storage-service.md) | √/√ | √/√ | √ | x/x | √/√ * | √ | x | √ | √ | √/√ * |
| [Google Cloud Storage](connector-google-cloud-storage.md) | √/√ | √/√ | √ | x/x | √/√ * | √ | x | √ | √ | √/√ * |
| [Azure Blob](connector-azure-blob-storage.md) | √/√ | √/√ | √ | x/x | √/√ * | √ | √ | √ | √ | √/√ |
| [Usługa Azure Data Lake Storage 1. generacji](connector-azure-data-lake-store.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Azure File Storage](connector-azure-file-storage.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [System plików](connector-file-system.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [SFTP](connector-sftp.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [FTP](connector-ftp.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |

- W przypadku usługi Amazon S3 i usługi Google Cloud `lastModified` Storage stosuje się do zasobnika i klucza, ale nie do folderu `exists` wirtualnego; i dotyczy zasobnika i klucza, ale nie prefiks lub folderu wirtualnego.
- W przypadku obiektów blob platformy `lastModified` Azure ma zastosowanie do kontenera i obiektu BLOB, ale nie do folderu wirtualnego.

**Relacyjna baza danych:**

| Łącznik/metadane | structure | Kolumn | istniejący |
|:--- |:--- |:--- |:--- |
| [Azure SQL Database](connector-azure-sql-database.md) | √ | √ | √ |
| [Wystąpienie zarządzane usługi Azure SQL Database](connector-azure-sql-database-managed-instance.md) | √ | √ | √ |
| [Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md) | √ | √ | √ |
| [SQL Server](connector-sql-server.md) | √ | √ | √ |

### <a name="metadata-options"></a>Opcje metadanych

Następujące typy metadanych można określić na liście pól działania GetMetadata do pobrania:

| Typ metadanych | Opis |
|:--- |:--- |
| itemName | Nazwa pliku lub folderu. |
| itemType | Typ pliku lub folderu. Wartość wyjściowa `File` to `Folder`lub. |
| size | Rozmiar pliku w bajtach. Dotyczy tylko pliku. |
| utworzone | Utworzono datę i godzinę dla pliku lub folderu. |
| lastModified | Data i godzina ostatniej modyfikacji pliku lub folderu. |
| childItems | Lista podfolderów i plików znajdujących się w danym folderze. Dotyczy tylko folderu. Wartość wyjściowa to lista nazw i typów każdego elementu podrzędnego. |
| contentMD5 | MD5 pliku. Dotyczy tylko pliku. |
| structure | Struktura danych w tabeli plik lub relacyjna baza danych. Wartość wyjściowa to lista nazw kolumn i typów kolumn. |
| Kolumn | Liczba kolumn wewnątrz pliku lub tabeli relacyjnej. |
| istniejący| Czy plik/folder/tabela istnieje, czy nie. Uwaga Jeśli na liście pól GetaMetadata zostanie określona wartość "istnieje", działanie nie powiedzie się, nawet jeśli element (plik/folder/tabela) nie istnieje. Zamiast tego zwraca `exists: false` w danych wyjściowych. |

>[!TIP]
>Aby sprawdzić, czy plik/folder/tabela istnieje lub nie, określ `exists` na liście pól działanie GetMetadata, można `exists: true/false` sprawdzić wynik z danych wyjściowych działania. Jeśli `exists` nie skonfigurowano na liście pól, działanie GetMetadata zakończy się niepowodzeniem, gdy obiekt nie zostanie znaleziony.

>[!NOTE]
>Podczas pobierania metadanych z magazynów plików i konfigurowania `modifiedDatetimeStart` i/lub `modifiedDatetimeEnd`, `childItems` w danych wyjściowych zwracane są tylko pliki pod daną ścieżką, a czas ostatniej modyfikacji między zakresem, ale bez podfolderów.

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

**Zestawu**

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

Obecnie działanie GetMetadata może pobrać następujące typy informacji o metadanych.

Właściwość | Opis | Wymagane
-------- | ----------- | --------
fieldList | Wyświetla listę typów wymaganych informacji metadanych. Zobacz szczegóły w sekcji [Opcje metadanych](#metadata-options) w temacie obsługiwane metadane. | Yes 
zestawu | Zestaw danych referencyjnych, którego aktywność metadanych ma zostać pobrana przez działanie GetMetadata. Zobacz sekcję [obsługiwane możliwości](#supported-capabilities) na obsługiwanych łącznikach i zapoznaj się z tematem łącznika w temacie Szczegóły składni zestawu danych. | Tak
formatSettings | Zastosuj przy użyciu zestawu danych typu format (Parquet, DelimitedText). | Nie
storeSettings | Zastosuj przy użyciu zestawu danych typu format (Parquet, DelimitedText). | Nie

## <a name="sample-output"></a>Przykładowe dane wyjściowe

Wynik GetMetadata jest pokazywany w danych wyjściowych działania. Poniżej znajdują się dwa przykłady z pełnymi opcjami metadanych wybranych na liście pól jako odwołanie. Aby użyć wyniku w kolejnych działaniach, użyj wzorca `@{activity('MyGetMetadataActivity').output.itemName}`.

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

## <a name="next-steps"></a>Następne kroki
Zobacz inne działania przepływu sterowania obsługiwane przez Data Factory: 

- [Działanie Execute Pipeline](control-flow-execute-pipeline-activity.md)
- [Dla każdego działania](control-flow-for-each-activity.md)
- [Działanie Lookup](control-flow-lookup-activity.md)
- [Aktywność sieci Web](control-flow-web-activity.md)
