---
title: Kopiowanie danych z platformy SAP HANA przy użyciu usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z platformy SAP HANA do magazynów danych ujścia obsługiwane za pomocą działania kopiowania w potoku usługi Azure Data Factory.
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
ms.openlocfilehash: cdd83c3ff9d34a5e8b7f2c164136ab82f498ffb5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60343770"
---
# <a name="copy-data-from-sap-hana-using-azure-data-factory"></a>Kopiowanie danych z platformy SAP HANA przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1](v1/data-factory-sap-hana-connector.md)
> * [Bieżąca wersja](connector-sap-hana.md)

W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory, aby skopiować dane z bazy danych SAP HANA. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Możesz skopiować dane z bazy danych SAP HANA, do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych obsługiwanych jako źródła/ujścia działania kopiowania, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności ten łącznik SAP HANA obsługuje:

- Kopiowanie danych z dowolnej wersji bazy danych SAP HANA.
- Kopiowanie danych z **modele informacji platformy HANA** (takich jak widoki analityczne i obliczenia) i **wiersza/kolumny tabel** przy użyciu zapytań SQL.
- Kopiowanie danych przy użyciu **podstawowe** lub **Windows** uwierzytelniania.

> [!NOTE]
> Aby skopiować dane **do** przechowywania danych SAP HANA, należy użyć ogólnego łącznika ODBC. Zobacz [ujścia platformy SAP HANA](connector-odbc.md#sap-hana-sink) ze szczegółowymi informacjami. Uwaga, połączone usługi dla łącznika SAP HANA i łącznik ODBC są z różnych typów związku z tym nie można użyć ponownie.

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć tego łącznika SAP HANA, należy:

- Skonfiguruj środowiskiem Integration Runtime. Zobacz [własne środowisko IR](create-self-hosted-integration-runtime.md) artykuł, aby uzyskać szczegółowe informacje.
- Zainstaluj sterownik ODBC platformy SAP HANA na komputerze środowisko Integration Runtime. Możesz pobrać sterownik ODBC platformy SAP HANA z [SAP Software Download Center](https://support.sap.com/swdc). Wyszukiwanie za pomocą słowa kluczowego **SAP HANA klienta dla Windows**.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które są używane do definiowania jednostek usługi fabryka danych określonej do łącznika SAP HANA.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Następujące właściwości są obsługiwane w przypadku oprogramowania SAP HANA, połączone usługi:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi być równa: **SapHana** | Yes |
| serwer | Nazwa serwera, na którym znajduje się wystąpienie SAP HANA. Jeśli serwer używa portu niestandardowe, należy określić `server:port`. | Yes |
| Element authenticationType | Typ uwierzytelniania używany do łączenia z bazą danych SAP HANA.<br/>Dozwolone wartości to: **Podstawowe**, i **Windows** | Yes |
| userName | Nazwa użytkownika, który ma dostęp do serwera SAP. | Yes |
| hasło | Hasło użytkownika. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| connectVia | [Środowiska Integration Runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. Własne środowisko IR jest wymagany, zgodnie z opisem w [wymagania wstępne](#prerequisites). |Yes |

**Przykład:**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "server": "<server>:<port (optional)>",
            "authenticationType": "Basic",
            "userName": "<username>",
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

Aby uzyskać pełną listę sekcje i właściwości dostępne Definiowanie zestawów danych zobacz artykuł zestawów danych. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych SAP HANA.

Aby skopiować dane z platformy SAP HANA, należy ustawić właściwość typu zestawu danych na **RelationalTable**. Gdy nie ma żadnych właściwości typu obsługiwanych dla zestawu danych SAP HANA z typu RelationalTable.

**Przykład:**

```json
{
    "name": "SAPHANADataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<SAP HANA linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło oprogramowania SAP HANA.

### <a name="sap-hana-as-source"></a>SAP HANA jako źródło

Aby skopiować dane z platformy SAP HANA, należy ustawić typ źródłowego w działaniu kopiowania, aby **RelationalSource**. Następujące właściwości są obsługiwane w działaniu kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi być równa wartości właściwości type źródło działania kopiowania: **RelationalSource** | Yes |
| query | Określa zapytanie SQL do odczytywania danych z wystąpienia oprogramowania SAP HANA. | Yes |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromSAPHANA",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP HANA input dataset name>",
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
                "query": "<SQL query for SAP HANA>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-hana"></a>Mapowanie typu danych dla oprogramowania SAP HANA

Podczas kopiowania danych z platformy SAP HANA, następujące mapowania są używane z typów danych SAP HANA na typy danych tymczasowych usługi Azure Data Factory. Zobacz [schemat i dane mapowanie typu](copy-activity-schema-and-type-mapping.md) Aby poznać sposób działania kopiowania mapowania typ schematu i danych źródła do ujścia.

| Typ danych SAP HANA | Typ danych tymczasowych fabryki danych |
|:--- |:--- |
| ALPHANUM | String |
| BIGINT | Int64 |
| BLOB | Byte[] |
| ATRYBUT TYPU WARTOŚĆ LOGICZNA | Byte |
| CLOB | Byte[] |
| DATE | DateTime |
| DECIMAL | Decimal |
| DOUBLE | Single |
| INT | Int32 |
| NVARCHAR | String |
| RZECZYWISTE | Single |
| SECONDDATE | DateTime |
| SMALLINT | Int16 |
| CZAS | TimeSpan |
| SYGNATURA CZASOWA | DateTime |
| TINYINT | Byte |
| VARCHAR | String |

## <a name="known-limitations"></a>Znane ograniczenia

Istnieje kilka znanych ograniczeniach podczas kopiowania danych z platformy SAP HANA:

- Ciągi NVARCHAR są obcinane do maksymalnej długości wynoszącej 4000 znaków Unicode
- Typ danych SMALLDECIMAL nie jest obsługiwane.
- VARBINARY nie jest obsługiwane.
- Prawidłowe daty są z zakresu od 1899, 12-30 a 9999-12-31


## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
