---
title: Indeksowanie źródła danych usługi Azure Cosmos DB dla usługi Azure Search | Dokumentacja firmy Microsoft
description: W tym artykule pokazano, jak utworzyć indeksator usługi Azure Search przy użyciu źródła danych usługi Azure Cosmos DB.
ms.date: 10/17/2018
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
robot: noindex
ms.openlocfilehash: c74563ba98835403f12a4df048d7ff358014b826
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/18/2018
ms.locfileid: "49406674"
---
# <a name="connecting-cosmos-db-with-azure-search-using-indexers"></a>Łączenie usługi Cosmos DB przy użyciu usługi Azure Search przy użyciu indeksatorów

W tym artykule wyjaśniono sposób:

> [!div class="checklist"]
> * Konfigurowanie [indeksator usługi Azure Search](search-indexer-overview.md) używającą kolekcji usługi Azure Cosmos DB jako źródła danych.
> * Utwórz indeks wyszukiwania z typami danych JSON zgodny.
> * Skonfiguruj indeksator na żądanie i indeksowanie cyklicznych.
> * Odświeżanie przyrostowe indeksu na podstawie zmian w danych bazowych.

> [!NOTE]
> Usługa Azure Cosmos DB to następna generacja bazy danych documentdb. Mimo, że nazwa produktu została zmieniona, `documentdb` składni w indeksatorach usługi Azure Search nadal istnieje dla wstecznej zgodności w interfejsów API usługi Azure Search i na stronach portalu. Podczas konfigurowania indeksatorów, pamiętaj określić `documentdb` składni zgodnie z instrukcjami w tym artykule.

W poniższym klipie wideo Azure Menedżer programu usługi Cosmos DB Andrew Liu pokazuje, jak dodać indeks usługi Azure Search do kontenera usługi Azure Cosmos DB.

