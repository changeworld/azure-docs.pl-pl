---
title: Kopiowanie danych ze źródeł ODBC przy użyciu Azure Data Factory
description: Informacje o kopiowaniu danych ze źródeł OData do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w potoku Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: jingwang
ms.openlocfilehash: a5489b22ade2c661b2fae23c70440a29489e39f7
ms.sourcegitcommit: 8b37091efe8c575467e56ece4d3f805ea2707a64
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75830364"
---
# <a name="copy-data-from-and-to-odbc-data-stores-using-azure-data-factory"></a>Kopiowanie danych z i do magazynów danych ODBC przy użyciu Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-odbc-connector.md)
> * [Bieżąca wersja](connector-odbc.md)

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych z i do magazynu danych ODBC. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Ten łącznik ODBC jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)

Możesz skopiować dane ze źródła ODBC do dowolnego obsługiwanego magazynu danych ujścia lub skopiować z dowolnego obsługiwanego źródłowego magazynu danych do ujścia ODBC. Aby uzyskać listę magazynów danych, obsługiwane przez działanie kopiowania jako źródła/ujścia, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

Ten łącznik ODBC obsługuje kopiowanie danych z/do **wszelkich magazynów danych zgodnych z ODBC** przy użyciu uwierzytelniania **podstawowego** lub **anonimowego** . Wymagany jest **64-bitowy sterownik ODBC** .

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć tego łącznika ODBC, należy wykonać następujące:

