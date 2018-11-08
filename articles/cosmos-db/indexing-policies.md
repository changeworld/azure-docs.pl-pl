---
title: Usługa Azure Cosmos DB zasady indeksowania | Dokumentacja firmy Microsoft
description: Dowiedz się, jak działa indeksowanie w usłudze Azure Cosmos DB. Dowiedz się, jak skonfigurować i zmienić zasady indeksowania do automatycznego indeksowania i większą wydajność.
keywords: Indeksowanie działania, automatycznego indeksowania, indeksowanie bazy danych
services: cosmos-db
author: rafats
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: rafats
ms.openlocfilehash: cd3b5f49788282b535f07c6f84bf7e4002132ab9
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51237591"
---
# <a name="how-does-azure-cosmos-db-index-data"></a>Jakie usługi Azure Cosmos DB indeksuje dane?

Domyślnie wszystkie dane z usługi Azure Cosmos DB jest indeksowana. Mimo że wielu klientów są ucieszy wiadomość umożliwić usłudze Azure Cosmos DB automatycznie obsługiwać wszystkie aspekty indeksowania, można określić niestandardowego *zasad indeksowania* kolekcji podczas tworzenia w usłudze Azure Cosmos DB. Zasady indeksowania w usłudze Azure Cosmos DB są bardziej elastyczne i wydajne niż indeksów pomocniczych, które są oferowane na innych platformach, bazy danych. W usłudze Azure Cosmos DB można projektować i Dostosuj kształt indeksu, bez obniżania oczekiwanego poziomu elastyczność schematu. 

Aby dowiedzieć się, jak działa indeksowanie w usłudze Azure Cosmos DB, ważne jest zrozumienie, że zasady indeksowania można zarządzać, można tworzyć szczegółową kompromis między narzut na przechowywanie indeksu, zapisu i przepływności zapytań oraz spójności zapytań.  

W poniższym klipie wideo Azure Menedżer programu usługi Cosmos DB Andrew Liu pokazuje automatycznego indeksowania możliwości i sposobu dostosowywania i konfigurowania zasad indeksowania w kontenerze usługi Azure Cosmos DB usługi Azure Cosmos DB. 

>[!VIDEO https://www.youtube.com/embed/uFu2D-GscG0]

W tym artykule będziemy Przyjrzyj się Zamknij usługę Azure Cosmos DB zasady, jak dostosować zasady indeksowania, skojarzone wady i zalety indeksowania. 

Po przeczytaniu tego artykułu, będziesz mieć możliwość odpowiedzieć na następujące pytania:

* Jak można zastąpić właściwości do dołączania lub wykluczania z indeksowania
* Jak można skonfigurować indeksu ostatecznej aktualizacji?
* Jak skonfigurować indeksowanie w celu wykonywania zapytań ORDER BY i zakresu?
* Jak dokonać zmian do zasady indeksowania kolekcji
* Jak porównać magazynu oraz wydajności różnych zasad indeksowania

## <a id="Indexing"></a> Indeksowania usługi cosmos DB

Celem indeksy bazy danych jest do obsługi zapytań w ich różnych formularzy i kształty o użyciu zasobów minimalny (na przykład procesora CPU lub we/wy) przy jednoczesnym zapewnieniu dobrej przepływności i małego opóźnienia. Często wybranego indeksu prawo do wykonywania zapytań bazy danych wymaga dużej ilości planowania i eksperymentowania. To podejście stanowi wyzwanie dla baz danych bez schematu, gdy dane nie są zgodne z ścisłego schematu i szybkiego rozwoju. 

W związku z tym Projektując podsystemu indeksowania usługi Cosmos DB, możemy ustawić następujące cele:

* Indeksować dokumenty bez schematu: indeksowania podsystem nie wymagają żadnych informacji o schemacie ani nie należy czynić żadnych założeń o schemacie dokumentów.  

* Obsługa zapytań efektywne, bogate hierarchicznych i relacyjne: indeks obsługuje język zapytań usługi Cosmos DB, takie jak obsługa projekcje relacyjnych i hierarchicznych.  

* Obsługa zapytań spójne in face of dużych ilości zapisów: w przypadku obciążeń zapisu o wysokiej przepływności za pomocą zapytań spójne zaktualizowaniu indeksu przyrostowo, sprawnie i online w przypadku dużych ilości zapisy. Aktualizacja spójne indeksu jest do obsługi zapytań na poziomie spójności, w którym użytkownik skonfigurowany usługa dokumentów.  

* Obsługa wielu dzierżawców: podana model oparty na rezerwacji dla nadzór nad zasobami dla dzierżaw, aktualizacje indeksu będą wprowadzane w ramach budżetu zasobów systemowych (CPU, pamięci i operacji wejścia/wyjścia na sekundę) przydzielone dla replik.  

* Wydajność magazynu: efektywność kosztową narzut na przechowywanie na dysku, indeksu jest ograniczony i przewidywalne. Jest to istotne, ponieważ usługi Cosmos DB umożliwia deweloperom kompromisy opartych na kosztach między narzut indeksu w odniesieniu do wydajności zapytań.  

## Dostosuj zasady indeksowania kolekcji <a id="CustomizingIndexingPolicy"></a>  
Kompromis między magazynu, zapisu i wydajności zapytań oraz spójności zapytań można dostosować poprzez zastąpienie domyślnej indeksowania zasad w kolekcji usługi Azure Cosmos DB. Można skonfigurować następujące aspekty:

* **Dołącz lub Wyklucz dokumentów i ścieżki do i z indeksu**. Można wyłączyć lub włączyć określonych dokumentów w indeksie, gdy wstawiasz lub Zamień dokumenty w kolekcji. Można również dołączyć lub wykluczyć określone właściwości kodu JSON, nazywany również *ścieżki*, mają być indeksowane wszystkich dokumentów, które znajdują się w indeksie. Ścieżki obejmują wzorców symboli wieloznacznych.
* **Skonfigurować różne typy indeksu**. Dla każdej ścieżki dołączone można określić typ indeksu, wymaganych przez ścieżkę dla kolekcji. Można określić typ indeksu na podstawie ścieżki danych, zapytanie oczekiwanego obciążenia i numeryczne/ciąg "dokładności."
* **Konfigurowanie trybów aktualizacji indeksu**. Usługa Azure Cosmos DB obsługuje trzy tryby indeksowania: spójny i leniwa, więc i None. Można skonfigurować tryby indeksowania za pośrednictwem zasad indeksowania w kolekcji usługi Azure Cosmos DB. 

Poniższy fragment kodu programu Microsoft .NET przedstawiono sposób ustawiania niestandardowych zasad indeksowania, podczas tworzenia kolekcji. W tym przykładzie ustawimy zasad z indeksem zakresu dla ciągi i liczby w maksymalna dokładność. Ta zasada służy do wykonywania zapytania w klauzuli ORDER BY, w odniesieniu do ciągów.

    DocumentCollection collection = new DocumentCollection { Id = "myCollection" };

    collection.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });
    collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), collection);   


