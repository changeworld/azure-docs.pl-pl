---
title: Azure DB rozwiązania Cosmos indeksowania zasad | Dokumentacja firmy Microsoft
description: Dowiedz się, jak indeksowania działa w usłudze Azure DB rozwiązania Cosmos. Dowiedz się, jak skonfigurować i zmienić zasady indeksowania dla automatycznego indeksowania i zwiększyć wydajność.
keywords: Indeksowanie działania, automatycznego indeksowania, indeksowania bazy danych
services: cosmos-db
author: rafats
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: rafats
ms.openlocfilehash: d867079b9a5546dc9555697a9066472e4e470977
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35298301"
---
# <a name="how-does-azure-cosmos-db-index-data"></a>Jak dane indeksu bazy danych rozwiązania Cosmos Azure?

Domyślnie wszystkie dane z bazy danych Azure rozwiązania Cosmos jest indeksowany. Choć wielu klientów chętnie let DB rozwiązania Cosmos Azure automatycznie obsługiwać wszystkie aspekty indeksowania, można określić niestandardowego *indeksowania zasad* kolekcji podczas tworzenia w usłudze Azure DB rozwiązania Cosmos. Zasady indeksowania w usłudze Azure DB rozwiązania Cosmos są bardziej elastyczne i wydajne niż indeksów pomocniczych, które są oferowane na innych platformach, bazy danych. W usłudze Azure DB rozwiązania Cosmos można projektować i dostosować kształt indeksu bez ograniczania elastyczność schematu. 

Aby dowiedzieć się, jak indeksowania działa w usłudze Azure DB rozwiązania Cosmos, jest wziąć pod uwagę podczas zarządzania zasady indeksowania, można oznaczyć szczegółowych kompromis między narzut na przechowywanie indeksu, zapisu i przepływności zapytania i spójności zapytania.  

Poniższe wideo Andrew Liu Menedżera programów DB rozwiązania Cosmos Azure pokazuje DB rozwiązania Cosmos Azure automatycznego indeksowania możliwości i sposobu dostrojenie i skonfigurować zasady indeksowania w kontenerze Twojej bazy danych Azure rozwiązania Cosmos. 

>[!VIDEO https://www.youtube.com/embed/uFu2D-GscG0]

W tym artykule firma Microsoft Przyjrzyjmy się zamknięcia bazy danych Azure rozwiązania Cosmos indeksowania zasad, jak dostosować zasady indeksowania i skojarzone kompromisy. 

Po przeczytaniu tego artykułu, będziesz mieć możliwość odpowiedzieć na następujące pytania:

* Jak można zastąpić właściwości, aby uwzględnić lub wykluczyć indeksowania?
* Jak można skonfigurować indeksu ostatecznego aktualizacji?
* Jak skonfigurować indeksowania do wykonywania zapytań ORDER BY i zakres?
* Jak dokonać zmiany do zbioru zasady indeksowania
* Jak porównać magazynu i wydajności różnych zasad indeksowania

## Dostosuj zasady indeksowania w kolekcji <a id="CustomizingIndexingPolicy"></a>  
Zastępowanie domyślnego indeksowania zasad w kolekcji usługi Azure DB rozwiązania Cosmos można dostosować kompromis między magazynu, zapisu i wydajność zapytań i spójności zapytania. Można skonfigurować następujące aspekty:

* **Dołącz lub Wyklucz dokumentów i ścieżki z indeksem i**. Można wyłączyć lub włączyć określonych dokumentów w indeksie, gdy Wstawianie lub zastępowanie dokumentów w kolekcji. Można również uwzględnić lub wykluczyć określone właściwości JSON, nazywany również *ścieżki*, zostać pomyślnie zindeksowane wszystkich dokumentów, które znajdują się w indeksie. Ścieżki obejmują wzorców symboli wieloznacznych.
* **Skonfigurować różne typy indeksu**. Dla każdej ścieżki dołączone można określić typ indeksu, który wymaga ścieżki dla kolekcji. Można określić typ indeksu na podstawie ścieżki danych, obciążenie oczekiwanego zapytania i numeryczne/ciąg "dokładności."
* **Konfigurowanie trybów aktualizacji indeksu**. Azure DB rozwiązania Cosmos obsługuje trzy tryby indeksowania: spójny, opóźnieniem i brak. Można skonfigurować indeksowania tryby za pomocą zasady indeksowania na kolekcji usługi Azure DB rozwiązania Cosmos. 

Poniższy fragment kodu platformy Microsoft .NET przedstawiono sposób ustawiania niestandardowe zasady indeksowania, podczas tworzenia kolekcji. W tym przykładzie mamy ustawioną zasad z indeksem zakresu dla ciągów i numery precyzja maksymalna. Ta zasada służy do wykonywania zapytań ORDER BY ciągów.

    DocumentCollection collection = new DocumentCollection { Id = "myCollection" };

    collection.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });
    collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), collection);   


