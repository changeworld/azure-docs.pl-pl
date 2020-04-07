---
title: Postrząślij sformatowane dane JSON do Eksploratora danych platformy Azure
description: Dowiedz się, jak pozyskiwania danych sformatowanych JSON do Usługi Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: kerend
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: bcf6a0ccfc04890052f1a4bab19f97ee4e55f87a
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756611"
---
# <a name="ingest-json-formatted-sample-data-into-azure-data-explorer"></a>Przyjmowanie przykładowych danych sformatowanych json do Eksploratora danych platformy Azure

W tym artykule pokazano, jak pozyskiwania danych Sformatowanych JSON do bazy danych Usługi Azure Data Explorer. Zaczniesz od prostych przykładów surowego i mapowane JSON, nadal wielościeżkowych JSON, a następnie rozwiązania bardziej złożonych schematów JSON zawierających tablice i słowniki.  Przykłady szczegółowo proces pozyskiwania danych JSON sformatowany przy użyciu języka zapytania Kusto (KQL), C#, lub Python. Polecenia sterujące `ingest` językiem kwerendy Kusto są wykonywane bezpośrednio do punktu końcowego aparatu. W scenariuszach produkcyjnych pozyskiwania jest wykonywane do usługi zarządzania danymi przy użyciu bibliotek klienta lub połączeń danych. [Odczytuj dane pozyskiwania przy użyciu biblioteki Języka Python usługi Azure Data Explorer](/azure/data-explorer/python-ingest-data) i pozyskiwania danych przy użyciu [standardowego sdk usługi Azure Data Explorer .NET](/azure/data-explorer/net-standard-ingest-data) dla przejścia dotyczącego pozyskiwania danych za pomocą tych bibliotek klienta.

## <a name="prerequisites"></a>Wymagania wstępne

[Klaster testowy i baza danych](create-cluster-database-portal.md)

## <a name="the-json-format"></a>Format JSON

Usługa Azure Data Explorer obsługuje dwa formaty plików JSON:
* `json`: Linia oddzielona JSON. Każdy wiersz w danych wejściowych ma dokładnie jeden rekord JSON.
* `multijson`: JSON z wieloma podszewkami. Analizator konsomanuje separatory linii i odczytuje rekord z poprzedniej pozycji do końca prawidłowego JSON.

### <a name="ingest-and-map-json-formatted-data"></a>Połknienie i mapowanie danych sformatowanych json