> [!NOTE]
> Schemat JSON dla zasad indeksowania zmiany w wersji interfejsu API REST w wersji 2015-06-03. Z tą wersją schematu JSON dla zasad indeksowania obsługuje indeksy zakresu względem ciągów. Zestaw SDK platformy .NET 1.2.0 lub nowszej i Java, Python i Node.js SDK 1.1.0 obsługuje nowy schemat zasad. Wcześniejsze wersje zestawu SDK, użyj interfejsu API REST w wersji 2015-04-08. Obsługiwane są też wcześniejszych schematu dla zasad indeksowania.
> 
> Domyślnie usługi Azure Cosmos DB indeksuje wszystkie właściwości ciągu w dokumentach spójne z indeksem wyznaczania wartości skrótu. Serwer ten indeksuje wszystkie właściwości liczbowe w dokumentach spójnie na indeks zakresu.  
> 
> 

### <a name="customize-the-indexing-policy-in-the-portal"></a>Dostosuj zasady indeksowania w portalu

Można zmienić zasady indeksowania kolekcji w witrynie Azure portal: 

1. W portalu przejdź na swoje konto usługi Azure Cosmos DB, a następnie wybierz kolekcję. 
2. W menu nawigacji po lewej stronie wybierz **ustawienia**, a następnie wybierz pozycję **zasady indeksowania**. 
3. W obszarze **zasady indeksowania**, zmienić zasady indeksowania, a następnie wybierz **OK**. 

### Tryby indeksowania bazy danych <a id="indexing-modes"></a>  
Usługa Azure Cosmos DB obsługuje trzy tryby indeksowania, które można skonfigurować za pomocą zasad indeksowania w kolekcji usługi Azure Cosmos DB: spójny i leniwa, więc i None.

**Spójne**: Jeśli zasady kolekcji usługi Azure Cosmos DB jest spójność, kwerend w określonej kolekcji usługi Azure Cosmos DB, wykonaj ten sam poziom spójności w określonych dla odczytów punktu (silne, powiązana nieaktualność, "session" lub ostateczna). Indeks jest aktualizowana synchronicznie, w ramach aktualizacji dokumentu (insert, Zastąp, update i delete dokumentów w kolekcji usługi Azure Cosmos DB).

Spójne indeksowania obsługuje spójne zapytania kosztem możliwe zmniejszenie przepływności zapisu. Obniżenie to funkcja unikatowych ścieżek, które muszą zostać pomyślnie zindeksowane i "poziom spójności". Spójne tryb indeksowania jest zaprojektowana dla obciążeń "write szybkiego zapytań od razu".

**Lazy**: zaktualizowaniu indeksu asynchronicznie, gdy kolekcji usługi Azure Cosmos DB jest spoczynku, oznacza to, gdy przepływność kolekcji nie jest w pełni wykorzystywany do obsługi żądań użytkowników.  Należy pamiętać, że może pobrać niespójne wyniki, ponieważ dane są pozyskiwane i indeksowane powoli. Oznacza to, że liczba zapytaniach ani wyników zapytania określonego może nie być zgodne lub repeatable w danej chwili. 

