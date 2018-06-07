---
title: Przenoszenie danych ze źródła OData | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposobu przenoszenia danych z źródła OData przy użyciu fabryki danych Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: de28fa56-3204-4546-a4df-21a21de43ed7
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: b87ed8b9d9b43de81bfe4173d117d9f1e2bd7abd
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34622010"
---
# <a name="move-data-from-a-odata-source-using-azure-data-factory"></a>Przenoszenie danych źródła z OData przy użyciu fabryki danych Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1 — ogólnie dostępna](data-factory-odata-connector.md)
> * [Wersja 2 — wersja zapoznawcza](../connector-odata.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory, która jest ogólnie dostępna (GA). Jeśli używasz wersji 2 usługi fabryka danych, która jest w wersji zapoznawczej, zobacz [łącznika OData w wersji 2](../connector-odata.md).


W tym artykule opisano sposób korzystania działanie kopiowania w fabryce danych Azure, aby przenieść dane ze źródła danych OData. Opiera się na [działań przepływu danych](data-factory-data-movement-activities.md) artykułu, który przedstawia ogólny przegląd przenoszenia danych z działania kopiowania.

Wszystkie obsługiwanych ujścia magazynu danych można skopiować danych ze źródła danych OData. Lista magazynów danych obsługiwane jako wychwytywanie przez działanie kopiowania, zobacz [obsługiwane magazyny danych](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabeli. Fabryka danych aktualnie obsługuje tylko dane przenoszenie, ze źródła danych OData do innych magazynów danych, ale nie do przenoszenia danych z innych magazynów danych do źródła danych OData. 

## <a name="supported-versions-and-authentication-types"></a>Obsługiwane wersje i typy uwierzytelniania
Ten łącznik OData obsługuje OData w wersji 3.0 i 4.0, a można skopiować danych z obu chmury OData i lokalnego źródła OData. W przypadku drugiego nagłówka musisz zainstalować bramę zarządzania danymi. Zobacz [przenoszenie danych między lokalnymi i w chmurze](data-factory-move-data-between-onprem-and-cloud.md) artykułu, aby uzyskać więcej informacji dotyczących bramy zarządzania danymi.

Poniżej uwierzytelniania są obsługiwane typy:

* Aby uzyskać dostęp do **chmury** źródła danych OData, można użyć anonimowe, podstawowe (nazwa użytkownika i hasło) lub Azure Active Directory na podstawie uwierzytelniania OAuth.
* Aby uzyskać dostęp do **lokalnymi** źródła danych OData, możesz użyć anonimowe, basic (nazwa użytkownika i hasło) lub uwierzytelniania systemu Windows.

## <a name="getting-started"></a>Wprowadzenie
Można utworzyć potok z działania kopiowania, który przenosi dane ze źródła danych OData przy użyciu różnych narzędzi/interfejsów API.

Najprostszym sposobem, aby utworzyć potok jest użycie **kreatora kopiowania**. Zobacz [samouczek: tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) szybkie przewodnik dotyczący tworzenia potoku za pomocą Kreatora kopiowania danych.

Umożliwia także następujące narzędzia do tworzenia potoku: **portalu Azure**, **programu Visual Studio**, **programu Azure PowerShell**, **szablonu usługi Azure Resource Manager**, **interfejs API .NET**, i **interfejsu API REST**. Zobacz [samouczek działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) instrukcje krok po kroku utworzyć potok z działaniem kopiowania. 

Czy można użyć narzędzia i interfejsy API, należy wykonać następujące kroki, aby utworzyć potok, który przenosi dane z magazynu danych źródła do ujścia magazynu danych: 

1. Utwórz **połączone usługi** Aby połączyć dane wejściowe i wyjściowe są przechowywane w fabryce danych.
2. Utwórz **zestawów danych** do reprezentowania danych wejściowych i wyjściowych operacji kopiowania. 
3. Utwórz **potoku** aktywnością kopiowania zestawu danych jako dane wejściowe i zestawu danych jako dane wyjściowe. 

Korzystając z kreatora, definicje JSON do tych jednostek fabryki danych (połączone usługi, zestawy danych i potoki) są tworzone automatycznie dla Ciebie. Korzystając z narzędzi/API (z wyjątkiem interfejs API .NET), należy zdefiniować tych jednostek fabryki danych w formacie JSON.  Dla przykładu z definicji JSON dla jednostek fabryki danych, które są używane do skopiowania danych ze źródła danych OData, zobacz [przykład JSON: kopiowanie danych z źródła OData do obiektów Blob platformy Azure](#json-example-copy-data-from-odata-source-to-azure-blob) sekcji tego artykułu. 

Poniższe sekcje zawierają szczegółowe informacje o właściwości JSON, które są używane do definiowania jednostek fabryki danych określonej do źródła OData:

## <a name="linked-service-properties"></a>Właściwości usługi połączonej
Poniższa tabela zawiera opis specyficzne dla usługi OData połączone elementy JSON.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| type |Właściwość type musi mieć ustawioną: **OData** |Yes |
| url |Adres URL usługi OData. |Yes |
| Typ authenticationType |Typ uwierzytelniania używany do nawiązania połączenia źródła OData. <br/><br/> Chmury OData możliwe wartości to anonimowe, podstawowe i OAuth (Uwaga obecnie tylko pomocy technicznej usługi fabryka danych Azure OAuth opartej na usłudze Azure Active Directory). <br/><br/> Dla protokołu OData lokalnymi możliwe wartości to anonimowe, podstawowe i systemu Windows. |Yes |
| nazwa użytkownika |Określ nazwę użytkownika, jeśli używasz uwierzytelniania podstawowego. |Tak (tylko wtedy, gdy używasz uwierzytelniania podstawowego) |
| hasło |Określ hasło dla konta użytkownika, określone nazwy użytkownika. |Tak (tylko wtedy, gdy używasz uwierzytelniania podstawowego) |
| authorizedCredential |Jeśli używasz uwierzytelniania OAuth, kliknij przycisk **autoryzacji** przycisku w kreatorze kopiowania fabryki danych lub edytorze, a następnie wprowadź Twoje poświadczenia wartość tej właściwości będzie wygenerowany automatycznie. |Tak (tylko wtedy, gdy używasz uwierzytelniania OAuth) |
| gatewayName |Nazwa bramy, która powinna być używana przez usługi fabryka danych nawiązać połączenia z lokalną usługą OData. Określ tylko, jeśli dane są kopiowane z lokalnego źródła OData. |Nie |

### <a name="using-basic-authentication"></a>Przy użyciu uwierzytelniania podstawowego
```json
{
    "name": "inputLinkedService",
    "properties":
    {
        "type": "OData",
            "typeProperties":
        {
            "url": "http://services.odata.org/OData/OData.svc",
            "authenticationType": "Basic",
            "username": "username",
            "password": "password"
        }
    }
}
```

### <a name="using-anonymous-authentication"></a>Przy użyciu uwierzytelniania anonimowego
```json
{
    "name": "ODataLinkedService",
        "properties":
    {
        "type": "OData",
        "typeProperties":
        {
            "url": "http://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        }
    }
}
```

### <a name="using-windows-authentication-accessing-on-premises-odata-source"></a>Przy użyciu uwierzytelniania systemu Windows podczas uzyskiwania dostępu do lokalnego źródła OData
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

### <a name="using-oauth-authentication-accessing-cloud-odata-source"></a>Przy użyciu OAuth uwierzytelniania podczas uzyskiwania dostępu do chmury źródło OData
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
Aby uzyskać pełną listę sekcje & właściwości dostępne do definiowania zestawów danych, zobacz [Tworzenie zbiorów danych](data-factory-create-datasets.md) artykułu. Sekcje zawierają informacje, takie jak struktury, dostępności i zasad zestawu danych JSON są podobne dla wszystkich typów obiektów dataset (Azure SQL, obiektów blob platformy Azure, Azure tabeli itp.).

**TypeProperties** sekcja jest różne dla każdego typu zestawu danych i zawiera informacje o lokalizacji danych w magazynie danych. TypeProperties sekcja dla zestawu danych typu **ODataResource** (w tym zestawie danych OData) ma następujące właściwości

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| ścieżka |Ścieżka do zasobu OData |Nie |

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Pełną listę sekcje & właściwości dostępne do definiowania działań, zobacz [tworzenie potoków](data-factory-create-pipelines.md) artykułu. Właściwości, takie jak nazwa, opis, dane wejściowe i wyjściowe tabel i zasady są dostępne dla wszystkich typów działań.

Właściwości, które są dostępne w sekcji typeProperties działania z drugiej strony zależą od każdego typu działania. Dla działania kopiowania różnią się w zależności od typów źródeł i sink.

Jeśli źródło jest typu **RelationalSource** (w tym OData) w sekcji typeProperties dostępne są następujące właściwości:

| Właściwość | Opis | Przykład | Wymagane |
| --- | --- | --- | --- |
| query |Użyj niestandardowych zapytania można odczytać danych. |"?$select=Name, Description&$top=5" |Nie |

## <a name="type-mapping-for-odata"></a>Mapowanie typu dla protokołu OData
Jak wspomniano w [działań przepływu danych](data-factory-data-movement-activities.md) artykułu, automatyczne konwersje z typów źródła do zbiornika typy z następujących rozwinięcie wykonuje działanie kopiowania.

1. Konwertowanie typów natywnych źródła na typ architektury .NET
2. Konwertowanie na typ macierzysty ujścia typ architektury .NET

Podczas przenoszenia danych z OData, następujące mapowania są używane typy OData do typ architektury .NET.

| Typ danych OData | Typ architektury .NET |
| --- | --- |
| Edm.Binary |Byte[] |
| Edm.Boolean |wartość logiczna |
| Edm.Byte |Byte[] |
| Edm.DateTime |DateTime |
| Edm.Decimal |Decimal |
| Edm.Double |O podwójnej precyzji |
| Edm.Single |Pojedyncze |
| Edm.Guid |Identyfikator GUID |
| Edm.Int16 |Int16 |
| Edm.Int32 |Int32 |
| Edm.Int64 |Int64 |
| Edm.SByte |Int16 |
| Edm.String |Ciąg |
| Edm.Time |Zakres czasu |
| Edm.DateTimeOffset |DateTimeOffset |

> [!Note]
> Typy złożone danych OData np. obiektu nie są obsługiwane.

## <a name="json-example-copy-data-from-odata-source-to-azure-blob"></a>Przykład JSON: kopiowanie danych z źródła OData do obiektów Blob platformy Azure
W poniższym przykładzie przedstawiono przykładowe definicje JSON, które można użyć, aby utworzyć potok przy użyciu [portalu Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) lub [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub [programu Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Przedstawiają sposób kopiowania danych ze źródła danych OData do magazynu obiektów Blob Azure. Jednak można skopiować danych do dowolnego wychwytywanie podane [tutaj](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania w fabryce danych Azure. Przykład zawiera następujące jednostek fabryki danych:

1. Połączonej usługi typu [OData](#linked-service-properties).
2. Połączonej usługi typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Dane wejściowe [dataset](data-factory-create-datasets.md) typu [ODataResource](#dataset-properties).
4. Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [potoku](data-factory-create-pipelines.md) z działaniem kopii, która używa [RelationalSource](#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Przykład kopiuje dane z zapytań względem źródła OData do obiektów blob platformy Azure co godzinę. Właściwości JSON używane w te przykłady są opisane w sekcjach poniżej próbek.

**OData połączonej usługi:** w tym przykładzie użyto uwierzytelnianie anonimowe. Zobacz [OData połączona usługa](#linked-service-properties) sekcji dla różnych typów uwierzytelniania, można użyć.

```json
{
    "name": "ODataLinkedService",
        "properties":
    {
        "type": "OData",
            "typeProperties":
        {
            "url": "http://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
            }
        }
}
```

**Połączonej usługi magazynu Azure:**

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

**Wejściowy zestaw danych OData:**

Ustawienie "external": "prawda" informuje usługi fabryka danych czy zestaw danych jest zewnętrzne do fabryki danych i nie jest generowany przez działanie w fabryce danych.

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

Określanie **ścieżki** w zestawie danych definicji jest opcjonalna.

**Azure Blob wyjściowy zestaw danych:**

Dane są zapisywane do nowego obiektu blob co godzinę (częstotliwość: godziny, interwał: 1). Ścieżka folderu dla obiekt blob jest dynamicznie obliczane na podstawie czasu rozpoczęcia wycinek, który jest przetwarzana. Ścieżka folderu używa rok, miesiąc, dzień i godziny części czas rozpoczęcia.

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


**Aktywność kopiowania w potoku z OData źródłowy i odbiorczy obiektów Blob:**

Potok zawiera działanie kopiowania, który jest skonfigurowany do używania wejściowe i wyjściowe zestawy danych i jest zaplanowane co godzinę. W definicji JSON potoku **źródła** ustawiono typ **RelationalSource** i **zbiornika** ustawiono typ **BlobSink**. Określony dla zapytania SQL **zapytania** właściwości wybiera (Najnowsza) najnowsze dane ze źródła OData.

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

Określanie **zapytania** w potoku definicji jest opcjonalna. **Adres URL** jest używany do pobierania danych usługi fabryka danych z: adres URL określony w połączonej usłudze (wymagane) + ścieżka zestawu danych (opcjonalnie) + zapytania w potoku (opcjonalnie).


### <a name="type-mapping-for-odata"></a>Mapowanie typu dla protokołu OData
Jak wspomniano w [działań przepływu danych](data-factory-data-movement-activities.md) artykułu, wykonuje działanie kopiowania automatyczne konwersje z typów źródła do zbiornika typów o następujące podejście krok 2:

1. Konwertowanie typów natywnych źródła na typ architektury .NET
2. Konwertowanie na typ macierzysty ujścia typ architektury .NET

Jeśli przechowuje przenoszenie danych ze źródła danych OData, typy danych OData są mapowane do typów .NET.

## <a name="map-source-to-sink-columns"></a>Obiekt sink kolumn mapy źródła
Aby uzyskać informacje dotyczące mapowania kolumn w zestawie źródła danych do kolumn w zestawie danych zbiornika, zobacz [mapowania kolumnach dataset w fabryce danych Azure](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Odczyt powtarzalny ze źródłami relacyjnymi
Podczas kopiowania danych z danych relacyjnych przechowuje, należy pamiętać, aby uniknąć niezamierzone wyniki powtarzalności. Fabryka danych Azure możesz ponownie ręcznie wycinek. Można również skonfigurować zasady ponawiania dla zestawu danych, aby wycinek jest uruchamiany ponownie, gdy wystąpi błąd. Podczas wycinek zostanie uruchomiona ponownie w obu przypadkach, należy się upewnić, że te same dane jest do odczytu niezależnie od tego, ile razy wycinek jest uruchamiany. Zobacz [Repeatable odczytywać źródłami relacyjnymi](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Wydajność i dostrajania
Zobacz [wydajności działania kopiowania & dostrajanie przewodnik](data-factory-copy-activity-performance.md) Aby dowiedzieć się więcej o kluczowych czynników tego wydajność wpływ przenoszenia danych (działanie kopiowania) w usłudze fabryka danych Azure i zoptymalizować ją na różne sposoby.
