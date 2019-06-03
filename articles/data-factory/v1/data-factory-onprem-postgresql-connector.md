---
title: Przenoszenie danych z PostgreSQL za pomocą usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o sposobach przenoszenia danych z bazy danych PostgreSQL za pomocą usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 888d9ebc-2500-4071-b6d1-0f6bd1b5997c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: bd39b0aae5b76f37e2153f8e4c4502be994fa5b5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61462007"
---
# <a name="move-data-from-postgresql-using-azure-data-factory"></a>Przenoszenie danych z PostgreSQL za pomocą usługi Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1](data-factory-onprem-postgresql-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-postgresql.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącą wersję usługi Data Factory, zobacz [łącznika PostgreSQL w wersji 2](../connector-postgresql.md).


W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory, aby przenieść dane z lokalnej bazy danych postgresql w warstwie. Opiera się na [działania przenoszenia danych](data-factory-data-movement-activities.md) artykułu, który przedstawia ogólne omówienie przenoszenie danych za pomocą działania kopiowania.

Można skopiować danych z magazynu danych PostgreSQL w środowisku lokalnym, do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych obsługiwanych jako ujścia działania kopiowania, zobacz [obsługiwane magazyny danych](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Data factory obsługuje obecnie przenosi dane z bazy danych PostgreSQL do innych magazynów danych, ale nie przenosi dane z innych magazynów danych do bazy danych PostgreSQL.

## <a name="prerequisites"></a>Wymagania wstępne

Usługa Data Factory obsługuje łączenie z lokalnymi źródłami PostgreSQL przy użyciu bramy zarządzania danymi. Zobacz [przenoszenia danych między lokalizacjami lokalnymi i chmurą](data-factory-move-data-between-onprem-and-cloud.md) artykuł, aby dowiedzieć się więcej na temat bramy zarządzania danymi i instrukcje krok po kroku dotyczące konfigurowania bramy.

Brama jest wymagana, nawet wtedy, gdy baza danych postgresql w warstwie znajduje się w maszynie Wirtualnej IaaS platformy Azure. Można zainstalować bramy w tej samej maszyny Wirtualnej IaaS jako magazyn danych lub w innej maszyny Wirtualnej, tak długo, jak bramy można połączyć z bazą danych.

> [!NOTE]
> Zobacz [problemów bramy](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) porady dotyczące rozwiązywania problemów z bramy połączenia/problemy związane z usługą.

## <a name="supported-versions-and-installation"></a>Obsługiwane wersje i instalacji
Brama zarządzania danymi nawiązać połączenie z bazą danych PostgreSQL, można zainstalować [Ngpsql dostawca danych dla PostgreSQL](https://go.microsoft.com/fwlink/?linkid=282716) przy użyciu wersji między 2.0.12 i 3.1.9 na tym samym systemie co brama zarządzania danymi. PostgreSQL w wersji 7.4 i nowszej jest obsługiwana.

## <a name="getting-started"></a>Wprowadzenie
Utworzysz potok z działaniem kopiowania, które przenosi dane z lokalnego magazynu danych PostgreSQL za pomocą różnych narzędzi/interfejsów API.

- Najprostszym sposobem utworzenia potoku jest użycie **kreatora kopiowania**. Zobacz [samouczka: Tworzenie potoku przy użyciu Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) szybki przewodnik dotyczący tworzenia potoku za pomocą Kreatora kopiowania danych.
- Aby utworzyć potok umożliwia także następujących narzędzi:
  - Azure Portal
  - Visual Studio
  - Azure PowerShell
  - Szablon usługi Azure Resource Manager
  - Interfejs API .NET
  - Interfejs API REST

    Zobacz [samouczka działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) instrukcje krok po kroku utworzyć potok z działaniem kopiowania.

Czy używasz narzędzi lub interfejsów API, należy wykonać poniższe kroki, aby utworzyć potok, który przenosi dane z magazynu danych źródłowych do magazynu danych ujścia:

1. Tworzenie **połączonych usług** połączyć dane wejściowe i wyjściowe przechowywane z fabryką danych.
2. Tworzenie **zestawów danych** do reprezentowania dane wejściowe i wyjściowe operacji kopiowania.
3. Tworzenie **potoku** za pomocą działania kopiowania, która przyjmuje jako dane wejściowe zestawu danych i zestaw danych jako dane wyjściowe.

Korzystając z kreatora, definicje JSON dotyczące tych jednostek usługi Data Factory (połączone usługi, zestawy danych i potok) są tworzone automatycznie dla Ciebie. Korzystając z narzędzi/interfejsów API (z wyjątkiem interfejsu API platformy .NET), należy zdefiniować te jednostki usługi Data Factory przy użyciu formatu JSON. Przykładowe definicje JSON dotyczące jednostek usługi Data Factory, które są używane do kopiowania danych z lokalnego magazynu danych postgresql w warstwie, możesz znaleźć [przykład kodu JSON: Kopiowanie danych z PostgreSQL do usługi Azure Blob](#json-example-copy-data-from-postgresql-to-azure-blob) dalszej części tego artykułu.

Poniższe sekcje zawierają szczegółowe informacje o właściwościach JSON, które są używane do definiowania jednostek usługi fabryka danych określonego w magazynie danych PostgreSQL:

## <a name="linked-service-properties"></a>Właściwości usługi połączonej
Poniższa tabela zawiera opis specyficzne dla usługi PostgreSQL, połączone elementy JSON.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| type |Właściwość type musi być równa: **OnPremisesPostgreSql** |Yes |
| server |Nazwa serwera PostgreSQL. |Yes |
| database |Nazwa bazy danych PostgreSQL. |Yes |
| schema |Nazwa schematu w bazie danych. Nazwa schematu jest uwzględniana wielkość liter. |Nie |
| authenticationType |Typ uwierzytelniania używany do łączenia z bazą danych PostgreSQL. Możliwe wartości: Anonimowe, podstawowe i Windows. |Yes |
| username |Określ nazwę użytkownika, jeśli używasz uwierzytelniania podstawowe lub Windows. |Nie |
| password |Określ hasło dla konta użytkownika, która została określona jako nazwy użytkownika. |Nie |
| gatewayName |Nazwa bramy, do którego usługa Data Factory powinna używać do łączenia z bazą danych postgresql w warstwie lokalnej. |Yes |

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby uzyskać pełną listę sekcje & właściwości dostępne Definiowanie zestawów danych, zobacz [tworzenie zestawów danych](data-factory-create-datasets.md) artykułu. Sekcje, takie jak struktury, dostępność i zasady zestawem danych JSON są podobne dla wszystkich typów w zestawie danych.

W sekcji typeProperties różni się dla każdego typu zestawu danych i zawiera informacje o lokalizacji danych w magazynie danych. Zestaw danych o typie sekcji typeProperties **RelationalTable** (w tym zestawie danych postgresql w warstwie) ma następujące właściwości:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| tableName |Nazwa tabeli w wystąpieniu bazy danych PostgreSQL, który połączona usługa przywołuje. Właściwość tableName jest uwzględniana wielkość liter. |Nie (Jeśli **zapytania** z **RelationalSource** zostanie określona) |

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Aby uzyskać pełną listę sekcje & właściwości dostępne do definiowania działań, zobacz [tworzenie potoków](data-factory-create-pipelines.md) artykułu. Właściwości, takie jak nazwa, opis, dane wejściowe i wyjściowe tabel i zasady są dostępne dla wszystkich typów działań.

Natomiast właściwości, które są dostępne w sekcji typeProperties działania zależą od każdego typu działania. Działanie kopiowania ich różnią się w zależności od typów źródła i ujścia.

Jeśli źródło jest typu **RelationalSource** (w tym PostgreSQL), w sekcji typeProperties dostępne są następujące właściwości:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| query |Użyj zapytania niestandardowe można odczytać danych. |Ciąg zapytania SQL. Na przykład: `"query": "select * from \"MySchema\".\"MyTable\""`. |Nie (Jeśli **tableName** z **dataset** zostanie określona) |

> [!NOTE]
> Nazwy schematu i tabeli jest rozróżniana wielkość liter. Ujmij ją w `""` (podwójne cudzysłowy) w zapytaniu.

**Przykład:**

 `"query": "select * from \"MySchema\".\"MyTable\""`

## <a name="json-example-copy-data-from-postgresql-to-azure-blob"></a>Przykład kodu JSON: Kopiowanie danych z PostgreSQL do obiektów Blob platformy Azure
W poniższym przykładzie przedstawiono przykładowe definicji JSON, które umożliwiają tworzenie potoku za pomocą [witryny Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) lub [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub [programu Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Pokazują, jak skopiować dane z bazy danych PostgreSQL w usłudze Azure Blob Storage. Jednakże, można skopiować danych do dowolnego ujścia, o których wspomniano [tutaj](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania w usłudze Azure Data Factory.

> [!IMPORTANT]
> W tym przykładzie przedstawiono fragmenty kodu JSON. Nie obejmuje instrukcje krok po kroku dotyczące tworzenia fabryki danych. Zobacz [przenoszenia danych między lokalizacjami lokalnymi i chmurą](data-factory-move-data-between-onprem-and-cloud.md) artykuł, aby uzyskać instrukcje krok po kroku.

Przykład obejmuje następujących jednostek fabryki danych:

1. Połączonej usługi typu [OnPremisesPostgreSql](data-factory-onprem-postgresql-connector.md#linked-service-properties).
2. Połączonej usługi typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Dane wejściowe [dataset](data-factory-create-datasets.md) typu [RelationalTable](data-factory-onprem-postgresql-connector.md#dataset-properties).
4. Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. [Potoku](data-factory-create-pipelines.md) za pomocą działania kopiowania, która używa [RelationalSource](data-factory-onprem-postgresql-connector.md#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Przykład kopiuje dane z wyniku kwerendy w bazie danych postgresql w warstwie do obiektu blob na godzinę. Właściwości JSON używanych w tych przykładach są opisane w sekcjach poniżej przykładów.

Pierwszym krokiem należy skonfigurować bramę zarządzania danymi. Instrukcje znajdują się w [przenoszenia danych między lokalizacjami lokalnymi i chmurą](data-factory-move-data-between-onprem-and-cloud.md) artykułu.

**PostgreSQL połączoną usługę:**

```json
{
    "name": "OnPremPostgreSqlLinkedService",
    "properties": {
        "type": "OnPremisesPostgreSql",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```
**Połączona usługa Azure Blob storage:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
        }
    }
}
```
**Wejściowy zestaw danych PostgreSQL:**

Przykład przyjęto założenie, utworzono tabelę "MyTable" w PostgreSQL i zawiera kolumnę o nazwie "timestamp" w danych szeregów czasowych.

Ustawienie `"external": true` usługi Data Factory informuje, że zestaw danych jest zewnętrzne w usłudze data factory i nie jest generowany przez działanie w usłudze data factory.

```json
{
    "name": "PostgreSqlDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremPostgreSqlLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

**Usługa Azure Blob wyjściowy zestaw danych:**

Dane są zapisywane do nowego obiektu blob, co godzinę (frequency: godzina, interwał: 1). Folder ścieżkę i nazwę dla obiektu blob są dynamicznie obliczana na podstawie czasu rozpoczęcia wycinek, który jest przetwarzany. Ścieżka folderu używa rok, miesiąc, dzień i części godzin od zaplanowanej godziny rozpoczęcia.

```json
{
    "name": "AzureBlobPostgreSqlDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/postgresql/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Potok za pomocą działania kopiowania:**

Potoku zawierającego działanie kopiowania jest skonfigurowany do korzystania z danych wejściowych i wyjściowych zestawów danych, która jest zaplanowane do uruchomienia co godzinę. W definicji JSON potok **źródła** ustawiono typ **RelationalSource** i **ujścia** ustawiono typ **BlobSink**. Zapytanie SQL, określony dla **zapytania** właściwość wybiera dane z tabeli public.usstates w bazie danych programu PostgreSQL.

```json
{
    "name": "CopyPostgreSqlToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "select * from \"public\".\"usstates\""
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "inputs": [
                    {
                        "name": "PostgreSqlDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobPostgreSqlDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "PostgreSqlToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```
## <a name="type-mapping-for-postgresql"></a>Mapowanie typu dla PostgreSQL
Jak wspomniano w [działania przenoszenia danych](data-factory-data-movement-activities.md) artykułu działanie kopiowania wykonuje automatyczne konwersje z typów źródła do ujścia typów przy użyciu następujących podejść krok 2:

1. Konwersji z typów natywnych źródła na typ architektury .NET
2. Przekonwertowanie z platformy .NET na typ ujścia natywne

Podczas przenoszenia danych postgresql w warstwie, następujące mapowania są używane z PostgreSQL typu na typ architektury .NET.

| Typ bazy danych PostgreSQL | Aliasy PostgresSQL | Typ .NET framework |
| --- | --- | --- |
| abstime | |DateTime |
| bigint |int8 |Int64 |
| bigserial |serial8 |Int64 |
| bitowe [(n)] | |Byte [] ciąg |
| bit zróżnicowanie [(n)] |varbit |Byte [] ciąg |
| wartość logiczna |bool |Boolean |
| box | |Byte [] ciąg |
| bytea | |Byte [] ciąg |
| znak [(n)] |char [(n)] |String |
| znak zróżnicowanie [(n)] |varchar [(n)] |String |
| identyfikator CID | |String |
| format cidr | |String |
| Okrąg | |Byte [] ciąg |
| date | |DateTime |
| DateRange | |String |
| podwójnej precyzji |FLOAT8 |Double |
| inet | |Byte [] ciąg |
| intarry | |String |
| int4range | |String |
| int8range | |String |
| liczba całkowita |int, int4 |Int32 |
| Interwał [polami] [(p)] | |Zakres czasu |
| json | |String |
| jsonb | |Byte[] |
| wiersz | |Byte [] ciąg |
| lseg | |Byte [] ciąg |
| macaddr | |Byte [] ciąg |
| money | |Decimal |
| numeryczne [(p, s)] |decimal [(p, s)] |Decimal |
| numrange | |String |
| Identyfikator OID | |Int32 |
| ścieżka | |Byte [] ciąg |
| pg_lsn | |Int64 |
| punkt | |Byte [] ciąg |
| Wielokąt | |Byte [] ciąg |
| real |FLOAT4 |Single |
| smallint |int2 |Int16 |
| smallserial |serial2 |Int16 |
| numer seryjny |serial4 |Int32 |
| tekst | |String |

## <a name="map-source-to-sink-columns"></a>Mapy źródła do ujścia kolumn
Aby uzyskać informacje dotyczące mapowania kolumn w zestaw danych źródłowych do kolumn w zestawie danych ujścia, zobacz [mapowanie kolumny zestawu danych w usłudze Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Odczyt powtarzalny ze źródeł relacyjnych
Podczas kopiowania danych z relacyjnej bazie danych są przechowywane, Zachowaj powtarzalności należy pamiętać, aby uniknąć niezamierzonego wyników. W usłudze Azure Data Factory możesz ponownie uruchomić wycinek ręcznie. Można również skonfigurować zasady ponawiania dla zestawu danych, dzięki czemu wycinek będzie uruchamiana ponownie, gdy wystąpi błąd. Gdy wycinek będzie uruchamiana ponownie w obu przypadkach, należy się upewnić, że te same dane jest do odczytu niezależnie od tego, ile razy wycinek jest uruchamiany. Zobacz [Repeatable odczytywać źródła relacyjnego](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Wydajność i dostrajanie
Zobacz [wydajności działania kopiowania & przewodnika dostrajania](data-factory-copy-activity-performance.md) Aby dowiedzieć się więcej o kluczowych czynników tego obniżenie wydajności przenoszenia danych (działanie kopiowania) w usłudze Azure Data Factory i różne sposoby, aby zoptymalizować ją.
