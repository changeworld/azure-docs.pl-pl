---
title: Kopiowanie danych do i z oprogramowania Oracle przy użyciu usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z obsługiwanego źródłowego magazynów z bazą danych Oracle lub Oracle do ujścia obsługiwanych magazynów za pomocą usługi Data Factory.
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
ms.openlocfilehash: 5039399ac875add02319e1a745d99344956c7bee
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2018
ms.locfileid: "37860218"
---
# <a name="copy-data-from-and-to-oracle-by-using-azure-data-factory"></a>Kopiowanie danych z i do oprogramowania Oracle przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1](v1/data-factory-onprem-oracle-connector.md)
> * [Bieżąca wersja](connector-oracle.md)

W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory do kopiowania danych z i do bazy danych Oracle. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Możesz skopiować dane z bazy danych Oracle do dowolnego obsługiwanego magazynu danych ujścia. Możesz także skopiować dane z dowolnego obsługiwanego źródłowego magazynu danych do bazy danych Oracle. Aby uzyskać listę magazynów danych, obsługiwane przez działanie kopiowania jako źródła lub ujścia, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności ten łącznik Oracle obsługuje następujące wersje bazy danych Oracle. Obsługuje również uwierzytelnianie Basic lub identyfikator OID:

- R1 Oracle 12c (12.1)
- Oracle 11g R1, R2 (11.1, 11.2)
- Oracle 10g R1, R2 (10.1, 10.2)
- Oracle 9i R1, R2 (9.0.1, 9.2)
- Oracle 8i R3 (8.1.7)

