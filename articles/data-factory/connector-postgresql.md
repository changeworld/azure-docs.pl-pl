---
title: Kopiowanie danych z PostgreSQL za pomocą Azure Data Factory
description: Informacje o kopiowaniu danych z programu PostgreSQL do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w potoku Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: 1f270d2e303a8f7b5589d28f101c797c38b2b626
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680459"
---
# <a name="copy-data-from-postgresql-by-using-azure-data-factory"></a>Kopiowanie danych z PostgreSQL za pomocą Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-onprem-postgresql-connector.md)
> * [Bieżąca wersja](connector-postgresql.md)

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych z bazy danych PostgreSQL. Jest ona oparta na [przeglądzie działania kopiowania](copy-activity-overview.md) , która przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik PostgreSQL jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)

Dane z bazy danych PostgreSQL można kopiować do dowolnego obsługiwanego magazynu danych ujścia. Listę magazynów danych obsługiwanych jako źródła/ujścia przez działanie kopiowania można znaleźć w tabeli [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) .

Ten łącznik PostgreSQL obsługuje PostgreSQL w **wersji 7,4 lub nowszej**.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

W przypadku samodzielnej wersji środowiska IR starszej niż 3,7 należy zainstalować [dostawcę danych Ngpsql dla PostgreSQL](https://go.microsoft.com/fwlink/?linkid=282716) z wersją między 2.0.12 i 3.1.9 na maszynie Integration Runtime.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek Data Factory specyficznych dla łącznika PostgreSQL.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Dla połączonej usługi PostgreSQL są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość Type musi mieć wartość: **PostgreSQL** | Tak |
| Przekształcon | Parametry połączenia ODBC do połączenia z Azure Database for PostgreSQL. <br/>Oznacz to pole jako element SecureString, aby bezpiecznie przechowywać go w Data Factory. Możesz również wprowadzić hasło w Azure Key Vault i ściągnąć konfigurację `password` z parametrów połączenia. Zapoznaj się z poniższymi przykładami i [Zapisz poświadczenia w Azure Key Vault](store-credentials-in-key-vault.md) artykule, aby uzyskać więcej szczegółów. | Tak |
| Właściwością connectvia | [Integration Runtime](concepts-integration-runtime.md) używany do nawiązywania połączenia z magazynem danych. Dowiedz się więcej z sekcji [wymagania wstępne](#prerequisites) . Jeśli nie zostanie określony, zostanie użyta domyślna Azure Integration Runtime. |Nie |

Typowe parametry połączenia to `Server=<server>;Database=<database>;Port=<port>;UID=<username>;Password=<Password>`. Więcej właściwości, które można ustawić dla danego przypadku:

| Właściwość | Opis | Opcje | Wymagany |
|:--- |:--- |:--- |:--- |
| EncryptionMethod (EM)| Metoda wykorzystywana przez sterownik do szyfrowania danych przesyłanych między sterownikiem a serwerem bazy danych. Np., `EncryptionMethod=<0/1/6>;`| 0 (bez szyfrowania) **(wartość domyślna)** /1 (SSL)/6 (RequestSSL) | Nie |
| ValidateServerCertificate (VSC) | Określa, czy sterownik sprawdza poprawność certyfikatu wysyłanego przez serwer bazy danych, gdy włączone jest szyfrowanie SSL (metoda szyfrowania = 1). Np., `ValidateServerCertificate=<0/1>;`| 0 (wyłączone) **(wartość domyślna)** /1 (włączone) | Nie |

**Przykład:**

```json
{
    "name": "PostgreSqlLinkedService",
    "properties": {
        "type": "PostgreSql",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<server>;Database=<database>;Port=<port>;UID=<username>;Password=<Password>"
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
    "name": "PostgreSqlLinkedService",
    "properties": {
        "type": "PostgreSql",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<server>;Database=<database>;Port=<port>;UID=<username>;"
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

Jeśli używasz połączonej usługi PostgreSQL z następującym ładunkiem, nadal jest ona obsługiwana w stanie takim, w jakim będziesz mieć możliwość użycia nowego.

**Poprzedni ładunek:**

```json
{
    "name": "PostgreSqlLinkedService",
    "properties": {
        "type": "PostgreSql",
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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [zestawy danych](concepts-datasets-linked-services.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych PostgreSQL.

Aby skopiować dane z PostgreSQL, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość Type zestawu danych musi być ustawiona na wartość: **PostgreSqlTable** | Tak |
| schematy | Nazwa schematu. |Nie (Jeśli określono parametr "query" w źródle działania)  |
| tabele | Nazwa tabeli. |Nie (Jeśli określono parametr "query" w źródle działania)  |
| tableName | Nazwa tabeli ze schematem. Ta właściwość jest obsługiwana w celu zapewnienia zgodności z poprzednimi wersjami. Użyj `schema` i `table` dla nowego obciążenia. | Nie (Jeśli określono parametr "query" w źródle działania) |

**Przykład**

```json
{
    "name": "PostgreSQLDataset",
    "properties":
    {
        "type": "PostgreSqlTable",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<PostgreSQL linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Jeśli używasz zestawu danych `RelationalTable` z określonym typem, nadal jest on obsługiwany w przypadku, gdy zamierzasz użyć nowego.

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz artykuł [potoki](concepts-pipelines-activities.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez źródło PostgreSQL.

### <a name="postgresql-as-source"></a>PostgreSQL jako źródło

Aby skopiować dane z PostgreSQL, w sekcji **Źródło** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość Type źródła działania Copy musi być ustawiona na wartość: **PostgreSqlSource** | Tak |
| query | Użyj niestandardowego zapytania SQL, aby odczytać dane. Na przykład: `"query": "SELECT * FROM \"MySchema\".\"MyTable\""`. | Nie (Jeśli określono "TableName" w zestawie danych) |

> [!NOTE]
> W nazwach schematu i tabeli jest rozróżniana wielkość liter. Ujmij je w `""` (podwójne cudzysłowy) w zapytaniu.

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromPostgreSQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<PostgreSQL input dataset name>",
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
                "type": "PostgreSqlSource",
                "query": "SELECT * FROM \"MySchema\".\"MyTable\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Jeśli używasz źródła z wpisaną `RelationalSource`, jest ono nadal obsługiwane w stanie takim, w jakim będziesz mieć możliwość korzystania z nowej usługi.

## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).


## <a name="next-steps"></a>Następne kroki
Listę magazynów danych obsługiwanych jako źródła i ujścia przez działanie kopiowania w Azure Data Factory można znaleźć w temacie [obsługiwane magazyny danych](copy-activity-overview.md##supported-data-stores-and-formats).
