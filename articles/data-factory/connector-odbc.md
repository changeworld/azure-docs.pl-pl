---
title: Kopiowanie danych z ODBC — źródła przy użyciu fabryki danych Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane ze źródeł OData do zbiornika obsługiwanych magazynów danych za pomocą działania kopiowania w potoku fabryki danych Azure.
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
ms.openlocfilehash: f1ab981247191db0abebd67701b07bf48b934292
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34617114"
---
# <a name="copy-data-from-and-to-odbc-data-stores-using-azure-data-factory"></a>Skopiuj dane z i do magazynów danych ODBC przy użyciu fabryki danych Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1 — ogólnie dostępna](v1/data-factory-odbc-connector.md)
> * [Wersja 2 — wersja zapoznawcza](connector-odbc.md)

W tym artykule omówiono sposób użycia działanie kopiowania w fabryce danych Azure, aby skopiować dane z i do magazynu danych ODBC. Opiera się na [skopiuj omówienie działania](copy-activity-overview.md) artykułu, który przedstawia ogólny przegląd działanie kopiowania.

> [!NOTE]
> Ten artykuł dotyczy wersji 2 usługi Data Factory, która jest obecnie dostępna w wersji zapoznawczej. Jeśli używasz wersji 1 usługi fabryka danych, która jest ogólnie dostępna (GA), zobacz [łącznika ODBC w wersji 1](v1/data-factory-odata-connector.md).

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Kopiowanie danych z źródła ODBC do żadnych obsługiwanych ujścia magazynu danych lub skopiować z dowolnego magazynu danych obsługiwanych źródłowych do ujścia ODBC. Lista magazynów danych, które są obsługiwane jako źródła/wychwytywanie przez działanie kopiowania, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności ten łącznik ODBC obsługuje kopiowanie danych z/do **wszystkie magazyny danych zgodne ze standardem ODBC** przy użyciu **podstawowe** lub **anonimowe** uwierzytelniania.

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć tego łącznika ODBC, musisz:

- Konfigurowanie środowiska uruchomieniowego integracji Self-hosted. Zobacz [środowiska uruchomieniowego integracji Self-hosted](create-self-hosted-integration-runtime.md) artykułu, aby uzyskać szczegółowe informacje.
- Instalowanie sterownika ODBC dla magazynu danych na komputerze środowiska uruchomieniowego integracji.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek fabryki danych określonej do łącznika ODBC.

## <a name="linked-service-properties"></a>Połączona usługa właściwości