- Skonfiguruj samodzielny Integration Runtime. Zobacz [własne środowisko IR](create-self-hosted-integration-runtime.md) artykuł, aby uzyskać szczegółowe informacje.
- Zainstaluj 64-bitowy sterownik ODBC dla magazynu danych na maszynie Integration Runtime.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek Data Factory specyficznych dla łącznika ODBC.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Dla połączonej usługi ODBC są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość Type musi być ustawiona na wartość: **ODBC** | Tak |
| connectionString | Parametry połączenia z wyjątkiem części poświadczenia. Można określić parametry połączenia z wzorcem, takie jak `"Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;"`, lub użyć nazwy DSN systemu (nazwa źródła danych) skonfigurowanej na maszynie Integration Runtime z `"DSN=<name of the DSN on IR machine>;"` (należy odpowiednio określić część poświadczeń w połączonej usłudze).<br>Możesz również wprowadzić hasło w Azure Key Vault i ściągnąć konfigurację   `password`z parametrów połączenia. Aby uzyskać więcej informacji, zapoznaj się z [poświadczeniami przechowywania w Azure Key Vault](store-credentials-in-key-vault.md) .| Tak |
| authenticationType | Typ uwierzytelniania używany do nawiązywania połączenia z magazynem danych ODBC.<br/>Dozwolone wartości to: **podstawowe** i **anonimowe**. | Tak |
| userName | Określ nazwę użytkownika w przypadku korzystania z uwierzytelniania podstawowego. | Nie |
| hasło | Określ hasło dla konta użytkownika określonego dla nazwy użytkownika. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Nie |
| poświadczenia | Część poświadczeń dostępu do parametrów połączenia określona w formacie wartości właściwości specyficznej dla sterownika. Przykład: `"RefreshToken=<secret refresh token>;"`. Oznacz to pole jako element SecureString. | Nie |
| connectVia | [Środowiska Integration Runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. Samodzielna Integration Runtime jest wymagana, jak wspomniano w [wymaganiach wstępnych](#prerequisites). |Tak |

**Przykład 1: używanie uwierzytelniania podstawowego**

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": "<connection string>",
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

**Przykład 2: używanie uwierzytelniania anonimowego**

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": "<connection string>",
            "authenticationType": "Anonymous",
            "credential": {
                "type": "SecureString",
                "value": "RefreshToken=<secret refresh token>;"
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

Aby uzyskać pełną listę sekcje i właściwości dostępne Definiowanie zestawów danych, zobacz [zestawów danych](concepts-datasets-linked-services.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych ODBC.

Aby skopiować dane z magazynu danych zgodnego ze standardem ODBC, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość Type zestawu danych musi być ustawiona na: **ODBC** | Tak |
| tableName | Nazwa tabeli w magazynie danych ODBC. | Nie dla źródła (Jeśli określono "zapytanie" w źródle aktywności);<br/>Tak dla ujścia |

**Przykład**

```json
{
    "name": "ODBCDataset",
    "properties": {
        "type": "OdbcTable",
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<ODBC linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

Jeśli używasz zestawu danych `RelationalTable` z określonym typem, nadal jest on obsługiwany w przypadku, gdy zamierzasz korzystać z nowego.

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło ODBC.

### <a name="odbc-as-source"></a>ODBC jako źródło

Aby skopiować dane z magazynu danych zgodnego z ODBC, w sekcji **Źródło** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość Type źródła działania Copy musi być ustawiona na wartość: **OdbcSource** | Tak |
| query | Umożliwia odczytywanie danych niestandardowe zapytania SQL. Na przykład: `"SELECT * FROM MyTable"`. | Nie (Jeśli określono parametr "tableName" w zestawie danych) |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromODBC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ODBC input dataset name>",
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
                "type": "OdbcSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Jeśli używasz źródła z wpisaną `RelationalSource`, jest ono nadal obsługiwane w stanie takim, w jakim będziesz mieć możliwość korzystania z nowej usługi.

### <a name="odbc-as-sink"></a>ODBC jako ujścia

Aby skopiować dane do magazynu danych zgodnego ze standardem ODBC, ustaw typ ujścia w działaniu Copy na **OdbcSink**. Następujące właściwości są obsługiwane w działaniu kopiowania **ujścia** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość Type ujścia działania Copy musi być ustawiona na wartość: **OdbcSink** | Tak |
| writeBatchTimeout |Czas oczekiwania na zakończenie operacji wstawiania partii przed upływem limitu czasu.<br/>Dozwolone wartości to: TimeSpan. Przykład: "00: 30:00" (30 minut). |Nie |
| writeBatchSize |Wstawia dane do tabeli SQL, gdy rozmiar buforu osiągnie writeBatchSize.<br/>Dozwolone wartości to: Integer (liczba wierszy). |Nie (domyślnie: 0 — wykryto Autowykrywanie) |
| preCopyScript |Określ zapytanie SQL dla działania kopiowania, które ma zostać wykonane przed zapisaniem danych w magazynie danych w każdym przebiegu. Ta właściwość służy do czyszczenia wstępnie załadowanych danych. |Nie |

> [!NOTE]
> W przypadku elementu "writeBatchSize", jeśli nie jest ustawiony (Autowykrywanie), działanie kopiowania najpierw wykrywa, czy sterownik obsługuje operacje wsadowe, i ustawia go na 10000, jeśli tak, lub jeśli nie, ustawia go na 1. Jeśli jawnie ustawisz wartość inną niż 0, działanie kopiowania uznaje wartość i kończy się niepowodzeniem w czasie wykonywania, jeśli sterownik nie obsługuje operacji wsadowych.

**Przykład:**

```json
"activities":[
    {
        "name": "CopyToODBC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ODBC output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "OdbcSink",
                "writeBatchSize": 100000
            }
        }
    }
]
```

## <a name="sap-hana-sink"></a>SAP HANA ujścia

>[!NOTE]
>Aby skopiować dane z SAP HANA magazynu danych, zapoznaj się z łącznikiem macierzystym [SAP HANA](connector-sap-hana.md). Aby skopiować dane do SAP HANA, wykonaj następującą instrukcję, aby użyć łącznika ODBC. Zwróć uwagę, że połączone usługi dla łącznika SAP HANA i łącznika ODBC są z różnymi rodzajami, więc nie mogą być ponownie używane.
>

Dane można kopiować do SAP HANA bazy danych przy użyciu uniwersalnego łącznika ODBC.

Skonfiguruj własne Integration Runtime na komputerze z dostępem do magazynu danych. Aby nawiązać połączenie z magazynem danych, Integration Runtime używa sterownika ODBC dla SAP HANA. W związku z tym Zainstaluj sterownik, jeśli nie został jeszcze zainstalowany na tym samym komputerze. Szczegółowe informacje znajdują się w sekcji [wymagania wstępne](#prerequisites) .

Przed użyciem ujścia SAP HANA w rozwiązaniu Data Factory Sprawdź, czy Integration Runtime może nawiązać połączenie z magazynem danych, korzystając z instrukcji w sekcji [Rozwiązywanie problemów z łącznością](#troubleshoot-connectivity-issues) .

Utwórz połączoną usługę ODBC, aby połączyć SAP HANA magazyn danych z usługą Azure Data Factory, jak pokazano w następującym przykładzie:

```json
{
    "name": "SAPHANAViaODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": "Driver={HDBODBC};servernode=<HANA server>.clouddatahub-int.net:30015",
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

Zapoznaj się z artykułem od początku, aby zapoznać się z szczegółowym omówieniem używania magazynów danych ODBC jako magazynów danych źródłowych/ujścia w operacji kopiowania.

## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).


## <a name="troubleshoot-connectivity-issues"></a>Rozwiązywanie problemów z łącznością

Aby rozwiązać problemy z połączeniem, Użyj karty **Diagnostyka** w **Integration Runtime Configuration Manager**.

1. Uruchom **Configuration Manager Integration Runtime**.
2. Przejdź na kartę **Diagnostyka** .
3. W sekcji "Testuj połączenie" Wybierz **Typ** magazynu danych (połączona usługa).
4. Określ **Parametry połączenia** , które są używane do nawiązywania połączenia z magazynem danych, **Wybierz uwierzytelnianie** i wprowadź **nazwę użytkownika**, **hasło**i/lub **poświadczenia**.
5. Kliknij przycisk **Test connection** , aby przetestować połączenie z magazynem danych.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md##supported-data-stores-and-formats).
