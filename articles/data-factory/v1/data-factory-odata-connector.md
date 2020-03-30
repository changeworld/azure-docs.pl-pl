---
title: Przenoszenie danych ze źródeł OData
description: Dowiedz się, jak przenosić dane ze źródeł OData przy użyciu usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: de28fa56-3204-4546-a4df-21a21de43ed7
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 95f92d4e5616d7754c355610685701a8e089b84e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265911"
---
# <a name="move-data-from-an-odata-source-using-azure-data-factory"></a>Przenoszenie danych ze źródła OData przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](data-factory-odata-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-odata.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [Łącznik OData w wersji 2](../connector-odata.md).


W tym artykule wyjaśniono, jak używać działania kopiowania w usłudze Azure Data Factory do przenoszenia danych ze źródła OData. Opiera się na [działaniach przenoszenia danych,](data-factory-data-movement-activities.md) który przedstawia ogólny przegląd przenoszenia danych z działaniem kopiowania.

Można skopiować dane ze źródła OData do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych obsługiwanych jako pochłaniacze przez działanie kopiowania, zobacz tabelę [Obsługiwane magazyny danych.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) Fabryka danych obsługuje obecnie tylko przenoszenie danych ze źródła OData do innych magazynów danych, ale nie do przenoszenia danych z innych magazynów danych do źródła OData.

## <a name="supported-versions-and-authentication-types"></a>Obsługiwane wersje i typy uwierzytelniania
Ten łącznik OData obsługuje OData w wersji 3.0 i 4.0 i można kopiować dane zarówno z chmury OData i lokalnych źródeł OData. W przypadku tej ostatniej należy zainstalować bramę zarządzania danymi. Aby uzyskać szczegółowe informacje na temat bramy zarządzania danymi, zobacz [Przenoszenie danych między środowiskiem lokalnym a chmurą.](data-factory-move-data-between-onprem-and-cloud.md)

Poniższe typy uwierzytelniania są obsługiwane:

* Aby uzyskać dostęp do kanału informacyjnego OData **w chmurze,** można użyć anonimowego, podstawowego (nazwa użytkownika i hasło) lub uwierzytelniania OAuth opartego na usłudze Azure Active Directory.
* Aby uzyskać dostęp do **lokalnego** źródła danych OData, można użyć anonimowego, podstawowego (nazwa użytkownika i hasło) lub uwierzytelniania systemu Windows.

## <a name="getting-started"></a>Wprowadzenie
Można utworzyć potok z działaniem kopiowania, który przenosi dane ze źródła OData przy użyciu różnych narzędzi/interfejsów API.

Najprostszym sposobem utworzenia potoku jest użycie **Kreatora kopiowania**. Zobacz [Samouczek: Tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) w celu szybkiego instruktażu w sprawie tworzenia potoku przy użyciu kreatora kopiowania danych.

Do utworzenia potoku można również użyć następujących narzędzi: **Visual Studio**, **Azure PowerShell**, **szablon usługi Azure Resource Manager**, **.NET API**i REST **API**. Zobacz [Kopiowanie samouczka aktywności,](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) aby uzyskać instrukcje krok po kroku, aby utworzyć potok z działaniem kopiowania.

Niezależnie od tego, czy są używane narzędzia, czy interfejsy API, wykonaj następujące kroki, aby utworzyć potok, który przenosi dane ze źródłowego magazynu danych do magazynu danych ujścia:

1. Tworzenie **połączonych usług** w celu połączenia magazynów danych wejściowych i wyjściowych z fabryką danych.
2. Tworzenie **zestawów danych** do reprezentowania danych wejściowych i wyjściowych dla operacji kopiowania.
3. Utwórz **potok** z działaniem kopiowania, które przyjmuje zestaw danych jako dane wejściowe i zestaw danych jako dane wyjściowe.

