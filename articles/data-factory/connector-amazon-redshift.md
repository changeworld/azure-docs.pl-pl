---
title: Kopiowanie danych z usługi Amazon Redshift przy użyciu fabryki danych Azure | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o tym, jak można skopiować danych z usługi Amazon Redshift do zbiornika obsługiwanych magazynów danych przy użyciu fabryki danych Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: 5787f944211c5dd6c0c83e7dc10170d8a0cd9a23
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34616614"
---
# <a name="copy-data-from-amazon-redshift-using-azure-data-factory"></a>Kopiowanie danych z usługi Amazon Redshift przy użyciu fabryki danych Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1 — ogólnie dostępna](v1/data-factory-amazon-redshift-connector.md)
> * [Wersja 2 — wersja zapoznawcza](connector-amazon-redshift.md)


Ten artykuł przedstawia sposób użycia działanie kopiowania w fabryce danych Azure można skopiować danych z Redshift Amazon. Opiera się na [skopiuj omówienie działania](copy-activity-overview.md) artykułu, który przedstawia ogólny przegląd działanie kopiowania.

> [!NOTE]
> Ten artykuł dotyczy wersji 2 usługi Data Factory, która jest obecnie dostępna w wersji zapoznawczej. Jeśli używasz wersji 1 usługi fabryka danych, która jest ogólnie dostępna (GA), zobacz [connnector Amazon Redshift w wersji 1](v1/data-factory-amazon-redshift-connector.md).

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Możesz skopiować dane z Amazon Redshift żadnych obsługiwanych ujścia magazynu danych. Lista magazynów danych, które są obsługiwane jako źródła/wychwytywanie przez działanie kopiowania, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności ten łącznik Amazon Redshift obsługuje pobieranie danych z Redshift przy użyciu zapytania lub wbudowana obsługa Redshift UNLOAD.