Połknienie danych sformatowanych json wymaga określenia *formatu* przy użyciu [właściwości pozyskiwania](/azure/kusto/management/data-ingestion/index#ingestion-properties). Pozyskiwanie danych JSON wymaga [mapowania](/azure/kusto/management/mappings), który mapuje wpis źródła JSON do kolumny docelowej. Podczas pozyskiwania danych należy użyć wstępnie `jsonMappingReference` zdefiniowanej właściwości pozyskiwania lub `jsonMapping`określić właściwość pozyskiwania. W tym artykule użyje `jsonMappingReference` właściwości pozyskiwania, która jest wstępnie zdefiniowana w tabeli używanej do pozyskiwania. W poniższych przykładach zaczniemy od pozyskiwania rekordów JSON jako nieprzetworzonych danych do pojedynczej tabeli kolumn. Następnie użyjemy mapowania do pozyskiwania każdej właściwości do jego zamapowane kolumny. 

### <a name="simple-json-example"></a>Prosty przykład JSON

Poniższy przykład jest prosty JSON, o płaskiej strukturze. Dane są zbierane przez kilka urządzeń. Każdy rekord jest oznaczony identyfikatorem i sygnaturą czasową.

```json
{
    "timestamp": "2019-05-02 15:23:50.0369439",
    "deviceId": "2945c8aa-f13e-4c48-4473-b81440bb5ca2",
    "messageId": "7f316225-839a-4593-92b5-1812949279b3",
    "temperature": 31.0301639051317,
    "humidity": 62.0791099602725
}
```

## <a name="ingest-raw-json-records"></a>Połknienie nieprzetworzonych rekordów JSON 

W tym przykładzie pozyskiwania rekordów JSON jako nieprzetworzonych danych do pojedynczej tabeli kolumn. Manipulowanie danymi, przy użyciu zapytań i zasad aktualizacji odbywa się po spożyciu danych.

# <a name="kql"></a>[Kql](#tab/kusto-query-language)

Użyj języka zapytania Kusto do pozyskiwania danych w nieprzetworzonym formacie JSON.

1. Zaloguj się [https://dataexplorer.azure.com](https://dataexplorer.azure.com)do .

1. Wybierz pozycję **Dodaj klaster**.

1. W oknie dialogowym **Dodawanie klastra** wprowadź `https://<ClusterName>.<Region>.kusto.windows.net/`adres URL klastra w formularzu , a następnie wybierz pozycję **Dodaj**.

1. Wklej w poniższym poleceniu, a następnie wybierz pozycję **Uruchom,** aby utworzyć tabelę.

    ```Kusto
    .create table RawEvents (Event: dynamic)
    ```

    Ta kwerenda tworzy tabelę z pojedynczą `Event` kolumną [dynamicznego](/azure/kusto/query/scalar-data-types/dynamic) typu danych.

1. Utwórz mapowanie JSON.

    ```Kusto
    .create table RawEvents ingestion json mapping 'RawEventMapping' '[{"column":"Event","path":"$"}]'
    ```

    To polecenie tworzy mapowanie i mapuje `$` ścieżkę główną JSON do `Event` kolumny.

1. Połknąć dane `RawEvents` do tabeli.

    ```Kusto
    .ingest into table RawEvents h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=json, jsonMappingReference=RawEventMapping)
    ```

# <a name="c"></a>[C#](#tab/c-sharp)

Użyj języka C# do pozyskiwania danych w formacie JSON nieprzetworzonego.

1. Utwórz `RawEvents` tabelę.

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
    To polecenie tworzy mapowanie i mapuje `$` ścieżkę główną JSON do `Event` kolumny.

1. Połknąć dane `RawEvents` do tabeli.

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
> Dane są agregowane zgodnie z [zasadami przetwarzania wsadowego,](/azure/kusto/concepts/batchingpolicy)co powoduje kilkuminutowe opóźnienie.

# <a name="python"></a>[Python](#tab/python)

Użyj języka Python do pozyskiwania danych w formacie JSON nieprzetworzonego.

1. Utwórz `RawEvents` tabelę.

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

1. Połknąć dane `RawEvents` do tabeli.

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
    > Dane są agregowane zgodnie z [zasadami przetwarzania wsadowego,](/azure/kusto/concepts/batchingpolicy)co powoduje kilkuminutowe opóźnienie.

---

## <a name="ingest-mapped-json-records"></a>Połknienie mapowanych rekordów JSON

W tym przykładzie pozyskiwania danych JSON rekordów. Każda właściwość JSON jest mapowana na jedną kolumnę w tabeli. 

# <a name="kql"></a>[Kql](#tab/kusto-query-language)

1. Utwórz nową tabelę o podobnym schemacie do danych wejściowych JSON. Użyjemy tej tabeli dla wszystkich poniższych przykładów i polecenia pozyskiwania. 

    ```Kusto
    .create table Events (Time: datetime, Device: string, MessageId: string, Temperature: double, Humidity: double)
    ```

1. Utwórz mapowanie JSON.

    ```Kusto
    .create table Events ingestion json mapping 'FlatEventMapping' '[{"column":"Time","path":"$.timestamp"},{"column":"Device","path":"$.deviceId"},{"column":"MessageId","path":"$.messageId"},{"column":"Temperature","path":"$.temperature"},{"column":"Humidity","path":"$.humidity"}]'
    ```

    W tym mapowaniu, zgodnie z definicją w schemacie tabeli, `timestamp` wpisy zostaną przyjmone do kolumny `Time` jako `datetime` typy danych.

1. Połknąć dane `Events` do tabeli.

    ```Kusto
    .ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=json, jsonMappingReference=FlatEventMapping)
    ```

    Plik "simple.json" ma kilka rekordów JSON oddzielonych linią. Format jest `json`, a mapowanie używane w poleceniu pozyskiwania jest utworzony. `FlatEventMapping`

# <a name="c"></a>[C#](#tab/c-sharp)

1. Utwórz nową tabelę o podobnym schemacie do danych wejściowych JSON. Użyjemy tej tabeli dla wszystkich poniższych przykładów i polecenia pozyskiwania. 

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

    W tym mapowaniu, zgodnie z definicją w schemacie tabeli, `timestamp` wpisy zostaną przyjmone do kolumny `Time` jako `datetime` typy danych.    

1. Połknąć dane `Events` do tabeli.

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

    Plik "simple.json" ma kilka rekordów JSON oddzielonych linią. Format jest `json`, a mapowanie używane w poleceniu pozyskiwania jest utworzony. `FlatEventMapping`

# <a name="python"></a>[Python](#tab/python)

1. Utwórz nową tabelę o podobnym schemacie do danych wejściowych JSON. Użyjemy tej tabeli dla wszystkich poniższych przykładów i polecenia pozyskiwania. 

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

1. Połknąć dane `Events` do tabeli.

    ```Python
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.json, mappingReference=MAPPING)
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

    Plik "simple.json" ma kilka wierszy oddzielone rekordy JSON. Format jest `json`, a mapowanie używane w poleceniu pozyskiwania jest utworzony. `FlatEventMapping`    
---

## <a name="ingest-multi-lined-json-records"></a>Połknienie wielościeżkowych rekordów JSON

W tym przykładzie pozyskiwania rekordów JSON z wieloma wyściełami. Każda właściwość JSON jest mapowana na jedną kolumnę w tabeli. Plik "multilined.json" ma kilka wciętych rekordów JSON. Format `multijson` nakazuje aparatowi odczytywanie rekordów przez strukturę JSON.

# <a name="kql"></a>[Kql](#tab/kusto-query-language)

Połknąć dane `Events` do tabeli.

```Kusto
.ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/multilined.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=multijson, jsonMappingReference=FlatEventMapping)
```

# <a name="c"></a>[C#](#tab/c-sharp)

Połknąć dane `Events` do tabeli.

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

# <a name="python"></a>[Python](#tab/python)

Połknąć dane `Events` do tabeli.

```Python
MAPPING = "FlatEventMapping"
BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/multilined.JSON?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.multijson, mappingReference=MAPPING)
BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
INGESTION_CLIENT.ingest_from_blob(
    BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
```

---

## <a name="ingest-json-records-containing-arrays"></a>Pochłonienie rekordów JSON zawierających tablice

Typy danych tablicy są uporządkowanym zbiorem wartości. Połknienie tablicy JSON odbywa się za pomocą [zasad aktualizacji](/azure/kusto/management/update-policy). JSON jest pochłonięty jako jest do tabeli pośredniej. Zasady aktualizacji uruchamia wstępnie zdefiniowaną `RawEvents` funkcję w tabeli, przykuwając ponownie wyniki do tabeli docelowej. Będziemy pojmować dane o następującej strukturze:

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

# <a name="kql"></a>[Kql](#tab/kusto-query-language)

1. Utwórz `update policy` funkcję, która rozwija `records` kolekcję tak, aby każda wartość w `mv-expand` kolekcji odbierał oddzielny wiersz, przy użyciu operatora. Użyjemy tabeli `RawEvents` jako tabeli źródłowej i `Events` tabeli docelowej.

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

1. Schemat odebrany przez funkcję musi być zgodny ze schematem tabeli docelowej. Użyj `getschema` operatora, aby przejrzeć schemat.

    ```Kusto
    EventRecordsExpand() | getschema
    ```

1. Dodaj zasady aktualizacji do tabeli docelowej. Ta zasada automatycznie uruchomi kwerendę na wszystkich nowo pochłoniętych danych w tabeli pośredniej `RawEvents` i pochłonie wyniki w `Events` tabeli. Zdefiniuj zasady zerowego przechowywania, aby uniknąć utrwalania tabeli pośredniej.

    ```Kusto
    .alter table Events policy update @'[{"Source": "RawEvents", "Query": "EventRecordsExpand()", "IsEnabled": "True"}]'
    ```

1. Połknąć dane `RawEvents` do tabeli.

    ```Kusto
    .ingest into table RawEvents h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/array.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=multijson, jsonMappingReference=RawEventMapping)
    ```

1. Przejrzyj `Events` dane w tabeli.

    ```Kusto
    Events
    ```

# <a name="c"></a>[C#](#tab/c-sharp)

1. Utwórz funkcję aktualizacji, która `records` rozwija kolekcję tak, aby każda wartość `mv-expand` w kolekcji odbierała oddzielny wiersz, przy użyciu operatora. Użyjemy tabeli `RawEvents` jako tabeli źródłowej i `Events` tabeli docelowej.   

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
    > Schemat odebrany przez funkcję musi być zgodny ze schematem tabeli docelowej.

1. Dodaj zasady aktualizacji do tabeli docelowej. Ta zasada automatycznie uruchomi kwerendę na wszystkich nowo pochłoniętych danych w tabeli pośredniej `RawEvents` i pochłonie jej wyniki w `Events` tabeli. Zdefiniuj zasady zerowego przechowywania, aby uniknąć utrwalania tabeli pośredniej.

    ```C#
    var command =
        ".alter table Events policy update @'[{'Source': 'RawEvents', 'Query': 'EventRecordsExpand()', 'IsEnabled': 'True'}]";

    kustoClient.ExecuteControlCommand(command);
    ```

1. Połknąć dane `RawEvents` do tabeli.

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
    
1. Przejrzyj `Events` dane w tabeli.

# <a name="python"></a>[Python](#tab/python)

1. Utwórz funkcję aktualizacji, która `records` rozwija kolekcję tak, aby każda wartość `mv-expand` w kolekcji odbierała oddzielny wiersz, przy użyciu operatora. Użyjemy tabeli `RawEvents` jako tabeli źródłowej i `Events` tabeli docelowej.   

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
    > Schemat odebrany przez funkcję musi być zgodny ze schematem tabeli docelowej.

1. Dodaj zasady aktualizacji do tabeli docelowej. Ta zasada automatycznie uruchomi kwerendę na wszystkich nowo pochłoniętych danych w tabeli pośredniej `RawEvents` i pochłonie jej wyniki w `Events` tabeli. Zdefiniuj zasady zerowego przechowywania, aby uniknąć utrwalania tabeli pośredniej.

    ```Python
    CREATE_UPDATE_POLICY_COMMAND = 
        """.alter table Events policy update @'[{'Source': 'RawEvents', 'Query': 'EventRecordsExpand()', 'IsEnabled': 'True'}]"""
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_UPDATE_POLICY_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Połknąć dane `RawEvents` do tabeli.

    ```Python
    TABLE = "RawEvents"
    MAPPING = "RawEventMapping"
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/array.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.multijson, mappingReference=MAPPING)
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

1. Przejrzyj `Events` dane w tabeli.

---    

## <a name="ingest-json-records-containing-dictionaries"></a>Niuszowanie rekordów JSON zawierających słowniki

Słownik strukturalny JSON zawiera pary klucz-wartość. Rekordy Json są poddawane mapowaniu pozyskiwania `JsonPath`przy użyciu wyrażenia logicznego w pliku . Można pozyskiwania danych z następującą strukturą:

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

# <a name="kql"></a>[Kql](#tab/kusto-query-language)

1. Tworzenie mapowania JSON.

    ```Kusto
    .create table Events ingestion json mapping 'KeyValueEventMapping' '[{"column":"Time","path":"$.event[?(@.Key == 'timestamp')]"},{"column":"Device","path":"$.event[?(@.Key == 'deviceId')]"},{"column":"MessageId","path":"$.event[?(@.Key == 'messageId')]"},{"column":"Temperature","path":"$.event[?(@.Key == 'temperature')]"},{"column":"Humidity","path":"$.event[?(@.Key == 'humidity')]"}]'
    ```

1. Połknąć dane `Events` do tabeli.

    ```Kusto
    .ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/dictionary.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=multijson, jsonMappingReference=KeyValueEventMapping)
    ```

# <a name="c"></a>[C#](#tab/c-sharp)

1. Tworzenie mapowania JSON.

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

1. Połknąć dane `Events` do tabeli.

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

# <a name="python"></a>[Python](#tab/python)

1. Tworzenie mapowania JSON.

    ```Python
    MAPPING = "KeyValueEventMapping"
    CREATE_MAPPING_COMMAND = ".create table Events ingestion json mapping '" + MAPPING + """' '[{"column":"Time","path":"$.event[?(@.Key == 'timestamp')]"},{"column":"Device","path":"$.event[?(@.Key == 'deviceId')]"},{"column":"MessageId","path":"$.event[?(@.Key == 'messageId')]"},{"column":"Temperature","path":"$.event[?(@.Key == 'temperature')]"},{"column":"Humidity","path":"$.event[?(@.Key == 'humidity')]"}]'""" 
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_MAPPING_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Połknąć dane `Events` do tabeli.

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

* [Omówienie pozyskiwania danych](ingest-data-overview.md)
* [Pisanie zapytań](write-queries.md)
