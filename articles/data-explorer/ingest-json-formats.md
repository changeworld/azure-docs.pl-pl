---
title: Pozyskiwanie danych w formacie JSON w usłudze Azure Eksplorator danych
description: Dowiedz się więcej o sposobie pozyskiwania danych w formacie JSON w usłudze Azure Eksplorator danych.
author: orspod
ms.author: orspodek
ms.reviewer: kerend
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: d293b76e004d693813a074cb8551a86cb3c0bec2
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2020
ms.locfileid: "76772332"
---
# <a name="ingest-json-formatted-sample-data-into-azure-data-explorer"></a>Zyskaj przykładowe dane JSON sformatowane do Eksplorator danych platformy Azure

W tym artykule opisano sposób pozyskiwania danych w formacie JSON w bazie danych Eksplorator danych platformy Azure. Zacznij od prostych przykładów nieprzetworzonego i zamapowanego JSON, przejdź do wielowierszowego kodu JSON, a następnie spróbuj wykonać bardziej złożone schematy JSON zawierające tablice i słowniki.  Przykłady opisują proces pozyskiwania danych w formacie JSON przy użyciu języka zapytań Kusto (KQL), C#lub Python. Polecenia sterowania `ingest` języka zapytań Kusto są wykonywane bezpośrednio w punkcie końcowym aparatu. W scenariuszach produkcyjnych pozyskiwanie jest wykonywane do usługi Zarządzanie danymi przy użyciu bibliotek klienta lub połączeń danych. Odczytaj dane pozyskiwania [przy użyciu biblioteki języka Python platformy azure Eksplorator danych](/azure/data-explorer/python-ingest-data) i pozyskaj [dane przy użyciu zestawu SDK usługi Azure Eksplorator danych .NET Standard](/azure/data-explorer/net-standard-ingest-data) w celu uzyskania informacji dotyczących pozyskiwania danych z tymi bibliotekami klientów.

## <a name="prerequisites"></a>Wymagania wstępne

[Klaster testowy i baza danych](create-cluster-database-portal.md)

## <a name="the-json-format"></a>Format JSON

Usługa Azure Eksplorator danych obsługuje dwa formaty plików JSON:
* `json`: kod JSON rozdzielony wierszem. Każdy wiersz w danych wejściowych ma dokładnie jeden rekord JSON.
* `multijson`: wielowierszowy kod JSON. Analizator ignoruje separatory wierszy i odczytuje rekord z poprzedniej pozycji do końca prawidłowego kodu JSON.

### <a name="ingest-and-map-json-formatted-data"></a>Pozyskiwanie i mapowanie danych w formacie JSON

