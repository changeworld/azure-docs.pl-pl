---
title: Kopiowanie danych do i z programem Oracle przy użyciu fabryki danych Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z obsługiwanej źródłowej magazynów z bazą danych Oracle lub Oracle sklepów zbiornika obsługiwanych przy użyciu fabryki danych.
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
ms.date: 06/14/2018
ms.author: jingwang
ms.openlocfilehash: 6a232787793f9f4992a4dece821ae0bcc9059afc
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37058990"
---
# <a name="copy-data-from-and-to-oracle-by-using-azure-data-factory"></a>Kopiowanie danych z i do Oracle przy użyciu fabryki danych Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [W wersji 1](v1/data-factory-onprem-oracle-connector.md)
> * [Bieżąca wersja](connector-oracle.md)

W tym artykule omówiono sposób użycia działanie kopiowania w fabryce danych Azure, aby skopiować dane z i do bazy danych programu Oracle. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Możesz skopiować dane z bazy danych Oracle żadnych obsługiwanych ujścia magazynu danych. Możesz również skopiować danych z dowolnego źródła obsługiwanych magazynu danych z bazą danych Oracle. Lista magazynów danych, które są obsługiwane jako źródła lub wychwytywanie przez działanie kopiowania, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności ten łącznik Oracle obsługuje następujące wersje bazy danych programu Oracle. Obsługuje ona również uwierzytelnianie podstawowe lub identyfikatora OID:

- R1 Oracle 12c (12.1)
- Oracle 11g R1, R2 (11.1, 11.2)
- Oracle 10g R1, R2 (10.1, 10.2)
- Oracle 9i R1, R2 (9.0.1, 9.2)
- Oracle 8i R3 (8.1.7)

> [!Note]
> Serwer proxy Oracle nie jest obsługiwane.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skopiować dane z i do bazy danych programu Oracle nie jest dostępny publicznie, należy skonfigurować środowisko uruchomieniowe Self-hosted integracji. Aby uzyskać więcej informacji na temat środowiska uruchomieniowego integracji, zobacz [środowiska uruchomieniowego integracji Self-hosted](create-self-hosted-integration-runtime.md). Środowiska uruchomieniowego integracji zawiera wbudowane Sterownik Oracle. W związku z tym nie trzeba ręcznie zainstalować sterownik podczas kopiowania danych z i do programu Oracle.

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek fabryki danych określonej do łącznika programu Oracle.

## <a name="linked-service-properties"></a>Połączona usługa właściwości

Następujące właściwości są obsługiwane przez usługę Oracle połączone.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi mieć ustawioną **Oracle**. | Yes |
| Parametry połączenia | Określa informacje wymagane do nawiązania połączenia z wystąpieniem bazy danych programu Oracle. Zaznacz to pole jako SecureString Zapisz w bezpiecznej lokalizacji w fabryce danych lub [odwołania klucz tajny przechowywane w usłudze Azure Key Vault](store-credentials-in-key-vault.md).<br><br>**Obsługiwany typ połączenia**: można użyć **Oracle SID** lub **nazwa usługi Oracle** Aby zidentyfikować bazy danych:<br>— Jeśli używasz identyfikator SID: `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;`<br>— Jeśli używasz nazwa usługi: `Host=<host>;Port=<port>;ServiceName=<servicename>;User Id=<username>;Password=<password>;` | Yes |
| connectVia | [Integrację środowiska uruchomieniowego](concepts-integration-runtime.md) ma być używany do nawiązania połączenia z magazynem danych. (Jeśli w magazynie danych jest dostępny publicznie) można użyć środowiska uruchomieniowego integracji Self-hosted lub środowiska uruchomieniowego integracji Azure. Jeśli nie zostanie określony, używa domyślnej środowiska uruchomieniowego integracji Azure. |Nie |

**Przykład:**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;"
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

