---
title: Przenoszenie danych z magazynu SAP Business przy użyciu usługi Azure Data Factory
description: Dowiedz się, jak przenosić dane z magazynu SAP Business przy użyciu usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 108bdf057cd375e28b10a6838ec5c8c6f57749a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281056"
---
# <a name="move-data-from-sap-business-warehouse-using-azure-data-factory"></a>Przenoszenie danych z magazynu sap business przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](data-factory-sap-business-warehouse-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-sap-business-warehouse.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [łącznik SAP Business Warehouse w wersji 2](../connector-sap-business-warehouse.md).


W tym artykule wyjaśniono, jak używać działania kopiowania w usłudze Azure Data Factory do przenoszenia danych z lokalnego magazynu biznesowego SAP (BW). Opiera się na [działaniach przenoszenia danych,](data-factory-data-movement-activities.md) który przedstawia ogólny przegląd przenoszenia danych z działaniem kopiowania.

Dane można kopiować z lokalnego magazynu sap business warehouse do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych obsługiwanych jako pochłaniacze przez działanie kopiowania, zobacz tabelę [Obsługiwane magazyny danych.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) Fabryka danych obsługuje obecnie tylko przenoszenie danych z magazynu SAP Business do innych magazynów danych, ale nie do przenoszenia danych z innych magazynów danych do magazynu biznesowego SAP. 

## <a name="supported-versions-and-installation"></a>Obsługiwane wersje i instalacja
Ten łącznik obsługuje sap business warehouse w wersji 7.x. Obsługuje kopiowanie danych z InfoCubes i QueryCubes (w tym zapytań BEx) przy użyciu zapytań MDX.

