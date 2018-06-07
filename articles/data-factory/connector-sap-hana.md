---
title: Kopiowanie danych z programu SAP HANA przy użyciu fabryki danych Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z SAP HANA do zbiornika obsługiwane magazyny danych za pomocą działania kopiowania w potoku fabryki danych Azure.
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
ms.openlocfilehash: 3afdcbfc2d1d6f40db0f0336be161a06908cb883
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34618001"
---
# <a name="copy-data-from-sap-hana-using-azure-data-factory"></a>Kopiowanie danych z programu SAP HANA przy użyciu fabryki danych Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1 — ogólnie dostępna](v1/data-factory-sap-hana-connector.md)
> * [Wersja 2 — wersja zapoznawcza](connector-sap-hana.md)

W tym artykule omówiono sposób użycia działanie kopiowania w fabryce danych Azure, aby skopiować dane z bazy danych SAP HANA. Opiera się na [skopiuj omówienie działania](copy-activity-overview.md) artykułu, który przedstawia ogólny przegląd działanie kopiowania.

> [!NOTE]
> Ten artykuł dotyczy wersji 2 usługi Data Factory, która jest obecnie dostępna w wersji zapoznawczej. Jeśli używasz wersji 1 usługi fabryka danych, która jest ogólnie dostępna (GA), zobacz [SAP HANA łącznika w wersji 1](v1/data-factory-sap-hana-connector.md).

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Możesz skopiować dane z bazy danych SAP HANA żadnych obsługiwanych ujścia magazynu danych. Lista magazynów danych obsługiwane jako źródła/wychwytywanie przez działanie kopiowania, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności ten łącznik SAP HANA obsługuje:

- Kopiowanie danych z dowolnej wersji bazy danych SAP HANA.
- Kopiowanie danych z **HANA informacji o modelach** (takich jak widoki analityczne i obliczeń) i **wierszy i kolumn tabel** przy użyciu zapytania SQL.
- Kopiowanie danych przy użyciu **podstawowe** lub **Windows** uwierzytelniania.

> [!NOTE]
> Aby skopiować dane **do** danych SAP HANA przechowywać, użyj ogólny łącznik ODBC. Zobacz [zbiornika SAP HANA](connector-odbc.md#sap-hana-sink) ze szczegółami. Uwaga, są połączone usługi SAP HANA connector i łącznika ODBC z innego typu w związku z tym nie można użyć ponownie.

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć tego łącznika SAP HANA, musisz:

- Konfigurowanie środowiska uruchomieniowego integracji Self-hosted. Zobacz [środowiska uruchomieniowego integracji Self-hosted](create-self-hosted-integration-runtime.md) artykułu, aby uzyskać szczegółowe informacje.
- Na komputerze środowiska uruchomieniowego integracji, należy zainstalować sterownik SAP HANA ODBC. Możesz pobrać sterownik SAP HANA ODBC z [Centrum pobierania oprogramowania SAP](https://support.sap.com/swdc). Wyszukiwanie ze słowem kluczowym **SAP HANA klienta dla systemu Windows**.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek fabryki danych określonej do łącznika SAP HANA.

## <a name="linked-service-properties"></a>Połączona usługa właściwości

Obsługiwane są następujące właściwości dla SAP HANA połączone usługi:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi mieć ustawioną: **SapHana** | Yes |
| serwer | Nazwa serwera, na którym znajduje się z wystąpieniem SAP HANA. Jeśli serwer używa portu dostosowane, określ `server:port`. | Yes |
| Typ authenticationType | Typ uwierzytelniania używany do łączenia z bazą danych SAP HANA.<br/>Dozwolone wartości to: **podstawowe**, i **systemu Windows** | Yes |
| userName | Nazwa użytkownika, który ma dostęp do serwera SAP. | Yes |
| hasło | Hasło dla użytkownika. Zaznacz to pole jako SecureString Zapisz w bezpiecznej lokalizacji w fabryce danych lub [odwołania klucz tajny przechowywane w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| connectVia | [Integrację środowiska uruchomieniowego](concepts-integration-runtime.md) ma być używany do nawiązania połączenia z magazynem danych. Środowisko uruchomieniowe integracji Self-hosted jest wymagana, jak wspomniano w [wymagania wstępne](#prerequisites). |Yes |

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

Aby uzyskać pełną listę właściwości dostępnych do definiowania zestawów danych i sekcje zobacz artykuł zestawów danych. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych SAP HANA.

Aby skopiować dane z programu SAP HANA, ustaw właściwość Typ zestawu danych do **RelationalTable**. Wpisz RelationalTable, gdy nie ma żadnych właściwości specyficzne dla typu obsługiwane dla zestawu danych SAP HANA.

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

Pełną listę sekcje i właściwości dostępnych dla definiowania działań, zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwane przez źródło SAP HANA.

### <a name="sap-hana-as-source"></a>SAP HANA jako źródło

Aby skopiować dane z programu SAP HANA, należy ustawić typ źródła w przypadku działania kopiowania do **RelationalSource**. Następujące właściwości są obsługiwane w przypadku działania kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi mieć ustawioną właściwość type źródła działania kopiowania: **RelationalSource** | Yes |
| query | Określa zapytanie SQL do odczytywania danych z wystąpieniem SAP HANA. | Yes |

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

## <a name="data-type-mapping-for-sap-hana"></a>Mapowanie typu danych dla SAP HANA

Podczas kopiowania danych z programu SAP HANA, następujące mapowania są używane do typów danych tymczasowych fabryki danych Azure z typów danych SAP HANA. Zobacz [schemat i dane typu mapowania](copy-activity-schema-and-type-mapping.md) Aby poznać sposób działania kopiowania mapowania typu źródłowego: schemat i dane sink.

| Typ danych SAP HANA | Typ danych tymczasowych fabryki danych |
|:--- |:--- |
| ALPHANUM | Ciąg |
| BIGINT | Int64 |
| OBIEKT BLOB | Byte[] |
| WARTOŚĆ LOGICZNA | Bajtów |
| CLOB | Byte[] |
| DATE | DateTime |
| DECIMAL | Decimal |
| O PODWÓJNEJ PRECYZJI | Pojedyncze |
| INT | Int32 |
| NVARCHAR | Ciąg |
| RZECZYWISTE | Pojedyncze |
| SECONDDATE | DateTime |
| SMALLINT | Int16 |
| CZAS | Zakres czasu |
| ZNACZNIK CZASU | DateTime |
| TINYINT | Bajtów |
| VARCHAR | Ciąg |

## <a name="known-limitations"></a>Znane ograniczenia

Podczas kopiowania danych z programu SAP HANA istnieje kilka znane ograniczenia:

- Ciągi NVARCHAR są zaokrąglane do maksymalnie 4000 znaków Unicode
- SMALLDECIMAL nie jest obsługiwane.
- VARBINARY nie jest obsługiwane.
- Prawidłowe daty należą do zakresu od 30-1899/12, a 9999-12/31


## <a name="next-steps"></a>Kolejne kroki
Lista magazynów danych obsługiwane jako źródła i wychwytywanie przez działanie kopiowania w fabryce danych Azure, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
