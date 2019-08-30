---
title: Kopiowanie danych do i z Azure Database for MySQL przy użyciu Azure Data Factory | Microsoft Docs
description: Informacje o kopiowaniu danych do i z Azure Database for MySQL przy użyciu działania kopiowania w potoku Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/25/2019
ms.author: jingwang
ms.openlocfilehash: b6d96ef2d2cdd79bec35f2581876823990e4a971
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70172616"
---
# <a name="copy-data-to-and-from-azure-database-for-mysql-using-azure-data-factory"></a>Kopiowanie danych do i z Azure Database for MySQL przy użyciu Azure Data Factory

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych z Azure Database for MySQL. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólne omówienie działania kopiowania.

Ten łącznik jest wyspecjalizowany dla [usługi Azure Database for MySQL](../mysql/overview.md). Aby skopiować dane z ogólnej bazy danych MySQL znajdującej się lokalnie lub w chmurze, użyj [łącznika MySQL](connector-mysql.md).

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Dane z Azure Database for MySQL można kopiować do dowolnego obsługiwanego magazynu danych ujścia. Można też skopiować dane z dowolnego obsługiwanego magazynu danych źródłowych do Azure Database for MySQL. Aby uzyskać listę magazynów danych, obsługiwane przez działanie kopiowania jako źródła/ujścia, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

Usługa Azure Data Factory udostępnia wbudowanego sterownika, aby umożliwić łączność, dlatego nie trzeba ręcznie zainstalować dowolnego sterownika, za pomocą tego łącznika.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania Data Factory jednostek specyficznych dla łącznika Azure Database for MySQL.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Następujące właściwości są obsługiwane dla Azure Database for MySQL połączonej usługi:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość Type musi mieć ustawioną wartość: **AzureMySql** | Tak |
| connectionString | Określ informacje, które są konieczne do nawiązania połączenia z wystąpieniem Azure Database for MySQL. <br/>Oznacz to pole jako element SecureString, aby bezpiecznie przechowywać go w Data Factory. Możesz również wprowadzić hasło w Azure Key Vault i ściągnąć `password` konfigurację z parametrów połączenia. Zapoznaj się z poniższymi przykładami i [Zapisz poświadczenia w Azure Key Vault](store-credentials-in-key-vault.md) artykule, aby uzyskać więcej szczegółów. | Tak |
| connectVia | [Środowiska Integration Runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. (Jeśli Twój magazyn danych znajduje się w sieci prywatnej), można użyć środowiska Azure Integration Runtime lub środowiskiem Integration Runtime. Jeśli nie zostanie określony, używa domyślnego środowiska Azure Integration Runtime. |Nie |

Typowe parametry połączenia jest `Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;PWD=<password>`. Więcej właściwości, które można ustawić dla tej sprawy:

| Właściwość | Opis | Opcje | Wymagane |
|:--- |:--- |:--- |:--- |
| SSLMode | Ta opcja określa, czy sterownik używa szyfrowania SSL i weryfikacji podczas łączenia z bazą danych MySQL. Na przykład `SSLMode=<0/1/2/3/4>`| WYŁĄCZONE (0)/PREFEROWANe (1) **(wartość domyślna)** /wymagane (2)/VERIFY_CA (3)/VERIFY_IDENTITY (4) | Nie |
| UseSystemTrustStore | Ta opcja umożliwia określenie, czy certyfikat urzędu certyfikacji ma być używany z magazynu zaufania systemu, czy z określonego pliku PEM. Na przykład `UseSystemTrustStore=<0/1>;`| Włączone (1)/wyłączone (0) **(wartość domyślna)** | Nie |

**Przykład:**

```json
{
    "name": "AzureDatabaseForMySQLLinkedService",
    "properties": {
        "type": "AzureMySql",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;PWD=<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Przykład: Przechowuj hasło w Azure Key Vault**

```json
{
    "name": "AzureDatabaseForMySQLLinkedService",
    "properties": {
        "type": "AzureMySql",
        "typeProperties": {
            "connectionString": {
                 "type": "SecureString",
                 "value": "Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;"
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

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcje i właściwości dostępne Definiowanie zestawów danych, zobacz [zestawów danych](concepts-datasets-linked-services.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych Azure Database for MySQL.

Aby skopiować dane z Azure Database for MySQL, ustaw właściwość Type zestawu danych na **AzureMySqlTable**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość Type zestawu danych musi być ustawiona na wartość: **AzureMySqlTable** | Tak |
| tableName | Nazwa tabeli w bazie danych MySQL. | Nie (Jeśli określono parametr "query" w źródle działania) |

**Przykład**

```json
{
    "name": "AzureMySQLDataset",
    "properties": {
        "type": "AzureMySqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure MySQL linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez Azure Database for MySQL źródła i ujścia.

### <a name="azure-database-for-mysql-as-source"></a>Azure Database for MySQL jako źródło

Aby skopiować dane z Azure Database for MySQL, w sekcji **Źródło** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość Type źródła działania Copy musi mieć ustawioną wartość: **AzureMySqlSource** | Tak |
| query | Umożliwia odczytywanie danych niestandardowe zapytania SQL. Na przykład: `"SELECT * FROM MyTable"`. | Nie (Jeśli określono parametr "tableName" w zestawie danych) |
| queryCommandTimeout | Czas oczekiwania przed upływem limitu czasu żądania zapytania. Wartość domyślna to 120 minut (02:00:00) | Nie |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromAzureDatabaseForMySQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure MySQL input dataset name>",
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
                "type": "AzureMySqlSource",
                "query": "<custom query e.g. SELECT * FROM MyTable>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-database-for-mysql-as-sink"></a>Azure Database for MySQL jako ujścia

Aby skopiować dane do Azure Database for MySQL, w sekcji **ujścia** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość Type ujścia działania Copy musi mieć ustawioną wartość: **AzureMySqlSink** | Tak |
| preCopyScript | Określ zapytanie SQL dla działania kopiowania, które ma zostać wykonane przed zapisaniem danych w Azure Database for MySQL w każdym przebiegu. Ta właściwość służy do czyszczenia wstępnie załadowanych danych. | Nie |
| writeBatchSize | Wstawia dane do tabeli Azure Database for MySQL, gdy rozmiar buforu osiągnie writeBatchSize.<br>Dozwolona wartość to liczba całkowita reprezentująca liczbę wierszy. | Nie (domyślnie 10 000) |
| writeBatchTimeout | Czas oczekiwania na zakończenie operacji wstawiania partii przed upływem limitu czasu.<br> 
Dozwolone wartości to TimeSpan. Przykładem jest 00:30:00 (30 minut). | Nie (domyślnie 00:00:30) |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyToAzureDatabaseForMySQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure MySQL output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureMySqlSink",
                "preCopyScript": "<custom SQL script>",
                "writeBatchSize": 100000
            }
        }
    }
]
```

## <a name="data-type-mapping-for-azure-database-for-mysql"></a>Mapowanie typu danych dla Azure Database for MySQL

Podczas kopiowania danych z Azure Database for MySQL następujące mapowania są używane z typów danych MySQL do Azure Data Factory pośrednich typów danych. Zobacz [schemat i dane mapowanie typu](copy-activity-schema-and-type-mapping.md) Aby poznać sposób działania kopiowania mapowania typ schematu i danych źródła do ujścia.

| Typ danych Azure Database for MySQL | Typ danych tymczasowych fabryki danych |
|:--- |:--- |
| `bigint` |`Int64` |
| `bigint unsigned` |`Decimal` |
| `bit` |`Boolean` |
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
| `year` |`Int32` |

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
