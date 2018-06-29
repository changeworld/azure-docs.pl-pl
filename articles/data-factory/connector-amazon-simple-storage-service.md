---
title: Kopiowanie danych z usługi Amazon proste usługi magazynu przy użyciu fabryki danych Azure | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o tym, jak można skopiować danych z usługi Amazon proste usługi Storage (S3) do ujścia obsługiwanych magazynów danych przy użyciu fabryki danych Azure.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/25/2018
ms.author: jingwang
ms.openlocfilehash: 3635e8bf1d9ba4061da5b8f416a3b755f7064000
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37045640"
---
# <a name="copy-data-from-amazon-simple-storage-service-using-azure-data-factory"></a>Kopiowanie danych z usługi Amazon proste usługi magazynu przy użyciu fabryki danych Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [W wersji 1](v1/data-factory-amazon-simple-storage-service-connector.md)
> * [Bieżąca wersja](connector-amazon-simple-storage-service.md)

W tym artykule omówiono sposób użycia działanie kopiowania w fabryce danych Azure, aby skopiować dane z usługi Amazon S3. Opiera się na [skopiuj omówienie działania](copy-activity-overview.md) artykułu, który przedstawia ogólny przegląd działanie kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Możesz skopiować dane Amazon S3 żadnych obsługiwanych ujścia magazynu danych. Lista magazynów danych, które są obsługiwane jako źródła lub wychwytywanie przez działanie kopiowania, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności ten łącznik Amazon S3 obsługuje kopiowanie plików jako — jest lub analizowanie plików z [obsługiwane formaty plików i kodery-dekodery kompresji](supported-file-formats-and-compression-codecs.md).

## <a name="required-permissions"></a>Wymagane uprawnienia

Aby skopiować dane z usługi Amazon S3, upewnij się, że przyznano następujące uprawnienia:

- `s3:GetObject` i `s3:GetObjectVersion` Amazon S3 obiektu operacji.
- `s3:ListBucket` lub `s3:GetBucketLocation` Amazon S3 zasobnika operacji. Jeśli używasz kreatora kopiowania fabryki danych `s3:ListAllMyBuckets` jest również wymagany.

Aby uzyskać więcej informacji o pełną listę uprawnień Amazon S3, zobacz [określanie uprawnień w zasadach](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html).

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek fabryki danych określonej do Amazon S3.

## <a name="linked-service-properties"></a>Połączona usługa właściwości

Obsługiwane są następujące właściwości usługi Amazon S3 połączone:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi mieć ustawioną **AmazonS3**. | Yes |
| accessKeyId | Identyfikator klucza tajnego dostępu. |Yes |
| secretAccessKey | Samego klucza tajnego dostępu. Zaznacz to pole jako SecureString Zapisz w bezpiecznej lokalizacji w fabryce danych lub [odwołania klucz tajny przechowywane w usłudze Azure Key Vault](store-credentials-in-key-vault.md). |Yes |
| connectVia | [Integrację środowiska uruchomieniowego](concepts-integration-runtime.md) ma być używany do nawiązania połączenia z magazynem danych. (Jeśli w magazynie danych znajduje się w sieci prywatnej), można użyć środowiska uruchomieniowego integracji Azure lub Self-hosted integracji w czasie wykonywania. Jeśli nie zostanie określony, używa domyślnej środowiska uruchomieniowego integracji Azure. |Nie |

