---
title: Kopiowanie danych z MySQL za pomocą usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Więcej informacji na temat łącznika MySQL w usłudze Azure Data Factory, która umożliwia kopiowanie danych z bazy danych MySQL do magazynu danych, obsługiwany jako ujście.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: jingwang
ms.openlocfilehash: e05e2f2d04aeb572307f8114ca80f148b3d50e3d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61370720"
---
# <a name="copy-data-from-mysql-using-azure-data-factory"></a>Kopiowanie danych z MySQL za pomocą usługi Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1](v1/data-factory-onprem-mysql-connector.md)
> * [Bieżąca wersja](connector-mysql.md)

W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory, aby skopiować dane z bazy danych MySQL. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Możesz skopiować dane z bazy danych MySQL do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych, obsługiwane przez działanie kopiowania jako źródła/ujścia, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności ten łącznik MySQL obsługuje MySQL **wersji 5.6 i 5.7**.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli bazy danych MySQL nie jest dostępny publicznie, musisz skonfigurować środowiskiem Integration Runtime. Aby dowiedzieć się więcej na temat środowisk Self-Hosted integration Runtime, zobacz [własne środowisko IR](create-self-hosted-integration-runtime.md) artykułu. Infrastruktura Integration Runtime zapewnia wbudowane sterownika MySQL, począwszy od wersji 3.7, dlatego nie trzeba ręcznie zainstalować dowolnego sterownika.

Własne środowisko IR w wersji niższej niż 3.7, trzeba instalować [MySQL Connector/Net dla Microsoft Windows](https://dev.mysql.com/downloads/connector/net/) wersji między 6.6.5 i 6.10.7 na komputerze środowisko Integration Runtime. Tego sterownika 32-bitowy jest zgodna z 64-bitowy podczerwieni.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które są używane do definiowania jednostek usługi fabryka danych określonej do łącznika MySQL.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Następujące właściwości są obsługiwane w przypadku usługi MySQL połączone:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi być równa: **MySql** | Yes |
| connectionString | Podaj informacje wymagane do połączenia usługi Azure Database for MySQL — wystąpienia.<br/>Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory. Można również wprowadzić hasło w usłudze Azure Key Vault i ściągania `password` konfiguracji poza parametry połączenia. Zobacz poniższe przykłady i [Store poświadczeń w usłudze Azure Key Vault](store-credentials-in-key-vault.md) artykułu z bardziej szczegółowymi informacjami. | Yes |
| connectVia | [Środowiska Integration Runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. Używając środowiskiem Integration Runtime lub Azure Integration Runtime (Jeśli magazyn danych jest publicznie dostępny). Jeśli nie zostanie określony, używa domyślnego środowiska Azure Integration Runtime. |Nie |

Typowe parametry połączenia jest `Server=<server>;Port=<port>;Database=<database>;UID=<username>;PWD=<password>`. Więcej właściwości, które można ustawić dla tej sprawy:

| Właściwość | Opis | Opcje | Wymagane |
|:--- |:--- |:--- |:--- |
| SSLMode | Ta opcja określa, czy sterownik używa szyfrowania SSL i weryfikacji podczas nawiązywania połączenia z bazą danych MySQL. Na przykład `SSLMode=<0/1/2/3/4>`| WYŁĄCZONE (0) / PREFEROWANYCH (1) **(opcja domyślna)** / wymagane (2) / VERIFY_CA (3) / VERIFY_IDENTITY (4) | Nie |
| UseSystemTrustStore | Ta opcja określa, czy ma być używany certyfikat urzędu certyfikacji z magazynu zaufania systemu lub z określonego pliku PEM. Na przykład `UseSystemTrustStore=<0/1>;`| (1) włączone / wyłączone (0) **(opcja domyślna)** | Nie |

**Przykład:**

```json
{
    "name": "MySQLLinkedService",
    "properties": {
        "type": "MySql",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<server>;Port=<port>;Database=<database>;UID=<username>;PWD=<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Przykład: przechowywanie haseł w usłudze Azure Key Vault**

```json
{
    "name": "MySQLLinkedService",
    "properties": {
        "type": "MySql",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<server>;Port=<port>;Database=<database>;UID=<username>;"
            },
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Jeśli używano usługi MySQL połączone przy użyciu następujących ładunku, nadal jest obsługiwany jako — jest, chociaż zaleca się użyć nowego przyszłości.

**Poprzednie ładunek:**

```json
{
    "name": "MySQLLinkedService",
    "properties": {
        "type": "MySql",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
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

Aby uzyskać pełną listę sekcje i właściwości dostępne Definiowanie zestawów danych zobacz artykuł zestawów danych. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych MySQL.

Aby skopiować dane z MySQL, należy ustawić właściwość typu zestawu danych na **RelationalTable**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość typu elementu dataset musi być równa: **RelationalTable** | Yes |
| tableName | Nazwa tabeli w bazie danych MySQL. | Nie (Jeśli określono parametr "zapytanie" w źródle działania) |

**Przykład**

```json
{
    "name": "MySQLDataset",
    "properties":
    {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<MySQL linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło MySQL.

### <a name="mysql-as-source"></a>MySQL jako źródło

Aby skopiować dane z MySQL, należy ustawić typ źródła w działaniu kopiowania, aby **RelationalSource**. Następujące właściwości są obsługiwane w działaniu kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi być równa wartości właściwości type źródło działania kopiowania: **RelationalSource** | Yes |
| query | Umożliwia odczytywanie danych niestandardowe zapytania SQL. Na przykład: `"SELECT * FROM MyTable"`. | Nie (Jeśli określono parametr "tableName" w zestawie danych) |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromMySQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MySQL input dataset name>",
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
                "type": "RelationalSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-mysql"></a>Typ danych mapowania dla MySQL

Podczas kopiowania danych z MySQL, następujące mapowania są używane z typów danych MySQL na typy danych tymczasowych usługi Azure Data Factory. Zobacz [schemat i dane mapowanie typu](copy-activity-schema-and-type-mapping.md) Aby poznać sposób działania kopiowania mapowania typ schematu i danych źródła do ujścia.

| Typ danych MySQL | Typ danych tymczasowych fabryki danych |
|:--- |:--- |
| `bigint` |`Int64` |
| `bigint unsigned` |`Decimal` |
| `bit(1)` |`Boolean` |
| `bit(M), M>1`|`Byte[]`|
| `blob` |`Byte[]` |
| `bool` |`Int16` |
| `char` |`String` |
| `date` |`Datetime` |
| `datetime` |`Datetime` |
| `decimal` |`Decimal, String` |
| `double` |`Double` |
| `double precision` |`Double` |
| `enum` |`String` |
| `float` |`Single` |
| `int` |`Int32` |
| `int unsigned` |`Int64`|
| `integer` |`Int32` |
| `integer unsigned` |`Int64` |
| `long varbinary` |`Byte[]` |
| `long varchar` |`String` |
| `longblob` |`Byte[]` |
| `longtext` |`String` |
| `mediumblob` |`Byte[]` |
| `mediumint` |`Int32` |
| `mediumint unsigned` |`Int64` |
| `mediumtext` |`String` |
| `numeric` |`Decimal` |
| `real` |`Double` |
| `set` |`String` |
| `smallint` |`Int16` |
| `smallint unsigned` |`Int32` |
| `text` |`String` |
| `time` |`TimeSpan` |
| `timestamp` |`Datetime` |
| `tinyblob` |`Byte[]` |
| `tinyint` |`Int16` |
| `tinyint unsigned` |`Int16` |
| `tinytext` |`String` |
| `varchar` |`String` |
| `year` |`Int` |

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
