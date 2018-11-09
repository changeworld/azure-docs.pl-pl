---
title: Kopiowanie danych z programu Teradata przy użyciu usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o Teradata łącznika usługi Data Factory, która umożliwia kopiowanie danych z bazy danych Teradata do magazynów danych obsługiwanych przez usługę Data Factory jako ujścia.
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
ms.openlocfilehash: 37e7281af87a8cfc57aae95411eb2d4cce9eef65
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51228066"
---
# <a name="copy-data-from-teradata-using-azure-data-factory"></a>Kopiowanie danych z programu Teradata przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1](v1/data-factory-onprem-teradata-connector.md)
> * [Bieżąca wersja](connector-teradata.md)

W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory, aby skopiować dane z bazy danych programu Teradata. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Możesz skopiować dane z bazy danych Teradata, do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych, obsługiwane przez działanie kopiowania jako źródła/ujścia, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności ten łącznik Teradata obsługuje:

- Teradata **wersja 12 i nowsze wersje**.
- Kopiowanie danych przy użyciu **podstawowe** lub **Windows** uwierzytelniania.

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć tego łącznika Teradata, należy:

- Skonfiguruj środowiskiem Integration Runtime. Zobacz [własne środowisko IR](create-self-hosted-integration-runtime.md) artykuł, aby uzyskać szczegółowe informacje.
- Zainstaluj [.NET Data Provider for Teradata](https://go.microsoft.com/fwlink/?LinkId=278886) wersji 14 lub nowszej na komputerze środowisko Integration Runtime.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje na temat właściwości, które są używane do definiowania jednostek usługi fabryka danych określonej do łącznika programu Teradata.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Następujące właściwości są obsługiwane dla programu Teradata połączone usługi:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi być równa: **Teradata** | Yes |
| serwer | Nazwa serwera programu Teradata. | Yes |
| Element authenticationType | Typ uwierzytelniania używany do łączenia z bazą danych programu Teradata.<br/>Dozwolone wartości to: **podstawowe**, i **Windows**. | Yes |
| nazwa użytkownika | Określ nazwę użytkownika do łączenia z bazą danych programu Teradata. | Yes |
| hasło | Określ hasło dla konta użytkownika, która została określona jako nazwy użytkownika. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| connectVia | [Środowiska Integration Runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. Własne środowisko IR jest wymagany, zgodnie z opisem w [wymagania wstępne](#prerequisites). |Yes |

**Przykład:**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "server": "<server>",
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

Aby uzyskać pełną listę sekcje i właściwości dostępne Definiowanie zestawów danych zobacz artykuł zestawów danych. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych Teradata.

Aby skopiować dane z programu Teradata, ustaw właściwość typu zestawu danych na **RelationalTable**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość typu elementu dataset musi być równa: **RelationalTable** | Yes |
| tableName | Nazwa tabeli w bazie danych programu Teradata. | Nie (Jeśli określono parametr "zapytanie" w źródle działania) |

**Przykład:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło Teradata.

### <a name="teradata-as-source"></a>Teradata jako źródło

Aby skopiować dane z programu Teradata, należy ustawić typ źródła w działanie kopiowania w celu **RelationalSource**. Następujące właściwości są obsługiwane w działaniu kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi być równa wartości właściwości type źródło działania kopiowania: **RelationalSource** | Yes |
| query | Umożliwia odczytywanie danych niestandardowe zapytania SQL. Na przykład: `"SELECT * FROM MyTable"`. | Nie (Jeśli określono parametr "tableName" w zestawie danych) |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromTeradata",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Teradata input dataset name>",
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

## <a name="data-type-mapping-for-teradata"></a>Typ danych mapowania dla programu Teradata

Podczas kopiowania danych z programu Teradata, następujące mapowania są używane z typów danych Teradata na typy danych tymczasowych usługi Azure Data Factory. Zobacz [schemat i dane mapowanie typu](copy-activity-schema-and-type-mapping.md) Aby poznać sposób działania kopiowania mapowania typ schematu i danych źródła do ujścia.

| Typ danych programu Teradata | Typ danych tymczasowych fabryki danych |
|:--- |:--- |
| BigInt |Int64 |
| Obiekt blob |Byte[] |
| Bajt |Byte[] |
| ByteInt |Int16 |
| Char |Ciąg |
| CLOB |Ciąg |
| Date |DateTime |
| Dziesiętna |Dziesiętna |
| Podwójne |Podwójne |
| Grafika |Ciąg |
| Liczba całkowita |Int32 |
| Interwał dnia |Przedział czasu |
| Interwału dnia do godziny |Przedział czasu |
| Interwału dnia do minuty |Przedział czasu |
| Interwału dnia do sekundy |Przedział czasu |
| Interwał, godzinę |Przedział czasu |
| Godzina przedział na minutę |Przedział czasu |
| Godzina przedział na sekundę |Przedział czasu |
| Interwał minutę |Przedział czasu |
| Interwału minuty do sekundy |Przedział czasu |
| Interwał miesiąca |Ciąg |
| Interwał drugiego |Przedział czasu |
| Interwał roku |Ciąg |
| Interwału roku do miesiąca |Ciąg |
| Liczba |Podwójne |
| Period(Date) |Ciąg |
| Period(Time) |Ciąg |
| Okres (godzina ze strefą czasową) |Ciąg |
| Period(TimeStamp) |Ciąg |
| Okres (sygnatura czasowa ze strefą czasową) |Ciąg |
| SmallInt |Int16 |
| Time |Przedział czasu |
| Godzina ze strefą czasową |Ciąg |
| Znacznik czasu |DateTime |
| Sygnatura czasowa ze strefą czasową |DateTimeOffset |
| VarByte |Byte[] |
| VarChar |Ciąg |
| VarGraphic |Ciąg |
| Xml |Ciąg |


## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
