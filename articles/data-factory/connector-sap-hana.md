---
title: Kopiuj dane z SAP HANA
description: Informacje o kopiowaniu danych z SAP HANA do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w potoku Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/17/2020
ms.openlocfilehash: fa165c21622110bb18476efdebf3264a11e26ad7
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79125994"
---
# <a name="copy-data-from-sap-hana-using-azure-data-factory"></a>Kopiowanie danych z SAP HANA przy użyciu Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-sap-hana-connector.md)
> * [Bieżąca wersja](connector-sap-hana.md)

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych z bazy danych SAP HANA. Jest ona oparta na [przeglądzie działania kopiowania](copy-activity-overview.md) , która przedstawia ogólne omówienie działania kopiowania.

>[!TIP]
>Aby poznać ogólną pomoc techniczną w scenariuszu integracji danych w systemie SAP, zobacz [integracja danych SAP przy użyciu Azure Data Factory oficjalny dokument](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) z szczegółowym wprowadzeniem, comparsion i wskazówkami.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Ten łącznik SAP HANA jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)

Dane z bazy danych programu SAP HANA można kopiować do dowolnego obsługiwanego magazynu danych ujścia. Listę magazynów danych obsługiwanych jako źródła/ujścia przez działanie kopiowania można znaleźć w tabeli [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) .

W SAP HANA ten łącznik obsługuje:

