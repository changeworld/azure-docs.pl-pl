---
title: Przenoszenie danych ze magazynów danych ODBC
description: Dowiedz się, jak przenosić dane z magazynów danych ODBC przy użyciu usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: ad70a598-c031-4339-a883-c6125403cb76
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/19/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: e1735c2d2ed107f7ec65d68a6826267ee83a93f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281394"
---
# <a name="move-data-from-odbc-data-stores-using-azure-data-factory"></a>Przenoszenie danych z magazynów danych ODBC przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](data-factory-odbc-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-odbc.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [łącznik ODBC w wersji 2](../connector-odbc.md).


W tym artykule wyjaśniono, jak używać działania kopiowania w usłudze Azure Data Factory do przenoszenia danych z lokalnego magazynu danych ODBC. Opiera się na [działaniach przenoszenia danych,](data-factory-data-movement-activities.md) który przedstawia ogólny przegląd przenoszenia danych z działaniem kopiowania.

Można skopiować dane z magazynu danych ODBC do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych obsługiwanych jako pochłaniacze przez działanie kopiowania, zobacz tabelę [Obsługiwane magazyny danych.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) Fabryka danych obsługuje obecnie tylko przenoszenie danych z magazynu danych ODBC do innych magazynów danych, ale nie do przenoszenia danych z innych magazynów danych do magazynu danych ODBC.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="enabling-connectivity"></a>Włączanie łączności
Usługa Data Factory obsługuje łączenie się z lokalnymi źródłami ODBC przy użyciu bramy zarządzania danymi. Zobacz [przenoszenie danych między lokalizacjami lokalnymi a chmurą,](data-factory-move-data-between-onprem-and-cloud.md) aby dowiedzieć się więcej o bramie zarządzania danymi i instrukcjach krok po kroku dotyczących konfigurowania bramy. Użyj bramy, aby połączyć się z magazynem danych ODBC, nawet jeśli jest on hostowany w maszynie wirtualnej usługi Azure IaaS.

Bramę można zainstalować na tym samym komputerze lokalnym lub maszynie wirtualnej platformy Azure jako magazyn danych ODBC. Jednak zaleca się zainstalowanie bramy na osobnym komputerze/maszynie wirtualnej usługi Azure IaaS, aby uniknąć rywalizacji o zasoby i zapewnić lepszą wydajność. Po zainstalowaniu bramy na oddzielnym komputerze, urządzenie powinno mieć dostęp do komputera z magazynu danych ODBC.

Oprócz bramy zarządzania danymi należy również zainstalować sterownik ODBC dla magazynu danych na komputerze bramy.

