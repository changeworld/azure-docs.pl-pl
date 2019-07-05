---
title: Indeks źródła danych usługi Azure Cosmos DB — usługa Azure Search
description: Przeszukaj źródła danych usługi Azure Cosmos DB i pozyskiwania danych w indeksie wyszukiwania pełnotekstowego w usłudze Azure Search. Indeksatory zautomatyzować pozyskiwanie danych dla wybranych źródeł danych takich jak Azure Cosmos DB.
ms.date: 05/02/2019
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 7f9df42725e41fb514370dbdb828ad5b1305ea78
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485449"
---
# <a name="how-to-index-cosmos-db-using-an-azure-search-indexer"></a>Jak indeksować Cosmos DB przy użyciu indeksator usługi Azure Search


> [!Note]
> Obsługa interfejsu API usługi MongoDB jest w wersji zapoznawczej i nie przeznaczonych do użycia w środowisku produkcyjnym. [Wersji interfejsu API REST 2019-05-06-Preview](search-api-preview.md) zapewnia tę funkcję. Nie ma portalu ani Obsługa zestawu SDK platformy .NET w tej chwili.
>
> Interfejs API SQL jest ogólnie dostępna.

W tym artykule przedstawiono sposób konfigurowania usługi Azure Cosmos DB [indeksatora](search-indexer-overview.md) do wyodrębniania zawartości, co można wyszukiwać w usłudze Azure Search. Ten przepływ tworzy indeks usługi Azure Search i ładuje je za pomocą istniejący tekst wyodrębnione z usługi Azure Cosmos DB. 

Ponieważ terminologia mogą być mylące, warto zauważyć, że [indeksowanie usługi Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/index-overview) i [indeksowanie usługi Azure Search](search-what-is-an-index.md) różne operacje, unikatowy dla każdej usługi. Przed rozpoczęciem korzystania z usługi Azure Search indeksowanie, bazy danych Azure Cosmos DB musi już istnieć i zawierać dane.