>[!NOTE]
>Ten łącznik wymaga klucze dostępu dla konta IAM skopiować dane z usługi Amazon S3. [Poświadczenia zabezpieczeń tymczasowego](http://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html) nie jest obsługiwane.
>

Oto przykład:

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AmazonS3",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": {
                    "type": "SecureString",
                    "value": "<secret access key>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę właściwości dostępnych do definiowania zestawów danych i sekcje zobacz artykuł zestawów danych. Ta sekcja zawiera listę obsługiwanych przez zestaw danych Amazon S3 właściwości.

Aby skopiować dane z usługi Amazon S3, ustaw właściwość Typ zestawu danych do **AmazonS3Object**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi mieć ustawioną właściwość type zestawu danych: **AmazonS3Object** |Yes |
| bucketName | Nazwa pakietu S3. Filtr symbolu wieloznacznego nie jest obsługiwane. |Yes |
| key | **Nazwę lub symbol wieloznaczny filtr** klucza obiektu S3 pod określony zasobnik. Dotyczy tylko, gdy nie jest określona właściwość "prefiksu". <br/><br/>Filtr symboli wieloznacznych jest obsługiwana tylko dla część nazwy pliku, ale nie należą do folderu. Dozwolone symbole wieloznaczne są: `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub pojedynczy znak).<br/>— Przykład 1: `"key": "rootfolder/subfolder/*.csv"`<br/>— Przykład 2: `"key": "rootfolder/subfolder/???20180427.txt"`<br/>Użyj `^` ucieczki Jeśli nazwę rzeczywisty plik ma symboli wieloznacznych lub ten znak ucieczki wewnątrz. |Nie |
| Prefiks | Prefiks klucza obiektu S3. Wybrano obiektów, w której klucze uruchomienia z tym prefiksem. Ma zastosowanie tylko wtedy, gdy nie określono właściwości "key". |Nie |
| wersja | Wersja obiektu S3, jeśli włączono S3 przechowywania wersji. |Nie |
| Format | Jeśli chcesz **skopiuj pliki jako — jest** między opartych na plikach magazynów (kopia binarnego), Pomiń sekcji format w obu definicji zestawu danych wejściowych i wyjściowych.<br/><br/>Jeśli chcesz przeanalizować lub wygenerować pliki w określonym formacie, obsługiwane są następujące typy plików w formacie: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ustaw **typu** właściwości w formacie do jednej z tych wartości. Aby uzyskać więcej informacji, zobacz [formacie tekstowym](supported-file-formats-and-compression-codecs.md#text-format), [formatu Json](supported-file-formats-and-compression-codecs.md#json-format), [Avro Format](supported-file-formats-and-compression-codecs.md#avro-format), [Orc Format](supported-file-formats-and-compression-codecs.md#orc-format), i [Parquet Format](supported-file-formats-and-compression-codecs.md#parquet-format) sekcje. |Nie (tylko w przypadku scenariusza kopiowania binarny) |
| Kompresja | Określ typ i poziom kompresji danych. Aby uzyskać więcej informacji, zobacz [obsługiwane formaty plików i kodery-dekodery kompresji](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Obsługiwane typy to: **GZip**, **Deflate**, **BZip2**, i **ZipDeflate**.<br/>Obsługiwane poziomy: **optymalna** i **najszybciej**. |Nie |

>[!TIP]
>Aby skopiować wszystkie pliki w folderze, określ **bucketName** zasobnika i **prefiks** części folderu.<br>Aby skopiować pojedynczy plik o podanej nazwie, określ **bucketName** zasobnika i **klucza** nazwy folderu części oraz pliku.<br>Aby skopiować podzestaw pliki w folderze, określ **bucketName** zasobnika i **klucza** folderu filtru części oraz symbol wieloznaczny.

**Przykład: użycie prefiksu**

```json
{
    "name": "AmazonS3Dataset",
    "properties": {
        "type": "AmazonS3Object",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "prefix": "testFolder/test",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

**Przykład: użycie klucza i wersji (opcjonalnie)**

```json
{
    "name": "AmazonS3Dataset",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "key": "testFolder/testfile.csv.gz",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Pełną listę sekcje i właściwości dostępnych dla definiowania działań, zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę obsługiwanych przez źródło Amazon S3 właściwości.

### <a name="amazon-s3-as-source"></a>Amazon S3 jako źródło

Aby skopiować dane z usługi Amazon S3, należy ustawić typ źródła w przypadku działania kopiowania do **FileSystemSource** (w tym Amazon S3). Następujące właściwości są obsługiwane w przypadku działania kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi mieć ustawioną właściwość type źródła działania kopiowania: **FileSystemSource** |Yes |
| cykliczne | Wskazuje, czy dane są odczytywane rekursywnie z folderów sub lub tylko określonego folderu. Uwaga: po cykliczne ma ustawioną wartość PRAWDA, a obiekt sink jest magazynu opartych na plikach, pusty folder/podrzędne-folder nie będą kopiowane utworzone w ujścia.<br/>Dozwolone wartości to: **true** (ustawienie domyślne), **false** | Nie |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromAmazonS3",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Amazon S3 input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "FileSystemSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```
## <a name="next-steps"></a>Kolejne kroki
Lista magazynów danych obsługiwane jako źródła i wychwytywanie przez działanie kopiowania w fabryce danych Azure, zobacz [obsługiwane magazyny danych](copy-activity-overview.md##supported-data-stores-and-formats).