> [!Note]
> Serwer proxy Oracle nie jest obsługiwana.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skopiować dane z i do bazy danych Oracle, która nie jest dostępny publicznie, musisz skonfigurować środowiskiem Integration Runtime. Aby uzyskać więcej informacji na temat środowiska integration runtime, zobacz [własne środowisko IR](create-self-hosted-integration-runtime.md). Infrastruktura integration runtime zapewnia wbudowane Sterownik Oracle. W związku z tym nie trzeba ręcznie zainstalować sterownik podczas kopiowania danych z i do bazy danych Oracle.

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które są używane do definiowania jednostek usługi fabryka danych określonej do łącznika programu Oracle.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Następujące właściwości są obsługiwane w przypadku usługi połączonej bazy danych Oracle.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi być równa **Oracle**. | Yes |
| Parametry połączenia | Określa informacje wymagane do nawiązania połączenia z wystąpieniem bazy danych Oracle. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md).<br><br>**Obsługiwany typ połączenia**: możesz użyć **identyfikator SID programu Oracle** lub **nazwa usługi Oracle** Aby zidentyfikować bazy danych:<br>— Jeśli używasz identyfikatora SID: `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;`<br>— Jeśli używasz nazwa usługi: `Host=<host>;Port=<port>;ServiceName=<servicename>;User Id=<username>;Password=<password>;` | Yes |
| connectVia | [Środowiska integration runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. Używając środowiskiem Integration Runtime lub Azure Integration Runtime (Jeśli magazyn danych jest publicznie dostępny). Jeśli nie zostanie określony, używa domyślnego środowiska Azure Integration Runtime. |Nie |

>[!TIP]
>Jeśli napotkasz błąd powiedzenie "ORA 01025: parametr zaległej płatności za przedmiot poza zakresem" i usługi oprogramowania Oracle jest wersji 8i, Dodaj `WireProtocolMode=1` parametry połączenia i spróbuj ponownie.

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

Aby uzyskać pełną listę sekcje i właściwości dostępne Definiowanie zestawów danych, zobacz [zestawów danych](concepts-datasets-linked-services.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych programu Oracle.

Aby skopiować dane z i do oprogramowania Oracle, należy ustawić właściwość typu zestawu danych na **OracleTable**. Następujące właściwości są obsługiwane.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość typu elementu dataset musi być równa **OracleTable**. | Yes |
| tableName |Nazwa tabeli w bazie danych Oracle, których połączona usługa przywołuje. | Yes |

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

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez Oracle źródła i ujścia.

### <a name="oracle-as-a-source-type"></a>Oracle jako typ źródła

Aby skopiować dane z bazy danych Oracle, należy ustawić typ źródła w działaniu kopiowania, aby **OracleSource**. Następujące właściwości są obsługiwane w działaniu kopiowania **źródła** sekcji.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość typu źródła działania kopiowania musi być równa **OracleSource**. | Yes |
| oracleReaderQuery | Umożliwia odczytywanie danych niestandardowe zapytania SQL. Może to być na przykład `"SELECT * FROM MyTable"`. | Nie |

Jeśli nie określisz "oracleReaderQuery" kolumny zdefiniowane w sekcji "strukturę" zestawu danych są używane do konstruowania zapytania (`select column1, column2 from mytable`) do uruchamiania w bazie danych Oracle. Definicja zestawu danych nie ma "strukturę", wszystkie kolumny są wybierane z tabeli.

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

Aby skopiować dane do bazy danych Oracle, należy ustawić typ ujścia w działaniu kopiowania, aby **elementu OracleSink**. Następujące właściwości są obsługiwane w działaniu kopiowania **ujścia** sekcji.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type ujścia działania kopiowania musi być równa **elementu OracleSink**. | Yes |
| writeBatchSize | Wstawia dane do tabeli SQL, gdy writeBatchSize osiągnie rozmiar buforu.<br/>Dozwolone wartości to liczby całkowitej (liczba wierszy). |Nie (wartość domyślna to 10 000) |
| writeBatchTimeout | Czas na ukończenie przed upływem limitu czasu operacji wstawiania wsadowego oczekiwania.<br/>Dozwolone wartości to przedziału czasu. Przykładem jest 00:30:00 (30 minut). | Nie |
| preCopyScript | Określ zapytanie SQL, działanie kopiowania do wykonania przed zapisaniem danych do bazy danych Oracle, w każdym przebiegu. Ta właściwość służy do czyszczenia załadowanych danych. | Nie |

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

## <a name="data-type-mapping-for-oracle"></a>Mapowanie na oprogramowanie Oracle, typ danych

Podczas kopiowania danych z i do oprogramowania Oracle, następujące mapowania są używane z typów danych programu Oracle do typów danych tymczasowych fabryki danych. Aby uzyskać informacje dotyczące sposobu działania kopiowania mapowania typ schematu i danych źródła do ujścia, zobacz [schemat i dane mapowanie typu](copy-activity-schema-and-type-mapping.md).

| Typ danych Oracle | Typ danych tymczasowych fabryki danych |
|:--- |:--- |
| BPLIK |Byte[] |
| OBIEKT BLOB |Byte[]<br/>(obsługiwane tylko w bazie danych Oracle 10g lub nowszy) |
| CHAR |Ciąg |
| CLOB |Ciąg |
| DATE |DateTime |
| FLOAT |Decimal, ciąg (jeśli dokładności > 28) |
| LICZBA CAŁKOWITA |Decimal, ciąg (jeśli dokładności > 28) |
| DŁUGI |Ciąg |
| DŁUGI NIEPRZETWORZONE |Byte[] |
| NCHAR |Ciąg |
| NCLOB |Ciąg |
| NUMER |Decimal, ciąg (jeśli dokładności > 28) |
| NVARCHAR2 |Ciąg |
| NIEPRZETWORZONE |Byte[] |
| ROWID |Ciąg |
| ZNACZNIK CZASU: |DateTime |
| SYGNATURA CZASOWA PRZY UŻYCIU LOKALNEJ STREFY CZASOWEJ |Ciąg |
| SYGNATURA CZASOWA ZE STREFĄ CZASOWĄ |Ciąg |
| LICZBA CAŁKOWITA BEZ ZNAKU |Liczba |
| VARCHAR2 |Ciąg |
| XML |Ciąg |

> [!NOTE]
> Po drugie INTERWAŁU roku do miesiąca i INTERWAŁU dnia na typy danych nie są obsługiwane.


## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md##supported-data-stores-and-formats).
