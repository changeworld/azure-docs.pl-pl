---
title: Kopiowanie danych z programu Teradata przy użyciu fabryki danych Azure | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o Teradata łącznika usługi fabryka danych, która umożliwia kopiowanie danych z bazy danych programu Teradata do magazynów danych obsługiwane przez fabrykę danych jako sink.
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
ms.openlocfilehash: 4360ff12a435afc4347fa97bba4506ccd81618aa
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34618984"
---
# <a name="copy-data-from-teradata-using-azure-data-factory"></a>Kopiowanie danych z programu Teradata przy użyciu fabryki danych Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1 — ogólnie dostępna](v1/data-factory-onprem-teradata-connector.md)
> * [Wersja 2 — wersja zapoznawcza](connector-teradata.md)

W tym artykule omówiono sposób użycia działanie kopiowania w fabryce danych Azure, aby skopiować dane z bazy danych programu Teradata. Opiera się na [skopiuj omówienie działania](copy-activity-overview.md) artykułu, który przedstawia ogólny przegląd działanie kopiowania.

> [!NOTE]
> Ten artykuł dotyczy wersji 2 usługi Data Factory, która jest obecnie dostępna w wersji zapoznawczej. Jeśli używasz wersji 1 usługi fabryka danych, która jest ogólnie dostępna (GA), zobacz [Teradata łącznika w wersji 1](v1/data-factory-onprem-teradata-connector.md).

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Możesz skopiować dane z bazy danych programu Teradata żadnych obsługiwanych ujścia magazynu danych. Lista magazynów danych, które są obsługiwane jako źródła/wychwytywanie przez działanie kopiowania, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności ten łącznik Teradata obsługuje:

- Teradata **wersji 12 i powyżej**.
- Kopiowanie danych przy użyciu **podstawowe** lub **Windows** uwierzytelniania.

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć tego łącznika programu Teradata, musisz:

- Konfigurowanie środowiska uruchomieniowego integracji Self-hosted. Zobacz [środowiska uruchomieniowego integracji Self-hosted](create-self-hosted-integration-runtime.md) artykułu, aby uzyskać szczegółowe informacje.
- Zainstaluj [dostawcy danych .NET dla programu Teradata](http://go.microsoft.com/fwlink/?LinkId=278886) wersji 14 lub nowszej na komputerze środowiska uruchomieniowego integracji.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek fabryki danych określonej do łącznika programu Teradata.

## <a name="linked-service-properties"></a>Połączona usługa właściwości

Obsługiwane są następujące właściwości dla programu Teradata połączone usługi:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi mieć ustawioną: **Teradata** | Yes |
| serwer | Nazwa serwera programu Teradata. | Yes |
| Typ authenticationType | Typ uwierzytelniania używany do łączenia z bazą danych programu Teradata.<br/>Dozwolone wartości to: **podstawowe**, i **Windows**. | Yes |
| nazwa użytkownika | Określ nazwę użytkownika do połączenia z bazą danych programu Teradata. | Yes |
| hasło | Określ hasło dla konta użytkownika, określone nazwy użytkownika. Zaznacz to pole jako SecureString Zapisz w bezpiecznej lokalizacji w fabryce danych lub [odwołania klucz tajny przechowywane w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| connectVia | [Integrację środowiska uruchomieniowego](concepts-integration-runtime.md) ma być używany do nawiązania połączenia z magazynem danych. Środowisko uruchomieniowe integracji Self-hosted jest wymagana, jak wspomniano w [wymagania wstępne](#prerequisites). |Yes |

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

Aby uzyskać pełną listę właściwości dostępnych do definiowania zestawów danych i sekcje zobacz artykuł zestawów danych. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych programu Teradata.

Aby skopiować dane z programu Teradata, ustaw właściwość Typ zestawu danych do **RelationalTable**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi mieć ustawioną właściwość type zestawu danych: **RelationalTable** | Yes |
| tableName | Nazwa tabeli w bazie danych programu Teradata. | Nie (Jeśli określono parametr "zapytania" w źródle działania) |

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

Pełną listę sekcje i właściwości dostępnych dla definiowania działań, zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwane przez źródło programu Teradata.

### <a name="teradata-as-source"></a>Teradata jako źródło

Aby skopiować dane z programu Teradata, należy ustawić typ źródła w przypadku działania kopiowania do **RelationalSource**. Następujące właściwości są obsługiwane w przypadku działania kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi mieć ustawioną właściwość type źródła działania kopiowania: **RelationalSource** | Yes |
| query | Użyj niestandardowych zapytania SQL można odczytać danych. Na przykład: `"SELECT * FROM MyTable"`. | Nie (Jeśli określono parametr "Nazwa_tabeli" w zestawie danych) |

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

Podczas kopiowania danych z programu Teradata, następujące mapowania są używane z typów danych programu Teradata do typów danych tymczasowych fabryki danych Azure. Zobacz [schemat i dane typu mapowania](copy-activity-schema-and-type-mapping.md) Aby poznać sposób działania kopiowania mapowania typu źródłowego: schemat i dane sink.

| Typ danych programu Teradata | Typ danych tymczasowych fabryki danych |
|:--- |:--- |
| BigInt |Int64 |
| Obiekt blob |Byte[] |
| Bajtów |Byte[] |
| ByteInt |Int16 |
| char |Ciąg |
| CLOB |Ciąg |
| Date |DateTime |
| Decimal |Decimal |
| O podwójnej precyzji |O podwójnej precyzji |
| Grafika |Ciąg |
| Liczba całkowita |Int32 |
| Interwał dnia |Zakres czasu |
| Interwał dzień na godzinę |Zakres czasu |
| Interwał dzień na minutę |Zakres czasu |
| Interwał dzień na sekundę |Zakres czasu |
| Interwał, godzinę |Zakres czasu |
| Interwał godzinę, minutę |Zakres czasu |
| Interwał godzinę na sekundę |Zakres czasu |
| Interwał minutę |Zakres czasu |
| Interwał minutę na sekundę |Zakres czasu |
| Interwał miesiąca |Ciąg |
| Interwał drugi |Zakres czasu |
| Interwał roku |Ciąg |
| Interwał rok, miesiąc |Ciąg |
| Liczba |O podwójnej precyzji |
| Period(Date) |Ciąg |
| Period(Time) |Ciąg |
| Okres (czas ze strefą czasową) |Ciąg |
| Period(TimeStamp) |Ciąg |
| Okres (sygnatura ze strefą czasową) |Ciąg |
| SmallInt |Int16 |
| Time |Zakres czasu |
| Czas ze strefą czasową |Ciąg |
| Sygnatura czasowa |DateTime |
| Sygnatura czasowa ze strefą czasową |DateTimeOffset |
| VarByte |Byte[] |
| VarChar |Ciąg |
| VarGraphic |Ciąg |
| Xml |Ciąg |


## <a name="next-steps"></a>Kolejne kroki
Lista magazynów danych obsługiwane jako źródła i wychwytywanie przez działanie kopiowania w fabryce danych Azure, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
