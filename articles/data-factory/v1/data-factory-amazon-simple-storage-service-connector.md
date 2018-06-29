---
title: Przenoszenie danych z usługi magazynowania proste Amazon przy użyciu fabryki danych | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposobu przenoszenia danych z usługi magazynowania proste Amazon (S3) przy użyciu fabryki danych Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 636d3179-eba8-4841-bcb4-3563f6822a26
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: d895dcdf9eefac01790aab6dc3f36a3feb0a8b12
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37051154"
---
# <a name="move-data-from-amazon-simple-storage-service-by-using-azure-data-factory"></a>Przenoszenia danych z usługi magazynowania proste Amazon przy użyciu fabryki danych Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [W wersji 1](data-factory-amazon-simple-storage-service-connector.md)
> * [W wersji 2 (bieżąca wersja)](../connector-amazon-simple-storage-service.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 fabryki danych. Jeśli używasz bieżącą wersję usługi fabryka danych, zobacz [Amazon S3 łącznika w wersji 2](../connector-amazon-simple-storage-service.md).

W tym artykule opisano sposób użycia działanie kopiowania w fabryce danych Azure, aby przenieść dane z usługi Amazon proste usługi Storage (S3). Opiera się na [działań przepływu danych](data-factory-data-movement-activities.md) artykułu, który przedstawia ogólny przegląd przenoszenia danych z działania kopiowania.

Możesz skopiować dane z usługi Amazon S3 żadnych obsługiwanych ujścia magazynu danych. Lista magazynów danych obsługiwane jako wychwytywanie przez działanie kopiowania, zobacz [obsługiwane magazyny danych](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabeli. Fabryka danych aktualnie obsługuje tylko przenoszenia danych z usługi Amazon S3 do innych magazynów danych, ale nie przenoszenia danych z innych danych są przechowywane na Amazon S3.

## <a name="required-permissions"></a>Wymagane uprawnienia
Aby skopiować dane z usługi Amazon S3, upewnij się, że przyznano następujące uprawnienia:

* `s3:GetObject` i `s3:GetObjectVersion` Amazon S3 obiektu operacji.
* `s3:ListBucket` operacjach Amazon S3 zasobnika. Jeśli używasz kreatora kopiowania fabryki danych `s3:ListAllMyBuckets` jest również wymagany.

Aby uzyskać więcej informacji o pełną listę uprawnień Amazon S3, zobacz [określanie uprawnień w zasadach](http://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html).

## <a name="getting-started"></a>Wprowadzenie
Można utworzyć potoku o działanie kopiowania, który przenosi dane ze źródła Amazon S3 przy użyciu różnych narzędzi lub interfejsów API.

Najprostszym sposobem, aby utworzyć potok jest użycie **kreatora kopiowania**. Przewodnik Szybki, zobacz [samouczek: tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md).

Umożliwia także następujące narzędzia do tworzenia potoku: **portalu Azure**, **programu Visual Studio**, **programu Azure PowerShell**, **szablonu usługi Azure Resource Manager** , **Interfejs API .NET**, i **interfejsu API REST**. Aby uzyskać instrukcje utworzyć potok z działania kopiowania, zobacz [samouczek działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Czy za pomocą narzędzia lub interfejsów API, należy wykonać następujące kroki, aby utworzyć potok, który przenosi dane z magazynu danych źródła do ujścia magazynu danych:

1. Utwórz **połączone usługi** Aby połączyć dane wejściowe i wyjściowe są przechowywane w fabryce danych.
2. Utwórz **zestawów danych** do reprezentowania danych wejściowych i wyjściowych operacji kopiowania.
3. Utwórz **potoku** aktywnością kopiowania zestawu danych jako dane wejściowe i zestawu danych jako dane wyjściowe.

Korzystając z kreatora, definicje JSON do tych jednostek fabryki danych (połączone usługi, zestawy danych i potoki) są tworzone automatycznie dla Ciebie. Korzystając z narzędzia lub interfejsów API (z wyjątkiem interfejs API .NET), należy zdefiniować tych jednostek fabryki danych w formacie JSON. Dla przykładu z definicji JSON dla jednostek fabryki danych, które są używane do kopiowania danych z magazynu danych Amazon S3, zobacz [przykład JSON: kopiowanie danych z usługi Amazon S3 do obiektów Blob platformy Azure](#json-example-copy-data-from-amazon-s3-to-azure-blob-storage) sekcji tego artykułu.

> [!NOTE]
> Aby uzyskać więcej informacji o obsługiwanych formatów plików i kompresji dla działania kopiowania, zobacz [formaty plików i kompresji w fabryce danych Azure](data-factory-supported-file-and-compression-formats.md).

Poniższe sekcje zawierają szczegółowe informacje o właściwości JSON, które są używane do definiowania jednostek fabryki danych określonej do Amazon S3.

## <a name="linked-service-properties"></a>Połączona usługa właściwości
Połączona usługa łączy magazynu danych z fabryki danych. Tworzenie połączonej usługi typu **AwsAccessKey** połączyć usługi Amazon S3 data store z fabryką danych. Poniższa tabela zawiera opis określone elementy JSON do Amazon S3 (AwsAccessKey) połączonej usługi.

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| accessKeyID |Identyfikator klucza tajnego dostępu. |ciąg |Yes |
| secretAccessKey |Samego klucza tajnego dostępu. |Zaszyfrowanego ciągu tajny |Yes |

>[!NOTE]
>Ten łącznik wymaga klucze dostępu dla konta IAM skopiować dane z usługi Amazon S3. [Poświadczenia zabezpieczeń tymczasowego](http://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html) nie jest obsługiwane.
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
Aby określić zestaw danych do reprezentowania danych wejściowych w magazynie obiektów Blob platformy Azure, ustaw właściwość Typ zestawu danych do **AmazonS3**. Ustaw **linkedServiceName** właściwości zestawu danych do nazwy Amazon S3 połączonej usługi. Aby uzyskać pełną listę właściwości dostępnych do definiowania zestawów danych i sekcje, zobacz [Tworzenie zbiorów danych](data-factory-create-datasets.md). 

Sekcje, takie jak struktury, dostępności i zasady są podobne dla wszystkich typów zestawu danych (takich jak bazy danych SQL, obiektów blob platformy Azure i tabeli platformy Azure). **TypeProperties** sekcja jest różne dla każdego typu zestawu danych i zawiera informacje o lokalizacji danych w magazynie danych. **TypeProperties** sekcja dla zestawu danych typu **AmazonS3** (w tym zestawie danych Amazon S3) ma następujące właściwości:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| bucketName |Nazwa pakietu S3. |Ciąg |Yes |
| key |Klucz obiektu S3. |Ciąg |Nie |
| Prefiks |Prefiks klucza obiektu S3. Wybrano obiektów, w której klucze uruchomienia z tym prefiksem. Ma zastosowanie tylko wtedy, gdy klucz jest pusty. |Ciąg |Nie |
| wersja |Wersja obiektu S3, jeśli włączono S3 przechowywania wersji. |Ciąg |Nie |
| Format | Obsługiwane są następujące typy format: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**,  **ParquetFormat**. Ustaw **typu** właściwości w formacie do jednej z tych wartości. Aby uzyskać więcej informacji, zobacz [formacie tekstowym](data-factory-supported-file-and-compression-formats.md#text-format), [formatu JSON](data-factory-supported-file-and-compression-formats.md#json-format), [Avro format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc format](data-factory-supported-file-and-compression-formats.md#orc-format), i [Parquet format ](data-factory-supported-file-and-compression-formats.md#parquet-format) sekcje. <br><br> Jeśli chcesz skopiować pliki jako — jest między opartych na plikach magazynów (kopia binarnego), Pomiń sekcji format w obu definicji zestawu danych wejściowych i wyjściowych. |Nie | |
| Kompresja | Określ typ i poziom kompresji danych. Obsługiwane typy to: **GZip**, **Deflate**, **BZip2**, i **ZipDeflate**. Obsługiwane poziomy: **optymalna** i **najszybciej**. Aby uzyskać więcej informacji, zobacz [formaty plików i kompresji w fabryce danych Azure](data-factory-supported-file-and-compression-formats.md#compression-support). |Nie | |


> [!NOTE]
> **bucketName + klawisz** Określa lokalizację obiektu S3, gdzie zasobnika jest nadrzędny kontener dla obiektów S3, a klucz jest pełną ścieżką do obiektu S3.

### <a name="sample-dataset-with-prefix"></a>Przykładowego zestawu danych z prefiksem

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
### <a name="sample-dataset-with-version"></a>Przykładowego zestawu danych (z wersją)

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

### <a name="dynamic-paths-for-s3"></a>Dynamiczne ścieżki S3
Wartości stałe dla korzysta z powyższego przykładu **klucza** i **bucketName** właściwości w elemencie dataset Amazon S3.

```json
"key": "testFolder/test.orc",
"bucketName": "testbucket",
```

Program może obliczyć te właściwości dynamicznie w czasie wykonywania za pomocą zmiennych systemowych, takich jak SliceStart fabryki danych.

```json
"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"
```

Wykonaj te same **prefiks** właściwości Amazon S3 zestawu danych. Aby uzyskać listę obsługiwanych funkcji i zmiennych, zobacz [funkcje fabryki danych i zmienne systemu](data-factory-functions-variables.md).

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Pełną listę sekcje i właściwości dostępnych dla definiowania działań, zobacz [tworzenie potoków](data-factory-create-pipelines.md). Właściwości, takie jak nazwa, opis, dane wejściowe i wyjściowe tabel i zasady są dostępne dla wszystkich typów działań. Właściwości dostępne w **typeProperties** sekcji działania zależne od każdego typu działania. Dla działania kopiowania właściwości się różnić w zależności od typów źródeł i sink. Gdy źródła w przypadku działania kopiowania jest typu **FileSystemSource** (która obejmuje Amazon S3), jest dostępna w następujących właściwości **typeProperties** sekcji:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| cykliczne |Określa, czy do rekursywnie lista S3 obiektów w katalogu. |wartość true, false |Nie |

## <a name="json-example-copy-data-from-amazon-s3-to-azure-blob-storage"></a>Przykład JSON: kopiowanie danych z usługi Amazon S3 do magazynu obiektów Blob platformy Azure
W tym przykładzie pokazano, jak skopiować dane z usługi Amazon S3 do magazynu obiektów Blob platformy Azure. Jednak możesz skopiować dane bezpośrednio do [żadnego wychwytywanie, które są obsługiwane](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania w fabryce danych.

Przykład zawiera definicje JSON dla następujących jednostek fabryki danych. Te definicje umożliwia tworzenie potoku, aby skopiować dane z usługi Amazon S3 do magazynu obiektów Blob za pomocą [portalu Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), lub [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md).   

* Połączonej usługi typu [AwsAccessKey](#linked-service-properties).
* Połączonej usługi typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Dane wejściowe [dataset](data-factory-create-datasets.md) typu [AmazonS3](#dataset-properties).
* Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* A [potoku](data-factory-create-pipelines.md) z działaniem kopii, która używa [FileSystemSource](#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Przykład kopiuje dane z usługi Amazon S3 obiektów blob platformy Azure co godzinę. Właściwości JSON używane w te przykłady są opisane w sekcjach poniżej próbek.

### <a name="amazon-s3-linked-service"></a>Usługi Amazon S3 połączone

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

Ustawienie **"external": true** informuje usługi fabryka danych z zestawu danych może być zewnętrzne fabryki danych. Ustaw tę właściwość na wartość true w wejściowy zestaw danych, który nie jest generowany przez działania w potoku.

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

Dane są zapisywane do nowego obiektu blob co godzinę (częstotliwość: godziny, interwał: 1). Ścieżka folderu dla obiekt blob jest dynamicznie obliczane na podstawie czasu rozpoczęcia wycinek, który jest przetwarzana. Ścieżka folderu używa rok, miesiąc, dzień i godziny części czas rozpoczęcia.

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


### <a name="copy-activity-in-a-pipeline-with-an-amazon-s3-source-and-a-blob-sink"></a>Działanie kopiowania w potoku ze źródłem Amazon S3 i ujście obiektów blob

Potok zawiera działanie kopiowania, który jest skonfigurowany do używania wejściowe i wyjściowe zestawy danych i jest zaplanowane co godzinę. W definicji JSON potoku **źródła** ustawiono typ **FileSystemSource**, i **zbiornika** ustawiono typ **BlobSink**.

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
> Aby mapować kolumn z zestawu źródła danych do kolumn z obiektu sink zestawu danych, zobacz [mapowania kolumnach dataset w fabryce danych Azure](data-factory-map-columns.md).


## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące artykuły:

* Informacje na temat kluczowych czynników tego wydajność wpływ przenoszenia danych (działanie kopiowania) w fabryce danych i zoptymalizować ją na różne sposoby, zobacz [skopiuj wydajności działania i dostrajania przewodnik](data-factory-copy-activity-performance.md).

* Aby uzyskać instrukcje tworzenia potoku z działaniem kopiowania, zobacz [samouczek działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