Aby uzyskać pełną listę właściwości dostępnych do definiowania zestawów danych i sekcje, zobacz [zestawów danych](concepts-datasets-linked-services.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych Oracle.

Aby skopiować dane z i do Oracle, ustaw właściwość Typ zestawu danych do **OracleTable**. Następujące właściwości są obsługiwane.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwości typu zestawu danych musi mieć ustawioną **OracleTable**. | Yes |
| tableName |Nazwa tabeli w bazie danych programu Oracle, odnoszący się do połączonej usługi. | Yes |

**Przykład:**

```json
{
    "name": "OracleDataset",
    "properties":
    {
        "type": "OracleTable",
        "linkedServiceName": {
            "referenceName": "<Oracle linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Pełną listę sekcje i właściwości dostępnych dla definiowania działań, zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę obsługiwanych przez Oracle źródłowy i odbiorczy właściwości.

### <a name="oracle-as-a-source-type"></a>Oracle jako typ źródła

Aby skopiować dane z bazy danych Oracle, należy ustawić typ źródła w przypadku działania kopiowania do **OracleSource**. Następujące właściwości są obsługiwane w przypadku działania kopiowania **źródła** sekcji.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi mieć ustawioną właściwość type źródła działania kopiowania **OracleSource**. | Yes |
| oracleReaderQuery | Użyj niestandardowych zapytania SQL można odczytać danych. Może to być na przykład `"SELECT * FROM MyTable"`. | Nie |

Jeśli nie określisz "oracleReaderQuery" kolumn określona w sekcji "structure" zestawu danych służą do utworzenia kwerendy (`select column1, column2 from mytable`) w celu uruchomienia bazy danych Oracle. Jeśli definicji zestawu danych nie ma "structure", wszystkie kolumny są wybierane w tabeli.

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Oracle input dataset name>",
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
                "type": "OracleSource",
                "oracleReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="oracle-as-a-sink-type"></a>Oracle jako typ ujścia

Aby skopiować dane do bazy danych Oracle, należy ustawić typ ujścia w działaniu kopiowania do **OracleSink**. Następujące właściwości są obsługiwane w przypadku działania kopiowania **zbiornika** sekcji.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi mieć ustawioną właściwość typu sink działania kopiowania **OracleSink**. | Yes |
| writeBatchSize | Wstawia dane do tabeli SQL, gdy writeBatchSize osiągnie rozmiar buforu.<br/>Dozwolone wartości to liczba całkowita (liczba wierszy). |Nie (wartość domyślna to 10 000) |
| writeBatchTimeout | Czas na ukończenie zanim upłynie limit czasu operacji wstawiania wsadowego oczekiwania.<br/>Dozwolone wartości to Timespan. Przykład to 00:30:00 (30 minut). | Nie |
| preCopyScript | Określ zapytanie SQL dla działania kopiowania do wykonania przed zapisaniem danych do bazy danych Oracle w każdym przebiegu. Ta właściwość służy do oczyszczania załadowanych danych. | Nie |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyToOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Oracle output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "OracleSink"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-oracle"></a>Mapowanie dla programu Oracle typu danych

Po skopiowaniu danych od i do Oracle, następujące mapowania są używane z typów danych Oracle do typów danych tymczasowych fabryki danych. Aby poznać sposób działania kopiowania mapowania typu źródłowego: schemat i dane sink, zobacz temat [schemat i dane typu mapowania](copy-activity-schema-and-type-mapping.md).

| Typ danych Oracle | Typ danych tymczasowych fabryki danych |
|:--- |:--- |
| BPLIK |Byte[] |
| OBIEKT BLOB |Byte[]<br/>(obsługiwana tylko w bazie danych Oracle 10 GB/s i nowsze) |
| CHAR |Ciąg |
| CLOB |Ciąg |
| DATE |DateTime |
| FLOAT |Decimal, ciąg (jeśli precyzja > 28) |
| LICZBA CAŁKOWITA |Decimal, ciąg (jeśli precyzja > 28) |
| DŁUGA |Ciąg |
| LONG RAW |Byte[] |
| NCHAR |Ciąg |
| NCLOB |Ciąg |
| NUMER |Decimal, ciąg (jeśli precyzja > 28) |
| NVARCHAR2 |Ciąg |
| NIEPRZETWORZONE |Byte[] |
| ROWID |Ciąg |
| ZNACZNIK CZASU |DateTime |
| SYGNATURA CZASOWA Z LOKALNEJ STREFIE CZASOWEJ |Ciąg |
| SYGNATURA CZASOWA ZE STREFĄ CZASOWĄ |Ciąg |
| LICZBA CAŁKOWITA BEZ ZNAKU |Liczba |
| VARCHAR2 |Ciąg |
| XML |Ciąg |

> [!NOTE]
> DRUGI INTERWAŁ rok na miesiąc i INTERWAŁ dzień na typy danych nie są obsługiwane.


## <a name="next-steps"></a>Kolejne kroki
Lista magazynów danych obsługiwane jako źródła i wychwytywanie przez działanie kopiowania w fabryce danych, zobacz [obsługiwane magazyny danych](copy-activity-overview.md##supported-data-stores-and-formats).
