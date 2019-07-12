---
title: Przenoszenie danych z rozwiązania SAP Business Warehouse przy użyciu usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposobu przenoszenia danych z rozwiązania SAP Business Warehouse przy użyciu usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: c928ad1fc9a8d6206c1b7e47591b17b6ae05ee4b
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839881"
---
# <a name="move-data-from-sap-business-warehouse-using-azure-data-factory"></a>Przenoszenie danych z rozwiązania SAP Business Warehouse przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję usługi Data Factory, którego używasz:"]
> * [Wersja 1](data-factory-sap-business-warehouse-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-sap-business-warehouse.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącą wersję usługi Data Factory, zobacz [łącznika SAP Business Warehouse w wersji 2](../connector-sap-business-warehouse.md).


W tym artykule wyjaśniono, jak użyć działania kopiowania w usłudze Azure Data Factory do przenoszenia danych z lokalnych SAP Business Warehouse (BW). Opiera się na [działania przenoszenia danych](data-factory-data-movement-activities.md) artykułu, który przedstawia ogólne omówienie przenoszenie danych za pomocą działania kopiowania.

Możesz skopiować dane z lokalnego magazynu danych SAP Business Warehouse, do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych obsługiwanych jako ujścia działania kopiowania, zobacz [obsługiwane magazyny danych](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabeli. Data factory obsługuje obecnie tylko przenosi dane z rozwiązania SAP Business Warehouse innych magazynach danych, ale nie przenosi dane z innych magazynów danych do rozwiązania SAP Business Warehouse. 

## <a name="supported-versions-and-installation"></a>Obsługiwane wersje i instalacji
Ten łącznik obsługuje wersję oprogramowania SAP Business Warehouse 7.x. Obsługuje kopiowanie danych z informacji (infocube) i QueryCubes (takie jak zapytania BEx) przy użyciu zapytań MDX.

Aby włączyć łączność z wystąpieniem programu SAP BW, należy zainstalować następujące składniki:
- **Brama zarządzania danymi**: Nawiązywanie połączeń z lokalnych magazynów danych (w tym SAP Business Warehouse) obsługiwane przez usługi fabryka danych za pomocą składnika o nazwie brama zarządzania danymi. Aby dowiedzieć się więcej na temat bramy zarządzania danymi i szczegółowe instrukcje dotyczące konfigurowania bramy, zobacz [przenoszenia danych między lokalnymi danymi zapisać w magazynie danych w chmurze](data-factory-move-data-between-onprem-and-cloud.md) artykułu. Brama jest wymagana, nawet wtedy, gdy system SAP Business Warehouse znajduje się na maszynie wirtualnej IaaS platformy Azure (VM). Można zainstalować bramy w tej samej maszyny Wirtualnej jako magazyn danych lub w innej maszyny Wirtualnej, tak długo, jak bramy można połączyć z bazą danych.
- **Oprogramowanie SAP NetWeaver biblioteki** na maszynie bramy. Można uzyskać biblioteki SAP Netweaver, od administratora SAP lub bezpośrednio z [SAP Software Download Center](https://support.sap.com/swdc). Wyszukaj **SAP Note #1025361** można pobrać lokalizacji pobierania dla najnowszej wersji. Upewnij się, że architektura biblioteki SAP NetWeaver (32-bitowy lub 64-bitowy) odpowiada instalacji bramy. Następnie zainstaluj wszystkie pliki zawarte w zestawie SDK SAP NetWeaver RFC zgodnie z SAP Note. Biblioteka oprogramowania SAP NetWeaver znajduje się również w instalacji narzędzi klienckich SAP.

> [!TIP]
> Umieścić wyodrębnione z zestawu SDK RFC NetWeaver w folderze system32 biblioteki dll.

## <a name="getting-started"></a>Wprowadzenie
Utworzysz potok z działaniem kopiowania, które przenosi dane z lokalnego magazynu danych Cassandra przy użyciu różnych narzędzi/interfejsów API. 

- Najprostszym sposobem utworzenia potoku jest użycie **kreatora kopiowania**. Zobacz [samouczka: Tworzenie potoku przy użyciu Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) szybki przewodnik dotyczący tworzenia potoku za pomocą Kreatora kopiowania danych. 
- Aby utworzyć potok umożliwia także następujących narzędzi: **Program Visual Studio**, **programu Azure PowerShell**, **szablonu usługi Azure Resource Manager**, **interfejsu API platformy .NET**, i **interfejsu API REST**. Zobacz [samouczka działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) instrukcje krok po kroku utworzyć potok z działaniem kopiowania. 

Czy używasz narzędzi lub interfejsów API, należy wykonać poniższe kroki, aby utworzyć potok, który przenosi dane z magazynu danych źródłowych do magazynu danych ujścia:

1. Tworzenie **połączonych usług** połączyć dane wejściowe i wyjściowe przechowywane z fabryką danych.
2. Tworzenie **zestawów danych** do reprezentowania dane wejściowe i wyjściowe operacji kopiowania. 
3. Tworzenie **potoku** za pomocą działania kopiowania, która przyjmuje jako dane wejściowe zestawu danych i zestaw danych jako dane wyjściowe. 

Korzystając z kreatora, definicje JSON dotyczące tych jednostek usługi Data Factory (połączone usługi, zestawy danych i potok) są tworzone automatycznie dla Ciebie. Korzystając z narzędzi/interfejsów API (z wyjątkiem interfejsu API platformy .NET), należy zdefiniować te jednostki usługi Data Factory przy użyciu formatu JSON.  Przykładowe definicje JSON dotyczące jednostek usługi Data Factory, które są używane w celu kopiowania danych ze środowiska lokalnego rozwiązania SAP Business Warehouse, możesz znaleźć [przykład kodu JSON: Kopiowanie danych z rozwiązania SAP Business Warehouse do usługi Azure Blob](#json-example-copy-data-from-sap-business-warehouse-to-azure-blob) dalszej części tego artykułu. 

Poniższe sekcje zawierają szczegółowe informacje o właściwościach JSON, które są używane do definiowania jednostek usługi fabryka danych określonej do magazynu danych programu SAP BW:

## <a name="linked-service-properties"></a>Właściwości usługi połączonej
Poniższa tabela zawiera opis specyficzne dla usługi SAP Business Warehouse (BW), połączone elementy JSON.

Właściwość | Opis | Dozwolone wartości | Wymagane
-------- | ----------- | -------------- | --------
server | Nazwa serwera, na którym znajduje się wystąpienie systemu SAP BW. | ciąg | Tak
systemNumber | Numer systemu systemu SAP BW. | Liczba dziesiętna dwucyfrowy reprezentowane jako ciąg. | Tak
clientId | Identyfikator klienta klienta w SAP W systemie. | Liczba dziesiętna trzycyfrowy reprezentowane jako ciąg. | Tak
username | Nazwa użytkownika, który ma dostęp do serwera SAP | ciąg | Tak
password | Hasło użytkownika. | ciąg | Tak
gatewayName | Nazwa bramy, która powinna być używana w usłudze Data Factory, połączyć się z lokalnym wystąpieniem programu SAP BW. | ciąg | Tak
encryptedCredential | Ciąg zaszyfrowane poświadczenia. | ciąg | Nie

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby uzyskać pełną listę sekcje & właściwości dostępne Definiowanie zestawów danych, zobacz [tworzenie zestawów danych](data-factory-create-datasets.md) artykułu. Sekcje, takie jak struktury, dostępność i zasady zestawem danych JSON są podobne dla wszystkich typów na zestaw danych (Azure SQL, obiektów blob platformy Azure, usługa Azure table itp.).

**TypeProperties** sekcji różni się dla każdego typu zestawu danych i zawiera informacje o lokalizacji danych w magazynie danych. Nie ma żadnych właściwości typu obsługiwanych dla zestawu danych SAP BW typu **RelationalTable**. 


## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Aby uzyskać pełną listę sekcje & właściwości dostępne do definiowania działań, zobacz [tworzenie potoków](data-factory-create-pipelines.md) artykułu. Właściwości, takie jak nazwa, opis i tabele wejściowe i wyjściowe, czy zasady są dostępne dla wszystkich typów działań.

Natomiast właściwości dostępnych w **typeProperties** różnią się w sekcji działania za pomocą poszczególnych typów działań. Działanie kopiowania ich różnią się w zależności od typów źródła i ujścia.

Gdy źródłowego w działaniu kopiowania jest typu **RelationalSource** (w tym system SAP BW), w sekcji typeProperties dostępne są następujące właściwości:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| query | Określa zapytanie MDX, które można odczytać danych z wystąpienia systemu SAP BW. | Zapytanie MDX. | Yes |


## <a name="json-example-copy-data-from-sap-business-warehouse-to-azure-blob"></a>Przykład kodu JSON: Kopiowanie danych z rozwiązania SAP Business Warehouse do obiektów Blob platformy Azure
W poniższym przykładzie przedstawiono przykładowe definicji JSON, które umożliwiają tworzenie potoku za pomocą [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub [programu Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ten przykład pokazuje, jak skopiować dane z lokalnego rozwiązania SAP Business Warehouse do usługi Azure Blob Storage. Jednak dane mogą być kopiowane **bezpośrednio** do dowolnego ujścia, o których wspomniano [tutaj](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania w usłudze Azure Data Factory.  

> [!IMPORTANT]
> W tym przykładzie przedstawiono fragmenty kodu JSON. Nie obejmuje instrukcje krok po kroku dotyczące tworzenia fabryki danych. Zobacz [przenoszenia danych między lokalizacjami lokalnymi i chmurą](data-factory-move-data-between-onprem-and-cloud.md) artykuł, aby uzyskać instrukcje krok po kroku.

Przykład obejmuje następujących jednostek fabryki danych:

1. Połączonej usługi typu [SapBw](#linked-service-properties).
2. Połączonej usługi typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Dane wejściowe [dataset](data-factory-create-datasets.md) typu [RelationalTable](#dataset-properties).
4. Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [potoku](data-factory-create-pipelines.md) za pomocą działania kopiowania, która używa [RelationalSource](#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Przykład kopiuje dane z wystąpienia oprogramowania SAP Business Warehouse do obiektu blob platformy Azure co godzinę. Właściwości JSON używanych w tych przykładach są opisane w sekcjach poniżej przykładów.

Pierwszym krokiem konfiguracji bramy zarządzania danymi. Instrukcje znajdują się w [przenoszenia danych między lokalizacjami lokalnymi i chmurą](data-factory-move-data-between-onprem-and-cloud.md) artykułu.

### <a name="sap-business-warehouse-linked-service"></a>SAP Business Warehouse połączona usługa
Ta połączona usługa łączy wystąpienie programu SAP BW z fabryką danych. Właściwość type jest ustawiona **SapBw**. W sekcji typeProperties przedstawiono informacje o połączeniu dla wystąpień programu SAP BW. 

```json
{
    "name": "SapBwLinkedService",
    "properties":
    {
        "type": "SapBw",
        "typeProperties":
        {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Połączona usługa Azure Storage
Ta połączona usługa łączy konto usługi Azure Storage z fabryką danych. Właściwość type jest ustawiona **AzureStorage**. W sekcji typeProperties przedstawiono informacje o połączeniu dla konta usługi Azure Storage.

```json
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

### <a name="sap-bw-input-dataset"></a>Wejściowy zestaw danych SAP BW
Ten zestaw danych definiuje zestaw danych SAP Business Warehouse. Ustaw typ zestawu danych usługi Data Factory na **RelationalTable**. Obecnie nie należy określać żadnych właściwości specyficzne dla zestawu danych SAP BW. Zapytanie w definicji działania kopiowania określa jakie dane mają być odczytywane wystąpienie programu SAP BW. 

Ustawienie właściwości zewnętrznych na wartość true informuje usługi Data Factory, że w tabeli zewnętrznej dla fabryki danych i nie jest generowany przez działanie w usłudze data factory.

Właściwości wartościami Frequency i interval definiuje harmonogramu. W tym przypadku dane są odczytywane z wystąpienia systemu SAP BW co godzinę. 

```json
{
    "name": "SapBwDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapBwLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```



### <a name="azure-blob-output-dataset"></a>Wyjściowy zestaw danych obiektów blob platformy Azure
Ten zestaw danych Określa wyjściowy zestaw danych obiektów Blob platformy Azure. Właściwość type jest ustawiona do usługi AzureBlob. W sekcji typeProperties miejsce, w którym są przechowywane dane skopiowane z wystąpienia systemu SAP BW. Dane są zapisywane do nowego obiektu blob, co godzinę (frequency: godzina, interwał: 1). Ścieżka folderu dla obiektu blob jest dynamicznie obliczana na podstawie czasu rozpoczęcia wycinek, który jest przetwarzany. Ścieżka folderu używa rok, miesiąc, dzień i części godzin od zaplanowanej godziny rozpoczęcia.

```json
{
    "name": "AzureBlobDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/sapbw/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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


### <a name="pipeline-with-copy-activity"></a>Potok z działaniem kopiowania
Potoku zawierającego działanie kopiowania, który jest skonfigurowany do korzystania z danych wejściowych i wyjściowych zestawów danych i jest zaplanowane do uruchomienia na godzinę. W definicji JSON potok **źródła** ustawiono typ **RelationalSource** (na źródle SAP BW) i **ujścia** ustawiono typ **BlobSink**. Zapytania określony dla **zapytania** właściwość wybiera dane w ciągu ostatniej godziny do skopiowania.

```json
{
    "name": "CopySapBwToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "<MDX query for SAP BW>"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "SapBwDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobDataSet"
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
                "name": "SapBwToBlob"
            }
        ],
        "start": "2017-03-01T18:00:00Z",
        "end": "2017-03-01T19:00:00Z"
    }
}
```



### <a name="type-mapping-for-sap-bw"></a>Mapowania typów dla programu SAP BW
Jak wspomniano w [działania przenoszenia danych](data-factory-data-movement-activities.md) artykułu, działanie kopiowania wykonuje operację automatyczne konwersje z typów źródła do ujścia typy za pomocą następujących podejście dwuetapowe:

1. Konwersji z typów natywnych źródła na typ architektury .NET
2. Przekonwertowanie z platformy .NET na typ ujścia natywne

Podczas przenoszenia danych z systemu SAP BW, następujące mapowania są używane do typów .NET z typów programu SAP BW.

Typ danych w słowniku ABAP | Typ danych .NET
-------------------------------- | --------------
ACCP |  Int
CHAR | String
CLNT | String
CURR | Decimal
CUKY | String
DEC | Decimal
FLTP | Double
INT1 | Byte
INT2 | Int16
INT4 | Int
LANG | String
LCHR | String
LRAW | Byte[]
PREC | Int16
QUAN | Decimal
RAW | Byte[]
RAWSTRING | Byte[]
STRING | String
UNIT | String
DATS | String
NUMC | String
TIMS | Ciąg

> [!NOTE]
> Aby zamapować kolumny z zestawu danych źródłowych do kolumn z zestawu danych ujścia, zobacz [mapowanie kolumny zestawu danych w usłudze Azure Data Factory](data-factory-map-columns.md).


## <a name="map-source-to-sink-columns"></a>Mapy źródła do ujścia kolumn
Aby uzyskać informacje dotyczące mapowania kolumn w zestaw danych źródłowych do kolumn w zestawie danych ujścia, zobacz [mapowanie kolumny zestawu danych w usłudze Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Odczyt powtarzalny ze źródeł relacyjnych
Podczas kopiowania danych z relacyjnej bazie danych są przechowywane, Zachowaj powtarzalności należy pamiętać, aby uniknąć niezamierzonego wyników. W usłudze Azure Data Factory możesz ponownie uruchomić wycinek ręcznie. Można również skonfigurować zasady ponawiania dla zestawu danych, dzięki czemu wycinek będzie uruchamiana ponownie, gdy wystąpi błąd. Gdy wycinek będzie uruchamiana ponownie w obu przypadkach, należy się upewnić, że te same dane jest do odczytu niezależnie od tego, ile razy wycinek jest uruchamiany. Zobacz [Repeatable odczytywać źródeł relacyjnych](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)

## <a name="performance-and-tuning"></a>Wydajność i dostrajanie
Zobacz [wydajności działania kopiowania & przewodnika dostrajania](data-factory-copy-activity-performance.md) Aby dowiedzieć się więcej o kluczowych czynników tego obniżenie wydajności przenoszenia danych (działanie kopiowania) w usłudze Azure Data Factory i różne sposoby, aby zoptymalizować ją.