Pozyskiwanie danych w formacie JSON wymaga określenia *formatu* przy użyciu [Właściwości](/azure/kusto/management/data-ingestion/index#ingestion-properties)pozyskiwania. Pozyskiwanie danych JSON wymaga [mapowania](/azure/kusto/management/mappings), które mapuje wpis źródła JSON do jego kolumny docelowej. Podczas pozyskiwania danych Użyj wstępnie zdefiniowanej właściwości pozyskiwania `jsonMappingReference` lub określ `jsonMapping`Właściwość pozyskiwania. W tym artykule zostanie użyta Właściwość pozyskiwania `jsonMappingReference`, która jest wstępnie zdefiniowana w tabeli używanej do pozyskiwania. W poniższych przykładach zaczniemy od pozyskania rekordów JSON jako danych pierwotnych do jednokolumnowej tabeli. Następnie użyjemy mapowania, aby pozyskiwać każdą właściwość do swojej mapowanej kolumny. 

### <a name="simple-json-example"></a>Prosty przykład JSON

Poniższy przykład to prosty kod JSON z płaską strukturą. Dane zawierają informacje o temperatury i wilgotności, zbierane przez kilka urządzeń. Każdy rekord jest oznaczony IDENTYFIKATORem i sygnaturą czasową.

```json
{
    "timestamp": "2019-05-02 15:23:50.0369439",
    "deviceId": "2945c8aa-f13e-4c48-4473-b81440bb5ca2",
    "messageId": "7f316225-839a-4593-92b5-1812949279b3",
    "temperature": 31.0301639051317,
    "humidity": 62.0791099602725
}
```

## <a name="ingest-raw-json-records"></a>Pozyskiwanie nieprzetworzonych rekordów JSON 

W tym przykładzie rekordy JSON są pozyskiwane jako dane pierwotne do pojedynczej tabeli kolumn. Manipulowanie danymi, korzystanie z zapytań i zasad aktualizacji odbywa się po pozyskaniu danych.

# <a name="kqltabkusto-query-language"></a>[KQL](#tab/kusto-query-language)

Użyj języka zapytań Kusto do pozyskiwania danych w formacie JSON.

1. Zaloguj się do witryny [https://dataexplorer.azure.com](https://dataexplorer.azure.com).

1. Wybierz pozycję **Dodaj klaster**.

1. W oknie dialogowym **Dodawanie klastra** wprowadź adres URL klastra w formularzu `https://<ClusterName>.<Region>.kusto.windows.net/`, a następnie wybierz pozycję **Dodaj**.

1. Wklej w poniższym poleceniu i wybierz polecenie **Uruchom** , aby utworzyć tabelę.

    ```Kusto
    .create table RawEvents (Event: dynamic)
    ```

    To zapytanie tworzy tabelę z pojedynczą kolumną `Event` o [dynamicznym](/azure/kusto/query/scalar-data-types/dynamic) typie danych.

1. Utwórz mapowanie JSON.

    ```Kusto
    .create table RawEvents ingestion json mapping 'RawEventMapping' '[{"column":"Event","path":"$"}]'
    ```

    To polecenie tworzy mapowanie i mapuje ścieżkę katalogu głównego JSON `$` do kolumny `Event`.

1. Pozyskaj dane do tabeli `RawEvents`.

    ```Kusto
    .ingest into table RawEvents h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=json, jsonMappingReference=RawEventMapping)
    ```

# <a name="ctabc-sharp"></a>[C#](#tab/c-sharp)

Służy C# do pozyskiwania danych w formacie nieprzetworzonym JSON.

1. Utwórz tabelę `RawEvents`.

    ```C#
    var kustoUri = "https://<ClusterName>.<Region>.kusto.windows.net:443/";
    var kustoConnectionStringBuilder =
        new KustoConnectionStringBuilder(ingestUri)
        {
            FederatedSecurity = true,
            InitialCatalog = database,
            UserID = user,
            Password = password,
            Authority = tenantId
        };
    var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder);

    var table = "RawEvents";
    var command =
        CslCommandGenerator.GenerateTableCreateCommand(
            table,
            new[]
            {
                Tuple.Create("Events", "System.Object"),
            });

    kustoClient.ExecuteControlCommand(command);
    ```

1. Utwórz mapowanie JSON.
    
    ```C#
    var tableMapping = "RawEventMapping";
    var command =
        CslCommandGenerator.GenerateTableJsonMappingCreateCommand(
            tableName,
            tableMapping,
            new[]
            {
                new JsonColumnMapping {ColumnName = "Events", JsonPath = "$"},
            });

    kustoClient.ExecuteControlCommand(command);
    ```
    To polecenie tworzy mapowanie i mapuje ścieżkę katalogu głównego JSON `$` do kolumny `Event`.

1. Pozyskaj dane do tabeli `RawEvents`.

    ```C#
    var ingestUri = "https://ingest-<ClusterName>.<Region>.kusto.windows.net:443/";
    var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
    var ingestConnectionStringBuilder =
        new KustoConnectionStringBuilder(ingestUri)
        {
            FederatedSecurity = true,
            InitialCatalog = database,
            UserID = user,
            Password = password,
            Authority = tenantId
        };
    var ingestClient = KustoIngestFactory.CreateQueuedIngestClient(ingestConnectionStringBuilder);
    
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.json,
            IngestionMappingReference = tableMapping
        };

    ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
    ```

> [!NOTE]
> Dane są agregowane zgodnie z [zasadami wsadowymi](/azure/kusto/concepts/batchingpolicy), co spowodowało opóźnienie wynoszące kilka minut.

# <a name="pythontabpython"></a>[Python](#tab/python)

Użyj języka Python do pozyskiwania danych w formacie nieprzetworzonym JSON.

1. Utwórz tabelę `RawEvents`.

    ```Python
    KUSTO_URI = "https://<ClusterName>.<Region>.kusto.windows.net:443/"
    KCSB_DATA = KustoConnectionStringBuilder.with_aad_device_authentication(KUSTO_URI, AAD_TENANT_ID)
    KUSTO_CLIENT = KustoClient(KCSB_DATA)
    TABLE = "RawEvents"

    CREATE_TABLE_COMMAND = ".create table " + TABLE + " (Events: dynamic)"
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_TABLE_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Utwórz mapowanie JSON.

    ```Python
    MAPPING = "RawEventMapping"
    CREATE_MAPPING_COMMAND = ".create table " + TABLE + " ingestion json mapping '" + MAPPING + """' '[{"column":"Event","path":"$"}]'"""
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_MAPPING_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Pozyskaj dane do tabeli `RawEvents`.

    ```Python
    INGEST_URI = "https://ingest-<ClusterName>.<Region>.kusto.windows.net:443/"
    KCSB_INGEST = KustoConnectionStringBuilder.with_aad_device_authentication(INGEST_URI, AAD_TENANT_ID)
    INGESTION_CLIENT = KustoIngestClient(KCSB_INGEST)
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.json, mappingReference=MAPPING)
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

    > [!NOTE]
    > Dane są agregowane zgodnie z [zasadami wsadowymi](/azure/kusto/concepts/batchingpolicy), co spowodowało opóźnienie wynoszące kilka minut.

---

## <a name="ingest-mapped-json-records"></a>Pozyskiwanie mapowanych rekordów JSON

W tym przykładzie pozyskasz dane rekordów JSON. Każda właściwość JSON jest zamapowana na pojedynczą kolumnę w tabeli. 

# <a name="kqltabkusto-query-language"></a>[KQL](#tab/kusto-query-language)

1. Utwórz nową tabelę zawierającą podobny schemat do danych wejściowych JSON. Użyjemy tej tabeli dla wszystkich poniższych przykładów i poleceń pozyskiwania. 

    ```Kusto
    .create table Events (Time: datetime, Device: string, MessageId: string, Temperature: double, Humidity: double)
    ```

1. Utwórz mapowanie JSON.

    ```Kusto
    .create table Events ingestion json mapping 'FlatEventMapping' '[{"column":"Time","path":"$.timestamp"},{"column":"Device","path":"$.deviceId"},{"column":"MessageId","path":"$.messageId"},{"column":"Temperature","path":"$.temperature"},{"column":"Humidity","path":"$.humidity"}]'
    ```

    W ramach tego mapowania zdefiniowanego przez schemat tabeli wpisy `timestamp` zostaną pozyskane do kolumny `Time` jako `datetime` typy danych.

1. Pozyskaj dane do tabeli `Events`.

    ```Kusto
    .ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=json, jsonMappingReference=FlatEventMapping)
    ```

    Plik "Simple. JSON" zawiera kilka rekordów JSON rozdzielonych wierszami. Format jest `json`, a mapowanie używane w poleceniu pozyskiwania to `FlatEventMapping` utworzony.

# <a name="ctabc-sharp"></a>[C#](#tab/c-sharp)

1. Utwórz nową tabelę zawierającą podobny schemat do danych wejściowych JSON. Użyjemy tej tabeli dla wszystkich poniższych przykładów i poleceń pozyskiwania. 

    ```C#
    var table = "Events";
    var command =
        CslCommandGenerator.GenerateTableCreateCommand(
            table,
            new[]
            {
                Tuple.Create("Time", "System.DateTime"),
                Tuple.Create("Device", "System.String"),
                Tuple.Create("MessageId", "System.String"),
                Tuple.Create("Temperature", "System.Double"),
                Tuple.Create("Humidity", "System.Double"),
            });

    kustoClient.ExecuteControlCommand(command);
    ```

1. Utwórz mapowanie JSON.

    ```C#
    var tableMapping = "FlatEventMapping";
    var command =
        CslCommandGenerator.GenerateTableJsonMappingCreateCommand(
            tableName,
            tableMapping,
            new[]
            {
                        new JsonColumnMapping {ColumnName = "Time", JsonPath = "$.timestamp"},
                        new JsonColumnMapping {ColumnName = "Device", JsonPath = "$.deviceId"},
                        new JsonColumnMapping {ColumnName = "MessageId", JsonPath = "$.messageId"},
                        new JsonColumnMapping {ColumnName = "Temperature", JsonPath = "$.temperature"},
                        new JsonColumnMapping {ColumnName = "Humidity", JsonPath = "$.humidity"},
            });

    kustoClient.ExecuteControlCommand(command);
    ```

    W ramach tego mapowania zdefiniowanego przez schemat tabeli wpisy `timestamp` zostaną pozyskane do kolumny `Time` jako `datetime` typy danych.    

1. Pozyskaj dane do tabeli `Events`.

    ```C#
    var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.json,
            IngestionMappingReference = tableMapping
        };

    ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
    ```

    Plik "Simple. JSON" zawiera kilka rekordów JSON rozdzielonych wierszami. Format jest `json`, a mapowanie używane w poleceniu pozyskiwania to `FlatEventMapping` utworzony.

# <a name="pythontabpython"></a>[Python](#tab/python)

1. Utwórz nową tabelę zawierającą podobny schemat do danych wejściowych JSON. Użyjemy tej tabeli dla wszystkich poniższych przykładów i poleceń pozyskiwania. 

    ```Python
    TABLE = "RawEvents"
    CREATE_TABLE_COMMAND = ".create table " + TABLE + " (Time: datetime, Device: string, MessageId: string, Temperature: double, Humidity: double)"
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_TABLE_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Utwórz mapowanie JSON.

    ```Python
    MAPPING = "FlatEventMapping"
    CREATE_MAPPING_COMMAND = ".create table Events ingestion json mapping '" + MAPPING + """' '[{"column":"Time","path":"$.timestamp"},{"column":"Device","path":"$.deviceId"},{"column":"MessageId","path":"$.messageId"},{"column":"Temperature","path":"$.temperature"},{"column":"Humidity","path":"$.humidity"}]'""" 
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_MAPPING_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Pozyskaj dane do tabeli `Events`.

    ```Python
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.json, mappingReference=MAPPING)
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

    Plik "Simple. JSON" zawiera kilka niepodzielonych wierszy rekordów JSON. Format jest `json`, a mapowanie używane w poleceniu pozyskiwania to `FlatEventMapping` utworzony.    
