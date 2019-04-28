---
title: Kopiowanie danych z bazy danych Sybase przy użyciu usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z programu Sybase do magazynów danych ujścia obsługiwane za pomocą działania kopiowania w potoku usługi Azure Data Factory.
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
ms.openlocfilehash: 55ff6d37f18f4ffa2f12e17bd33dd196b77f79af
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61473063"
---
# <a name="copy-data-from-sybase-using-azure-data-factory"></a>Kopiowanie danych z bazy danych Sybase przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1](v1/data-factory-onprem-sybase-connector.md)
> * [Bieżąca wersja](connector-sybase.md)

W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory, aby skopiować dane z bazy danych programu Sybase. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Możesz skopiować dane z bazy danych programu Sybase do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych, obsługiwane przez działanie kopiowania jako źródła/ujścia, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności ten łącznik programu Sybase obsługuje:

- SAP Sybase SQL dowolnego miejsca (ASA) **wersji 16 i nowsze wersje**; IQ i środowisko ASE nie są obsługiwane.
- Kopiowanie danych przy użyciu **podstawowe** lub **Windows** uwierzytelniania.

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć tego łącznika programu Sybase, należy:

- Skonfiguruj środowiskiem Integration Runtime. Zobacz [własne środowisko IR](create-self-hosted-integration-runtime.md) artykuł, aby uzyskać szczegółowe informacje.
- Zainstaluj [dostawcy danych programu Sybase iAnywhere.Data.SQLAnywhere](https://go.microsoft.com/fwlink/?linkid=324846) 16 lub nowszej na komputerze środowisko Integration Runtime.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje na temat właściwości, które są używane do definiowania jednostek usługi fabryka danych określonej do łącznika programu Sybase.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Następujące właściwości są obsługiwane dla bazy danych Sybase połączone usługi:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi być równa: **Sybase** | Yes |
| serwer | Nazwa serwera bazy danych Sybase. |Yes |
| baza danych | Nazwa bazy danych programu Sybase. |Yes |
| Element authenticationType | Typ uwierzytelniania używany do łączenia z bazą danych programu Sybase.<br/>Dozwolone wartości to: **Podstawowe**, i **Windows**. |Yes |
| nazwa użytkownika | Określ nazwę użytkownika do łączenia z bazą danych programu Sybase. |Yes |
| password | Określ hasło dla konta użytkownika, która została określona jako nazwy użytkownika. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). |Yes |
| connectVia | [Środowiska Integration Runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. Własne środowisko IR jest wymagany, zgodnie z opisem w [wymagania wstępne](#prerequisites). |Yes |

**Przykład:**

```json
{
    "name": "SybaseLinkedService",
    "properties": {
        "type": "Sybase",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "authenticationType": "Basic",
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

Aby uzyskać pełną listę sekcje i właściwości dostępne Definiowanie zestawów danych zobacz artykuł zestawów danych. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych programu Sybase.

Aby skopiować dane z bazy danych Sybase, należy ustawić właściwość typu zestawu danych na **RelationalTable**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość typu elementu dataset musi być równa: **RelationalTable** | Yes |
| tableName | Nazwa tabeli w bazie danych programu Sybase. | Nie (Jeśli określono parametr "zapytanie" w źródle działania) |

**Przykład**

```json
{
    "name": "SybaseDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Sybase linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło programu Sybase.

### <a name="sybase-as-source"></a>Bazy danych Sybase jako źródło

Aby skopiować dane z bazy danych Sybase, należy ustawić typ źródła w działaniu kopiowania, aby **RelationalSource**. Następujące właściwości są obsługiwane w działaniu kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi być równa wartości właściwości type źródło działania kopiowania: **RelationalSource** | Yes |
| query | Umożliwia odczytywanie danych niestandardowe zapytania SQL. Na przykład: `"SELECT * FROM MyTable"`. | Nie (Jeśli określono parametr "tableName" w zestawie danych) |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromSybase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Sybase input dataset name>",
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

## <a name="data-type-mapping-for-sybase"></a>Mapowanie dla bazy danych Sybase — typ danych

Podczas kopiowania danych z bazy danych Sybase, następujące mapowania są używane z typów danych programu Sybase do typów danych tymczasowych usługi Azure Data Factory. Zobacz [schemat i dane mapowanie typu](copy-activity-schema-and-type-mapping.md) Aby poznać sposób działania kopiowania mapowania typ schematu i danych źródła do ujścia.

Bazy danych Sybase obsługuje typy T-SQL. Dla tabeli mapowanie z typu SQL na typy danych tymczasowych usługi Azure Data Factory, zobacz [łącznika usługi Azure SQL Database — mapowanie typu danych](connector-azure-sql-database.md#data-type-mapping-for-azure-sql-database) sekcji.


## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
