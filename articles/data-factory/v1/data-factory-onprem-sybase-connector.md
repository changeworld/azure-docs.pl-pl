---
title: Przenoszenie danych z platformy Sybase przy użyciu usługi Azure Data Factory
description: Dowiedz się, jak przenieść dane z bazy danych Sybase przy użyciu usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: b379ee10-0ff5-4974-8c87-c95f82f1c5c6
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/02/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: cefa0c15dd50f95780034dcb63f888a2e1c6b65e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281251"
---
# <a name="move-data-from-sybase-using-azure-data-factory"></a>Przenoszenie danych z platformy Sybase przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](data-factory-onprem-sybase-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-sybase.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [Łącznik Sybase w wersji 2](../connector-sybase.md).

W tym artykule wyjaśniono, jak używać działania kopiowania w usłudze Azure Data Factory do przenoszenia danych z lokalnej bazy danych Sybase. Opiera się na [działaniach przenoszenia danych,](data-factory-data-movement-activities.md) który przedstawia ogólny przegląd przenoszenia danych z działaniem kopiowania.

Można skopiować dane z lokalnego magazynu danych Sybase do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych obsługiwanych jako pochłaniacze przez działanie kopiowania, zobacz tabelę [Obsługiwane magazyny danych.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) Fabryka danych obsługuje obecnie tylko przenoszenie danych z magazynu danych Sybase do innych magazynów danych, ale nie do przenoszenia danych z innych magazynów danych do magazynu danych Sybase. 

## <a name="prerequisites"></a>Wymagania wstępne
Usługa Data Factory obsługuje łączenie się z lokalnymi źródłami Sybase przy użyciu bramy zarządzania danymi. Zobacz [przenoszenie danych między lokalizacjami lokalnymi a chmurą,](data-factory-move-data-between-onprem-and-cloud.md) aby dowiedzieć się więcej o bramie zarządzania danymi i instrukcjach krok po kroku dotyczących konfigurowania bramy.

Brama jest wymagana, nawet jeśli baza danych Sybase jest hostowana na maszynie wirtualnej usługi Azure IaaS. Bramę można zainstalować na tej samej maszynie wirtualnej IaaS co magazyn danych lub na innej maszynie wirtualnej, o ile brama może łączyć się z bazą danych.