Aby włączyć łączność z wystąpieniem SAP BW, zainstaluj następujące składniki:
- **Brama zarządzania danymi:** Usługa data factory obsługuje łączenie się z lokalnymi magazynami danych (w tym z magazynem SAP Business Warehouse) przy użyciu składnika o nazwie Brama zarządzania danymi. Aby dowiedzieć się więcej o bramie zarządzania danymi i instrukcjach krok po kroku dotyczących konfigurowania bramy, zobacz [Przenoszenie danych między lokalnym magazynem danych do magazynu danych w chmurze.](data-factory-move-data-between-onprem-and-cloud.md) Brama jest wymagana, nawet jeśli magazyn biznesowy SAP jest obsługiwany na maszynie wirtualnej usługi Azure IaaS (VM). Bramę można zainstalować na tej samej maszynie wirtualnej co magazyn danych lub na innej maszynie wirtualnej, o ile brama może połączyć się z bazą danych.
- **Sap NetWeaver biblioteki** na komputerze bramy. Bibliotekę SAP Netweaver można pobrać od administratora SAP lub bezpośrednio z [centrum pobierania oprogramowania SAP.](https://support.sap.com/swdc) Wyszukaj **#1025361 SAP Note,** aby uzyskać lokalizację pobierania najnowszej wersji. Upewnij się, że architektura biblioteki SAP NetWeaver (32-bitowej lub 64-bitowej) jest zgodna z instalacją bramy. Następnie zainstaluj wszystkie pliki zawarte w SAP NetWeaver RFC SDK zgodnie z SAP Note. Biblioteka SAP NetWeaver jest również uwzględniona w instalacji narzędzi klienckich SAP.

> [!TIP]
> Umieść biblioteki DLL wyodrębnione z netweaver RFC SDK do folderu system32.

## <a name="getting-started"></a>Wprowadzenie
Można utworzyć potok z działaniem kopiowania, który przenosi dane z lokalnego magazynu danych Cassandra przy użyciu różnych narzędzi/interfejsów API. 

- Najprostszym sposobem utworzenia potoku jest użycie **Kreatora kopiowania**. Zobacz [Samouczek: Tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) w celu szybkiego instruktażu w sprawie tworzenia potoku przy użyciu kreatora kopiowania danych. 
- Do utworzenia potoku można również użyć następujących narzędzi: **Visual Studio**, **Azure PowerShell**, **szablon usługi Azure Resource Manager**, **.NET API**i REST **API**. Zobacz [Kopiowanie samouczka aktywności,](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) aby uzyskać instrukcje krok po kroku, aby utworzyć potok z działaniem kopiowania. 

Niezależnie od tego, czy są używane narzędzia, czy interfejsy API, wykonaj następujące kroki, aby utworzyć potok, który przenosi dane ze źródłowego magazynu danych do magazynu danych ujścia:

1. Tworzenie **połączonych usług** w celu połączenia magazynów danych wejściowych i wyjściowych z fabryką danych.
2. Tworzenie **zestawów danych** do reprezentowania danych wejściowych i wyjściowych dla operacji kopiowania. 
3. Utwórz **potok** z działaniem kopiowania, które przyjmuje zestaw danych jako dane wejściowe i zestaw danych jako dane wyjściowe. 

Korzystając z kreatora, definicje JSON dla tych jednostek usługi Data Factory (połączone usługi, zestawy danych i potok) są tworzone automatycznie dla Ciebie. Korzystając z narzędzi/interfejsów API (z wyjątkiem interfejsu API platformy .NET), można zdefiniować te jednostki fabryki danych przy użyciu formatu JSON.  W przypadku przykładu z definicjami JSON dla jednostek usługi Data Factory, które są używane do kopiowania danych z lokalnego magazynu biznesowego SAP, zobacz [przykład JSON: Kopiowanie danych z magazynu sap business do usługi Azure Blob](#json-example-copy-data-from-sap-business-warehouse-to-azure-blob) sekcji tego artykułu. 

Poniższe sekcje zawierają szczegółowe informacje o właściwościach JSON, które są używane do definiowania jednostek fabryki danych specyficznych dla magazynu danych SAP BW:

## <a name="linked-service-properties"></a>Połączone właściwości usługi
Poniższa tabela zawiera opis elementów JSON specyficznych dla usługi połączonej SAP Business Warehouse (BW).

Właściwość | Opis | Dozwolone wartości | Wymagany
-------- | ----------- | -------------- | --------
serwer | Nazwa serwera, na którym znajduje się wystąpienie SAP BW. | ciąg | Tak
systemNumer | Numer systemu SAP BW. | Dwucyfrowy numer dziesiętny reprezentowany jako ciąg. | Tak
clientId | Identyfikator klienta klienta w systemie SAP W. | Trzycyfrowy numer dziesiętny reprezentowany jako ciąg. | Tak
nazwa użytkownika | Nazwa użytkownika, który ma dostęp do serwera SAP | ciąg | Tak
hasło | Hasło użytkownika. | ciąg | Tak
nazwa bramy | Nazwa bramy, której usługa Data Factory powinna używać do łączenia się z lokalnym wystąpieniem SAP BW. | ciąg | Tak
encryptedCredential | Zaszyfrowany ciąg poświadczeń. | ciąg | Nie

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby uzyskać pełną listę sekcji & właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [Tworzenie zestawów danych.](data-factory-create-datasets.md) Sekcje, takie jak struktura, dostępność i zasady zestawu danych JSON są podobne dla wszystkich typów zestawów danych (Azure SQL, Azure blob, tabela platformy Azure itp.).

Sekcja **typeProperties** jest inna dla każdego typu zestawu danych i zawiera informacje o lokalizacji danych w magazynie danych. Nie są obsługiwane żadne właściwości specyficzne dla typu dla zestawu danych SAP BW typu **RelationalTable**. 


## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Aby uzyskać pełną listę sekcji & właściwości dostępne do definiowania działań, zobacz [tworzenie potoków](data-factory-create-pipelines.md) artykułu. Właściwości, takie jak nazwa, opis, dane wejściowe i wyjściowe tabele, są zasady są dostępne dla wszystkich typów działań.

Mając na uwadze, właściwości dostępne w sekcji **typeProperties** działania różnią się w zależności od typu działania. W przypadku działania kopiowania różnią się one w zależności od typów źródeł i pochłaniacze.

Gdy źródło w działaniu kopiowania jest typu **RelationalSource** (który obejmuje SAP BW), następujące właściwości są dostępne w sekcji typeProperties:

| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| query | Określa kwerendę MDX do odczytu danych z wystąpienia SAP BW. | kwerenda MDX. | Tak |


## <a name="json-example-copy-data-from-sap-business-warehouse-to-azure-blob"></a>Przykład JSON: Kopiowanie danych z magazynu sap business do obiektu Blob platformy Azure
Poniższy przykład zawiera przykładowe definicje JSON, których można użyć do utworzenia potoku przy użyciu [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub programu Azure [PowerShell.](data-factory-copy-activity-tutorial-using-powershell.md) W tym przykładzie pokazano, jak skopiować dane z lokalnego magazynu biznesowego SAP do usługi Azure Blob Storage. Jednak dane mogą być kopiowane **bezpośrednio** do dowolnego ujścia podane [w tym miejscu](data-factory-data-movement-activities.md#supported-data-stores-and-formats) przy użyciu działania kopiowania w usłudze Azure Data Factory.  

> [!IMPORTANT]
> Ten przykład zawiera fragmenty kodu JSON. Nie zawiera instrukcje krok po kroku dotyczące tworzenia fabryki danych. Aby uzyskać instrukcje krok po kroku, zobacz [przenoszenie danych między lokalizacjami lokalnymi a artykułami w chmurze.](data-factory-move-data-between-onprem-and-cloud.md)

Próbka ma następujące jednostki fabryki danych:

1. Połączona usługa typu [SapBw](#linked-service-properties).
2. Połączona usługa typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [RelationalTable](#dataset-properties).
4. Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. [Potok](data-factory-create-pipelines.md) z działaniem kopiowania, który używa [RelationalSource](#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Przykładowy kopie dane z wystąpienia sap business warehouse do obiektu blob platformy Azure co godzinę. Właściwości JSON używane w tych próbkach są opisane w sekcjach następujących po próbkach.

W pierwszej kolejności skonfiguruj bramę zarządzania danymi. Instrukcje znajdują się w [ruchomych danych między lokalizacjami lokalnymi i](data-factory-move-data-between-onprem-and-cloud.md) w chmurze artykułu.

### <a name="sap-business-warehouse-linked-service"></a>Usługa połączona z magazynem biznesowym SAP
Ta połączona usługa łączy wystąpienie SAP BW z fabryką danych. Właściwość type jest ustawiona na **SapBw**. Sekcja typeProperties zawiera informacje o połączeniu dla wystąpienia SAP BW. 

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
Ta połączona usługa łączy twoje konto usługi Azure Storage z fabryką danych. Właściwość typu jest ustawiona na **AzureStorage**. Sekcja typeProperties zawiera informacje o połączeniu dla konta usługi Azure Storage.

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

### <a name="sap-bw-input-dataset"></a>Zestaw danych wejściowych SAP BW
Ten zestaw danych definiuje zestaw danych SAP Business Warehouse. Typ zestawu danych Data Factory można ustawić na **RelationalTable**. Obecnie nie należy określać żadnych właściwości specyficznych dla typu dla zestawu danych SAP BW. Kwerenda w definicji działania kopiowania określa, jakie dane mają być odczytywane z wystąpienia SAP BW. 

Ustawienie właściwości zewnętrznej na true informuje usługę Data Factory, że tabela jest zewnętrzna dla fabryki danych i nie jest wytwarzana przez działanie w fabryce danych.

Właściwości częstotliwości i interwału definiują harmonogram. W takim przypadku dane są odczytywane z wystąpienia SAP BW co godzinę. 

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
Ten zestaw danych definiuje wyjściowy zestaw danych obiektów blob platformy Azure. Właściwość typu jest ustawiona na AzureBlob. Sekcja typeProperties zawiera, gdzie są przechowywane dane skopiowane z wystąpienia SAP BW. Dane są zapisywane w nowym obiekcie blob co godzinę (częstotliwość: godzina, interwał: 1). Ścieżka folderu obiektu blob jest dynamicznie oceniana na podstawie czasu rozpoczęcia przetwarzanego plasterka. Ścieżka folderu używa części czasu rozpoczęcia z roku, miesiąca, dnia i godziny.

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


### <a name="pipeline-with-copy-activity"></a>Potok z działaniem Kopiowania
Potok zawiera działanie kopiowania, który jest skonfigurowany do korzystania z wejściowych i wyjściowych zestawów danych i jest zaplanowane do uruchomienia co godzinę. W definicji JSON potoku typ **źródła** jest ustawiony na **RelationalSource** (dla źródła SAP BW), a typ **ujścia** jest ustawiony na **BlobSink**. Kwerenda określona dla właściwości **kwerendy** wybiera dane w ciągu ostatniej godziny do skopiowania.

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



### <a name="type-mapping-for-sap-bw"></a>Mapowanie typów dla SAP BW
Jak wspomniano w artykule działania związane z [przenoszeniem danych,](data-factory-data-movement-activities.md) działanie kopiowania wykonuje automatyczne konwersje typów z typów źródłowych do typów ujścia z następującym podejściem dwuetapowym:

1. Konwertowanie z natywnych typów źródeł na typ .NET
2. Konwertowanie z typu .NET na natywny typ ujścia

Podczas przenoszenia danych z SAP BW, następujące mapowania są używane z typów SAP BW do typów .NET.

Typ danych w słowniku ABAP | Typ danych platformy .NET
-------------------------------- | --------------
ACCP (włask. |  int
Char | Ciąg
Clnt | Ciąg
Curr | Wartość dziesiętna
CUKY (CUKY) | Ciąg
Grudnia | Wartość dziesiętna
protokół FLTP | Double
Int1 (wład. | Byte
Int2 (włas ie) | Int16
Int4 (włas ie) | int
Lang | Ciąg
LCHR (LCHR) | Ciąg
LRAW (LRAW) | Bajt[]
Prec (prec) | Int16
Quan | Wartość dziesiętna
Raw | Bajt[]
RAWSTROWANIE | Bajt[]
Ciąg | Ciąg
Jednostki | Ciąg
Dats | Ciąg
NuMC (numc) | Ciąg
TIMS (TIMS) | Ciąg

> [!NOTE]
> Aby mapować kolumny ze źródłowego zestawu danych na kolumny z zestawu danych ujścia, zobacz [Mapowanie kolumn zestawu danych w usłudze Azure Data Factory](data-factory-map-columns.md).


## <a name="map-source-to-sink-columns"></a>Mapowanie źródła do kolumn ujścia
Aby dowiedzieć się więcej o mapowaniu kolumn w źródłowym zestawie danych na kolumny w zestawie danych ujścia, zobacz [Mapowanie kolumn zestawu danych w usłudze Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Powtarzalny odczyt ze źródeł relacyjnych
Podczas kopiowania danych z relacyjnych magazynów danych należy pamiętać o powtarzalności, aby uniknąć niezamierzonych wyników. W usłudze Azure Data Factory można ponownie uruchomić plasterek ręcznie. Można również skonfigurować zasady ponawiania dla zestawu danych, tak aby plasterek został ponownie uruchomny w przypadku wystąpienia błędu. Gdy plasterek jest uruchamiany ponownie w obu przypadkach, należy upewnić się, że te same dane są odczytywane bez względu na to, ile razy jest uruchamiany plasterek. Zobacz [powtarzalny odczyt ze źródeł relacyjnych](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)

## <a name="performance-and-tuning"></a>Wydajność i strojenie
Zobacz [Kopiowanie wydajności działania & dostrajania przewodnika,](data-factory-copy-activity-performance.md) aby dowiedzieć się więcej o kluczowych czynnikach, które wpływają na wydajność przenoszenia danych (Copy Activity) w usłudze Azure Data Factory i na różne sposoby optymalizacji.