> [!TIP]
> Aby uzyskać najlepszą wydajność podczas kopiowania dużych ilości danych z Redshift, należy rozważyć użycie wbudowanego zwolnienie Redshift za pomocą usługi Amazon S3. Zobacz [zwolnienie Użyj można skopiować danych z usługi Amazon Redshift](#use-unload-to-copy-data-from-amazon-redshift) sekcji, aby uzyskać szczegółowe informacje.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli kopiujesz magazynu danych do danych lokalnych przy użyciu [środowiska uruchomieniowego integracji Self-hosted](create-self-hosted-integration-runtime.md), udzielić dostępu do klastra usługi Amazon Redshift integrację środowiska uruchomieniowego (Użyj adres IP komputera). Zobacz [autoryzowania dostępu do klastra](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) instrukcje.
* Jeśli dane są kopiowane do magazynu danych Azure, zobacz [zakresy IP centrum danych Azure](https://www.microsoft.com/download/details.aspx?id=41653) koncentruje się na adres IP obliczeniowe i zakresy SQL używane przez usługi Azure data.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek fabryki danych określonej do łącznika Amazon Redshift.

## <a name="linked-service-properties"></a>Połączona usługa właściwości

Obsługiwane są następujące właściwości usługi Amazon Redshift połączone:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi mieć ustawioną: **AmazonRedshift** | Yes |
| serwer |IP adres lub nazwę hosta serwera Amazon Redshift. |Yes |
| port |Numer portu TCP używany przez serwer Amazon Redshift do nasłuchiwania dla połączeń klienta. |Nie, domyślnie jest 5439 |
| baza danych |Nazwa bazy danych Amazon Redshift. |Yes |
| nazwa użytkownika |Nazwa użytkownika, który ma dostęp do bazy danych. |Yes |
| hasło |Hasło dla konta użytkownika. Zaznacz to pole jako SecureString Zapisz w bezpiecznej lokalizacji w fabryce danych lub [odwołania klucz tajny przechowywane w usłudze Azure Key Vault](store-credentials-in-key-vault.md). |Yes |
| connectVia | [Integrację środowiska uruchomieniowego](concepts-integration-runtime.md) ma być używany do nawiązania połączenia z magazynem danych. (Jeśli w magazynie danych znajduje się w sieci prywatnej), można użyć środowiska uruchomieniowego integracji Azure lub Self-hosted integracji w czasie wykonywania. Jeśli nie zostanie określony, używa domyślnej środowiska uruchomieniowego integracji Azure. |Nie |

**Przykład:**

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties":
    {
        "type": "AmazonRedshift",
        "typeProperties":
        {
            "server": "<server name>",
            "database": "<database name>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
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

Aby uzyskać pełną listę właściwości dostępnych do definiowania zestawów danych i sekcje zobacz artykuł zestawów danych. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych Amazon Redshift.

Aby skopiować dane z Amazon Redshift, ustaw właściwość Typ zestawu danych do **RelationalTable**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi mieć ustawioną właściwość type zestawu danych: **RelationalTable** | Yes |
| tableName | Nazwa tabeli w Amazon Redshift. | Nie (Jeśli określono parametr "zapytania" w źródle działania) |

**Przykład**

```json
{
    "name": "AmazonRedshiftDataset",
    "properties":
    {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Amazon Redshift linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Pełną listę sekcje i właściwości dostępnych dla definiowania działań, zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwane przez źródło Amazon Redshift.

### <a name="amazon-redshift-as-source"></a>Redshift Amazon jako źródło

Aby skopiować dane z Amazon Redshift, należy ustawić typ źródła w przypadku działania kopiowania do **AmazonRedshiftSource**. Następujące właściwości są obsługiwane w przypadku działania kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi mieć ustawioną właściwość type źródła działania kopiowania: **AmazonRedshiftSource** | Yes |
| query |Użyj niestandardowych zapytania można odczytać danych. |Ciąg zapytania SQL. Na przykład: Wybierz * z MyTable. |Nie (Jeśli określono parametr "Nazwa_tabeli" w zestawie danych) |
| redshiftUnloadSettings | Grupa właściwości używając Amazon Redshift UNLOAD. | Nie |
| s3LinkedServiceName | Odwołuje się do Amazon S3 do-być używane jako magazyn tymczasowy, określając nazwę połączonej usługi typu "AmazonS3". | Tak, jeśli przy użyciu zwolnienia |
| bucketName | Wskazuje przedział S3 do przechowywania danych tymczasowych. Jeśli nie zostanie podana, usługi fabryka danych generuje go automatycznie.  | Tak, jeśli przy użyciu zwolnienia |

**Przykład: Amazon Redshift źródła w przypadku działania kopiowania za pomocą zwolnienia**

```json
"source": {
    "type": "AmazonRedshiftSource",
    "query": "<SQL query>",
    "redshiftUnloadSettings": {
        "s3LinkedServiceName": {
            "referenceName": "<Amazon S3 linked service>",
            "type": "LinkedServiceReference"
        },
        "bucketName": "bucketForUnload"
    }
}
```

Dowiedz się więcej na temat korzystania UNLOAD można skopiować danych z Redshift Amazon wydajnie z następnej sekcji.

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>Umożliwia kopiowanie danych z usługi Amazon Redshift zwolnienia

[ZWALNIANIE](http://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) mechanizm udostępniane przez firmy Amazon Redshift, które można zwolnić wyników zapytania do jednego lub więcej plików na Amazon proste usługi Storage (Amazon S3). Jest to zalecane przez firmy Amazon do kopiowania dużych zestawów danych z Redshift sposób.

**Przykład: kopiowanie danych z Amazon Redshift do usługi Azure SQL Data Warehouse przy użyciu ZWOLNIONY, przygotowanego kopiowania i PolyBase**

Dla tego przykładu przypadek użycia, skopiuj działania zwalnia dane z Amazon Redshift do Amazon S3 zgodnie z konfiguracją "redshiftUnloadSettings", a następnie skopiuj dane z usługi Amazon S3 do obiektów Blob platformy Azure zgodnie z "stagingSettings" koniec za pomocą programu PolyBase ładowanie danych do danych programu SQL Magazyn. Tymczasowe format jest obsługiwany przez działanie kopiowania poprawnie.

![Redshift do magazynu danych SQL skopiuj przepływ pracy](media\copy-data-from-amazon-redshift\redshift-to-sql-dw-copy-workflow.png)

```json
"activities":[
    {
        "name": "CopyFromAmazonRedshiftToSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "AmazonRedshiftDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AmazonRedshiftSource",
                "query": "select * from MyTable",
                "redshiftUnloadSettings": {
                    "s3LinkedServiceName": {
                        "referenceName": "AmazonS3LinkedService",
                        "type": "LinkedServiceReference"
                    },
                    "bucketName": "bucketForUnload"
                }
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": "AzureStorageLinkedService",
                "path": "adfstagingcopydata"
            },
            "cloudDataMovementUnits": 32
        }
    }
]
```

## <a name="data-type-mapping-for-amazon-redshift"></a>Mapowanie typu danych dla Amazon Redshift

Podczas kopiowania danych z Amazon Redshift, następujące mapowania są używane z Amazon Redshift typów danych do typów danych tymczasowych fabryki danych Azure. Zobacz [schemat i dane typu mapowania](copy-activity-schema-and-type-mapping.md) Aby poznać sposób działania kopiowania mapowania typu źródłowego: schemat i dane sink.

| Typ danych Amazon Redshift | Typ danych tymczasowych fabryki danych |
|:--- |:--- |
| BIGINT |Int64 |
| WARTOŚĆ LOGICZNA |Ciąg |
| CHAR |Ciąg |
| DATE |DateTime |
| DECIMAL |Decimal |
| PODWÓJNEJ PRECYZJI |O podwójnej precyzji |
| LICZBA CAŁKOWITA |Int32 |
| RZECZYWISTE |Pojedyncze |
| SMALLINT |Int16 |
| TEKST |Ciąg |
| ZNACZNIK CZASU |DateTime |
| VARCHAR |Ciąg |

## <a name="next-steps"></a>Kolejne kroki
Lista magazynów danych obsługiwane jako źródła i wychwytywanie przez działanie kopiowania w fabryce danych Azure, zobacz [obsługiwane magazyny danych](copy-activity-overview.md##supported-data-stores-and-formats).
