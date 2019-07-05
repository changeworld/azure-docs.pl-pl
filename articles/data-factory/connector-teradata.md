---
title: Kopiowanie danych z programu Teradata przy użyciu usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o Teradata łącznika usługi Data Factory, która umożliwia kopiowanie danych z bazy danych Teradata do magazynów danych obsługiwanych przez usługę Data Factory jako ujścia.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: jingwang
ms.openlocfilehash: 63f28c8b6eaceed12e1f76e9c0c5984e3b63b500
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561420"
---
# <a name="copy-data-from-teradata-using-azure-data-factory"></a>Kopiowanie danych z programu Teradata przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję usługi Data Factory, którego używasz:"]
>
> * [Wersja 1](v1/data-factory-onprem-teradata-connector.md)
> * [Bieżąca wersja](connector-teradata.md)

W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory, aby skopiować dane z bazy danych programu Teradata. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Możesz skopiować dane z bazy danych Teradata, do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych, obsługiwane przez działanie kopiowania jako źródła/ujścia, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności ten łącznik Teradata obsługuje:

- Teradata **wersji 14.10, 15.0, 15.10 16.0, 16.10 i 16.20**.
- Kopiowanie danych przy użyciu **podstawowe** lub **Windows** uwierzytelniania.
- Równoległe kopia ze źródła programu Teradata. Zobacz [równoległe kopiowanie danych z programu Teradata](#parallel-copy-from-teradata) sekcja ze szczegółowymi informacjami.

> [!NOTE]
>
> Usługa Azure Data Factory uaktualnione łącznika Teradata od v3.18 środowiskiem Integration Runtime, która jest upoważniony przez wbudowane sterownik ODBC i udostępnia połączenie elastyczne opcje także osobną kopię out-of-box do poprawienia wydajności. Wszelkie istniejące obciążenie za pomocą poprzedniego łącznika Teradata upoważnionych przez dostawcę danych .NET dla programu Teradata nadal jest obsługiwany jako — jest, chociaż zaleca się użyć nowego przyszłości. Należy pamiętać, że nowa ścieżka wymaga innego zestawu połączone źródło zestawu danych/service/kopiowania. Zobacz sekcję odpowiednie szczegóły konfiguracji.

## <a name="prerequisites"></a>Wymagania wstępne

Twoje Teradata nie jest dostępny publicznie, musisz skonfigurować środowiskiem Integration Runtime. Aby uzyskać więcej informacji na temat środowiska integration runtime, zobacz [własne środowisko IR](create-self-hosted-integration-runtime.md). Infrastruktura Integration Runtime zapewnia wbudowane sterownik Teradata, począwszy od wersji ppkt 3.18, dlatego nie trzeba ręcznie zainstalować dowolnego sterownika. Sterownik wymaga "Visual C++ Redistributable 2012 Update 4" na maszynie własne środowisko IR, Pobierz z [tutaj](https://www.microsoft.com/en-sg/download/details.aspx?id=30679) Jeśli jeszcze nie jest ona zainstalowana.

Własne środowisko IR w wersji niższej niż ppkt 3.18, musisz zainstalować [.NET Data Provider for Teradata](https://go.microsoft.com/fwlink/?LinkId=278886) wersji 14 lub nowszej na komputerze środowisko Integration Runtime. 

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje na temat właściwości, które są używane do definiowania jednostek usługi fabryka danych określonej do łącznika programu Teradata.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Następujące właściwości są obsługiwane dla programu Teradata połączone usługi:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi być równa: **Teradata** | Yes |
| connectionString | Określa informacje wymagane do nawiązania połączenia z wystąpieniem bazy danych programu Teradata. Zapoznaj się z poniższych przykładów.<br/>Można również wprowadzić hasło w usłudze Azure Key Vault i ściągania `password` konfiguracji poza parametry połączenia. Zapoznaj się [Store poświadczeń w usłudze Azure Key Vault](store-credentials-in-key-vault.md) artykułu z bardziej szczegółowymi informacjami. | Tak |
| username | Określ nazwę użytkownika do łączenia z bazą danych programu Teradata. Dotyczy przypadku korzystania z uwierzytelniania Windows. | Nie |
| password | Określ hasło dla konta użytkownika, która została określona jako nazwy użytkownika. Istnieje również możliwość [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). <br>Dotyczy przypadku korzystania z uwierzytelniania Windows lub odwołuje się do hasła w usłudze Key Vault dla uwierzytelniania podstawowego. | Nie |
| connectVia | [Środowiska Integration Runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. Własne środowisko IR jest wymagany, zgodnie z opisem w [wymagania wstępne](#prerequisites). |Tak |

**Przykład: przy użyciu uwierzytelniania podstawowego**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>;Uid=<username>;Pwd=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Przykład: przy użyciu uwierzytelniania Windows**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>",
            "username": "<username>",
            "password": "<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

> [!NOTE]
>
> Jeśli używano Teradata większe połączona usługa przez dostawcę danych .NET dla programu Teradata przy użyciu następujących ładunku jest nadal obsługiwany jako — jest, chociaż zaleca się użyć nowego przyszłości.

**Poprzednie ładunek:**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "<Basic/Windows>",
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

Aby uzyskać pełną listę sekcje i właściwości dostępne Definiowanie zestawów danych zobacz artykuł zestawów danych. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych Teradata.

Aby skopiować dane z programu Teradata, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość typu elementu dataset musi być równa: **TeradataTable** | Tak |
| baza danych | Nazwa bazy danych programu Teradata. | Nie (Jeśli określono parametr "zapytanie" w źródle działania) |
| table | Nazwa tabeli w bazie danych programu Teradata. | Nie (Jeśli określono parametr "zapytanie" w źródle działania) |

**Przykład:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "TeradataTable",
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

> [!NOTE]
>
> Jeśli zestaw danych typu "RelationalTable" była używana zgodnie z poniższymi, nadal jest obsługiwany jako — jest, chociaż zaleca się użyć nowego przyszłości.

**Poprzednie ładunek:**

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

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło Teradata.

### <a name="teradata-as-source"></a>Teradata jako źródło

> [!TIP]
>
> Dowiedz się więcej z [równoległe kopiowanie danych z programu Teradata](#parallel-copy-from-teradata) sekcję dotyczącą sposobu ładowania danych z efektywnie są używane partycjonowanie danych Teradata.

Aby skopiować dane z programu Teradata, następujące właściwości są obsługiwane w działaniu kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi być równa wartości właściwości type źródło działania kopiowania: **TeradataSource** | Yes |
| query | Umożliwia odczytywanie danych niestandardowe zapytania SQL. Na przykład: `"SELECT * FROM MyTable"`.<br>Po włączeniu ładowania podzielonym na partycje, musisz dołączyć odpowiednie parametry wbudowanej partycji w zapytaniu. Zobacz przykłady w [równoległe kopiowanie danych z programu Teradata](#parallel-copy-from-teradata) sekcji. | Nie (Jeśli określono tabeli w zestawie danych) |
| partitionOptions | Określa dane, partycjonowanie opcje używane do ładowania danych z programu Teradata. <br>Zezwalaj na wartości to: **Brak** (ustawienie domyślne), **skrótu** i **DynamicRange**.<br>Po włączeniu opcji partycji (nie "None"), należy również skonfigurować **[ `parallelCopies` ](copy-activity-performance.md#parallel-copy)** ustawienie dla działania kopiowania, np. jako 4, która określa stopień równoległego do współbieżnego ładowania danych z programu Teradata Baza danych. | Nie |
| partitionSettings | Określ grupę ustawień partycjonowanie danych. <br>Są stosowane, gdy opcja partycji nie jest `None`. | Nie |
| partitionColumnName | Określ nazwę kolumny źródłowej **typu Liczba całkowita** który będzie używany przez zakres partycjonowanie związanym z kopiowaniem równoległych. Jeśli nie zostanie określony, klucz podstawowy tabeli będzie automatycznie wykryte i jest używana jako kolumna partycji. <br>Są stosowane, gdy opcja partycji jest `Hash` lub `DynamicRange`. Użycie zapytania można pobrać źródła danych, podłączyć `?AdfHashPartitionCondition` lub `?AdfRangePartitionColumnName` w klauzuli WHERE. Zobacz przykład w [równoległe kopiowanie danych z programu Teradata](#parallel-copy-from-teradata) sekcji. | Nie |
| partitionUpperBound | Maksymalna wartość kolumny partycji w celu skopiowania danych. <br>Są stosowane, gdy opcja partycji jest `DynamicRange`. Użycie zapytania można pobrać źródła danych, podłączyć `?AdfRangePartitionUpbound` w klauzuli WHERE. Zobacz przykład w [równoległe kopiowanie danych z programu Teradata](#parallel-copy-from-teradata) sekcji. | Nie |
| PartitionLowerBound | Minimalna wartość kolumny partycji w celu skopiowania danych. <br>Są stosowane, gdy opcja partycji jest `DynamicRange`. Użycie zapytania można pobrać źródła danych, podłączyć `?AdfRangePartitionLowbound` w klauzuli WHERE. Zobacz przykład w [równoległe kopiowanie danych z programu Teradata](#parallel-copy-from-teradata) sekcji. | Nie |

> [!NOTE]
>
> Jeśli były używane źródła kopii typu "RelationalSource", nadal jest obsługiwany jako — jest, ale nie obsługuje nowe wbudowane Załaduj równolegle z Teradata (opcje partycji). Sugerowane są do użycia z tym nowym przyszłości.

**Przykład: kopiowanie danych przy użyciu podstawowego zapytania bez partycji**

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
                "type": "TeradataSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="parallel-copy-from-teradata"></a>Równoległe kopiowanie danych z programu Teradata

Łącznik programu Teradata fabryki danych zawiera wbudowane danych partycji w celu skopiowania danych Teradata równolegle z doskonałą wydajność. Można znaleźć opcje partycjonowania danych w działaniu kopiowania -> Teradata źródła:

![Opcje partycji](./media/connector-teradata/connector-teradata-partition-options.png)

Po włączeniu kopii podzielonym na partycje, usługi data factory uruchamia zapytania równoległe względem źródła danych Teradata do ładowania danych przez partycje. Równoległe stopień jest skonfigurowana i kontrolowany za pośrednictwem **[ `parallelCopies` ](copy-activity-performance.md#parallel-copy)** ustawienie dla działania kopiowania. Na przykład jeśli ustawisz `parallelCopies` cztery, jednocześnie generuje usługi data factory i uruchomienia cztery kwerend na podstawie określonej partycji opcji i ustawień, każdy fragment podczas pobierania danych z bazy danych programu Teradata.

Sugerowane są umożliwiające równoległych kopii danych, partycjonowanie, szczególnie w przypadku, gdy ładowanie dużej ilości danych z bazy danych programu Teradata. Poniżej przedstawiono sugerowany konfiguracje w różnych scenariuszach:

| Scenariusz                                                     | Sugerowane ustawienia                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Pełne ładowanie z dużych tabel                                   | **Opcja partycji**: Skrót. <br><br/>W czasie wykonywania data Factory automatycznie wykryć kolumny klucza podstawowego, zastosowanie skrótu przed nim i kopiowanie danych przez partycje. |
| Ładowanie dużej ilości danych przy użyciu niestandardowych zapytań                 | **Opcja partycji**: Skrót.<br>**Zapytanie**: `SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>`.<br>**Kolumna partycji**: Określ kolumna używana na potrzeby Zastosuj wyznaczania wartości skrótu partycji. Jeśli nie zostanie określony, ADF automatycznie wykryje kolumny klucza podstawowego tabeli, którą określono w zestawie danych Teradata.<br><br>W czasie wykonywania i Zastąp fabryki danych `?AdfHashPartitionCondition` przy użyciu skrótu partycji logiki i Wyślij do programu Teradata. |
| Ładowanie dużej ilości danych przy użyciu having kolumna liczb całkowitych o wartości równomiernie rozłożony partycjonowania zakresu zapytania niestandardowe | **Opcje partycji**: Partycja zakresu dynamicznego.<br>**Zapytanie**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Kolumna partycji**: Określ kolumny, używany do partycjonowania danych. Można podzielić wobec kolumny z typem danych integer.<br>**Partycja górną granicę** i **partycji dolna granica**: Określ, jeśli chcesz filtrować wobec kolumny partycji w celu pobrania tylko dane między dolny i górny zakres.<br><br>W czasie wykonywania i Zastąp fabryki danych `?AdfRangePartitionColumnName`, `?AdfRangePartitionUpbound`, i `?AdfRangePartitionLowbound` o nazwie rzeczywiste kolumny i wartości zakresów dla każdej partycji i wysyłać do programu Teradata. <br>Na przykład jeśli Twoje kolumny partycji "ID" ustawiony za pomocą dolnej granicy jako 1 a górną granicę jako 80, za pomocą zestawu równoległego kopii jako 4, usługi ADF pobierania danych przez 4 partycje o identyfikatorze między [1,20], [21, 40], [41, 60] i [61, 80]. |

**Przykład: zapytanie o partycji wyznaczania wartości skrótu**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "Hash",
    "partitionSettings": {
        "partitionColumnName": "<hash_partition_column_name>"
    }
}
```

**Przykład: zapytanie z dynamicznego zakresu partycji**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<dynamic_range_partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="data-type-mapping-for-teradata"></a>Typ danych mapowania dla programu Teradata

Podczas kopiowania danych z programu Teradata, następujące mapowania są używane z typów danych Teradata na typy danych tymczasowych usługi Azure Data Factory. Zobacz [schemat i dane mapowanie typu](copy-activity-schema-and-type-mapping.md) Aby poznać sposób działania kopiowania mapowania typ schematu i danych źródła do ujścia.

| Typ danych programu Teradata | Typ danych tymczasowych fabryki danych |
|:--- |:--- |
| BigInt |Int64 |
| Blob |Byte[] |
| Byte |Byte[] |
| ByteInt |Int16 |
| Char |String |
| Clob |String |
| Date |DateTime |
| Decimal |Decimal |
| Double |Double |
| Graphic |Nieobsługiwane. Zastosuj jawnego rzutowania w zapytaniu źródła. |
| Integer |Int32 |
| Interval Day |Nieobsługiwane. Zastosuj jawnego rzutowania w zapytaniu źródła. |
| Interval Day To Hour |Nieobsługiwane. Zastosuj jawnego rzutowania w zapytaniu źródła. |
| Interval Day To Minute |Nieobsługiwane. Zastosuj jawnego rzutowania w zapytaniu źródła. |
| Interval Day To Second |Nieobsługiwane. Zastosuj jawnego rzutowania w zapytaniu źródła. |
| Interval Hour |Nieobsługiwane. Zastosuj jawnego rzutowania w zapytaniu źródła. |
| Interval Hour To Minute |Nieobsługiwane. Zastosuj jawnego rzutowania w zapytaniu źródła. |
| Interval Hour To Second |Nieobsługiwane. Zastosuj jawnego rzutowania w zapytaniu źródła. |
| Interval Minute |Nieobsługiwane. Zastosuj jawnego rzutowania w zapytaniu źródła. |
| Interval Minute To Second |Nieobsługiwane. Zastosuj jawnego rzutowania w zapytaniu źródła. |
| Interval Month |Nieobsługiwane. Zastosuj jawnego rzutowania w zapytaniu źródła. |
| Interval Second |Nieobsługiwane. Zastosuj jawnego rzutowania w zapytaniu źródła. |
| Interval Year |Nieobsługiwane. Zastosuj jawnego rzutowania w zapytaniu źródła. |
| Interval Year To Month |Nieobsługiwane. Zastosuj jawnego rzutowania w zapytaniu źródła. |
| Liczba |Double |
| Okres (Data) |Nieobsługiwane. Zastosuj jawnego rzutowania w zapytaniu źródła. |
| Okres (czas) |Nieobsługiwane. Zastosuj jawnego rzutowania w zapytaniu źródła. |
| Okres (godzina ze strefą czasową) |Nieobsługiwane. Zastosuj jawnego rzutowania w zapytaniu źródła. |
| Okres (sygnatura czasowa) |Nieobsługiwane. Zastosuj jawnego rzutowania w zapytaniu źródła. |
| Okres (sygnatura czasowa ze strefą czasową) |Nieobsługiwane. Zastosuj jawnego rzutowania w zapytaniu źródła. |
| SmallInt |Int16 |
| Time |TimeSpan |
| Time With Time Zone |TimeSpan |
| Timestamp |DateTime |
| Timestamp With Time Zone |DateTime |
| VarByte |Byte[] |
| VarChar |String |
| VarGraphic |Nieobsługiwane. Zastosuj jawnego rzutowania w zapytaniu źródła. |
| Xml |Nieobsługiwane. Zastosuj jawnego rzutowania w zapytaniu źródła. |


## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