Korzystając z kreatora, definicje JSON dla tych jednostek usługi Data Factory (połączone usługi, zestawy danych i potok) są tworzone automatycznie dla Ciebie. Korzystając z narzędzi/interfejsów API (z wyjątkiem interfejsu API platformy .NET), można zdefiniować te jednostki fabryki danych przy użyciu formatu JSON.  W przypadku przykładu z definicjami JSON dla jednostek usługi Data Factory, które są używane do kopiowania danych ze źródła OData, zobacz [przykład JSON: Kopiowanie danych ze źródła OData do usługi Azure Blob](#json-example-copy-data-from-odata-source-to-azure-blob) sekcji tego artykułu.

Poniższe sekcje zawierają szczegółowe informacje o właściwościach JSON, które są używane do definiowania jednostek fabryki danych specyficznych dla źródła OData:

## <a name="linked-service-properties"></a>Właściwości usługi połączonej
Poniższa tabela zawiera opis elementów JSON specyficznych dla usługi połączonej OData.

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| type |Właściwość typu musi być ustawiona na: **OData** |Tak |
| url |Adres URL usługi OData. |Tak |
| authenticationType |Typ uwierzytelniania używanego do łączenia się ze źródłem OData. <br/><br/> W przypadku odat w chmurze możliwe wartości to Anonimowe, Podstawowe i OAuth (uwaga Usługa Azure Data Factory obsługuje obecnie tylko usługę Azure Active Directory opartą na OAuth). <br/><br/> W przypadku lokalnego odata możliwe wartości są anonimowe, podstawowe i windows. |Tak |
| nazwa użytkownika |Określ nazwę użytkownika, jeśli używasz uwierzytelniania podstawowego. |Tak (tylko w przypadku korzystania z uwierzytelniania podstawowego) |
| hasło |Określ hasło dla konta użytkownika określonego dla nazwy użytkownika. |Tak (tylko w przypadku korzystania z uwierzytelniania podstawowego) |
| autoryzowaneCrededential |Jeśli używasz OAuth, kliknij przycisk **Autoryzuj** w Kreatorze kopiowania fabryki danych lub edytorze i wprowadź poświadczenia, a następnie wartość tej właściwości zostanie wygenerowana automatycznie. |Tak (tylko w przypadku korzystania z uwierzytelniania OAuth) |
| nazwa bramy |Nazwa bramy, której usługa Data Factory powinna używać do łączenia się z lokalną usługą OData. Określ tylko, czy są kopiowane dane z lokalnego źródła OData. |Nie |

### <a name="using-basic-authentication"></a>Korzystanie z uwierzytelniania podstawowego
```json
{
    "name": "inputLinkedService",
    "properties":
    {
        "type": "OData",
        "typeProperties":
        {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Basic",
            "username": "username",
            "password": "password"
        }
    }
}
```

### <a name="using-anonymous-authentication"></a>Korzystanie z uwierzytelniania anonimowego
```json
{
    "name": "ODataLinkedService",
    "properties":
    {
        "type": "OData",
        "typeProperties":
        {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        }
    }
}
```

### <a name="using-windows-authentication-accessing-on-premises-odata-source"></a>Korzystanie z uwierzytelniania systemu Windows uzyskiwania dostępu do lokalnego źródła OData
```json
{
    "name": "inputLinkedService",
    "properties":
    {
        "type": "OData",
        "typeProperties":
        {
            "url": "<endpoint of on-premises OData source e.g. Dynamics CRM>",
            "authenticationType": "Windows",
            "username": "domain\\user",
            "password": "password",
            "gatewayName": "mygateway"
        }
    }
}
```

### <a name="using-oauth-authentication-accessing-cloud-odata-source"></a>Korzystanie z uwierzytelniania OAuth uzyskującego dostęp do źródła OData w chmurze
```json
{
    "name": "inputLinkedService",
    "properties":
    {
        "type": "OData",
            "typeProperties":
        {
            "url": "<endpoint of cloud OData source e.g. https://<tenant>.crm.dynamics.com/XRMServices/2011/OrganizationData.svc>",
            "authenticationType": "OAuth",
            "authorizedCredential": "<auto generated by clicking the Authorize button on UI>"
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby uzyskać pełną listę sekcji & właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [Tworzenie zestawów danych.](data-factory-create-datasets.md) Sekcje, takie jak struktura, dostępność i zasady zestawu danych JSON są podobne dla wszystkich typów zestawów danych (Azure SQL, Azure blob, tabela platformy Azure itp.).

Sekcja **typeProperties** jest inna dla każdego typu zestawu danych i zawiera informacje o lokalizacji danych w magazynie danych. Sekcja typeProperties dla zestawu danych typu **ODataResource** (który zawiera zestaw danych OData) ma następujące właściwości

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| ścieżka |Ścieżka do zasobu OData |Nie |

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Aby uzyskać pełną listę sekcji & właściwości dostępne do definiowania działań, zobacz [tworzenie potoków](data-factory-create-pipelines.md) artykułu. Właściwości, takie jak nazwa, opis, tabele danych wejściowych i wyjściowych oraz zasady są dostępne dla wszystkich typów działań.

Właściwości dostępne w sekcji typeProperties działania z drugiej strony różnią się w zależności od typu działania. W przypadku działania kopiowania różnią się one w zależności od typów źródeł i pochłaniacze.

Gdy źródło jest typu **RelationalSource** (który zawiera OData) następujące właściwości są dostępne w sekcji typeProperties:

| Właściwość | Opis | Przykład | Wymagany |
| --- | --- | --- | --- |
| query |Użyj kwerendy niestandardowej, aby odczytać dane. |"?$select=Nazwa, Opis&$top=5" |Nie |

## <a name="type-mapping-for-odata"></a>Mapowanie typów dla OData
Jak wspomniano w [artykule działania przenoszenia danych,](data-factory-data-movement-activities.md) Copy activity wykonuje automatyczne konwersje typów z typów źródłowych do typów ujścia z następującym podejściem dwuetapowym.

1. Konwertowanie z natywnych typów źródeł na typ .NET
2. Konwertowanie z typu .NET na natywny typ ujścia

Podczas przenoszenia danych z OData, następujące mapowania są używane z typów OData do typu .NET.

| Typ danych protokołu OData | Typ .NET |
| --- | --- |
| Edm.Binary (Edm.Binary) |Bajt[] |
| Edm.Boolean |Wartość logiczna |
| Edm.Byte (Edm.Byte) |Bajt[] |
| Edm.DateTime |DateTime |
| Edm.Decimal (Edm.Decimal) |Wartość dziesiętna |
| Edm.Double |Double |
| Edm.Single (Edm.Pojedyncze) |Single |
| Edm.Guid (Edm.Guid) |Guid (identyfikator GUID) |
| Edm.Int16 (Edm.Int16) |Int16 |
| Edm.Int32 |Int32 |
| Edm.Int64 |Int64 |
| Edm.SByte (Edm.SByte) |Int16 |
| Edm.String |Ciąg |
| Edm.Time (Edm.Time) |przedział_czasu |
| Edm.DateTimeOffset |Datetimeoffset |

> [!Note]
> OData złożonych typów danych, takich jak obiekt nie są obsługiwane.

## <a name="json-example-copy-data-from-odata-source-to-azure-blob"></a>Przykład JSON: Kopiowanie danych ze źródła OData do obiektu Blob platformy Azure
W tym przykładzie przedstawiono przykładowe definicje JSON, których można użyć do utworzenia potoku przy użyciu [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub programu Azure [PowerShell.](data-factory-copy-activity-tutorial-using-powershell.md) Pokazują one, jak skopiować dane ze źródła OData do usługi Azure Blob Storage. Jednak dane mogą być kopiowane do dowolnego ujścia podane [w tym miejscu](data-factory-data-movement-activities.md#supported-data-stores-and-formats) przy użyciu działania kopiowania w usłudze Azure Data Factory. W przykładzie są dostępne następujące jednostki Data Factory:

1. Połączona usługa typu [OData](#linked-service-properties).
2. Połączona usługa typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [ODataResource](#dataset-properties).
4. Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. [Potok](data-factory-create-pipelines.md) z działaniem kopiowania, który używa [RelationalSource](#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Przykładowy kopiuje dane z kwerend względem źródła OData do obiektu blob platformy Azure co godzinę. Właściwości JSON używane w tych próbkach są opisane w sekcjach następujących po próbkach.

**Usługa połączona odata:** W tym przykładzie użyto uwierzytelniania anonimowego. Zobacz [sekcję usługi połączonej OData](#linked-service-properties) dla różnych typów uwierzytelniania, których można użyć.

```json
{
    "name": "ODataLinkedService",
    "properties":
    {
        "type": "OData",
        "typeProperties":
        {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        }
    }
}
```

**Usługa połączona z usługą Azure Storage:**

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

**Zestaw danych wejściowych OData:**

Ustawienie "zewnętrzne": "true" informuje usługę Data Factory, że zestaw danych jest zewnętrzny dla fabryki danych i nie jest produkowany przez działanie w fabryce danych.

```json
{
    "name": "ODataDataset",
    "properties":
    {
        "type": "ODataResource",
        "typeProperties":
        {
            "path": "Products"
        },
        "linkedServiceName": "ODataLinkedService",
        "structure": [],
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "retryInterval": "00:01:00",
            "retryTimeout": "00:10:00",
            "maximumRetry": 3
        }
    }
}
```

Określanie **ścieżki** w definicji zestawu danych jest opcjonalne.

**Zestaw danych wyjściowych obiektów blob platformy Azure:**

Dane są zapisywane w nowym obiekcie blob co godzinę (częstotliwość: godzina, interwał: 1). Ścieżka folderu obiektu blob jest dynamicznie oceniana na podstawie czasu rozpoczęcia przetwarzanego plasterka. Ścieżka folderu używa części czasu rozpoczęcia z roku, miesiąca, dnia i godziny.

```json
{
    "name": "AzureBlobODataDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/odata/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Kopiowanie działania w potoku ze źródłem OData i ujściem obiektów Blob:**

Potok zawiera działanie kopiowania, który jest skonfigurowany do korzystania z wejściowych i wyjściowych zestawów danych i jest zaplanowane do uruchomienia co godzinę. W definicji JSON potoku typ **źródła** jest ustawiony na **RelationalSource** i typ **ujścia** jest ustawiony na **BlobSink**. Kwerenda SQL określona dla właściwości **kwerendy** wybiera najnowsze (najnowsze) dane ze źródła OData.

```json
{
    "name": "CopyODataToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "?$select=Name, Description&$top=5",
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "ODataDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobODataDataSet"
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
                "name": "ODataToBlob"
            }
        ],
        "start": "2017-02-01T18:00:00Z",
        "end": "2017-02-03T19:00:00Z"
    }
}
```

Określanie **kwerendy** w definicji potoku jest opcjonalne. **Adres URL** używany przez usługę Data Factory do pobierania danych to: adres URL określony w połączonej usłudze (wymagana) + ścieżka określona w zestawie danych (opcjonalnie) + kwerenda w potoku (opcjonalnie).

### <a name="type-mapping-for-odata"></a>Mapowanie typów dla OData
Jak wspomniano w artykule działania związane z [przenoszeniem danych,](data-factory-data-movement-activities.md) działanie kopiowania wykonuje automatyczne konwersje typów z typów źródłowych do typów ujścia z następującym podejściem dwuetapowym:

1. Konwertowanie z natywnych typów źródeł na typ .NET
2. Konwertowanie z typu .NET na natywny typ ujścia

Podczas przenoszenia danych z magazynów danych OData typy danych OData są mapowane na typy .NET.

## <a name="map-source-to-sink-columns"></a>Mapowanie źródła do kolumn ujścia
Aby dowiedzieć się więcej o mapowaniu kolumn w źródłowym zestawie danych na kolumny w zestawie danych ujścia, zobacz [Mapowanie kolumn zestawu danych w usłudze Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Powtarzalny odczyt ze źródeł relacyjnych
Podczas kopiowania danych z relacyjnych magazynów danych należy pamiętać o powtarzalności, aby uniknąć niezamierzonych wyników. W usłudze Azure Data Factory można ponownie uruchomić plasterek ręcznie. Można również skonfigurować zasady ponawiania dla zestawu danych, tak aby plasterek został ponownie uruchomny w przypadku wystąpienia błędu. Gdy plasterek jest uruchamiany ponownie w obu przypadkach, należy upewnić się, że te same dane są odczytywane bez względu na to, ile razy jest uruchamiany plasterek. Zobacz [Powtarzalny odczyt ze źródeł relacyjnych](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Wydajność i strojenie
Zobacz [Kopiowanie wydajności działania & dostrajania przewodnika,](data-factory-copy-activity-performance.md) aby dowiedzieć się więcej o kluczowych czynnikach, które wpływają na wydajność przenoszenia danych (Copy Activity) w usłudze Azure Data Factory i na różne sposoby optymalizacji.