Indeks znajduje się zwykle w trybie wyrównującej z pozyskiwanych danych. Z opóźnieniem, indeksowanie czas wygaśnięcia (TTL) zmieni wynik w indeksie jest porzucona i utworzona ponownie. To sprawia, że wyniki zapytania i liczba niespójna w okresie czasu. Większość kont usługi Azure Cosmos DB, należy używać spójnego trybu indeksowania.

**Brak**: kolekcja, która nie ma żadnego indeksu trybie nie ma indeksu skojarzonych z nim. Jest to często używane, jeśli usługi Azure Cosmos DB jest używany jako magazyn kluczy i wartości, a dostęp do dokumentów tylko przez ich właściwości Identyfikatora. 

> [!NOTE]
> Konfigurowanie zasad indeksowania za pomocą None powoduje po stronie porzucenie dowolnego istniejącego indeksu. Użyj, jeżeli Twoich wzorców dostępu wymagają tylko identyfikator lub link do samego siebie.
> 
> 

W poniższej tabeli przedstawiono spójności zapytań na podstawie tryb indeksowania (spójność i leniwy) skonfigurowane dla kolekcji i poziomu spójności, określony dla żądań zapytań. Dotyczy to zapytań przy użyciu dowolnego interfejsu: interfejsu API, SDK, REST, lub z poziomu procedur składowanych i wyzwalaczy. 

|Spójność|Tryb indeksowania: zgodne|Tryb indeksowania: powolne|
|---|---|---|
|Silna|Silna|Ostateczna|
|Powiązana nieaktualność|Powiązana nieaktualność|Ostateczna|
|Sesja|Sesja|Ostateczna|
|Ostateczna|Ostateczna|Ostateczna|

Usługa Azure Cosmos DB zwraca błąd dla zapytań w kolekcji, które Brak indeksowania w trybie. Nadal można wykonać zapytania jako skanowania za pomocą jawnego **x-ms-bazy danych documentdb — enable skanowania** nagłówka w interfejsie API REST lub **EnableScanInQuery** zażądać opcja przy użyciu zestawu .NET SDK. Niektóre funkcje zapytań, takich jak ORDER BY nie są obsługiwane jako skanowania za pomocą **EnableScanInQuery**.

W poniższej tabeli przedstawiono spójności zapytań na podstawie indeksowania trybu (spójność, opóźnieniem i None) podczas **EnableScanInQuery** jest określony.

|Spójność|Indeksowanie tryb: zgodne|Indeksowanie tryb: powolne|Indeksowanie tryb: Brak|
|---|---|---|---|
|Silna|Silna|Ostateczna|Silna|
|Powiązana nieaktualność|Powiązana nieaktualność|Ostateczna|Powiązana nieaktualność|
|Sesja|Sesja|Ostateczna|Sesja|
|Ostateczna|Ostateczna|Ostateczna|Ostateczna|

Poniższych pokazano przykładowy kod jak utworzyć kolekcję usługi Azure Cosmos DB przy użyciu zestawu .NET SDK spójne indeksowanie wszystkich wstawienia dokumentu.

     // Default collection creates a Hash index for all string fields and a Range index for all numeric    
     // fields. Hash indexes are compact and offer efficient performance for equality queries.

     var collection = new DocumentCollection { Id ="defaultCollection" };

     collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;

     collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("mydb"), collection);


### <a name="index-paths"></a>Indeks ścieżki
Usługa Azure Cosmos DB modeluje dokumenty JSON i indeks jako drzewa. Możesz określić zasad dla ścieżek w drzewie. W dokumentach możesz wybrać ścieżki do dołączania lub wykluczania z indeksowania. To może zaoferować zapisu ulepszoną wydajność i niższe magazyn indeksów dla scenariuszy, w których wzorców zapytań są znane wcześniej.

Ścieżki indeksu rozpoczynać root (/) i zazwyczaj kończy się? operator symboli wieloznacznych. Oznacza to, czy istnieje wiele możliwych wartości dla prefiksu. Na przykład, aby obsługiwać SELECT * FROM F.familyName gdzie rodzin F = "Andersen" musi zawierać ścieżkę indeksu dla /familyName/? w kolekcji indeks zasadach.

Indeks ścieżki można również użyć \* operator symbolu wieloznacznego do określania zachowania rekursywnie ścieżki w ramach prefiksu. Na przykład/ładunku / * można wykluczyć wszystkie elementy w ramach właściwości ładunku z indeksowania.

Poniżej przedstawiono typowe wzorce do określania ścieżek indeksu:

| Ścieżka                | Opis elementu/użycia                                                                                                                                                                                                                                                                                         |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| /                   | Domyślna ścieżka dla kolekcji. Cykliczne oraz będzie miało zastosowanie do drzewa całego dokumentu.                                                                                                                                                                                                                                   |
| / prop /?             | Ścieżka indeksu jest wymagana do obsługi zapytań, podobnie do następującej (w przypadku typów wyznaczania wartości skrótu lub zakresu, odpowiednio):<br><br>Wybierz z kolekcji języka c WHERE c.prop = "value"<br><br>Wybierz z kolekcji języka c WHERE c.prop > 5<br><br>Wybierz z kolekcji c ORDER BY c.prop                                                                       |
| / prop / *             | Ścieżka indeksu dla wszystkich ścieżek w ramach określonej etykiety. Działa z następujących zapytań<br><br>Wybierz z kolekcji języka c WHERE c.prop = "value"<br><br>Wybierz z kolekcji języka c WHERE c.prop.subprop > 5<br><br>Wybierz z kolekcji języka c WHERE c.prop.subprop.nextprop = "value"<br><br>Wybierz z kolekcji c ORDER BY c.prop         |
| / Właściwości / [] /?         | Ścieżka indeksu są wymagane do obsługi iteracji, a następnie dołącz zapytania względem tablic wartości skalarnych, takich jak ["a", "b", "c"]:<br><br>Wybierz tag z collection.props w tagu, gdzie tag = "value"<br><br>Wybierz tag z kolekcji c sprzężenia tagu w c.props gdzie tag > 5                                                                         |
| /subprop/ [] /props/? | Ścieżka indeksu wymagane do obsługi iteracji i zapytań sprzężenia dla tablic obiektów, takich jak [{subprop: ""}, {subprop: "b"}]:<br><br>Wybierz tag z tagu w collection.props tag.subprop gdzie = "value"<br><br>Wybierz tag z kolekcji c sprzężenia tagu w c.props tag.subprop gdzie = "value"                                  |
| / prop/subprop /?     | Ścieżka indeksu jest wymagana do obsługi zapytań (w przypadku typów wyznaczania wartości skrótu lub zakresu, odpowiednio):<br><br>Wybierz z kolekcji języka c WHERE c.prop.subprop = "value"<br><br>Wybierz z kolekcji języka c WHERE c.prop.subprop > 5                                                                                                                    |

> [!NOTE]
> Podczas ustawiania indeksu niestandardowej ścieżki jest wymagane do określenia zasadę domyślną indeksowania drzewa całego dokumentu, która jest oznaczona przy użyciu specjalnych ścieżki "/ *". 
> 
> 

Poniższy przykład umożliwia skonfigurowanie określonej ścieżki przy użyciu początkowemu indeksowi i wartość niestandardowego dokładności 20 bajtów:

```
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
```

Po dodaniu ścieżki do indeksowania liczb i ciągów w ramach tych ścieżek są indeksowane. Dlatego nawet jeśli zdefiniujesz indeksowanie tylko ciągi, usługi Azure Cosmos DB dodaje definicji domyślnej w przypadku liczb, jak również. Innymi słowy, usługi Azure Cosmos DB ma możliwość wyłączenia ścieżki z zasad indeksowania, ale nie wpisać wykluczenia z określonej ścieżki. Oto przykład, pamiętaj, że tylko jeden indeks jest określona dla obu ścieżek (ścieżka = "/ *" i ścieżka = "/\"attr1\"/?"), ale liczba typ danych jest także dodawane do wyniku.

```
var indices = new[]{
                new IncludedPath  {
                    Indexes = new Collection<Index>
                    {
                        new RangeIndex(DataType.String) { Precision = 3 }// <- note: only 1 index specified
                    },
                    Path =  "/*"
                },
                new IncludedPath  {
                    Indexes = new Collection<Index>
                    {
                        new RangeIndex(DataType.String) { Precision = 3 } // <- note: only 1 index specified
                    },
                    Path =  "/\"attr1\"/?"
                }
            };...

            foreach (var index in indices)
            {
                documentCollection.IndexingPolicy.IncludedPaths.Add(index);
            }
```