> [!NOTE]
> Schematu JSON dla zasady indeksowania zmienione w wersji interfejsu API REST wersji 2015-06-03. W tej wersji schematu JSON dla zasady indeksowania obsługuje indeksów zakresu względem ciągów. Zestaw .NET SDK 1.2.0 i Java, Python i Node.js SDK 1.1.0 obsługuje nowego schematu zasad. Wcześniejszych wersji zestawu SDK za pomocą interfejsu API REST wersji 2015-04-08. Obsługują one wcześniejszych schematu dla zasady indeksowania.
> 
> Domyślnie bazy danych Azure rozwiązania Cosmos indeksuje wszystkie właściwości ciągu w dokumentach spójne z indeksem wyznaczania wartości skrótu. Wszystkie właściwości liczbowe w dokumentach go indeksuje spójne z indeksem typu zakres.  
> 
> 

### <a name="customize-the-indexing-policy-in-the-portal"></a>Dostosuj zasady indeksowania w portalu

Można zmienić zasady indeksowania w kolekcji w portalu Azure: 

1. W portalu przejdź do swojego konta bazy danych rozwiązania Cosmos platformy Azure, a następnie wybierz kolekcji. 
2. W menu nawigacji po lewej stronie wybierz **ustawienia**, a następnie wybierz **zasady indeksowania**. 
3. W obszarze **zasady indeksowania**, zmienić zasady indeksowania, a następnie wybierz **OK**. 

### Tryby indeksowania bazy danych <a id="indexing-modes"></a>  
Azure DB rozwiązania Cosmos obsługuje trzy tryby indeksowania, które można skonfigurować za pomocą zasady indeksowania w kolekcji usługi Azure DB rozwiązania Cosmos: spójny, opóźnieniem i brak.

**Spójne**: zasady zbierania bazy danych Azure rozwiązania Cosmos jest spójne, zapytania w określonej kolekcji bazy danych Azure rozwiązania Cosmos wykonaj poziomu spójności określone dla odczytów punktu (silne, nieaktualność, sesji lub ostatecznego). Indeks jest aktualizowana synchronicznie, w ramach aktualizacji dokumentu (insert, replace, update i delete dokumentów w kolekcji usługi Azure DB rozwiązania Cosmos).

Spójne indeksowania obsługuje zapytania spójne kosztem możliwe zmniejszenie przepływność zapisu. Obniżenie to funkcja unikatowy ścieżek, które muszą zostać pomyślnie zindeksowane i "poziom zgodności". Spójne tryb indeksowania jest przeznaczona dla obciążeń "write szybkiego zapytań od razu".

**Opóźnieniem**: indeks jest aktualizowany asynchronicznie, gdy kolekcji usługi Azure DB rozwiązania Cosmos jest spoczynku, oznacza to, gdy przepływności kolekcji nie jest w pełni wykorzystane do obsługi żądań użytkownika.  Należy pamiętać, że można uzyskać niespójne wyników, ponieważ dane są pozyskanych i indeksowane powoli. Oznacza to, że Twoje liczby zapytań lub wyników określonego zapytania mogą nie być zgodne lub repeatable na podany czas. 

Indeks jest zazwyczaj w trybie wyrównującej pozyskiwane danych. Z opóźnieniem indeksowania czas wygaśnięcia (TTL) zmienia wynik w indeksie jest porzucona i utworzona ponownie. Dzięki temu wyniki zapytania i liczba niespójne w danym okresie czasu. Większość kont Azure DB rozwiązania Cosmos używać spójne trybu indeksowania.

**Brak**: kolekcja, która ma wartość None tryb indeksu nie ma indeksu skojarzonych z nim. Jest ona powszechnie stosowana w przypadku bazy danych Azure rozwiązania Cosmos jest używana jako magazyn kluczy i wartości i dokumenty są dostępne tylko dla ich właściwość Identyfikatora. 

> [!NOTE]
> Skonfigurowanie zasady indeksowania z None ma efektem ubocznym usunięcie wszelkich istniejący indeks. Użyj, jeśli Twoich wzorców dostępu wymagają tylko Identyfikatora lub link do samego siebie.
> 
> 

W poniższej tabeli przedstawiono spójności dla zapytań na podstawie tryb indeksowania (spójność i Lazy) skonfigurowane dla kolekcji i poziomu spójności określonego dla żądania zapytania. Dotyczy to zapytań przy użyciu dowolnego interfejsu: REST API, zestawy SDK, lub z poziomu procedury składowane i wyzwalaczy. 

|Spójność|Tryb indeksowania: zgodne|Tryb indeksowania: powolne|
|---|---|---|
|Silna|Silna|Ostateczna|
|Spójność powiązanej nieaktualności|Spójność powiązanej nieaktualności|Ostateczna|
|Sesja|Sesja|Ostateczna|
|Ostateczna|Ostateczna|Ostateczna|

