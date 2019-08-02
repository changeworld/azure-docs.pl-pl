---
title: Kopiowanie danych z SAP HANA przy użyciu Azure Data Factory | Microsoft Docs
description: Informacje o kopiowaniu danych z SAP HANA do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w potoku Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: jingwang
ms.openlocfilehash: e9b024fc3c07670201cf72cf80c0b69bf68f1cc8
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68725998"
---
# <a name="copy-data-from-sap-hana-using-azure-data-factory"></a>Kopiowanie danych z SAP HANA przy użyciu Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-sap-hana-connector.md)
> * [Bieżąca wersja](connector-sap-hana.md)

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych z bazy danych SAP HANA. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Dane z bazy danych programu SAP HANA można kopiować do dowolnego obsługiwanego magazynu danych ujścia. Listę magazynów danych obsługiwanych jako źródła/ujścia przez działanie kopiowania można znaleźć w tabeli [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) .

W SAP HANA ten łącznik obsługuje:

- Kopiowanie danych z dowolnej wersji SAP HANA Database.
- Kopiowanie danych z **modeli informacji platformy Hana** (takich jak widoki analityczne i obliczeniowe) oraz **tabele wierszy/kolumn**.
- Kopiowanie danych przy użyciu uwierzytelniania **podstawowego** lub **systemu Windows** .

