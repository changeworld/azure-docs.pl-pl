---
title: Przenieść dane z usługi Amazon Redshift przy użyciu fabryki danych Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przenieść dane z usługi Amazon Redshift za pomocą działania kopiowania fabryki danych Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 01d15078-58dc-455c-9d9d-98fbdf4ea51e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 7ece34809734478ddb52c12d5dbd92291231f439
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37045691"
---
# <a name="move-data-from-amazon-redshift-using-azure-data-factory"></a>Przenoszenie danych z Redshift Amazon przy użyciu fabryki danych Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [W wersji 1](data-factory-amazon-redshift-connector.md)
> * [W wersji 2 (bieżąca wersja)](../connector-amazon-redshift.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 fabryki danych. Jeśli używasz bieżącą wersję usługi fabryka danych, zobacz [Amazon Redshift łącznika w wersji 2](../connector-amazon-redshift.md).

W tym artykule opisano sposób używania działania kopiowania w fabryce danych Azure do przenoszenia danych z Amazon Redshift. Artykuł opiera się na [działań przepływu danych](data-factory-data-movement-activities.md) artykułu, który przedstawia ogólny przegląd przenoszenia danych z działania kopiowania. 

Fabryka danych aktualnie obsługuje tylko przenoszenia danych z usługi Amazon Redshift do [obsługiwanych ujścia magazynu danych](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Przenoszenie danych z innych magazynów danych do firmy Amazon Redshift nie jest obsługiwane.

> [!TIP]
> Aby uzyskać najlepszą wydajność podczas kopiowania dużych ilości danych z Amazon Redshift, należy rozważyć użycie wbudowanego Redshift **zwolnienie** polecenia za pomocą usługi Amazon proste magazynu usługi (Amazon S3). Aby uzyskać więcej informacji, zobacz [zwolnienie Użyj można skopiować danych z usługi Amazon Redshift](#use-unload-to-copy-data-from-amazon-redshift).

## <a name="prerequisites"></a>Wymagania wstępne
* Jeśli przenosisz dane z lokalnym magazynem danych, zainstaluj [brama zarządzania danymi](data-factory-data-management-gateway.md) na maszynie lokalnej. Udziel dostępu dla bramy do klastra usługi Amazon Redshift przy użyciu adresu IP komputera lokalnego. Aby uzyskać instrukcje, zobacz [autoryzowania dostępu do klastra](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html).
* Aby przenieść dane do magazynu danych Azure, zobacz [adres IP obliczeniowe i zakresy SQL, które są używane w centrach danych Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).

## <a name="getting-started"></a>Wprowadzenie
Można utworzyć potok z działaniem kopiowania do przenoszenia danych z źródła Amazon Redshift przy użyciu różnych narzędzi i interfejsów API.

Najprostszym sposobem, aby utworzyć potok jest użycie Kreatora kopiowania fabryki danych Azure. Szybkie przewodnik dotyczący tworzenia potoku za pomocą Kreatora kopiowania, zobacz [samouczek: tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md).

Można również utworzyć potok za pomocą portalu Azure, programu Visual Studio, programu Azure PowerShell lub innych narzędzi. Szablony usługi Azure Resource Manager, interfejsu API programu .NET lub interfejsu API REST może również służyć do tworzenia potoku. Aby uzyskać instrukcje utworzyć potok z działania kopiowania, zobacz [samouczek działanie kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). 

Czy można użyć narzędzia i interfejsy API, należy wykonać następujące kroki, aby utworzyć potok, który przenosi dane z magazynu danych źródła do ujścia magazynu danych: 

1. Utwórz połączone usługi, aby połączyć dane wejściowe i wyjściowe magazyny danych z fabryką danych.
2. Tworzenie zestawów danych do reprezentowania danych wejściowych i wyjściowych operacji kopiowania. 
3. Utworzyć potok aktywnością kopiowania zestawu danych jako dane wejściowe i zestawu danych jako dane wyjściowe. 

Korzystając z Kreatora kopiowania, definicje JSON do tych jednostek fabryki danych są tworzone automatycznie. Korzystając z narzędzia lub interfejsów API (z wyjątkiem interfejsu API programu .NET), należy zdefiniować jednostek fabryki danych w formacie JSON. [Przykład JSON: kopiowanie danych z Amazon Redshift do magazynu obiektów Blob Azure](#json-example-copy-data-from-amazon-redshift-to-azure-blob) zawiera definicje JSON dla jednostek fabryki danych, które są używane do kopiowania danych z magazynu danych Amazon Redshift.

W poniższych sekcjach opisano właściwości JSON, które są używane do definiowania jednostek fabryki danych Amazon Redshift.

## <a name="linked-service-properties"></a>Połączona usługa właściwości

Poniższa tabela zawiera opisy elementów JSON, które są specyficzne dla usługi Amazon Redshift połączone.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| **type** |Ta właściwość musi mieć ustawioną **AmazonRedshift**. |Yes |
| **server** |IP adres lub nazwę hosta serwera Amazon Redshift. |Yes |
| **Port** |Numer portu TCP używany przez serwer Amazon Redshift do nasłuchiwania dla połączeń klienta. |Nie (wartość domyślna to 5439) |
| **Bazy danych** |Nazwa bazy danych Amazon Redshift. |Yes |
| **Nazwa użytkownika** |Nazwa użytkownika, który ma dostęp do bazy danych. |Yes |
| **hasło** |Hasło dla konta użytkownika. |Yes |

## <a name="dataset-properties"></a>Właściwości zestawu danych

Listę sekcje i właściwości, które są dostępne do definiowania zestawów danych, zobacz [Tworzenie zbiorów danych](data-factory-create-datasets.md) artykułu. **Struktury**, **dostępności**, i **zasad** sekcje są podobne dla wszystkich typów w zestawie danych. Przykłady typów zestawu danych Azure SQL, magazyn obiektów Blob platformy Azure i magazynem tabel Azure.

**TypeProperties** sekcja jest różne dla każdego typu zestawu danych i udostępnia informacje o lokalizacji w magazynie danych. **TypeProperties** sekcja dla zestawu danych typu **RelationalTable**, w tym dataset Amazon Redshift ma następujące właściwości:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| **tableName** |Nazwa tabeli w bazie danych Amazon Redshift, odnoszący się do połączonej usługi. |Nie (Jeśli **zapytania** właściwości działania kopiowania typu **RelationalSource** jest określona) |

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać listę sekcji i właściwości, które są dostępne do definiowania działań, zobacz [tworzenie potoków](data-factory-create-pipelines.md) artykułu. **Nazwa**, **opis**, **dane wejściowe** tabeli, **generuje** tabeli, i **zasad** właściwości dostępne dla wszystkich typów działań. Właściwości, które są dostępne w **typeProperties** sekcji różnią się dla każdego typu działania. Dla działania kopiowania właściwości różnią się w zależności od typów źródeł danych i sink.

Dla działania kopiowania, gdy źródłem jest typu **AmazonRedshiftSource**, są dostępne w następujących właściwości **typeProperties** sekcji:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| **zapytania** | Użyj niestandardowych zapytania, aby odczytać danych. |Nie (Jeśli **tableName** określono właściwości zestawu danych) |
| **redshiftUnloadSettings** | Zawiera grupę właściwości, używając Redshift **zwolnienie** polecenia. | Nie |
| **s3LinkedServiceName** | Amazon S3, który ma być używana jako magazyn tymczasowy. Połączona usługa jest określana za pomocą nazwy typu fabryki danych Azure **AwsAccessKey**. | Wymagane, gdy usługa **redshiftUnloadSettings** właściwości |
| **bucketName** | Wskazuje zasobnika Amazon S3 służące do przechowywania danych tymczasowych. Jeśli ta właściwość nie zostanie podany, działanie kopiowania auto generuje zasobnika. | Wymagane, gdy usługa **redshiftUnloadSettings** właściwości |

Alternatywnie można użyć **RelationalSource** typu, który obejmuje Amazon Redshift z następującą właściwość w **typeProperties** sekcji. Ten typ źródła nie obsługuje Redshift Uwaga **zwolnienie** polecenia.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| **zapytania** |Użyj niestandardowych zapytania, aby odczytać danych. | Nie (Jeśli **tableName** określono właściwości zestawu danych) |

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>Umożliwia kopiowanie danych z usługi Amazon Redshift zwolnienia

Amazon Redshift [ **zwolnienie** ](http://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) polecenie zwalnia wyników zapytania do jednego lub więcej plików na Amazon S3. To polecenie jest zalecane przez firmy Amazon do kopiowania dużych zestawów danych z Redshift.

**Przykład: Kopiowanie danych z Amazon Redshift do magazynu danych SQL Azure**

W tym przykładzie kopiuje dane z usługi Amazon Redshift magazyn danych SQL Azure. W przykładzie użyto Redshift **zwolnienie** polecenia, przygotowanego skopiować dane i PolyBase firmy Microsoft.

Dla tego przypadku użycia przykładowe działanie kopiowania najpierw zwalnia danych z Amazon Redshift do Amazon S3 zgodnie z konfiguracją **redshiftUnloadSettings** opcji. Następnie dane są kopiowane z usługi Amazon S3 do magazynu obiektów Blob platformy Azure, jak określono w **stagingSettings** opcji. Na koniec PolyBase ładuje dane do usługi SQL Data Warehouse. Wszystkie pośrednie formaty są obsługiwane przez działanie kopiowania.

![Skopiuj przepływ pracy z Amazon Redshift do magazynu danych SQL](media\data-factory-amazon-redshift-connector\redshift-to-sql-dw-copy-workflow.png)

```json
{
    "name": "CopyFromRedshiftToSQLDW",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "AmazonRedshiftSource",
            "query": "select * from MyTable",
            "redshiftUnloadSettings": {
                "s3LinkedServiceName":"MyAmazonS3StorageLinkedService",
                "bucketName": "bucketForUnload"
            }
        },
        "sink": {
            "type": "SqlDWSink",
            "allowPolyBase": true
        },
        "enableStaging": true,
        "stagingSettings": {
            "linkedServiceName": "MyAzureStorageLinkedService",
            "path": "adfstagingcopydata"
        },
        "cloudDataMovementUnits": 32
        .....
    }
}
```

## <a name="json-example-copy-data-from-amazon-redshift-to-azure-blob-storage"></a>Przykład JSON: kopiowanie danych z Amazon Redshift do magazynu obiektów Blob platformy Azure
W tym przykładzie pokazano, jak skopiować dane z bazy danych Amazon Redshift do magazynu obiektów Blob Azure. Dane mogą być kopiowane bezpośrednio do dowolnego [obsługiwane zbiornika](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania.  

Przykład zawiera następujące obiekty fabryki danych:

* Połączonej usługi typu [AmazonRedshift](#linked-service-properties)
* Połączonej usługi typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Dane wejściowe [dataset](data-factory-create-datasets.md) typu [RelationalTable](#dataset-properties)
* Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* A [potoku](data-factory-create-pipelines.md) z działania kopiowania, która używa [RelationalSource](#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md##copy-activity-properties) właściwości

Przykład kopiuje dane z wyniku kwerendy w Amazon Redshift do obiektów blob platformy Azure co godzinę. Właściwości JSON, które są używane w przykładowym są opisane w następnych sekcjach definicje jednostki.

**Usługi Amazon Redshift połączone**

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties":
    {
        "type": "AmazonRedshift",
        "typeProperties":
        {
            "server": "< The IP address or host name of the Amazon Redshift server >",
            "port": <The number of the TCP port that the Amazon Redshift server uses to listen for client connections.>,
            "database": "<The database name of the Amazon Redshift database>",
            "username": "<username>",
            "password": "<password>"
        }
    }
}
```

**Azure Blob połączoną usługą magazynu**

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Zestaw danych wejściowych Amazon Redshift**

**Zewnętrznych** właściwość jest ustawiona na "true", aby poinformować usługi fabryka danych z zestawu danych może być zewnętrzne fabryki danych. To ustawienie właściwości wskazuje, że zestaw danych nie jest generowany przez działanie w fabryce danych. Ustaw właściwość na wartość true na wejściowy zestaw danych, który nie jest generowany przez działania w potoku.

```json
{
    "name": "AmazonRedshiftInputDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "AmazonRedshiftLinkedService",
        "typeProperties": {
            "tableName": "<Table name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

**Wyjściowy zestaw danych obiektów blob platformy Azure**

Dane są zapisywane do nowego obiektu blob co godzinę, ustawiając **częstotliwość** dla właściwości "Godzina" i **interwał** właściwości na wartość 1. **FolderPath** dynamicznie oceny właściwości dla obiektu blob. Wartość właściwości jest oparta na czas rozpoczęcia wycinek, który jest przetwarzana. Ścieżka folderu używa rok, miesiąc, dzień i godziny części czas rozpoczęcia.

```json
{
    "name": "AzureBlobOutputDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/fromamazonredshift/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Aktywność kopiowania w potoku ze źródłem Azure Redshift (typu RelationalSource) i ujście obiektów Blob platformy Azure**

Potok zawiera działanie kopiowania, który jest skonfigurowany do używania zestawów danych wejściowych i wyjściowych. Potok jest zaplanowane co godzinę. W definicji JSON dla potoku **źródła** ustawiono typ **RelationalSource** i **zbiornika** ustawiono typ **BlobSink**. Określony dla zapytania SQL **zapytania** właściwości wybiera dane do skopiowania z ostatniej godziny.

```json
{
    "name": "CopyAmazonRedshiftToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "AmazonRedshiftSource",
                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)",
                        "redshiftUnloadSettings": {
                            "s3LinkedServiceName":"myS3Storage",
                            "bucketName": "bucketForUnload"
                        }
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    },
                    "cloudDataMovementUnits": 32
                },
                "inputs": [
                    {
                        "name": "AmazonRedshiftInputDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutputDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "AmazonRedshiftToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```
### <a name="type-mapping-for-amazon-redshift"></a>Mapowanie typu dla Amazon Redshift
Jak wspomniano w [działań przepływu danych](data-factory-data-movement-activities.md) artykułu, działanie kopiowania przeprowadza automatyczne konwersje typu źródłowego na typ sink. Typy są konwertowane przy użyciu rozwinięcie:

1. Konwertować z typu natywnego źródła do typu .NET
2. Konwertować z typu .NET do typu sink natywnego

Następujące mapowania są używane, gdy działanie kopiowania konwertuje dane z typu Amazon Redshift typ .NET:

| Typ Amazon Redshift | Typ architektury .NET |
| --- | --- |
| SMALLINT |Int16 |
| LICZBA CAŁKOWITA |Int32 |
| BIGINT |Int64 |
| DECIMAL |Decimal |
| RZECZYWISTE |Pojedyncze |
| PODWÓJNEJ PRECYZJI |podwójne |
| WARTOŚĆ LOGICZNA |Ciąg |
| CHAR |Ciąg |
| VARCHAR |Ciąg |
| DATE |DateTime |
| ZNACZNIK CZASU |DateTime |
| TEKST |Ciąg |

## <a name="map-source-to-sink-columns"></a>Obiekt sink kolumn mapy źródła
Informacje na temat mapowanie kolumn w zestawie źródła danych do kolumn w zestawie danych zbiornika, zobacz [mapowania kolumnach dataset w fabryce danych Azure](data-factory-map-columns.md).

## <a name="repeatable-reads-from-relational-sources"></a>Odczyty powtarzalne źródłami relacyjnymi
Po skopiowaniu danych z magazynu danych relacyjnych należy pamiętać, aby uniknąć niezamierzone wyniki powtarzalności. Fabryka danych Azure możesz ponownie ręcznie wycinek. Można również skonfigurować retry **zasad** dla elementu dataset ponownie uruchomić wycinek, gdy wystąpi błąd. Upewnij się, że te same dane jest do odczytu, niezależnie od tego, ile razy wycinek zostanie uruchomiona ponownie. Upewnij się, że te same dane jest do odczytu niezależnie od tego, jak ponownie uruchomić wycinka również. Aby uzyskać więcej informacji, zobacz [Repeatable odczytuje z źródłami relacyjnymi](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Wydajności i dostosowywanie
Więcej informacji na temat kluczowych czynników wpływających na wydajność działania kopiowania i sposobów w celu optymalizacji wydajności w [wydajności działania kopiowania i dostrajania przewodnik](data-factory-copy-activity-performance.md). 

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać instrukcje tworzenia potoku z działania kopiowania, zobacz [samouczek działanie kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
