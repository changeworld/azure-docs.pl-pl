---
title: 'Szybki start: Pozyskiwanie danych przy użyciu biblioteki środowiska Node w usłudze Azure Data Explorer'
description: Z tego przewodnika Szybki start dowiesz się, jak pozyskiwać (ładować) dane do usługi Azure Data Explorer za pomocą środowiska Node.js.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 10/25/2018
ms.openlocfilehash: d530ed3710a3406e5cd8115d615e91d5056a5f8c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59047657"
---
# <a name="quickstart-ingest-data-using-the-azure-data-explorer-node-library"></a>Szybki start: Pozyskiwanie danych przy użyciu biblioteki środowiska Node w usłudze Azure Data Explorer

Azure Data Explorer to szybka i wysoce skalowalna usługa eksploracji danych na potrzeby danych dziennika i telemetrycznych. Usługa Azure Data Explorer udostępnia dwie biblioteki klienckie dla środowiska Node: [bibliotekę pozyskiwania](https://github.com/Azure/azure-kusto-node/tree/master/azure-kusto-ingest) i [bibliotekę danych](https://github.com/Azure/azure-kusto-node/tree/master/azure-kusto-data). Te biblioteki umożliwiają pozyskiwanie (ładowanie) danych do klastra i wykonywanie zapytań o dane z kodu. W tym przewodniku Szybki start najpierw utworzysz tabelę i mapowanie danych w klastrze testowym. Następnie umieścisz pozyskiwanie w kolejce do klastra i sprawdzisz poprawność wyników.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego przewodnika Szybki start, oprócz subskrypcji platformy Azure, potrzebne są następujące elementy:

* [Klaster testowy i baza danych](create-cluster-database-portal.md)

* Środowiska [Node.js](https://nodejs.org/en/download/) zainstalowane na komputerze deweloperskim

## <a name="install-the-data-and-ingest-libraries"></a>Instalowanie biblioteki danych i biblioteki pozyskiwania

Instalowanie bibliotek *azure-kusto-ingest* i *azure-kusto-data*

```bash
npm i --save azure-kusto-ingest azure-kusto-data
```

## <a name="add-import-statements-and-constants"></a>Dodawanie instrukcji importu i stałych

Importowanie klas z bibliotek

```javascript

const KustoConnectionStringBuilder = require("azure-kusto-data").KustoConnectionStringBuilder;
const KustoClient = require("azure-kusto-data").Client;
const KustoIngestClient = require("azure-kusto-ingest").IngestClient;
const IngestionProperties = require("azure-kusto-ingest").IngestionProperties;
const { DataFormat } = require("azure-kusto-ingest").IngestionPropertiesEnums;
const { BlobDescriptor } = require("azure-kusto-ingest").IngestionDescriptors;

```
Usługa Azure Data Explorer korzysta z identyfikatora dzierżawy usługi Azure Active Directory w celu uwierzytelnienia aplikacji. Aby znaleźć swój identyfikator dzierżawy, wykonaj procedurę opisaną w artykule [Znajdowanie identyfikatora dzierżawy usługi Office 365](https://docs.microsoft.com/onedrive/find-your-office-365-tenant-id).

Zanim uruchomisz ten kod, ustaw wartości `authorityId`, `kustoUri`, `kustoIngestUri` i `kustoDatabase`.

```javascript
const cluster = "MyCluster";
const region = "westus";
const authorityId = "microsoft.com";
const kustoUri = `https://${cluster}.${region}.kusto.windows.net:443`;
const kustoIngestUri = `https://ingest-${cluster}.${region}.kusto.windows.net:443`;
const kustoDatabase  = "Weather";
```

Teraz możesz utworzyć parametry połączenia. W tym przykładzie w celu uzyskania dostępu do klastra używane jest uwierzytelnianie urządzenia. Możesz również użyć certyfikatu aplikacji, klucza aplikacji oraz użytkownika i hasła usługi Azure Active Directory.

W kolejnym kroku utworzysz tabelę docelową i mapowanie.

```javascript
const kcsbIngest = KustoConnectionStringBuilder.withAadDeviceAuthentication(kustoIngestUri, authorityId);
const kcsbData = KustoConnectionStringBuilder.withAadDeviceAuthentication(kustoUri, authorityId);
const destTable = "StormEvents";
const destTableMapping = "StormEvents_CSV_Mapping";
```

## <a name="set-source-file-information"></a>Ustawianie informacji o pliku źródłowym

Zaimportuj dodatkowe klasy i ustaw stałe dla pliku źródła danych. W tym przykładzie używany jest przykładowy plik hostowany w usłudze Azure Blob Storage. Przykładowy zestaw danych **StormEvents** zawiera dane dotyczące pogody pochodzące z organizacji [National Centers for Environmental Information](https://www.ncdc.noaa.gov/stormevents/).

```javascript
const container = "samplefiles";
const account = "kustosamplefiles";
const sas = "?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
const filePath = "StormEvents.csv";
const blobPath = `https://${account}.blob.core.windows.net/${container}/${filePath}${sas}`;
```

## <a name="create-a-table-on-your-test-cluster"></a>Tworzenie tabeli w klastrze testowym

Utwórz tabelę, która będzie zgodna ze schematem danych w pliku `StormEvents.csv`. Uruchomienie tego kodu powoduje zwrócenie komunikatu, jak pokazano poniżej: *Aby się zalogować, otwórz stronę https://microsoft.com/devicelogin w przeglądarce sieci Web. Wprowadź kod XXXXXXXXX, aby się uwierzytelnić*. Postępuj zgodnie z instrukcjami, aby się zalogować, a następnie wróć w celu uruchomienia kolejnego bloku kodu. Kolejne bloki kodu umożliwiające nawiązanie połączenia będą wymagały ponownego zalogowania.

```javascript
const kustoClient = new KustoClient(kcsbData);
const createTableCommand = `.create table ${destTable} (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)`;

kustoClient.executeMgmt(kustoDatabase, createTableCommand, (err, results) => {
    console.log(results.primaryResults[0][0].toString());
});
```

## <a name="define-ingestion-mapping"></a>Definiowanie mapowania pozyskiwania

Zmapuj przychodzące dane CSV na nazwy kolumn i typy danych używane podczas tworzenia tabeli.

```javascript
const createMappingCommand = `.create table ${destTable} ingestion csv mapping '${destTableMapping}' '[{"Name":"StartTime","datatype":"datetime","Ordinal":0}, {"Name":"EndTime","datatype":"datetime","Ordinal":1},{"Name":"EpisodeId","datatype":"int","Ordinal":2},{"Name":"EventId","datatype":"int","Ordinal":3},{"Name":"State","datatype":"string","Ordinal":4},{"Name":"EventType","datatype":"string","Ordinal":5},{"Name":"InjuriesDirect","datatype":"int","Ordinal":6},{"Name":"InjuriesIndirect","datatype":"int","Ordinal":7},{"Name":"DeathsDirect","datatype":"int","Ordinal":8},{"Name":"DeathsIndirect","datatype":"int","Ordinal":9},{"Name":"DamageProperty","datatype":"int","Ordinal":10},{"Name":"DamageCrops","datatype":"int","Ordinal":11},{"Name":"Source","datatype":"string","Ordinal":12},{"Name":"BeginLocation","datatype":"string","Ordinal":13},{"Name":"EndLocation","datatype":"string","Ordinal":14},{"Name":"BeginLat","datatype":"real","Ordinal":16},{"Name":"BeginLon","datatype":"real","Ordinal":17},{"Name":"EndLat","datatype":"real","Ordinal":18},{"Name":"EndLon","datatype":"real","Ordinal":19},{"Name":"EpisodeNarrative","datatype":"string","Ordinal":20},{"Name":"EventNarrative","datatype":"string","Ordinal":21},{"Name":"StormSummary","datatype":"dynamic","Ordinal":22}]'`;

kustoClient.executeMgmt(kustoDatabase, createMappingCommand, (err, results) => {
    console.log(results.primaryResults[0][0].toString());
});
```

## <a name="queue-a-message-for-ingestion"></a>Wysyłanie komunikatu do kolejki w celu pozyskiwania

Wyślij komunikat do kolejki, aby ściągnąć dane z magazynu obiektów blob i pozyskać dane do usługi Azure Data Explorer.

```javascript
const defaultProps  = new IngestionProperties(kustoDatabase, destTable, DataFormat.csv, null,destTableMapping, {'ignoreFirstRecord': 'true'});
const ingestClient = new KustoIngestClient(kcsbIngest, defaultProps);
// All ingestion properties are documented here: https://docs.microsoft.com/azure/kusto/management/data-ingest#ingestion-properties

const blobDesc = new BlobDescriptor(blobPath, 10);
ingestClient.ingestFromBlob(blobDesc,null, (err) => {
    if (err) throw new Error(err);
});
```

## <a name="validate-that-table-contains-data"></a>Sprawdzanie, czy tabela zawiera dane

Sprawdź, czy dane zostały pozyskane do tabeli. Poczekaj od pięciu do dziesięciu minut, aby umieszczone w kolejce pozyskiwanie zostało zaplanowane do pozyskiwania i załadowania danych do usługi Azure Data Explorer. Następnie uruchom następujący kod, aby uzyskać liczbę rekordów w tabeli `StormEvents`.

```javascript
const query = `${destTable} | count`;

kustoClient.execute(kustoDatabase, query, (err, results) => {
    if (err) throw new Error(err);  
    console.log(results.primaryResults[0][0].toString());
});
```

## <a name="run-troubleshooting-queries"></a>Uruchamianie zapytań dotyczących rozwiązywania problemów

Zaloguj się do portalu [https://dataexplorer.azure.com](https://dataexplorer.azure.com) i nawiąż połączenie z klastrem. Uruchom następujące polecenie w bazie danych, aby sprawdzić, czy wystąpiły jakieś niepowodzenia pozyskiwania w ciągu ostatnich czterech godzin. Przed uruchomieniem zastąp nazwę bazy danych.
    
```Kusto
.show ingestion failures
| where FailedOn > ago(4h) and Database == "<DatabaseName>"
```

Uruchom następujące polecenie, aby wyświetlić stan wszystkich operacji pozyskiwania z ostatnich czterech godzin. Przed uruchomieniem zastąp nazwę bazy danych.

```Kusto
.show operations
| where StartedOn > ago(4h) and Database == "<DatabaseName>" and Operation == "DataIngestPull"
| summarize arg_max(LastUpdatedOn, *) by OperationId
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli zamierzasz skorzystać z naszych pozostałych przewodników Szybki start i samouczków, zachowaj utworzone zasoby. W przeciwnym razie uruchom następujące polecenie w bazie danych, aby wyczyścić tabelę `StormEvents`.

```Kusto
.drop table StormEvents
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Pisanie zapytań](write-queries.md)
