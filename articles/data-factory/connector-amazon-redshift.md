---
title: Kopiowanie danych z usługi Amazon Redshift, za pomocą usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o tym, jak w celu skopiowania danych z usługi Amazon Redshift do magazynów danych ujścia obsługiwane za pomocą usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: 9e1dde57dc1903e87704bd55fb0b942b7cc349e5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61262323"
---
# <a name="copy-data-from-amazon-redshift-using-azure-data-factory"></a>Kopiowanie danych z usługi Amazon Redshift, za pomocą usługi Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję usługi Data Factory, którego używasz:"]
> * [Wersja 1](v1/data-factory-amazon-redshift-connector.md)
> * [Bieżąca wersja](connector-amazon-redshift.md)


W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory do kopiowania danych z usługi Amazon Redshift. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Możesz skopiować dane z usługi Amazon Redshift do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych, obsługiwane przez działanie kopiowania jako źródła/ujścia, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności ten łącznik Amazon Redshift obsługuje pobieranie danych z usługi Redshift przy użyciu zapytania lub zwolnienie usługi Redshift wbudowaną.

> [!TIP]
> Aby uzyskać najlepszą wydajność podczas kopiowania dużych ilości danych z usługi Redshift, należy wziąć pod uwagę przy użyciu wbudowanych zwolnienie usługi Redshift za pośrednictwem Amazon S3. Zobacz [zwolnienie Użyj, aby skopiować dane z usługi Amazon Redshift](#use-unload-to-copy-data-from-amazon-redshift) sekcji, aby uzyskać szczegółowe informacje.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli kopiujesz magazynu danych, do danych lokalnych przy użyciu [własne środowisko IR](create-self-hosted-integration-runtime.md), przyznać dostęp do klastra usługi Amazon Redshift środowiska Integration Runtime (Użyj adres IP komputera). Zobacz [Autoryzowanie dostępu do klastra](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) instrukcje.
* Jeśli dane są kopiowane do magazynu danych na platformie Azure, zobacz [zakresów IP centrum danych Azure](https://www.microsoft.com/download/details.aspx?id=41653) dla adresu IP zasobów obliczeniowych i zakresy SQL używane przez usługi Azure data centers.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które są używane do definiowania jednostek usługi fabryka danych określonej do łącznika usługi Amazon Redshift.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Następujące właściwości są obsługiwane przez usługi Amazon Redshift, połączone usługi:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi być równa: **AmazonRedshift** | Yes |
| server |Adres IP lub hosta nazwę serwera usługi Amazon Redshift. |Yes |
| port |Numer portu TCP używanego przez serwer usługi Amazon Redshift do nasłuchiwania połączeń klientów. |Nie, wartość domyślna to 5439 |
| database |Nazwa bazy danych usługi Amazon Redshift. |Yes |
| username |Nazwa użytkownika, który ma dostęp do bazy danych. |Yes |
| password |Hasło dla konta użytkownika. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). |Yes |
| connectVia | [Środowiska Integration Runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. (Jeśli Twój magazyn danych znajduje się w sieci prywatnej), można użyć środowiska Azure Integration Runtime lub środowiskiem Integration Runtime. Jeśli nie zostanie określony, używa domyślnego środowiska Azure Integration Runtime. |Nie |

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

Aby uzyskać pełną listę sekcje i właściwości dostępne Definiowanie zestawów danych zobacz artykuł zestawów danych. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych usługi Amazon Redshift.

Aby skopiować dane z usługi Amazon Redshift, należy ustawić właściwość typu zestawu danych na **RelationalTable**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość typu elementu dataset musi być równa: **RelationalTable** | Yes |
| tableName | Nazwa tabeli w Amazon Redshift. | Nie (Jeśli określono parametr "zapytanie" w źródle działania) |

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

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródła usługi Amazon Redshift.

### <a name="amazon-redshift-as-source"></a>Usługi Amazon Redshift jako źródło

Aby skopiować dane z usługi Amazon Redshift, należy ustawić typ źródłowego w działaniu kopiowania, aby **obiektu AmazonRedshiftSource**. Następujące właściwości są obsługiwane w działaniu kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi być równa wartości właściwości type źródło działania kopiowania: **AmazonRedshiftSource** | Yes |
| query |Użyj zapytania niestandardowe można odczytać danych. Na przykład: Wybierz * z MyTable. |Nie (Jeśli określono parametr "tableName" w zestawie danych) |
| redshiftUnloadSettings | Grupa właściwości przy użyciu zwolnienie usługi Redshift Amazon. | Nie |
| s3LinkedServiceName | Odnosi się do Amazon S3 to-być używane jako przejściowy magazyn, określając nazwę połączonej usługi typu "AmazonS3". | Tak, jeśli za pomocą zwolnienia |
| bucketName | Wskazuje przedział S3 do przechowywania danych tymczasowych. Jeśli nie zostanie podana, usługa Data Factory wygeneruje ją automatycznie.  | Tak, jeśli za pomocą zwolnienia |

**Przykład: Źródła usługi Amazon Redshift w działania kopiowania przy użyciu zwolnienia**

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

Dowiedz się więcej na temat korzystania z zwolnienia do skopiowania danych z usługi Amazon Redshift wydajnie w następnej sekcji.

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>Umożliwia kopiowanie danych z usługi Amazon Redshift przez zwolnienie

[ZWOLNIJ](https://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) jest mechanizmem udostępniane przez usługi Amazon Redshift, który można zwolnić wyników zapytania do jednej lub więcej plików na Amazon Simple Storage Service (Amazon S3). Jest to sposób zalecane przez Amazon do kopiowania dużych zestawów danych z usługi Redshift.

**Przykład: kopiowanie danych z usługi Amazon Redshift do usługi Azure SQL Data Warehouse przy użyciu UNLOAD, kopiowania przejściowego i programu PolyBase**

W tym przykładzie przypadek użycia, Kopiuj działania zwalnia dane z usługi Amazon Redshift do Amazon S3 zgodnie z konfiguracją w elemencie "redshiftUnloadSettings", a następnie skopiuj dane z usługi Amazon S3 do obiektów Blob platformy Azure zgodnie z "stagingSettings" koniec przy użyciu technologii PolyBase do ładowania danych do danych programu SQL Magazyn. Tymczasowe format odbywa się przez działanie kopiowania prawidłowo.

![Redshift do usługi SQL DW skopiuj przepływ pracy](media/copy-data-from-amazon-redshift/redshift-to-sql-dw-copy-workflow.png)

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
            "dataIntegrationUnits": 32
        }
    }
]
```

## <a name="data-type-mapping-for-amazon-redshift"></a>Mapowanie typu danych dla usługi Amazon Redshift

Podczas kopiowania danych z usługi Amazon Redshift, następujące mapowania są używane z typów danych usługi Amazon Redshift do typów danych tymczasowych usługi Azure Data Factory. Zobacz [schemat i dane mapowanie typu](copy-activity-schema-and-type-mapping.md) Aby poznać sposób działania kopiowania mapowania typ schematu i danych źródła do ujścia.

| Typ danych Amazon Redshift | Typ danych tymczasowych fabryki danych |
|:--- |:--- |
| BIGINT |Int64 |
| BOOLEAN |String |
| CHAR |String |
| DATE |DateTime |
| DECIMAL |Decimal |
| DOUBLE PRECISION |Double |
| INTEGER |Int32 |
| REAL |Single |
| SMALLINT |Int16 |
| TEXT |String |
| TIMESTAMP |DateTime |
| VARCHAR |String |

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md##supported-data-stores-and-formats).
