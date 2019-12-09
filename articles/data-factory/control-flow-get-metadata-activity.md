---
title: Działanie pobierania metadanych w Azure Data Factory
description: Dowiedz się, jak używać działania Get Metadata w potoku Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: ''
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: jingwang
ms.openlocfilehash: 9985997ff4bef727676232705297379ccfc179c5
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928562"
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>Działanie pobierania metadanych w Azure Data Factory

Możesz użyć działania Pobierz metadane, aby pobrać metadane dowolnych danych w Azure Data Factory. Tego działania można użyć w następujących scenariuszach:

- Sprawdź poprawność metadanych dowolnych danych.
- Wyzwalanie potoku, gdy dane są gotowe/dostępne.

W przepływie sterowania jest dostępna następująca funkcja:

- Możesz użyć danych wyjściowych z działania Pobierz metadane w wyrażeniach warunkowych, aby przeprowadzić walidację.
- Możesz wyzwolić potok, gdy warunek jest spełniony przez pętlę do momentu, aż do pętli.

## <a name="capabilities"></a>Możliwości

Działanie Get Metadata Pobiera zestaw danych jako dane wejściowe i zwraca informacje o metadanych jako dane wyjściowe. Obecnie obsługiwane są następujące łączniki i odpowiednie metadane do pobierania. Maksymalny rozmiar zwracanych metadanych wynosi 1 MB.

>[!NOTE]
>W przypadku uruchomienia działania Get Metadata w środowisku środowiska Integration Runtime (samodzielne) najnowsze funkcje są obsługiwane w wersji 3,6 lub nowszej.

### <a name="supported-connectors"></a>Obsługiwane łączniki

**File Storage**