Możesz użyć [portal](#cosmos-indexer-portal), interfejsów API REST lub zestawu .NET SDK do indeksowania zawartości Cosmos. Indeksator usługi Cosmos DB w usłudze Azure Search może przeszukiwać [elementów Azure Cosmos](https://docs.microsoft.com/azure/cosmos-db/databases-containers-items#azure-cosmos-items) dostępne za pośrednictwem tych protokołów:

* [INTERFEJS API SQL](https://docs.microsoft.com/azure/cosmos-db/sql-api-query-reference) 
* [Interfejsu API usługi MongoDB (wersja zapoznawcza)](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction)

> [!Note]
> User Voice ma istniejące elementy uzyskać dodatkową Pomoc interfejsu API. Można rzutować głosu dla interfejsów API Cosmos, które chcesz zobaczyć obsługiwanych w usłudze Azure Search: [Table API](https://feedback.azure.com/forums/263029-azure-search/suggestions/32759746-azure-search-should-be-able-to-index-cosmos-db-tab), [Graph API](https://feedback.azure.com/forums/263029-azure-search/suggestions/13285011-add-graph-databases-to-your-data-sources-eg-neo4), [Apache Cassandra API](https://feedback.azure.com/forums/263029-azure-search/suggestions/32857525-indexer-crawler-for-apache-cassandra-api-in-azu).
>

<a name="cosmos-indexer-portal"></a>

## <a name="use-the-portal"></a>Używanie portalu

Najprostsza metoda indeksowania elementów w usłudze Azure Cosmos jest użycie kreatora w [witryny Azure portal](https://portal.azure.com/). Próbkowanie danych, a podczas odczytywania metadanych w kontenerze, [ **importowania danych** ](search-import-data-portal.md) kreatora w usłudze Azure Search można utworzyć indeks domyślny mapują pola źródłowe na pola indeksu docelowego, a ładowanie indeksu w jednym Operacja. W zależności od rozmiaru i stopnia złożoności danych źródła może mieć indeks wyszukiwania operacyjnej pełny tekst w ciągu kilku minut.

Zalecamy używanie tej samej subskrypcji platformy Azure dla usługi Azure Search i Azure Cosmos DB, w tym samym regionie.

### <a name="1---prepare-source-data"></a>1 — Przygotowanie danych źródłowych

Należy mieć konto Cosmos, bazą danych Azure Cosmos mapowany do interfejsu API SQL lub interfejsu API usługi MongoDB i kontenerem dokumentów JSON. 

Upewnij się, że bazy danych usługi Cosmos DB zawiera dane. [Kreatora importu danych](search-import-data-portal.md) odczytuje metadane i wykonuje próbkowanie danych na potrzeby wnioskowania dotyczącego schematu indeksu, ale również ładowania danych z usługi Cosmos DB. Jeśli brakuje danych, Kreator przestanie się z powodu następującego błędu "błąd podczas wykrywania schematu indeksu na podstawie źródła danych: Nie można zbudować prototypu indeksu, ponieważ źródło danych "emptycollection" nie zwrócił danych".

### <a name="2---start-import-data-wizard"></a>2 — Uruchom Kreatora importu danych

Możesz [uruchom kreatora](search-import-data-portal.md) na pasku poleceń, na stronie usługi Azure Search lub przez kliknięcie przycisku **Dodaj usługę Azure Search** w **ustawienia** lewą sekcji konta magazynu w okienku nawigacji.

   ![Polecenie importu danych w portalu](./media/search-import-data-portal/import-data-cmd2.png "uruchomić Kreatora importu danych")

### <a name="3---set-the-data-source"></a>3 — Ustaw źródło danych

> [!NOTE] 
> Obecnie nie można utworzyć lub edytować **bazy danych MongoDB** źródeł danych przy użyciu witryny Azure portal lub zestawu .NET SDK. Jednak możesz **można** monitorować historię wykonywania bazy danych MongoDB indeksatorów w portalu.

W **źródła danych** stronie źródłowy musi być **Cosmos DB**, z następującymi specyfikacjami:

+ **Nazwa** to nazwa obiektu źródła danych. Po utworzeniu, możesz je dla innych obciążeń.

+ **Konto usługi cosmos DB** powinien być podstawowy lub pomocniczy połączenia z usługi Cosmos DB przy użyciu `AccountEndpoint` i `AccountKey`. To konto Określa, czy data jest rzutowany jako interfejsu API SQL lub interfejsu API usługi Mongo DB

+ **Baza danych** jest istniejącą bazę danych z konta. 

+ **Kolekcja** jest kontenerem dokumentów. Dokumenty muszą istnieć w aby Importowanie powiodło się. 

+ **Zapytanie** może być pusta, jeśli chcesz, aby wszystkie dokumenty, w przeciwnym razie można wpisać zapytania, który wybierze podzbiór dokumentów. 

   ![Definicja źródła danych usługi cosmos DB](media/search-howto-index-cosmosdb/cosmosdb-datasource.png "definicji źródła danych usługi Cosmos DB")

### <a name="4---skip-the-add-cognitive-search-page-in-the-wizard"></a>4 - Pomiń stronę "Dodaj cognitive search" w Kreatorze

Dodawanie umiejętności poznawcze nie jest konieczne do importowania dokumentu. Jeśli nie ma określonych specjalnych potrzeb [obejmują interfejsy API usług Cognitive Services i przekształcenia](cognitive-search-concept-intro.md) do potoku indeksowania, należy pominąć ten krok.

Aby pominąć ten krok, najpierw przejdź do następnej strony.

   ![Przycisk następnej strony dla wyszukiwania poznawczego](media/search-get-started-portal/next-button-add-cog-search.png)

Na tej stronie możesz przejść od razu do dostosowywania indeksu.

   ![Pomijanie kroku Umiejętności poznawcze](media/search-get-started-portal/skip-cog-skill-step.png)

### <a name="5---set-index-attributes"></a>5 — Ustaw atrybuty indeksu

W **indeksu** stronie powinien zostać wyświetlony listy pól z typem danych i serii pola wyboru do ustawiania atrybuty indeksu. Kreator może wygenerować listy pól na podstawie metadanych i przez próbkowanie źródła danych. 

Użytkownik może zbiorcze zaznaczania atrybutów, klikając pole wyboru w górnej części kolumny atrybutu. Wybierz **możliwość pobierania** i **z możliwością wyszukiwania** dla każdego pola, które ma zostać zwrócony do aplikacji klienckich i będą podlegać przetwarzania wyszukiwania pełnotekstowego. Można zauważyć, że liczb całkowitych nie są pełnotekstowego lub rozmyte wyszukiwanie (są oceniane verbatim i często są przydatne w filtrach).

Przejrzyj opis [atrybutami indeksu](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) i [analizatory języka](https://docs.microsoft.com/rest/api/searchservice/language-support) Aby uzyskać więcej informacji. 

Poświęć chwilę, aby przejrzeć wybrane opcje. Po uruchomieniu kreatora, struktur danych fizycznych są tworzone i nie będzie można edytować tych pól bez porzucenie i ponowne utworzenie wszystkich obiektów.

   ![Definicja indeksu cosmos DB](media/search-howto-index-cosmosdb/cosmosdb-index-schema.png "definicję indeksu Cosmos DB")

### <a name="6---create-indexer"></a>6\. Tworzenie indeksatora

W pełni określona, Kreator tworzy trzy różne obiekty w usłudze wyszukiwania. Obiekt źródła danych i indeksu obiektu są zapisywane jako o nazwie zasoby w usłudze Azure Search. Ostatnim krokiem tworzy obiekt indeksatora. Nazewnictwo indeksatora umożliwia mu istnieje jako zasób autonomicznych, której możliwe jest planowanie i zarządzanie nimi niezależnie od obiektu źródłowego indeksu i danych, tworzone po kolei kreatora.

Jeśli nie jesteś zaznajomiony z indeksatorów, *indeksatora* jest zasobem w usłudze Azure Search, która przeszukuje zewnętrzne źródło danych dla zawartość do przeszukiwania. Dane wyjściowe **importowania danych** Kreator jest działanie indeksatora, który przeszukuje źródło danych usługi Cosmos DB, wyodrębnia zawartość, którą można przeszukiwać i importuje je do indeksu usługi Azure Search.

Poniższy zrzut ekranu przedstawia domyślnej konfiguracji indeksatora. Możesz przełączyć się do **raz** Jeśli chcesz uruchomić indeksatora jednorazowo. Kliknij przycisk **przesyłania** uruchom kreatora do tworzenia wszystkich obiektów. Indeksowanie rozpoczyna się natychmiast.

   ![Definicja indeksatora usługi cosmos DB](media/search-howto-index-cosmosdb/cosmosdb-indexer.png "definicja indeksatora usługi Cosmos DB")

Można monitorować importowanie danych na stronach portalu. Powiadomienia o postępie wskazują stan indeksowania i liczby dokumentów są przekazywane. 

Po zakończeniu indeksowania, można użyć [Eksploratora wyszukiwania](search-explorer.md) wykonywać zapytania w indeksie.

> [!NOTE]
> Jeśli nie widzisz danych, których oczekujesz, może być konieczne ustawienie więcej atrybutów w kolejnych pól. Usuwanie indeksu i indeksatora, właśnie utworzone i przejść przez kreatora ponownie, modyfikując wybrane atrybuty indeksu w kroku 5. 

<a name="cosmosdb-indexer-rest"></a>

## <a name="use-rest-apis"></a>Używanie interfejsów API REST

Można użyć interfejsu API REST do indeksu usługi Azure Cosmos DB danych, zgodnie z przepływu pracy trzyczęściowej wspólne dla wszystkich indeksatorów w usłudze Azure Search: Utwórz źródło danych, Utwórz indeks, Utwórz indeksator. Wyodrębnianie danych z magazynu Cosmos występuje, gdy wniosek tworzenie indeksatora. Po zakończeniu tego żądania, konieczne będzie odpytywalny indeksu. 

Jeśli dokonujesz oceny bazy danych MongoDB, należy użyć pozostałe `api-version=2019-05-06-Preview` można utworzyć źródła danych.

W ramach konta usługi Cosmos DB można, czy mają kolekcja do automatycznego indeksowania wszystkich dokumentów. Domyślnie wszystkie dokumenty są automatycznie indeksowane, ale możesz wyłączyć automatyczne indeksowanie. Po wyłączeniu indeksowanie dokumentów można uzyskać dostęp tylko za pośrednictwem ich linków do samego siebie lub przez zapytania za pomocą dokumentów identyfikator. Usługa Azure Search wymaga usługi Cosmos DB automatyczne indeksowanie w celu włączenia w kolekcji, które będą indeksowane przez usługę Azure Search. 

> [!WARNING]
> Usługa Azure Cosmos DB to następna generacja bazy danych documentdb. Wcześniej z wersją interfejsu API **2017-11-11** wystarczą `documentdb` składni. Oznacza to, że można określić typu źródła danych, co `cosmosdb` lub `documentdb`. Począwszy od wersji interfejsu API **2019-05-06** zarówno interfejsów API usługi Azure Search, jak i portalu obsługują tylko `cosmosdb` składni zgodnie z instrukcjami w tym artykule. Oznacza to, że typ źródła danych muszą `cosmosdb` Jeśli chcesz połączyć się z punktem końcowym usługi Cosmos DB.

### <a name="1---assemble-inputs-for-the-request"></a>1 — złożyć dane wejściowe dla żądania

Dla każdego żądania należy podać nazwę usługi i klucz administratora dla usługi Azure Search (w nagłówku POST), a nazwa konta magazynu i klucz do magazynu obiektów blob. Możesz użyć [Postman](search-get-started-postman.md) do wysyłania żądań HTTP do usługi Azure Search.

Skopiuj następujące cztery wartości do Notatnika, dzięki czemu można je wkleić do żądania:

+ Nazwa usługi w usłudze Azure Search
+ Klucz administratora w usłudze Azure Search
+ Parametry połączenia usługi cosmos DB

Te wartości można znaleźć w portalu:

1. Na stronach portalu usługi Azure Search skopiuj adres URL usługi wyszukiwania na stronie Przegląd.

2. W okienku nawigacji po lewej stronie kliknij **klucze** , a następnie skopiuj podstawowy lub pomocniczy klucza (są one równoważne).

3. Przełącz się do stron portalu dla konta magazynu usługi Cosmos. W okienku nawigacji po lewej stronie w obszarze **ustawienia**, kliknij przycisk **klucze**. Ta strona zawiera identyfikator URI, dwa zestawy parametrów połączenia, a dwa zestawy kluczy. Skopiuj jeden z parametrów połączenia do Notatnika.

### <a name="2---create-a-data-source"></a>2 — Tworzenie źródła danych

A **źródła danych** Określa dane do indeksu, poświadczeń i zasad do identyfikowania zmian w danych (takich jak dokumenty zmodyfikowany lub usunięty wewnątrz kolekcji). Źródło danych jest zdefiniowane jako niezależnym zasobem, dzięki czemu mogą być używane przez wiele indeksatorów.

Aby utworzyć źródło danych, sformułuj żądanie POST:

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mycosmosdbdatasource",
        "type": "cosmosdb",
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

| Pole   | Opis |
|---------|-------------|
| **name** | Wymagany. Wybierz dowolną nazwę do reprezentowania obiektu źródła danych. |
|**type**| Wymagany. Musi być `cosmosdb`. |
|**Poświadczenia** | Wymagany. Musi być ciąg połączenia usługi Cosmos DB.<br/>Dla kolekcji SQL parametry połączenia są w następującym formacie: `AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>`<br/>Kolekcje bazy danych MongoDB, można dodać **rodzaju interfejsu API = MongoDb** parametry połączenia:<br/>`AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=MongoDb`<br/>Należy unikać numerów portów w programie adres url punktu końcowego. Jeśli uwzględniony numer portu usługi Azure Search można indeksować bazy danych Azure Cosmos DB.|
| **Kontener** | zawiera następujące elementy: <br/>**name**: Wymagany. Określ identyfikator kolekcji bazy danych mają być indeksowane.<br/>**Zapytanie**: Opcjonalny. Można określić zapytanie w celu spłaszczenia dowolny dokument JSON do płaski schemat, który usługa Azure Search umożliwia indeksowanie.<br/>Dla kolekcji usługi MongoDB zapytania nie są obsługiwane. |
| **dataChangeDetectionPolicy** | Zalecane. Zobacz [indeksowania zmienione dokumenty](#DataChangeDetectionPolicy) sekcji.|
|**dataDeletionDetectionPolicy** | Opcjonalny. Zobacz [indeksowanie dokumentów usunięte](#DataDeletionDetectionPolicy) sekcji.|

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
        "tags": ["azure", "cosmosdb", "search"]
    }

Zapytanie filtru:

    SELECT * FROM c WHERE c.company = "microsoft" and c._ts >= @HighWaterMark ORDER BY c._ts

Spłaszczanie kwerendy:

    SELECT c.id, c.userId, c.contact.firstName, c.contact.lastName, c.company, c._ts FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts
    
    
Projekcja zapytanie:

    SELECT VALUE { "id":c.id, "Name":c.contact.firstName, "Company":c.company, "_ts":c._ts } FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts


Zapytanie spłaszczania tablicy:

    SELECT c.id, c.userId, tag, c._ts FROM c JOIN tag IN c.tags WHERE c._ts >= @HighWaterMark ORDER BY c._ts


### <a name="3---create-a-target-search-index"></a>3 — Tworzenie docelowym indeksem wyszukiwania 

[Tworzenie indeksu usługi Azure Search docelowego](/rest/api/searchservice/create-index) Jeśli nie masz jeszcze takiego. Poniższy przykład tworzy indeks z polem Identyfikator i opis:

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
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
| Bool |Edm.Boolean, Edm.String |
| Numery, które przypominają liczb całkowitych |Edm.Int32, Edm.Int64, Edm.String |
| Numery tego wygląd, takich jak punkty liczb zmiennoprzecinkowych |Edm.Double, Edm.String |
| String |Edm.String |
| Tablice typów pierwotnych, na przykład ["a", "b", "c"] |Collection(Edm.String) |
| Ciągi, które mają postać daty |Edm.DateTimeOffset, Edm.String |
| Obiekty GeoJSON, na przykład {"type": "Point", "coordinates": [długie, lat]} |Edm.GeographyPoint |
| Inne obiekty JSON |ND |

### <a name="4---configure-and-run-the-indexer"></a>4 — Konfigurowanie i uruchamianie indeksatora

Po utworzeniu indeks i źródło danych możesz przystąpić do tworzenia indeksatora:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "mycosmosdbindexer",
      "dataSourceName" : "mycosmosdbdatasource",
      "targetIndexName" : "mysearchindex",
      "schedule" : { "interval" : "PT2H" }
    }

Ten indeksator jest uruchamiane co dwie godziny (interwał harmonogramu jest ustawiona na "PT2H"). Aby uruchomić indeksatora co 30 minut, należy ustawić interwał o "PT30M". Najkrótszy obsługiwany interwał wynosi 5 minut. Harmonogram jest opcjonalnie — w przypadku pominięcia, indeksatora jest uruchamiany tylko raz, po jego utworzeniu. Jednak w dowolnym momencie można uruchomić indeksatora na żądanie.   

Aby uzyskać szczegółowe informacje na temat tworzenia interfejsu API indeksatora, zapoznaj się [tworzenie indeksatora](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

Aby uzyskać więcej informacji na temat definiowania harmonogramy indeksatora zobacz [sposób tworzenia harmonogramu indeksatorów usługi Azure Search](search-howto-schedule-indexers.md).

## <a name="use-net"></a>Korzystanie z platformy .NET

Dostępnego ogólnie zestawu SDK platformy .NET ma pełną parzystości przy użyciu interfejsu API REST jest ogólnie dostępna. Firma Microsoft zaleca przejrzenie poprzedniej sekcji interfejsu API REST, aby dowiedzieć się, pojęcia i przepływu pracy oraz wymagania. Następnie można się odwoływać do poniższej dokumentacji interfejsu API platformy .NET do zaimplementowania indeksatora JSON w kodzie zarządzanym.

+ [microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

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

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mycosmosdbdatasource",
        "type": "cosmosdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCosmosDbCollectionId" },
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
