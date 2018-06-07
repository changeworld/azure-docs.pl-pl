---
title: Przenoszenia danych z programu SAP Business Warehouse przy użyciu fabryki danych Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposobu przenoszenia danych z programu SAP Business Warehouse przy użyciu fabryki danych Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 6debc77bf7232f5ee5d7e638ee25b5eccdbdeeef
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34622081"
---
# <a name="move-data-from-sap-business-warehouse-using-azure-data-factory"></a>Przenoszenie danych z programu SAP Business Warehouse przy użyciu fabryki danych Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1 — ogólnie dostępna](data-factory-sap-business-warehouse-connector.md)
> * [Wersja 2 — wersja zapoznawcza](../connector-sap-business-warehouse.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory, która jest ogólnie dostępna (GA). Jeśli używasz wersji 2 usługi fabryka danych, która jest w wersji zapoznawczej, zobacz [SAP Business Warehouse łącznika w wersji 2](../connector-sap-business-warehouse.md).


W tym artykule opisano sposób używania działania kopiowania w fabryce danych Azure do przenoszenia danych z lokalnego SAP Business magazynu (BW). Opiera się na [działań przepływu danych](data-factory-data-movement-activities.md) artykułu, który przedstawia ogólny przegląd przenoszenia danych z działania kopiowania.

Można skopiować danych z lokalnego magazynu danych SAP Business Warehouse żadnych obsługiwanych ujścia magazynu danych. Lista magazynów danych obsługiwane jako wychwytywanie przez działanie kopiowania, zobacz [obsługiwane magazyny danych](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabeli. Fabryki danych aktualnie obsługuje tylko dane przenoszenie, z SAP Business Warehouse do innych magazynów danych, ale nie do przenoszenia danych z innych magazynów danych do programu SAP Business Warehouse. 

## <a name="supported-versions-and-installation"></a>Obsługiwane wersje i instalacji
Ten łącznik obsługuje wersję SAP Business Warehouse 7.x. Obsługuje ona kopiowania danych z InfoCubes i QueryCubes (takie jak zapytania BEx) przy użyciu zapytania MDX.

Aby umożliwić łączność z wystąpienia programu SAP BW, należy zainstalować następujące składniki:
- **Brama zarządzania danymi**: łączenie z danymi lokalnymi obsługuje usługi fabryka danych magazynów (w tym SAP Business Warehouse) przy użyciu składnika o nazwie brama zarządzania danymi. Informacje na temat bramy zarządzania danymi i szczegółowe instrukcje dotyczące konfigurowania bramy, zobacz [przenoszenie danych między danymi lokalnymi magazynu do magazynu danych w chmurze](data-factory-move-data-between-onprem-and-cloud.md) artykułu. Nawet jeśli SAP Business Warehouse znajduje się na maszynie wirtualnej platformy Azure IaaS (VM), wymagana jest brama. Można zainstalować bramę na tej samej maszyny Wirtualnej do przechowywania danych lub w innej maszyny Wirtualnej, tak długo, jak bramy można połączyć z bazą danych.
- **SAP NetWeaver biblioteki** na komputerze bramy. Biblioteka SAP Netweaver można uzyskać od administratora SAP, lub bezpośrednio z [Centrum pobierania oprogramowania SAP](https://support.sap.com/swdc). Wyszukaj **1025361 # Uwaga SAP** można pobrać lokalizację pobierania najnowszej wersji. Upewnij się, czy architektura biblioteki SAP NetWeaver (32-bitowy lub 64-bitowy) jest zgodna instalacji bramy. Następnie zainstaluj wszystkie pliki zawarte w zestawie SDK programu SAP NetWeaver RFC zgodnie z Uwaga SAP. Biblioteka programu SAP NetWeaver znajduje się również w narzędziach klienckich SAP instalacji.

> [!TIP]
> Umieść wyodrębniony z zestawu SDK RFC NetWeaver w folderze system32 bibliotek DLL.

## <a name="getting-started"></a>Wprowadzenie
Można utworzyć potok z działania kopiowania, który przenosi dane z magazynu lokalnego Cassandra danych przy użyciu różnych narzędzi/interfejsów API. 

- Najprostszym sposobem, aby utworzyć potok jest użycie **kreatora kopiowania**. Zobacz [samouczek: tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) szybkie przewodnik dotyczący tworzenia potoku za pomocą Kreatora kopiowania danych. 
- Umożliwia także następujące narzędzia do tworzenia potoku: **portalu Azure**, **programu Visual Studio**, **programu Azure PowerShell**, **szablonu usługi Azure Resource Manager**, **interfejs API .NET**, i **interfejsu API REST**. Zobacz [samouczek działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) instrukcje krok po kroku utworzyć potok z działaniem kopiowania. 

Czy można użyć narzędzia i interfejsy API, należy wykonać następujące kroki, aby utworzyć potok, który przenosi dane z magazynu danych źródła do ujścia magazynu danych:

1. Utwórz **połączone usługi** Aby połączyć dane wejściowe i wyjściowe są przechowywane w fabryce danych.
2. Utwórz **zestawów danych** do reprezentowania danych wejściowych i wyjściowych operacji kopiowania. 
3. Utwórz **potoku** aktywnością kopiowania zestawu danych jako dane wejściowe i zestawu danych jako dane wyjściowe. 

Korzystając z kreatora, definicje JSON do tych jednostek fabryki danych (połączone usługi, zestawy danych i potoki) są tworzone automatycznie dla Ciebie. Korzystając z narzędzi/API (z wyjątkiem interfejs API .NET), należy zdefiniować tych jednostek fabryki danych w formacie JSON.  Dla przykładu z definicji JSON dla jednostek fabryki danych, które są używane do skopiowania danych z lokalnego SAP Business Warehouse, zobacz [przykład JSON: kopiowanie danych z programu SAP Business Warehouse do obiektów Blob platformy Azure](#json-example-copy-data-from-sap-business-warehouse-to-azure-blob) sekcji tego artykułu. 

Poniższe sekcje zawierają szczegółowe informacje o właściwości JSON, które są używane do definiowania jednostek fabryki danych określonej do magazynu danych programu SAP BW:

## <a name="linked-service-properties"></a>Połączona usługa właściwości
Poniższa tabela zawiera opis specyficzne dla usługi programu SAP Business magazynu (BW) połączone elementy JSON.

Właściwość | Opis | Dozwolone wartości | Wymagane
-------- | ----------- | -------------- | --------
serwer | Nazwa serwera, na którym znajduje się wystąpienie programu SAP BW. | ciąg | Yes
systemNumber | Numer systemu systemu SAP BW. | Liczba dziesiętna dwucyfrowe reprezentowany jako ciąg. | Yes
clientId | Identyfikator klienta w systemie SAP W klienta. | Trzycyfrowa liczba dziesiętna reprezentowany jako ciąg. | Yes
nazwa użytkownika | Nazwa użytkownika, który ma dostęp do serwera SAP | ciąg | Yes
hasło | Hasło dla użytkownika. | ciąg | Yes
gatewayName | Nazwa bramy, która powinna być używana do nawiązania połączenia lokalnego wystąpienia programu SAP BW usługi fabryka danych. | ciąg | Yes
encryptedCredential | Ciąg zaszyfrowane poświadczenia. | ciąg | Nie

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby uzyskać pełną listę sekcje & właściwości dostępne do definiowania zestawów danych, zobacz [Tworzenie zbiorów danych](data-factory-create-datasets.md) artykułu. Sekcje zawierają informacje, takie jak struktury, dostępności i zasad zestawu danych JSON są podobne dla wszystkich typów obiektów dataset (Azure SQL, obiektów blob platformy Azure, Azure tabeli itp.).

**TypeProperties** sekcja jest różne dla każdego typu zestawu danych i zawiera informacje o lokalizacji danych w magazynie danych. Nie ma żadnych właściwości specyficzne dla typu obsługiwane dla zestawu danych SAP BW typu **RelationalTable**. 


## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Pełną listę sekcje & właściwości dostępne do definiowania działań, zobacz [tworzenie potoków](data-factory-create-pipelines.md) artykułu. Właściwości, takie jak nazwa, opis i tabel wejściowych i wyjściowych, czy zasady są dostępne dla wszystkich typów działań.

Właściwości dostępne w **typeProperties** sekcji działania zależne od każdego typu działania. Dla działania kopiowania różnią się w zależności od typów źródeł i sink.

Gdy źródło w przypadku działania kopiowania jest typu **RelationalSource** (która obejmuje programu SAP BW), w sekcji typeProperties dostępne są następujące właściwości:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| query | Określa zapytanie MDX, które można odczytać danych z wystąpienia programu SAP BW. | Zapytania MDX. | Yes |


## <a name="json-example-copy-data-from-sap-business-warehouse-to-azure-blob"></a>Przykład JSON: kopiowanie danych z programu SAP Business Warehouse do obiektów Blob platformy Azure
W poniższym przykładzie przedstawiono przykładowe definicje JSON, które można użyć, aby utworzyć potok przy użyciu [portalu Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) lub [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub [programu Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). W tym przykładzie pokazano, jak można skopiować danych z lokalnego SAP Business Warehouse do magazynu obiektów Blob Azure. Jednak dane mogą być kopiowane **bezpośrednio** do dowolnego wychwytywanie podane [tutaj](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania w fabryce danych Azure.  

> [!IMPORTANT]
> W tym przykładzie przedstawiono fragmenty kodu JSON. Zawiera instrukcje krok po kroku dotyczące tworzenia fabryki danych. Zobacz [przenoszenie danych między lokalizacji lokalnej i w chmurze](data-factory-move-data-between-onprem-and-cloud.md) artykułu, aby uzyskać instrukcje krok po kroku.

Przykład zawiera następujące obiekty fabryki danych:

1. Połączonej usługi typu [SapBw](#linked-service-properties).
2. Połączonej usługi typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Dane wejściowe [dataset](data-factory-create-datasets.md) typu [RelationalTable](#dataset-properties).
4. Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [potoku](data-factory-create-pipelines.md) z działaniem kopii, która używa [RelationalSource](#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Przykład kopiuje dane z wystąpieniem SAP Business Warehouse obiektów blob platformy Azure co godzinę. Właściwości JSON używane w te przykłady są opisane w sekcjach poniżej próbek.

Pierwszym krokiem konfiguracji bramy zarządzania danymi. Instrukcje znajdują się w [przenoszenie danych między lokalizacji lokalnej i w chmurze](data-factory-move-data-between-onprem-and-cloud.md) artykułu.

### <a name="sap-business-warehouse-linked-service"></a>SAP Business Warehouse połączona usługa
To połączone usługi łączy wystąpienia programu SAP BW fabryki danych. Właściwość type ma ustawioną **SapBw**. Sekcja typeProperties zawiera informacje o połączeniu dla wystąpienia programu SAP BW. 

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
Łącza usługi to połączone konta magazynu Azure do fabryki danych. Właściwość type ma ustawioną **AzureStorage**. Sekcja typeProperties zawiera informacje o połączeniu dla konta usługi Magazyn Azure.

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
Ten zestaw danych definiuje zestaw danych SAP Business Warehouse. Ustaw typ fabryki danych zestawu danych do **RelationalTable**. Obecnie nie określisz żadnych właściwości określonego typu dla programu SAP BW zestawu danych. Zapytania w definicji działania kopiowania Określa, jakie dane do odczytu z wystąpienia programu SAP BW. 

Ustawienie właściwości zewnętrznych true informuje o usługi fabryka danych czy tabeli zewnętrznej dla fabryki danych i nie jest generowany przez działanie w fabryce danych.

Częstotliwość i interwał właściwości definiuje harmonogramu. W takim przypadku dane są odczytywane z wystąpienia programu SAP BW co godzinę. 

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
Ten zestaw danych określa wyjściowego zestawu danych obiektów Blob platformy Azure. Właściwość type ma ustawioną AzureBlob. Sekcji typeProperties miejsce, w którym są przechowywane dane skopiowane z wystąpienia programu SAP BW. Dane są zapisywane do nowego obiektu blob co godzinę (częstotliwość: godziny, interwał: 1). Ścieżka folderu dla obiekt blob jest dynamicznie obliczane na podstawie czasu rozpoczęcia wycinek, który jest przetwarzana. Ścieżka folderu używa rok, miesiąc, dzień i godziny części czas rozpoczęcia.

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


### <a name="pipeline-with-copy-activity"></a>W potoku z działanie kopiowania
Potok zawiera działanie kopiowania, który jest skonfigurowany do używania wejściowe i wyjściowe zestawy danych i jest zaplanowane co godzinę. W definicji JSON potoku **źródła** ustawiono typ **RelationalSource** (dla programu SAP BW źródła) i **zbiornika** ustawiono typ **BlobSink**. Zapytanie określone dla **zapytania** właściwości wybiera dane w ostatniej godziny do skopiowania.

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



### <a name="type-mapping-for-sap-bw"></a>Mapowanie typu dla programu SAP BW
Jak wspomniano w [działań przepływu danych](data-factory-data-movement-activities.md) artykułu, automatyczne konwersje z typów źródła do zbiornika typy z następujących rozwinięcie wykonuje działania kopiowania:

1. Konwertowanie typów natywnych źródła na typ architektury .NET
2. Konwertowanie na typ macierzysty ujścia typ architektury .NET

Podczas przenoszenia danych z programu SAP BW, następujące mapowania są używane z programu SAP BW typów do typów .NET.

Typ danych w słowniku ABAP | Typ danych .net
-------------------------------- | --------------
ACCP |  Int
CHAR | Ciąg
CLNT | Ciąg
CURR | Decimal
CUKY | Ciąg
DEC | Decimal
FLTP | O podwójnej precyzji
INT1 | Bajtów
INT2 | Int16
INT4 | Int
JĘZYK | Ciąg
LCHR | Ciąg
LRAW | Byte[]
PREC | Int16
QUAN | Decimal
NIEPRZETWORZONE | Byte[]
RAWSTRING | Byte[]
CIĄG | Ciąg
JEDNOSTKA | Ciąg
DATS | Ciąg
NUMC | Ciąg
TIMS | Ciąg

> [!NOTE]
> Aby mapować kolumn z zestawu źródła danych do kolumn z obiektu sink zestawu danych, zobacz [mapowania kolumnach dataset w fabryce danych Azure](data-factory-map-columns.md).


## <a name="map-source-to-sink-columns"></a>Obiekt sink kolumn mapy źródła
Aby uzyskać informacje dotyczące mapowania kolumn w zestawie źródła danych do kolumn w zestawie danych zbiornika, zobacz [mapowania kolumnach dataset w fabryce danych Azure](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Odczyt powtarzalny ze źródłami relacyjnymi
Podczas kopiowania danych z danych relacyjnych przechowuje, należy pamiętać, aby uniknąć niezamierzone wyniki powtarzalności. Fabryka danych Azure możesz ponownie ręcznie wycinek. Można również skonfigurować zasady ponawiania dla zestawu danych, aby wycinek jest uruchamiany ponownie, gdy wystąpi błąd. Podczas wycinek zostanie uruchomiona ponownie w obu przypadkach, należy się upewnić, że te same dane jest do odczytu niezależnie od tego, ile razy wycinek jest uruchamiany. Zobacz [Repeatable odczytywać relacyjne źródła](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)

## <a name="performance-and-tuning"></a>Wydajność i dostrajania
Zobacz [wydajności działania kopiowania & dostrajanie przewodnik](data-factory-copy-activity-performance.md) Aby dowiedzieć się więcej o kluczowych czynników tego wydajność wpływ przenoszenia danych (działanie kopiowania) w usłudze fabryka danych Azure i zoptymalizować ją na różne sposoby.