| Łącznik/metadane | itemName<br>(plik/folder) | {1&gt;itemType&lt;1}<br>(plik/folder) | size<br>rozszerzeniem | utworzony<br>(plik/folder) | lastModified<br>(plik/folder) |childItems<br>system32\drivers\etc |contentMD5<br>rozszerzeniem | structure<br/>rozszerzeniem | Kolumn<br>rozszerzeniem | istniejący<br>(plik/folder) |
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| [Amazon S3](connector-amazon-simple-storage-service.md) | √/√ | √/√ | √ | x/x | √/√ * | √ | x | √ | √ | √/√ * |
| [Google Cloud Storage](connector-google-cloud-storage.md) | √/√ | √/√ | √ | x/x | √/√ * | √ | x | √ | √ | √/√ * |
| [Azure Blob Storage](connector-azure-blob-storage.md) | √/√ | √/√ | √ | x/x | √/√ * | √ | √ | √ | √ | √/√ |
| [Usługa Azure Data Lake Storage 1. generacji](connector-azure-data-lake-store.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Azure Files](connector-azure-file-storage.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [System plików](connector-file-system.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [SFTP](connector-sftp.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [FTP](connector-ftp.md) | √/√ | √/√ | √ | x/x | x/x | √ | x | √ | √ | √/√ |

- W przypadku usługi Amazon S3 i Google Cloud Storage `lastModified` ma zastosowanie do zasobnika i klucza, ale nie do folderu wirtualnego, a `exists` ma zastosowanie do zasobnika i klucza, ale nie do folderu wirtualnego.
- W przypadku usługi Azure Blob Storage `lastModified` ma zastosowanie do kontenera i obiektu BLOB, ale nie do folderu wirtualnego.
- Filtr `lastModified` jest obecnie stosowany do filtrowania elementów podrzędnych, ale nie do samego folderu lub samego pliku.
- Filtr symboli wieloznacznych dla folderów/plików nie jest obsługiwany w przypadku działania pobierania metadanych.

**Relacyjna baza danych**

| Łącznik/metadane | structure | Kolumn | istniejący |
|:--- |:--- |:--- |:--- |
| [Azure SQL Database](connector-azure-sql-database.md) | √ | √ | √ |
| [Wystąpienie zarządzane usługi Azure SQL Database](connector-azure-sql-database-managed-instance.md) | √ | √ | √ |
| [Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md) | √ | √ | √ |
| [SQL Server](connector-sql-server.md) | √ | √ | √ |

### <a name="metadata-options"></a>Opcje metadanych

Aby pobrać odpowiednie informacje, możesz określić następujące typy metadanych na liście pól Pobieranie metadanych:

| Typ metadanych | Opis |
|:--- |:--- |
| itemName | Nazwa pliku lub folderu. |
| {1&gt;itemType&lt;1} | Typ pliku lub folderu. Zwrócona wartość to `File` lub `Folder`. |
| size | Rozmiar pliku w bajtach. Dotyczy tylko plików. |
| utworzony | Utworzono datę i godzinę dla pliku lub folderu. |
| lastModified | Data i godzina ostatniej modyfikacji pliku lub folderu. |
| childItems | Lista podfolderów i plików w danym folderze. Dotyczy tylko folderów. Zwracana wartość to lista nazw i typów każdego elementu podrzędnego. |
| contentMD5 | MD5 pliku. Dotyczy tylko plików. |
| structure | Struktura danych w tabeli pliku lub relacyjnej bazy danych. Zwracana wartość to lista nazw kolumn i typów kolumn. |
| Kolumn | Liczba kolumn w pliku lub tabeli relacyjnej. |
| istniejący| Czy istnieje plik, folder lub tabela. Należy pamiętać, że jeśli `exists` określono na liście pól Pobierz metadane, działanie nie powiedzie się, nawet jeśli plik, folder lub tabela nie istnieją. Zamiast tego `exists: false` jest zwracany w danych wyjściowych. |

>[!TIP]
>Jeśli chcesz sprawdzić, czy istnieje plik, folder lub tabela, określ `exists` na liście pól działanie pobierania metadanych. Następnie można sprawdzić wynik `exists: true/false` w danych wyjściowych działania. Jeśli `exists` nie zostanie określony na liście pól, działanie Pobierz metadane zakończy się niepowodzeniem, jeśli nie zostanie znaleziony obiekt.

>[!NOTE]
>Podczas pobierania metadanych z magazynów plików i konfigurowania `modifiedDatetimeStart` lub `modifiedDatetimeEnd`, `childItems` w danych wyjściowych będzie zawierać tylko pliki w danej ścieżce, które mają czas ostatniej modyfikacji w określonym zakresie. W programie nie będą uwzględniane elementy w podfolderach.

## <a name="syntax"></a>Składnia

**Działanie pobierania metadanych**

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

Obecnie działanie Get Metadata może zwracać następujące typy informacji o metadanych:

Właściwość | Opis | Wymagane
-------- | ----------- | --------
fieldList | Typy wymaganych informacji metadanych. Aby uzyskać szczegółowe informacje na temat obsługiwanych metadanych, zobacz sekcję [Opcje metadanych](#metadata-options) w tym artykule. | Tak 
zestaw danych | Zestaw danych referencyjnych, którego metadane mają być pobierane przez działanie pobierania metadanych. Zapoznaj się z sekcją [możliwości](#capabilities) , aby uzyskać informacje na temat obsługiwanych łączników. Zapoznaj się z tematami dotyczącymi szczegółowych informacji o składni zestawu danych. | Tak
formatSettings | Zastosuj przy użyciu zestawu danych typu format. | Nie
storeSettings | Zastosuj przy użyciu zestawu danych typu format. | Nie

## <a name="sample-output"></a>Przykładowe dane wyjściowe

Wyniki pobierania metadanych są wyświetlane w danych wyjściowych działania. Poniżej przedstawiono dwie przykłady przedstawiające rozbudowane opcje metadanych. Aby użyć wyników w kolejnym działaniu, użyj tego wzorca: `@{activity('MyGetMetadataActivity').output.itemName}`.

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
Dowiedz się więcej na temat innych działań przepływu sterowania obsługiwanych przez Data Factory:

- [Działanie wykonywania potoku](control-flow-execute-pipeline-activity.md)
- [Działanie ForEach](control-flow-for-each-activity.md)
- [Działanie Lookup](control-flow-lookup-activity.md)
- [Działanie Web](control-flow-web-activity.md)
