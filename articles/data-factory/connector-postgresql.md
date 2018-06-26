---
title: Kopiowanie danych z PostgreSQL przy użyciu fabryki danych Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z PostgreSQL do zbiornika obsługiwane magazyny danych za pomocą działania kopiowania w potoku fabryki danych Azure.
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
ms.date: 06/23/2018
ms.author: jingwang
ms.openlocfilehash: 436725e54e197e021f088e0fd0cd75fc944983a3
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36751381"
---
# <a name="copy-data-from-postgresql-by-using-azure-data-factory"></a>Kopiowanie danych z PostgreSQL przy użyciu fabryki danych Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1 — ogólnie dostępna](v1/data-factory-onprem-postgresql-connector.md)
> * [Wersja 2 — wersja zapoznawcza](connector-postgresql.md)

W tym artykule omówiono sposób użycia działanie kopiowania w fabryce danych Azure, aby skopiować dane z bazy danych programu PostgreSQL. Opiera się na [skopiuj omówienie działania](copy-activity-overview.md) artykułu, który przedstawia ogólny przegląd działanie kopiowania.


> [!NOTE]
> Ten artykuł dotyczy wersji 2 usługi Data Factory, która jest obecnie dostępna w wersji zapoznawczej. Jeśli używasz wersji 1 usługi fabryka danych, która jest ogólnie dostępna (GA), zobacz [PostgreSQL łącznika w wersji 1](v1/data-factory-onprem-postgresql-connector.md).

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Możesz skopiować dane z bazy danych PostgreSQL żadnych obsługiwanych ujścia magazynu danych. Lista magazynów danych, które są obsługiwane jako źródła/wychwytywanie przez działanie kopiowania, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności ten łącznik PostgreSQL obsługuje PostgreSQL **wersji 7.4 lub nowszej**.

## <a name="prerequisites"></a>Wymagania wstępne

PostgreSQL bazy danych nie jest dostępny publicznie, należy skonfigurować środowisko uruchomieniowe Self-hosted integracji. Informacje na temat środowisk uruchomieniowych siebie integracji, zobacz [środowiska uruchomieniowego integracji Self-hosted](create-self-hosted-integration-runtime.md) artykułu. Środowiska uruchomieniowego integracji zawiera wbudowane sterownik PostgreSQL, począwszy od wersji 3.7, dlatego nie trzeba ręcznie zainstalowania sterownika.

