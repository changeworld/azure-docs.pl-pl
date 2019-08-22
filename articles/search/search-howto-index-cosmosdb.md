---
title: Indeksowanie Azure Cosmos DB źródła danych — Azure Search
description: Przeszukaj Azure Cosmos DB źródło danych i pozyskaj dane w indeksie pełnotekstowym z możliwością wyszukiwania pełnotekstowego w Azure Search. Indeksatory automatyzują pozyskiwanie danych dla wybranych źródeł danych, takich jak Azure Cosmos DB.
ms.date: 05/02/2019
author: mgottein
manager: nitinme
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 802a4e9c6191d33051eb075543691845595bc9c3
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69656692"
---
# <a name="how-to-index-cosmos-db-using-an-azure-search-indexer"></a>Jak indeksować Cosmos DB przy użyciu indeksatora Azure Search


> [!Note]
> Obsługa interfejsu API MongoDB jest w wersji zapoznawczej i nie jest przeznaczona do użycia w środowisku produkcyjnym. [Interfejs API REST w wersji 2019-05-06 —](search-api-preview.md) wersja zapoznawcza zawiera tę funkcję. W tej chwili nie ma obsługi portalu lub zestawu SDK platformy .NET.
>
> Interfejs API SQL jest ogólnie dostępny.

W tym artykule opisano sposób konfigurowania indeksatora Azure Cosmos DB [](search-indexer-overview.md) w celu wyodrębnienia zawartości i przeszukiwania jej w programie Azure Search. Ten przepływ pracy tworzy indeks Azure Search i ładuje go z istniejącym tekstem wyodrębnionym z Azure Cosmos DB. 

