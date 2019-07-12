---
title: Przenoszenie danych z MySQL za pomocą usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o sposobach przenoszenia danych z bazy danych MySQL przy użyciu usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 452f4fce-9eb5-40a0-92f8-1e98691bea4c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/06/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 20dd86a46ac1b50f5ce20da6ecf9dff251a8c0b0
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839022"
---
# <a name="move-data-from-mysql-using-azure-data-factory"></a>Przenoszenie danych z MySQL za pomocą usługi Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję usługi Data Factory, którego używasz:"]
> * [Wersja 1](data-factory-onprem-mysql-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-mysql.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącą wersję usługi Data Factory, zobacz [łącznika MySQL w wersji 2](../connector-mysql.md).


W tym artykule wyjaśniono, jak użyć działania kopiowania w usłudze Azure Data Factory, aby przenieść dane z lokalnej bazy danych MySQL. Opiera się na [działania przenoszenia danych](data-factory-data-movement-activities.md) artykułu, który przedstawia ogólne omówienie przenoszenie danych za pomocą działania kopiowania.

Możesz skopiować dane z magazynu danych MySQL w środowisku lokalnym, do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych obsługiwanych jako ujścia działania kopiowania, zobacz [obsługiwane magazyny danych](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabeli. Data factory obsługuje obecnie tylko przenosi dane z magazynu danych MySQL do innych magazynów danych, ale nie przenosi dane z innych magazynów danych do magazynu danych MySQL. 

## <a name="prerequisites"></a>Wymagania wstępne
Usługa Data Factory obsługuje łączenie z lokalnymi źródłami MySQL przy użyciu bramy zarządzania danymi. Zobacz [przenoszenia danych między lokalizacjami lokalnymi i chmurą](data-factory-move-data-between-onprem-and-cloud.md) artykuł, aby dowiedzieć się więcej na temat bramy zarządzania danymi i instrukcje krok po kroku dotyczące konfigurowania bramy.

Brama jest wymagana, nawet wtedy, gdy baza danych MySQL jest hostowany na maszynie wirtualnej IaaS platformy Azure (VM). Można zainstalować bramy w tej samej maszyny Wirtualnej jako magazyn danych lub w innej maszyny Wirtualnej, tak długo, jak bramy można połączyć z bazą danych.

> [!NOTE]
> Zobacz [problemów bramy](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) porady dotyczące rozwiązywania problemów z bramy połączenia/problemy związane z usługą.

## <a name="supported-versions-and-installation"></a>Obsługiwane wersje i instalacji
Bramy zarządzania danymi nawiązać połączenie z bazy danych MySQL, musisz zainstalować [MySQL Connector/Net dla Microsoft Windows](https://dev.mysql.com/downloads/connector/net/) (wersja między 6.6.5 i 6.10.7) na tym samym systemie co brama zarządzania danymi. Ten sterownik 32-bitowy jest zgodny z bramy zarządzania danymi w 64-bitowych. MySQL w wersji 5.1 i nowszych są obsługiwane.

> [!TIP]
> Jeśli napotkasz błąd "Uwierzytelnianie nie powiodło się, ponieważ strona zdalna zamknęła strumień transportu.", należy rozważyć uaktualnienie MySQL Connector/Net do nowszej wersji.

## <a name="getting-started"></a>Wprowadzenie
Utworzysz potok z działaniem kopiowania, które przenosi dane z lokalnego magazynu danych Cassandra przy użyciu różnych narzędzi/interfejsów API. 

- Najprostszym sposobem utworzenia potoku jest użycie **kreatora kopiowania**. Zobacz [samouczka: Tworzenie potoku przy użyciu Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) szybki przewodnik dotyczący tworzenia potoku za pomocą Kreatora kopiowania danych. 
- Aby utworzyć potok umożliwia także następujących narzędzi: **Program Visual Studio**, **programu Azure PowerShell**, **szablonu usługi Azure Resource Manager**, **interfejsu API platformy .NET**, i **interfejsu API REST**. Zobacz [samouczka działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) instrukcje krok po kroku utworzyć potok z działaniem kopiowania. 

Czy używasz narzędzi lub interfejsów API, należy wykonać poniższe kroki, aby utworzyć potok, który przenosi dane z magazynu danych źródłowych do magazynu danych ujścia:

1. Tworzenie **połączonych usług** połączyć dane wejściowe i wyjściowe przechowywane z fabryką danych.
2. Tworzenie **zestawów danych** do reprezentowania dane wejściowe i wyjściowe operacji kopiowania. 
3. Tworzenie **potoku** za pomocą działania kopiowania, która przyjmuje jako dane wejściowe zestawu danych i zestaw danych jako dane wyjściowe. 

Korzystając z kreatora, definicje JSON dotyczące tych jednostek usługi Data Factory (połączone usługi, zestawy danych i potok) są tworzone automatycznie dla Ciebie. Korzystając z narzędzi/interfejsów API (z wyjątkiem interfejsu API platformy .NET), należy zdefiniować te jednostki usługi Data Factory przy użyciu formatu JSON.  Przykładowe definicje JSON dotyczące jednostek usługi Data Factory, które są używane do kopiowania danych z magazynu danych MySQL w środowisku lokalnym, możesz znaleźć [przykład kodu JSON: Kopiowanie danych z MySQL do usługi Azure Blob](#json-example-copy-data-from-mysql-to-azure-blob) dalszej części tego artykułu. 

Poniższe sekcje zawierają szczegółowe informacje o właściwościach JSON, które są używane do definiowania jednostek usługi fabryka danych określonego w magazynie danych MySQL:

## <a name="linked-service-properties"></a>Właściwości usługi połączonej
Poniższa tabela zawiera opis specyficzne dla usługi MySQL połączone elementy JSON.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| type |Właściwość type musi być równa: **OnPremisesMySql** |Tak |
| server |Nazwa serwera MySQL. |Tak |
| database |Nazwa bazy danych MySQL. |Yes |
| schema |Nazwa schematu w bazie danych. |Nie |
| authenticationType |Typ uwierzytelniania używany do łączenia z bazą danych MySQL. Możliwe wartości to: `Basic`. |Tak |
| userName |Określ nazwę użytkownika do łączenia z bazą danych MySQL. |Tak |
| password |Określ hasło dla konta użytkownika, który określiłeś. |Tak |
| gatewayName |Nazwa bramy, do którego usługa Data Factory powinna używać do łączenia z bazą danych MySQL w środowisku lokalnym. |Tak |

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby uzyskać pełną listę sekcje & właściwości dostępne Definiowanie zestawów danych, zobacz [tworzenie zestawów danych](data-factory-create-datasets.md) artykułu. Sekcje, takie jak struktury, dostępność i zasady zestawem danych JSON są podobne dla wszystkich typów na zestaw danych (Azure SQL, obiektów blob platformy Azure, usługa Azure table itp.).

**TypeProperties** sekcji różni się dla każdego typu zestawu danych i zawiera informacje o lokalizacji danych w magazynie danych. Zestaw danych o typie sekcji typeProperties **RelationalTable** (w tym zestawie danych MySQL) ma następujące właściwości

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| tableName |Nazwa tabeli w wystąpieniu bazy danych MySQL, który połączona usługa przywołuje. |Nie (Jeśli **zapytania** z **RelationalSource** zostanie określona) |

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Aby uzyskać pełną listę sekcje & właściwości dostępne do definiowania działań, zobacz [tworzenie potoków](data-factory-create-pipelines.md) artykułu. Właściwości, takie jak nazwa, opis i tabele wejściowe i wyjściowe, czy zasady są dostępne dla wszystkich typów działań.

Natomiast właściwości dostępnych w **typeProperties** różnią się w sekcji działania za pomocą poszczególnych typów działań. Działanie kopiowania ich różnią się w zależności od typów źródła i ujścia.

Gdy źródłowego w działaniu kopiowania jest typu **RelationalSource** (w tym MySQL), w sekcji typeProperties dostępne są następujące właściwości:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| query |Użyj zapytania niestandardowe można odczytać danych. |Ciąg zapytania SQL. Na przykład: Wybierz * z MyTable. |Nie (Jeśli **tableName** z **dataset** zostanie określona) |


## <a name="json-example-copy-data-from-mysql-to-azure-blob"></a>Przykład kodu JSON: Kopiowanie danych z MySQL do obiektów Blob platformy Azure
W poniższym przykładzie przedstawiono przykładowe definicji JSON, które umożliwiają tworzenie potoku za pomocą [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub [programu Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Widoczny jest sposób skopiować dane z lokalnej bazy danych MySQL do usługi Azure Blob Storage. Jednakże, można skopiować danych do dowolnego ujścia, o których wspomniano [tutaj](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania w usłudze Azure Data Factory.

> [!IMPORTANT]
> W tym przykładzie przedstawiono fragmenty kodu JSON. Nie obejmuje instrukcje krok po kroku dotyczące tworzenia fabryki danych. Zobacz [przenoszenia danych między lokalizacjami lokalnymi i chmurą](data-factory-move-data-between-onprem-and-cloud.md) artykuł, aby uzyskać instrukcje krok po kroku.

Przykład obejmuje następujących jednostek fabryki danych:

1. Połączonej usługi typu [OnPremisesMySql](data-factory-onprem-mysql-connector.md#linked-service-properties).
2. Połączonej usługi typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Dane wejściowe [dataset](data-factory-create-datasets.md) typu [RelationalTable](data-factory-onprem-mysql-connector.md#dataset-properties).
4. Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [potoku](data-factory-create-pipelines.md) za pomocą działania kopiowania, która używa [RelationalSource](data-factory-onprem-mysql-connector.md#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Przykład kopiuje dane z wyniku kwerendy w bazie danych MySQL do obiektu blob co godzinę. Właściwości JSON używanych w tych przykładach są opisane w sekcjach poniżej przykładów.

Pierwszym krokiem konfiguracji bramy zarządzania danymi. Instrukcje znajdują się w [przenoszenia danych między lokalizacjami lokalnymi i chmurą](data-factory-move-data-between-onprem-and-cloud.md) artykułu.

**MySQL połączoną usługę:**

```JSON
    {
      "name": "OnPremMySqlLinkedService",
      "properties": {
        "type": "OnPremisesMySql",
        "typeProperties": {
          "server": "<server name>",
          "database": "<database name>",
          "schema": "<schema name>",
          "authenticationType": "<authentication type>",
          "userName": "<user name>",
          "password": "<password>",
          "gatewayName": "<gateway>"
        }
      }
    }
```

**Połączona usługa Azure Storage:**

```JSON
    {
      "name": "AzureStorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }
```

**Wejściowy zestaw danych MySQL:**

Przykład przyjęto założenie, utworzono tabelę "MyTable" w bazie danych MySQL i zawiera kolumnę o nazwie "timestampcolumn" w danych szeregów czasowych.

Ustawienie "external": "true" informuje usługę fabryka danych, w tabeli zewnętrznej dla fabryki danych i nie jest generowany przez działanie w usłudze data factory.

```JSON
    {
        "name": "MySqlDataSet",
        "properties": {
            "published": false,
            "type": "RelationalTable",
            "linkedServiceName": "OnPremMySqlLinkedService",
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

Dane są zapisywane do nowego obiektu blob, co godzinę (frequency: godzina, interwał: 1). Ścieżka folderu dla obiektu blob jest dynamicznie obliczana na podstawie czasu rozpoczęcia wycinek, który jest przetwarzany. Ścieżka folderu używa rok, miesiąc, dzień i części godzin od zaplanowanej godziny rozpoczęcia.

```JSON
    {
        "name": "AzureBlobMySqlDataSet",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/mysql/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

Potoku zawierającego działanie kopiowania, który jest skonfigurowany do korzystania z danych wejściowych i wyjściowych zestawów danych i jest zaplanowane do uruchomienia na godzinę. W definicji JSON potok **źródła** ustawiono typ **RelationalSource** i **ujścia** ustawiono typ **BlobSink**. Zapytanie SQL, określony dla **zapytania** właściwość wybiera dane w ciągu ostatniej godziny do skopiowania.

```JSON
    {
        "name": "CopyMySqlToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "RelationalSource",
                            "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "MySqlDataSet"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobMySqlDataSet"
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
                    "name": "MySqlToBlob"
                }
            ],
            "start": "2014-06-01T18:00:00Z",
            "end": "2014-06-01T19:00:00Z"
        }
    }
```


### <a name="type-mapping-for-mysql"></a>Mapowanie typu dla MySQL
Jak wspomniano w [działania przenoszenia danych](data-factory-data-movement-activities.md) artykułu, działanie kopiowania wykonuje operację automatyczne konwersje z typów źródła do ujścia typy za pomocą następujących podejście dwuetapowe:

1. Konwersji z typów natywnych źródła na typ architektury .NET
2. Przekonwertowanie z platformy .NET na typ ujścia natywne

Podczas przenoszenia danych do bazy danych MySQL, następujące mapowania są używane do typów .NET z typów MySQL.

| Typ bazy danych MySQL | Typ .NET framework |
| --- | --- |
| bigint bez znaku |Decimal |
| bigint |Int64 |
| bit |Decimal |
| blob |Byte[] |
| bool |Boolean |
| char |Ciąg |
| date |Datetime |
| datetime |Datetime |
| decimal |Decimal |
| podwójnej precyzji |Double |
| double |Double |
| Wyliczenia |Ciąg |
| float |Single |
| int unsigned |Int64 |
| int |Int32 |
| Liczba całkowita bez znaku |Int64 |
| integer |Int32 |
| długie varbinary |Byte[] |
| varchar długi |Ciąg |
| longblob |Byte[] |
| longtext |Ciąg |
| mediumblob |Byte[] |
| mediumint bez znaku |Int64 |
| mediumint |Int32 |
| mediumtext |String |
| numeric |Decimal |
| real |Double |
| set |String |
| smallint bez znaku |Int32 |
| smallint |Int16 |
| text |Ciąg |
| time |TimeSpan |
| timestamp |Datetime |
| tinyblob |Byte[] |
| tinyint bez znaku |Int16 |
| tinyint |Int16 |
| tinytext |Ciąg |
| varchar |String |
| rok |Int |

## <a name="map-source-to-sink-columns"></a>Mapy źródła do ujścia kolumn
Aby uzyskać informacje dotyczące mapowania kolumn w zestaw danych źródłowych do kolumn w zestawie danych ujścia, zobacz [mapowanie kolumny zestawu danych w usłudze Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Odczyt powtarzalny ze źródeł relacyjnych
Podczas kopiowania danych z relacyjnej bazie danych są przechowywane, Zachowaj powtarzalności należy pamiętać, aby uniknąć niezamierzonego wyników. W usłudze Azure Data Factory możesz ponownie uruchomić wycinek ręcznie. Można również skonfigurować zasady ponawiania dla zestawu danych, dzięki czemu wycinek będzie uruchamiana ponownie, gdy wystąpi błąd. Gdy wycinek będzie uruchamiana ponownie w obu przypadkach, należy się upewnić, że te same dane jest do odczytu niezależnie od tego, ile razy wycinek jest uruchamiany. Zobacz [Repeatable odczytywać źródła relacyjnego](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Wydajność i dostrajanie
Zobacz [wydajności działania kopiowania & przewodnika dostrajania](data-factory-copy-activity-performance.md) Aby dowiedzieć się więcej o kluczowych czynników tego obniżenie wydajności przenoszenia danych (działanie kopiowania) w usłudze Azure Data Factory i różne sposoby, aby zoptymalizować ją.
