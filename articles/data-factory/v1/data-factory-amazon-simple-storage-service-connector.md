---
title: Przenoszenie danych z usługi Amazon Simple Storage Service przy użyciu usługi Data Factory
description: Dowiedz się, jak przenieść dane z amazon simple storage service (S3) przy użyciu usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 636d3179-eba8-4841-bcb4-3563f6822a26
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: b23be9901df7ca435f412d9f49e1a7ad88382ade
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281654"
---
# <a name="move-data-from-amazon-simple-storage-service-by-using-azure-data-factory"></a>Przenoszenie danych z usługi Amazon Simple Storage service przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](data-factory-amazon-simple-storage-service-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-amazon-simple-storage-service.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [złącze Amazon S3 w wersji V2](../connector-amazon-simple-storage-service.md).

W tym artykule wyjaśniono, jak używać działania kopiowania w usłudze Azure Data Factory do przenoszenia danych z usługi Amazon Simple Storage Service (S3). Opiera się na [działaniach przenoszenia danych,](data-factory-data-movement-activities.md) który przedstawia ogólny przegląd przenoszenia danych z działaniem kopiowania.

Możesz skopiować dane z Amazon S3 do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych obsługiwanych jako pochłaniacze przez działanie kopiowania, zobacz tabelę [Obsługiwane magazyny danych.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) Data Factory obsługuje obecnie tylko przenoszenie danych z Amazon S3 do innych magazynów danych, ale nie przenosi danych z innych magazynów danych do Amazon S3.

## <a name="required-permissions"></a>Wymagane uprawnienia
Aby skopiować dane z Amazon S3, upewnij się, że przyznano następujące uprawnienia:

* `s3:GetObject`i `s3:GetObjectVersion` dla Amazon S3 Object Operations.
* `s3:ListBucket`do operacji wiader amazon S3. Jeśli używasz Kreatora kopiowania `s3:ListAllMyBuckets` fabryki danych, jest również wymagane.

Aby uzyskać szczegółowe informacje na temat pełnej listy uprawnień Amazon S3, zobacz [Określanie uprawnień w zasadach](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html).

## <a name="getting-started"></a>Wprowadzenie
Potok można utworzyć z działaniem kopiowania, które przenosi dane ze źródła Amazon S3 przy użyciu różnych narzędzi lub interfejsów API.

Najprostszym sposobem utworzenia potoku jest użycie **Kreatora kopiowania**. Aby uzyskać szybki instruktaż, zobacz [Samouczek: Tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md).