---

## <a name="ingest-multi-lined-json-records"></a>Pozyskiwanie wielowierszowych rekordów JSON

W tym przykładzie pozyskasz wielowierszowe rekordy JSON. Każda właściwość JSON jest zamapowana na pojedynczą kolumnę w tabeli. Plik "wieloline. JSON" zawiera kilka wcięć rekordów JSON. Format `multijson` informuje aparat do odczytywania rekordów przez strukturę JSON.

# <a name="kqltabkusto-query-language"></a>[KQL](#tab/kusto-query-language)

Pozyskaj dane do tabeli `Events`.

```Kusto
.ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/multilined.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=multijson, jsonMappingReference=FlatEventMapping)
```

# <a name="ctabc-sharp"></a>[C#](#tab/c-sharp)

Pozyskaj dane do tabeli `Events`.

```C#
var tableMapping = "FlatEventMapping";
var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/multilined.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
var properties =
    new KustoQueuedIngestionProperties(database, table)
    {
        Format = DataSourceFormat.multijson,
        IngestionMappingReference = tableMapping
    };

ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Pozyskaj dane do tabeli `Events`.

```Python
MAPPING = "FlatEventMapping"
BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/multilined.JSON?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.multijson, mappingReference=MAPPING)
BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
INGESTION_CLIENT.ingest_from_blob(
    BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
```

---

## <a name="ingest-json-records-containing-arrays"></a>Pozyskiwanie rekordów JSON zawierających tablice

Typy danych tablicy to uporządkowana Kolekcja wartości. Pozyskiwanie tablicy JSON jest wykonywane przez [zasady aktualizacji](/azure/kusto/management/update-policy). KOD JSON jest pobierany jako-do tabeli pośredniej. Zasady aktualizacji uruchamiają wstępnie zdefiniowaną funkcję w tabeli `RawEvents`, przepobierając wyniki do tabeli docelowej. Będziemy pozyskiwać dane przy użyciu następującej struktury:

```json
{
    "records": 
    [
        {
            "timestamp": "2019-05-02 15:23:50.0000000",
            "deviceId": "ddbc1bf5-096f-42c0-a771-bc3dca77ac71",
            "messageId": "7f316225-839a-4593-92b5-1812949279b3",
            "temperature": 31.0301639051317,
            "humidity": 62.0791099602725
        },
        {
            "timestamp": "2019-05-02 15:23:51.0000000",
            "deviceId": "ddbc1bf5-096f-42c0-a771-bc3dca77ac71",
            "messageId": "57de2821-7581-40e4-861e-ea3bde102364",
            "temperature": 33.7529423105311,
            "humidity": 75.4787976739364
        }
    ]
}
```

# <a name="kqltabkusto-query-language"></a>[KQL](#tab/kusto-query-language)

1. Utwórz funkcję `update policy`, która rozszerza kolekcję `records`, tak aby każda wartość w kolekcji otrzymywała oddzielny wiersz przy użyciu operatora `mv-expand`. Użyjemy `RawEvents` tabeli jako tabeli źródłowej i `Events` jako tabeli docelowej.

    ```Kusto
    .create function EventRecordsExpand() {
        RawEvents
        | mv-expand records = Event
        | project
            Time = todatetime(records["timestamp"]),
            Device = tostring(records["deviceId"]),
            MessageId = tostring(records["messageId"]),
            Temperature = todouble(records["temperature"]),
            Humidity = todouble(records["humidity"])
    }
    ```

1. Schemat otrzymany przez funkcję musi być zgodny ze schematem tabeli docelowej. Użyj operatora `getschema`, aby przejrzeć schemat.

    ```Kusto
    EventRecordsExpand() | getschema
    ```

1. Dodaj zasady aktualizacji do tabeli docelowej. Te zasady spowodują automatyczne uruchomienie zapytania na wszystkich nowych danych pozyskanych w `RawEvents` tabeli pośredniej i pozyskaniu wyników do tabeli `Events`. Zdefiniuj zasady przechowywania zerowego, aby uniknąć utrwalania tabeli pośredniej.

    ```Kusto
    .alter table Events policy update @'[{"Source": "RawEvents", "Query": "EventRecordsExpand()", "IsEnabled": "True"}]'
    ```

1. Pozyskaj dane do tabeli `RawEvents`.

    ```Kusto
    .ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/array.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=multijson, jsonMappingReference=RawEventMapping)
    ```

1. Przejrzyj dane w tabeli `Events`.

    ```Kusto
    Events
    ```

# <a name="ctabc-sharp"></a>[C#](#tab/c-sharp)

1. Utwórz funkcję aktualizacji, która rozszerza zbiór `records` tak, aby każda wartość w kolekcji otrzymywała oddzielny wiersz przy użyciu operatora `mv-expand`. Użyjemy `RawEvents` tabeli jako tabeli źródłowej i `Events` jako tabeli docelowej.   

    ```C#
    var command =
        CslCommandGenerator.GenerateCreateFunctionCommand(
            "EventRecordsExpand",
            "UpdateFunctions",
            string.Empty,
            null,
            @"RawEvents
                | mv-expand records = Event
                | project
                    Time = todatetime(records['timestamp']),
                    Device = tostring(records['deviceId']),
                    MessageId = tostring(records['messageId']),
                    Temperature = todouble(records['temperature']),
                    Humidity = todouble(records['humidity'])",
            ifNotExists: false);

    kustoClient.ExecuteControlCommand(command);
    ```

    > [!NOTE]
    > Schemat otrzymany przez funkcję musi być zgodny ze schematem tabeli docelowej.

1. Dodaj zasady aktualizacji do tabeli docelowej. Te zasady spowodują automatyczne uruchomienie zapytania na wszystkich nowych danych pozyskanych w `RawEvents` tabeli pośredniej i pozyskaniu wyników do tabeli `Events`. Zdefiniuj zasady przechowywania zerowego, aby uniknąć utrwalania tabeli pośredniej.

    ```C#
    var command =
        ".alter table Events policy update @'[{'Source': 'RawEvents', 'Query': 'EventRecordsExpand()', 'IsEnabled': 'True'}]";

    kustoClient.ExecuteControlCommand(command);
    ```

1. Pozyskaj dane do tabeli `RawEvents`.

    ```C#
    var table = "RawEvents";
    var tableMapping = "RawEventMapping";
    var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/array.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.multijson,
            IngestionMappingReference = tableMapping
        };

    ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
    ```
    
1. Przejrzyj dane w tabeli `Events`.

# <a name="pythontabpython"></a>[Python](#tab/python)

1. Utwórz funkcję aktualizacji, która rozszerza zbiór `records` tak, aby każda wartość w kolekcji otrzymywała oddzielny wiersz przy użyciu operatora `mv-expand`. Użyjemy `RawEvents` tabeli jako tabeli źródłowej i `Events` jako tabeli docelowej.   

    ```Python
    CREATE_FUNCTION_COMMAND = 
        '''.create function EventRecordsExpand() { 
            RawEvents
            | mv-expand records = Event
            | project
                Time = todatetime(records["timestamp"]),
                Device = tostring(records["deviceId"]),
                MessageId = tostring(records["messageId"]),
                Temperature = todouble(records["temperature"]),
                Humidity = todouble(records["humidity"])
            }'''
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_FUNCTION_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

    > [!NOTE]
    > Schemat otrzymany przez funkcję musi być zgodny ze schematem tabeli docelowej.

