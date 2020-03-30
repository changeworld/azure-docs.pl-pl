---
title: Przenoszenie danych z usługi Amazon Redshift przy użyciu usługi Azure Data Factory
description: Dowiedz się, jak przenieść dane z usługi Amazon Redshift przy użyciu aktywności kopiowania fabrycznej usługi Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 01d15078-58dc-455c-9d9d-98fbdf4ea51e
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: c2e2394bbcee5294bfb752a0af2969457ffff0ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260529"
---
# <a name="move-data-from-amazon-redshift-using-azure-data-factory"></a>Przenoszenie danych z usługi Amazon Redshift przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](data-factory-amazon-redshift-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-amazon-redshift.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [Łącznik Amazon Redshift w wersji 2](../connector-amazon-redshift.md).

W tym artykule wyjaśniono, jak używać działania kopiowania w usłudze Azure Data Factory do przenoszenia danych z usługi Amazon Redshift. Artykuł opiera się na działania [przenoszenia danych](data-factory-data-movement-activities.md) artykułu, który przedstawia ogólny przegląd przenoszenia danych z działania kopiowania.

Data Factory obsługuje obecnie tylko przenoszenie danych z Amazon Redshift do [obsługiwanego magazynu danych ujścia.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) Przenoszenie danych z innych magazynów danych do Amazon Redshift nie jest obsługiwane.

