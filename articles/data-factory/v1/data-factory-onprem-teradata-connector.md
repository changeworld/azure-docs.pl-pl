---
title: Przenoszenie danych z programu Teradata przy użyciu usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o Teradata łącznika usługi Data Factory, która umożliwia przenoszenie danych z bazy danych programu Teradata
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 98eb76d8-5f3d-4667-b76e-e59ed3eea3ae
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: d22318f4d9e233a57d521fe36f0827b9fc3af3e0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60610737"
---
# <a name="move-data-from-teradata-using-azure-data-factory"></a>Przenoszenie danych z programu Teradata przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1](data-factory-onprem-teradata-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-teradata.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącą wersję usługi Data Factory, zobacz [Teradata łącznika w wersji 2](../connector-teradata.md).

W tym artykule wyjaśniono, jak użyć działania kopiowania w usłudze Azure Data Factory, aby przenieść dane z lokalnej bazy danych programu Teradata. Opiera się na [działania przenoszenia danych](data-factory-data-movement-activities.md) artykułu, który przedstawia ogólne omówienie przenoszenie danych za pomocą działania kopiowania.

Możesz skopiować dane z magazynu danych programu Teradata w środowisku lokalnym, do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych obsługiwanych jako ujścia działania kopiowania, zobacz [obsługiwane magazyny danych](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabeli. Data factory obsługuje obecnie tylko przenosi dane z magazynu danych programu Teradata do innych magazynów danych, ale nie przenosi dane z innych magazynów danych do magazynu danych programu Teradata.

## <a name="prerequisites"></a>Wymagania wstępne
Usługa Data factory obsługuje łączenie z lokalnymi źródłami Teradata za pośrednictwem bramy zarządzania danymi. Zobacz [przenoszenia danych między lokalizacjami lokalnymi i chmurą](data-factory-move-data-between-onprem-and-cloud.md) artykuł, aby dowiedzieć się więcej na temat bramy zarządzania danymi i instrukcje krok po kroku dotyczące konfigurowania bramy.

Brama jest wymagana, nawet wtedy, gdy Teradata znajduje się w maszynie Wirtualnej IaaS platformy Azure. Można zainstalować bramy w tej samej maszyny Wirtualnej IaaS jako magazyn danych lub w innej maszyny Wirtualnej, tak długo, jak bramy można połączyć z bazą danych.

> [!NOTE]
> Zobacz [problemów bramy](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) porady dotyczące rozwiązywania problemów z bramy połączenia/problemy związane z usługą.

## <a name="supported-versions-and-installation"></a>Obsługiwane wersje i instalacji
Bramy zarządzania danymi do łączenia z bazą danych programu Teradata, musisz zainstalować [.NET Data Provider for Teradata](https://go.microsoft.com/fwlink/?LinkId=278886) wersji 14 lub nowszej na tym samym systemie co brama zarządzania danymi. Teradata wersja 12 i nowszej jest obsługiwana.

## <a name="getting-started"></a>Wprowadzenie
Utworzysz potok z działaniem kopiowania, które przenosi dane z lokalnego magazynu danych Cassandra przy użyciu różnych narzędzi/interfejsów API.

- Najprostszym sposobem utworzenia potoku jest użycie **kreatora kopiowania**. Zobacz [samouczka: Tworzenie potoku przy użyciu Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) szybki przewodnik dotyczący tworzenia potoku za pomocą Kreatora kopiowania danych.
- Aby utworzyć potok umożliwia także następujących narzędzi: **Witryna Azure portal**, **programu Visual Studio**, **programu Azure PowerShell**, **szablonu usługi Azure Resource Manager**, **interfejsu API platformy .NET**i  **Interfejs API REST**. Zobacz [samouczka działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) instrukcje krok po kroku utworzyć potok z działaniem kopiowania.

Czy używasz narzędzi lub interfejsów API, należy wykonać poniższe kroki, aby utworzyć potok, który przenosi dane z magazynu danych źródłowych do magazynu danych ujścia:

1. Tworzenie **połączonych usług** połączyć dane wejściowe i wyjściowe przechowywane z fabryką danych.
2. Tworzenie **zestawów danych** do reprezentowania dane wejściowe i wyjściowe operacji kopiowania.
3. Tworzenie **potoku** za pomocą działania kopiowania, która przyjmuje jako dane wejściowe zestawu danych i zestaw danych jako dane wyjściowe.

Korzystając z kreatora, definicje JSON dotyczące tych jednostek usługi Data Factory (połączone usługi, zestawy danych i potok) są tworzone automatycznie dla Ciebie. Korzystając z narzędzi/interfejsów API (z wyjątkiem interfejsu API platformy .NET), należy zdefiniować te jednostki usługi Data Factory przy użyciu formatu JSON.  Przykładowe definicje JSON dotyczące jednostek usługi Data Factory, które są używane do kopiowania danych z lokalnego magazynu danych programu Teradata, możesz znaleźć [przykład kodu JSON: Kopiowanie danych z programu Teradata do usługi Azure Blob](#json-example-copy-data-from-teradata-to-azure-blob) dalszej części tego artykułu.

Poniższe sekcje zawierają szczegółowe informacje o właściwościach JSON, które są używane do definiowania jednostek usługi fabryka danych określonego w magazynie danych programu Teradata:

## <a name="linked-service-properties"></a>Właściwości usługi połączonej
Poniższa tabela zawiera opis dla elementów JSON, które są specyficzne dla programu Teradata połączonej usługi.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| type |Właściwość type musi być równa: **OnPremisesTeradata** |Yes |
| server |Nazwa serwera programu Teradata. |Yes |
| authenticationType |Typ uwierzytelniania używany do łączenia z bazą danych programu Teradata. Możliwe wartości: Anonimowe, podstawowe i Windows. |Yes |
| username |Określ nazwę użytkownika, jeśli używasz uwierzytelniania podstawowe lub Windows. |Nie |
| password |Określ hasło dla konta użytkownika, która została określona jako nazwy użytkownika. |Nie |
| gatewayName |Nazwa bramy, do którego usługa Data Factory powinna używać do łączenia z bazą danych Teradata w środowisku lokalnym. |Yes |

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby uzyskać pełną listę sekcje & właściwości dostępne Definiowanie zestawów danych, zobacz [tworzenie zestawów danych](data-factory-create-datasets.md) artykułu. Sekcje, takie jak struktury, dostępność i zasady zestawem danych JSON są podobne dla wszystkich typów na zestaw danych (Azure SQL, obiektów blob platformy Azure, usługa Azure table itp.).

**TypeProperties** sekcji różni się dla każdego typu zestawu danych i zawiera informacje o lokalizacji danych w magazynie danych. Obecnie nie istnieją żadne właściwości typu obsługiwanych dla zestawu danych programu Teradata.

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Aby uzyskać pełną listę sekcje & właściwości dostępne do definiowania działań, zobacz [tworzenie potoków](data-factory-create-pipelines.md) artykułu. Właściwości, takie jak nazwa, opis, dane wejściowe i wyjściowe tabel i zasady są dostępne dla wszystkich typów działań.

Natomiast właściwości, które są dostępne w sekcji typeProperties działania zależą od każdego typu działania. Działanie kopiowania ich różnią się w zależności od typów źródła i ujścia.

Jeśli źródło jest typu **RelationalSource** (w tym Teradata), następujące właściwości są dostępne w **typeProperties** sekcji:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| query |Użyj zapytania niestandardowe można odczytać danych. |Ciąg zapytania SQL. Na przykład: Wybierz * z MyTable. |Yes |

### <a name="json-example-copy-data-from-teradata-to-azure-blob"></a>Przykład kodu JSON: Kopiowanie danych z programu Teradata do obiektów Blob platformy Azure
W poniższym przykładzie przedstawiono przykładowe definicji JSON, które umożliwiają tworzenie potoku za pomocą [witryny Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) lub [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub [programu Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Pokazują, jak skopiować dane z programu Teradata w usłudze Azure Blob Storage. Jednakże, można skopiować danych do dowolnego ujścia, o których wspomniano [tutaj](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania w usłudze Azure Data Factory.

Przykład obejmuje następujących jednostek fabryki danych:

1. Połączonej usługi typu [OnPremisesTeradata](#linked-service-properties).
2. Połączonej usługi typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Dane wejściowe [dataset](data-factory-create-datasets.md) typu [RelationalTable](#dataset-properties).
4. Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. [Potoku](data-factory-create-pipelines.md) za pomocą działania kopiowania, która używa [RelationalSource](#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Przykład kopiuje dane z wyniku kwerendy w bazie danych programu Teradata do obiektu blob na godzinę. Właściwości JSON używanych w tych przykładach są opisane w sekcjach poniżej przykładów.

Pierwszym krokiem konfiguracji bramy zarządzania danymi. Instrukcje znajdują się w [przenoszenia danych między lokalizacjami lokalnymi i chmurą](data-factory-move-data-between-onprem-and-cloud.md) artykułu.

**Teradata połączoną usługę:**

```json
{
    "name": "OnPremTeradataLinkedService",
    "properties": {
        "type": "OnPremisesTeradata",
        "typeProperties": {
            "server": "<server>",
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
        "type": "AzureStorageLinkedService",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
        }
    }
}
```

**Wejściowy zestaw danych Teradata:**

Przykład przyjęto założenie, utworzono tabelę "MyTable" w Teradata i zawiera kolumnę o nazwie "timestamp" w danych szeregów czasowych.

Ustawienie "external": true usługi Data Factory informuje, że w tabeli zewnętrznej dla fabryki danych i nie jest generowany przez działanie w usłudze data factory.

```json
{
    "name": "TeradataDataSet",
    "properties": {
        "published": false,
        "type": "RelationalTable",
        "linkedServiceName": "OnPremTeradataLinkedService",
        "typeProperties": {
        },
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

```json
{
    "name": "AzureBlobTeradataDataSet",
    "properties": {
        "published": false,
        "location": {
            "type": "AzureBlobLocation",
            "folderPath": "mycontainer/teradata/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
            ],
            "linkedServiceName": "AzureStorageLinkedService"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```
**Potok za pomocą działania kopiowania:**

Potoku zawierającego działanie kopiowania jest skonfigurowany do korzystania z danych wejściowych i wyjściowych zestawów danych, która jest zaplanowane do uruchomienia co godzinę. W definicji JSON potok **źródła** ustawiono typ **RelationalSource** i **ujścia** ustawiono typ **BlobSink**. Zapytanie SQL, określony dla **zapytania** właściwość wybiera dane w ciągu ostatniej godziny do skopiowania.

```json
{
    "name": "CopyTeradataToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', SliceStart, SliceEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "TeradataDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobTeradataDataSet"
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
                "name": "TeradataToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z",
        "isPaused": false
    }
}
```
## <a name="type-mapping-for-teradata"></a>Mapowania typów dla programu Teradata
Jak wspomniano w [działania przenoszenia danych](data-factory-data-movement-activities.md) artykułu, działanie kopiowania wykonuje operację automatyczne konwersje z typów źródła do ujścia typów przy użyciu następujących podejść krok 2:

1. Konwersji z typów natywnych źródła na typ architektury .NET
2. Przekonwertowanie z platformy .NET na typ ujścia natywne

Podczas przenoszenia danych Teradata, następujące mapowania są używane z Teradata typu na typ architektury .NET.

| Typ bazy danych programu Teradata | Typ .NET framework |
| --- | --- |
| char |String |
| CLOB |String |
| Grafika |String |
| VarChar |String |
| VarGraphic |String |
| Obiekt blob |Byte[] |
| Byte |Byte[] |
| VarByte |Byte[] |
| BigInt |Int64 |
| ByteInt |Int16 |
| Decimal |Decimal |
| Double |Double |
| Liczba całkowita |Int32 |
| Liczba |Double |
| SmallInt |Int16 |
| Date |DateTime |
| Time |TimeSpan |
| Godzina ze strefą czasową |String |
| Znacznik czasu |DateTime |
| Sygnatura czasowa ze strefą czasową |DateTimeOffset |
| Interwał dnia |TimeSpan |
| Interwału dnia do godziny |TimeSpan |
| Interwału dnia do minuty |TimeSpan |
| Interwału dnia do sekundy |TimeSpan |
| Interwał, godzinę |TimeSpan |
| Godzina przedział na minutę |TimeSpan |
| Godzina przedział na sekundę |TimeSpan |
| Interwał minutę |TimeSpan |
| Interwału minuty do sekundy |TimeSpan |
| Interwał drugiego |TimeSpan |
| Interwał roku |String |
| Interwału roku do miesiąca |String |
| Interwał miesiąca |String |
| Period(Date) |String |
| Period(Time) |String |
| Okres (godzina ze strefą czasową) |String |
| Period(TimeStamp) |String |
| Okres (sygnatura czasowa ze strefą czasową) |String |
| Xml |String |

## <a name="map-source-to-sink-columns"></a>Mapy źródła do ujścia kolumn
Aby uzyskać informacje dotyczące mapowania kolumn w zestaw danych źródłowych do kolumn w zestawie danych ujścia, zobacz [mapowanie kolumny zestawu danych w usłudze Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Odczyt powtarzalny ze źródeł relacyjnych
Podczas kopiowania danych z relacyjnej bazie danych są przechowywane, Zachowaj powtarzalności należy pamiętać, aby uniknąć niezamierzonego wyników. W usłudze Azure Data Factory możesz ponownie uruchomić wycinek ręcznie. Można również skonfigurować zasady ponawiania dla zestawu danych, dzięki czemu wycinek będzie uruchamiana ponownie, gdy wystąpi błąd. Gdy wycinek będzie uruchamiana ponownie w obu przypadkach, należy się upewnić, że te same dane jest do odczytu niezależnie od tego, ile razy wycinek jest uruchamiany. Zobacz [Repeatable odczytywać źródła relacyjnego](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Wydajność i dostrajanie
Zobacz [wydajności działania kopiowania & przewodnika dostrajania](data-factory-copy-activity-performance.md) Aby dowiedzieć się więcej o kluczowych czynników tego obniżenie wydajności przenoszenia danych (działanie kopiowania) w usłudze Azure Data Factory i różne sposoby, aby zoptymalizować ją.
