---
title: Kopiowanie danych z usługi Amazon RedShift
description: Informacje o sposobie kopiowania danych z usługi Amazon RedShift do obsługiwanych magazynów danych ujścia przy użyciu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/04/2018
ms.author: jingwang
ms.openlocfilehash: 8cb604996f6cde5c55098fd3fdc51d0d061dbcd1
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2019
ms.locfileid: "74891222"
---
# <a name="copy-data-from-amazon-redshift-using-azure-data-factory"></a>Kopiowanie danych z usługi Amazon RedShift przy użyciu Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-amazon-redshift-connector.md)
> * [Bieżąca wersja](connector-amazon-redshift.md)


W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych z usługi Amazon RedShift. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Ten łącznik Amazon RedShift jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)

Dane z usługi Amazon RedShift można skopiować do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych, obsługiwane przez działanie kopiowania jako źródła/ujścia, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W każdym przypadku ten łącznik usługi Amazon RedShift obsługuje pobieranie danych z usługi RedShift przy użyciu zapytania lub wbudowanej obsługi ZWALNIAnia RedShift.

> [!TIP]
> Aby osiągnąć najlepszą wydajność podczas kopiowania dużych ilości danych z RedShift, należy rozważyć użycie wbudowanej usługi RedShift UNLOAD za pośrednictwem usługi Amazon S3. Aby uzyskać szczegółowe informacje, zobacz sekcję korzystanie z usługi [Unload w celu skopiowania danych z usługi Amazon RedShift](#use-unload-to-copy-data-from-amazon-redshift) .

## <a name="prerequisites"></a>Wymagania wstępne

* W przypadku kopiowania danych do lokalnego magazynu danych przy użyciu [Samoobsługowego Integration Runtime](create-self-hosted-integration-runtime.md)Udziel Integration Runtime (Użyj adresu IP maszyny) dostępu do klastra Amazon RedShift. Aby uzyskać instrukcje [, zobacz Autoryzuj dostęp do klastra](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) .
* Jeśli kopiujesz dane do magazynu danych platformy Azure, zobacz [zakres adresów IP centrów danych platformy Azure](https://www.microsoft.com/download/details.aspx?id=41653) dla adresu IP obliczeń i zakresów SQL używanych przez centra danych platformy Azure.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek Data Factory specyficznych dla łącznika Amazon RedShift.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Dla połączonej usługi Amazon RedShift są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość Type musi mieć wartość: **AmazonRedshift** | Tak |
| serwer |Adres IP lub nazwa hosta serwera Amazon RedShift. |Tak |
| port |Numer portu TCP używanego przez serwer Amazon RedShift do nasłuchiwania połączeń klientów. |Nie, wartość domyślna to 5439 |
| baza danych |Nazwa bazy danych Amazon RedShift. |Tak |
| nazwa użytkownika |Nazwa użytkownika, który ma dostęp do bazy danych programu. |Tak |
| hasło |Hasło konta użytkownika. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). |Tak |
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

Aby uzyskać pełną listę sekcje i właściwości dostępne Definiowanie zestawów danych, zobacz [zestawów danych](concepts-datasets-linked-services.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych Amazon RedShift.

Aby skopiować dane z usługi Amazon RedShift, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość Type zestawu danych musi być ustawiona na wartość: **AmazonRedshiftTable** | Tak |
| schema | Nazwa schematu. |Nie (Jeśli określono parametr "query" w źródle działania)  |
| table | Nazwa tabeli. |Nie (Jeśli określono parametr "query" w źródle działania)  |
| tableName | Nazwa tabeli ze schematem. Ta właściwość jest obsługiwana w celu zapewnienia zgodności z poprzednimi wersjami. Użyj `schema` i `table` dla nowego obciążenia. | Nie (Jeśli określono parametr "query" w źródle działania) |

**Przykład**

```json
{
    "name": "AmazonRedshiftDataset",
    "properties":
    {
        "type": "AmazonRedshiftTable",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Amazon Redshift linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Jeśli używasz zestawu danych `RelationalTable` z określonym typem, nadal jest on obsługiwany w przypadku, gdy zamierzasz korzystać z nowego.

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło Amazon RedShift.

### <a name="amazon-redshift-as-source"></a>Amazon RedShift jako źródło

Aby skopiować dane z Amazon RedShift, ustaw typ źródła w działaniu Copy na **AmazonRedshiftSource**. Następujące właściwości są obsługiwane w działaniu kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość Type źródła działania Copy musi być ustawiona na wartość: **AmazonRedshiftSource** | Tak |
| query |Użyj zapytania niestandardowego do odczytywania danych. Na przykład: select * from MyTable. |Nie (Jeśli określono parametr "tableName" w zestawie danych) |
| redshiftUnloadSettings | Grupa właściwości podczas korzystania z narzędzia Amazon RedShift UNLOAD. | Nie |
| s3LinkedServiceName | Odwołuje się do usługi Amazon S3, która ma być używana jako magazyn tymczasowy, określając nazwę połączonej usług typu "AmazonS3". | Tak, jeśli używasz opcji UNLOAD |
| bucketName | Wskaż zasobnik S3, aby przechowywać dane pośrednie. Jeśli nie zostanie podany, Usługa Data Factory automatycznie wygeneruje ją.  | Tak, jeśli używasz opcji UNLOAD |

**Przykład: Źródło Amazon RedShift w działaniu kopiowania przy użyciu UNLOAD**

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

Dowiedz się więcej na temat sposobu korzystania z usługi UNLOAD w celu wydajnego kopiowania danych z usługi Amazon RedShift z kolejnej sekcji.

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>Korzystanie z usługi UNLOAD do kopiowania danych z usługi Amazon RedShift

[Unload](https://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) to mechanizm zapewniany przez usługę Amazon RedShift, który może zwolnić wyniki zapytania do jednego lub kilku plików w usłudze Amazon Simple Storage Service (Amazon S3). Jest to sposób zalecane przez Amazon do kopiowania dużych zestawów danych z RedShift.

**Przykład: Kopiuj dane z Amazon RedShift do Azure SQL Data Warehouse przy użyciu UNLOAD, kopii etapowej i bazy danych**

W przypadku tego przykładowego przypadku użycia działanie Copy powoduje odładowanie danych z Amazon RedShift do Amazon S3 zgodnie z konfiguracją w "redshiftUnloadSettings", a następnie skopiowanie danych z usługi Amazon S3 do obiektu blob platformy Azure zgodnie z definicją w "stagingSettings", a następnie użycie podstawy do ładowania danych do danych SQL Hurtowni. Cały format tymczasowy jest obsługiwany przez działanie kopiowania prawidłowo.

![Przepływ pracy kopiowania RedShift do magazynu danych SQL](media/copy-data-from-amazon-redshift/redshift-to-sql-dw-copy-workflow.png)

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

## <a name="data-type-mapping-for-amazon-redshift"></a>Mapowanie typu danych dla Amazon RedShift

Podczas kopiowania danych z usługi Amazon RedShift następujące mapowania są używane z typów danych Amazon RedShift do Azure Data Factory danych pośrednich. Zobacz [schemat i dane mapowanie typu](copy-activity-schema-and-type-mapping.md) Aby poznać sposób działania kopiowania mapowania typ schematu i danych źródła do ujścia.

| Amazon RedShift — typ danych | Typ danych tymczasowych fabryki danych |
|:--- |:--- |
| BIGINT |Int64 |
| BOOLEAN |Ciąg |
| CHAR |Ciąg |
| DATE |Data i godzina |
| DECIMAL |Decimal |
| DOUBLE PRECISION |Double |
| INTEGER |Int32 |
| REAL |Pojedyncze |
| SMALLINT |Int16 |
| TEXT |Ciąg |
| TIMESTAMP |Data i godzina |
| VARCHAR |Ciąg |

## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).

## <a name="next-steps"></a>Następne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md##supported-data-stores-and-formats).
