---
title: Przenoszenie danych z Amazon Simple Storage Service przy użyciu usługi fabryka danych | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposobu przenoszenia danych z Amazon Simple Storage Service (S3), za pomocą usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 636d3179-eba8-4841-bcb4-3563f6822a26
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 1f5064cece32cfc38f149816961e5156ff20974a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60335338"
---
# <a name="move-data-from-amazon-simple-storage-service-by-using-azure-data-factory"></a>Przenoszenie danych z Amazon Simple Storage Service za pomocą usługi Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1](data-factory-amazon-simple-storage-service-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-amazon-simple-storage-service.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącą wersję usługi Data Factory, zobacz [łącznik Amazon S3 w wersji 2](../connector-amazon-simple-storage-service.md).

W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory, aby przenieść dane z Amazon Simple Storage Service (S3). Opiera się na [działania przenoszenia danych](data-factory-data-movement-activities.md) artykułu, który przedstawia ogólne omówienie przenoszenie danych za pomocą działania kopiowania.

Możesz skopiować dane z usługi Amazon S3, do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych obsługiwanych jako ujścia działania kopiowania, zobacz [obsługiwane magazyny danych](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabeli. Usługa Data Factory obsługuje obecnie tylko przenosi dane z usługi Amazon S3 w innych magazynach danych, ale nie przenosi dane z innych danych są przechowywane na Amazon S3.

## <a name="required-permissions"></a>Wymagane uprawnienia
Aby skopiować dane z usługi Amazon S3, upewnij się, że przyznano następujące uprawnienia:

* `s3:GetObject` i `s3:GetObjectVersion` Amazon S3 obiektu operacji.
* `s3:ListBucket` Amazon S3 zasobnika operacji. Jeśli używasz kreatora kopiowania usługi Data Factory, `s3:ListAllMyBuckets` jest również wymagany.

Aby uzyskać szczegółowe informacje o pełną listę uprawnień Amazon S3, zobacz [określanie uprawnień w zasadach](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html).

## <a name="getting-started"></a>Wprowadzenie
Utworzysz potok z działaniem kopiowania, które przenosi dane ze źródła Amazon S3 przy użyciu różnych narzędzi lub interfejsów API.

Najprostszym sposobem utworzenia potoku jest użycie **kreatora kopiowania**. Aby uzyskać szybki przewodnik, zobacz [samouczka: Tworzenie potoku przy użyciu Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md).

Aby utworzyć potok umożliwia także następujących narzędzi: **Witryna Azure portal**, **programu Visual Studio**, **programu Azure PowerShell**, **szablonu usługi Azure Resource Manager**, **interfejsu API platformy .NET**i  **Interfejs API REST**. Aby uzyskać instrukcje krok po kroku utworzysz potok z działaniem kopiowania, zobacz [samouczka działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Czy używasz narzędzi lub interfejsów API, należy wykonać poniższe kroki, aby utworzyć potok, który przenosi dane z magazynu danych źródłowych do magazynu danych ujścia:

1. Tworzenie **połączonych usług** połączyć dane wejściowe i wyjściowe przechowywane z fabryką danych.
2. Tworzenie **zestawów danych** do reprezentowania dane wejściowe i wyjściowe operacji kopiowania.
3. Tworzenie **potoku** za pomocą działania kopiowania, która przyjmuje jako dane wejściowe zestawu danych i zestaw danych jako dane wyjściowe.

Korzystając z kreatora, definicje JSON dotyczące tych jednostek usługi Data Factory (połączone usługi, zestawy danych i potok) są tworzone automatycznie dla Ciebie. Korzystając z narzędzi lub interfejsów API (z wyjątkiem interfejsu API platformy .NET), należy zdefiniować te jednostki usługi Data Factory przy użyciu formatu JSON. Przykładowe definicje JSON dotyczące jednostek usługi Data Factory, które są używane w celu skopiowania danych z magazynu danych Amazon S3, możesz znaleźć [przykład kodu JSON: Kopiowanie danych z usługi Amazon S3 do usługi Azure Blob](#json-example-copy-data-from-amazon-s3-to-azure-blob-storage) dalszej części tego artykułu.

> [!NOTE]
> Aby uzyskać szczegółowe informacje o obsługiwanych formatów plików i kompresji dla działania kopiowania, zobacz [formaty plików i kompresji w usłudze Azure Data Factory](data-factory-supported-file-and-compression-formats.md).

Poniższe sekcje zawierają szczegółowe informacje o właściwościach JSON, które są używane do definiowania jednostek usługi fabryka danych określonej do Amazon S3.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej
Połączona usługa łączy magazyn danych do usługi data factory. Tworzenie połączonej usługi typu **AwsAccessKey** połączyć magazyn danych usługi Amazon S3 z fabryką danych. Poniższa tabela zawiera opis dla określonych elementów JSON do Amazon S3 (AwsAccessKey) połączoną usługę.

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| accessKeyID |Identyfikator klucza dostępu do kluczy tajnych. |string |Yes |
| secretAccessKey |Sam klucz dostępu do kluczy tajnych. |Zaszyfrowanego ciągu wpisu tajnego |Yes |

>[!NOTE]
>Ten łącznik wymaga klucze dostępu dla konta zarządzania tożsamościami i Dostępem w celu skopiowania danych z usługi Amazon S3. [Tymczasowe poświadczeń zabezpieczeń](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html) nie jest obsługiwane.
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
Aby określić zestaw danych do reprezentowania danych wejściowych usługi Azure Blob Storage, ustaw właściwość typu zestawu danych na **AmazonS3**. Ustaw **linkedServiceName** właściwości zestawu danych do nazwy Amazon S3 połączoną usługę. Aby uzyskać pełną listę sekcje i właściwości dostępne Definiowanie zestawów danych, zobacz [tworzenie zestawów danych](data-factory-create-datasets.md). 

Sekcje, takie jak struktury, dostępność i zasady są podobne dla wszystkich typów w zestawie danych (takich jak bazy danych SQL, obiektów blob platformy Azure i usługi Azure table). **TypeProperties** sekcji różni się dla każdego typu zestawu danych oraz informacje o lokalizacji danych w magazynie danych. **TypeProperties** sekcji dla zestawu danych typu **AmazonS3** (w tym zestawie danych Amazon S3) ma następujące właściwości:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| bucketName |Nazwa zasobnika S3. |String |Yes |
| key |Klucz obiektu usługi S3. |String |Nie |
| Prefiks |Prefiks klucza obiektu S3. Zostaną zaznaczone obiekty, których klucze rozpoczynały od tego prefiksu. Ma zastosowanie tylko wtedy, gdy klucz jest pusty. |String |Nie |
| version |Wersja obiektu S3, jeśli jest włączone w wersji S3. |String |Nie |
| format | Obsługiwane są następujące typy formatów: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ustaw **typu** właściwości w obszarze format ma jedną z następujących wartości. Aby uzyskać więcej informacji, zobacz [format tekstu](data-factory-supported-file-and-compression-formats.md#text-format), [formatu JSON](data-factory-supported-file-and-compression-formats.md#json-format), [Avro format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc format](data-factory-supported-file-and-compression-formats.md#orc-format), i [formatu Parquet ](data-factory-supported-file-and-compression-formats.md#parquet-format) sekcje. <br><br> Jeśli chcesz skopiować pliki — jest między opartych na plikach magazynów (kopia binarna), Pomiń sekcji format w obu definicji zestawu danych wejściowych i wyjściowych. |Nie | |
| Kompresja | Określ typ i poziom kompresji danych. Obsługiwane typy to: **GZip**, **Deflate**, **BZip2**, i **ZipDeflate**. Są obsługiwane poziomy: **Optymalne** i **najszybszy**. Aby uzyskać więcej informacji, zobacz [formaty plików i kompresji w usłudze Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nie | |


> [!NOTE]
> **bucketName + klawisz** Określa lokalizację obiektu S3, gdzie zasobnika jest nadrzędny kontener obiektów S3, a klucz jest pełną ścieżką do obiektu S3.

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
### <a name="sample-dataset-with-version"></a>Przykładowy zestaw danych (wersja)

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
Poprzedni przykład używa wartości stałe dla **klucz** i **bucketName** właściwości w zestawie danych Amazon S3.

```json
"key": "testFolder/test.orc",
"bucketName": "testbucket",
```

Program może obliczyć te właściwości dynamicznie w czasie wykonywania przy użyciu zmiennych systemowych, takich jak SliceStart usługi fabryka danych.

```json
"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"
```

Można takie same jak w przypadku **prefiks** właściwość zestaw danych usługi Amazon S3. Aby uzyskać listę obsługiwanych funkcji i zmiennych, zobacz [funkcji usługi fabryka danych i zmiennych systemowych](data-factory-functions-variables.md).

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [tworzenia potoków](data-factory-create-pipelines.md). Właściwości, takie jak nazwa, opis, dane wejściowe i wyjściowe tabel i zasady są dostępne dla wszystkich typów działań. Właściwości dostępne w **typeProperties** różnią się w sekcji działania za pomocą poszczególnych typów działań. Właściwości różnią się w zależności od tego, jakiego typu źródła i ujścia dla działania kopiowania. Gdy źródłowego w działaniu kopiowania jest typu **FileSystemSource** (w tym Amazon S3), następująca właściwość jest dostępna w **typeProperties** sekcji:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| cykliczne |Określa, czy rekursywnie lista S3 obiektów w katalogu. |PRAWDA/FAŁSZ |Nie |

## <a name="json-example-copy-data-from-amazon-s3-to-azure-blob-storage"></a>Przykład kodu JSON: Kopiowanie danych z usługi Amazon S3 w usłudze Azure Blob storage
Niniejszy przykład pokazuje, jak skopiować dane z usługi Amazon S3 do usługi Azure Blob storage. Jednakże, możesz skopiować dane bezpośrednio do [dowolnego ujścia, które są obsługiwane](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania w fabryce danych.

Przykład zawiera definicje JSON dotyczące następujących jednostek usługi Data Factory. Te definicje umożliwia tworzenie potoku w celu kopiowania danych z usługi Amazon S3 do magazynu obiektów Blob za pomocą [witryny Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md), [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), lub [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md).   

* Połączonej usługi typu [AwsAccessKey](#linked-service-properties).
* Połączonej usługi typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Dane wejściowe [dataset](data-factory-create-datasets.md) typu [AmazonS3](#dataset-properties).
* Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* A [potoku](data-factory-create-pipelines.md) za pomocą działania kopiowania, która używa [FileSystemSource](#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Przykład kopiuje dane z usługi Amazon S3 do obiektu blob platformy Azure co godzinę. Właściwości JSON używanych w tych przykładach są opisane w sekcjach poniżej przykładów.

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

### <a name="amazon-s3-input-dataset"></a>Wejściowy zestaw danych Amazon S3

Ustawienie **"external": true** informuje usługa Data Factory, że zestaw danych jest zewnętrzne z fabryką danych. Ustaw tę właściwość na wartość true dla wejściowego zestawu danych, które nie są generowane przez działanie w potoku.

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

Dane są zapisywane do nowego obiektu blob, co godzinę (frequency: godzina, interwał: 1). Ścieżka folderu dla obiektu blob jest dynamicznie obliczana na podstawie czasu rozpoczęcia wycinek, który jest przetwarzany. Ścieżka folderu używa rok, miesiąc, dzień i części godzin od zaplanowanej godziny rozpoczęcia.

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


### <a name="copy-activity-in-a-pipeline-with-an-amazon-s3-source-and-a-blob-sink"></a>Działanie kopiowania w potoku za pomocą Amazon S3 źródła i ujścia obiektu blob

Potoku zawierającego działanie kopiowania, który jest skonfigurowany do korzystania z danych wejściowych i wyjściowych zestawów danych i jest zaplanowane do uruchomienia na godzinę. W definicji JSON potok **źródła** ustawiono typ **FileSystemSource**, i **ujścia** ustawiono typ **BlobSink**.

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
> Aby zamapować kolumny z zestawu danych źródłowych do kolumn z zestawu danych ujścia, zobacz [mapowanie kolumny zestawu danych w usłudze Azure Data Factory](data-factory-map-columns.md).


## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące artykuły:

* Informacje na temat kluczowych czynników tego obniżenie wydajności przenoszenia danych (działanie kopiowania) w fabryce danych i różne sposoby, aby zoptymalizować ją, zobacz [skopiuj dostrajania przewodnik dotyczący wydajności działania i](data-factory-copy-activity-performance.md).

* Aby uzyskać szczegółowe instrukcje tworzenia potoku za pomocą działania kopiowania, zobacz [samouczka działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
