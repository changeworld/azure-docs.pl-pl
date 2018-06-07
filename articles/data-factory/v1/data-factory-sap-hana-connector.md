---
title: Przenoszenia danych z programu SAP HANA przy użyciu fabryki danych Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposobu przenoszenia danych z programu SAP HANA przy użyciu fabryki danych Azure.
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
ms.openlocfilehash: 0149b15fdfbd9fd7a3c9f9c099db9d505d27d1c5
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34623064"
---
# <a name="move-data-from-sap-hana-using-azure-data-factory"></a>Przenoszenie danych z SAP HANA przy użyciu fabryki danych Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1 — ogólnie dostępna](data-factory-sap-hana-connector.md)
> * [Wersja 2 — wersja zapoznawcza](../connector-sap-hana.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory, która jest ogólnie dostępna (GA). Jeśli używasz wersji 2 usługi fabryka danych, która jest w wersji zapoznawczej, zobacz [SAP HANA łącznika w wersji 2](../connector-sap-business-warehouse.md).

W tym artykule opisano sposób używania działania kopiowania w fabryce danych Azure do przenoszenia danych z lokalnego SAP HANA. Opiera się na [działań przepływu danych](data-factory-data-movement-activities.md) artykułu, który przedstawia ogólny przegląd przenoszenia danych z działania kopiowania.

Wszystkie obsługiwanych ujścia magazynu danych można skopiować danych z lokalnego magazynu danych SAP HANA. Lista magazynów danych obsługiwane jako wychwytywanie przez działanie kopiowania, zobacz [obsługiwane magazyny danych](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabeli. Fabryka danych obsługują obecnie tylko danych przenoszenie, z SAP HANA do innych magazynów danych, ale nie do przenoszenia danych z innych magazynów danych do SAP HANA.

## <a name="supported-versions-and-installation"></a>Obsługiwane wersje i instalacji
Ten łącznik obsługuje dowolnej wersji bazy danych SAP HANA. Obsługuje ona kopiowanie danych z tabel wierszy i kolumn, za pomocą zapytań SQL i HANA modelach informacji (takich jak widoki analityczne i obliczeń).

Aby umożliwić łączność z wystąpieniem SAP HANA, należy zainstalować następujące składniki:
- **Brama zarządzania danymi**: łączenie z danymi lokalnymi obsługuje usługi fabryka danych magazynów (w tym SAP HANA) przy użyciu składnika o nazwie brama zarządzania danymi. Informacje na temat bramy zarządzania danymi i szczegółowe instrukcje dotyczące konfigurowania bramy, zobacz [przenoszenie danych między danymi lokalnymi magazynu do magazynu danych w chmurze](data-factory-move-data-between-onprem-and-cloud.md) artykułu. Nawet jeśli SAP HANA znajduje się na maszynie wirtualnej platformy Azure IaaS (VM), wymagana jest brama. Można zainstalować bramę na tej samej maszyny Wirtualnej do przechowywania danych lub w innej maszyny Wirtualnej, tak długo, jak bramy można połączyć z bazą danych.
- **Sterownik SAP HANA ODBC** na komputerze bramy. Możesz pobrać sterownik SAP HANA ODBC z [Centrum pobierania oprogramowania SAP](https://support.sap.com/swdc). Wyszukiwanie ze słowem kluczowym **SAP HANA klienta dla systemu Windows**. 

## <a name="getting-started"></a>Wprowadzenie
Można utworzyć potok z działania kopiowania, który przenosi dane z magazynu danych SAP HANA lokalnymi przy użyciu różnych narzędzi/interfejsów API. 

- Najprostszym sposobem, aby utworzyć potok jest użycie **kreatora kopiowania**. Zobacz [samouczek: tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) szybkie przewodnik dotyczący tworzenia potoku za pomocą Kreatora kopiowania danych. 
- Umożliwia także następujące narzędzia do tworzenia potoku: **portalu Azure**, **programu Visual Studio**, **programu Azure PowerShell**, **szablonu usługi Azure Resource Manager**, **interfejs API .NET**, i **interfejsu API REST**. Zobacz [samouczek działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) instrukcje krok po kroku utworzyć potok z działaniem kopiowania. 

Czy można użyć narzędzia i interfejsy API, należy wykonać następujące kroki, aby utworzyć potok, który przenosi dane z magazynu danych źródła do ujścia magazynu danych:

1. Utwórz **połączone usługi** Aby połączyć dane wejściowe i wyjściowe są przechowywane w fabryce danych.
2. Utwórz **zestawów danych** do reprezentowania danych wejściowych i wyjściowych operacji kopiowania. 
3. Utwórz **potoku** aktywnością kopiowania zestawu danych jako dane wejściowe i zestawu danych jako dane wyjściowe. 

Korzystając z kreatora, definicje JSON do tych jednostek fabryki danych (połączone usługi, zestawy danych i potoki) są tworzone automatycznie dla Ciebie. Korzystając z narzędzi/API (z wyjątkiem interfejs API .NET), należy zdefiniować tych jednostek fabryki danych w formacie JSON.  Dla przykładu z definicji JSON dla jednostek fabryki danych, które są używane do skopiowania danych SAP HANA lokalnych, zobacz [przykład JSON: kopiowanie danych z programu SAP HANA do obiektów Blob platformy Azure](#json-example-copy-data-from-sap-hana-to-azure-blob) sekcji tego artykułu. 

Poniższe sekcje zawierają szczegółowe informacje o właściwości JSON, które są używane do definiowania jednostek fabryki danych określonej do magazynu danych SAP HANA:

## <a name="linked-service-properties"></a>Połączona usługa właściwości
Poniższa tabela zawiera opis specyficzne dla usługi SAP HANA połączone elementy JSON.

Właściwość | Opis | Dozwolone wartości | Wymagane
-------- | ----------- | -------------- | --------
serwer | Nazwa serwera, na którym znajduje się z wystąpieniem SAP HANA. Jeśli serwer używa portu dostosowane, określ `server:port`. | ciąg | Yes
Typ authenticationType | Typ uwierzytelniania. | Ciąg. "Basic" lub "Windows" | Yes 
nazwa użytkownika | Nazwa użytkownika, który ma dostęp do serwera SAP | ciąg | Yes
hasło | Hasło dla użytkownika. | ciąg | Yes
gatewayName | Nazwa bramy, która powinna być używana przez usługi fabryka danych nawiązywania połączenia z lokalnym wystąpieniem SAP HANA. | ciąg | Yes
encryptedCredential | Ciąg zaszyfrowane poświadczenia. | ciąg | Nie

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby uzyskać pełną listę sekcje & właściwości dostępne do definiowania zestawów danych, zobacz [Tworzenie zbiorów danych](data-factory-create-datasets.md) artykułu. Sekcje zawierają informacje, takie jak struktury, dostępności i zasad zestawu danych JSON są podobne dla wszystkich typów obiektów dataset (Azure SQL, obiektów blob platformy Azure, Azure tabeli itp.).

**TypeProperties** sekcja jest różne dla każdego typu zestawu danych i zawiera informacje o lokalizacji danych w magazynie danych. Nie ma żadnych właściwości specyficzne dla typu obsługiwane dla zestawu danych SAP HANA typu **RelationalTable**. 


## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Pełną listę sekcje & właściwości dostępne do definiowania działań, zobacz [tworzenie potoków](data-factory-create-pipelines.md) artykułu. Właściwości, takie jak nazwa, opis i tabel wejściowych i wyjściowych, czy zasady są dostępne dla wszystkich typów działań.

Właściwości dostępne w **typeProperties** sekcji działania zależne od każdego typu działania. Dla działania kopiowania różnią się w zależności od typów źródeł i sink.

Gdy źródło w przypadku działania kopiowania jest typu **RelationalSource** (która obejmuje SAP HANA), w sekcji typeProperties dostępne są następujące właściwości:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| query | Określa zapytanie SQL do odczytywania danych z wystąpieniem SAP HANA. | Zapytanie SQL. | Yes |

## <a name="json-example-copy-data-from-sap-hana-to-azure-blob"></a>Przykład JSON: kopiowanie danych z programu SAP HANA do obiektów Blob platformy Azure
Poniższy przykład zawiera definicje JSON, których można utworzyć potok przy użyciu [portalu Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) lub [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub [programu Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). W tym przykładzie pokazano, jak można skopiować danych z lokalnego SAP HANA do magazynu obiektów Blob Azure. Jednak dane mogą być kopiowane **bezpośrednio** do dowolnego wychwytywanie wymienione [tutaj](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania w fabryce danych Azure.  

> [!IMPORTANT]
> W tym przykładzie przedstawiono fragmenty kodu JSON. Zawiera instrukcje krok po kroku dotyczące tworzenia fabryki danych. Zobacz [przenoszenie danych między lokalizacji lokalnej i w chmurze](data-factory-move-data-between-onprem-and-cloud.md) artykułu, aby uzyskać instrukcje krok po kroku.

Przykład zawiera następujące obiekty fabryki danych:

1. Połączonej usługi typu [SapHana](#linked-service-properties).
2. Połączonej usługi typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Dane wejściowe [dataset](data-factory-create-datasets.md) typu [RelationalTable](#dataset-properties).
4. Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [potoku](data-factory-create-pipelines.md) z działaniem kopii, która używa [RelationalSource](#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Przykład kopiuje dane z wystąpieniem SAP HANA obiektów blob platformy Azure co godzinę. Właściwości JSON używane w te przykłady są opisane w sekcjach poniżej próbek.

Pierwszym krokiem konfiguracji bramy zarządzania danymi. Instrukcje znajdują się w [przenoszenie danych między lokalizacji lokalnej i w chmurze](data-factory-move-data-between-onprem-and-cloud.md) artykułu.

### <a name="sap-hana-linked-service"></a>SAP HANA połączone usługi
To połączone usługi łączy wystąpieniem SAP HANA fabryki danych. Właściwość type ma ustawioną **SapHana**. Sekcja typeProperties zawiera informacje o połączeniu dla wystąpienia programu SAP HANA.

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

### <a name="sap-hana-input-dataset"></a>Zestaw danych wejściowych SAP HANA

Ten zestaw danych definiuje zestaw danych SAP HANA. Ustaw typ fabryki danych zestawu danych do **RelationalTable**. Obecnie nie określisz żadnych właściwości określonego typu dla zestawu danych SAP HANA. Zapytania w definicji działania kopiowania Określa, jakie dane do odczytu z wystąpieniem SAP HANA. 

Ustawienie właściwości zewnętrznych true informuje o usługi fabryka danych czy tabeli zewnętrznej dla fabryki danych i nie jest generowany przez działanie w fabryce danych.

Częstotliwość i interwał właściwości definiuje harmonogramu. W takim przypadku dane są odczytywane z wystąpieniem SAP HANA co godzinę. 

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
Ten zestaw danych określa wyjściowego zestawu danych obiektów Blob platformy Azure. Właściwość type ma ustawioną AzureBlob. Sekcja typeProperties zawiera, gdzie są przechowywane dane skopiowane z wystąpieniem SAP HANA. Dane są zapisywane do nowego obiektu blob co godzinę (częstotliwość: godziny, interwał: 1). Ścieżka folderu dla obiekt blob jest dynamicznie obliczane na podstawie czasu rozpoczęcia wycinek, który jest przetwarzana. Ścieżka folderu używa rok, miesiąc, dzień i godziny części czas rozpoczęcia.

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


### <a name="pipeline-with-copy-activity"></a>W potoku z działanie kopiowania

Potok zawiera działanie kopiowania, który jest skonfigurowany do używania wejściowe i wyjściowe zestawy danych i jest zaplanowane co godzinę. W definicji JSON potoku **źródła** ustawiono typ **RelationalSource** (dla źródła SAP HANA) i **zbiornika** ustawiono typ **BlobSink**. Określony dla zapytania SQL **zapytania** właściwości wybiera dane w ostatniej godziny do skopiowania.

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


### <a name="type-mapping-for-sap-hana"></a>Mapowanie typu dla SAP HANA
Jak wspomniano w [działań przepływu danych](data-factory-data-movement-activities.md) artykułu, automatyczne konwersje z typów źródła do zbiornika typy z następujących rozwinięcie wykonuje działania kopiowania:

1. Konwertowanie typów natywnych źródła na typ architektury .NET
2. Konwertowanie na typ macierzysty ujścia typ architektury .NET

Podczas przenoszenia danych z programu SAP HANA, następujące mapowania są używane do typów .NET z typów SAP HANA.

SAP HANA typu | Typ na podstawie .NET
------------- | ---------------
TINYINT | Bajtów
SMALLINT | Int16
INT | Int32
BIGINT | Int64
RZECZYWISTE | Pojedyncze
O PODWÓJNEJ PRECYZJI | Pojedyncze
DECIMAL | Decimal
WARTOŚĆ LOGICZNA | Bajtów
VARCHAR | Ciąg
NVARCHAR | Ciąg
CLOB | Byte[]
ALPHANUM | Ciąg
OBIEKT BLOB | Byte[]
DATE | DateTime
CZAS | Zakres czasu
ZNACZNIK CZASU | DateTime
SECONDDATE | DateTime

## <a name="known-limitations"></a>Znane ograniczenia
Podczas kopiowania danych z programu SAP HANA istnieje kilka znane ograniczenia:

- Ciągi NVARCHAR są zaokrąglane do maksymalnie 4000 znaków Unicode
- SMALLDECIMAL nie jest obsługiwane.
- VARBINARY nie jest obsługiwane.
- Prawidłowe daty należą do zakresu od 30-1899/12, a 9999-12/31

## <a name="map-source-to-sink-columns"></a>Obiekt sink kolumn mapy źródła
Aby uzyskać informacje dotyczące mapowania kolumn w zestawie źródła danych do kolumn w zestawie danych zbiornika, zobacz [mapowania kolumnach dataset w fabryce danych Azure](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Odczyt powtarzalny ze źródłami relacyjnymi
Podczas kopiowania danych z danych relacyjnych przechowuje, należy pamiętać, aby uniknąć niezamierzone wyniki powtarzalności. Fabryka danych Azure możesz ponownie ręcznie wycinek. Można również skonfigurować zasady ponawiania dla zestawu danych, aby wycinek jest uruchamiany ponownie, gdy wystąpi błąd. Podczas wycinek zostanie uruchomiona ponownie w obu przypadkach, należy się upewnić, że te same dane jest do odczytu niezależnie od tego, ile razy wycinek jest uruchamiany. Zobacz [Repeatable odczytywać relacyjne źródła](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)

## <a name="performance-and-tuning"></a>Wydajność i dostrajania
Zobacz [wydajności działania kopiowania & dostrajanie przewodnik](data-factory-copy-activity-performance.md) Aby dowiedzieć się więcej o kluczowych czynników tego wydajność wpływ przenoszenia danych (działanie kopiowania) w usłudze fabryka danych Azure i zoptymalizować ją na różne sposoby.
