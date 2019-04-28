---
title: 'Szybki start: pozyskiwanie danych przy użyciu biblioteki języka Python w usłudze Azure Data Explorer'
description: Z tego przewodnika Szybki start dowiesz się, jak pozyskiwać (ładować) dane do usługi Azure Data Explorer za pomocą języka Python.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 10/16/2018
ms.openlocfilehash: fdeae2c6b598feee0abc57c80ea32f2108504330
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60827174"
---
# <a name="quickstart-ingest-data-using-the-azure-data-explorer-python-library"></a>Szybki start: pozyskiwanie danych przy użyciu biblioteki języka Python w usłudze Azure Data Explorer

Azure Data Explorer to szybka i wysoce skalowalna usługa eksploracji danych na potrzeby danych dziennika i telemetrycznych. Usługa Azure Data Explorer udostępnia dwie biblioteki klienckie dla języka Python: [bibliotekę pozyskiwania](https://github.com/Azure/azure-kusto-python/tree/master/azure-kusto-ingest) i [bibliotekę danych](https://github.com/Azure/azure-kusto-python/tree/master/azure-kusto-data). Te biblioteki umożliwiają pozyskiwanie (ładowanie) danych do klastra i wykonywanie zapytań o dane z kodu. W tym przewodniku Szybki start najpierw utworzysz tabelę i mapowanie danych w klastrze. Następnie umieścisz pozyskiwanie w kolejce do klastra i sprawdzisz poprawność wyników.

Ten przewodnik Szybki start jest również dostępny w formie [notesu platformy Azure](https://notebooks.azure.com/ManojRaheja/libraries/KustoPythonSamples/html/QueuedIngestSingleBlob.ipynb).

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).

* [Klaster i baza danych](create-cluster-database-portal.md)

* Język [Python](https://www.python.org/downloads/) zainstalowany na komputerze deweloperskim

## <a name="install-the-data-and-ingest-libraries"></a>Instalowanie biblioteki danych i biblioteki pozyskiwania

Zainstaluj biblioteki *azure-kusto-data* i *azure-kusto-ingest*.

```
pip install azure-kusto-data
pip install azure-kusto-ingest
```

## <a name="add-import-statements-and-constants"></a>Dodawanie instrukcji importu i stałych

Zaimportuj klasy z biblioteki azure-kusto-data.

```python
from azure.kusto.data.request import KustoClient, KustoConnectionStringBuilder
from azure.kusto.data.exceptions import KustoServiceError
from azure.kusto.data.helpers import dataframe_from_result_table
```

Usługa Azure Data Explorer korzysta z identyfikatora dzierżawy usługi AAD w celu uwierzytelnienia aplikacji. Aby odnaleźć identyfikator dzierżawy, użyj następującego adresu URL, zastępując ciąg *YourDomain* nazwą domeny.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Jeśli na przykład Twoja domena to *contoso.com*, adres URL to [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Kliknij ten adres URL, aby wyświetlić wyniki. Pierwszy wiersz wygląda w następujący sposób. 

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

Identyfikator dzierżawy w tym przypadku to `6babcaad-604b-40ac-a9d7-9fd97c0b779f`. Przed uruchomieniem tego kodu ustaw wartości dla stałych AAD_TENANT_ID, KUSTO_URI, KUSTO_INGEST_URI oraz KUSTO_DATABASE.

```python
AAD_TENANT_ID = "<TenantId>"
KUSTO_URI = "https://<ClusterName>.<Region>.kusto.windows.net:443/"
KUSTO_INGEST_URI = "https://ingest-<ClusterName>.<Region>.kusto.windows.net:443/"
KUSTO_DATABASE = "<DatabaseName>"
```

Teraz możesz utworzyć parametry połączenia. W tym przykładzie w celu uzyskania dostępu do klastra używane jest uwierzytelnianie urządzenia. Możesz również użyć [certyfikatu aplikacji usługi AAD](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L24), [klucza aplikacji usługi ADD](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L20) oraz [użytkownika i hasła usługi AAD](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L34).

W kolejnym kroku utworzysz tabelę docelową i mapowanie.

```python
KCSB_INGEST = KustoConnectionStringBuilder.with_aad_device_authentication(KUSTO_INGEST_URI, AAD_TENANT_ID)

KCSB_DATA = KustoConnectionStringBuilder.with_aad_device_authentication(KUSTO_URI, AAD_TENANT_ID)

DESTINATION_TABLE = "StormEvents"
DESTINATION_TABLE_COLUMN_MAPPING = "StormEvents_CSV_Mapping"
```

## <a name="set-source-file-information"></a>Ustawianie informacji o pliku źródłowym

Zaimportuj dodatkowe klasy i ustaw stałe dla pliku źródła danych. W tym przykładzie używany jest przykładowy plik hostowany w usłudze Azure Blob Storage. Przykładowy zestaw danych **StormEvents** zawiera dane dotyczące pogody pochodzące z organizacji [National Centers for Environmental Information](https://www.ncdc.noaa.gov/stormevents/).

```python
from azure.storage.blob import BlockBlobService
from azure.kusto.ingest import KustoIngestClient, IngestionProperties, FileDescriptor, BlobDescriptor, DataFormat, ReportLevel, ReportMethod

CONTAINER = "samplefiles"
ACCOUNT_NAME = "kustosamplefiles"
SAS_TOKEN = "?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D"
FILE_PATH = "StormEvents.csv"
FILE_SIZE = 64158321    # in bytes

BLOB_PATH = "https://" + ACCOUNT_NAME + ".blob.core.windows.net/" + CONTAINER + "/" + FILE_PATH + SAS_TOKEN
```

## <a name="create-a-table-on-your-cluster"></a>Tworzenie tabeli w klastrze

Utwórz tabelę, która będzie zgodna ze schematem danych w pliku StormEvents.csv. Uruchomienie tego kodu powoduje zwrócenie komunikatu, jak pokazano poniżej: *Aby się zalogować, otwórz stronę https://microsoft.com/devicelogin w przeglądarce internetowej i wprowadź kod F3W4VWZDM, aby się uwierzytelnić*. Postępuj zgodnie z instrukcjami, aby się zalogować, a następnie wróć w celu uruchomienia kolejnego bloku kodu. Kolejne bloki kodu umożliwiające nawiązanie połączenia wymagają ponownego zalogowania.

```python
KUSTO_CLIENT = KustoClient(KCSB_DATA)
CREATE_TABLE_COMMAND = ".create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)"

RESPONSE = KUSTO_CLIENT.execute_mgmt(KUSTO_DATABASE, CREATE_TABLE_COMMAND)

dataframe_from_result_table(RESPONSE.primary_results[0])
```

## <a name="define-ingestion-mapping"></a>Definiowanie mapowania pozyskiwania

Zmapuj przychodzące dane CSV na nazwy kolumn i typy danych używane podczas tworzenia tabeli. Spowoduje to zamapowanie pól danych źródłowych na kolumny tabeli docelowej

```python
CREATE_MAPPING_COMMAND = """.create table StormEvents ingestion csv mapping 'StormEvents_CSV_Mapping' '[{"Name":"StartTime","datatype":"datetime","Ordinal":0}, {"Name":"EndTime","datatype":"datetime","Ordinal":1},{"Name":"EpisodeId","datatype":"int","Ordinal":2},{"Name":"EventId","datatype":"int","Ordinal":3},{"Name":"State","datatype":"string","Ordinal":4},{"Name":"EventType","datatype":"string","Ordinal":5},{"Name":"InjuriesDirect","datatype":"int","Ordinal":6},{"Name":"InjuriesIndirect","datatype":"int","Ordinal":7},{"Name":"DeathsDirect","datatype":"int","Ordinal":8},{"Name":"DeathsIndirect","datatype":"int","Ordinal":9},{"Name":"DamageProperty","datatype":"int","Ordinal":10},{"Name":"DamageCrops","datatype":"int","Ordinal":11},{"Name":"Source","datatype":"string","Ordinal":12},{"Name":"BeginLocation","datatype":"string","Ordinal":13},{"Name":"EndLocation","datatype":"string","Ordinal":14},{"Name":"BeginLat","datatype":"real","Ordinal":16},{"Name":"BeginLon","datatype":"real","Ordinal":17},{"Name":"EndLat","datatype":"real","Ordinal":18},{"Name":"EndLon","datatype":"real","Ordinal":19},{"Name":"EpisodeNarrative","datatype":"string","Ordinal":20},{"Name":"EventNarrative","datatype":"string","Ordinal":21},{"Name":"StormSummary","datatype":"dynamic","Ordinal":22}]'"""

RESPONSE = KUSTO_CLIENT.execute_mgmt(KUSTO_DATABASE, CREATE_MAPPING_COMMAND)

dataframe_from_result_table(RESPONSE.primary_results[0])
```

## <a name="queue-a-message-for-ingestion"></a>Wysyłanie komunikatu do kolejki w celu pozyskiwania

Wyślij komunikat do kolejki, aby ściągnąć dane z magazynu obiektów blob i pozyskać dane do usługi Azure Data Explorer.

```python
INGESTION_CLIENT = KustoIngestClient(KCSB_INGEST)

# All ingestion properties are documented here: https://docs.microsoft.com/azure/kusto/management/data-ingest#ingestion-properties
INGESTION_PROPERTIES = IngestionProperties(database=KUSTO_DATABASE, table=DESTINATION_TABLE, dataFormat=DataFormat.csv, mappingReference = DESTINATION_TABLE_COLUMN_MAPPING, additionalProperties={'ignoreFirstRecord': 'true'})
BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)  # FILE_SIZE is the raw size of the data in bytes
INGESTION_CLIENT.ingest_from_blob(BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)

print('Done queuing up ingestion with Azure Data Explorer')

```

## <a name="query-data-that-was-ingested-into-the-table"></a>Wykonywanie zapytań o dane pozyskane do tabeli

Poczekaj od pięciu do dziesięciu minut, aby umieszczone w kolejce pozyskiwanie zostało zaplanowane do pozyskiwania i załadowania danych do usługi Azure Data Explorer. Następnie uruchom następujący kod, aby uzyskać liczbę rekordów w tabeli StormEvents.

```python
QUERY = "StormEvents | count"

RESPONSE = KUSTO_CLIENT.execute_query(KUSTO_DATABASE, QUERY)

dataframe_from_result_table(RESPONSE.primary_results[0])
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
| where StartedOn > ago(4h) and Database == "<DatabaseName>" and Table == "StormEvents" and Operation == "DataIngestPull"
| summarize arg_max(LastUpdatedOn, *) by OperationId
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli zamierzasz skorzystać z naszych pozostałych przewodników Szybki start i samouczków, zachowaj utworzone zasoby. W przeciwnym razie uruchom następujące polecenie w bazie danych, aby wyczyścić tabelę StormEvents.

```Kusto
.drop table StormEvents
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wykonywanie zapytań o dane przy użyciu języka Python](python-query-data.md)