Ze względu na to, że terminologia może być myląca, warto zauważyć, że [Azure Cosmos DB indeksowania](https://docs.microsoft.com/azure/cosmos-db/index-overview) i [Azure Search indeksowania](search-what-is-an-index.md) to różne operacje, unikatowe dla każdej usługi. Przed rozpoczęciem Azure Search indeksowania, baza danych Azure Cosmos DB musi już istnieć i zawierać dane.

Za pomocą [portalu](#cosmos-indexer-portal), interfejsów API REST lub zestawu .NET SDK można indeksować zawartość Cosmos. Indeksator Cosmos DB w Azure Search umożliwia przeszukiwanie [elementów Cosmos platformy Azure](https://docs.microsoft.com/azure/cosmos-db/databases-containers-items#azure-cosmos-items) , do których dostęp odbywa się za pomocą tych protokołów:

* [INTERFEJS API SQL](https://docs.microsoft.com/azure/cosmos-db/sql-api-query-reference) 
* [Interfejs API MongoDB (wersja zapoznawcza)](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction)

> [!Note]
> Głos użytkownika ma istniejące elementy do obsługi dodatkowych interfejsów API. Możesz odłożyć głos na Cosmos interfejsy API, które chcesz zobaczyć jako obsługiwane w Azure Search: [Interfejs API tabel](https://feedback.azure.com/forums/263029-azure-search/suggestions/32759746-azure-search-should-be-able-to-index-cosmos-db-tab), [interfejs API programu Graph](https://feedback.azure.com/forums/263029-azure-search/suggestions/13285011-add-graph-databases-to-your-data-sources-eg-neo4), [interfejs API Apache Cassandra](https://feedback.azure.com/forums/263029-azure-search/suggestions/32857525-indexer-crawler-for-apache-cassandra-api-in-azu).
>

<a name="cosmos-indexer-portal"></a>

## <a name="use-the-portal"></a>Używanie portalu

Najprostszym sposobem indeksowania elementów usługi Azure Cosmos jest użycie Kreatora w [Azure Portal](https://portal.azure.com/). Poprzez próbkowanie danych i odczytywanie metadanych w kontenerze Kreator [**importowania danych**](search-import-data-portal.md) w Azure Search może utworzyć domyślny indeks, zmapować pola źródłowe na docelowe pola indeksu i załadować indeks w ramach jednej operacji. W zależności od rozmiaru i stopnia złożoności danych źródłowych można mieć indeks wyszukiwania pełnotekstowego w ciągu kilku minut.

Zalecamy używanie tej samej subskrypcji platformy Azure dla Azure Search i Azure Cosmos DB, najlepiej w tym samym regionie.

### <a name="1---prepare-source-data"></a>1 — Przygotowywanie danych źródłowych

Należy mieć konto Cosmos, bazę danych Cosmos platformy Azure zamapowane na interfejs API SQL lub interfejs API MongoDB oraz kontener dokumentów JSON. 

Upewnij się, że baza danych Cosmos DB zawiera dane. [Kreator importu danych](search-import-data-portal.md) odczytuje metadane i wykonuje próbkowanie danych w celu wywnioskowania schematu indeksu, ale również ładuje dane z Cosmos DB. W przypadku braku danych Kreator zatrzyma się z powodu błędu "błąd podczas wykrywania schematu indeksu ze źródła danych: Nie można utworzyć indeksu prototypowego, ponieważ element DataSource "emptycollection" nie zwrócił żadnych danych ".

### <a name="2---start-import-data-wizard"></a>2 — Uruchom Kreatora importu danych

Kreatora można [uruchomić](search-import-data-portal.md) z poziomu paska poleceń na stronie usługi Azure Search lub klikając pozycję **Dodaj Azure Search** w sekcji **Ustawienia** w lewym okienku nawigacji konta magazynu.

   ![Importuj dane — polecenie w portalu](./media/search-import-data-portal/import-data-cmd2.png "Uruchom Kreatora importu danych")

### <a name="3---set-the-data-source"></a>3 — Ustawianie źródła danych

> [!NOTE] 
> Obecnie nie można tworzyć ani edytować źródeł danych **MongoDB** za pomocą Azure Portal lub zestawu .NET SDK. **Można** jednak monitorować historię wykonywania indeksatorów MongoDB w portalu.

Na stronie **Źródło danych** Źródło musi być **Cosmos DB**z następującymi specyfikacjami:

+ **Nazwa** to nazwa obiektu źródła danych. Po utworzeniu można wybrać go dla innych obciążeń.

+ **Konto Cosmos DB** powinno być podstawowym lub pomocniczym ciągiem połączenia z Cosmos DB, z `AccountEndpoint` i. `AccountKey` Konto określa, czy dane są rzutowane jako interfejs API SQL lub interfejs API usługi Mongo DB

+ **Baza danych** jest istniejącą bazą danych z konta. 

+ **Kolekcja** jest kontenerem dokumentów. Aby importowanie powiodło się, muszą istnieć dokumenty. 

+ **Zapytanie** może być puste, jeśli chcesz uzyskać wszystkie dokumenty, w przeciwnym razie możesz wprowadzić zapytanie, które wybierze podzestaw dokumentu. 

   ![Cosmos DB definicję źródła danych](media/search-howto-index-cosmosdb/cosmosdb-datasource.png "Cosmos DB definicję źródła danych")

### <a name="4---skip-the-add-cognitive-search-page-in-the-wizard"></a>4 — Pomiń stronę "Dodawanie wyszukiwania poznawczego" w Kreatorze

Dodawanie umiejętności poznawczych nie jest konieczne do importowania dokumentów. O ile nie trzeba [uwzględniać interfejsy API usług Cognitive Services i transformacji](cognitive-search-concept-intro.md) do potoku indeksowania, należy pominąć ten krok.

Aby pominąć ten krok, najpierw przejdź do następnej strony.

   ![Przycisk następnej strony dla wyszukiwania poznawczego](media/search-get-started-portal/next-button-add-cog-search.png)

Na tej stronie możesz przejść do dostosowywania indeksu.

   ![Pomijanie kroku Umiejętności poznawcze](media/search-get-started-portal/skip-cog-skill-step.png)

### <a name="5---set-index-attributes"></a>5 — Ustawianie atrybutów indeksu

Na stronie **indeks** powinna zostać wyświetlona lista pól z typem danych oraz seria pola wyboru służących do ustawiania atrybutów indeksu. Kreator może wygenerować listę pól na podstawie metadanych i przez próbkowanie danych źródłowych. 

Możesz wybrać atrybuty zbiorcze, klikając pole wyboru u góry kolumny atrybutu. Wybierz opcję **pobierania** i **wyszukiwania** dla każdego pola, które ma zostać zwrócone do aplikacji klienckiej i podlegające przetwarzaniu wyszukiwania pełnotekstowego. Zauważ, że liczby całkowite nie są pełnymi tekstami ani rozmyte wyszukiwania (liczby są oceniane Verbatim i często są przydatne w filtrach).

Przejrzyj opis [atrybutów indeksu](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) i analizatorów [języka](https://docs.microsoft.com/rest/api/searchservice/language-support) , aby uzyskać więcej informacji. 

Poświęć chwilę, aby przejrzeć wybrane opcje. Po uruchomieniu kreatora są tworzone fizyczne struktury danych i nie będzie można edytować tych pól bez porzucania i ponownego tworzenia wszystkich obiektów.

   ![Definicja indeksu Cosmos DB](media/search-howto-index-cosmosdb/cosmosdb-index-schema.png "Definicja indeksu Cosmos DB")

### <a name="6---create-indexer"></a>6 — Tworzenie indeksatora

W pełni określony Kreator tworzy trzy odrębne obiekty w usłudze wyszukiwania. Obiekt źródła danych i obiekt indeksu są zapisywane jako zasoby nazwane w usłudze Azure Search. Ostatnim krokiem jest utworzenie obiektu indeksatora. Nazwa indeksatora pozwala na jego istnienie jako zasób autonomiczny, który można zaplanować i zarządzać niezależnie od obiektu indeksu i źródła danych, utworzonego w tej samej sekwencji kreatora.

Jeśli nie znasz indeksatorów, *indeksator* jest zasobem w Azure Search przeszukiwanym zewnętrznym źródłem danych dla zawartości możliwej do przeszukania. Dane wyjściowe kreatora **importu danych** to indeksator, który przeszukuje źródło danych Cosmos DB, wyodrębnia zawartość z możliwością przeszukiwania i importuje go do indeksu na Azure Search.

Poniższy zrzut ekranu przedstawia domyślną konfigurację indeksatora. Jeśli chcesz uruchomić indeksator jednorazowo, możesz przełączyć się do niego. Kliknij przycisk **Prześlij** , aby uruchomić kreatora i utworzyć wszystkie obiekty. Indeksowanie rozpocznie się natychmiast.

   ![Definicja indeksatora Cosmos DB](media/search-howto-index-cosmosdb/cosmosdb-indexer.png "Definicja indeksatora Cosmos DB")

Import danych można monitorować na stronach portalu. Powiadomienia o postępie wskazują stan indeksowania oraz liczbę przekazywanych dokumentów. 

Po zakończeniu indeksowania można użyć [Eksploratora wyszukiwania](search-explorer.md) do wykonywania zapytań względem indeksu.

> [!NOTE]
> Jeśli nie widzisz danych, których oczekujesz, może być konieczne ustawienie więcej atrybutów na więcej pól. Usuń indeks i indeksator, który właśnie został utworzony, a następnie ponownie wykonaj kroki kreatora, modyfikując wybrane opcje dla atrybutów indeksu w kroku 5. 

<a name="cosmosdb-indexer-rest"></a>

## <a name="use-rest-apis"></a>Używanie interfejsów API REST

Za pomocą interfejsu API REST można indeksować Azure Cosmos DB dane, wykonując wieloczęściowy przepływ pracy wspólny dla wszystkich indeksatorów w Azure Search: tworzenie źródła danych, tworzenie indeksu i tworzenie indeksatora. Wyodrębnianie danych z magazynu Cosmos odbywa się po przesłaniu żądania utworzenia indeksatora. Po zakończeniu tego żądania będzie miał indeks queryable. 

Jeśli oceniasz MongoDB, musisz użyć reszty `api-version=2019-05-06-Preview` do utworzenia źródła danych.

Na koncie Cosmos DB możesz wybrać, czy kolekcja ma automatycznie indeksować wszystkie dokumenty. Domyślnie wszystkie dokumenty są indeksowane automatycznie, ale można wyłączyć automatyczne indeksowanie. Gdy indeksowanie jest wyłączone, do dokumentów można uzyskać dostęp tylko za pośrednictwem własnych linków lub zapytań przy użyciu identyfikatora dokumentu. Azure Search wymaga włączenia Cosmos DB automatycznego indeksowania w kolekcji, która będzie indeksowana przez Azure Search. 

> [!WARNING]
> Azure Cosmos DB to Kolejna generacja DocumentDB. Wcześniej z interfejsem API w wersji **2017-11-11** , `documentdb` można użyć składni. Oznacza to, że można określić typ źródła danych jako `cosmosdb` lub. `documentdb` Począwszy od interfejsu API w wersji **2019-05-06** interfejsy API i Portal Azure Search obsługują `cosmosdb` tylko składnię, jak opisano w tym artykule. Oznacza to, że typ źródła danych musi `cosmosdb` nawiązać połączenie z punktem końcowym Cosmos DB.

### <a name="1---assemble-inputs-for-the-request"></a>1 — Tworzenie danych wejściowych dla żądania

Dla każdego żądania należy podać nazwę usługi i klucz administratora dla Azure Search (w nagłówku) oraz nazwę i klucz konta magazynu dla usługi BLOB Storage. Można użyć programu [Poster](search-get-started-postman.md) do wysyłania żądań HTTP do Azure Search.

Skopiuj następujące cztery wartości do Notatnika, aby można było je wkleić do żądania:

+ Nazwa usługi Azure Search
+ Klucz administratora Azure Search
+ Cosmos DB parametry połączenia

Te wartości można znaleźć w portalu:

1. Na stronach portalu Azure Search Skopiuj adres URL usługi Search na stronie Przegląd.

2. W okienku nawigacji po lewej stronie kliknij pozycję **klucze** , a następnie skopiuj klucz podstawowy lub pomocniczy (są one równoważne).

3. Przejdź do strony portalu dla konta magazynu Cosmos. W okienku nawigacji po lewej stronie w obszarze **Ustawienia**kliknij pozycję **klucze**. Ta strona zawiera identyfikator URI, dwa zestawy parametrów połączenia i dwa zestawy kluczy. Skopiuj jeden z parametrów połączenia do Notatnika.

### <a name="2---create-a-data-source"></a>2 — Tworzenie źródła danych

**Źródło danych** określa dane do indeksowania, poświadczeń i zasad służących do identyfikowania zmian w danych (takich jak zmodyfikowane lub usunięte dokumenty wewnątrz kolekcji). Źródło danych jest zdefiniowane jako zasób niezależny, aby mógł być używany przez wiele indeksatorów.

Aby utworzyć źródło danych, należy sformułować żądanie POST:

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

Treść żądania zawiera definicję źródła danych, która powinna zawierać następujące pola:

| Pole   | Opis |
|---------|-------------|
| **name** | Wymagana. Wybierz dowolną nazwę reprezentującą obiekt źródła danych. |
|**type**| Wymagane. Musi być `cosmosdb`. |
|**uwierzytelniające** | Wymagany. Musi być Cosmos DB parametrami połączenia.<br/>W przypadku kolekcji SQL parametry połączenia mają następujący format:`AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>`<br/>W przypadku kolekcji MongoDB Dodaj **rodzaju interfejsu API = MongoDB** do parametrów połączenia:<br/>`AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=MongoDb`<br/>Należy unikać numerów portów w adresie URL punktu końcowego. Jeśli dołączysz numer portu, Azure Search nie będzie można indeksować Azure Cosmos DB bazy danych.|
| **wbudowane** | Zawiera następujące elementy: <br/>**name**: Wymagana. Określ identyfikator kolekcji baz danych do indeksowania.<br/>**zapytanie**: Opcjonalna. Możesz określić zapytanie, aby spłaszczyć dowolny dokument JSON do prostego schematu, który Azure Search może indeksować.<br/>W przypadku kolekcji MongoDB zapytania nie są obsługiwane. |
| **dataChangeDetectionPolicy** | Rekomendowane. Zobacz sekcję [indeksowanie zmienionych dokumentów](#DataChangeDetectionPolicy) .|
|**dataDeletionDetectionPolicy** | Opcjonalny. Zobacz sekcję [indeksowanie usuniętych dokumentów](#DataDeletionDetectionPolicy) .|

### <a name="using-queries-to-shape-indexed-data"></a>Używanie zapytań w celu kształtowania indeksowanych danych
Możesz określić zapytanie SQL do spłaszczania zagnieżdżonych właściwości lub tablic, właściwości JSON projektu i przefiltrować dane, które mają być indeksowane. 

> [!WARNING]
> Zapytania niestandardowe nie są obsługiwane dla kolekcji **MongoDB** : `container.query` parametr musi mieć wartość null lub być pominięty. Jeśli musisz użyć zapytania niestandardowego, poinformuj nas o [głosowaniu użytkownika](https://feedback.azure.com/forums/263029-azure-search).

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

Spłaszczanie zapytania:

    SELECT c.id, c.userId, c.contact.firstName, c.contact.lastName, c.company, c._ts FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts
    
    
Zapytanie projekcji:

    SELECT VALUE { "id":c.id, "Name":c.contact.firstName, "Company":c.company, "_ts":c._ts } FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts


Zapytanie spłaszczone tablicy:

    SELECT c.id, c.userId, tag, c._ts FROM c JOIN tag IN c.tags WHERE c._ts >= @HighWaterMark ORDER BY c._ts


### <a name="3---create-a-target-search-index"></a>3 — Tworzenie docelowego indeksu wyszukiwania 

[Utwórz docelowy indeks Azure Search](/rest/api/searchservice/create-index) , jeśli jeszcze go nie masz. Poniższy przykład tworzy indeks z polem ID i Description:

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

Upewnij się, że schemat indeksu docelowego jest zgodny ze schematem źródłowych dokumentów JSON lub danymi wyjściowymi projekcji zapytań niestandardowych.

> [!NOTE]
> W przypadku kolekcji partycjonowanych domyślnym kluczem dokumentu jest Azure Cosmos DB `_rid` właściwość, która Azure Search automatycznie zmienia nazwy `rid` , ponieważ nazwy pól nie mogą rozpoczynać się od znaku undescore. Ponadto wartości Azure Cosmos DB `_rid` zawierają znaki, które są nieprawidłowe w kluczach Azure Search. Z `_rid` tego powodu wartości są zakodowane w formacie base64.
> 
> W przypadku kolekcji MongoDB Azure Search automatycznie zmienia nazwę `_id` właściwości na. `doc_id`  

### <a name="mapping-between-json-data-types-and-azure-search-data-types"></a>Mapowanie między typami danych JSON i Azure Search typami danych
| Typ danych JSON | Zgodne typy pól indeksu docelowego |
| --- | --- |
| Bool |Edm.Boolean, Edm.String |
| Liczby, które wyglądają jak liczby całkowite |Edm.Int32, Edm.Int64, Edm.String |
| Liczby, które wyglądają jak zmiennoprzecinkowe |Edm.Double, Edm.String |
| String |Edm.String |
| Tablice typów pierwotnych, na przykład ["a", "b", "c"] |Collection(Edm.String) |
| Ciągi, które wyglądają jak daty |Edm.DateTimeOffset, Edm.String |
| Obiekty GEOJSON, na przykład {"Type": "Point", "współrzędne": [Long, lat]} |Edm.GeographyPoint |
| Inne obiekty JSON |ND |

### <a name="4---configure-and-run-the-indexer"></a>4 — Konfigurowanie i uruchamianie indeksatora

Po utworzeniu indeksu i źródła danych można rozpocząć tworzenie indeksatora:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "mycosmosdbindexer",
      "dataSourceName" : "mycosmosdbdatasource",
      "targetIndexName" : "mysearchindex",
      "schedule" : { "interval" : "PT2H" }
    }

Ten indeksator działa co dwie godziny (interwał harmonogramu jest ustawiony na wartość "PT2H"). Aby uruchomić indeksator co 30 minut, ustaw interwał na wartość "PT30M". Najkrótszy obsługiwany interwał to 5 minut. Harmonogram jest opcjonalny — w przypadku pominięcia, indeksator jest uruchamiany tylko raz, gdy zostanie utworzony. Można jednak uruchomić indeksator na żądanie w dowolnym momencie.   

Aby uzyskać więcej informacji na temat interfejsu API tworzenia indeksatora, zapoznaj się z tematem [Tworzenie indeksatora](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

Więcej informacji o definiowaniu harmonogramów indeksatorów znajduje się w temacie [jak zaplanować indeksatory dla Azure Search](search-howto-schedule-indexers.md).

## <a name="use-net"></a>Korzystanie z platformy .NET

Ogólnie dostępny zestaw .NET SDK ma pełną zgodność z ogólnie dostępnym interfejsem API REST. Zalecamy zapoznanie się z poprzednią sekcją interfejsu API REST, aby poznać koncepcje, przepływ pracy i wymagania. Następnie można zapoznać się z następującą dokumentacją interfejsu API platformy .NET, aby zaimplementować indeksator JSON w kodzie zarządzanym.

+ [microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

<a name="DataChangeDetectionPolicy"></a>

## <a name="indexing-changed-documents"></a>Indeksowanie zmienionych dokumentów

Celem zasad wykrywania zmian danych jest efektywne zidentyfikowanie zmienionych elementów danych. Obecnie jedynymi obsługiwanymi zasadami są `High Water Mark` zasady `_ts` korzystające z właściwości (Sygnatura czasowa) dostarczonej przez Azure Cosmos DB, która jest określona w następujący sposób:

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

Korzystanie z tych zasad jest zdecydowanie zalecane, aby zapewnić dobrą wydajność indeksatora. 

Jeśli używasz zapytania niestandardowego, upewnij się, że `_ts` właściwość jest rzutowana przez zapytanie.

<a name="IncrementalProgress"></a>

### <a name="incremental-progress-and-custom-queries"></a>Postęp przyrostowy i zapytania niestandardowe

Przyrostowy postęp podczas indeksowania zapewnia, że jeśli wykonywanie indeksatora zostanie przerwane przez przejściowe błędy lub limit czasu wykonywania, indeksator może zostać pobrany w miejscu, w którym zostanie pozostawiony po następnym uruchomieniu, zamiast konieczności ponownego indeksowania całej kolekcji od podstaw. Jest to szczególnie ważne podczas indeksowania dużych kolekcji. 

Aby włączyć przyrostowy postęp przy użyciu zapytania niestandardowego, upewnij się, że zapytanie porządkuje wyniki według `_ts` kolumny. Pozwala to na okresowe sprawdzanie, których Azure Search używa do zapewnienia przyrostowego postępu w przypadku wystąpienia błędów.   

W niektórych przypadkach, nawet jeśli zapytanie zawiera `ORDER BY [collection alias]._ts` klauzulę, Azure Search nie może wnioskować, że zapytanie jest uporządkowane `_ts`według. Możesz powiedzieć Azure Search, że wyniki są uporządkowane przy użyciu `assumeOrderByHighWaterMarkColumn` właściwości konfiguracja. Aby określić tę wskazówkę, należy utworzyć lub zaktualizować indeksator w następujący sposób: 

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "assumeOrderByHighWaterMarkColumn" : true } }
    } 

<a name="DataDeletionDetectionPolicy"></a>

## <a name="indexing-deleted-documents"></a>Indeksowanie usuniętych dokumentów

Po usunięciu wierszy z kolekcji, zazwyczaj chcesz usunąć te wiersze z indeksu wyszukiwania. Celem zasad wykrywania usuwania danych jest efektywne identyfikowanie usuniętych elementów danych. Obecnie jedynymi obsługiwanymi zasadami są `Soft Delete` zasady (usuwanie jest oznaczone flagą niektórych rodzajów sortowania), które są określone w następujący sposób:

    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }

Jeśli używasz zapytania niestandardowego, upewnij się, że właściwość, do której się odwołuje `softDeleteColumnName` się, jest rzutowana przez zapytanie.

Poniższy przykład tworzy źródło danych z zasadami usuwania nietrwałego:

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

Gratulacje! Wiesz już, jak zintegrować Azure Cosmos DB z Azure Search za pomocą indeksatora.

* Aby dowiedzieć się więcej na temat Azure Cosmos DB, zobacz [stronę usługi Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).
* Aby dowiedzieć się więcej na temat Azure Search, zobacz [stronę usługi wyszukiwania](https://azure.microsoft.com/services/search/).