1. Dodaj zasady aktualizacji do tabeli docelowej. Te zasady spowodują automatyczne uruchomienie zapytania na wszystkich nowych danych pozyskanych w `RawEvents` tabeli pośredniej i pozyskaniu wyników do tabeli `Events`. Zdefiniuj zasady przechowywania zerowego, aby uniknąć utrwalania tabeli pośredniej.

    ```Python
    CREATE_UPDATE_POLICY_COMMAND = 
        """.alter table Events policy update @'[{'Source': 'RawEvents', 'Query': 'EventRecordsExpand()', 'IsEnabled': 'True'}]"""
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_UPDATE_POLICY_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Pozyskaj dane do tabeli `RawEvents`.

    ```Python
    TABLE = "RawEvents"
    MAPPING = "RawEventMapping"
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/array.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.multijson, mappingReference=MAPPING)
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

1. Przejrzyj dane w tabeli `Events`.

---    

## <a name="ingest-json-records-containing-dictionaries"></a>Pozyskiwanie rekordów JSON zawierających słowniki

Strukturalny plik JSON zawiera pary klucz-wartość. Rekordy JSON podlegają mapowaniu pozyskiwania przy użyciu wyrażenia logicznego w `JsonPath`. Można pozyskać dane przy użyciu następującej struktury:

