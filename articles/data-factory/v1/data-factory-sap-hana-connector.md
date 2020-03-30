---
title: Przenoszenie danych z systemu SAP HANA przy użyciu usługi Azure Data Factory
description: Dowiedz się, jak przenosić dane z usługi SAP HANA przy użyciu usługi Azure Data Factory.
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
ms.openlocfilehash: 361b98a1cde8ee5dee99a370b46d8fc8e0f5af28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265820"
---
# <a name="move-data-from-sap-hana-using-azure-data-factory"></a>Przenoszenie danych z usługi SAP HANA przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](data-factory-sap-hana-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-sap-hana.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [łącznik SAP HANA w wersji 2](../connector-sap-business-warehouse.md).

W tym artykule wyjaśniono, jak używać działania kopiowania w usłudze Azure Data Factory do przenoszenia danych z lokalnego systemu SAP HANA. Opiera się na [działaniach przenoszenia danych,](data-factory-data-movement-activities.md) który przedstawia ogólny przegląd przenoszenia danych z działaniem kopiowania.

Dane można kopiować z lokalnego magazynu danych SAP HANA do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych obsługiwanych jako pochłaniacze przez działanie kopiowania, zobacz tabelę [Obsługiwane magazyny danych.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) Fabryka danych obsługuje obecnie tylko przenoszenie danych z sap HANA do innych magazynów danych, ale nie do przenoszenia danych z innych magazynów danych do SAP HANA.

## <a name="supported-versions-and-installation"></a>Obsługiwane wersje i instalacja
Ten łącznik obsługuje dowolną wersję bazy danych SAP HANA. Obsługuje kopiowanie danych z modeli informacyjnych HANA (takich jak widoki analityczne i obliczenia) i tabel wiersz/kolumna przy użyciu zapytań SQL.

