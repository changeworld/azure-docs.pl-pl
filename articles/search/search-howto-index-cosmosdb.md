---
title: Wyszukiwanie danych usługi Azure Cosmos DB
titleSuffix: Azure Cognitive Search
description: Importowanie danych z usługi Azure Cosmos DB do indeksu z wyszukuj w usłudze Azure Cognitive Search. Indeksatory automatyzują pozyskiwanie danych dla wybranych źródeł danych, takich jak usługa Azure Cosmos DB.
author: mgottein
manager: nitinme
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/02/2020
ms.openlocfilehash: d1723b6c5d56554fbff576f6a07e37455845bda4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283006"
---
# <a name="how-to-index-cosmos-db-data-using-an-indexer-in-azure-cognitive-search"></a>Jak indeksować dane usługi Cosmos DB przy użyciu indeksatora usługi Azure Cognitive Search 

> [!IMPORTANT] 
> Interfejs API sql jest ogólnie dostępny.
> Interfejs API MongoDB, interfejs API Gremlin i obsługa interfejsu API Cassandra są obecnie w publicznej wersji zapoznawczej. Funkcja w wersji zapoznawczej jest dostarczana bez umowy dotyczącej poziomu usług i nie jest zalecana dla obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Możesz poprosić o dostęp do podglądów, wypełniając [ten formularz](https://aka.ms/azure-cognitive-search/indexer-preview). Interfejs [API REST w wersji 2019-05-06-Preview](search-api-preview.md) udostępnia funkcje w wersji zapoznawczej. Obecnie istnieje ograniczona obsługa portalu i nie ma obsługi sdk .NET.

> [!WARNING]
> Tylko kolekcje usługi Cosmos DB z [zasadami indeksowania](https://docs.microsoft.com/azure/cosmos-db/index-policy) ustawionymi na [Spójne](https://docs.microsoft.com/azure/cosmos-db/index-policy#indexing-mode) są obsługiwane przez usługę Azure Cognitive Search. Indeksowanie kolekcji z zasad indeksowania z opóźnieniem nie jest zalecane i może spowodować brakujące dane. Kolekcje z wyłączonym indeksowania nie są obsługiwane.

W tym artykule pokazano, jak skonfigurować [indeksator](search-indexer-overview.md) usługi Azure Cosmos DB, aby wyodrębnić zawartość i uczynić ją przeszukiwaną w usłudze Azure Cognitive Search. Ten przepływ pracy tworzy indeks usługi Azure Cognitive Search i ładuje go z istniejącym tekstem wyodrębnionym z usługi Azure Cosmos DB. 

Ponieważ terminologia może być mylące, warto zauważyć, że [indeksowanie usługi Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/index-overview) i [indeksowanie usługi Azure Cognitive Search](search-what-is-an-index.md) są różne operacje, unikatowe dla każdej usługi. Przed rozpoczęciem indeksowania usługi Azure Cognitive Search baza danych usługi Azure Cosmos DB musi już istnieć i zawierać dane.

Indeksator usługi Cosmos DB w usłudze Azure Cognitive Search może indeksować [elementy usługi Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/databases-containers-items#azure-cosmos-items) dostępne za pośrednictwem różnych protokołów. 

+ W przypadku [interfejsu API SQL](https://docs.microsoft.com/azure/cosmos-db/sql-api-query-reference), który jest ogólnie dostępny, można użyć [portalu](#cosmos-indexer-portal), interfejsu [API REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations)lub [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet) do utworzenia źródła danych i indeksatora.

+ W przypadku [interfejsu API MongoDB (wersja zapoznawcza)](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction)można użyć [portalu](#cosmos-indexer-portal) lub interfejsu API REST w [wersji 2019-05-06-Preview,](search-api-preview.md) aby utworzyć źródło danych i indeksator.

+ W przypadku [interfejsu API Cassandra (wersja zapoznawcza)](https://docs.microsoft.com/azure/cosmos-db/cassandra-introduction) i [interfejsu API Gremlin (wersja zapoznawcza)](https://docs.microsoft.com/azure/cosmos-db/graph-introduction)można używać tylko [interfejsu API REST w wersji 2019-05-06-Preview,](search-api-preview.md) aby utworzyć źródło danych i indeksator.


> [!Note]
> Możesz oddać głos na user voice dla [interfejsu API tabeli,](https://feedback.azure.com/forums/263029-azure-search/suggestions/32759746-azure-search-should-be-able-to-index-cosmos-db-tab) jeśli chcesz, aby był obsługiwany w usłudze Azure Cognitive Search.
>

<a name="cosmos-indexer-portal"></a>

## <a name="use-the-portal"></a>Używanie portalu

> [!Note]
> Portal obsługuje obecnie interfejs API SQL i interfejs API MongoDB (wersja zapoznawcza).

Najprostszą metodą indeksowania elementów usługi Azure Cosmos DB jest użycie kreatora w [witrynie Azure portal.](https://portal.azure.com/) Pobierając próbki danych i odczytywania metadanych w kontenerze, Kreator [**importu danych**](search-import-data-portal.md) w usłudze Azure Cognitive Search może utworzyć domyślny indeks, mapować pola źródłowe do docelowych pól indeksu i załadować indeks w jednej operacji. W zależności od rozmiaru i złożoności danych źródłowych może mieć operacyjny indeks wyszukiwania pełnotekstowego w ciągu kilku minut.

Firma Microsoft zaleca używanie tego samego regionu lub lokalizacji dla usługi Azure Cognitive Search i usługi Azure Cosmos DB w celu zmniejszenia opóźnień i uniknięcia opłat za przepustowość.

### <a name="1---prepare-source-data"></a>1 - Przygotowanie danych źródłowych

Powinno mieć konto usługi Cosmos DB, bazę danych usługi Azure Cosmos DB mapowane do interfejsu API SQL, interfejsu API mongodb (wersja zapoznawcza) lub gremlin interfejsu API (wersja zapoznawcza) i zawartości w bazie danych.

Upewnij się, że baza danych usługi Cosmos DB zawiera dane. [Kreator importu danych](search-import-data-portal.md) odczytuje metadane i wykonuje próbkowanie danych, aby wywnioskować schemat indeksu, ale ładuje również dane z usługi Cosmos DB. Jeśli brakuje danych, kreator zatrzymuje się z tym błędem "Błąd wykrywania schematu indeksu ze źródła danych: Nie można utworzyć indeks prototypu, ponieważ datasource 'emptycollection' zwrócił żadnych danych".

### <a name="2---start-import-data-wizard"></a>2 - Uruchom Kreatora danych importu

Kreator można [uruchomić](search-import-data-portal.md) na pasku poleceń na stronie usługi Azure Cognitive Search lub w przypadku nawiązania połączenia z interfejsem API SQL usługi Cosmos DB, można kliknąć przycisk **Dodaj usługę Azure Cognitive Search** w sekcji **Ustawienia** lewego okienka nawigacji konta usługi Cosmos DB.

   ![Polecenie Importuj dane w portalu](./media/search-import-data-portal/import-data-cmd2.png "Uruchamianie kreatora importu danych")

### <a name="3---set-the-data-source"></a>3 - Ustawianie źródła danych

Na stronie **źródła danych** źródłem musi być **usługa Cosmos DB**, z następującymi specyfikacjami:

+ **Nazwa** jest nazwą obiektu źródła danych. Po utworzeniu można go wybrać dla innych obciążeń.

+ **Konto usługi Cosmos DB** powinno być podstawowym lub pomocniczym `AccountEndpoint` ciągiem `AccountKey`połączenia z usługi Cosmos DB, z programem . W przypadku kolekcji MongoDB dodaj **ApiKind=MongoDb** na końcu ciągu połączenia i oddziel go od ciągu połączenia średnikiem. W przypadku interfejsu API Gremlin i interfejsu API Cassandra należy korzystać z instrukcji dotyczących [interfejsu API REST](#cosmosdb-indexer-rest).

+ **Baza danych** jest istniejącą bazą danych z konta. 

+ **Kolekcja** jest kontenerem dokumentów. Dokumenty muszą istnieć, aby importowanie zakończyło się pomyślnie. 

+ **Kwerenda** może być pusta, jeśli chcesz wszystkie dokumenty, w przeciwnym razie można wprowadzić kwerendę, która wybiera podzbiór dokumentu. **Kwerenda** jest dostępna tylko dla interfejsu API SQL.

   ![Definicja źródła danych usługi Cosmos DB](media/search-howto-index-cosmosdb/cosmosdb-datasource.png "Definicja źródła danych usługi Cosmos DB")

### <a name="4---skip-the-enrich-content-page-in-the-wizard"></a>4 - Pomiń stronę "Wzbogać zawartość" w kreatorze

Dodawanie umiejętności poznawczych (lub wzbogacenia) nie jest wymogiem importu. Jeśli nie masz określonej potrzeby, aby [dodać wzbogacenie AI](cognitive-search-concept-intro.md) do potoku indeksowania, należy pominąć ten krok.

Aby pominąć ten krok, kliknij niebieskie przyciski u dołu strony dla "Dalej" i "Pomiń".

### <a name="5---set-index-attributes"></a>5 - Ustawianie atrybutów indeksu

Na stronie **Indeks** powinna zostać wyświetlona lista pól z typem danych i seria pól wyboru do ustawiania atrybutów indeksu. Kreator może wygenerować listę pól na podstawie metadanych i próbkowania danych źródłowych. 

Atrybuty zaznaczać zbiorczo, klikając pole wyboru u góry kolumny atrybutów. Wybierz **opcję Możliwe do pobrania** i **przeszukiwania** dla każdego pola, które powinno zostać zwrócone do aplikacji klienckiej i podlegać przetwarzaniu wyszukiwania pełnotekstowego. Można zauważyć, że liczby całkowite nie są pełny tekst lub rozmyte przeszukiwania (numery są oceniane dosłownie i są często przydatne w filtrach).

Przejrzyj opis [atrybutów indeksu](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) i [analizatorów języka,](https://docs.microsoft.com/rest/api/searchservice/language-support) aby uzyskać więcej informacji. 

Poświęć chwilę, aby przejrzeć swoje wybory. Po uruchomieniu kreatora tworzone są struktury danych fizycznych i nie będzie można edytować tych pól bez upuszczania i ponownego tworzenia wszystkich obiektów.

   ![Definicja indeksu bazy danych Usługi Cosmos](media/search-howto-index-cosmosdb/cosmosdb-index-schema.png "Definicja indeksu bazy danych Usługi Cosmos")

### <a name="6---create-indexer"></a>6 - Tworzenie indeksatora

W pełni określony kreator tworzy trzy odrębne obiekty w usłudze wyszukiwania. Obiekt źródła danych i obiekt indeksu są zapisywane jako nazwane zasoby w usłudze Azure Cognitive Search. Ostatni krok tworzy obiekt indeksatora. Nazywanie indeksatora umożliwia jego istnienie jako autonomicznego zasobu, który można zaplanować i zarządzać niezależnie od obiektu źródła indeksu i danych, utworzonego w tej samej sekwencji kreatora.

Jeśli nie jesteś zaznajomiony z indeksatorów, *indeksator* jest zasobem w usłudze Azure Cognitive Search, który indeksuje zewnętrzne źródło danych dla zawartości z wyszukuj. Dane wyjściowe Kreatora **danych importu** jest indeksatorem, który indeksuje źródło danych usługi Cosmos DB, wyodrębnia zawartość zbywalną i importuje ją do indeksu w usłudze Azure Cognitive Search.

Poniższy zrzut ekranu przedstawia domyślną konfigurację indeksatora. Można przełączyć się na **Once,** jeśli chcesz uruchomić indeksator jeden raz. Kliknij **przycisk Prześlij,** aby uruchomić kreatora i utworzyć wszystkie obiekty. Indeksowanie rozpoczyna się natychmiast.

   ![Definicja indeksatora bazy danych Usługi Cosmos](media/search-howto-index-cosmosdb/cosmosdb-indexer.png "Definicja indeksatora bazy danych Usługi Cosmos")

Importowanie danych można monitorować na stronach portalu. Powiadomienia o postępie wskazują stan indeksowania i liczbę dokumentów. 

Po zakończeniu indeksowania można użyć [Eksploratora wyszukiwania,](search-explorer.md) aby zbadać indeks.

> [!NOTE]
> Jeśli nie widzisz oczekiwanych danych, może być konieczne ustawienie większej liczby atrybutów w większej liczbie pól. Usuń indeks i indeksator, który właśnie utworzono, i ponownie przestępuj kreatora, modyfikując wybrane atrybuty indeksu w kroku 5. 

<a name="cosmosdb-indexer-rest"></a>

## <a name="use-rest-apis"></a>Używanie interfejsów API REST

Za pomocą interfejsu API REST można indeksować dane usługi Azure Cosmos DB, zgodnie z trzyczęściowym przepływem pracy wspólnym dla wszystkich indeksatorów w usłudze Azure Cognitive Search: utworzyć źródło danych, utworzyć indeks, utworzyć indeksator. Wyodrębnianie danych z usługi Cosmos DB występuje podczas przesyłania żądania Utwórz indeksatora. Po zakończeniu tego żądania, będziesz miał indeks queryable. 

> [!NOTE]
> Aby uzyskać indeksowanie danych z interfejsu API Programu Cosmos DB Gremlin lub interfejsu API Cosmos DB Cassandra, należy najpierw zażądać dostępu do wersji zapoznawczej, wypełniając [ten formularz.](https://aka.ms/azure-cognitive-search/indexer-preview) Po przetworzeniu żądania otrzymasz instrukcje dotyczące używania [interfejsu API REST w wersji 2019-05-06-Preview](search-api-preview.md) w celu utworzenia źródła danych.

Wcześniej w tym artykule wspomniano, że [indeksowanie usługi Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/index-overview) i [indeksowanie usługi Azure Cognitive Search](search-what-is-an-index.md) są różne operacje. W przypadku indeksowania usługi Cosmos DB domyślnie wszystkie dokumenty są automatycznie indeksowane z wyjątkiem interfejsu API Cassandra. Jeśli wyłączysz automatyczne indeksowanie, dostęp do dokumentów będzie można uzyskać tylko za pośrednictwem ich łączy własnych lub zapytań przy użyciu identyfikatora dokumentu. Indeksowanie usługi Azure Cognitive Search wymaga automatycznego indeksowania usługi Cosmos DB, które mają być włączone w kolekcji, która będzie indeksowana przez usługę Azure Cognitive Search. Podczas rejestrowania się w podglądzie indeksatora interfejsu API usługi Cosmos DB Cassandra otrzymasz instrukcje dotyczące konfigurowania indeksowania usługi Cosmos DB.

> [!WARNING]
> Usługa Azure Cosmos DB to następna generacja bazy danych DocumentDB. Poprzednio w wersji interfejsu API **2017-11-11** można było użyć `documentdb` składni. Oznaczało to, że można określić `cosmosdb` `documentdb`typ źródła danych jako lub . Począwszy od wersji interfejsu API **2019-05-06** zarówno interfejsów `cosmosdb` API usługi Azure Cognitive Search i portal tylko obsługiwać składnię zgodnie z instrukcjami w tym artykule. Oznacza to, że typ `cosmosdb` źródła danych musi, jeśli chcesz połączyć się z punktem końcowym usługi Cosmos DB.

### <a name="1---assemble-inputs-for-the-request"></a>1 - Montaż wejść dla żądania

Dla każdego żądania należy podać nazwę usługi i klucz administratora usługi Azure Cognitive Search (w nagłówku POST) oraz nazwę konta magazynu i klucz magazynu dla magazynu obiektów blob. [Listonosza](search-get-started-postman.md) służy do wysyłania żądań HTTP do usługi Azure Cognitive Search.

Skopiuj następujące cztery wartości do Notatnika, aby można je wkleić do żądania:

+ Nazwa usługi Azure Cognitive Search
+ Klucz administratora usługi Azure Cognitive Search
+ Parametry połączenia usługi Cosmos DB

Wartości te można znaleźć w portalu:

1. Na stronach portalu usługi Azure Cognitive Search skopiuj adres URL usługi wyszukiwania ze strony Przegląd.

2. W lewym okienku nawigacji kliknij pozycję **Klawisze,** a następnie skopiuj klucz podstawowy lub pomocniczy (są one równoważne).

3. Przełącz się na strony portalu dla konta magazynu usługi Cosmos. W lewym okienku nawigacji w obszarze **Ustawienia**kliknij pozycję **Klawisze**. Ta strona zawiera identyfikator URI, dwa zestawy ciągów połączeń i dwa zestawy kluczy. Skopiuj jeden z ciągów połączeń do Notatnika.

### <a name="2---create-a-data-source"></a>2 - Tworzenie źródła danych

**Źródło danych** określa dane do indeksowania, poświadczenia i zasady do identyfikowania zmian w danych (takich jak zmodyfikowane lub usunięte dokumenty wewnątrz kolekcji). Źródło danych jest zdefiniowany jako niezależny zasób, dzięki czemu może być używany przez wiele indeksatorów.

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
| **Nazwa** | Wymagany. Wybierz dowolną nazwę do reprezentowania obiektu źródła danych. |
|**Typu**| Wymagany. Musi `cosmosdb`być . |
|**Poświadczenia** | Wymagany. Musi być ciągiem połączenia usługi Cosmos DB.<br/>W przypadku kolekcji SQL parametry połączenia są w tym formacie:`AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>`<br/><br/>W przypadku kolekcji MongoDB dodaj **ApiKind=MongoDb** do ciągu połączenia:<br/>`AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=MongoDb`<br/><br/>W przypadku wykresów Gremlin i tabel Cassandra zarejestruj się w [podglądzie indeksatora z bramkami,](https://aka.ms/azure-cognitive-search/indexer-preview) aby uzyskać dostęp do wersji zapoznawczej i informacji o sposobie formatowania poświadczeń.<br/><br/>Unikaj numerów portów w adresie URL punktu końcowego. Jeśli dołączysz numer portu, usługa Azure Cognitive Search nie będzie mogła indeksować bazy danych usługi Azure Cosmos DB.|
| **Kontenera** | Zawiera następujące elementy: <br/>**nazwa**: Wymagane. Określ identyfikator kolekcji bazy danych, która ma być indeksowana.<br/>**zapytanie**: Opcjonalnie. Można określić kwerendę, aby spłaszczyć dowolny dokument JSON do schematu płaskiego, który może indeksować usługi Azure Cognitive Search.<br/>W przypadku interfejsu API mongodb, interfejsu API Gremlin i interfejsu API Cassandra kwerendy nie są obsługiwane. |
| **dataChangeDetectionPolicy** | Zalecane. Zobacz [sekcję Indeksowanie zmienionych dokumentów.](#DataChangeDetectionPolicy)|
|**dataDeletionDetectionPolicy** | Element opcjonalny. Zobacz [sekcję Indeksowanie usuniętych dokumentów.](#DataDeletionDetectionPolicy)|

### <a name="using-queries-to-shape-indexed-data"></a>Używanie zapytań do kształtowania indeksowanych danych
Można określić kwerendę SQL, aby spłaszczyć zagnieżdżone właściwości lub tablice, właściwości projektu JSON i filtrować dane, które mają być indeksowane. 

> [!WARNING]
> Zapytania niestandardowe nie są obsługiwane dla **interfejsu API mongodb**, interfejsu `container.query` API **Gremlin**i interfejsu API **Cassandra**: parametr musi być ustawiony na wartość null lub pominięty. Jeśli chcesz użyć zapytania niestandardowego, poinformuj nas o tym w [uchodź głos użytkownika.](https://feedback.azure.com/forums/263029-azure-search)

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

Kwerenda filtrująca:

    SELECT * FROM c WHERE c.company = "microsoft" and c._ts >= @HighWaterMark ORDER BY c._ts

Spłaszczanie kwerendy:

    SELECT c.id, c.userId, c.contact.firstName, c.contact.lastName, c.company, c._ts FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts
    
    
Kwerenda projekcyjna:

    SELECT VALUE { "id":c.id, "Name":c.contact.firstName, "Company":c.company, "_ts":c._ts } FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts


Kwerenda spłaszczająca tablicę:

    SELECT c.id, c.userId, tag, c._ts FROM c JOIN tag IN c.tags WHERE c._ts >= @HighWaterMark ORDER BY c._ts


### <a name="3---create-a-target-search-index"></a>3 - Tworzenie docelowego indeksu wyszukiwania 

[Utwórz docelowy indeks usługi Azure Cognitive Search,](/rest/api/searchservice/create-index) jeśli jeszcze go nie masz. Poniższy przykład tworzy indeks z polem identyfikatora i opisu:

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

Upewnij się, że schemat indeksu docelowego jest zgodny ze schematem źródłowych dokumentów JSON lub dane wyjściowe niestandardowego rzutowania zapytania.

> [!NOTE]
> W przypadku kolekcji podzielonych na partycje domyślnym `_rid` kluczem dokumentu jest właściwość usługi `rid` Azure Cosmos DB, na którą usługa Azure Cognitive Search automatycznie zmienia nazwę, ponieważ nazwy pól nie mogą zaczynać się od znaku podkreślenia. Ponadto wartości usługi Azure `_rid` Cosmos DB zawierają znaki, które są nieprawidłowe w kluczach usługi Azure Cognitive Search. Z tego powodu `_rid` wartości są zakodowane base64.
> 
> W przypadku kolekcji MongoDB usługa Azure `_id` Cognitive `id`Search automatycznie zmienia nazwę właściwości na .  

### <a name="mapping-between-json-data-types-and-azure-cognitive-search-data-types"></a>Mapowanie między typami danych JSON a typami danych usługi Azure Cognitive Search
| Typ danych JSON | Zgodne typy pól indeksu docelowego |
| --- | --- |
| Wartość logiczna |Edm.Boolean, Edm.String |
| Liczby, które wyglądają jak liczby całkowite |Edm.Int32, Edm.Int64, Edm.String |
| Liczby, które wyglądają jak zmiennoprzecinki |Edm.Double, Edm.String |
| Ciąg |Edm.String |
| Tablice typów pierwotnych, na przykład ["a", "b", "c"] |Collection(Edm.String) |
| Ciągi, które wyglądają jak daty |Edm.DateTimeOffset, Edm.String |
| Obiekty GeoJSON, na przykład { "type": "Point", "coordinates": [long, lat] } |Edm.GeographyPoint |
| Inne obiekty JSON |Nie dotyczy |

### <a name="4---configure-and-run-the-indexer"></a>4 - Konfigurowanie i uruchamianie indeksatora

Po utworzeniu indeksu i źródła danych można przystąpić do tworzenia indeksatora:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "mycosmosdbindexer",
      "dataSourceName" : "mycosmosdbdatasource",
      "targetIndexName" : "mysearchindex",
      "schedule" : { "interval" : "PT2H" }
    }

Ten indeksator jest uruchamiany co dwie godziny (interwał harmonogramu jest ustawiony na "PT2H"). Aby uruchomić indeksator co 30 minut, należy ustawić interwał na "PT30M". Najkrótszy obsługiwany interwał wynosi 5 minut. Harmonogram jest opcjonalny — jeśli pominięto, indeksator uruchamia się tylko raz podczas jego tworzenia. Jednak można uruchomić indeksatora na żądanie w dowolnym momencie.   

Aby uzyskać więcej informacji na temat interfejsu API tworzenia indeksatora, zapoznaj się z [programem Utwórz indeksator](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

Aby uzyskać więcej informacji na temat definiowania harmonogramów indeksatora, zobacz [Jak zaplanować indeksatory dla usługi Azure Cognitive Search](search-howto-schedule-indexers.md).

## <a name="use-net"></a>Korzystanie z platformy .NET

Ogólnie dostępny plik .NET SDK ma pełną parzystość z ogólnie dostępnym interfejsem API REST. Zaleca się przejrzenie poprzedniej sekcji interfejsu API REST, aby poznać pojęcia, przepływ pracy i wymagania. Następnie można odwołać się do następującej dokumentacji odwołania do interfejsu API platformy .NET, aby zaimplementować indeksator JSON w kodzie zarządzanym.

+ [źródło danych microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

<a name="DataChangeDetectionPolicy"></a>

## <a name="indexing-changed-documents"></a>Indeksowanie zmienionych dokumentów

Celem zasad wykrywania zmian danych jest efektywna identyfikacja zmienionych elementów danych. Obecnie jedyną obsługiwaną zasadą [`HighWaterMarkChangeDetectionPolicy`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.highwatermarkchangedetectionpolicy) jest `_ts` przy użyciu właściwości (sygnatura czasowa) dostarczonej przez usługę Azure Cosmos DB, która jest określona w następujący sposób:

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

Za pomocą tej zasady jest wysoce zalecane, aby zapewnić dobrą wydajność indeksatora. 

Jeśli używasz kwerendy niestandardowej, `_ts` upewnij się, że właściwość jest rzutowana przez kwerendę.

<a name="IncrementalProgress"></a>

### <a name="incremental-progress-and-custom-queries"></a>Postęp przyrostowy i kwerendy niestandardowe

Przyrostowy postęp podczas indeksowania zapewnia, że jeśli wykonanie indeksatora zostanie przerwane przez przejściowe błędy lub limit czasu wykonywania, indeksator może podnieść, gdzie zostało przerwane następnym uruchomieniu, zamiast ponownego indeksowania całej kolekcji od podstaw. Jest to szczególnie ważne podczas indeksowania dużych kolekcji. 

Aby włączyć postęp przyrostowy podczas korzystania z kwerendy niestandardowej, `_ts` upewnij się, że kwerenda porządkuuje wyniki według kolumny. Umożliwia to okresowe sprawdzanie, które używa usługi Azure Cognitive Search w celu zapewnienia przyrostowego postępu w obecności błędów.   

W niektórych przypadkach, nawet `ORDER BY [collection alias]._ts` jeśli zapytanie zawiera klauzulę, usługa Azure Cognitive `_ts`Search może nie wywnioskować, że kwerenda jest uporządkowana przez plik . Można powiedzieć usługi Azure Cognitive Search, że `assumeOrderByHighWaterMarkColumn` wyniki są uporządkowane przy użyciu właściwości konfiguracji. Aby określić tę wskazówkę, utwórz lub zaktualizuj indeksator w następujący sposób: 

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "assumeOrderByHighWaterMarkColumn" : true } }
    } 

<a name="DataDeletionDetectionPolicy"></a>

## <a name="indexing-deleted-documents"></a>Indeksowanie usuniętych dokumentów

Gdy wiersze są usuwane z kolekcji, zwykle chcesz usunąć te wiersze z indeksu wyszukiwania, jak również. Celem zasad wykrywania usuwania danych jest efektywna identyfikacja usuniętych elementów danych. Obecnie jedyną obsługiwaną zasadą `Soft Delete` jest zasada (usunięcie jest oznaczone flagą pewnego rodzaju), która jest określona w następujący sposób:

    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }

Jeśli używasz kwerendy niestandardowej, upewnij się, `softDeleteColumnName` że właściwość, do której odwołuje się jest przewidywana przez kwerendę.

Poniższy przykład tworzy źródło danych z zasadami usuwania nietremowego:

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

## <a name="next-steps"></a><a name="NextSteps"></a>Następne kroki

Gratulacje! Dowiesz się, jak zintegrować usługę Azure Cosmos DB z usługą Azure Cognitive Search przy użyciu indeksatora.

* Aby dowiedzieć się więcej o usłudze Azure Cosmos DB, zobacz [stronę usługi Usługi Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).
* Aby dowiedzieć się więcej o usłudze Azure Cognitive Search, zobacz [stronę usługi wyszukiwania](https://azure.microsoft.com/services/search/).