```json
{
    "event": 
    [
        {
            "Key": "timestamp",
            "Value": "2019-05-02 15:23:50.0000000"
        },
        {
            "Key": "deviceId",
            "Value": "ddbc1bf5-096f-42c0-a771-bc3dca77ac71"
        },
        {
            "Key": "messageId",
            "Value": "7f316225-839a-4593-92b5-1812949279b3"
        },
        {
            "Key": "temperature",
            "Value": 31.0301639051317
        },
        {
            "Key": "humidity",
            "Value": 62.0791099602725
        }
    ]
}
```

# <a name="kqltabkusto-query-language"></a>[KQL](#tab/kusto-query-language)

1. Utwórz mapowanie JSON.

    ```Kusto
    .create table Events ingestion json mapping 'KeyValueEventMapping' '[{"column":"Time","path":"$.event[?(@.Key == 'timestamp')]"},{"column":"Device","path":"$.event[?(@.Key == 'deviceId')]"},{"column":"MessageId","path":"$.event[?(@.Key == 'messageId')]"},{"column":"Temperature","path":"$.event[?(@.Key == 'temperature')]"},{"column":"Humidity","path":"$.event[?(@.Key == 'humidity')]"}]'
    ```

1. Pozyskaj dane do tabeli `Events`.

    ```Kusto
    .ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/dictionary.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=multijson, jsonMappingReference=KeyValueEventMapping)
    ```