>[!VIDEO https://www.youtube.com/embed/OyoYu1Wzk4w]

<a name="supportedAPIs"></a>
## <a name="supported-api-types"></a>Obsługiwane typy interfejsów API

Mimo że usługi Azure Cosmos DB obsługuje wiele modeli danych i interfejsów API, obsługa produkcji indeksator usługi Azure Search rozciąga się do interfejsu API SQL. Obsługa interfejsu API usługi MongoDB jest obecnie w publicznej wersji zapoznawczej.  

Nadchodzi obsługę dodatkowe interfejsy API. Aby pomóc nam określić priorytety te, które umożliwiają najpierw, można rzutować swój głos w witrynie User Voice:

* [Obsługa źródła danych interfejsu API tabel](https://feedback.azure.com/forums/263029-azure-search/suggestions/32759746-azure-search-should-be-able-to-index-cosmos-db-tab)
* [Obsługa źródła danych interfejsu API programu Graph](https://feedback.azure.com/forums/263029-azure-search/suggestions/13285011-add-graph-databases-to-your-data-sources-eg-neo4)
* [Obsługa źródła danych interfejsu API Apache Cassandra](https://feedback.azure.com/forums/263029-azure-search/suggestions/32857525-indexer-crawler-for-apache-cassandra-api-in-azu)

## <a name="prerequisites"></a>Wymagania wstępne

Oprócz konta usługi Cosmos DB, musisz mieć [usługi Azure Search](search-create-service-portal.md). 

<a name="Concepts"></a>
## <a name="azure-search-indexer-concepts"></a>Pojęcia dotyczące indeksator usługi Azure Search

A **źródła danych** Określa dane do indeksu, poświadczeń i zasad do identyfikowania zmian w danych (takich jak dokumenty zmodyfikowany lub usunięty wewnątrz kolekcji). Źródło danych jest zdefiniowane jako niezależnym zasobem, dzięki czemu mogą być używane przez wiele indeksatorów.

**Indeksatora** opisuje sposób przepływu danych ze źródła danych w docelowym indeksem wyszukiwania. Indeksator może służyć do:

* Wykonaj jednorazowej kopii danych do wypełniania indeksu.
* Synchronizuj indeks ze zmianami w źródle danych, zgodnie z harmonogramem.
* Wywoływanie na żądanie aktualizacje indeksu zgodnie z potrzebami.

Aby skonfigurować indeksator usługi Azure Cosmos DB, musisz utworzyć indeks, źródło danych, a na końcu indeksatora. Można utworzyć za pomocą [portal](search-import-data-portal.md), [zestawu .NET SDK](/dotnet/api/microsoft.azure.search), lub [interfejsu API REST](/rest/api/searchservice/). 

W tym artykule pokazano, jak używać interfejsu API REST. Jeśli postanowisz dla portalu [Kreatora importu danych](search-import-data-portal.md) przeprowadzi Cię przez tworzenie tych zasobów, łącznie z indeksu.

> [!TIP]
> W celu uproszczenia indeksowania dla danego źródła danych można uruchomić kreator **Importuj dane** na pulpicie nawigacyjnym usługi Azure Cosmos DB. Aby rozpocząć, w obszarze nawigacyjnym po lewej stronie przejdź do pozycji **Kolekcje** > **Dodaj usługę Azure Search**.

> [!NOTE] 
> Obecnie nie można utworzyć lub edytować **bazy danych MongoDB** źródeł danych przy użyciu witryny Azure Portal lub zestawu .NET SDK. Jednak możesz **można** monitorować historię wykonywania bazy danych MongoDB indeksatorów w portalu.  

<a name="CreateDataSource"></a>
## <a name="step-1-create-a-data-source"></a>Krok 1. Tworzenie źródła danych
Aby utworzyć źródło danych, czy WPIS:

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mydocdbdatasource",
        "type": "documentdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCollection", "query": null },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        }
    }

Treść żądania zawiera definicję źródła danych, który powinien zawierać następujące pola:

* **Nazwa**: Wybierz dowolną nazwę do reprezentowania bazy danych.
* **Typ**: musi być `documentdb`.
* **poświadczenia**:
  
  * **element connectionString**: wymagane. Określ informacje o połączeniu z bazą danych Azure Cosmos DB w następującym formacie: `AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>` MongoDB dla kolekcji, Dodaj **rodzaju interfejsu API = MongoDb** parametry połączenia: `AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=MongoDb`
  Należy unikać numerów portów w programie adres url punktu końcowego. Jeśli uwzględniony numer portu usługi Azure Search można indeksować bazy danych Azure Cosmos DB.
* **kontener**:
  
  * **Nazwa**: wymagane. Określ identyfikator kolekcji bazy danych, które mają być indeksowane.
  * **Zapytanie**: opcjonalne. Można określić zapytanie w celu spłaszczenia dowolny dokument JSON do płaski schemat, który usługa Azure Search umożliwia indeksowanie. Dla kolekcji usługi MongoDB zapytania nie są obsługiwane. 
* **dataChangeDetectionPolicy**: zalecane. Zobacz [indeksowania zmienione dokumenty](#DataChangeDetectionPolicy) sekcji.
* **dataDeletionDetectionPolicy**: Optional. Zobacz [indeksowanie dokumentów usunięte](#DataDeletionDetectionPolicy) sekcji.

### <a name="using-queries-to-shape-indexed-data"></a>Za pomocą zapytań do kształtu indeksowane dane
Określanie zapytania SQL do spłaszczenia zagnieżdżonych właściwości lub tablic, właściwości JSON projektu i filtrować dane, które mają być indeksowane. 

> [!WARNING]
> Zapytania niestandardowe nie są obsługiwane dla **bazy danych MongoDB** kolekcje: `container.query` parametr musi być ustawiony na wartość null lub jest pominięty. Jeśli zachodzi potrzeba używanie zapytania niestandardowego, Daj nam znać na [User Voice](https://feedback.azure.com/forums/263029-azure-search).

Przykładowy dokument:

    {
        "userId": 10001,
        "contact": {
            "firstName": "andy",
            "lastName": "hoh"
        },
        "company": "microsoft",
        "tags": ["azure", "documentdb", "search"]
    }

Zapytanie filtru:

    SELECT * FROM c WHERE c.company = "microsoft" and c._ts >= @HighWaterMark ORDER BY c._ts

Spłaszczanie kwerendy:

    SELECT c.id, c.userId, c.contact.firstName, c.contact.lastName, c.company, c._ts FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts
    
    
Projekcja zapytanie:

    SELECT VALUE { "id":c.id, "Name":c.contact.firstName, "Company":c.company, "_ts":c._ts } FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts


Zapytanie spłaszczania tablicy:

    SELECT c.id, c.userId, tag, c._ts FROM c JOIN tag IN c.tags WHERE c._ts >= @HighWaterMark ORDER BY c._ts

<a name="CreateIndex"></a>
## <a name="step-2-create-an-index"></a>Krok 2. Tworzenie indeksu
Tworzenie indeksu usługi Azure Search docelowego, jeśli nie masz jeszcze takiego. Można utworzyć indeksu przy użyciu [interfejsu użytkownika witryny Azure portal](search-create-index-portal.md), [utworzyć indeks interfejsu API REST](/rest/api/searchservice/create-index) lub [Index, klasa](/dotnet/api/microsoft.azure.search.models.index).

Poniższy przykład tworzy indeks z polem Identyfikator i opis:

    POST https://[service name].search.windows.net/indexes?api-version=2017-11-11
    Content-Type: application/json
    api-key: [Search service admin key]

    {
       "name": "mysearchindex",
       "fields": [{
         "name": "id",
         "type": "Edm.String",
         "key": true,
         "searchable": false
       }, {
         "name": "description",
         "type": "Edm.String",
         "filterable": false,
         "sortable": false,
         "facetable": false,
         "suggestions": true
       }]
     }

Upewnij się, że schemat indeksu docelowego jest zgodna z schemat źródła dokumentów JSON lub danych wyjściowych swojej projekcji zapytań niestandardowych.

> [!NOTE]
> W przypadku kolekcji podzielonych na partycje, domyślny klucz dokumentu jest usługi Azure Cosmos DB `_rid` właściwości usługi Azure Search automatycznie zmienia nazwę na `rid` ponieważ nazwy pól nie może rozpoczynać się od znaku undescore. Ponadto usługi Azure Cosmos DB `_rid` wartości zawierają znaki, które są nieprawidłowe w kluczach usługi Azure Search. Z tego powodu `_rid` wartości są zakodowane w formacie Base64.
> 
> Dla kolekcji bazy danych MongoDB, automatycznie zmienia nazwę usługi Azure Search `_id` właściwość `doc_id`.  

### <a name="mapping-between-json-data-types-and-azure-search-data-types"></a>Mapowanie między typami danych JSON i typy danych w usłudze Azure Search
| Typ danych JSON | Typy pól indeksu docelowego zgodne |
| --- | --- |
| wartość logiczna |Edm.Boolean, Edm.String |
| Numery, które przypominają liczb całkowitych |Edm.Int32, Edm.Int64, Edm.String |
| Numery tego wygląd, takich jak punkty liczb zmiennoprzecinkowych |Edm.Double, Edm.String |
| Ciąg |Edm.String |
| Tablice typów pierwotnych, na przykład ["a", "b", "c"] |Collection(Edm.String) |
| Ciągi, które mają postać daty |Edm.DateTimeOffset, Edm.String |
| Obiekty GeoJSON, na przykład {"type": "Point", "coordinates": [długie, lat]} |Edm.GeographyPoint |
| Inne obiekty JSON |ND |

<a name="CreateIndexer"></a>

## <a name="step-3-create-an-indexer"></a>Krok 3. Tworzenie indeksatora

Po utworzeniu indeks i źródło danych możesz przystąpić do tworzenia indeksatora:

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "mydocdbindexer",
      "dataSourceName" : "mydocdbdatasource",
      "targetIndexName" : "mysearchindex",
      "schedule" : { "interval" : "PT2H" }
    }

Ten indeksator jest uruchamiane co dwie godziny (interwał harmonogramu jest ustawiona na "PT2H"). Aby uruchomić indeksatora co 30 minut, należy ustawić interwał o "PT30M". Najkrótszy obsługiwany interwał wynosi 5 minut. Harmonogram jest opcjonalnie — w przypadku pominięcia, indeksatora jest uruchamiany tylko raz, po jego utworzeniu. Jednak w dowolnym momencie można uruchomić indeksatora na żądanie.   

Aby uzyskać szczegółowe informacje na temat tworzenia interfejsu API indeksatora, zapoznaj się [tworzenie indeksatora](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

<a id="RunIndexer"></a>
### <a name="running-indexer-on-demand"></a>Uruchamianie indeksatora na żądanie
Poza uruchamianiem okresowo, zgodnie z harmonogramem, indeksator może być wywołana na żądanie:

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2017-11-11
    api-key: [Search service admin key]

> [!NOTE]
> Uruchom interfejs API zwraca pomyślnie, wywołanie indeksatora zostało zaplanowane, ale rzeczywisty przetwarzanie odbywa się asynchronicznie. 

Możesz monitorować stan indeksatora w portalu lub za pomocą uzyskać indeksatora stan interfejsu API, który opisano obok. 

<a name="GetIndexerStatus"></a>
### <a name="getting-indexer-status"></a>Uzyskiwanie stanu indeksatora
Możesz pobrać historii stanu i wykonywanie indeksatora:

    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2017-11-11
    api-key: [Search service admin key]

Odpowiedź zawiera ogólny stan indeksatora, wywołanie indeksatora ostatniego (lub w toku) i historię ostatnich wywołania indeksatora.

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
             "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

Historia wykonywania zawiera maksymalnie 50 ostatnich wykonań ukończone, które są sortowane w kolejności chronologicznej odwrotnej (tak, aby najnowsze wykonywania wykorzystasz w odpowiedzi).

<a name="DataChangeDetectionPolicy"></a>
## <a name="indexing-changed-documents"></a>Indeksowanie zmienione dokumenty
Zasady wykrywania zmian danych ma na celu wydajnego zidentyfikować elementy zmienione dane. Obecnie jest obsługiwane tylko zasady `High Water Mark` zasad przy użyciu `_ts` właściwości (sygnatury czasowej) udostępniane przez usługi Azure Cosmos DB, która jest określona w następujący sposób:

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

Przy użyciu tych zasad zdecydowanie zaleca się zapewnienie dobrej indeksatora wydajności. 

Jeśli używane są niestandardowe zapytanie, upewnij się, że `_ts` właściwość jest chroniony przez zapytanie.

<a name="IncrementalProgress"></a>
### <a name="incremental-progress-and-custom-queries"></a>Przyrostowy postęp i zapytań niestandardowych
Przyrostowy postęp podczas indeksowania gwarantuje, że jeśli wykonywanie indeksatora zostanie przerwany przez przejściowe awarie lub limitu czasu wykonywania, indeksator mogą odebrać tam, gdzie ją przerwaliśmy czasu ponownego uruchomienia, bez konieczności ponownego poindeksowania danych całą kolekcję od podstaw. Jest to szczególnie ważne podczas indeksowania dużych kolekcjach. 

Aby włączyć przyrostowy postęp, korzystając z zapytania niestandardowego, upewnij się, że zapytanie porządkuje wyniki według `_ts` kolumny. Dzięki temu okresowe sprawdzanie kwadrat korzystającą z usługi Azure Search w celu zapewnienia przyrostowy postęp w razie awarii.   

W niektórych przypadkach, nawet jeśli zapytanie zawiera `ORDER BY [collection alias]._ts` klauzuli, usługa Azure Search może nie wywnioskować czy zapytania są uporządkowane według `_ts`. Możesz poinformować usługę Azure Search, wyniki są porządkowane przy użyciu `assumeOrderByHighWaterMarkColumn` właściwość konfiguracji. Aby określić niej tę wskazówkę, należy utworzyć lub zaktualizować indeksator w następujący sposób: 

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "assumeOrderByHighWaterMarkColumn" : true } }
    } 

<a name="DataDeletionDetectionPolicy"></a>
## <a name="indexing-deleted-documents"></a>Indeksowanie usunięte dokumentów
Jeśli wiersze są usuwane z kolekcji, zazwyczaj chcesz usunąć te wiersze z z indeksu wyszukiwania. Zasady wykrywania usunięcia danych ma na celu wydajnego zidentyfikować elementy usunięte dane. Obecnie jest obsługiwane tylko zasady `Soft Delete` zasad (usuwanie jest oznaczona za pomocą flagi jakieś), który jest określony w następujący sposób:

    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }

Jeśli używane są niestandardowe zapytanie, upewnij się, że właściwość odwołuje się `softDeleteColumnName` jest chroniony przez zapytanie.

Poniższy przykład tworzy źródła danych za pomocą zasad usuwania nietrwałego:

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mydocdbdatasource",
        "type": "documentdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myDocDbEndpoint.documents.azure.com;AccountKey=myDocDbAuthKey;Database=myDocDbDatabaseId"
        },
        "container": { "name": "myDocDbCollectionId" },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        },
        "dataDeletionDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName": "isDeleted",
            "softDeleteMarkerValue": "true"
        }
    }

## <a name="NextSteps"></a>Następne kroki
Gratulacje! Masz pokazaliśmy ci, jak zintegrować usługę Azure Cosmos DB przy użyciu usługi Azure Search przy użyciu indeksatora.

* Aby dowiedzieć się więcej na temat usługi Azure Cosmos DB, zobacz [stronę usługi Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).
* Aby dowiedzieć się więcej o usłudze Azure Search, zobacz [stronę usługi wyszukiwania](https://azure.microsoft.com/services/search/).