> [!NOTE]
> Aby uzyskać wskazówki dotyczące rozwiązywania problemów związanych z połączeniem/bramą, zobacz [Rozwiązywanie problemów z bramą.](data-factory-data-management-gateway.md#troubleshooting-gateway-issues)

## <a name="getting-started"></a>Wprowadzenie
Można utworzyć potok z działaniem kopiowania, który przenosi dane z magazynu danych ODBC przy użyciu różnych narzędzi/interfejsów API.

Najprostszym sposobem utworzenia potoku jest użycie **Kreatora kopiowania**. Zobacz [Samouczek: Tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) w celu szybkiego instruktażu w sprawie tworzenia potoku przy użyciu kreatora kopiowania danych.

Do utworzenia potoku można również użyć następujących narzędzi: **Visual Studio**, **Azure PowerShell**, **szablon usługi Azure Resource Manager**, **.NET API**i REST **API**. Zobacz [Kopiowanie samouczka aktywności,](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) aby uzyskać instrukcje krok po kroku, aby utworzyć potok z działaniem kopiowania.

Niezależnie od tego, czy są używane narzędzia, czy interfejsy API, wykonaj następujące kroki, aby utworzyć potok, który przenosi dane ze źródłowego magazynu danych do magazynu danych ujścia:

1. Tworzenie **połączonych usług** w celu połączenia magazynów danych wejściowych i wyjściowych z fabryką danych.
2. Tworzenie **zestawów danych** do reprezentowania danych wejściowych i wyjściowych dla operacji kopiowania.
3. Utwórz **potok** z działaniem kopiowania, które przyjmuje zestaw danych jako dane wejściowe i zestaw danych jako dane wyjściowe.

Korzystając z kreatora, definicje JSON dla tych jednostek usługi Data Factory (połączone usługi, zestawy danych i potok) są tworzone automatycznie dla Ciebie. Korzystając z narzędzi/interfejsów API (z wyjątkiem interfejsu API platformy .NET), można zdefiniować te jednostki fabryki danych przy użyciu formatu JSON. W przypadku przykładu z definicjami JSON dla jednostek usługi Data Factory, które są używane do kopiowania danych z magazynu danych ODBC, zobacz [przykład JSON: Kopiowanie danych z magazynu danych ODBC do sekcji Azure Blob](#json-example-copy-data-from-odbc-data-store-to-azure-blob) w tym artykule.

Poniższe sekcje zawierają szczegółowe informacje o właściwościach JSON, które są używane do definiowania jednostek usługi Data Factory specyficznych dla magazynu danych ODBC:

## <a name="linked-service-properties"></a>Połączone właściwości usługi
Poniższa tabela zawiera opis elementów JSON specyficznych dla usługi połączonej ODBC.

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| type |Właściwość typu musi być ustawiona na: **OnPremisesOdbc** |Tak |
| Parametry połączenia |Część poświadczeń nieuzyskanych w ciągu połączenia i opcjonalne zaszyfrowane poświadczenia. Zobacz przykłady w poniższych sekcjach. <br/><br/>Można określić parametry połączenia `"Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;"`za pomocą wzorca, takiego jak , lub użyć systemu `"DSN=<name of the DSN>;"` DSN (nazwa źródła danych), który został skonfigurowany na komputerze bramy (nadal trzeba odpowiednio określić część poświadczeń w połączonej usłudze). |Tak |
| poświadczenia |Część poświadczeń dostępu ciągu połączenia określona w formacie właściwości-wartość specyficzne dla sterownika. Przykład: `"Uid=<user ID>;Pwd=<password>;RefreshToken=<secret refresh token>;"`. |Nie |
| authenticationType |Typ uwierzytelniania używany do łączenia się z magazynem danych ODBC. Możliwe wartości to: Anonimowy i Podstawowy. |Tak |
| userName |Określ nazwę użytkownika, jeśli używasz uwierzytelniania podstawowego. |Nie |
| hasło |Określ hasło dla konta użytkownika, które zostało określone dla nazwy użytkownika. |Nie |
| nazwa bramy |Nazwa bramy, której usługa Data Factory powinna używać do łączenia się z magazynem danych ODBC. |Tak |

### <a name="using-basic-authentication"></a>Korzystanie z uwierzytelniania podstawowego

```json
{
    "name": "odbc",
    "properties":
    {
        "type": "OnPremisesOdbc",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
            "userName": "username",
            "password": "password",
            "gatewayName": "mygateway"
        }
    }
}
```
### <a name="using-basic-authentication-with-encrypted-credentials"></a>Korzystanie z uwierzytelniania podstawowego przy użyciu zaszyfrowanych poświadczeń
Poświadczenia można szyfrować przy użyciu polecenia cmdlet [New-AzDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) (1.0 wersja programu Azure PowerShell) lub [New-AzureDataFactoryEncryptValue](https://msdn.microsoft.com/library/dn834940.aspx) (0.9 lub wcześniejsza wersja programu Azure PowerShell).

```json
{
    "name": "odbc",
    "properties":
    {
        "type": "OnPremisesOdbc",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=myserver.database.windows.net; Database=TestDatabase;;EncryptedCredential=eyJDb25uZWN0...........................",
            "gatewayName": "mygateway"
        }
    }
}
```

### <a name="using-anonymous-authentication"></a>Korzystanie z uwierzytelniania anonimowego

```json
{
    "name": "odbc",
    "properties":
    {
        "type": "OnPremisesOdbc",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "connectionString": "Driver={SQL Server};Server={servername}.database.windows.net; Database=TestDatabase;",
            "credential": "UID={uid};PWD={pwd}",
            "gatewayName": "mygateway"
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby uzyskać pełną listę sekcji & właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [Tworzenie zestawów danych.](data-factory-create-datasets.md) Sekcje, takie jak struktura, dostępność i zasady zestawu danych JSON są podobne dla wszystkich typów zestawów danych (Azure SQL, Azure blob, tabela platformy Azure itp.).

Sekcja **typeProperties** jest inna dla każdego typu zestawu danych i zawiera informacje o lokalizacji danych w magazynie danych. Sekcja typeProperties dla zestawu danych typu **RelationalTable** (która zawiera zestaw danych ODBC) ma następujące właściwości

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| tableName |Nazwa tabeli w magazynie danych ODBC. |Tak |

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Aby uzyskać pełną listę sekcji & właściwości dostępne do definiowania działań, zobacz [tworzenie potoków](data-factory-create-pipelines.md) artykułu. Właściwości, takie jak nazwa, opis, tabele danych wejściowych i wyjściowych oraz zasady są dostępne dla wszystkich typów działań.

Właściwości dostępne w sekcji **typeProperties** działania z drugiej strony różnią się w zależności od typu działania. W przypadku działania kopiowania różnią się one w zależności od typów źródeł i pochłaniacze.

W działaniu kopiowania, gdy źródło jest typu **RelationalSource** (który zawiera ODBC), następujące właściwości są dostępne w sekcji typeProperties:

| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| query |Użyj kwerendy niestandardowej, aby odczytać dane. |Ciąg zapytania SQL. Na przykład: wybierz * z MyTable. |Tak |


## <a name="json-example-copy-data-from-odbc-data-store-to-azure-blob"></a>Przykład JSON: kopiowanie danych z magazynu danych ODBC do obiektu Blob platformy Azure
W tym przykładzie przedstawiono definicje JSON, których można użyć do utworzenia potoku przy użyciu [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub programu Azure [PowerShell.](data-factory-copy-activity-tutorial-using-powershell.md) Pokazano w nim, jak skopiować dane ze źródła ODBC do usługi Azure Blob Storage. Jednak dane mogą być kopiowane do dowolnego ujścia podane [w tym miejscu](data-factory-data-movement-activities.md#supported-data-stores-and-formats) przy użyciu działania kopiowania w usłudze Azure Data Factory.

Próbka ma następujące jednostki fabryki danych:

1. Połączona usługa typu [OnPremisesOdbc](#linked-service-properties).
2. Połączona usługa typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [RelationalTable](#dataset-properties).
4. Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. [Potok](data-factory-create-pipelines.md) z działaniem kopiowania, który używa [RelationalSource](#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Przykładowy kopiuje dane z kwerendy spowodować magazyn danych ODBC do obiektu blob co godzinę. Właściwości JSON używane w tych próbkach są opisane w sekcjach następujących po próbkach.

W pierwszym kroku skonfiguruj bramę zarządzania danymi. Instrukcje znajdują się w [ruchomych danych między lokalizacjami lokalnymi i](data-factory-move-data-between-onprem-and-cloud.md) w chmurze artykułu.

**Usługa połączona z ODBC** W tym przykładzie użyto uwierzytelniania podstawowego. Zobacz [sekcję usługi połączonej ODBC](#linked-service-properties) dla różnych typów uwierzytelniania, których można użyć.

```json
{
    "name": "OnPremOdbcLinkedService",
    "properties":
    {
        "type": "OnPremisesOdbc",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
            "userName": "username",
            "password": "password",
            "gatewayName": "mygateway"
        }
    }
}
```

**Usługa połączona usługi Usługi Azure Storage**

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

**Wejściowy zestaw danych ODBC**

W przykładzie przyjęto założenie, że utworzono tabelę "MyTable" w bazie danych ODBC i zawiera kolumnę o nazwie "timestampcolumn" dla danych szeregów czasowych.

Ustawienie "zewnętrzne": "true" informuje usługę Data Factory, że zestaw danych jest zewnętrzny dla fabryki danych i nie jest produkowany przez działanie w fabryce danych.

```json
{
    "name": "ODBCDataSet",
    "properties": {
        "published": false,
        "type": "RelationalTable",
        "linkedServiceName": "OnPremOdbcLinkedService",
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

**Wyjściowy zestaw danych obiektów blob platformy Azure**

Dane są zapisywane w nowym obiekcie blob co godzinę (częstotliwość: godzina, interwał: 1). Ścieżka folderu obiektu blob jest dynamicznie oceniana na podstawie czasu rozpoczęcia przetwarzanego plasterka. Ścieżka folderu używa części czasu rozpoczęcia z roku, miesiąca, dnia i godziny.

```json
{
    "name": "AzureBlobOdbcDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/odbc/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Kopiowanie działania w potoku ze źródłem ODBC (RelationalSource) i ujściem obiektu Blob (BlobSink)**

Potok zawiera działanie kopiowania, który jest skonfigurowany do korzystania z tych danych wejściowych i wyjściowych i jest zaplanowane do uruchomienia co godzinę. W definicji JSON potoku typ **źródła** jest ustawiony na **RelationalSource** i typ **ujścia** jest ustawiony na **BlobSink**. Kwerenda SQL określona dla właściwości **kwerendy** wybiera dane w ciągu ostatniej godziny do skopiowania.

```json
{
    "name": "CopyODBCToBlob",
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
                        "name": "OdbcDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOdbcDataSet"
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
                "name": "OdbcToBlob"
            }
        ],
        "start": "2016-06-01T18:00:00Z",
        "end": "2016-06-01T19:00:00Z"
    }
}
```
### <a name="type-mapping-for-odbc"></a>Mapowanie typów dla ODBC
Jak wspomniano w artykule działania związane z [przenoszeniem danych,](data-factory-data-movement-activities.md) działanie kopiowania wykonuje automatyczne konwersje typów z typów źródłowych do typów ujścia z następującym podejściem dwuetapowym:

1. Konwertowanie z natywnych typów źródeł na typ .NET
2. Konwertowanie z typu .NET na natywny typ ujścia

Podczas przenoszenia danych z magazynów danych ODBC typy danych ODBC są mapowane na typy .NET, jak wspomniano w temacie [Mapowania typów danych ODBC.](https://msdn.microsoft.com/library/cc668763.aspx)

## <a name="map-source-to-sink-columns"></a>Mapowanie źródła do kolumn ujścia
Aby dowiedzieć się więcej o mapowaniu kolumn w źródłowym zestawie danych na kolumny w zestawie danych ujścia, zobacz [Mapowanie kolumn zestawu danych w usłudze Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Powtarzalny odczyt ze źródeł relacyjnych
Podczas kopiowania danych z relacyjnych magazynów danych należy pamiętać o powtarzalności, aby uniknąć niezamierzonych wyników. W usłudze Azure Data Factory można ponownie uruchomić plasterek ręcznie. Można również skonfigurować zasady ponawiania dla zestawu danych, tak aby plasterek został ponownie uruchomny w przypadku wystąpienia błędu. Gdy plasterek jest uruchamiany ponownie w obu przypadkach, należy upewnić się, że te same dane są odczytywane bez względu na to, ile razy jest uruchamiany plasterek. Zobacz [Powtarzalny odczyt ze źródeł relacyjnych](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="troubleshoot-connectivity-issues"></a>Rozwiązywanie problemów z łącznością
Aby rozwiązać problemy z połączeniem, użyj karty **Diagnostyka** **programu Menedżer konfiguracji bramy zarządzania danymi**.

1. Uruchom **program Data Management Gateway Configuration Manager**. Można uruchomić bezpośrednio (lub) uruchomić "C:\Program Files\Microsoft Data Management Gateway\1.0\Shared\ConfigManager.exe" bezpośrednio (lub) **wyszukać** łącze do aplikacji **Bramy zarządzania danymi firmy Microsoft,** jak pokazano na poniższej ilustracji.

    ![Brama wyszukiwania](./media/data-factory-odbc-connector/search-gateway.png)
2. Przełącz się do karty **Diagnostyka.**

    ![Diagnostyka bramy](./media/data-factory-odbc-connector/data-factory-gateway-diagnostics.png)
3. Wybierz **typ** magazynu danych (usługa połączona).
4. Określ **uwierzytelnianie** i wprowadź **poświadczenia** (lub) wprowadź **parametry połączenia** używane do łączenia się z magazynem danych.
5. Kliknij **przycisk Testuj połączenie,** aby przetestować połączenie z magazynem danych.

## <a name="performance-and-tuning"></a>Wydajność i strojenie
Zobacz [Kopiowanie wydajności działania & dostrajania przewodnika,](data-factory-copy-activity-performance.md) aby dowiedzieć się więcej o kluczowych czynnikach, które wpływają na wydajność przenoszenia danych (Copy Activity) w usłudze Azure Data Factory i na różne sposoby optymalizacji.