> [!TIP]
> Aby osiągnąć najlepszą wydajność podczas kopiowania dużych ilości danych z Amazon Redshift, należy rozważyć użycie wbudowanego polecenia Redshift **UNLOAD** za pośrednictwem amazon Simple Storage Service (Amazon S3). Aby uzyskać szczegółowe informacje, zobacz [Używanie funkcji ROZŁADuj do kopiowania danych z usługi Amazon Redshift](#use-unload-to-copy-data-from-amazon-redshift).

## <a name="prerequisites"></a>Wymagania wstępne
* W przypadku przenoszenia danych do lokalnego magazynu danych należy zainstalować [bramę zarządzania danymi](data-factory-data-management-gateway.md) na komputerze lokalnym. Udziel dostępu dla bramy do klastra Amazon Redshift przy użyciu lokalnego adresu IP komputera. Aby uzyskać [instrukcje, zobacz Autoryzowanie dostępu do klastra](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html).
* Aby przenieść dane do magazynu danych platformy Azure, zobacz [obliczeniowy adres IP i zakresy SQL, które są używane przez centra danych platformy Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).

## <a name="getting-started"></a>Wprowadzenie
Potoku z działaniem kopiowania można przenieść dane ze źródła Amazon Redshift przy użyciu różnych narzędzi i interfejsów API.

Najprostszym sposobem utworzenia potoku jest użycie Kreatora kopiowania fabryki danych platformy Azure. Aby uzyskać szybki przewodnik dotyczący tworzenia potoku za pomocą Kreatora kopiowania, zobacz [Samouczek: Tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md).

Można również utworzyć potok przy użyciu programu Visual Studio, programu Azure PowerShell lub innych narzędzi. Szablony usługi Azure Resource Manager, interfejs API platformy .NET lub interfejs API REST mogą być również używane do tworzenia potoku. Aby uzyskać instrukcje krok po kroku, aby utworzyć potok z działaniem kopiowania, zobacz [samouczek Kopiowania działania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Niezależnie od tego, czy są używane narzędzia, czy interfejsy API, wykonaj następujące kroki, aby utworzyć potok, który przenosi dane ze źródłowego magazynu danych do magazynu danych ujścia:

1. Tworzenie połączonych usług w celu połączenia magazynów danych wejściowych i wyjściowych z fabryką danych.
2. Tworzenie zestawów danych do reprezentowania danych wejściowych i wyjściowych dla operacji kopiowania.
3. Utwórz potok z działaniem kopiowania, które przyjmuje zestaw danych jako dane wejściowe i zestaw danych jako dane wyjściowe.

Podczas korzystania z Kreatora kopiowania definicje JSON dla tych elementów fabryki danych są tworzone automatycznie. Podczas korzystania z narzędzi lub interfejsów API (z wyjątkiem interfejsu API .NET), należy zdefiniować jednostki fabryki danych przy użyciu formatu JSON. Przykład JSON: Kopiuj dane z magazynu Amazon Redshift do usługi Azure Blob zawiera definicje JSON dla jednostek usługi Data Factory, które są używane do kopiowania danych z magazynu danych Amazon Redshift.

W poniższych sekcjach opisano właściwości JSON, które są używane do definiowania jednostek fabryki danych dla amazon redshift.

## <a name="linked-service-properties"></a>Połączone właściwości usługi

Poniższa tabela zawiera opisy elementów JSON, które są specyficzne dla usługi połączonej Amazon Redshift.

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| **Typu** |Ta właściwość musi być ustawiona na **AmazonRedshift**. |Tak |
| **Serwera** |Adres IP lub nazwa hosta serwera Amazon Redshift. |Tak |
| **Portu** |Numer portu TCP używany przez serwer Amazon Redshift do nasłuchiwać połączeń klientów. |Nie (wartość domyślna to 5439) |
| **Bazy danych** |Nazwa bazy danych Amazon Redshift. |Tak |
| **Nazwę użytkownika** |Nazwa użytkownika, który ma dostęp do bazy danych. |Tak |
| **hasło** |Hasło do konta użytkownika. |Tak |

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać listę sekcji i właściwości, które są dostępne do definiowania zestawów danych, zobacz artykuł [Tworzenie zestawów danych.](data-factory-create-datasets.md) **Struktura,** **dostępność**i sekcje **zasad** są podobne dla wszystkich typów zestawów danych. Przykłady typów zestawów danych obejmują usługi Azure SQL, usługi Azure Blob storage i magazynu tabel platformy Azure.

Sekcja **typeProperties** jest inna dla każdego typu zestawu danych i zawiera informacje o lokalizacji danych w magazynie. **Sekcja typeProperties** dla zestawu danych typu **RelationalTable**, który zawiera zestaw danych Amazon Redshift, ma następujące właściwości:

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| **tableName** |Nazwa tabeli w bazie danych Amazon Redshift, do których odwołuje się usługa połączona. |Nie (jeśli określono właściwość **kwerendy** działania kopiowania typu **RelationalSource)** |

## <a name="copy-activity-properties"></a>Kopiowanie właściwości działania

Aby uzyskać listę sekcji i właściwości, które są dostępne do definiowania działań, zobacz [tworzenie potoków](data-factory-create-pipelines.md) artykułu. **Nazwa,** **opis,** **tabela danych wejściowych,** **tabela danych wyjściowych** i właściwości **zasad** są dostępne dla wszystkich typów działań. Właściwości, które są dostępne w sekcji **typeProperties** różnią się dla każdego typu działania. W przypadku działania kopiowania właściwości różnią się w zależności od typów źródeł danych i pochłaniacze.

W przypadku działania kopiowania, gdy źródłem jest typ **AmazonRedshiftSource,** w sekcji **typeProperties** dostępne są następujące właściwości:

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| **query** | Użyj kwerendy niestandardowej, aby odczytać dane. |Nie (jeśli określono właściwość **tableName** zestawu danych) |
| **redshiftUnloadSettings** | Zawiera grupę właściwości podczas korzystania z polecenia Redshift **UNLOAD.** | Nie |
| **s3LinkedServiceName** | Amazon S3 do wykorzystania jako sklep tymczasowy. Połączona usługa jest określona przy użyciu nazwy usługi Azure Data Factory typu **AwsAccessKey**. | Wymagane podczas korzystania z właściwości **redshiftUnloadSettings** |
| **nazwa łyżki** | Wskazuje wiadro Amazon S3 do przechowywania danych tymczasowych. Jeśli ta właściwość nie jest podana, Funkcja kopiowania automatycznie generuje zasobnik. | Wymagane podczas korzystania z właściwości **redshiftUnloadSettings** |

Alternatywnie można użyć **RelationalSource** typu, który obejmuje Amazon Redshift, z następującą właściwość w **sekcji typeProperties.** Uwaga Ten typ źródła nie obsługuje redshift **UNLOAD** polecenia.

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| **query** |Użyj kwerendy niestandardowej, aby odczytać dane. | Nie (jeśli określono właściwość **tableName** zestawu danych) |

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>Kopiowanie danych z usługi Amazon Redshift za pomocą funkcji UNLOAD

Polecenie Amazon Redshift [**UNLOAD**](https://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) zwalnia wyniki kwerendy do jednego lub więcej plików na Amazon S3. To polecenie jest zalecane przez firmę Amazon do kopiowania dużych zestawów danych z redshift.

**Przykład: Kopiowanie danych z usługi Amazon Redshift do usługi Azure SQL Data Warehouse**

W tym przykładzie kopiuje dane z Amazon Redshift do usługi Azure SQL Data Warehouse. W przykładzie użyto polecenia Redshift **UNLOAD,** danych kopii etapowej i microsoft polybase.

W tym przykładowym przypadku użycia copy activity najpierw zwalnia dane z Amazon Redshift do Amazon S3 skonfigurowane w opcji **redshiftUnloadSettings.** Następnie dane są kopiowane z Amazon S3 do magazynu obiektów Blob platformy Azure, jak określono w **stagingSettings** opcji. Na koniec PolyBase ładuje dane do magazynu danych SQL. Wszystkie formaty tymczasowe są obsługiwane przez działanie kopiowania.

![Kopiowanie przepływu pracy z usługi Amazon Redshift do magazynu danych SQL](media/data-factory-amazon-redshift-connector/redshift-to-sql-dw-copy-workflow.png)

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

## <a name="json-example-copy-data-from-amazon-redshift-to-azure-blob-storage"></a>Przykład JSON: Kopiowanie danych z usługi Amazon Redshift do magazynu obiektów Blob platformy Azure
W tym przykładzie pokazano, jak skopiować dane z bazy danych Amazon Redshift do usługi Azure Blob Storage. Dane mogą być kopiowane bezpośrednio do dowolnego [obsługiwanego ujścia](data-factory-data-movement-activities.md#supported-data-stores-and-formats) przy użyciu działania kopiowania.

Próbka ma następujące jednostki fabryki danych:

* Powiązana usługa typu [AmazonRedshift](#linked-service-properties)
* Połączona usługa typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [RelationalTable](#dataset-properties)
* Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* [Potok](data-factory-create-pipelines.md) z działaniem kopiowania, który używa właściwości [RelationalSource](#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)

Przykładowy kopie dane z wyniku kwerendy w Amazon Redshift do azure obiektu blob co godzinę. Właściwości JSON, które są używane w przykładzie są opisane w sekcjach, które są zgodne z definicjami jednostki.

**Usługa połączona z Amazon Redshift**

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

**Usługa połączona z magazynem obiektów Blob platformy Azure**

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

Właściwość **zewnętrzna** jest ustawiona na "true", aby poinformować usługę Data Factory, że zestaw danych jest zewnętrzny dla fabryki danych. To ustawienie właściwości wskazuje, że zestaw danych nie jest produkowany przez działanie w fabryce danych. Ustaw właściwość true na wejściowym zestawie danych, który nie jest produkowany przez działanie w potoku.

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

Dane są zapisywane w nowym obiekcie blob co godzinę, ustawiając właściwość **frequency** na "Godzina" i właściwość **interwału** na 1. Właściwość **folderPath** dla obiektu blob jest oceniana dynamicznie. Wartość właściwości jest oparta na godzinie rozpoczęcia wycinka, który jest przetwarzany. Ścieżka folderu używa części roku, miesiąca, dnia i godziny godziny godziny rozpoczęcia.

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

**Kopiowanie działania w potoku za pomocą źródła redshift usługi Azure (typu RelationalSource) i ujścia obiektów Blob platformy Azure**

Potok zawiera działanie kopiowania, które jest skonfigurowane do używania wejściowych i wyjściowych zestawów danych. Potok jest zaplanowane do uruchomienia co godzinę. W definicji JSON dla potoku typ **źródła** jest ustawiony na **RelationalSource,** a typ **ujścia** jest ustawiony na **BlobSink**. Kwerenda SQL określona dla właściwości **kwerendy** wybiera dane do skopiowania z ostatniej godziny.

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
### <a name="type-mapping-for-amazon-redshift"></a>Mapowanie typów dla Amazon Redshift
Jak wspomniano w [artykule działania przenoszenia danych,](data-factory-data-movement-activities.md) Działanie kopiowania wykonuje automatyczne konwersje typu z typu źródłowego do typu ujścia. Typy są konwertowane przy użyciu podejścia dwuetapowego:

1. Konwertowanie z macierzystego typu źródła na typ .NET
2. Konwertowanie z typu .NET na typ ujścia macierzystego

Następujące mapowania są używane, gdy działanie kopiowania konwertuje dane z typu Amazon Redshift na typ .NET:

| Amazon Redshift typu | Typ .NET |
| --- | --- |
| Smallint |Int16 |
| LICZBA CAŁKOWITA |Int32 |
| Bigint |Int64 |
| Dziesiętnych |Wartość dziesiętna |
| LICZBA RZECZYWISTA |Single |
| PODWÓJNA PRECYZJA |Double |
| Boolean |Ciąg |
| Char |Ciąg |
| Varchar |Ciąg |
| DATE |DateTime |
| Sygnatury czasowej |DateTime |
| TEKST |Ciąg |

## <a name="map-source-to-sink-columns"></a>Mapowanie źródła do kolumn ujścia
Aby dowiedzieć się, jak mapować kolumny w źródłowym zestawie danych na kolumny w zestawie danych ujścia, zobacz [Mapowanie kolumn zestawu danych w usłudze Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-reads-from-relational-sources"></a>Powtarzalne odczyty ze źródeł relacyjnych
Podczas kopiowania danych z relacyjnego magazynu danych należy pamiętać o powtarzalności, aby uniknąć niezamierzonych wyników. W usłudze Azure Data Factory można ponownie uruchomić plasterek ręcznie. Można również skonfigurować **zasady** ponawiania dla zestawu danych, aby ponownie uruchomić plasterek, gdy wystąpi błąd. Upewnij się, że te same dane są odczytywane, niezależnie od tego, ile razy plasterek jest ponownie odtwarzany. Upewnij się również, że te same dane są odczytywane niezależnie od sposobu ponownego uruchomienia plasterka. Aby uzyskać więcej informacji, zobacz [Powtarzalne odczyty ze źródeł relacyjnych](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Wydajności i dostosowywanie
Dowiedz się więcej o kluczowych czynnikach, które wpływają na wydajność działania kopiowania i sposobach optymalizacji wydajności w [przewodniku Wydajność i dostrajanie działania kopiowania](data-factory-copy-activity-performance.md).

## <a name="next-steps"></a>Następne kroki
Aby uzyskać instrukcje krok po kroku dotyczące tworzenia potoku z działaniem kopiowania, zobacz [samouczek Działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