Obsługiwane są następujące właściwości dla ODBC połączone usługi:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi mieć ustawioną: **Odbc** | Yes |
| Parametry połączenia | Parametry połączenia, z wyjątkiem części poświadczeń. Można określić parametry połączenia ze wzorcem, takich jak `"Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;"`, lub użyj systemową nazwę DSN (nazwa źródła danych), należy skonfigurować na komputerze środowiska uruchomieniowego integracji z `"DSN=<name of the DSN on IR machine>;"` (trzeba nadal określać części poświadczeń w połączonej usłudze odpowiednio).<br>Zaznacz to pole jako SecureString Zapisz w bezpiecznej lokalizacji w fabryce danych lub [odwołania klucz tajny przechowywane w usłudze Azure Key Vault](store-credentials-in-key-vault.md).| Yes |
| Typ authenticationType | Typ uwierzytelniania używany do łączenia się z magazynem danych ODBC.<br/>Dozwolone wartości to: **podstawowe** i **anonimowe**. | Yes |
| userName | Określ nazwę użytkownika, jeśli używasz uwierzytelniania podstawowego. | Nie |
| hasło | Określ hasło dla konta użytkownika, określone nazwy użytkownika. Zaznacz to pole jako SecureString Zapisz w bezpiecznej lokalizacji w fabryce danych lub [odwołania klucz tajny przechowywane w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Nie |
| poświadczenia | Dostęp do poświadczeń część ciągu połączenia określonego w formacie wartości właściwości sterownika. Przykład: `"RefreshToken=<secret refresh token>;"`. Zaznacz to pole jako SecureString. | Nie |
| connectVia | [Integrację środowiska uruchomieniowego](concepts-integration-runtime.md) ma być używany do nawiązania połączenia z magazynem danych. Środowisko uruchomieniowe integracji Self-hosted jest wymagana, jak wspomniano w [wymagania wstępne](#prerequisites). |Yes |

**Przykład 1: użycie uwierzytelniania podstawowego**

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

**Przykład 2: przy użyciu uwierzytelniania anonimowego**

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

Aby uzyskać pełną listę właściwości dostępnych do definiowania zestawów danych i sekcje zobacz artykuł zestawów danych. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych ODBC.

Aby skopiować dane z/do magazynu danych zgodne ze standardem ODBC, ustaw właściwość Typ zestawu danych do **RelationalTable**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi mieć ustawioną właściwość type zestawu danych: **RelationalTable** | Yes |
| tableName | Nazwa tabeli w magazynie danych ODBC. | Brak źródła (Jeśli określono wartość "query" w źródle działania);<br/>Tak dla obiekt sink |

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

Pełną listę sekcje i właściwości dostępnych dla definiowania działań, zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwane przez źródło ODBC.

### <a name="odbc-as-source"></a>ODBC jako źródła

Aby skopiować dane z magazynu danych zgodne ze standardem ODBC, należy ustawić typ źródła w przypadku działania kopiowania do **RelationalSource**. Następujące właściwości są obsługiwane w przypadku działania kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi mieć ustawioną właściwość type źródła działania kopiowania: **RelationalSource** | Yes |
| query | Użyj niestandardowych zapytania SQL można odczytać danych. Na przykład: `"SELECT * FROM MyTable"`. | Nie (Jeśli określono parametr "Nazwa_tabeli" w zestawie danych) |

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

### <a name="odbc-as-sink"></a>ODBC jako odbioru

Aby skopiować dane do magazynu danych zgodne ze standardem ODBC, należy ustawić typ ujścia w działaniu kopiowania do **OdbcSink**. Następujące właściwości są obsługiwane w przypadku działania kopiowania **zbiornika** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi mieć ustawioną właściwość typu sink działania kopiowania: **OdbcSink** | Yes |
| writeBatchTimeout |Czas na ukończenie zanim upłynie limit czasu operacji wstawiania wsadowego oczekiwania.<br/>Dozwolone wartości to: timespan. Przykład: "00: 30:00" (30 minut). |Nie |
| writeBatchSize |Wstawia dane do tabeli SQL, gdy writeBatchSize osiągnie rozmiar buforu.<br/>Dozwolone wartości to: liczba całkowita (liczba wierszy). |Nie (wartość domyślna to 0 – wykrywane automatycznie) |
| preCopyScript |Określ zapytanie SQL dla aktywności kopiowania do wykonania przed zapisaniem danych do magazynu danych w każdym przebiegu. Ta właściwość służy do oczyszczania danych wstępnie załadowane. |Nie |

> [!NOTE]
> "WriteBatchSize" Jeśli nie ustawiono (wykrywane automatycznie), działanie kopiowania najpierw wykrycie sterownik obsługuje operacje partii i ustaw ją na 10000, jeśli tak czy ustawiona na 1, w przeciwnym razie. Jeśli jawnie ustawiona wartość inna niż 0, działanie kopiowania honoruje wartość i kończy się niepowodzeniem w czasie wykonywania, jeśli sterownik nie obsługuje operacji wsadowych.

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

Można skopiować danych z bazą danych IBM Informix za pomocą łącznika ogólnego ODBC.

Konfigurowanie środowiska uruchomieniowego integracji Self-hosted na komputerze z dostępem do magazynu danych. Środowisko uruchomieniowe integracji używa sterownika ODBC dla programu Informix do nawiązania połączenia z magazynem danych. W związku z tym należy zainstalować sterownik, jeśli nie jest już zainstalowany na tym samym komputerze. Na przykład można użyć sterownika "IBM INFORMIX sterownika ODBC (64-bitowy)". Zobacz [wymagania wstępne](#prerequisites) sekcji, aby uzyskać szczegółowe informacje.

Przed użyciem źródła Informix w rozwiązaniu fabryki danych, sprawdź, czy środowiska uruchomieniowego integracji można połączyć z magazynem danych przy użyciu instrukcji w [Rozwiązywanie problemów z łącznością](#troubleshoot-connectivity-issues) sekcji.

Tworzenie usługi ODBC połączone, aby połączyć magazyn danych IBM Informix fabryki danych Azure, jak pokazano w poniższym przykładzie:

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

Przeczytaj artykuł od samego początku, aby uzyskać szczegółowy przegląd przy użyciu danych ODBC są przechowywane jako źródło/ujście magazyny danych w operacji kopiowania.

## <a name="microsoft-access-source"></a>Źródło programu Microsoft Access

Możesz skopiować dane z bazy danych programu Microsoft Access za pomocą łącznika ogólnego ODBC.

Konfigurowanie środowiska uruchomieniowego integracji Self-hosted na komputerze z dostępem do magazynu danych. Środowiska uruchomieniowego integracji używa sterownika ODBC dla programu Microsoft Access do połączenia z magazynem danych. W związku z tym należy zainstalować sterownik, jeśli nie jest już zainstalowany na tym samym komputerze. Zobacz [wymagania wstępne](#prerequisites) sekcji, aby uzyskać szczegółowe informacje.

Przed użyciem źródła programu Microsoft Access w rozwiązaniu fabryki danych, sprawdź, czy środowiska uruchomieniowego integracji można połączyć z magazynem danych przy użyciu instrukcji w [Rozwiązywanie problemów z łącznością](#troubleshoot-connectivity-issues) sekcji.

Tworzenie usługi ODBC połączone, aby połączyć bazy danych programu Microsoft Access z fabryki danych Azure, jak pokazano w poniższym przykładzie:

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

Przeczytaj artykuł od samego początku, aby uzyskać szczegółowy przegląd przy użyciu danych ODBC są przechowywane jako źródło/ujście magazyny danych w operacji kopiowania.

## <a name="ge-historian-source"></a>GE historyk źródła

Dane należy skopiować z historyk GE za pomocą łącznika ogólnego ODBC.

Konfigurowanie środowiska uruchomieniowego integracji Self-hosted na komputerze z dostępem do magazynu danych. Środowisko uruchomieniowe integracji używa sterownika ODBC dla historyk GE do nawiązania połączenia z magazynem danych. W związku z tym należy zainstalować sterownik, jeśli nie jest już zainstalowany na tym samym komputerze. Zobacz [wymagania wstępne](#prerequisites) sekcji, aby uzyskać szczegółowe informacje.

Przed użyciem źródła historyk GE w rozwiązaniu fabryki danych, sprawdź, czy środowiska uruchomieniowego integracji można połączyć z magazynem danych przy użyciu instrukcji w [Rozwiązywanie problemów z łącznością](#troubleshoot-connectivity-issues) sekcji.

Tworzenie usługi ODBC połączone, aby połączyć bazy danych programu Microsoft Access z fabryki danych Azure, jak pokazano w poniższym przykładzie:

```json
{
    "name": "HistorianLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "<GE Historian store connection string or DSN>"
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

Przeczytaj artykuł od samego początku, aby uzyskać szczegółowy przegląd przy użyciu danych ODBC są przechowywane jako źródło/ujście magazyny danych w operacji kopiowania.

## <a name="sap-hana-sink"></a>Obiekt sink SAP HANA

>[!NOTE]
>Aby skopiować dane z magazynu danych SAP HANA, należy zapoznać się z macierzystego [łącznika SAP HANA](connector-sap-hana.md). Aby skopiować dane do programu SAP HANA, wykonaj tej instrukcji, aby użyć łącznika ODBC. Uwaga, są połączone usługi SAP HANA connector i łącznika ODBC z innego typu w związku z tym nie można użyć ponownie.
>

Można skopiować dane do bazy danych SAP HANA za pomocą łącznika ogólnego ODBC.

Konfigurowanie środowiska uruchomieniowego integracji Self-hosted na komputerze z dostępem do magazynu danych. Środowisko uruchomieniowe integracji używa sterownika ODBC dla SAP HANA do nawiązania połączenia z magazynem danych. W związku z tym należy zainstalować sterownik, jeśli nie jest już zainstalowany na tym samym komputerze. Zobacz [wymagania wstępne](#prerequisites) sekcji, aby uzyskać szczegółowe informacje.

Przed użyciem zbiornika SAP HANA w rozwiązaniu fabryki danych, sprawdź, czy środowiska uruchomieniowego integracji może nawiązać magazynu danych przy użyciu instrukcji w [Rozwiązywanie problemów z łącznością](#troubleshoot-connectivity-issues) sekcji.

Tworzenie usługi ODBC połączone, aby połączyć magazyn danych SAP HANA z fabryki danych Azure, jak pokazano w poniższym przykładzie:

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

Przeczytaj artykuł od samego początku, aby uzyskać szczegółowy przegląd przy użyciu danych ODBC są przechowywane jako źródło/ujście magazyny danych w operacji kopiowania.

## <a name="troubleshoot-connectivity-issues"></a>Rozwiązywanie problemów z połączeniem

Aby rozwiązać problemy z połączeniem, użyj **diagnostyki** karcie **integrację środowiska uruchomieniowego Configuration Manager**.

1. Uruchom **Menedżera konfiguracji środowiska uruchomieniowego integracji**.
2. Przełącz się do **diagnostyki** kartę.
3. W sekcji "Test połączenia" Wybierz **typu** danych magazynu (połączonej usługi).
4. Określ **ciąg połączenia** używany do nawiązania połączenia z magazynem danych, wybierz **uwierzytelniania** , a następnie wprowadź **nazwy użytkownika**, **hasło**, i/lub **poświadczenia**.
5. Kliknij przycisk **połączenie testowe** do testowania połączenia z magazynem danych.

## <a name="next-steps"></a>Kolejne kroki
Lista magazynów danych obsługiwane jako źródła i wychwytywanie przez działanie kopiowania w fabryce danych Azure, zobacz [obsługiwane magazyny danych](copy-activity-overview.md##supported-data-stores-and-formats).