> [!TIP]
> Aby skopiować dane **do** SAP HANA magazynu danych, użyj ogólnego łącznika ODBC. Aby uzyskać szczegółowe informacje, zobacz [SAP HANA sink](connector-odbc.md#sap-hana-sink) . Zwróć uwagę, że połączone usługi dla łącznika SAP HANA i łącznika ODBC są z różnymi rodzajami, więc nie mogą być ponownie używane.

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć tego łącznika SAP HANA, musisz:

- Skonfiguruj samodzielny Integration Runtime. Zobacz [własne środowisko IR](create-self-hosted-integration-runtime.md) artykuł, aby uzyskać szczegółowe informacje.
- Zainstaluj SAP HANA sterownika ODBC na maszynie Integration Runtime. Sterownik SAP HANA ODBC można pobrać z [Centrum pobierania oprogramowania SAP](https://support.sap.com/swdc). Wyszukaj za pomocą słowa kluczowego **SAP HANA Client dla systemu Windows**.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania Data Factory jednostek specyficznych dla łącznika SAP HANA.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Następujące właściwości są obsługiwane dla SAP HANA połączonej usługi:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| — typ | Właściwość Type musi mieć ustawioną wartość: **SapHana** | Tak |
| connectionString | Określ informacje, które są konieczne do nawiązania połączenia z SAP HANA przy użyciu **uwierzytelniania podstawowego** lub **uwierzytelniania systemu Windows**. Zapoznaj się z poniższymi przykładami.<br>W parametrach połączenia wymagany jest serwer/port (port domyślny to 30015), a nazwa użytkownika i hasło są wymagane w przypadku uwierzytelniania podstawowego. Aby uzyskać dodatkowe ustawienia zaawansowane, zapoznaj się z tematem [SAP HANA właściwości połączenia ODBC](<https://help.sap.com/viewer/0eec0d68141541d1b07893a39944924e/2.0.02/en-US/7cab593774474f2f8db335710b2f5c50.html>)<br/>Możesz również wprowadzić hasło w Azure Key Vault i ściągnąć konfigurację hasła z parametrów połączenia. Aby uzyskać więcej informacji, zobacz artykuł [przechowywanie poświadczeń w Azure Key Vault](store-credentials-in-key-vault.md) artykule. | Tak |
| userName | Określ nazwę użytkownika w przypadku korzystania z uwierzytelniania systemu Windows. Przykład: `user@domain.com` | Nie |
| password | Określ hasło dla konta użytkownika. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Nie |
| connectVia | [Środowiska Integration Runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. Samodzielna Integration Runtime jest wymagana, jak wspomniano [](#prerequisites)w wymaganiach wstępnych. |Yes |

**Przykład: Użyj uwierzytelniania podstawowego**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "connectionString": "SERVERNODE=<server>:<port (optional)>;UID=<userName>;PWD=<Password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Przykład: Użyj uwierzytelniania systemu Windows**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "connectionString": "SERVERNODE=<server>:<port (optional)>;",
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

Jeśli używasz połączonej usługi SAP HANA z następującym ładunkiem, nadal jest ona obsługiwana w stanie takim, w jakim jesteś nowym użytkownikiem.

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

Aby skopiować dane z SAP HANA, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość Type zestawu danych musi być ustawiona na wartość: **SapHanaTable** | Tak |
| schema | Nazwa schematu w bazie danych SAP HANA. | Nie (Jeśli określono parametr "zapytanie" w źródle działania) |
| table | Nazwa tabeli w bazie danych SAP HANA. | Nie (Jeśli określono parametr "zapytanie" w źródle działania) |

**Przykład:**

```json
{
    "name": "SAPHANADataset",
    "properties": {
        "type": "SapHanaTable",
        "typeProperties": {
            "schema": "<schema name>",
            "table": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP HANA linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Jeśli używasz `RelationalTable` określonego zestawu danych, jest on nadal obsługiwany w stanie takim, w jakim będziesz mieć możliwość korzystania z nowej usługi.

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez SAP HANA źródło.

### <a name="sap-hana-as-source"></a>SAP HANA jako źródło

Aby skopiować dane z SAP HANA, w sekcji **Źródło** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość Type źródła działania Copy musi mieć ustawioną wartość: **SapHanaSource** | Tak |
| query | Określa zapytanie SQL do odczytu danych z wystąpienia SAP HANA. | Tak |
| packetSize | Określa rozmiar pakietu sieciowego (w kilobajtach), aby podzielić dane na wiele bloków. Jeśli masz dużą ilość danych do skopiowania, zwiększenie rozmiaru pakietu może zwiększyć szybkość odczytywania SAP HANA w większości przypadków. Podczas dopasowywania rozmiaru pakietu zaleca się testowanie wydajności. | Nie.<br>Wartość domyślna to 2048 (2 MB). |

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
                "type": "SapHanaSource",
                "query": "<SQL query for SAP HANA>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Jeśli używasz `RelationalSource` źródła kopii typu, jest ono nadal obsługiwane w stanie takim, w jakim będziesz mieć możliwość użycia nowego.

## <a name="data-type-mapping-for-sap-hana"></a>Mapowanie typu danych dla SAP HANA

Podczas kopiowania danych z SAP HANA następujące mapowania są używane z SAP HANA typów danych do Azure Data Factory danych pośrednich. Zobacz [schemat i dane mapowanie typu](copy-activity-schema-and-type-mapping.md) Aby poznać sposób działania kopiowania mapowania typ schematu i danych źródła do ujścia.

| Typ danych SAP HANA | Typ danych tymczasowych fabryki danych |
| ------------------ | ------------------------------ |
| ALPHANUM           | String                         |
| BIGINT             | Int64                          |
| BINARNY             | Byte[]                         |
| DWUNTEXT            | String                         |
| TWORZENIA               | Byte[]                         |
| LOGICZNA               | Byte                           |
| OBIEKTÓW CLOB               | String                         |
| DATE               | Datetime                       |
| DECIMAL            | Decimal                        |
| DOUBLE             | Double                         |
| FLOAT              | Double                         |
| CAŁKOWITĄ            | Int32                          |
| NCLOB              | String                         |
| NVARCHAR           | String                         |
| REAL               | Single                         |
| SECONDDATE         | Datetime                       |
| SHORTTEXT          | String                         |
| SMALLDECIMAL       | Decimal                        |
| SMALLINT           | Int16                          |
| STGEOMETRYTYPE     | Byte[]                         |
| STPOINTTYPE        | Byte[]                         |
| TEXT               | Ciąg                         |
| TIME               | TimeSpan                       |
| TINYINT            | Byte                           |
| VARCHAR            | Ciąg                         |
| TIMESTAMP          | Datetime                       |
| LICZBY          | Byte[]                         |

## <a name="next-steps"></a>Następne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
