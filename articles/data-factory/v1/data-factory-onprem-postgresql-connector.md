---
title: Przenoszenie danych z postgreSQL przy użyciu usługi Azure Data Factory
description: Dowiedz się, jak przenieść dane z bazy danych PostgreSQL przy użyciu usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 888d9ebc-2500-4071-b6d1-0f6bd1b5997c
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 37c83e77cadae002ff701a08c4b36a86f7cab9a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281238"
---
# <a name="move-data-from-postgresql-using-azure-data-factory"></a>Przenoszenie danych z postgreSQL przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](data-factory-onprem-postgresql-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-postgresql.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [Łącznik PostgreSQL w wersji 2](../connector-postgresql.md).


W tym artykule wyjaśniono, jak używać działania kopiowania w usłudze Azure Data Factory do przenoszenia danych z lokalnej bazy danych PostgreSQL. Opiera się na [działaniach przenoszenia danych,](data-factory-data-movement-activities.md) który przedstawia ogólny przegląd przenoszenia danych z działaniem kopiowania.

Dane można kopiować z lokalnego magazynu danych PostgreSQL do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych obsługiwanych jako pochłaniacze przez działanie kopiowania, zobacz [obsługiwane magazyny danych](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Fabryka danych obsługuje obecnie przenoszenie danych z bazy danych PostgreSQL do innych magazynów danych, ale nie do przenoszenia danych z innych magazynów danych do bazy danych PostgreSQL.

## <a name="prerequisites"></a>Wymagania wstępne

Usługa Data Factory obsługuje łączenie się z lokalnymi źródłami PostgreSQL przy użyciu bramy zarządzania danymi. Zobacz [przenoszenie danych między lokalizacjami lokalnymi a chmurą,](data-factory-move-data-between-onprem-and-cloud.md) aby dowiedzieć się więcej o bramie zarządzania danymi i instrukcjach krok po kroku dotyczących konfigurowania bramy.

Brama jest wymagana, nawet jeśli baza danych PostgreSQL jest hostowana na maszynie wirtualnej usługi Azure IaaS. Bramę można zainstalować na tej samej maszynie wirtualnej IaaS co magazyn danych lub na innej maszynie wirtualnej, o ile brama może łączyć się z bazą danych.

> [!NOTE]
> Aby uzyskać wskazówki dotyczące rozwiązywania problemów związanych z połączeniem/bramą, zobacz [Rozwiązywanie problemów z bramą.](data-factory-data-management-gateway.md#troubleshooting-gateway-issues)

## <a name="supported-versions-and-installation"></a>Obsługiwane wersje i instalacja
Aby brama zarządzania danymi łączyła się z bazą danych PostgreSQL, zainstaluj [dostawcę danych Ngpsql dla postgreSql](https://go.microsoft.com/fwlink/?linkid=282716) w wersji między 2.0.12 a 3.1.9 w tym samym systemie co brama zarządzania danymi. PostgreSQL w wersji 7.4 i powyżej jest obsługiwany.

## <a name="getting-started"></a>Wprowadzenie
Można utworzyć potok z działaniem kopiowania, który przenosi dane z lokalnego magazynu danych PostgreSQL przy użyciu różnych narzędzi/interfejsów API.

- Najprostszym sposobem utworzenia potoku jest użycie **Kreatora kopiowania**. Zobacz [Samouczek: Tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) w celu szybkiego instruktażu w sprawie tworzenia potoku przy użyciu kreatora kopiowania danych.
- Można również użyć następujących narzędzi do utworzenia potoku:
  - Visual Studio
  - Azure PowerShell
  - Szablon usługi Azure Resource Manager
  - Interfejs API .NET
  - Interfejs API REST

    Zobacz [Kopiowanie samouczka aktywności,](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) aby uzyskać instrukcje krok po kroku, aby utworzyć potok z działaniem kopiowania.

Niezależnie od tego, czy są używane narzędzia, czy interfejsy API, wykonaj następujące kroki, aby utworzyć potok, który przenosi dane ze źródłowego magazynu danych do magazynu danych ujścia:

1. Tworzenie **połączonych usług** w celu połączenia magazynów danych wejściowych i wyjściowych z fabryką danych.
2. Tworzenie **zestawów danych** do reprezentowania danych wejściowych i wyjściowych dla operacji kopiowania.
3. Utwórz **potok** z działaniem kopiowania, które przyjmuje zestaw danych jako dane wejściowe i zestaw danych jako dane wyjściowe.

Korzystając z kreatora, definicje JSON dla tych jednostek usługi Data Factory (połączone usługi, zestawy danych i potok) są tworzone automatycznie dla Ciebie. Korzystając z narzędzi/interfejsów API (z wyjątkiem interfejsu API platformy .NET), można zdefiniować te jednostki fabryki danych przy użyciu formatu JSON. W przypadku przykładu z definicjami JSON dla jednostek usługi Data Factory, które są używane do kopiowania danych z lokalnego magazynu danych PostgreSQL, zobacz [przykład JSON: Kopiuj dane z postgreSql do usługi Azure Blob](#json-example-copy-data-from-postgresql-to-azure-blob) sekcji tego artykułu.

Poniższe sekcje zawierają szczegółowe informacje o właściwościach JSON, które są używane do definiowania jednostek usługi Data Factory specyficznych dla magazynu danych PostgreSQL:

## <a name="linked-service-properties"></a>Połączone właściwości usługi
Poniższa tabela zawiera opis elementów JSON specyficznych dla usługi połączonej PostgreSQL.

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| type |Właściwość typu musi być ustawiona na: **OnPremisesPostgreSql** |Tak |
| serwer |Nazwa serwera PostgreSQL. |Tak |
| database |Nazwa bazy danych PostgreSQL. |Tak |
| Schematu |Nazwa schematu w bazie danych. W nazwie schematu rozróżniana jest wielkość liter. |Nie |
| authenticationType |Typ uwierzytelniania używany do łączenia się z bazą danych PostgreSQL. Możliwe wartości to: Anonimowy, Podstawowy i Windows. |Tak |
| nazwa użytkownika |Określ nazwę użytkownika, jeśli używasz uwierzytelniania podstawowego lub uwierzytelniania systemu Windows. |Nie |
| hasło |Określ hasło dla konta użytkownika określonego dla nazwy użytkownika. |Nie |
| nazwa bramy |Nazwa bramy, której usługa Data Factory powinna używać do łączenia się z lokalną bazą danych PostgreSQL. |Tak |

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby uzyskać pełną listę sekcji & właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [Tworzenie zestawów danych.](data-factory-create-datasets.md) Sekcje, takie jak struktura, dostępność i zasady zestawu danych JSON są podobne dla wszystkich typów zestawów danych.

Sekcja typeProperties jest inna dla każdego typu zestawu danych i zawiera informacje o lokalizacji danych w magazynie danych. Sekcja typeProperties dla zestawu danych typu **RelationalTable** (która zawiera zestaw danych PostgreSQL) ma następujące właściwości:

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| tableName |Nazwa tabeli w wystąpieniu bazy danych PostgreSQL, do których odwołuje się usługa. W tableName jest rozróżniana wielkość liter. |Nie (jeśli określono **kwerendę** **relacyjnego źródła)** |

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Aby uzyskać pełną listę sekcji & właściwości dostępne do definiowania działań, zobacz [tworzenie potoków](data-factory-create-pipelines.md) artykułu. Właściwości, takie jak nazwa, opis, tabele danych wejściowych i wyjściowych oraz zasady są dostępne dla wszystkich typów działań.

Mając na uwadze, właściwości dostępne w sekcji typeProperties działania różnią się w zależności od typu działania. W przypadku działania kopiowania różnią się one w zależności od typów źródeł i pochłaniacze.

Gdy źródło jest typu **RelationalSource** (który obejmuje PostgreSQL), następujące właściwości są dostępne w sekcji typeProperties:

| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| query |Użyj kwerendy niestandardowej, aby odczytać dane. |Ciąg zapytania SQL. Na przykład: `"query": "select * from \"MySchema\".\"MyTable\""`. |Nie (jeśli określono **nazwa tabeli** **zestawu danych)** |

> [!NOTE]
> W nazwach schematów i tabel rozróżniana jest wielkość liter. Ująć `""` je w (cudzysłowy podwójne) w kwerendzie.

**Przykład:**

 `"query": "select * from \"MySchema\".\"MyTable\""`

## <a name="json-example-copy-data-from-postgresql-to-azure-blob"></a>Przykład JSON: Kopiowanie danych z postgreSql do obiektu Blob platformy Azure
W tym przykładzie przedstawiono przykładowe definicje JSON, których można użyć do utworzenia potoku przy użyciu [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub programu Azure [PowerShell.](data-factory-copy-activity-tutorial-using-powershell.md) Pokazują one, jak skopiować dane z bazy danych PostgreSQL do usługi Azure Blob Storage. Jednak dane mogą być kopiowane do dowolnego ujścia podane [w tym miejscu](data-factory-data-movement-activities.md#supported-data-stores-and-formats) przy użyciu działania kopiowania w usłudze Azure Data Factory.

> [!IMPORTANT]
> Ten przykład zawiera fragmenty kodu JSON. Nie zawiera instrukcje krok po kroku dotyczące tworzenia fabryki danych. Aby uzyskać instrukcje krok po kroku, zobacz [przenoszenie danych między lokalizacjami lokalnymi a artykułami w chmurze.](data-factory-move-data-between-onprem-and-cloud.md)

Próbka ma następujące jednostki fabryki danych:

1. Powiązana usługa typu [OnPremisesPostgreSql](data-factory-onprem-postgresql-connector.md#linked-service-properties).
2. Połączona usługa typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [RelationalTable](data-factory-onprem-postgresql-connector.md#dataset-properties).
4. Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. [Potok](data-factory-create-pipelines.md) z działaniem kopiowania, który używa [RelationalSource](data-factory-onprem-postgresql-connector.md#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Przykładowy kopiuje dane z wyniku kwerendy w bazie danych PostgreSQL do obiektu blob co godzinę. Właściwości JSON używane w tych próbkach są opisane w sekcjach następujących po próbkach.

W pierwszym kroku skonfiguruj bramę zarządzania danymi. Instrukcje znajdują się w [ruchomych danych między lokalizacjami lokalnymi i](data-factory-move-data-between-onprem-and-cloud.md) w chmurze artykułu.

**Usługa połączona z PostgreSQL:**

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
**Usługa połączona z magazynem obiektów Blob platformy Azure:**

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
**Zestaw danych wejściowych PostgreSQL:**

W przykładzie przyjęto założenie, że utworzono tabelę "MyTable" w PostgreSQL i zawiera kolumnę o nazwie "timestamp" dla danych szeregów czasowych.

Ustawienie `"external": true` informuje usługę Data Factory, że zestaw danych jest zewnętrzny dla fabryki danych i nie jest produkowany przez działanie w fabryce danych.

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

**Zestaw danych wyjściowych obiektów blob platformy Azure:**

Dane są zapisywane w nowym obiekcie blob co godzinę (częstotliwość: godzina, interwał: 1). Ścieżka folderu i nazwa pliku obiektu blob są dynamicznie oceniane na podstawie czasu rozpoczęcia wycinka, który jest przetwarzany. Ścieżka folderu używa części czasu rozpoczęcia z roku, miesiąca, dnia i godziny.

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

**Potok z działaniem kopiowania:**

Potok zawiera działanie kopiowania, który jest skonfigurowany do korzystania z wejściowych i wyjściowych zestawów danych i jest zaplanowane do uruchomienia co godzinę. W definicji JSON potoku typ **źródła** jest ustawiony na **RelationalSource** i typ **ujścia** jest ustawiony na **BlobSink**. Kwerenda SQL określona dla właściwości **kwerendy** wybiera dane z tabeli public.usstates w bazie danych PostgreSQL.

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
## <a name="type-mapping-for-postgresql"></a>Mapowanie typów dla postgresql
Jak wspomniano w [działaniach przenoszenia danych](data-factory-data-movement-activities.md) artykuł Kopiowanie działania wykonuje automatyczne konwersje typów z typów źródłowych do typów ujścia z następującym podejściem 2-etapowym:

1. Konwertowanie z natywnych typów źródeł na typ .NET
2. Konwertowanie z typu .NET na natywny typ ujścia

Podczas przenoszenia danych do postgreSql, następujące mapowania są używane z postgreSql typu do typu .NET.

| Typ bazy danych PostgreSQL | Aliasy PostgresSQL | Typ programu .NET Framework |
| --- | --- | --- |
| abstime | |Datetime (data/godzina) |
| bigint |int8 (int8) |Int64 |
| bigserial |serial8 |Int64 |
| bit [(n)] | |Bajt[], Ciąg |
| bitowy zróżnicowany [ (n) ] |varbit |Bajt[], Ciąg |
| wartość logiczna |bool |Wartość logiczna |
| Pole | |Bajt[], Ciąg |
| bajta | |Bajt[], Ciąg |
| znak [(n)] |znak [(n)] |Ciąg |
| znak zmienny [(n)] |varchar [(n)] |Ciąg |
| Cid | |Ciąg |
| cidr | |Ciąg |
| koło | |Bajt[], Ciąg |
| date | |Datetime (data/godzina) |
| rozmieszczenie dat | |Ciąg |
| podwójna precyzja |spławik8 |Double |
| inet | |Bajt[], Ciąg |
| intarry | |Ciąg |
| int4range (rozmieszczenie int4range) | |Ciąg |
| int8range (własna) | |Ciąg |
| liczba całkowita |int, int4 |Int32 |
| interwał [pola] [(p)] | |Zakres czasu |
| json | |Ciąg |
| jsonb ( jsonb ) | |Bajt[] |
| line | |Bajt[], Ciąg |
| lseg | |Bajt[], Ciąg |
| macaddr | |Bajt[], Ciąg |
| pieniędzy | |Wartość dziesiętna |
| numeryczne [(p, s)] |dziesiętne [(p, s)] |Wartość dziesiętna |
| liczba | |Ciąg |
| Oid | |Int32 |
| ścieżka | |Bajt[], Ciąg |
| pg_lsn | |Int64 |
| Punkt | |Bajt[], Ciąg |
| Wielokąt | |Bajt[], Ciąg |
| rzeczywiste |spławik4 |Single |
| smallint |int2 |Int16 |
| zanikanie |serial2 |Int16 |
| Seryjny |serial4 |Int32 |
| tekst | |Ciąg |

## <a name="map-source-to-sink-columns"></a>Mapowanie źródła do kolumn ujścia
Aby dowiedzieć się więcej o mapowaniu kolumn w źródłowym zestawie danych na kolumny w zestawie danych ujścia, zobacz [Mapowanie kolumn zestawu danych w usłudze Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Powtarzalny odczyt ze źródeł relacyjnych
Podczas kopiowania danych z relacyjnych magazynów danych należy pamiętać o powtarzalności, aby uniknąć niezamierzonych wyników. W usłudze Azure Data Factory można ponownie uruchomić plasterek ręcznie. Można również skonfigurować zasady ponawiania dla zestawu danych, tak aby plasterek został ponownie uruchomny w przypadku wystąpienia błędu. Gdy plasterek jest uruchamiany ponownie w obu przypadkach, należy upewnić się, że te same dane są odczytywane bez względu na to, ile razy jest uruchamiany plasterek. Zobacz [Powtarzalny odczyt ze źródeł relacyjnych](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Wydajność i strojenie
Zobacz [Kopiowanie wydajności działania & dostrajania przewodnika,](data-factory-copy-activity-performance.md) aby dowiedzieć się więcej o kluczowych czynnikach, które wpływają na wydajność przenoszenia danych (Copy Activity) w usłudze Azure Data Factory i na różne sposoby optymalizacji.
