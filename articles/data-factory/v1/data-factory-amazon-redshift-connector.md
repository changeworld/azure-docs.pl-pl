---
title: Przenoszenie danych z usługi Amazon Redshift, za pomocą usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przenieść dane z usługi Amazon Redshift, za pomocą działania kopiowania w fabryce danych Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 01d15078-58dc-455c-9d9d-98fbdf4ea51e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 3a1497211cc42c702537cbbdfea32ff71a400c7c
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836687"
---
# <a name="move-data-from-amazon-redshift-using-azure-data-factory"></a>Przenoszenie danych z usługi Amazon Redshift przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję usługi Data Factory, którego używasz:"]
> * [Wersja 1](data-factory-amazon-redshift-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-amazon-redshift.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącą wersję usługi Data Factory, zobacz [łącznik Amazon Redshift w wersji 2](../connector-amazon-redshift.md).

W tym artykule wyjaśniono, jak użyć działania kopiowania w usłudze Azure Data Factory do przenoszenia danych z usługi Amazon Redshift. Artykuł opiera się na [działania przenoszenia danych](data-factory-data-movement-activities.md) artykułu, który przedstawia ogólne omówienie przenoszenie danych za pomocą działania kopiowania.

Usługa Data Factory obsługuje obecnie tylko przenosi dane z usługi Amazon Redshift do [obsługiwanego magazynu danych ujścia](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Przenoszenie danych z innych magazynów danych do usługi Amazon Redshift nie jest obsługiwane.

> [!TIP]
> Aby uzyskać najlepszą wydajność podczas kopiowania dużych ilości danych z usługi Amazon Redshift, należy wziąć pod uwagę przy użyciu wbudowanych Redshift **zwolnienie** polecenia za pomocą Amazon Simple Storage Service (Amazon S3). Aby uzyskać więcej informacji, zobacz [zwolnienie Użyj, aby skopiować dane z usługi Amazon Redshift](#use-unload-to-copy-data-from-amazon-redshift).

## <a name="prerequisites"></a>Wymagania wstępne
* Jeśli dane są przenoszone do magazynu danych w środowisku lokalnym, należy zainstalować [bramy zarządzania danymi](data-factory-data-management-gateway.md) na maszynie lokalnej. Przy użyciu adresu IP komputera lokalnego, należy udzielić dostępu dla bramy do klastra usługi Amazon Redshift. Aby uzyskać instrukcje, zobacz [Autoryzowanie dostępu do klastra](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html).
* Aby przenieść dane do magazynu danych na platformie Azure, zobacz [adres IP zasobów obliczeniowych i zakresy SQL, które są używane w centrach danych platformy Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).

## <a name="getting-started"></a>Wprowadzenie
Utworzysz potok z działaniem kopiowania, aby przenieść dane ze źródła usługi Amazon Redshift przy użyciu różnych narzędzi i interfejsów API.

Najprostszym sposobem utworzenia potoku jest używanie kreatora kopiowania usługi Azure Data Factory. Szybki przewodnik dotyczący tworzenia potoku za pomocą Kreatora kopiowania, zobacz [samouczka: Tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md).

Można również utworzyć potok za pomocą programu Visual Studio, programu Azure PowerShell lub innych narzędzi. Szablony usługi Azure Resource Manager, interfejsu API platformy .NET lub interfejsu API REST można również utworzyć potok. Aby uzyskać instrukcje krok po kroku utworzysz potok z działaniem kopiowania, zobacz [samouczka działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Czy używasz narzędzi lub interfejsów API, należy wykonać poniższe kroki, aby utworzyć potok, który przenosi dane z magazynu danych źródłowych do magazynu danych ujścia:

1. Utwórz połączone usługi, aby połączyć dane wejściowe i wyjściowe magazynów danych z fabryką danych.
2. Utwórz zestawy danych reprezentujące dane wejściowe i wyjściowe operacji kopiowania.
3. Tworzenie potoku za pomocą działania kopiowania, która przyjmuje jako dane wejściowe zestawu danych i zestaw danych jako dane wyjściowe.

Korzystając z Kreatora kopiowania, definicje JSON dotyczące jednostek usługi Data Factory, te są tworzone automatycznie. Korzystając z narzędzi lub interfejsów API (z wyjątkiem interfejsu API platformy .NET), zdefiniujesz jednostki usługi Data Factory przy użyciu formatu JSON. Przykład kodu JSON: Kopiuj dane z usługi Amazon Redshift do usługi Azure Blob storage zawiera definicje JSON dotyczące jednostek usługi Data Factory, które są używane do kopiowania danych z magazynu danych usługi Amazon Redshift.

W poniższych sekcjach opisano właściwości kodu JSON, które są używane do definiowania jednostek usługi Data Factory dla usługi Amazon Redshift.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Poniższa tabela zawiera opisy elementów JSON, które są specyficzne dla usługi Amazon Redshift połączone.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| **type** |Ta właściwość musi być równa **AmazonRedshift**. |Tak |
| **server** |Adres IP lub hosta nazwę serwera usługi Amazon Redshift. |Tak |
| **port** |Numer portu TCP używanego przez serwer usługi Amazon Redshift do nasłuchiwania połączeń klientów. |Nie (wartość domyślna to 5439) |
| **database** |Nazwa bazy danych usługi Amazon Redshift. |Tak |
| **Nazwa użytkownika** |Nazwa użytkownika, który ma dostęp do bazy danych. |Tak |
| **Hasło** |Hasło dla konta użytkownika. |Yes |

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać listę sekcje i właściwości, które są dostępne do definiowania zestawów danych, zobacz [tworzenie zestawów danych](data-factory-create-datasets.md) artykułu. **Struktury**, **dostępności**, i **zasad** sekcje są podobne dla wszystkich typów w zestawie danych. Przykładami typy zestawów danych Azure SQL, Azure Blob storage i Azure Table storage.

**TypeProperties** sekcji różni się dla każdego typu zestawu danych i udostępnia informacje o lokalizacji danych w magazynie. **TypeProperties** sekcji dla zestawu danych typu **RelationalTable**, w tym zestawie danych usługi Amazon Redshift, ma następujące właściwości:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| **tableName** |Nazwa tabeli w bazie danych usługi Amazon Redshift, których połączona usługa przywołuje. |Nie (Jeśli **zapytania** właściwości działania kopiowania typu **RelationalSource** zostanie określona) |

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać listę sekcji i właściwości, które są dostępne do definiowania działań, zobacz [tworzenie potoków](data-factory-create-pipelines.md) artykułu. **Nazwa**, **opis**, **dane wejściowe** tabeli **generuje** tabeli i **zasad** właściwości są dostępne dla wszystkich typów działań. Właściwości, które są dostępne w **typeProperties** różnią się w sekcji dla każdego typu działania. Właściwości różnią się w zależności od typów danych źródła i ujścia dla działania kopiowania.

Działanie kopiowania, gdy źródłem jest typu **obiektu AmazonRedshiftSource**, następujące właściwości są dostępne w **typeProperties** sekcji:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| **Zapytanie** | Użyj niestandardowego zapytania, aby odczytywać dane. |Nie (Jeśli **tableName** określono właściwości zestawu danych) |
| **redshiftUnloadSettings** | Zawiera grupę właściwości, korzystając z usługi Redshift **zwolnienie** polecenia. | Nie |
| **s3LinkedServiceName** | Amazon S3 do użycia jako tymczasowego magazynu. Połączona usługa jest określony za pomocą usługi Azure Data Factory nazwę typu **AwsAccessKey**. | Wymagany w przypadku korzystania **elemencie redshiftUnloadSettings** właściwości |
| **bucketName** | Wskazuje zasobnika Amazon S3 można używać do przechowywania danych tymczasowych. Jeśli ta właściwość nie zostanie podany, działanie kopiowania automatycznie generuje koszyka. | Wymagany w przypadku korzystania **elemencie redshiftUnloadSettings** właściwości |

Alternatywnie, można użyć **RelationalSource** typu, który obejmuje Amazon Redshift, z następującymi właściwościami w **typeProperties** sekcji. Uwaga tego typu źródła nie obsługuje usługi Redshift **zwolnienie** polecenia.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| **Zapytanie** |Użyj niestandardowego zapytania, aby odczytywać dane. | Nie (Jeśli **tableName** określono właściwości zestawu danych) |

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>Umożliwia kopiowanie danych z usługi Amazon Redshift przez zwolnienie

Usługi Amazon Redshift [ **zwolnienie** ](https://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) polecenia zwalnia wyników zapytania do jednej lub więcej plików na Amazon S3. To polecenie jest zalecane, Amazon do kopiowania dużych zestawów danych z usługi Redshift.

**Przykład: Kopiowanie danych z usługi Amazon Redshift do usługi Azure SQL Data Warehouse**

W tym przykładzie kopiuje dane z usługi Amazon Redshift do usługi Azure SQL Data Warehouse. W przykładzie użyto usługi Redshift **zwolnienie** polecenia, danych kopiowania przejściowego i PolyBase firmy Microsoft.

Dla tego przypadku użycia przykładowe działanie kopiowania najpierw zwalnia dane z usługi Amazon Redshift do Amazon S3 zgodnie z konfiguracją **elemencie redshiftUnloadSettings** opcji. Następnie dane są kopiowane z usługi Amazon S3 w usłudze Azure Blob storage, jak to określono w **stagingSettings** opcji. Na koniec PolyBase powoduje załadowanie danych do usługi SQL Data Warehouse. Wszystkie formaty tymczasowe są obsługiwane przez działanie kopiowania.

![Skopiuj przepływ pracy z usługi Amazon Redshift do usługi SQL Data Warehouse](media/data-factory-amazon-redshift-connector/redshift-to-sql-dw-copy-workflow.png)

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

## <a name="json-example-copy-data-from-amazon-redshift-to-azure-blob-storage"></a>Przykład kodu JSON: Kopiowanie danych z usługi Amazon Redshift do usługi Azure Blob storage
Ten przykład pokazuje, jak skopiować dane z bazą danych Amazon Redshift do usługi Azure Blob Storage. Możesz skopiować dane bezpośrednio do dowolnej [obsługiwane ujścia](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania.

Przykład obejmuje następujących jednostek fabryki danych:

* Połączonej usługi typu [AmazonRedshift](#linked-service-properties)
* Połączonej usługi typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Dane wejściowe [dataset](data-factory-create-datasets.md) typu [RelationalTable](#dataset-properties)
* Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [obiektu blob platformy Azure](data-factory-azure-blob-connector.md#dataset-properties)
* A [potoku](data-factory-create-pipelines.md) za pomocą działania kopiowania, która używa [RelationalSource](#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md##copy-activity-properties) właściwości

Przykład kopiuje dane z wyniku kwerendy w Amazon Redshift do obiektu blob platformy Azure co godzinę. Właściwości kodu JSON, które są używane w przykładzie są opisane w sekcjach definicji jednostki.

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
            "port": "<The number of the TCP port that the Amazon Redshift server uses to listen for client connections.>",
            "database": "<The database name of the Amazon Redshift database>",
            "username": "<username>",
            "password": "<password>"
        }
    }
}
```

**Obiekt Blob połączona usługa Azure storage**

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
**Wejściowy zestaw danych usługi Amazon Redshift**

**Zewnętrznych** właściwość jest ustawiona na "true" w celu poinformowania usługi Data Factory, że zestaw danych jest zewnętrzne z fabryką danych. Ustawienie tej właściwości oznacza, że zestaw danych nie jest generowany przez działanie w usłudze data factory. Ustaw właściwość na wartość true dla wejściowego zestawu danych, które nie są generowane przez działanie w potoku.

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

Dane są zapisywane do nowego obiektu blob co godzinę, ustawiając **częstotliwość** właściwość "Hour" i **interwał** właściwości na wartość 1. **FolderPath** właściwości dla obiektu blob jest dynamicznie obliczana. Wartość właściwości jest oparta na czas rozpoczęcia wycinek, który jest przetwarzany. Ścieżka folderu używa rok, miesiąc, dzień i części godzin od zaplanowanej godziny rozpoczęcia.

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

**Działanie kopiowania w potoku za pomocą źródła Redshift platformy Azure (typu RelationalSource) i ujścia obiektu Blob platformy Azure**

Potoku zawierającego działanie kopiowania, który jest skonfigurowany do korzystania z danych wejściowych i wyjściowych zestawów danych. Potok jest zaplanowane do uruchomienia na godzinę. W definicji JSON potok **źródła** ustawiono typ **RelationalSource** i **ujścia** ustawiono typ **BlobSink**. Zapytanie SQL, określony dla **zapytania** właściwość wybiera dane do skopiowania z ostatniej godziny.

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
### <a name="type-mapping-for-amazon-redshift"></a>Mapowanie typu dla usługi Amazon Redshift
Jak wspomniano w [działania przenoszenia danych](data-factory-data-movement-activities.md) artykułu, działanie kopiowania wykonuje automatyczne konwersje z typu źródłowego na typ ujścia. Typy są konwertowane przy użyciu podejście dwuetapowe:

1. Konwersji z typu natywnego źródłowego na typ architektury .NET
2. Przekonwertowanie z .NET do typu natywnego obiektu sink

Następujące mapowania są używane podczas działania kopiowania konwertuje dane z usługi Amazon Redshift typ architektury .NET:

| Typ usługi Amazon Redshift | Typ architektury .NET |
| --- | --- |
| SMALLINT |Int16 |
| INTEGER |Int32 |
| BIGINT |Int64 |
| DECIMAL |Decimal |
| REAL |Single |
| DOUBLE PRECISION |Double |
| BOOLEAN |String |
| CHAR |String |
| VARCHAR |String |
| DATE |Datetime |
| TIMESTAMP |Datetime |
| TEXT |Ciąg |

## <a name="map-source-to-sink-columns"></a>Mapy źródła do ujścia kolumn
Aby dowiedzieć się, jak mapowania kolumn w zestawie danych źródłowych do kolumn w zestawie danych ujścia, zobacz [mapowanie kolumny zestawu danych w usłudze Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-reads-from-relational-sources"></a>Powtarzalnego odczytu z źródeł relacyjnych
Podczas kopiowania danych z relacyjnego magazynu danych, Zachowaj powtarzalności należy pamiętać, aby uniknąć niezamierzonego wyników. W usłudze Azure Data Factory możesz ponownie uruchomić wycinek ręcznie. Można również skonfigurować ponownych prób **zasad** dla zestawu danych ponownie uruchomić wycinek, gdy wystąpi awaria. Upewnij się, że te same dane jest do odczytu, niezależnie od tego, ile razy ponownego uruchomienia wycinka. Również upewnić się, że niezależnie od tego, jak ponownie uruchomić wycinek jest do odczytu tych samych danych. Aby uzyskać więcej informacji, zobacz [Repeatable odczytuje dane ze źródeł relacyjnych](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Wydajności i dostosowywanie
Więcej informacji na temat kluczowych czynników wpływających na wydajność działania kopiowania i sposobów na optymalizację wydajności [wydajności i działania kopiowania przewodnika dostrajania](data-factory-copy-activity-performance.md).

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać szczegółowe instrukcje tworzenia potoku za pomocą działania kopiowania, zobacz [samouczka działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