Do utworzenia potoku można również użyć następujących narzędzi: **Visual Studio**, **Azure PowerShell**, **szablon usługi Azure Resource Manager**, **.NET API**i REST **API**. Aby uzyskać instrukcje krok po kroku, aby utworzyć potok z działaniem kopiowania, zobacz [samouczek Kopiowania działania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Niezależnie od tego, czy używasz narzędzi, czy interfejsów API, wykonaj następujące kroki, aby utworzyć potok, który przenosi dane ze źródłowego magazynu danych do magazynu danych ujścia:

1. Tworzenie **połączonych usług** w celu połączenia magazynów danych wejściowych i wyjściowych z fabryką danych.
2. Tworzenie **zestawów danych** do reprezentowania danych wejściowych i wyjściowych dla operacji kopiowania.
3. Utwórz **potok** z działaniem kopiowania, które przyjmuje zestaw danych jako dane wejściowe i zestaw danych jako dane wyjściowe.

Korzystając z kreatora, definicje JSON dla tych jednostek usługi Data Factory (połączone usługi, zestawy danych i potok) są tworzone automatycznie dla Ciebie. Podczas korzystania z narzędzi lub interfejsów API (z wyjątkiem interfejsu API platformy .NET) definiuje się te jednostki fabryki danych przy użyciu formatu JSON. W przypadku przykładu z definicjami JSON dla jednostek usługi Data Factory, które są używane do kopiowania danych z magazynu danych Amazon S3, zobacz [przykład JSON: Kopiowanie danych z Amazon S3 do usługi Azure Blob](#json-example-copy-data-from-amazon-s3-to-azure-blob-storage) sekcji tego artykułu.

> [!NOTE]
> Aby uzyskać szczegółowe informacje na temat obsługiwanych formatów plików i kompresji dla działania kopiowania, zobacz [Formaty plików i kompresji w usłudze Azure Data Factory](data-factory-supported-file-and-compression-formats.md).

Poniższe sekcje zawierają szczegółowe informacje o właściwościach JSON, które są używane do definiowania jednostek usługi Data Factory specyficznych dla Amazon S3.

## <a name="linked-service-properties"></a>Połączone właściwości usługi
Połączona usługa łączy magazyn danych z fabryką danych. Utwórz połączony serwis typu **AwsAccessKey,** aby połączyć magazyn danych Amazon S3 z fabryką danych. Poniższa tabela zawiera opis elementów JSON specyficznych dla amazon S3 (AwsAccessKey) połączone usługi.

| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| identyfikator klucza accessKeyID |Identyfikator tajnego klucza dostępu. |ciąg |Tak |
| klucz tajnyAccess |Sam klucz dostępu tajnego. |Zaszyfrowany tajny ciąg |Tak |

>[!NOTE]
>Ten łącznik wymaga kluczy dostępu dla konta IAM do kopiowania danych z Amazon S3. [Tymczasowe poświadczenia zabezpieczeń](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html) nie są obsługiwane.
>

Oto przykład:

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AwsAccessKey",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": "<secret access key>"
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby określić zestaw danych do reprezentowania danych wejściowych w magazynie obiektów Blob platformy Azure, należy ustawić właściwość typu zestawu danych na **AmazonS3**. Ustaw właściwość **linkedServiceName** zestawu danych na nazwę połączonej usługi Amazon S3. Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz [Tworzenie zestawów danych](data-factory-create-datasets.md). 

Sekcje, takie jak struktura, dostępność i zasady są podobne dla wszystkich typów zestawów danych (takich jak baza danych SQL, obiekt blob platformy Azure i tabela platformy Azure). Sekcja **typeProperties** jest inna dla każdego typu zestawu danych i zawiera informacje o lokalizacji danych w magazynie danych. Sekcja **typeProperties** dla zestawu danych typu **AmazonS3** (który zawiera zestaw danych Amazon S3) ma następujące właściwości:

| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| nazwa łyżki |Nazwa zasobnika S3. |Ciąg |Tak |
| key |Klucz obiektu S3. |Ciąg |Nie |
| Prefiks |Prefiks klucza obiektu S3. Obiekty, których klucze zaczynają się od tego prefiksu są zaznaczone. Ma zastosowanie tylko wtedy, gdy klucz jest pusty. |Ciąg |Nie |
| version |Wersja obiektu S3, jeśli jest włączona wersja S3. |Ciąg |Nie |
| format | Obsługiwane są następujące typy formatów: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ustaw właściwość **typu** w formacie na jedną z tych wartości. Aby uzyskać więcej informacji, zobacz [sekcje Format tekstu,](data-factory-supported-file-and-compression-formats.md#text-format) [Format JSON,](data-factory-supported-file-and-compression-formats.md#json-format) [Format Avro,](data-factory-supported-file-and-compression-formats.md#avro-format) [Format Orków](data-factory-supported-file-and-compression-formats.md#orc-format)i [Format parkietu.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Jeśli chcesz skopiować pliki jako znajdujące się między magazynami opartymi na plikach (kopia binarna), pomiń sekcję formatu w definicjach danych wejściowych i wyjściowych. | |Nie |
| kompresja | Określ typ i poziom kompresji danych. Obsługiwane typy to: **GZip**, **Deflate**, **BZip2**i **ZipDeflate**. Obsługiwane poziomy to: **Optymalne** i **najszybsze**. Aby uzyskać więcej informacji, zobacz [Formaty plików i kompresji w usłudze Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). | |Nie |


> [!NOTE]
> **bucketName + key** określa lokalizację obiektu S3, gdzie wiadro jest kontenerem głównym dla obiektów S3, a klucz jest pełną ścieżką do obiektu S3.

### <a name="sample-dataset-with-prefix"></a>Przykładowy zestaw danych z prefiksem

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "prefix": "testFolder/test",
            "bucketName": "testbucket",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
### <a name="sample-dataset-with-version"></a>Przykładowy zestaw danych (z wersją)

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "key": "testFolder/test.orc",
            "bucketName": "testbucket",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

### <a name="dynamic-paths-for-s3"></a>Ścieżki dynamiczne dla S3
W poprzednim przykładzie użyto stałych wartości dla **właściwości klucza** i **bucketName** w zestawie danych Amazon S3.

```json
"key": "testFolder/test.orc",
"bucketName": "testbucket",
```

Usługa Data Factory może obliczać te właściwości dynamicznie w czasie wykonywania przy użyciu zmiennych systemowych, takich jak SliceStart.

```json
"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"
```

Można zrobić to samo dla właściwości **prefiksu** zestawu danych Amazon S3. Aby uzyskać listę obsługiwanych funkcji i zmiennych, zobacz [Funkcje fabryki danych i zmienne systemowe](data-factory-functions-variables.md).

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz [Tworzenie potoków](data-factory-create-pipelines.md). Właściwości, takie jak nazwa, opis, tabele danych wejściowych i wyjściowych oraz zasady są dostępne dla wszystkich typów działań. Właściwości dostępne w sekcji **typeProperties** działania różnią się w zależności od typu działania. W przypadku działania kopiowania właściwości różnią się w zależności od typów źródeł i pochłaniacze. Gdy źródło w działaniu kopiowania jest typu **FileSystemSource** (który obejmuje Amazon S3), następująca właściwość jest dostępna w sekcji **typeProperties:**

| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| Cykliczne |Określa, czy w katalogu mają być rekursywnie wymieniane obiekty S3. |prawda/fałsz |Nie |

## <a name="json-example-copy-data-from-amazon-s3-to-azure-blob-storage"></a>Przykład JSON: kopiowanie danych z usługi Amazon S3 do magazynu obiektów Blob platformy Azure
W tym przykładzie pokazano, jak skopiować dane z amazon S3 do magazynu obiektów Blob platformy Azure. Jednak dane mogą być kopiowane bezpośrednio do [dowolnego ujścia, które są obsługiwane](data-factory-data-movement-activities.md#supported-data-stores-and-formats) przy użyciu działania kopiowania w fabryce danych.

Przykład zawiera definicje JSON dla następujących jednostek usługi Data Factory. Za pomocą tych definicji można utworzyć potok do kopiowania danych z magazynu Amazon S3 do obiektów Blob przy użyciu [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub programu [PowerShell.](data-factory-copy-activity-tutorial-using-powershell.md)   

* Połączona usługa typu [AwsAccessKey](#linked-service-properties).
* Połączona usługa typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [AmazonS3](#dataset-properties).
* Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* [Potok](data-factory-create-pipelines.md) z działaniem kopiowania, który używa [FileSystemSource](#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Przykładowy kopie dane z Amazon S3 do obiektu blob platformy Azure co godzinę. Właściwości JSON używane w tych próbkach są opisane w sekcjach następujących po próbkach.

### <a name="amazon-s3-linked-service"></a>Usługa połączona z Amazon S3

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AwsAccessKey",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": "<secret access key>"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Połączona usługa Azure Storage

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

### <a name="amazon-s3-input-dataset"></a>Zestaw danych wejściowych Amazon S3

Ustawienie **"zewnętrzne": true** informuje usługę Data Factory, że zestaw danych jest zewnętrzny dla fabryki danych. Ustaw tę właściwość na true na wejściowym zestawie danych, który nie jest produkowany przez działanie w potoku.

```json
    {
        "name": "AmazonS3InputDataset",
        "properties": {
            "type": "AmazonS3",
            "linkedServiceName": "AmazonS3LinkedService",
            "typeProperties": {
                "key": "testFolder/test.orc",
                "bucketName": "testbucket",
                "format": {
                    "type": "OrcFormat"
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true
        }
    }
```


### <a name="azure-blob-output-dataset"></a>Wyjściowy zestaw danych obiektów blob platformy Azure

Dane są zapisywane w nowym obiekcie blob co godzinę (częstotliwość: godzina, interwał: 1). Ścieżka folderu obiektu blob jest dynamicznie oceniana na podstawie czasu rozpoczęcia przetwarzanego plasterka. Ścieżka folderu używa części roku, miesiąca, dnia i godziny godziny godziny rozpoczęcia.

```json
{
    "name": "AzureBlobOutputDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/fromamazons3/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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


### <a name="copy-activity-in-a-pipeline-with-an-amazon-s3-source-and-a-blob-sink"></a>Kopiowanie działania w potoku ze źródłem Amazon S3 i zlewem obiektów blob

Potok zawiera działanie kopiowania, który jest skonfigurowany do korzystania z wejściowych i wyjściowych zestawów danych i jest zaplanowane do uruchomienia co godzinę. W definicji JSON potoku typ **źródła** jest ustawiony na **FileSystemSource,** a typ **ujścia** jest ustawiony na **BlobSink**.

```json
{
    "name": "CopyAmazonS3ToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "FileSystemSource",
                        "recursive": true
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "AmazonS3InputDataset"
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
                "name": "AmazonS3ToBlob"
            }
        ],
        "start": "2014-08-08T18:00:00Z",
        "end": "2014-08-08T19:00:00Z"
    }
}
```
> [!NOTE]
> Aby mapować kolumny ze źródłowego zestawu danych na kolumny z zestawu danych ujścia, zobacz [Mapowanie kolumn zestawu danych w usłudze Azure Data Factory](data-factory-map-columns.md).


## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

* Aby dowiedzieć się więcej o kluczowych czynnikach wpływających na wydajność przenoszenia danych (aktywność kopiowania) w fabryce danych oraz na różnych sposobach jej optymalizacji, zobacz [przewodnik Wydajność działania kopiowania i dostrajanie](data-factory-copy-activity-performance.md).

* Aby uzyskać instrukcje krok po kroku dotyczące tworzenia potoku z działaniem kopiowania, zobacz [samouczek Kopiowania aktywności](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