> [!NOTE]
> Aby uzyskać wskazówki dotyczące rozwiązywania problemów związanych z połączeniem/bramą, zobacz [Rozwiązywanie problemów z bramą.](data-factory-data-management-gateway.md#troubleshooting-gateway-issues)

## <a name="supported-versions-and-installation"></a>Obsługiwane wersje i instalacja
Aby brama zarządzania danymi połączyła się z bazą danych Sybase, należy zainstalować [dostawcę danych dla sybase iAnywhere.Data.SQLAnywhere](https://go.microsoft.com/fwlink/?linkid=324846) 16 lub wyższym w tym samym systemie co brama zarządzania danymi. 

SAP Sybase SQL Anywhere (ASA) w wersji 16 lub wyższej jest obsługiwany; IQ i ASE nie są obsługiwane.

## <a name="getting-started"></a>Wprowadzenie
Można utworzyć potok z działaniem kopiowania, który przenosi dane z lokalnego magazynu danych Cassandra przy użyciu różnych narzędzi/interfejsów API. 

- Najprostszym sposobem utworzenia potoku jest użycie **Kreatora kopiowania**. Zobacz [Samouczek: Tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) w celu szybkiego instruktażu w sprawie tworzenia potoku przy użyciu kreatora kopiowania danych. 
- Do utworzenia potoku można również użyć następujących narzędzi: **Visual Studio**, **Azure PowerShell**, **szablon usługi Azure Resource Manager**, **.NET API**i REST **API**. Zobacz [Kopiowanie samouczka aktywności,](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) aby uzyskać instrukcje krok po kroku, aby utworzyć potok z działaniem kopiowania. 

Niezależnie od tego, czy są używane narzędzia, czy interfejsy API, wykonaj następujące kroki, aby utworzyć potok, który przenosi dane ze źródłowego magazynu danych do magazynu danych ujścia:

1. Tworzenie **połączonych usług** w celu połączenia magazynów danych wejściowych i wyjściowych z fabryką danych.
2. Tworzenie **zestawów danych** do reprezentowania danych wejściowych i wyjściowych dla operacji kopiowania. 
3. Utwórz **potok** z działaniem kopiowania, które przyjmuje zestaw danych jako dane wejściowe i zestaw danych jako dane wyjściowe. 

Korzystając z kreatora, definicje JSON dla tych jednostek usługi Data Factory (połączone usługi, zestawy danych i potok) są tworzone automatycznie dla Ciebie. Korzystając z narzędzi/interfejsów API (z wyjątkiem interfejsu API platformy .NET), można zdefiniować te jednostki fabryki danych przy użyciu formatu JSON.  W przypadku przykładu z definicjami JSON dla jednostek usługi Data Factory, które są używane do kopiowania danych z lokalnego magazynu danych Sybase, zobacz [przykład JSON: Kopiowanie danych z Sybase do usługi Azure Blob](#json-example-copy-data-from-sybase-to-azure-blob) sekcji tego artykułu. 

Poniższe sekcje zawierają szczegółowe informacje o właściwościach JSON, które są używane do definiowania jednostek usługi Data Factory specyficznych dla magazynu danych Sybase:

## <a name="linked-service-properties"></a>Połączone właściwości usługi
Poniższa tabela zawiera opis elementów JSON specyficznych dla usługi połączonej Sybase.

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| type |Właściwość typu musi być ustawiona na: **OnPremisesSybase** |Tak |
| serwer |Nazwa serwera Sybase. |Tak |
| database |Nazwa bazy danych Sybase. |Tak |
| Schematu |Nazwa schematu w bazie danych. |Nie |
| authenticationType |Typ uwierzytelniania używany do łączenia się z bazą danych Sybase. Możliwe wartości to: Anonimowy, Podstawowy i Windows. |Tak |
| nazwa użytkownika |Określ nazwę użytkownika, jeśli używasz uwierzytelniania podstawowego lub uwierzytelniania systemu Windows. |Nie |
| hasło |Określ hasło dla konta użytkownika określonego dla nazwy użytkownika. |Nie |
| nazwa bramy |Nazwa bramy, której usługa Data Factory powinna używać do łączenia się z lokalną bazą danych Sybase. |Tak |

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby uzyskać pełną listę sekcji & właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [Tworzenie zestawów danych.](data-factory-create-datasets.md) Sekcje, takie jak struktura, dostępność i zasady zestawu danych JSON są podobne dla wszystkich typów zestawów danych (Azure SQL, Azure blob, tabela platformy Azure itp.).

Sekcja typeProperties jest inna dla każdego typu zestawu danych i zawiera informacje o lokalizacji danych w magazynie danych. Sekcja **typeProperties** dla zestawu danych typu **RelationalTable** (która zawiera zestaw danych Sybase) ma następujące właściwości:

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| tableName |Nazwa tabeli w wystąpieniu bazy danych Sybase, do których odwołuje się usługa. |Nie (jeśli określono **kwerendę** **relacyjnego źródła)** |

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Aby uzyskać pełną listę sekcji & właściwości dostępne do definiowania działań, zobacz [Tworzenie potoków](data-factory-create-pipelines.md) artykułu. Właściwości, takie jak nazwa, opis, tabele danych wejściowych i wyjściowych oraz zasady są dostępne dla wszystkich typów działań.

Mając na uwadze, właściwości dostępne w sekcji typeProperties działania różnią się w zależności od typu działania. W przypadku działania kopiowania różnią się one w zależności od typów źródeł i pochłaniacze.

Gdy źródło jest typu **RelationalSource** (który zawiera Sybase), następujące właściwości są dostępne w **sekcji typeProperties:**

| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| query |Użyj kwerendy niestandardowej, aby odczytać dane. |Ciąg zapytania SQL. Na przykład: wybierz * z MyTable. |Nie (jeśli określono **nazwa tabeli** **zestawu danych)** |


## <a name="json-example-copy-data-from-sybase-to-azure-blob"></a>Przykład JSON: Kopiowanie danych z bazy Sybase do obiektu Blob platformy Azure
Poniższy przykład zawiera przykładowe definicje JSON, których można użyć do utworzenia potoku przy użyciu [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub programu Azure [PowerShell.](data-factory-copy-activity-tutorial-using-powershell.md) Pokazują one, jak skopiować dane z bazy danych Sybase do usługi Azure Blob Storage. Jednak dane mogą być kopiowane do dowolnego ujścia podane [w tym miejscu](data-factory-data-movement-activities.md#supported-data-stores-and-formats) przy użyciu działania kopiowania w usłudze Azure Data Factory.   

Próbka ma następujące jednostki fabryki danych:

1. Połączona usługa typu [OnPremisesSybase](data-factory-onprem-sybase-connector.md#linked-service-properties).
2. Lubiana usługa typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [RelationalTable](data-factory-onprem-sybase-connector.md#dataset-properties).
4. Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. [Potok](data-factory-create-pipelines.md) z działaniem kopiowania, który używa [RelationalSource](data-factory-onprem-sybase-connector.md#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Przykładowy kopiuje dane z wyniku kwerendy w bazie danych Sybase do obiektu blob co godzinę. Właściwości JSON używane w tych próbkach są opisane w sekcjach następujących po próbkach.

W pierwszej kolejności skonfiguruj bramę zarządzania danymi. Instrukcje znajdują się w [ruchomych danych między lokalizacjami lokalnymi i](data-factory-move-data-between-onprem-and-cloud.md) w chmurze artykułu.

**Usługa połączona z Sybase:**

```JSON
{
    "name": "OnPremSybaseLinkedService",
    "properties": {
        "type": "OnPremisesSybase",
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

```JSON
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

**Zestaw danych wejściowych Sybase:**

W przykładzie przyjęto założenie, że utworzono tabelę "MyTable" w sybase i zawiera kolumnę o nazwie "sygnatura czasowa" dla danych szeregów czasowych.

Ustawienie "zewnętrzne": true informuje usługę Data Factory, że ten zestaw danych jest zewnętrzny dla fabryki danych i nie jest produkowany przez działanie w fabryce danych. Należy zauważyć, że **typ** połączonej usługi jest ustawiony na: **RelationalTable**.

```JSON
{
    "name": "SybaseDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremSybaseLinkedService",
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

Dane są zapisywane w nowym obiekcie blob co godzinę (częstotliwość: godzina, interwał: 1). Ścieżka folderu obiektu blob jest dynamicznie oceniana na podstawie czasu rozpoczęcia przetwarzanego plasterka. Ścieżka folderu używa części czasu rozpoczęcia z roku, miesiąca, dnia i godziny.

```JSON
{
    "name": "AzureBlobSybaseDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/sybase/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

Potok zawiera działanie kopiowania, który jest skonfigurowany do korzystania z wejściowych i wyjściowych zestawów danych i jest zaplanowane do uruchomienia co godzinę. W definicji JSON potoku typ **źródła** jest ustawiony na **RelationalSource** i typ **ujścia** jest ustawiony na **BlobSink**. Kwerenda SQL określona dla właściwości **kwerendy** wybiera dane z dba. Tabela zamówień w bazie danych.

```JSON
{
    "name": "CopySybaseToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "select * from DBA.Orders"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "inputs": [
                    {
                        "name": "SybaseDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobSybaseDataSet"
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
                "name": "SybaseToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```

## <a name="type-mapping-for-sybase"></a>Mapowanie typu dla Sybase
Jak wspomniano w działania [przenoszenia danych](data-factory-data-movement-activities.md) artykułu Kopiowanie działania wykonuje automatyczne konwersje typów z typów źródłowych do zlewu typów z następujących 2-stopniowe podejście:

1. Konwertowanie z natywnych typów źródeł na typ .NET
2. Konwertowanie z typu .NET na natywny typ ujścia

Sybase obsługuje typy T-SQL i T-SQL. Aby uzyskać tabelę mapowania z typów sql do typu .NET, zobacz artykuł [programu Azure SQL Connector.](data-factory-azure-sql-connector.md)

## <a name="map-source-to-sink-columns"></a>Mapowanie źródła do kolumn ujścia
Aby dowiedzieć się więcej o mapowaniu kolumn w źródłowym zestawie danych na kolumny w zestawie danych ujścia, zobacz [Mapowanie kolumn zestawu danych w usłudze Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Powtarzalny odczyt ze źródeł relacyjnych
Podczas kopiowania danych z relacyjnych magazynów danych należy pamiętać o powtarzalności, aby uniknąć niezamierzonych wyników. W usłudze Azure Data Factory można ponownie uruchomić plasterek ręcznie. Można również skonfigurować zasady ponawiania dla zestawu danych, tak aby plasterek został ponownie uruchomny w przypadku wystąpienia błędu. Gdy plasterek jest uruchamiany ponownie w obu przypadkach, należy upewnić się, że te same dane są odczytywane bez względu na to, ile razy jest uruchamiany plasterek. Zobacz [Powtarzalny odczyt ze źródeł relacyjnych](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Wydajność i strojenie
Zobacz [Kopiowanie wydajności działania & dostrajania przewodnika,](data-factory-copy-activity-performance.md) aby dowiedzieć się więcej o kluczowych czynnikach, które wpływają na wydajność przenoszenia danych (Copy Activity) w usłudze Azure Data Factory i na różne sposoby optymalizacji.