Dla Self-hosted IR w wersji niższej niż 3.7, musisz zainstalować [Ngpsql dostawcy danych PostgreSQL](http://go.microsoft.com/fwlink/?linkid=282716) z wersją między 2.0.12 i 3.1.9 na komputerze środowiska uruchomieniowego integracji.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek fabryki danych określonej do łącznika PostgreSQL.

## <a name="linked-service-properties"></a>Połączona usługa właściwości

PostgreSQL połączone usługi, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi mieć ustawioną: **PostgreSql** | Yes |
| Parametry połączenia | Parametry połączenia ODBC do łączenia z bazą danych Azure dla PostgreSQL. Zaznacz to pole jako SecureString Zapisz w bezpiecznej lokalizacji w fabryce danych lub [odwołania klucz tajny przechowywane w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| connectVia | [Integrację środowiska uruchomieniowego](concepts-integration-runtime.md) ma być używany do nawiązania połączenia z magazynem danych. (Jeśli w magazynie danych jest dostępny publicznie) można użyć środowiska uruchomieniowego integracji Self-hosted lub środowiska uruchomieniowego integracji Azure. Jeśli nie zostanie określony, używa domyślnej środowiska uruchomieniowego integracji Azure. |Nie |

Ciąg połączenia typowe jest `Server=<server>;Database=<database>;Port=<port>;UID=<username>;Password=<Password>`. Więcej właściwości, które można ustawić dla tej sprawy:

| Właściwość | Opis | Opcje | Wymagane |
|:--- |:--- |:--- |:--- |:--- |
| EncryptionMethod (EM)| Metoda sterownik używa do szyfrowania danych przesyłanych między sterownika i serwer bazy danych. Na przykład `ValidateServerCertificate=<0/1/6>;`| 0 (brak szyfrowania) **(domyślna)** / 1 (SSL) / 6 (RequestSSL) | Nie |
| ValidateServerCertificate (wirtualnej karty Inteligentnej) | Określa, czy sterownik weryfikuje certyfikat, który jest wysyłany przez serwer bazy danych, jeśli jest włączone szyfrowanie SSL (metoda szyfrowania = 1). Na przykład `ValidateServerCertificate=<0/1>;`| 0 (wyłączone) **(domyślna)** / 1 (włączone) | Nie |

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

Jeśli następujące ładunek były używane PostgreSQL połączone usługi, nadal jest obsługiwany jako — jest, gdy zaleca się użyć nowego idąc dalej.

**Poprzednie ładunku:**

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

Aby uzyskać pełną listę właściwości dostępnych do definiowania zestawów danych i sekcje zobacz artykuł zestawów danych. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych PostgreSQL.

Aby skopiować dane z PostgreSQL, ustaw właściwość Typ zestawu danych do **RelationalTable**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi mieć ustawioną właściwość type zestawu danych: **RelationalTable** | Yes |
| tableName | Nazwa tabeli w bazie danych programu PostgreSQL. | Nie (Jeśli określono parametr "zapytania" w źródle działania) |

**Przykład**

```json
{
    "name": "PostgreSQLDataset",
    "properties":
    {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<PostgreSQL linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Pełną listę sekcje i właściwości dostępnych dla definiowania działań, zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwane przez źródło PostgreSQL.

### <a name="postgresql-as-source"></a>PostgreSQL jako źródło

Aby skopiować dane z PostgreSQL, należy ustawić typ źródła w przypadku działania kopiowania do **RelationalSource**. Następujące właściwości są obsługiwane w przypadku działania kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi mieć ustawioną właściwość type źródła działania kopiowania: **RelationalSource** | Yes |
| query | Użyj niestandardowych zapytania SQL można odczytać danych. Na przykład: `"query": "SELECT * FROM \"MySchema\".\"MyTable\""`. | Nie (Jeśli określono parametr "Nazwa_tabeli" w zestawie danych) |

> [!NOTE]
> Nazwy schematu i tabeli jest rozróżniana wielkość liter. Umieść je w `""` (podwójne cudzysłowy) w zapytaniu.

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
                "type": "RelationalSource",
                "query": "SELECT * FROM \"MySchema\".\"MyTable\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-postgresql"></a>Mapowanie dla PostgreSQL — typ danych

Podczas kopiowania danych z PostgreSQL, następujące mapowania są używane z typów danych PostgreSQL do typów danych tymczasowych fabryki danych Azure. Zobacz [schemat i dane typu mapowania](copy-activity-schema-and-type-mapping.md) Aby poznać sposób działania kopiowania mapowania typu źródłowego: schemat i dane sink.

| Typ danych PostgreSQL | Aliasy PostgresSQL | Typ danych tymczasowych fabryki danych |
|:--- |:--- |:--- |
| `abstime` |&nbsp; |`String` |
| `bigint` | `int8` | `Int64` |
| `bigserial` | `serial8` | `Int64` |
| `bit [1]` |&nbsp; | `Boolean` |
| `bit [(n)], n>1` |&nbsp; | `Byte[]` |
| `bit varying [(n)]` | `varbit` |`Byte[]` |
| `boolean` | `bool` | `Boolean` |
| `box` |&nbsp; | `String` |
| `bytea` |&nbsp; | `Byte[], String` |
| `character [(n)]` | `char [(n)]` | `String` |
| `character varying [(n)]` | `varchar [(n)]` | `String` |
| `cid` |&nbsp; | `Int32` |
| `cidr` |&nbsp; | `String` |
| `circle` |&nbsp; |` String` |
| `date` |&nbsp; |`Datetime` |
| `daterange` |&nbsp; |`String` |
| `double precision` |`float8` |`Double` |
| `inet` |&nbsp; |`String` |
| `intarray` |&nbsp; |`String` |
| `int4range` |&nbsp; |`String` |
| `int8range` |&nbsp; |`String` |
| `integer` | `int, int4` |`Int32` |
| `interval [fields] [(p)]` | | `String` |
| `json` |&nbsp; | `String` |
| `jsonb` |&nbsp; | `Byte[]` |
| `line` |&nbsp; | `Byte[], String` |
| `lseg` |&nbsp; | `String` |
| `macaddr` |&nbsp; | `String` |
| `money` |&nbsp; | `String` |
| `numeric [(p, s)]`|`decimal [(p, s)]` |`String` |
| `numrange` |&nbsp; |`String` |
| `oid` |&nbsp; |`Int32` |
| `path` |&nbsp; |`String` |
| `pg_lsn` |&nbsp; |`Int64` |
| `point` |&nbsp; |`String` |
| `polygon` |&nbsp; |`String` |
| `real` |`float4` |`Single` |
| `smallint` |`int2` |`Int16` |
| `smallserial` |`serial2` |`Int16` |
| `serial` |`serial4` |`Int32` |
| `text` |&nbsp; |`String` |
| `timewithtimezone` |&nbsp; |`String` |
| `timewithouttimezone` |&nbsp; |`String` |
| `timestampwithtimezone` |&nbsp; |`String` |
| `xid` |&nbsp; |`Int32` |

## <a name="next-steps"></a>Kolejne kroki
Lista magazynów danych obsługiwane jako źródła i wychwytywanie przez działanie kopiowania w fabryce danych Azure, zobacz [obsługiwane magazyny danych](copy-activity-overview.md##supported-data-stores-and-formats).