# <a name="ctabc-sharp"></a>[C#](#tab/c-sharp)

1. Utwórz mapowanie JSON.

    ```C#
    var tableName = "Events";
    var tableMapping = "KeyValueEventMapping";
    var command =
        CslCommandGenerator.GenerateTableJsonMappingCreateCommand(
            tableName,
            tableMapping,
            new[]
            {
                        new JsonColumnMapping {ColumnName = "Time", JsonPath = "$.event[?(@.Key == 'timestamp')]"},
                        new JsonColumnMapping {ColumnName = "Device", JsonPath = "$.event[?(@.Key == 'deviceId')]"},
                        new JsonColumnMapping {ColumnName = "MessageId", JsonPath = "$.event[?(@.Key == 'messageId')]"},
                        new JsonColumnMapping {ColumnName = "Temperature", JsonPath = "$.event[?(@.Key == 'temperature')]"},
                        new JsonColumnMapping {ColumnName = "Humidity", JsonPath = "$.event[?(@.Key == 'humidity')]"},
            });

    kustoClient.ExecuteControlCommand(command);
    ```

1. Pozyskaj dane do tabeli `Events`.

    ```C#
    var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/dictionary.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.multijson,
            IngestionMappingReference = tableMapping
        };

    ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
    ```

# <a name="pythontabpython"></a>[Python](#tab/python)

1. Utwórz mapowanie JSON.

    ```Python
    MAPPING = "KeyValueEventMapping"
    CREATE_MAPPING_COMMAND = ".create table Events ingestion json mapping '" + MAPPING + """' '[{"column":"Time","path":"$.event[?(@.Key == 'timestamp')]"},{"column":"Device","path":"$.event[?(@.Key == 'deviceId')]"},{"column":"MessageId","path":"$.event[?(@.Key == 'messageId')]"},{"column":"Temperature","path":"$.event[?(@.Key == 'temperature')]"},{"column":"Humidity","path":"$.event[?(@.Key == 'humidity')]"}]'""" 
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_MAPPING_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Pozyskaj dane do tabeli `Events`.

     ```Python
    MAPPING = "KeyValueEventMapping"
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/dictionary.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.multijson, mappingReference=MAPPING)u
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

---    

## <a name="next-steps"></a>Następne kroki

* [Przegląd pozyskiwania danych](ingest-data-overview.md)
* [Pisanie zapytań](write-queries.md)