Aby włączyć łączność z wystąpieniem SAP HANA, zainstaluj następujące składniki:
- **Brama zarządzania danymi:** Usługa fabryka danych obsługuje łączenie się z lokalnymi magazynami danych (w tym SAP HANA) przy użyciu składnika o nazwie Brama zarządzania danymi. Aby dowiedzieć się więcej o bramie zarządzania danymi i instrukcjach krok po kroku dotyczących konfigurowania bramy, zobacz [Przenoszenie danych między lokalnym magazynem danych do magazynu danych w chmurze.](data-factory-move-data-between-onprem-and-cloud.md) Brama jest wymagana, nawet jeśli sap HANA jest hostowany na maszynie wirtualnej usługi Azure IaaS (VM). Bramę można zainstalować na tej samej maszynie wirtualnej co magazyn danych lub na innej maszynie wirtualnej, o ile brama może połączyć się z bazą danych.
- **Sterownik SAP HANA ODBC** na komputerze bramy. Sterownik ODBC platformy SAP HANA możesz pobrać z witryny [SAP Software Download Center (Centrum pobierania oprogramowania SAP)](https://support.sap.com/swdc). Szukaj za pomocą słowa kluczowego **SAP HANA CLIENT for Windows**. 

## <a name="getting-started"></a>Wprowadzenie
Można utworzyć potok z działaniem kopiowania, który przenosi dane z lokalnego magazynu danych SAP HANA przy użyciu różnych narzędzi/interfejsów API. 

- Najprostszym sposobem utworzenia potoku jest użycie **Kreatora kopiowania**. Zobacz [Samouczek: Tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) w celu szybkiego instruktażu w sprawie tworzenia potoku przy użyciu kreatora kopiowania danych. 
- Do utworzenia potoku można również użyć następujących narzędzi: **Visual Studio**, **Azure PowerShell**, **szablon usługi Azure Resource Manager**, **.NET API**i REST **API**. Zobacz [Kopiowanie samouczka aktywności,](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) aby uzyskać instrukcje krok po kroku, aby utworzyć potok z działaniem kopiowania. 

Niezależnie od tego, czy są używane narzędzia, czy interfejsy API, wykonaj następujące kroki, aby utworzyć potok, który przenosi dane ze źródłowego magazynu danych do magazynu danych ujścia:

1. Tworzenie **połączonych usług** w celu połączenia magazynów danych wejściowych i wyjściowych z fabryką danych.
2. Tworzenie **zestawów danych** do reprezentowania danych wejściowych i wyjściowych dla operacji kopiowania. 
3. Utwórz **potok** z działaniem kopiowania, które przyjmuje zestaw danych jako dane wejściowe i zestaw danych jako dane wyjściowe. 

Korzystając z kreatora, definicje JSON dla tych jednostek usługi Data Factory (połączone usługi, zestawy danych i potok) są tworzone automatycznie dla Ciebie. Korzystając z narzędzi/interfejsów API (z wyjątkiem interfejsu API platformy .NET), można zdefiniować te jednostki fabryki danych przy użyciu formatu JSON.  W przypadku przykładu z definicjami JSON dla jednostek usługi Data Factory, które są używane do kopiowania danych z lokalnego systemu SAP HANA, zobacz [przykład JSON: Kopiowanie danych z sap HANA do usługi Azure Blob](#json-example-copy-data-from-sap-hana-to-azure-blob) sekcji tego artykułu. 

Poniższe sekcje zawierają szczegółowe informacje o właściwościach JSON, które są używane do definiowania jednostek usługi Data Factory specyficznych dla magazynu danych SAP HANA:

## <a name="linked-service-properties"></a>Połączone właściwości usługi
Poniższa tabela zawiera opis elementów JSON specyficznych dla usługi połączonej SAP HANA.

Właściwość | Opis | Dozwolone wartości | Wymagany
-------- | ----------- | -------------- | --------
serwer | Nazwa serwera, na którym znajduje się wystąpienie SAP HANA. Jeśli serwer używa niestandardowego portu, `server:port`określ . | ciąg | Tak
authenticationType | Typ uwierzytelniania. | . "Podstawowe" lub "Windows" | Tak 
nazwa użytkownika | Nazwa użytkownika, który ma dostęp do serwera SAP | ciąg | Tak
hasło | Hasło użytkownika. | ciąg | Tak
nazwa bramy | Nazwa bramy, której usługa Data Factory powinna używać do łączenia się z lokalnym wystąpieniem sap hana. | ciąg | Tak
encryptedCredential | Zaszyfrowany ciąg poświadczeń. | ciąg | Nie

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby uzyskać pełną listę sekcji & właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [Tworzenie zestawów danych.](data-factory-create-datasets.md) Sekcje, takie jak struktura, dostępność i zasady zestawu danych JSON są podobne dla wszystkich typów zestawów danych (Azure SQL, Azure blob, tabela platformy Azure itp.).

Sekcja **typeProperties** jest inna dla każdego typu zestawu danych i zawiera informacje o lokalizacji danych w magazynie danych. Nie są obsługiwane żadne właściwości specyficzne dla typu dla zestawu danych SAP HANA typu **RelationalTable**. 


## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Aby uzyskać pełną listę sekcji & właściwości dostępne do definiowania działań, zobacz [tworzenie potoków](data-factory-create-pipelines.md) artykułu. Właściwości, takie jak nazwa, opis, dane wejściowe i wyjściowe tabele, są zasady są dostępne dla wszystkich typów działań.

Mając na uwadze, właściwości dostępne w sekcji **typeProperties** działania różnią się w zależności od typu działania. W przypadku działania kopiowania różnią się one w zależności od typów źródeł i pochłaniacze.

Gdy źródło w działaniu kopiowania jest typu **RelationalSource** (który obejmuje SAP HANA), następujące właściwości są dostępne w sekcji typeProperties:

| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| query | Określa kwerendę SQL do odczytu danych z wystąpienia SAP HANA. | Kwerenda SQL. | Tak |

## <a name="json-example-copy-data-from-sap-hana-to-azure-blob"></a>Przykład JSON: Kopiowanie danych z usługi SAP HANA do obiektu Blob platformy Azure
Poniższy przykład zawiera przykładowe definicje JSON, których można użyć do utworzenia potoku przy użyciu [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub programu Azure [PowerShell.](data-factory-copy-activity-tutorial-using-powershell.md) W tym przykładzie pokazano, jak skopiować dane z lokalnego systemu SAP HANA do usługi Azure Blob Storage. Jednak dane mogą być kopiowane **bezpośrednio** do dowolnego ujścia wymienione [w tym miejscu](data-factory-data-movement-activities.md#supported-data-stores-and-formats) przy użyciu działania kopiowania w usłudze Azure Data Factory.  

> [!IMPORTANT]
> Ten przykład zawiera fragmenty kodu JSON. Nie zawiera instrukcje krok po kroku dotyczące tworzenia fabryki danych. Aby uzyskać instrukcje krok po kroku, zobacz [przenoszenie danych między lokalizacjami lokalnymi a artykułami w chmurze.](data-factory-move-data-between-onprem-and-cloud.md)

Próbka ma następujące jednostki fabryki danych:

1. Powiązana usługa typu [SapHana](#linked-service-properties).
2. Połączona usługa typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [RelationalTable](#dataset-properties).
4. Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. [Potok](data-factory-create-pipelines.md) z działaniem kopiowania, który używa [RelationalSource](#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Przykładowy kopie dane z wystąpienia SAP HANA do obiektu blob platformy Azure co godzinę. Właściwości JSON używane w tych próbkach są opisane w sekcjach następujących po próbkach.

W pierwszej kolejności skonfiguruj bramę zarządzania danymi. Instrukcje znajdują się w [ruchomych danych między lokalizacjami lokalnymi i](data-factory-move-data-between-onprem-and-cloud.md) w chmurze artykułu.

### <a name="sap-hana-linked-service"></a>Usługa połączona SAP HANA
Ta połączona usługa łączy wystąpienie SAP HANA z fabryką danych. Właściwość type jest ustawiona na **SapHana**. Sekcja typeProperties zawiera informacje o połączeniu dla wystąpienia SAP HANA.

```json
{
    "name": "SapHanaLinkedService",
    "properties":
    {
        "type": "SapHana",
        "typeProperties":
        {
            "server": "<server name>",
            "authenticationType": "<Basic, or Windows>",
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

### <a name="sap-hana-input-dataset"></a>Zestaw danych wejściowych SAP HANA

Ten zestaw danych definiuje zestaw danych SAP HANA. Typ zestawu danych Data Factory można ustawić na **RelationalTable**. Obecnie nie należy określać żadnych właściwości specyficznych dla typu dla zestawu danych SAP HANA. Kwerenda w definicji działania kopiowania określa, jakie dane mają być odczytywane z wystąpienia SAP HANA. 

Ustawienie właściwości zewnętrznej na true informuje usługę Data Factory, że tabela jest zewnętrzna dla fabryki danych i nie jest wytwarzana przez działanie w fabryce danych.

Właściwości częstotliwości i interwału definiują harmonogram. W takim przypadku dane są odczytywane z wystąpienia SAP HANA co godzinę. 

```json
{
    "name": "SapHanaDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapHanaLinkedService",
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
Ten zestaw danych definiuje wyjściowy zestaw danych obiektów blob platformy Azure. Właściwość typu jest ustawiona na AzureBlob. Sekcja typeProperties zawiera, gdzie są przechowywane dane skopiowane z wystąpienia SAP HANA. Dane są zapisywane w nowym obiekcie blob co godzinę (częstotliwość: godzina, interwał: 1). Ścieżka folderu obiektu blob jest dynamicznie oceniana na podstawie czasu rozpoczęcia przetwarzanego plasterka. Ścieżka folderu używa części czasu rozpoczęcia z roku, miesiąca, dnia i godziny.

```json
{
    "name": "AzureBlobDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/saphana/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

Potok zawiera działanie kopiowania, który jest skonfigurowany do korzystania z wejściowych i wyjściowych zestawów danych i jest zaplanowane do uruchomienia co godzinę. W definicji JSON potoku typ **źródła** jest ustawiony na **RelationalSource** (dla źródła SAP HANA) i typ **ujścia** jest ustawiony na **BlobSink**. Kwerenda SQL określona dla właściwości **kwerendy** wybiera dane w ciągu ostatniej godziny do skopiowania.

```json
{
    "name": "CopySapHanaToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "<SQL Query for HANA>"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "SapHanaDataset"
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
                "name": "SapHanaToBlob"
            }
        ],
        "start": "2017-03-01T18:00:00Z",
        "end": "2017-03-01T19:00:00Z"
    }
}
```


### <a name="type-mapping-for-sap-hana"></a>Mapowanie typów dla SAP HANA
Jak wspomniano w artykule działania związane z [przenoszeniem danych,](data-factory-data-movement-activities.md) działanie kopiowania wykonuje automatyczne konwersje typów z typów źródłowych do typów ujścia z następującym podejściem dwuetapowym:

1. Konwertowanie z natywnych typów źródeł na typ .NET
2. Konwertowanie z typu .NET na natywny typ ujścia

Podczas przenoszenia danych z sap HANA, następujące mapowania są używane z typów SAP HANA do typów .NET.

Typ SAP HANA | Typ oparty na sieci .NET
------------- | ---------------
Tinyint | Byte
Smallint | Int16
INT | Int32
Bigint | Int64
LICZBA RZECZYWISTA | Single
Podwójne | Single
Dziesiętnych | Wartość dziesiętna
Boolean | Byte
Varchar | Ciąg
Nvarchar | Ciąg
Clob | Bajt[]
ALFANUM | Ciąg
Blob | Bajt[]
DATE | DateTime
CZAS | przedział_czasu
Sygnatury czasowej | DateTime
ODDELEGOWKA | DateTime

## <a name="known-limitations"></a>Znane ograniczenia
Istnieje kilka znanych ograniczeń podczas kopiowania danych z SAP HANA:

- Ciągi NVARCHAR są obcinane do maksymalnej długości wynoszącej 4000 znaków Unicode
- Typ danych SMALLDECIMAL nie jest obsługiwany
- Typ danych VARBINARY nie jest obsługiwany
- Prawidłowe daty są z zakresu od 1899-12-30 do 9999-12-31

## <a name="map-source-to-sink-columns"></a>Mapowanie źródła do kolumn ujścia
Aby dowiedzieć się więcej o mapowaniu kolumn w źródłowym zestawie danych na kolumny w zestawie danych ujścia, zobacz [Mapowanie kolumn zestawu danych w usłudze Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Powtarzalny odczyt ze źródeł relacyjnych
Podczas kopiowania danych z relacyjnych magazynów danych należy pamiętać o powtarzalności, aby uniknąć niezamierzonych wyników. W usłudze Azure Data Factory można ponownie uruchomić plasterek ręcznie. Można również skonfigurować zasady ponawiania dla zestawu danych, tak aby plasterek został ponownie uruchomny w przypadku wystąpienia błędu. Gdy plasterek jest uruchamiany ponownie w obu przypadkach, należy upewnić się, że te same dane są odczytywane bez względu na to, ile razy jest uruchamiany plasterek. Zobacz [powtarzalny odczyt ze źródeł relacyjnych](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)

## <a name="performance-and-tuning"></a>Wydajność i strojenie
Zobacz [Kopiowanie wydajności działania & dostrajania przewodnika,](data-factory-copy-activity-performance.md) aby dowiedzieć się więcej o kluczowych czynnikach, które wpływają na wydajność przenoszenia danych (Copy Activity) w usłudze Azure Data Factory i na różne sposoby optymalizacji.
