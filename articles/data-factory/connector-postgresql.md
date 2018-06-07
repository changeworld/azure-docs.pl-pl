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
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: 7b75bd5987ccf89c77509d0f2b4d8def5583e928
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34617437"
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

Aby użyć tego łącznika PostgreSQL, musisz:

- Konfigurowanie środowiska uruchomieniowego integracji Self-hosted. Zobacz [środowiska uruchomieniowego integracji Self-hosted](create-self-hosted-integration-runtime.md) artykułu, aby uzyskać szczegółowe informacje.
- Zainstaluj [Ngpsql dostawcy danych PostgreSQL](http://go.microsoft.com/fwlink/?linkid=282716) z wersją między 2.0.12 i 3.1.9 na komputerze środowiska uruchomieniowego integracji.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek fabryki danych określonej do łącznika PostgreSQL.

## <a name="linked-service-properties"></a>Połączona usługa właściwości

PostgreSQL połączone usługi, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi mieć ustawioną: **PostgreSql** | Yes |
| serwer | Nazwa serwera PostgreSQL. |Yes |
| baza danych | Nazwa bazy danych PostgreSQL. |Yes |
| Schemat | Nazwa schematu w bazie danych. Nazwa schematu jest rozróżniana wielkość liter. |Nie |
| nazwa użytkownika | Określ nazwę użytkownika do połączenia z bazą danych PostgreSQL. |Yes |
| hasło | Określ hasło dla konta użytkownika, określone nazwy użytkownika. Zaznacz to pole jako SecureString Zapisz w bezpiecznej lokalizacji w fabryce danych lub [odwołania klucz tajny przechowywane w usłudze Azure Key Vault](store-credentials-in-key-vault.md). |Yes |
| connectVia | [Integrację środowiska uruchomieniowego](concepts-integration-runtime.md) ma być używany do nawiązania połączenia z magazynem danych. Środowisko uruchomieniowe integracji Self-hosted jest wymagana, jak wspomniano w [wymagania wstępne](#prerequisites). |Yes |

**Przykład:**

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
| `abstime` | |`Datetime` | &nbsp;
| `bigint` | `int8` | `Int64` |
| `bigserial` | `serial8` | `Int64` |
| `bit [ (n) ]` | | `Byte[], String` | &nbsp;
| `bit varying [ (n) ]` | "varbit |Byte [], ciąg " |
| `boolean` | `bool` | `Boolean` |
| `box` | | `Byte[], String` | &nbsp;
| `bytea` | | `Byte[], String` |&nbsp;
| `character [ (n) ]` | `char [ (n) ]` | `String` |
| `character varying [ (n) ]` | `varchar [ (n) ]` | `String` |
| `cid` | | `String` |&nbsp;
| `cidr` | | `String` |&nbsp;
| `circle` | |`Byte[], String` |&nbsp;
| `date` | |`Datetime` |&nbsp;
| `daterange` | |`String` |&nbsp;
| `double precision` |`float8` |`Double` |
| `inet` | |`Byte[], String` |&nbsp;
| `intarry` | |`String` |&nbsp;
| `int4range` | |`String` |&nbsp;
| `int8range` | |`String` |&nbsp;
| `integer` | "int, int4 |Int32 " |
| `interval [ fields ] [ (p) ]` | | `Timespan` |&nbsp;
| `json` | | `String` |&nbsp;
| `jsonb` | | `Byte[]` |&nbsp;
| `line` | | `Byte[], String` |&nbsp;
| `lseg` | | `Byte[], String` |&nbsp;
| `macaddr` | | `Byte[], String` |&nbsp;
| `money` | | `Decimal` |&nbsp;
| `numeric [ (p, s) ]`|`decimal [ (p, s) ]` |`Decimal` |
| `numrange` | |`String` |&nbsp;
| `oid` | |`Int32` |&nbsp;
| `path` | |`Byte[], String` |&nbsp;
| `pg_lsn` | |`Int64` |&nbsp;
| `point` | |`Byte[], String` |&nbsp;
| `polygon` | |`Byte[], String` |&nbsp;
| `real` |`float4` |`Single` |
| `smallint` |`int2` |`Int16` |
| `smallserial` |`serial2` |`Int16` |
| `serial` |`serial4` |`Int32` |
| `text` | |`String` |&nbsp;


## <a name="next-steps"></a>Kolejne kroki
Lista magazynów danych obsługiwane jako źródła i wychwytywanie przez działanie kopiowania w fabryce danych Azure, zobacz [obsługiwane magazyny danych](copy-activity-overview.md##supported-data-stores-and-formats).