Wynik utworzenia indeksu:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*",
            "indexes": [
                {
                    "kind": "Range",
                    "dataType": "String",
                    "precision": 3
                },
                {
                    "kind": "Range",
                    "dataType": "Number",
                    "precision": -1
                }
            ]
        },
        {
            "path": "/\"attr\"/?",
            "indexes": [
                {
                    "kind": "Range",
                    "dataType": "String",
                    "precision": 3
                },
                {
                    "kind": "Range",
                    "dataType": "Number",
                    "precision": -1
                }
            ]
        }
    ],
}
```

### <a name="index-data-types-kinds-and-precisions"></a>Typy danych, rodzajów i opisie
Istnieje wiele opcji, podczas konfigurowania zasad indeksowania dla ścieżki. Można określić jedną lub więcej definicji indeksowania dla każdej ścieżki:

* **Typ danych**: ciąg, liczba, punkt, wielokąta lub LineString (może zawierać tylko jeden wpis dla typu danych na każdej ścieżce).
* **Indeks rodzaj**: wyznaczania wartości skrótu (zapytań o równość), zakresu (równości, zakresu lub zapytania w klauzuli ORDER BY) lub przestrzenne (zapytań przestrzennych).
* **Dokładność**: indeks do wyznaczania wartości skrótu, to różni się od 1 do 8 na ciągi i liczby. Wartość domyślna to 3. Indeks zakresu ta wartość może być -1 (maksymalna dozwolona dokładność). Może się różnić znajdujące 1 do 100 (maksymalna dozwolona dokładność) dla parametrów lub wartości liczbowe.

#### <a name="index-kind"></a>Typ indeksu
Usługa Azure Cosmos DB obsługuje indeksu skrótu i rodzajów indeks zakresu dla każdej ścieżce, które można skonfigurować ciąg lub liczba typów danych lub obu.

* **Skrót** obsługuje wydajne równości i łączenia zapytań. W większości przypadków użycia większą precyzję niż domyślna wartość 3 bajtów nie ma potrzeby indeksów skrótu. Typ danych może być ciąg lub liczba.
* **Zakres** obsługuje zapytań o równość wydajne, zapytań o zakres (przy użyciu >, <>, =, < =,! =) i zapytania w klauzuli ORDER BY. Zapytania w klauzuli ORDER By, domyślnie wymagają także precyzja maksymalna wartość indeksu (-1). Typ danych może być ciąg lub liczba.

Usługa Azure Cosmos DB obsługuje również rodzaj indeks przestrzenny dla każdej ścieżki, który może być określony dla typów danych punkt, wielokąta lub LineString. Wartość w określonej ścieżce musi być prawidłowy fragment GeoJSON, takich jak `{"type": "Point", "coordinates": [0.0, 10.0]}`.

* **Przestrzenne** obsługuje wydajne przestrzennego (w ramach i odległość) zapytania. Typ danych może być punkt, wielokąta lub LineString.

> [!NOTE]
> Usługa Azure Cosmos DB obsługuje automatyczne indeksowanie punkt wielokąta i LineString typów danych.
> 
> 

Poniżej przedstawiono typy obsługiwanych indeksu i przykłady kwerend, które mogą być używane do udostępniania:

| Typ indeksu | Opis elementu/użycia                                                                                                                                                                                                                                                                                                                                                                                                              |
| ---------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Skrót       | Wyznaczania wartości skrótu, za pośrednictwem/prop /? (lub /) może służyć do efektywnie obsługiwać następujące zapytania:<br><br>Wybierz z kolekcji języka c WHERE c.prop = "value"<br><br>Skrót/właściwości / [] /? (i / lub/właściwości /) może służyć do efektywnie obsługiwać następujące zapytania:<br><br>Wybierz tag z kolekcji c sprzężenia tagu w c.props, gdzie tag = 5                                                                                                                       |
| Zakres      | Zakres za pośrednictwem/prop /? (lub /) może służyć do efektywnie obsługiwać następujące zapytania:<br><br>Wybierz z kolekcji języka c WHERE c.prop = "value"<br><br>Wybierz z kolekcji języka c WHERE c.prop > 5<br><br>Wybierz z kolekcji c ORDER BY c.prop                                                                                                                                                                                                              |
| Przestrzenne     | Zakres za pośrednictwem/prop /? (lub /) może służyć do efektywnie obsługiwać następujące zapytania:<br><br>Wybierz z kolekcji języka c<br><br>GDZIE ST_DISTANCE (c.prop, {"type": "Punkt", "coordinates": [0.0, 10.0]}) < 40<br><br>Wybierz z kolekcji c gdzie ST_WITHIN(c.prop, {"type": "Polygon",...}) — za pomocą indeksowania w punktach włączone<br><br>Wybierz z kolekcji c gdzie ST_WITHIN({"type": "Point",...}, c.prop) — za pomocą indeksowanie włączone wielokątów              |

Domyślnie, zwracany jest błąd dla zapytania z zakresu operatorów, takich jak > = w przypadku nie zakresu indeks (wszystkie precyzji) do sygnalizowania, że skanowanie może być niezbędne do obsługi zapytań. Można wykonać zapytania zakresowe bez indeks zakresu przy użyciu **x-ms-bazy danych documentdb — enable skanowania** nagłówka w interfejsie API REST lub **EnableScanInQuery** zażądać opcja przy użyciu zestawu .NET SDK. Jeśli istnieją inne filtry w zapytaniu usługi Azure Cosmos DB można użyć indeksu do filtrowania względem, nie błąd jest zwracany.

Te same zasady mają zastosowanie dla zapytań przestrzennych. Domyślnie błąd jest zwracany dla zapytań przestrzennych, jeśli istnieje nie indeksów przestrzennych, a nie filtrów, które mogą być udostępniane przez indeks. Może zostać wykonana jako skanowania za pomocą **x-ms-bazy danych documentdb — enable skanowania** lub **EnableScanInQuery**.

#### <a name="index-precision"></a>Precyzja indeksu
Indeks dokładności umożliwia kompromisu między magazyn indeksów obciążenie i wydajność zapytań. W przypadku liczb zaleca się przy użyciu domyślnej konfiguracji precision-1 (maksimum). Ponieważ cyfry 8 bajtów w formacie JSON, jest to równoważne do konfiguracji 8 bajtów. Wybieranie niższa wartość dokładności, np. od 1 do 7, oznacza, że wartości w niektórych zakresach mapy do tej samej pozycja indeksu. W związku z tym można zmniejszyć miejsce do magazynowania indeksu, ale wykonanie zapytania może być konieczne przetwarzanie więcej dokumentów. W związku z tym zużywa więcej przepływności w jednostkach żądania.

Konfiguracja dokładności indeks ma praktyczniejsze w aplikacji przy użyciu ciągu zakresów. Ponieważ ciągi mogą być każdej dowolnej długości, wybór dokładności indeks może mieć wpływ na wydajność zapytań o zakres ciągu. To również może wpłynąć na ilość miejsca do magazynowania indeksów, które są wymagane. Można skonfigurować indeksy ciąg zakresu od 1 do 100 lub wartość -1 (maksimum). Jeśli chcesz wykonywać zapytania w klauzuli ORDER BY, w odniesieniu do właściwości ciągów, należy określić dokładności-1 dla odpowiednich ścieżek.

Indeksy przestrzenne zawsze używaj domyślna dokładność indeksu dla wszystkich typów (punkt, LineString i wielokąta). Nie można zastąpić domyślną precyzję indeksu dla indeksów przestrzennych. 

Poniższy przykład pokazuje, jak zwiększyć dokładność dla indeksów zakresu w kolekcji przy użyciu zestawu .NET SDK. 

**Utwórz kolekcję o indeksie niestandardowym dokładności**

    var rangeDefault = new DocumentCollection { Id = "rangeCollection" };

    // Override the default policy for strings to Range indexing and "max" (-1) precision
    rangeDefault.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), rangeDefault);   


> [!NOTE]
> Usługa Azure Cosmos DB zwraca błąd, gdy zapytanie używa klauzuli ORDER BY, ale nie ma indeksu zakresu na ścieżce kwerendy z maksymalną dokładnością. 
> 
> 

Podobnie można całkowicie wykluczać ścieżki z indeksowania. W kolejnym przykładzie pokazano, jak wykluczyć całą sekcję dokumentów ( *poddrzewo*) z indeksowania za pomocą \* operator symboli wieloznacznych.

    var excluded = new DocumentCollection { Id = "excludedPathCollection" };
    excluded.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
    excluded.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*" });

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), excluded);



## <a name="opt-in-and-opt-out-of-indexing"></a>Zgoda i zrezygnować z indeksowania
Można wybrać, czy mają kolekcja do automatycznego indeksowania wszystkich dokumentów. Domyślnie wszystkie dokumenty są automatycznie indeksowane, ale możesz wyłączyć automatyczne indeksowanie. Po wyłączeniu indeksowanie dokumentów można uzyskać dostęp tylko za pośrednictwem ich linków do samego siebie lub przez zapytania za pomocą dokumentów identyfikator.

Za pomocą automatycznego indeksowania, wyłączona, nadal selektywnie można dodać tylko do określonych dokumentów do indeksu. Z drugiej strony możesz pozostawić automatyczne indeksowanie i selektywnie wskazać, które mają zostać wykluczone z określonych dokumentów. Indeksowanie i wyłączanie konfiguracji są przydatne, jeśli masz tylko podzbiór dokumentów, które muszą zostać wykonane zapytanie.

Poniższy przykład pokazuje, jak dołączyć dokument jawnie za pomocą [SQL API .NET SDK](https://docs.microsoft.com/azure/cosmos-db/sql-api-sdk-dotnet) i [RequestOptions.IndexingDirective](https://msdn.microsoft.com/library/microsoft.azure.documents.client.requestoptions.indexingdirective.aspx) właściwości.

    // If you want to override the default collection behavior to either
    // exclude (or include) a document in indexing,
    // use the RequestOptions.IndexingDirective property.
    client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"),
        new { id = "AndersenFamily", isRegistered = true },
        new RequestOptions { IndexingDirective = IndexingDirective.Include });

## <a name="modify-the-indexing-policy-of-a-collection"></a>Zmodyfikuj zasady indeksowania kolekcji
W usłudze Azure Cosmos DB możesz wprowadzić zmiany do zasady indeksowania kolekcji na bieżąco. Zmiana zasad w kolekcji usługi Azure Cosmos DB indeksowania może prowadzić do zmian w kształcie indeksu. Wpływa ścieżek, które mogą być indeksowane, ich dokładności i modelu spójności samego indeksu. Zmiana zasad indeksowania skutecznie wymaga transformacji starego indeksu do nowego indeksu. 

**Przekształcenia indeksu w trybie online**

![Jak działa indeksowanie — przekształcenia indeksu w trybie online w usłudze Azure Cosmos DB](./media/indexing-policies/index-transformations.png)

Indeks przekształcenia są przełączyć do trybu online. Oznacza to, że dokumentów indeksowanych na stare zasady wydajne są przekształcane na nowe zasady *bez wywierania wpływu na dostępność zapisu lub aprowizowanej przepływności* kolekcji. Spójności operacji odczytu i zapisu przy użyciu interfejsu API REST zestawów SDK, lub z poziomu procedur składowanych i wyzwalaczy nie występuje podczas przekształcania indeksu. 

Zmiana zasad indeksowania jest proces asynchroniczny i czas wymagany do ukończenia operacji zależy od liczby dokumentów, zainicjowanych jednostek zarezerwowanych i rozmiar dokumentów. Podczas ponownego indeksowania, jest w toku, zapytanie może nie zwracać wszystkie zgodnych wyników, jeśli zapytanie używa indeksu, która jest modyfikowana i zapytania nie będzie zwracać wszystkie błędy/błędy. Podczas ponownego indeksowania, jest w toku, zapytania są ostatecznie spójne bez względu na konfigurację trybu indeksowania (spójność lub leniwy). Po indeksie przekształcenie zostało zakończone, możesz będą nadal widzieć spójne wyniki. Dotyczy to również zapytania ze wszystkich interfejsów: interfejsu API, SDK, REST i z poziomu procedur składowanych i wyzwalaczy. Podobnie jak z opóźnieniem, indeksowanie, przekształcania indeksu jest wykonywana asynchronicznie w tle repliki przy użyciu wolnym zasoby, które są dostępne dla określonych repliki. 

Indeks przekształcenia są również w miejscu. Usługa Azure Cosmos DB nie Obsługa dwie kopie indeksu i wymiany się stary indeks nowym plikiem. Oznacza to, że nie dodatkowe miejsce na dysku jest wymagane lub używane w kolekcji, gdy indeks przekształcenia występują.

Po zmianie zasad indeksowania, zmiany zostaną zastosowane przenoszenia ze starego indeksu do nowych przede wszystkim na podstawie indeksowania tryb konfiguracji. Indeksowanie konfiguracji trybu mają znaczenie większych niż inne wartości, takich jak ścieżki uwzględniony/wykluczony, rodzaje indeksu i opisie. 

Użycie zasad zarówno stare i nowe spójne indeksowania, usługę Azure Cosmos DB wykonuje przekształcenie indeksu w trybie online. Nie można zastosować inny zmiany zasad indeksowania, z trybem spójne indeksowania w trakcie przekształcenia. Jednak można przenieść do leniwy lub brak indeksowania tryb podczas transformacji jest w toku: 

* Gdy przeniesiesz się na leniwy zmiany zasad indeksu, zacznie ona obowiązywać natychmiast. Usługa Azure Cosmos DB uruchamia ponownie utworzyć indeks asynchronicznie. 
* Gdy przesuniesz None, indeks zostało porzucone od razu. Przenoszenie na brak jest przydatne w przypadku, gdy chcesz anulować przekształcania w toku i zacznij od różnych zasad indeksowania. 

Poniższy fragment kodu przedstawia sposób modyfikowania zasad indeksowania kolekcji z trybu indeksowania spójne z opóźnieniem tryb indeksowania. Jeśli korzystasz z zestawu .NET SDK, możesz uruchamiał indeksowania zmiany zasad, za pomocą nowego **ReplaceDocumentCollectionAsync** metody.

**Modyfikowanie zasad indeksowania z spójność leniwy**

    // Switch to Lazy indexing mode.
    Console.WriteLine("Changing from Default to Lazy IndexingMode.");

    collection.IndexingPolicy.IndexingMode = IndexingMode.Lazy;

    await client.ReplaceDocumentCollectionAsync(collection);

**Śledzenie postępu przekształcania indeksu**

Możesz śledzić postępy procent, przekształcenia indeksu do spójnego indeksu za pomocą **IndexTransformationProgress** właściwości odpowiedzi z **ReadDocumentCollectionAsync** wywołania. Inne zestawy SDK i interfejsu API REST, obsługa równoważne właściwości i metody wprowadzania zmian zasad indeksowania. Można sprawdzić postęp przekształcania indeksu do spójnego indeksu przez wywołanie metody **ReadDocumentCollectionAsync**: 

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
> * **IndexTransformationProgress** właściwość ma zastosowanie tylko wtedy, gdy przekształcenie do spójnego indeksu. Użyj **ResourceResponse.LazyIndexingProgress** właściwości do śledzenia przekształcenia do indeksu z opóźnieniem.
> * **IndexTransformationProgress** i **LazyIndexingProgress** właściwości są wypełniane tylko w przypadku kolekcja niepartycjonowana, oznacza to, kolekcję, która została utworzona bez klucza partycji.
>

Można usunąć indeksu dla kolekcji, przenosząc None indeksowania w trybie. Może to być przydatne narzędzie operacyjnej, jeśli chcesz anulować przekształcania w toku i od razu rozpocząć nową.

**Usuń indeks dla kolekcji**

    // Switch to Lazy indexing mode.
    Console.WriteLine("Dropping index by changing to the None IndexingMode.");

    collection.IndexingPolicy.IndexingMode = IndexingMode.None;

    await client.ReplaceDocumentCollectionAsync(collection);

Kiedy będzie wprowadzeniu zmiany zasad indeksowania do kolekcji usługi Azure Cosmos DB? Poniżej przedstawiono najbardziej typowe przypadki użycia:

* Obsługiwać spójne wyniki podczas normalnego działania, ale podczas importu danych zbiorczego przełączyć się na tryb indeksowania z opóźnieniem.
* Rozpocznij korzystanie z nowych funkcji indeksowania w bieżącej kolekcji usługi Azure Cosmos DB. Na przykład można użyć zapytania geoprzestrzenne, co wymaga rodzaju indeks przestrzenny, lub ORDER BY opcji / ciąg kwerendy zakresu, wymagających ciągu rodzaju indeks zakresu.
* Ręcznie wybierz opcję właściwości, które mają być indeksowane i zmień je wraz z upływem czasu.
* Dostosowywanie indeksowania dokładności, aby poprawić wydajność zapytań, lub aby ograniczyć wykorzystanie magazynu.

> [!NOTE]
> Aby zmodyfikować zasady indeksowania za pomocą **ReplaceDocumentCollectionAsync**, należy użyć wersji 1.3.0 lub nowszej wersji zestawu SDK platformy .NET.
> 
> W przypadku przekształcania indeksu do pomyślnego zakończenia upewnij się, że jest wystarczająco dużo wolnego miejsca dostępna w kolekcji. Jeśli kolekcja osiągnie przydział magazynowania, przekształcania indeksu zostało wstrzymane. Przekształcenie indeksu zostanie automatycznie wznowione, gdy miejsce do magazynowania jest dostępna, na przykład, jeśli usuniesz niektóre dokumenty.
> 
> 

## <a name="performance-tuning"></a>Dostosowywanie wydajności
Interfejsy API SQL zawierają informacje dotyczące metryk wydajności, takich jak magazyn indeksów używane i kosztów przepustowości (w jednostkach żądania) dla każdej operacji. Można użyć tych informacji do porównania różnych zasad indeksowania i dotyczące dostosowywania wydajności.

Aby sprawdzić limit przydziału magazynu i użycia kolekcji, należy uruchomić **HEAD** lub **UZYSKAĆ** żądania dotyczącego zasobu kolekcji. Następnie należy sprawdzić **x-ms zażądać przydziału** i **x-ms żądania — użycie** nagłówków. W zestawie SDK platformy .NET [DocumentSizeQuota](https://msdn.microsoft.com/library/dn850325.aspx) i [DocumentSizeUsage](https://msdn.microsoft.com/library/azure/dn850324.aspx) właściwości w [ResourceResponse < T\> ](https://msdn.microsoft.com/library/dn799209.aspx) zawierają te odpowiadające im wartości.

     // Measure the document size usage (which includes the index size) against   
     // different policies.
     ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));  
     Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);


Do mierzenia obciążenie indeksowanie każdej operacji zapisu (Tworzenie, aktualizowanie lub usuwanie), sprawdzić **x-ms żądanie — opłata** nagłówka (lub odpowiednik [RequestCharge](https://msdn.microsoft.com/library/dn799099.aspx) właściwości w [ ResourceResponse < T\> ](https://msdn.microsoft.com/library/dn799209.aspx) w zestawie .NET SDK) do mierzenia liczby jednostek żądania, które są używane przez te operacje.

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

## <a name="changes-to-the-indexing-policy-specification"></a>Zmiany w specyfikacji zasad indeksowania
Zmiany schematu dla zasad indeksowania wprowadzono 7 lipca 2015 za pomocą interfejsu API REST w wersji 2015-06-03. Odpowiednich klas w wersji zestawu SDK ma nowy implementacji w celu dostosowania do schematu. 

Następujące zmiany zostały wprowadzone w specyfikacji formatu JSON:

* Zasady dotyczące indeksowania obsługuje indeksy zakresu dla ciągów.
* Każda ścieżka może mieć wiele definicji indeksu. Może mieć jeden dla każdego typu danych.
* Indeksowanie dokładności obsługuje od 1 do 8 w przypadku liczb, od 1 do 100 dla ciągów i -1 (maksymalna dozwolona dokładność).
* Segmenty ścieżki nie wymagają cudzysłowu jako znak ucieczki dla każdej ścieżki. Na przykład można dodać ścieżkę   **/tytuł /?** zamiast **/ "title" /?**.
* Ścieżka katalogu głównego, który reprezentuje "wszystkie ścieżki" może być reprezentowana jako **/ \*** (oprócz **/**).

Jeśli masz kod tej kolekcji przepisów za pomocą niestandardowych zasad indeksowania napisane przy użyciu zestawu SDK .NET w wersji 1.1.0 lub wcześniejszej wersji, aby przejść do zestawu SDK w wersji 1.2.0 lub nowszej, należy zmienić kod aplikacji do obsługi tych zmian. Jeśli nie masz kodu, który konfiguruje zasad indeksowania lub jeśli planujesz kontynuować korzystanie z wcześniejszej wersji zestawu SDK są wymagane nie zmiany.

Dla porównania praktycznych poniżej przedstawiono przykład niestandardowych zasad indeksowania napisane przy użyciu interfejsu API REST w wersji 2015-06-03, a następnie te same zasady indeksowania napisane przy użyciu interfejsu API REST wcześniejszych wersji 2015-04-08.

**Bieżące zasady JSON (interfejs API REST w wersji 2015-06-03) indeksowania**

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


**Indeksowanie wcześniej zasad JSON (interfejs API REST w wersji 2015-04-08)**

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
Indeks zasadach zarządzania przykładów i dowiedzieć się więcej na temat języka zapytań usługi Azure Cosmos DB zobacz następujące linki:

* [Przykłady kodu zarządzania indeksem .NET interfejsu API SQL](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/IndexManagement/Program.cs)
* [Operacje kolekcji SQL interfejsu API REST](https://msdn.microsoft.com/library/azure/dn782195.aspx)
* [Zapytania przy użyciu języka SQL](sql-api-sql-query.md)