- Kopiowanie danych z dowolnej wersji SAP HANA Database.
- Kopiowanie danych z **modeli informacji platformy Hana** (takich jak widoki analityczne i obliczeniowe) oraz **tabele wierszy/kolumn**.
- Kopiowanie danych przy użyciu uwierzytelniania **podstawowego** lub **systemu Windows** .
- Równoległe kopiowanie ze źródła SAP HANA. Aby uzyskać szczegółowe informacje, zobacz sekcję [Kopiowanie równoległe z SAP HANA](#parallel-copy-from-sap-hana) .

> [!TIP]
> Aby skopiować dane **do** SAP HANA magazynu danych, użyj ogólnego łącznika ODBC. Aby uzyskać szczegółowe informacje, zobacz [SAP HANA sink](connector-odbc.md#sap-hana-sink) . Zwróć uwagę, że połączone usługi dla łącznika SAP HANA i łącznika ODBC są z różnymi rodzajami, więc nie mogą być ponownie używane.

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć tego łącznika SAP HANA, musisz:

- Skonfiguruj samodzielny Integration Runtime. Aby uzyskać szczegółowe informacje, zobacz artykuł [Integration Runtime samodzielny](create-self-hosted-integration-runtime.md) .
- Zainstaluj SAP HANA sterownika ODBC na maszynie Integration Runtime. Sterownik SAP HANA ODBC można pobrać z [Centrum pobierania oprogramowania SAP](https://support.sap.com/swdc). Wyszukaj za pomocą słowa kluczowego **SAP HANA Client dla systemu Windows**.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania Data Factory jednostek specyficznych dla łącznika SAP HANA.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Następujące właściwości są obsługiwane dla SAP HANA połączonej usługi:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość Type musi mieć wartość: **SapHana** | Yes |
| connectionString | Określ informacje, które są konieczne do nawiązania połączenia z SAP HANA przy użyciu **uwierzytelniania podstawowego** lub **uwierzytelniania systemu Windows**. Zapoznaj się z poniższymi przykładami.<br>W parametrach połączenia wymagany jest serwer/port (port domyślny to 30015), a nazwa użytkownika i hasło są wymagane w przypadku uwierzytelniania podstawowego. Aby uzyskać dodatkowe ustawienia zaawansowane, zapoznaj się z tematem [SAP HANA właściwości połączenia ODBC](<https://help.sap.com/viewer/0eec0d68141541d1b07893a39944924e/2.0.02/en-US/7cab593774474f2f8db335710b2f5c50.html>)<br/>Możesz również wprowadzić hasło w Azure Key Vault i ściągnąć konfigurację hasła z parametrów połączenia. Aby uzyskać więcej informacji, zobacz artykuł [przechowywanie poświadczeń w Azure Key Vault](store-credentials-in-key-vault.md) artykule. | Yes |
| userName | Określ nazwę użytkownika w przypadku korzystania z uwierzytelniania systemu Windows. Przykład: `user@domain.com` | Nie |
| hasło | Określ hasło dla konta użytkownika. Oznacz to pole jako element SecureString, aby bezpiecznie przechowywać go w Data Factory, lub [odwoływać się do wpisu tajnego przechowywanego w Azure Key Vault](store-credentials-in-key-vault.md). | Nie |
| connectVia | [Integration Runtime](concepts-integration-runtime.md) używany do nawiązywania połączenia z magazynem danych. Samodzielna Integration Runtime jest wymagana, jak wspomniano w [wymaganiach wstępnych](#prerequisites). |Yes |

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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [zestawy danych](concepts-datasets-linked-services.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych SAP HANA.

Aby skopiować dane z SAP HANA, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość Type zestawu danych musi być ustawiona na wartość: **SapHanaTable** | Yes |
| schema | Nazwa schematu w bazie danych SAP HANA. | Nie (Jeśli określono parametr "query" w źródle działania) |
| table | Nazwa tabeli w bazie danych SAP HANA. | Nie (Jeśli określono parametr "query" w źródle działania) |

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

Jeśli używasz zestawu danych `RelationalTable` z określonym typem, nadal jest on obsługiwany w przypadku, gdy zamierzasz korzystać z nowego.

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz artykuł [potoki](concepts-pipelines-activities.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez SAP HANA źródło.

### <a name="sap-hana-as-source"></a>SAP HANA jako źródło

>[!TIP]
>Aby efektywnie pozyskiwać dane z SAP HANA przy użyciu partycjonowania danych, Dowiedz się więcej z sekcji [SAP HANA Copy (kopiowanie równoległe](#parallel-copy-from-sap-hana) ).

Aby skopiować dane z SAP HANA, w sekcji **Źródło** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość Type źródła działania Copy musi być ustawiona na wartość: **SapHanaSource** | Yes |
| query | Określa zapytanie SQL do odczytu danych z wystąpienia SAP HANA. | Yes |
| partitionOptions | Określa opcje partycjonowania danych, które są używane do pozyskiwania danych z SAP HANA. Dowiedz się więcej z sekcji [SAP HANA Copy (kopiowanie równoległe](#parallel-copy-from-sap-hana) ).<br>Zezwalaj na wartości: **None** (wartość domyślna), **PhysicalPartitionsOfTable**, **SapHanaDynamicRange**. Dowiedz się więcej z sekcji [SAP HANA Copy (kopiowanie równoległe](#parallel-copy-from-sap-hana) ). `PhysicalPartitionsOfTable` można używać tylko podczas kopiowania danych z tabeli, ale nie zapytania. <br>Gdy opcja partycji jest włączona (to nie `None`), stopień równoległości do współbieżnego ładowania danych z SAP HANA jest kontrolowany przez ustawienie [`parallelCopies`](copy-activity-performance.md#parallel-copy) dla działania kopiowania. | False |
| partitionSettings | Określ grupę ustawień partycjonowania danych.<br>Zastosuj, gdy opcja partycji jest `SapHanaDynamicRange`. | False |
| partitionColumnName | Określ nazwę kolumny źródłowej, która będzie używana przez partycję do kopiowania równoległego. Jeśli nie zostanie określony, indeks lub klucz podstawowy tabeli są wykrywane i używane jako kolumny partycji.<br>Zastosuj, gdy opcja partycji jest `SapHanaDynamicRange`. Jeśli używasz zapytania do pobierania danych źródłowych, hak `?AdfHanaDynamicRangePartitionCondition` w klauzuli WHERE. Zobacz przykład w sekcji [Kopiowanie równoległe z SAP HANA](#parallel-copy-from-sap-hana) . | Tak, gdy jest używana partycja `SapHanaDynamicRange`. |
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

Jeśli korzystasz ze źródła kopii typu `RelationalSource`, jest ono nadal obsługiwane w stanie takim, w jakim będziesz mieć możliwość korzystania z nowej usługi.

## <a name="parallel-copy-from-sap-hana"></a>Kopiowanie równoległe z SAP HANA

Łącznik Data Factory SAP HANA zapewnia wbudowane Partycjonowanie danych do kopiowania danych ze SAP HANA równolegle. Opcje partycjonowania danych można znaleźć w tabeli **źródłowej** działania kopiowania.

![Zrzut ekranu przedstawiający opcje partycji](./media/connector-sap-hana/connector-sap-hana-partition-options.png)

Po włączeniu kopiowania partycjonowanego Data Factory uruchamia zapytania równoległe względem źródła SAP HANA w celu pobrania danych przez partycje. Stopień równoległy jest kontrolowany przez [`parallelCopies`](copy-activity-performance.md#parallel-copy) ustawienia dla działania kopiowania. Na przykład jeśli ustawisz `parallelCopies` na cztery, Data Factory jednocześnie generuje i uruchamia cztery zapytania w oparciu o określoną opcję partycji i ustawienia, a każde zapytanie pobiera część danych z SAP HANA.

Zalecane jest włączenie kopiowania równoległego przy użyciu partycjonowania danych, szczególnie w przypadku pozyskiwania dużej ilości danych z SAP HANA. Poniżej przedstawiono sugerowane konfiguracje dla różnych scenariuszy. Podczas kopiowania danych do magazynu danych opartego na plikach zaleca się zapisanie do folderu jako wielu plików (określenie tylko nazwy folderu), w którym to przypadku wydajność jest lepsza niż zapis do pojedynczego pliku.

| Scenariusz                                           | Sugerowane ustawienia                                           |
| -------------------------------------------------- | ------------------------------------------------------------ |
| Pełne ładowanie z dużej tabeli.                        | **Opcja partycji**: partycje fizyczne tabeli. <br><br/>Podczas wykonywania Data Factory automatycznie wykrywa typ partycji fizycznej określonej tabeli SAP HANA i wybiera odpowiednią strategię partycji:<br>**partycjonowanie zakresu**- : Pobierz kolumny partycji i zakresy partycji zdefiniowane dla tabeli, a następnie skopiuj dane według zakresu. <br>- **partycjonowanie skrótów**: Użyj klucza partycji skrótu jako kolumny partycji, a następnie Podziel dane na partycje i skopiuj je na podstawie zakresów obliczeniowych ADF. <br>- **partycjonowanie okrężne** lub **Brak partycji**: Użyj klucza podstawowego jako kolumny partycji, a następnie Podziel dane na partycje i skopiuj je w oparciu o zakresy obliczeniowe ADF. |
| Załaduj dużą ilość danych przy użyciu kwerendy niestandardowej. | **Opcja partycji**: dynamiczna partycja zakresu.<br>**Zapytanie**: `SELECT * FROM <TABLENAME> WHERE ?AdfHanaDynamicRangePartitionCondition AND <your_additional_where_clause>`.<br>**Kolumna partycji**: określ kolumnę używaną do stosowania dynamicznej partycji zakresów. <br><br>Podczas wykonywania, Data Factory po raz pierwsze oblicza zakresy wartości określonej kolumny partycji, przez równomierne rozłożenie wierszy w wielu zasobnikach zgodnie z liczbą unikatowych wartości kolumn partycji i ustawieniem kopiowania współbieżności ADF, a następnie zastępuje `?AdfHanaDynamicRangePartitionCondition` filtrowaniem zakres wartości kolumny partycji dla każdej partycji i wysyła do SAP HANA.<br><br>Jeśli chcesz użyć wielu kolumn jako kolumny partycji, możesz połączyć wartości każdej kolumny jako jedną kolumnę w zapytaniu i określić ją jako kolumnę partycji w podajniku APD, na przykład `SELECT * FROM (SELECT *, CONCAT(<KeyColumn1>, <KeyColumn2>) AS PARTITIONCOLUMN FROM <TABLENAME>) WHERE ?AdfHanaDynamicRangePartitionCondition`. |

**Przykład: zapytanie z partycjami fizycznymi tabeli**

```json
"source": {
    "type": "SapHanaSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**Przykład: zapytanie z dynamiczną partycją zakresu**

```json
"source": {
    "type": "SapHanaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfHanaDynamicRangePartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "SapHanaDynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<Partition_column_name>"
    }
}
```

## <a name="data-type-mapping-for-sap-hana"></a>Mapowanie typu danych dla SAP HANA

Podczas kopiowania danych z SAP HANA następujące mapowania są używane z SAP HANA typów danych do Azure Data Factory danych pośrednich. Zobacz [Mapowanie schematu i typu danych](copy-activity-schema-and-type-mapping.md) , aby dowiedzieć się, jak działanie kopiowania mapuje schemat źródłowy i typ danych do ujścia.

| Typ danych SAP HANA | Typ danych tymczasowych fabryki danych |
| ------------------ | ------------------------------ |
| ALPHANUM           | Ciąg                         |
| BIGINT             | Int64                          |
| BINARY             | Byte[]                         |
| BINTEXT            | Ciąg                         |
| BLOB               | Byte[]                         |
| BOOL               | Bajtów                           |
| CLOB               | Ciąg                         |
| DATE               | DateTime                       |
| DECIMAL            | Dziesiętna                        |
| DOUBLE             | Podwójne                         |
| FLOAT              | Podwójne                         |
| INTEGER            | Int32                          |
| NCLOB              | Ciąg                         |
| NVARCHAR           | Ciąg                         |
| REAL               | Single                         |
| SECONDDATE         | DateTime                       |
| SHORTTEXT          | Ciąg                         |
| SMALLDECIMAL       | Dziesiętna                        |
| SMALLINT           | Int16                          |
| STGEOMETRYTYPE     | Byte[]                         |
| STPOINTTYPE        | Byte[]                         |
| TEXT               | Ciąg                         |
| CZAS               | TimeSpan                       |
| TINYINT            | Bajtów                           |
| VARCHAR            | Ciąg                         |
| TIMESTAMP          | DateTime                       |
| VARBINARY          | Byte[]                         |

## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).

## <a name="next-steps"></a>Następne kroki
Listę magazynów danych obsługiwanych jako źródła i ujścia przez działanie kopiowania w Azure Data Factory można znaleźć w temacie [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