Azure DB rozwiązania Cosmos zwraca błąd dla zapytań w kolekcji, których nie masz żadnej indeksowania tryb. Nadal można wykonać zapytania jako skanowania za pomocą jawnych **x-ms-documentdb Włącz — skanowania** nagłówka w interfejsie API REST lub **EnableScanInQuery** żądanie opcja przy użyciu zestawu .NET SDK. Niektóre funkcje zapytania, takie jak ORDER BY nie są obsługiwane jako skanowania z **EnableScanInQuery**.

W poniższej tabeli przedstawiono spójności dla zapytań na podstawie trybu indeksowania (spójność opóźnieniem i None) podczas **EnableScanInQuery** jest określona.

|Spójność|Tryb indeksowania: zgodne|Tryb indeksowania: powolne|Tryb indeksowania: Brak|
|---|---|---|---|
|Silna|Silna|Ostateczna|Silna|
|Spójność powiązanej nieaktualności|Spójność powiązanej nieaktualności|Ostateczna|Spójność powiązanej nieaktualności|
|Sesja|Sesja|Ostateczna|Sesja|
|Ostateczna|Ostateczna|Ostateczna|Ostateczna|

Następujące Pokaż przykładowy kod tworzenia kolekcji usługi Azure DB rozwiązania Cosmos przy użyciu zestawu .NET SDK indeksowania spójna na wszystkich wstawienia dokumentu.

     // Default collection creates a Hash index for all string fields and a Range index for all numeric    
     // fields. Hash indexes are compact and offer efficient performance for equality queries.

     var collection = new DocumentCollection { Id ="defaultCollection" };

     collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;

     collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("mydb"), collection);


### <a name="index-paths"></a>Indeks ścieżki
Azure DB rozwiązania Cosmos modele dokumentów JSON i indeksu jako drzewa. Można dostosować do zasad dla ścieżki w drzewie. W dokumentach można wybrać ścieżki do dołączania lub wykluczania indeksowania. To oferują zapisu lepszą wydajność i dolnym indeksu magazynu w scenariuszach, w których wiadomo wcześniej wzorcami zapytań.

Indeks ścieżek rozpoczynać się od katalogu głównego (/) i zwykle kończyć? operator symboli wieloznacznych. Oznacza to, istnieje wiele możliwych wartości dla prefiksu. Na przykład, aby obsługiwać SELECT * z F.familyName WHERE rodzin F = "Andersen" musi zawierać ścieżkę indeksu /familyName/? w zasadach indeks kolekcji.

Umożliwia także ścieżek w indeksie \* symbolu wieloznacznego operatora, aby określić zachowanie dla ścieżki rekursywnie w obszarze prefiks. Na przykład/ładunku / * można wykluczyć wszystkie elementy w obszarze właściwości ładunku z indeksowania.

Poniżej przedstawiono typowe wzorce służący do określania ścieżki indeksu:

| Ścieżka                | Przypadek użycia/opis                                                                                                                                                                                                                                                                                         |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| /                   | Domyślna ścieżka dla kolekcji. Cykliczne i ma zastosowanie do całego dokumentu drzewa.                                                                                                                                                                                                                                   |
| / prop /?             | Wymagane do obsługi zapytań, podobnie do następującej ścieżki indeksu (o typach skrótu lub zakres, odpowiednio):<br><br>Wybierz z kolekcji c WHERE c.prop = "value"<br><br>Wybierz z kolekcji c WHERE c.prop > 5<br><br>Wybierz z kolekcji c ORDER BY c.prop                                                                       |
| / prop / *             | Ścieżka indeksu dla wszystkich ścieżek w ramach określonej etykiety. Działa z następujące kwerendy<br><br>Wybierz z kolekcji c WHERE c.prop = "value"<br><br>Wybierz z kolekcji c WHERE c.prop.subprop > 5<br><br>Wybierz z kolekcji c WHERE c.prop.subprop.nextprop = "value"<br><br>Wybierz z kolekcji c ORDER BY c.prop         |
| [] / właściwości / /?         | Ścieżka indeksu wymagane do obsługi iteracji, a następnie dołącz zapytań dotyczących tablic wartości skalarne, takie jak ["a", "b", "c"]:<br><br>Wybierz znacznik z collection.props w tagu WHERE tag = "value"<br><br>Wybierz znacznik z kolekcji c sprzężenia tagu w c.props gdzie tagu > 5                                                                         |
| /props/ /subprop/ []? | Ścieżka indeksu wymaganych do obsłużenia iteracji i sprzężenia zapytania względem tablice obiektów, takich jak [{subprop: ""}, {subprop: "b"}]:<br><br>Wybierz znacznik z collection.props w tagu WHERE tag.subprop = "value"<br><br>Wybierz znacznik z kolekcji c sprzężenia tagu w c.props WHERE tag.subprop = "value"                                  |
| / prop/subprop /?     | Ścieżka indeksu wymaganych do obsłużenia zapytania (z typami skrótu lub zakres, odpowiednio):<br><br>Wybierz z kolekcji c WHERE c.prop.subprop = "value"<br><br>Wybierz z kolekcji c WHERE c.prop.subprop > 5                                                                                                                    |

