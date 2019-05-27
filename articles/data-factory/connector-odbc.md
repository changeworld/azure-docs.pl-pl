---
title: Kopiowanie danych ze źródeł ODBC za pomocą usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak kopiować dane ze źródła danych OData do magazynów danych ujścia obsługiwane za pomocą działania kopiowania w potoku usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/19/2018
ms.author: jingwang
ms.openlocfilehash: f14c8f8ef9f0e59ac35dd7346bf37cc07f2cfb19
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60711462"
---
# <a name="copy-data-from-and-to-odbc-data-stores-using-azure-data-factory"></a>Kopiowanie danych z i do magazyny danych ODBC za pomocą usługi Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1](v1/data-factory-odbc-connector.md)
> * [Bieżąca wersja](connector-odbc.md)

W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory do kopiowania danych z i do magazynu danych ODBC. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Kopiowanie danych ze źródła ODBC do dowolnego obsługiwanego magazynu danych ujścia lub skopiować z dowolnego obsługiwanego źródłowego magazynu danych ujścia ODBC. Aby uzyskać listę magazynów danych, obsługiwane przez działanie kopiowania jako źródła/ujścia, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności ten łącznik ODBC obsługuje kopiowanie danych do/z **wszystkie magazyny danych zgodne ze standardem ODBC** przy użyciu **podstawowe** lub **anonimowe** uwierzytelniania.

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć tego łącznika ODBC, należy:

- Skonfiguruj środowiskiem Integration Runtime. Zobacz [własne środowisko IR](create-self-hosted-integration-runtime.md) artykuł, aby uzyskać szczegółowe informacje.
- Zainstaluj sterownik ODBC dla magazynu danych na komputerze środowisko Integration Runtime.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które są używane do definiowania jednostek usługi Data Factory określonych łącznik ODBC.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Następujące właściwości są obsługiwane dla ODBC połączone usługi:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi być równa: **Odbc** | Yes |
| connectionString | Parametry połączenia, z wyjątkiem części poświadczeń. Można określić parametry połączenia za pomocą wzorca, takich jak `"Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;"`, lub użyć systemu DSN (nazwa źródła danych), możesz skonfigurować na komputerze środowisko Integration Runtime za pomocą `"DSN=<name of the DSN on IR machine>;"` (muszą nadal określić część poświadczeń w połączonej usłudze odpowiednio).<br>Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md).| Yes |
| authenticationType | Typ uwierzytelniania używany do łączenia się z magazynem danych ODBC.<br/>Dozwolone wartości to: **Podstawowe** i **anonimowe**. | Yes |
| userName | Określ nazwę użytkownika, jeśli używasz uwierzytelniania podstawowego. | Nie |
| password | Określ hasło dla konta użytkownika, która została określona jako nazwy użytkownika. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Nie |
| poświadczenia | Dostęp do poświadczeń część parametrów połączenia określonych w formacie wartości właściwości specyficzne dla sterownika. Przykład: `"RefreshToken=<secret refresh token>;"`. Oznacz to pole jako obiektu SecureString. | Nie |
| connectVia | [Środowiska Integration Runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. Własne środowisko IR jest wymagany, zgodnie z opisem w [wymagania wstępne](#prerequisites). |Yes |

**Przykład 1: przy użyciu uwierzytelniania podstawowego**

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "<connection string>"
            },
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
            "connectionString": {
                "type": "SecureString",
                "value": "<connection string>"
            },
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

Aby uzyskać pełną listę sekcje i właściwości dostępne Definiowanie zestawów danych zobacz artykuł zestawów danych. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych ODBC.

Aby skopiować dane z/do magazynu danych zgodne ze standardem ODBC, należy ustawić właściwość typu zestawu danych na **RelationalTable**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość typu elementu dataset musi być równa: **RelationalTable** | Yes |
| tableName | Nazwa tabeli w magazynie danych ODBC. | Brak źródła (Jeśli określono wartość "query" źródło działania);<br/>Tak w przypadku ujścia |

**Przykład**

```json
{
    "name": "ODBCDataset",
    "properties": {
        "type": "RelationalTable",
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

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło ODBC.

### <a name="odbc-as-source"></a>ODBC jako źródła

Aby skopiować dane z magazynu danych zgodne ze standardem ODBC, należy ustawić typ źródłowego w działaniu kopiowania, aby **RelationalSource**. Następujące właściwości są obsługiwane w działaniu kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi być równa wartości właściwości type źródło działania kopiowania: **RelationalSource** | Yes |
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

### <a name="odbc-as-sink"></a>ODBC jako ujście

Aby skopiować dane do magazynu danych zgodne ze standardem ODBC, należy ustawić typ ujścia w działaniu kopiowania, aby **OdbcSink**. Następujące właściwości są obsługiwane w działaniu kopiowania **ujścia** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi być równa wartości właściwości type ujścia działania kopiowania: **OdbcSink** | Yes |
| writeBatchTimeout |Czas na ukończenie przed upływem limitu czasu operacji wstawiania wsadowego oczekiwania.<br/>Dozwolone wartości to: przedziału czasu. Przykład: "00: 30:00" (30 minut). |Nie |
| writeBatchSize |Wstawia dane do tabeli SQL, gdy writeBatchSize osiągnie rozmiar buforu.<br/>Dozwolone wartości to: liczba całkowita (liczba wierszy). |Nie (wartość domyślna to 0 - wykryte automatycznie) |
| preCopyScript |Określ zapytanie SQL, działanie kopiowania w celu wykonania przed zapisanie danych w magazynie danych w każdym przebiegu. Ta właściwość umożliwia czyszczenie wstępnie załadowanych danych. |Nie |

> [!NOTE]
> "WriteBatchSize" Jeśli nie ustawiono (wykrywane automatycznie), działanie kopiowania najpierw wykrycie sterownik obsługuje operacje wsadowe i ustaw ją na 10000, jeśli tak jest czy ustawiona na 1, w przeciwnym razie. Jeśli jawnie ustawić wartość inną niż 0, działanie kopiowania będzie używana wartość i kończy się niepowodzeniem w czasie wykonywania, jeśli sterownik nie obsługuje operacji wsadowych.

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

## <a name="ibm-informix-source"></a>Źródło programu IBM Informix

Możesz skopiować dane z bazy danych programu IBM Informix za pomocą ogólnego łącznika ODBC.

Skonfiguruj własne środowisko Integration Runtime na komputerze z dostępem do magazynu danych. Środowisko Integration Runtime używa sterownika ODBC dla programu Informix nawiązać połączenia z magazynem danych. W związku z tym należy zainstalować sterownik, jeśli nie jest już zainstalowany na tym samym komputerze. Na przykład można użyć sterownika "Sterownika ODBC programu IBM INFORMIX (64-bitowy)". Zobacz [wymagania wstępne](#prerequisites) sekcji, aby uzyskać szczegółowe informacje.

Przed użyciem źródła Informix w ramach rozwiązania fabryki danych, sprawdź, czy środowiska Integration Runtime może połączyć się magazyn danych, zgodnie z instrukcjami przedstawionymi w [Rozwiązywanie problemów z łącznością](#troubleshoot-connectivity-issues) sekcji.

Tworzenie usługi połączonej ODBC połączyć magazyn danych programu IBM Informix usługi Azure data factory, jak pokazano w poniższym przykładzie:

```json
{
    "name": "InformixLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "<Informix connection string or DSN>"
            },
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

Przeczytaj artykuł od samego początku szczegółowe omówienie przy użyciu danych ODBC są przechowywane jako źródło/ujście danych magazynów w operacji kopiowania.

## <a name="microsoft-access-source"></a>Źródło programu Microsoft Access

Możesz skopiować dane z bazy danych Microsoft Access za pomocą ogólnego łącznika ODBC.

Skonfiguruj własne środowisko Integration Runtime na komputerze z dostępem do magazynu danych. Środowisko Integration Runtime używa sterownika ODBC dla programu Microsoft Access nawiązać połączenia z magazynem danych. W związku z tym należy zainstalować sterownik, jeśli nie jest już zainstalowany na tym samym komputerze. Zobacz [wymagania wstępne](#prerequisites) sekcji, aby uzyskać szczegółowe informacje.

Przed użyciem źródła programu Microsoft Access w ramach rozwiązania fabryki danych, sprawdź, czy środowiska Integration Runtime może połączyć się magazyn danych, zgodnie z instrukcjami przedstawionymi w [Rozwiązywanie problemów z łącznością](#troubleshoot-connectivity-issues) sekcji.

Tworzenie usługi połączonej ODBC połączyć bazę danych Microsoft Access z usługi Azure data factory, jak pokazano w poniższym przykładzie:

```json
{
    "name": "MicrosoftAccessLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Driver={Microsoft Access Driver (*.mdb, *.accdb)};Dbq=<path to your DB file e.g. C:\\mydatabase.accdb>;"
            },
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

Przeczytaj artykuł od samego początku szczegółowe omówienie przy użyciu danych ODBC są przechowywane jako źródło/ujście danych magazynów w operacji kopiowania.

## <a name="sap-hana-sink"></a>Obiekt sink platformy SAP HANA

>[!NOTE]
>Aby skopiować dane z magazynu danych SAP HANA, należy zapoznać się z natywnym [łącznika SAP HANA](connector-sap-hana.md). Aby skopiować dane do platformy SAP HANA, wykonaj tę instrukcję do używania łącznika ODBC. Uwaga, połączone usługi dla łącznika SAP HANA i łącznik ODBC są z różnych typów związku z tym nie można użyć ponownie.
>

Można kopiować dane do bazy danych SAP HANA przy użyciu ogólnego łącznika ODBC.

Skonfiguruj własne środowisko Integration Runtime na komputerze z dostępem do magazynu danych. Środowiska Integration Runtime używa sterownik ODBC platformy SAP Hana do łączenia się z magazynem danych. W związku z tym należy zainstalować sterownik, jeśli nie jest już zainstalowany na tym samym komputerze. Zobacz [wymagania wstępne](#prerequisites) sekcji, aby uzyskać szczegółowe informacje.

Przed użyciem ujścia platformy SAP HANA w ramach rozwiązania fabryki danych, sprawdź, czy środowiska Integration Runtime może połączyć się magazyn danych, zgodnie z instrukcjami przedstawionymi w [Rozwiązywanie problemów z łącznością](#troubleshoot-connectivity-issues) sekcji.

Tworzenie usługi połączonej ODBC połączyć magazyn danych SAP HANA z usługi Azure data factory, jak pokazano w poniższym przykładzie:

```json
{
    "name": "SAPHANAViaODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Driver={HDBODBC};servernode=<HANA server>.clouddatahub-int.net:30015"
            },
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

Przeczytaj artykuł od samego początku szczegółowe omówienie przy użyciu danych ODBC są przechowywane jako źródło/ujście danych magazynów w operacji kopiowania.

## <a name="troubleshoot-connectivity-issues"></a>Rozwiązywanie problemów z łącznością

Aby rozwiązać problemy z połączeniem, należy użyć **diagnostyki** karcie **Menedżera konfiguracji produktu Integration Runtime**.

1. Uruchom **Menedżera konfiguracji produktu Integration Runtime**.
2. Przełącz się do **diagnostyki** kartę.
3. W sekcji "Testuj połączenie" Wybierz **typu** danych przechowywania (usługę połączoną).
4. Określ **parametry połączenia** używany do nawiązania połączenia z magazynem danych, wybierz **uwierzytelniania** i wprowadź **nazwa_użytkownika**, **hasło**, i/lub **poświadczenia**.
5. Kliknij przycisk **Testuj połączenie** Aby przetestować połączenie z magazynem danych.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md##supported-data-stores-and-formats).