> [!NOTE]
> Po ustawieniu ścieżek w indeksie niestandardowym jest wymagane do określenia indeksowania reguły domyślnej dla drzewa całego dokumentu, która jest oznaczona specjalne ścieżka "/ *". 
> 
> 

Poniższy przykład umożliwia skonfigurowanie określonej ścieżki z indeksem typu zakres i wartość niestandardowego dokładności 20 bajtów:

    var collection = new DocumentCollection { Id = "rangeSinglePathCollection" };    

    collection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/Title/?", 
            Indexes = new Collection<Index> { 
                new RangeIndex(DataType.String) { Precision = 20 } } 
            });

    // Default for everything else
    collection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/*" ,
            Indexes = new Collection<Index> {
                new HashIndex(DataType.String) { Precision = 3 }, 
                new RangeIndex(DataType.Number) { Precision = -1 } 
            }
        });

    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), pathRange);


### <a name="index-data-types-kinds-and-precisions"></a>Typy danych, typy i opisie
Masz wiele opcji, konfigurując zasady indeksowania dla ścieżki. Można określić co najmniej jedną definicję indeksowania dla każdej ścieżki:

* **Typ danych**: ciąg, liczbę, punktu, wielokąta lub LineString (może zawierać tylko jeden wpis dla typu danych na ścieżki).
* **Indeks typu**: wyznaczania wartości skrótu (zapytań o równość), zakresu (równości, zakres lub zapytań ORDER BY) lub przestrzennym (przestrzennych kwerendy).
* **Dokładność**: indeks dla skrótu to różni się od 1 do 8 dla liczb i ciągów. Wartość domyślna to 3. Zakres indeksu ta wartość może być -1 (maksymalna dozwolona dokładność). Może się różnić znajdujące 1 do 100 (maksymalna dozwolona dokładność) na ciąg lub wartości liczbowe.

#### <a name="index-kind"></a>Typ indeksu
Azure DB rozwiązania Cosmos obsługuje indeksu skrótu i rodzaje indeks zakresu dla każdej ścieżki skonfigurowanego dla ciąg lub liczba typów danych lub obu.

* **Skrót** obsługuje równości wydajne i sprzężenia zapytania. W większości przypadków użycia większą dokładność niż domyślna wartość 3 bajtów nie ma potrzeby indeksów skrótu. Typ danych może być ciąg lub liczba.
* **Zakres** obsługuje zapytań o równość wydajne, kwerendy zakresu (przy użyciu >, <>, =, < =,! =) oraz zapytań ORDER BY. Zapytań ORDER By domyślnie wymagają również precyzja maksymalna wartość indeksu (-1). Typ danych może być ciąg lub liczba.

Azure DB rozwiązania Cosmos obsługuje również rodzaj indeks przestrzenny dla każdej ścieżki, który może być określony dla typów danych punktu wielokąta i LineString. Wartość w określonej ścieżce musi być prawidłowy fragment GeoJSON, takich jak `{"type": "Point", "coordinates": [0.0, 10.0]}`.

* **Przestrzenne** obsługuje wydajne przestrzennych (w ramach i odległość) zapytania. Typ danych może być punkt, wielokąta lub LineString.

> [!NOTE]
> Azure DB rozwiązania Cosmos obsługuje automatycznego indeksowania punktu wielokąta i LineString typów danych.
> 
> 

Poniżej przedstawiono typy obsługiwanych indeksu i przykłady zapytań, które mogą być używane do obsługi:

| Typ indeksu | Przypadek użycia/opis                                                                                                                                                                                                                                                                                                                                                                                                              |
| ---------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Skrót       | Hash — za pośrednictwem/prop /? (lub /) umożliwia wydajne służą następujące zapytania:<br><br>Wybierz z kolekcji c WHERE c.prop = "value"<br><br>Skrót za pośrednictwem/właściwości / [] /? (i / lub/właściwości /) umożliwia wydajne służą następujące zapytania:<br><br>Wybierz znacznik z kolekcji c sprzężenia tagu w c.props WHERE tag = 5                                                                                                                       |
| Zakres      | Zakres za pośrednictwem/prop /? (lub /) umożliwia wydajne służą następujące zapytania:<br><br>Wybierz z kolekcji c WHERE c.prop = "value"<br><br>Wybierz z kolekcji c WHERE c.prop > 5<br><br>Wybierz z kolekcji c ORDER BY c.prop                                                                                                                                                                                                              |
| Przestrzenne     | Zakres za pośrednictwem/prop /? (lub /) umożliwia wydajne służą następujące zapytania:<br><br>Wybierz z c kolekcji<br><br>GDZIE ST_DISTANCE (c.prop, {"type": "Punkt", "coordinates": [0.0, 10.0]}) < 40<br><br>Wybierz z kolekcji c gdzie ST_WITHIN(c.prop, {"type": "Polygon",...}) — za pomocą indeksowania w punktach włączone<br><br>Wybierz z kolekcji c gdzie ST_WITHIN({"type": "Point",...}, c.prop) — za pomocą indeksowania na wielokątów włączone              |

Domyślnie, zwracany jest błąd dla zapytania z zakres operatorów, takich jak > = w przypadku nie zakresu indeks (żadnych precyzja) która sygnalizuje, że skanowania może być niezbędne do obsługi zapytań. Za pomocą można wykonywać bez indeksu zakres kwerendy zakresu **x-ms-documentdb Włącz — skanowania** nagłówka w interfejsie API REST lub **EnableScanInQuery** żądanie opcja przy użyciu zestawu .NET SDK. Jeśli istnieją inne filtry w zapytaniu bazy danych rozwiązania Cosmos Azure za pomocą indeks filtru względem, jest zwracany błąd braku.

Te same zasady mają zastosowanie dla zapytania przestrzennych. Domyślnie jeśli istnieje nie indeks przestrzenny, a nie filtrów, które mogą być przekazywane z indeksu, przestrzennych zapytań zostanie zwrócony błąd. Mogą być wykonywane podczas skanowania za pomocą **x-ms-documentdb Włącz — skanowania** lub **EnableScanInQuery**.

#### <a name="index-precision"></a>Dokładność indeksu
Dokładność indeksu umożliwia upewnij kompromis między narzut magazynu indeksu i wydajności zapytania. W przypadku numerów zalecamy użycie domyślnej konfiguracji precision-1 (maksymalna). Ponieważ liczby 8 bajtów w formacie JSON, co jest równoważne konfiguracji 8 bajtów. Wybieranie niższa wartość precyzji, takich jak 1 do 7, oznacza, że wartości w niektórych zakresy mapowane do tej samej pozycja indeksu. W związku z tym można zmniejszyć miejsce do magazynowania indeksu, ale wykonanie kwerendy może być konieczne przetwarzać więcej dokumentów. W rezultacie zużywa więcej przepustowości w jednostkach żądań.

Konfiguracja dokładności indeksu zawiera bardziej praktyczne aplikacji z zakresami ciągu. Ciągi znaków mogą być dowolnego dowolnej długości, wybór dokładność indeks może wpłynąć na wydajność kwerend zakresu ciągu. On również może mieć wpływ na ilość miejsca do magazynowania indeksu, która jest wymagana. Indeksy zakresu ciągu można skonfigurować za pomocą od 1 do 100 lub wartość -1 (maksymalna). Jeśli chcesz wykonać zapytań ORDER BY właściwości ciągów, należy określić precision-1 dla odpowiednich ścieżek.

Indeksy przestrzenne zawsze używać dokładność indeksu domyślnego dla wszystkich typów (punkt, LineString i wielokąta). Nie można zastąpić domyślne dokładność indeks przestrzenny. 

Poniższy przykład pokazuje, jak zwiększyć dokładność dla indeksów zakresu w kolekcji przy użyciu zestawu .NET SDK. 

**Utwórz kolekcję o indeksie niestandardowym dokładności**

    var rangeDefault = new DocumentCollection { Id = "rangeCollection" };

    // Override the default policy for strings to Range indexing and "max" (-1) precision
    rangeDefault.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), rangeDefault);   


> [!NOTE]
> Azure DB rozwiązania Cosmos zwraca błąd, gdy zapytanie używa ORDER BY, ale nie ma indeksu zakresu względem ścieżki, którego dotyczy kwerenda za precyzja maksymalna. 
> 
> 

Podobnie można całkowicie Wyklucz ścieżki indeksowania. W kolejnym przykładzie pokazano, jak wykluczyć całą sekcję dokumentów ( *poddrzewo*) indeksowania przy użyciu \* operator symboli wieloznacznych.

    var excluded = new DocumentCollection { Id = "excludedPathCollection" };
    excluded.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
    excluded.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*" });

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), excluded);



## <a name="opt-in-and-opt-out-of-indexing"></a>Zgódź się i zrezygnować z indeksowania
Można wybrać, czy ma kolekcję do automatycznego indeksowania wszystkie dokumenty. Domyślnie wszystkie dokumenty są automatycznie indeksowane, ale można wyłączyć automatycznego indeksowania. Po wyłączeniu indeksowania dokumentów jest możliwy tylko za pomocą ich linki do samego siebie lub przez zapytania za pomocą dokumentów identyfikator.

Z automatycznego indeksowania wyłączone, nadal selektywnie można dodać tylko dokumenty do indeksu. Z drugiej strony można pozostawić automatycznego indeksowania na i selektywnie wybrać wykluczanie określonych dokumentów. Indeksowanie lub wyłącza konfiguracje są przydatne, gdy masz tylko podzbiór dokumentów, które trzeba można wykonać zapytania.

Poniższy przykład przedstawia sposób jawnie zawierały dokumentu za pomocą [zestawu SDK .NET interfejsu API SQL](https://docs.microsoft.com/azure/cosmos-db/sql-api-sdk-dotnet) i [RequestOptions.IndexingDirective](http://msdn.microsoft.com/library/microsoft.azure.documents.client.requestoptions.indexingdirective.aspx) właściwości.

    // If you want to override the default collection behavior to either
    // exclude (or include) a document in indexing,
    // use the RequestOptions.IndexingDirective property.
    client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"),
        new { id = "AndersenFamily", isRegistered = true },
        new RequestOptions { IndexingDirective = IndexingDirective.Include });

## <a name="modify-the-indexing-policy-of-a-collection"></a>Zmodyfikuj zasady indeksowania w kolekcji
W usłudze Azure DB rozwiązania Cosmos można wprowadzić zmiany, aby zasady indeksowania w kolekcji na bieżąco. Zmiana indeksowania zasad w kolekcji usługi Azure DB rozwiązania Cosmos może prowadzić do zmiany w kształcie indeksu. Dotyczy ścieżek, które mogą być indeksowane, ich dokładność i modelu spójności indeksu samej siebie. Zmiana skutecznie indeksowania zasad wymaga transformacji starego indeksu na nowego indeksu. 

**Przekształcenia indeksu w trybie online**

![Indeksowanie działania — przekształcenia indeksu w trybie online bazy danych Azure rozwiązania Cosmos](./media/indexing-policies/index-transformations.png)

Indeks przekształceń są wykonywane online. Oznacza to, że indeksowane dla poszczególnych zasad stare dokumenty są wydajnie przekształcone na nowe zasady *bez wpływu na dostępność zapisu lub zainicjowaną przepływności* kolekcji. Spójność odczytu i zapisu przy użyciu interfejsu API REST, zestawy SDK, lub z poziomu procedury składowane i wyzwalaczy nie występuje podczas transformacji indeksu. Nie jest brak spadku wydajności ani przestoju do aplikacji po wprowadzeniu zmian zasad indeksowania.

Jednak w czasie, który przekształcenie indeksu jest postęp zapytania są ostatecznie spójne niezależnie od konfiguracji trybu indeksowania (spójność lub Lazy). Dotyczy to również zapytania ze wszystkich interfejsów: REST API, zestawy SDK, a z poziomu procedury składowane i wyzwalaczy. Podobnie jak z opóźnieniem indeksowania, przekształcania indeksu jest wykonywane asynchronicznie w tle repliki przy użyciu zapasowe zasoby, które są dostępne dla określonego repliki. 

Indeks przekształceń są również w miejscu. Azure DB rozwiązania Cosmos nie obsługiwać dwie kopie indeksu i wymiany limit starego indeksu nowym. Oznacza to, że nie dodatkowe miejsce na dysku jest wymagane lub używane w kolekcji, a indeks przekształcenia.

Jeśli zmienisz zasady indeksowania, zmiany zostaną zastosowane do przenoszenia z starego indeksu na nowego przede wszystkim oparte na indeksowania konfiguracji trybu. Indeksowania konfiguracji trybu pełnić rolę większych niż inne wartości, takie jak ścieżki uwzględniony/wykluczony, rodzaje indeksu i opisie. 

Użycie zasad zarówno stary i nowy spójne indeksowania, bazy danych Azure rozwiązania Cosmos wykonuje transformację indeksu w trybie online. Nie można zastosować innej indeksowania z trybem spójne indeksowania w trakcie transformacja zmiany zasad. Jednak możesz przejść do opóźnieniem lub brak indeksowania tryb podczas przekształcenia jest w toku: 

* Po przeniesieniu do opóźnieniem, zmiana zasad indeksu zaczyna się natychmiast. Azure DB rozwiązania Cosmos uruchamia ponownie utworzyć indeks asynchronicznie. 
* W przypadku przenoszenia None indeksu zostało przerwane natychmiast. Przenoszenie na brak jest przydatne, gdy chcesz anulować transformację w toku i rozpocząć od nowa pracę z różnych zasad indeksowania. 

Poniższy fragment kodu przedstawia sposób zmodyfikować zasady indeksowania w kolekcji z trybu indeksowania spójne opóźnieniem tryb indeksowania. Jeśli używasz zestawu .NET SDK, należy rozpocząć się poza indeksowania zmian zasad przy użyciu nowej **ReplaceDocumentCollectionAsync** metody.

**Zmodyfikuj zasady indeksowania z spójność opóźnieniem**

    // Switch to Lazy indexing mode.
    Console.WriteLine("Changing from Default to Lazy IndexingMode.");

    collection.IndexingPolicy.IndexingMode = IndexingMode.Lazy;

    await client.ReplaceDocumentCollectionAsync(collection);

**Śledź postęp przekształcania indeksu**

Można śledzić postęp procent transformacji indeksu spójne indeksu przy użyciu **IndexTransformationProgress** właściwość odpowiedzi z **ReadDocumentCollectionAsync** wywołania. Inne zestawy SDK i interfejsu API REST, obsługuje równoważne właściwości i metod do indeksowania zmiany zasad. Możesz sprawdzić postęp przekształcenia indeksu spójne indeksu przez wywołanie metody **ReadDocumentCollectionAsync**: 

    long smallWaitTimeMilliseconds = 1000;
    long progress = 0;

    while (progress < 100)
    {
        ResourceResponse<DocumentCollection> collectionReadResponse = await client.ReadDocumentCollectionAsync(
            UriFactory.CreateDocumentCollectionUri("db", "coll"));

        progress = collectionReadResponse.IndexTransformationProgress;

        await Task.Delay(TimeSpan.FromMilliseconds(smallWaitTimeMilliseconds));
    }

> [!NOTE]
> * **IndexTransformationProgress** właściwość jest stosowana tylko wtedy, gdy Przekształcanie spójne indeksu. Użyj **ResourceResponse.LazyIndexingProgress** właściwości śledzenia przekształceń do indeksu opóźnieniem.
> * **IndexTransformationProgress** i **LazyIndexingProgress** właściwości są wypełniane tylko dla kolekcji niepartycjonowany, oznacza to, że kolekcja, która została utworzona bez klucza partycji.
>

Przenosząc None indeksowania w trybie, musisz porzucić indeksu dla kolekcji. Może to być przydatne narzędzie operational, aby anulować transformację w toku, a następnie natychmiast rozpocząć nową.

**Indeks kolekcji**

    // Switch to Lazy indexing mode.
    Console.WriteLine("Dropping index by changing to to the None IndexingMode.");

    collection.IndexingPolicy.IndexingMode = IndexingMode.None;

    await client.ReplaceDocumentCollectionAsync(collection);

Podczas czy wprowadzania zmian zasad indeksowania do kolekcji bazy danych rozwiązania Cosmos Azure? Poniżej przedstawiono typowe przypadki użycia:

* Obsługiwać spójne wyniki podczas normalnego działania, ale powrotu do trybu indeksowania opóźnieniem podczas importowania danych zbiorczego.
* Uruchom przy użyciu nowych funkcji indeksowania w kolekcji bieżącej bazy danych Azure rozwiązania Cosmos. Można na przykład użyć dane geograficzne zapytań, który wymaga typu indeksu przestrzennego lub ORDER BY / ciągu kwerendy zakresu, wymagających ciągu rodzaju indeks zakresu.
* Ręcznie, zaznacz właściwości, które mają być indeksowane i ulec zmianie.
* Dostosuj indeksowania dokładności poprawić wydajność zapytań, lub Zmniejsz magazynu używane.

> [!NOTE]
> Aby zmodyfikować zasady indeksowania przy użyciu **ReplaceDocumentCollectionAsync**, musisz użyć wersji 1.3.0 lub nowszej wersji zestawu .NET SDK.
> 
> Dla indeksu transformacji do pomyślnego zakończenia Sprawdź, czy jest wystarczająco dużo wolnego miejsca dostępne w kolekcji. Jeśli kolekcja osiągnie przydział magazynowania, transformacja indeksu jest wstrzymana. Przekształcenie indeksu automatycznie zostanie wznowione, gdy miejsca do magazynowania jest dostępna, na przykład, jeśli usuniesz niektórych dokumentów.
> 
> 

## <a name="performance-tuning"></a>Dostosowywanie wydajności
Interfejsy API SQL zawierają informacje dotyczące metryki wydajności, takich jak magazyny indeksu używane i kosztów przepustowości (jednostki żądania) dla każdej operacji. Te informacje umożliwiają porównanie różnych zasad indeksowania i dostrajania wydajności.

Aby sprawdzić przydział pamięci masowej i użycia kolekcji, uruchom **HEAD** lub **UZYSKAĆ** żądania dotyczącego zasobu kolekcji. Następnie należy sprawdzić **x-ms żądania przydziału** i **x-ms żądania użycia** nagłówków. W zestawie SDK .NET [DocumentSizeQuota](http://msdn.microsoft.com/library/dn850325.aspx) i [DocumentSizeUsage](http://msdn.microsoft.com/library/azure/dn850324.aspx) właściwości w [ResourceResponse < T\> ](http://msdn.microsoft.com/library/dn799209.aspx) zawierają te wartości.

     // Measure the document size usage (which includes the index size) against   
     // different policies.
     ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));  
     Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);


Do mierzenia koszty indeksowania na każdej operacji zapisu (Tworzenie, aktualizowanie lub usuwanie), sprawdzić **x-ms żądania — opłata** nagłówka (lub odpowiednik [RequestCharge](http://msdn.microsoft.com/library/dn799099.aspx) właściwości w [ ResourceResponse < T\> ](http://msdn.microsoft.com/library/dn799209.aspx) zestawu .NET SDK) do mierzenia liczby jednostek żądania, które są używane przez te operacje.

     // Measure the performance (request units) of writes.     
     ResourceResponse<Document> response = await client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"), myDocument);              
     Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);

     // Measure the performance (request units) of queries.    
     IDocumentQuery<dynamic> queryable =  client.CreateDocumentQuery(UriFactory.CreateDocumentCollectionUri("db", "coll"), queryString).AsDocumentQuery();

     double totalRequestCharge = 0;
     while (queryable.HasMoreResults)
     {
        FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>(); 
        Console.WriteLine("Query batch consumed {0} request units",queryResponse.RequestCharge);
        totalRequestCharge += queryResponse.RequestCharge;
     }

     Console.WriteLine("Query consumed {0} request units in total", totalRequestCharge);

## <a name="changes-to-the-indexing-policy-specification"></a>Zmiany w specyfikacji zasady indeksowania
Zmiany w schemacie indeksowania zasad została wprowadzona 7 lipca 2015 z interfejsu API REST wersji 2015-06-03. Z odpowiednich klas w wersji zestawu SDK ma nowe implementacje odpowiadające schematu. 

Następujące zmiany zostały wprowadzone w specyfikacji JSON:

* Zasady indeksowania obsługuje indeksów zakresu dla ciągów.
* Każda ścieżka może mieć wiele definicji indeksu. Może mieć jeden dla każdego typu danych.
* Indeksowanie dokładności obsługuje od 1 do 8 dla liczb, od 1 do 100 dla ciągów i -1 (maksymalna dozwolona dokładność).
* Segmenty ścieżki nie wymagają cudzysłowu ucieczki poszczególnych ścieżek. Na przykład można dodać ścieżki dla   **/tytuł /?** zamiast **/ "title" /?**.
* Ścieżka katalogu głównego, który reprezentuje "wszystkie ścieżki" może być reprezentowana jako **/ \*** (oprócz **/**).

Jeśli masz kod tej kolekcji przepisy z niestandardowe zasady indeksowania napisany za pomocą zestawu .NET SDK w wersji 1.1.0 lub starszej wersji, aby przejść do zestawu SDK w wersji 1.2.0, należy zmienić kod aplikacji w celu obsługi tych zmian. Jeśli nie masz kod, który konfiguruje zasady indeksowania, lub jeśli zamierzasz nadal używać starszej wersji zestawu SDK, zmiany nie są wymagane.

Porównanie praktyczne Oto przykład niestandardowe zasady indeksowania napisane przy użyciu interfejsu API REST wersji 2015-06-03, a następnie te same zasady indeksowania napisane przy użyciu interfejsu API REST wcześniejszych wersji 2015-04-08.

**Bieżąca indeksowania zasad JSON (interfejsu API REST wersji 2015-06-03)**

    {
       "automatic":true,
       "indexingMode":"Consistent",
       "includedPaths":[
          {
             "path":"/*",
             "indexes":[
                {
                   "kind":"Hash",
                   "dataType":"String",
                   "precision":3
                },
                {
                   "kind":"Hash",
                   "dataType":"Number",
                   "precision":7
                }
             ]
          }
       ],
       "ExcludedPaths":[
          {
             "path":"/nonIndexedContent/*"
          }
       ]
    }


**Wcześniej indeksowania zasad JSON (interfejsu API REST wersji 2015-04-08)**

    {
       "automatic":true,
       "indexingMode":"Consistent",
       "IncludedPaths":[
          {
             "IndexType":"Hash",
             "Path":"/",
             "NumericPrecision":7,
             "StringPrecision":3
          }
       ],
       "ExcludedPaths":[
          "/\"nonIndexedContent\"/*"
       ]
    }


## <a name="next-steps"></a>Kolejne kroki
Dla indeksu zasad zarządzania przykładów i dowiedzieć się więcej o języku kwerend bazy danych Azure rozwiązania Cosmos zobacz następujące linki:

* [Przykłady kodu zarządzania indeksu SQL interfejs API .NET](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/IndexManagement/Program.cs)
* [Operacje kolekcji SQL interfejsu API REST](https://msdn.microsoft.com/library/azure/dn782195.aspx)
* [Zapytania SQL](sql-api-sql-query.md)

