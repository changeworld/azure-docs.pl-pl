---
title: Jak wdrożyć nawigację aspektową w hierarchii kategorii — Usługa Azure Search
description: Dodaj zestaw reguł nawigacji do aplikacji, które integrują się z usługą Azure Search, Usługa wyszukiwania hostowane w chmurze w systemie Microsoft Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: c032dbc528ed5034280d0ecb4c95700b51869991
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65793621"
---
# <a name="how-to-implement-faceted-navigation-in-azure-search"></a>Jak wdrożyć nawigację aspektową w usłudze Azure Search
Nawigacja aspektowa jest mechanizm filtrowania, który zapewnia kierunkowane samodzielnie przez użytkowników Przechodzenie do szczegółów nawigacji w aplikacji do wyszukiwania. Termin "nawigacji aspektowej" mogą być nieznane, ale prawdopodobnie użyto go przed. Jak pokazano na poniższym przykładzie, nawigacji aspektowej ma więcej niż kategorii, używana do filtrowania wyników.

 ![Usługa Azure Search zadania portalu w wersji demonstracyjnej](media/search-faceted-navigation/azure-search-faceting-example.png "pokaz portalu zadania usługi Azure Search")

Nawigacja aspektowa jest punktem wejścia alternatywne do wyszukiwania. Oferuje ona jest wygodną alternatywą dla ręcznego wpisywania wyrażeń złożonych wyszukiwań. Aspektami może pomóc Ci znaleźć, czego szukasz, przy jednoczesnym zapewnieniu, że nie otrzymasz wyników, zerowego. Jako deweloper aspektami umożliwiają udostępnianie najbardziej przydatne kryteria wyszukiwania do nawigowania między indeksu wyszukiwania. W aplikacjach detalicznego handlu online nawigacji aspektowej, często bazuje na marki, działów (Kącik buty), rozmiar, ceny, popularność i oceny. 

Implementowanie nawigacji aspektowej różni się w technologiach wyszukiwania. W usłudze Azure Search nawigacji aspektowej bazuje na czas wykonywania zapytania przy użyciu pól, które uprzednio przypisane w schemacie.

-   W zapytaniach, które kompilacje aplikacji, zapytanie musi wysłać *parametry zapytania aspekt* można pobrać wartości filtru reguł dostępnych dla zestawu wyników tego dokumentu.

-   Faktycznie przyciąć wynik dokumentu należy ustawić, aplikacja musi mieć zastosowanie `$filter` wyrażenia.

W zakresie opracowywania aplikacji pisanie kodu, który konstruuje zapytania stanowi duża część pracy. Wiele zachowań aplikacji, które można oczekiwać od nawigacji aspektowej są udostępniane przez usługi, w tym wbudowaną obsługę definiowania zakresów i wprowadzenie liczby wyników zestawu reguł. Ta usługa obejmuje również rozsądne wartości domyślne, które pomagają uniknąć struktury nawigacji niewygodna. 

## <a name="sample-code-and-demo"></a>Przykładowy kod i wersji demonstracyjnej
Tym artykule użyto portalu do wyszukiwania zadania jako przykład. Przykład jest implementowany jako aplikację ASP.NET MVC.

-   Obejrzeć i przetestować pokaz pracy w [usługi Azure Search zadania Portal Demo](https://azjobsdemo.azurewebsites.net/).

-   Pobierz kod ze [repozytorium przykładów dla platformy Azure w serwisie GitHub](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs).

## <a name="get-started"></a>Rozpoczęcie pracy
Jeśli jesteś nowym użytkownikiem wyszukiwania rozwoju, jest najlepszym sposobem traktować nawigacji aspektowej, pokazuje możliwości wyszukiwanie kierunkowane samodzielnie przez użytkowników. Jest to typ środowiska wyszukiwania Przechodzenie do szczegółów, w oparciu o wstępnie zdefiniowane filtry, umożliwiający szybkie zawężając wyników wyszukiwania za pomocą akcji wskaż i kliknij. 

### <a name="interaction-model"></a>Model interakcji

Środowisko wyszukiwania nawigacji aspektowej jest iteracyjną, więc warto zacząć od zrozumienia go jako sekwencja zapytań, które są rozwijane w odpowiedzi na działania użytkownika.

Punktem wyjścia jest strona aplikacji, który zapewnia nawigacji aspektowej, zwykle umieszczane w obwodzie. Nawigacja aspektowa jest często strukturze drzewa za pomocą pola wyboru dla każdej wartości lub możesz klikać tekstu. 

1. Zapytanie wysyłane do usługi Azure Search określa strukturę nawigacji aspektowej, za pośrednictwem co najmniej jeden parametr zapytania zestawu reguł. Na przykład, zapytanie może obejmować `facet=Rating`, być może z `:values` lub `:sort` opcję, aby uściślić prezentacji.
2. Warstwa prezentacji renderuje stronę wyszukiwania, która zapewnia nawigacji aspektowej, za pomocą aspektami określonymi dla żądania.
3. Biorąc pod uwagę strukturę nawigacji aspektowej, która obejmuje klasyfikację, możesz kliknąć pozycję "4", aby wskazać, że mają być wyświetlane tylko produkty o ocenę 4 lub nowszy. 
4. W odpowiedzi aplikacja wysyła zapytanie, które zawiera `$filter=Rating ge 4` 
5. Warstwa prezentacji aktualizuje zestaw wyników mniejsze, zawierający tylko te elementy, które spełniają kryteria nowe przedstawiający stronę (w tym przypadku produktów oceniane 4 i nowsze).

Zestaw reguł jest parametr zapytania, ale nie należy mylić z danymi wejściowymi zapytania. Nigdy nie jest używany jako kryteria wyboru w zapytaniu. Zamiast tego Pomyśl o aspekt parametry zapytania jako dane wejściowe do struktury nawigacji, która będzie działać w odpowiedzi. Dla każdego parametru zapytania aspekt podawane usługi Azure Search ocenia, liczby dokumentów znajdują się w wyniki częściowe dla każdej wartości zestawu reguł.

Zwróć uwagę `$filter` w kroku 4. Filtr jest istotnym elementem nawigacji aspektowej. Mimo że zestawy reguł i filtrów są niezależne w interfejsie API, należy zarówno do dostarczania środowiska pracy, które mają. 

### <a name="app-design-pattern"></a>Wzorzec projektowania aplikacji

W kodzie aplikacji wzorzec jest użycie parametrów zapytania reguł do zwraca strukturę nawigacji aspektowej wraz z wyników zestawu reguł, a także wyrażenie $filter.  Wyrażenie filtru obsługuje zdarzenie kliknięcia na wartość zestawu reguł. Traktować `$filter` zwracane wyrażenie jako kod związany z rzeczywistych przycinania wyników wyszukiwania do warstwy prezentacji. Biorąc pod uwagę zestaw reguł kolorów, klikając kolor czerwony jest implementowane za pomocą `$filter` wyrażenia, który wybiera tylko te elementy, które mają kolor czerwony. 

### <a name="query-basics"></a>Podstawowe informacje o zapytań

W usłudze Azure Search, żądanie jest określony za pomocą co najmniej jeden parametr zapytania (zobacz [wyszukiwania dokumentów](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) opis każdej z nich). Brak parametrów zapytania są wymagane, ale konieczne jest posiadanie co najmniej jeden w kolejności dla zapytania był prawidłowy.

Dokładność, rozumieć możliwość odfiltrować znaczenia trafień odbywa się za pośrednictwem co najmniej jeden z tych wyrażeń:

-   **search=**  
    Wartość tego parametru, stanowi wyrażenie wyszukiwania. Może to być pojedynczy tekst lub wyrażenie złożone wyszukiwania, które zawiera wiele warunków i operatorów. Na serwerze wyrażenie wyszukiwania jest używana do wyszukiwania pełnotekstowego, wykonywanie zapytań pola z możliwością wyszukiwania w indeksie dopasowanie postanowień dotyczących zwracanie wyników według rangi. Jeśli ustawisz `search` na wartość null, zapytanie wykonywania umieszczeniu całego indeksu (czyli `search=*`). W tym przypadku inne elementy zapytania, takie jak `$filter` czy profilu oceniania głównych czynników wpływających na dokumenty, które są zwracane `($filter`) i w jakiej kolejności (`scoringProfile` lub `$orderby`).

-   **$filter =**  
    Filtr jest zaawansowany mechanizm ograniczania rozmiaru wyniki wyszukiwania oparte na wartości atrybutów określonego dokumentu. Element `$filter` jest stosowana jako pierwsza, a następnie tworzenia kategorii wyszukanych informacji logiki, która generuje dostępne wartości i odpowiedniej liczby dla każdej wartości

Wyrażeń złożonych wyszukiwań zmniejszenie wydajności zapytania. W przypadku, gdy jest to możliwe, należy użyć wyrażenia filtru dobrze skonstruowany, zwiększyć dokładność i poprawić wydajność zapytań.

Aby lepiej zrozumieć, jak filtr dodaje większą dokładność, porównaj wyrażenia złożone na taki, który zawiera wyrażenie filtru:

-   `GET /indexes/hotel/docs?search=lodging budget +Seattle –motel +parking`
-   `GET /indexes/hotel/docs?search=lodging&$filter=City eq ‘Seattle’ and Parking and Type ne ‘motel’`

Oba zapytania są prawidłowe, a w drugim wyższego poziomu, jeśli potrzebujesz innego niż motele parkowania w Seattle.
-   Pierwsze zapytanie opiera się na tych konkretnych słów, jest wymienione lub nie jest wymieniony w ciągu pól, takich jak nazwa, opis i inne pola zawierające dane z możliwością wyszukiwania.
-   Drugie zapytanie szuka dokładne dopasowanie danych strukturalnych i może być znacznie bardziej dokładne.

W aplikacji, które obejmują nawigacji aspektowej upewnij się, że każda akcja użytkownika za pośrednictwem strukturę nawigacji aspektowej towarzyszy zawęzić wyniki wyszukiwania. Aby zawęzić wyniki, należy użyć wyrażenia filtru.

<a name="howtobuildit"></a>

## <a name="build-a-faceted-navigation-app"></a>Tworzenie aplikacji Nawigacja aspektowa
Nawigacji aspektowej z usługą Azure Search jest implementowana w kodzie aplikacji, która tworzy żądanie wyszukiwania. Nawigacja aspektowa opiera się na elementy w schemacie zdefiniowanego wcześniej.

Wstępnie zdefiniowane w wyszukiwaniu indeks jest `Facetable [true|false]` atrybutu indeksu, ustaw na wybrane pola, aby włączyć lub wyłączyć ich użycie w strukturze nawigacji aspektowej. Bez `"Facetable" = true`, pola nie można używać w nawigacji zestawu reguł.

Warstwa prezentacji w kodzie zawiera środowisko użytkownika. Należy go listę elementów składowych nawigacji aspektowej, takie jak etykiety, wartości, pola wyboru i liczba. Interfejs API REST usługi Azure Search jest niezależny od platformy, dlatego używanie dowolnych języków i platform, które chcesz. Ważne jest, aby uwzględnić obsługuje odświeżania przyrostowego, za pomocą zaktualizowany stan interfejsu użytkownika, ponieważ wybrano każdy aspekt dodatkowe elementy interfejsu użytkownika. 

Podczas przeszukiwania, kod aplikacji tworzy żądanie zawiera `facet=[string]`, parametr żądania, który zawiera pole do zestawu reguł przez. Zapytanie może mieć wiele zestawów reguł, takich jak `&facet=color&facet=category&facet=rating`, każdy z nich rozdzielone handlowe "i" (&) znaków.

Kod aplikacji musi także skonstruować `$filter` wyrażenia do obsługi zdarzeń kliknięcia w nawigacji aspektowej. A `$filter` ogranicza wyniki wyszukiwania przy użyciu wartości aspektu jako kryteria filtrowania.

Usługa Azure Search zwraca wyniki wyszukiwania, w oparciu o jeden lub więcej warunków, które należy wprowadzić, wraz z aktualizacji w strukturze nawigacji aspektowej. W usłudze Azure Search nawigacji aspektowej składa się z jednym poziomie, przy użyciu wartości aspektu i zlicza, ile wyników znajdują się dla każdej z nich.

W poniższych sekcjach podejmujemy dokładniej poznać sposób tworzenia każdej części.

<a name="buildindex"></a>

## <a name="build-the-index"></a>Tworzenie indeksu
Wielopoziomowe jest włączona na podstawie pól pola w indeksie, za pośrednictwem tego atrybutu indeksu: `"Facetable": true`.  
Wszystkie typy pól, które prawdopodobnie mogłyby zostać użyte w nawigacji aspektowej są `Facetable` domyślnie. Typy takie pola zawierają `Edm.String`, `Edm.DateTimeOffset`oraz wszystkich typów pola numerycznego (zasadniczo wszystkie typy pól są tworzenia aspektów, z wyjątkiem `Edm.GeographyPoint`, nie można używać w nawigacji aspektowej). 

Podczas tworzenia indeksu, najlepszym rozwiązaniem dla nawigacji aspektowej jest jawnie wyłączyć tworzenia kategorii wyszukanych informacji dla pól, które nigdy nie powinny być używane jako zestaw reguł.  W szczególności pól ciągów dla wartości pojedynczego wystąpienia, takich jak identyfikator lub nazwa produktu, powinna być równa `"Facetable": false` przed ich użyciem przypadkowym (i nieskuteczne) w nawigacji aspektowej. Włączanie tworzenia kategorii wyszukanych informacji poza gdzie nie są potrzebne pomaga utrzymać mały rozmiar indeksu i zwykle zwiększa wydajność.

Poniżej przedstawiono część schematu dla przykładowej aplikacji zadanie portalu w wersji demonstracyjnej, spacje niektórych atrybutów, aby zmniejszyć rozmiar:

```json
{
  ...
  "name": "nycjobs",
  "fields": [
    { “name”: "id",                 "type": "Edm.String",              "searchable": false, "filterable": false, ... "facetable": false, ... },
    { “name”: "job_id",             "type": "Edm.String",              "searchable": false, "filterable": false, ... "facetable": false, ... },
    { “name”: "agency",              "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "posting_type",        "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "num_of_positions",    "type": "Edm.Int32",              "searchable": false, "filterable": true, ...  "facetable": true, ...  },
    { “name”: "business_title",      "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "civil_service_title", "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "title_code_no",       "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "level",               "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "salary_range_from",   "type": "Edm.Int32",              "searchable": false, "filterable": true, ...  "facetable": true, ...  },
    { “name”: "salary_range_to",     "type": "Edm.Int32",              "searchable": false, "filterable": true, ...  "facetable": true, ...  },
    { “name”: "salary_frequency",    "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "work_location",       "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
…
    { “name”: "geo_location",        "type": "Edm.GeographyPoint",     "searchable": false, "filterable": true, ...  "facetable": false, ... },
    { “name”: "tags",                "type": "Collection(Edm.String)", "searchable": true,  "filterable": true, ...  "facetable": true, ...  }
  ],
…
}
```

Jak widać w schemacie przykładowe `Facetable` jest wyłączona w przypadku pól ciągów, które nie powinny być używane jako aspekty, takie jak wartości Identyfikatora. Włączanie tworzenia kategorii wyszukanych informacji poza gdzie nie są potrzebne pomaga utrzymać mały rozmiar indeksu i zwykle zwiększa wydajność.

> [!TIP]
> Najlepszym rozwiązaniem jest obejmują pełny zestaw atrybutów indeksu dla każdego pola. Mimo że `Facetable` jest domyślnie włączone dla prawie wszystkich pól, celowo ustawienie każdego atrybutu może pomóc w przemyśleniu skutki każdej decyzji schematu. 

<a name="checkdata"></a>

## <a name="check-the-data"></a>Sprawdź dane
Jakość danych ma bezpośredni wpływ na tego, czy strukturę nawigacji aspektowej materializuje zgodnie z oczekiwaniami na. Wpływa również na łatwość tworzenia filtry, aby zmniejszyć zestaw wyników.

Jeśli chcesz aspekt przez markę lub cena każdego dokumentu może zawierać wartości *Nazwa_witryny_zbiorczej* i *ProductPrice* , które są prawidłowe, zgodne i wydajnie pracować jako opcji filtrowania.

Poniżej przedstawiono kilka przypomnienia o co czyszczenie dla:

* Dla każdego pola, którą chcesz aspekt, spróbuj odpowiedzieć sobie czy zawiera on wartości, które są odpowiednie, jako filtry w wyszukiwanie kierunkowane samodzielnie przez użytkowników. Wartości powinny być krótkie, opisu i dostatecznie wyróżniające się oferują możliwość wyboru między opcjami konkurujących.
* Błędy pisowni lub prawie pasujących wartości. Jeśli reguł kolorów i wartości pól zawierają firmami Orange i Ornage (błąd), zestaw reguł na podstawie pola koloru czy wczytać obu.
* Mieszanej wielkości liter tekstu można też zrobić spustoszenie w nawigacji aspektowej, z firmami orange i pomarańczowy występujących w dwóch różnych wartości. 
* Pojedynczy, jak i mnogich wersji tej samej wartości może spowodować oddzielny zestaw reguł dla każdego.

Jak możesz sobie wyobrazić, starannością w ramach przygotowywania danych jest ważnym aspektem skutecznego nawigacji aspektowej.

<a name="presentationlayer"></a>

## <a name="build-the-ui"></a>Tworzenie interfejsu użytkownika
Pracy powrót po awarii z warstwy prezentacji może pomóc odkryć wymagania, które mogły pominięte, w przeciwnym razie oraz wiedzieć, jakie funkcje są niezbędne do środowiska wyszukiwania.

Pod względem nawigacji aspektowej strony sieci web lub aplikacja wyświetla strukturę nawigacji aspektowej, wykrywa dane wejściowe użytkownika na stronie i wstawia zmienione elementy. 

Dla aplikacji sieci web AJAX jest często używany w Warstwa prezentacji, ponieważ umożliwia odświeżanie zmiany przyrostowe. Można także użyć programu ASP.NET MVC lub jakiejkolwiek innej platformie wizualizacji, które można podłączyć do usługi Azure Search przy użyciu protokołu HTTP. Przykładowa aplikacja w tym artykule **pokaz Portal zadania wyszukiwania dla platformy Azure** — jako aplikację ASP.NET MVC.

W tym przykładzie nawigacji aspektowej jest wbudowana w stronę wyników wyszukiwania. Poniższy przykład, pobrany z `index.cshtml` strona wyników pliku przykładowej aplikacji, struktura pokazuje statyczny kod HTML do wyświetlania nawigację aspektową w wyszukiwaniu. Lista zestawów reguł zostanie skompilowany lub ponownie skompilowany, dynamicznie, gdy użytkownik przesyła wyszukiwany termin, lub zaznacz lub wyczyść zestaw reguł.

```html
<div class="widget sidebar-widget jobs-filter-widget">
  <h5 class="widget-title">Filter Results</h5>
    <p id="filterReset"></p>
    <div class="widget-content">

      <h6 id="businessTitleFacetTitle">Business Title</h6>
      <ul class="filter-list" id="business_title_facets">
      </ul>

      <h6>Location</h6>
      <ul class="filter-list" id="posting_type_facets">
      </ul>

      <h6>Posting Type</h6>
      <ul class="filter-list" id="posting_type_facets"></ul>

      <h6>Minimum Salary</h6>
      <ul class="filter-list" id="salary_range_facets">
      </ul>

  </div>
</div>
```

Poniższy fragment kodu z `index.cshtml` stronę dynamicznie tworzy kod HTML do wyświetlenia pierwszy zestaw reguł biznesowych tytuł. Podobne funkcje dynamicznie tworzenie kodu HTML dla innych zestawów reguł. Każdy aspekt ma etykietę i liczbę, która wyświetla liczbę elementów, znaleziono wynik reguł.

```js
function UpdateBusinessTitleFacets(data) {
  var facetResultsHTML = '';
  for (var i = 0; i < data.length; i++) {
    facetResultsHTML += '<li><a href="javascript:void(0)" onclick="ChooseBusinessTitleFacet(\'' + data[i].Value + '\');">' + data[i].Value + ' (' + data[i].Count + ')</span></a></li>';
  }

  $("#business_title_facets").html(facetResultsHTML);
}
```

> [!TIP]
> Podczas projektowania na stronie wyników wyszukiwania, pamiętaj, aby dodać mechanizm wyczyszczenie aspektami. Jeśli dodasz pola wyboru, możesz łatwo dowiedzieć się, jak można wyczyścić filtry. Inne układy może być konieczne wzorca nawigacji lub innym podejściem creative. Na przykład, w portalu do wyszukiwania zadania przykładowej aplikacji, można kliknąć `[X]` po wybrany zestaw reguł można wyczyścić aspekt.

<a name="buildquery"></a>

## <a name="build-the-query"></a>Utwórz zapytanie
Kod, który możesz napisać do tworzenia zapytania należy określić wszystkie części prawidłowe zapytanie, w tym wyrażeniach wyszukiwania, aspektami, filtry i oceniania profile — wszystko umożliwia Sformułuj żądanie. W tej sekcji omówimy, kiedy warto korzystać z zestawów reguł do zapytania i używania filtrów z aspektami dostarczać zestaw wyników mniejsze.

Należy zauważyć, że zestawy reguł są integralną częścią, w tej przykładowej aplikacji. Środowisko wyszukiwania w wersji demonstracyjnej Portal zadania został opracowany nawigacji aspektowej i filtry. Położenie wyraźną nawigacji aspektowej, na stronie pokazuje jego znaczenie. 

Przykładem jest często dobrym miejscem do rozpoczęcia. Poniższy przykład, pobrany z `JobsSearch.cs` pliku kompilacji żądania, który tworzy zestaw reguł nawigacji bazujących na tytuł biznesowych, lokalizacji, typu publikowanie i wynagrodzenia co najmniej. 

```cs
SearchParameters sp = new SearchParameters()
{
  ...
  // Add facets
  Facets = new List<String>() { "business_title", "posting_type", "level", "salary_range_from,interval:50000" },
};
```

Parametr zapytania reguł jest ustawiona na pole, a w zależności od typu danych, może być dodatkowo sparametryzowany przez rozdzielana przecinkami lista, która obejmuje `count:<integer>`, `sort:<>`, `interval:<integer>`, i `values:<list>`. Lista wartości jest obsługiwana dla danych liczbowych, podczas konfigurowania zakresów. Zobacz [wyszukiwania dokumentów (Azure Search API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) szczegóły użycia.

Wraz z aspektami żądanie formułować przez aplikację powinno również tworzyć filtry, aby zawęzić zestaw dokumentów kandydat na podstawie wybranych wartości aspektu. W sklepie rowerów, nawigacji aspektowej udostępnia wskazówki na pytania, takie jak *jakie kolory, producentów i typy rowery są dostępne?* . Filtrowanie odpowiedzi na pytania, takie jak *które dokładnie rowery są oznaczone kolorem czerwonym, rowery górskie, w tym cena zakres?* . Po kliknięciu przycisku "Red", aby wskazać, czy mają być wyświetlane tylko produktów Red następne zapytanie, aplikacja wysyła obejmuje `$filter=Color eq ‘Red’`.

Poniższy fragment kodu z `JobsSearch.cs` strony dodaje wybrany tytuł biznesowych do filtru, po wybraniu wartości z zestawu reguł biznesowych tytuł.

```cs
if (businessTitleFacet != "")
  filter = "business_title eq '" + businessTitleFacet + "'";
```

<a name="tips"></a> 

## <a name="tips-and-best-practices"></a>Wskazówki i najlepsze rozwiązania

### <a name="indexing-tips"></a>Wskazówki dotyczące indeksowania
**Zwiększ wydajność indeksu, jeśli nie korzystasz z pola wyszukiwania**

Jeśli aplikacja używa nawigacji aspektowej wyłącznie (czyli nie pola wyszukiwania), można oznaczyć jako `searchable=false`, `facetable=true` do wygenerowania indeksu bardziej zwarty. Ponadto indeksowania odbywa się tylko na wartości całego zestawu reguł, nie dzielenia wyrazów lub indeksowania składniki wartości wielu słów.

**Określ pola, które mogą być używane jako zestawy reguł**

Pamiętaj, że schemat indeksu określa pola, które są dostępne do użycia jako zestaw reguł. Przy założeniu, że pole jest tworzenie aspektów, zapytanie określa, które pola do zestawu reguł przez. Pola, według której jesteś tworzenia kategorii wyszukanych informacji zawiera wartości, które pojawiają się pod etykietą. 

Wartości, które są wyświetlane w obszarze każdej etykiety są pobierane z indeksu. Na przykład, jeśli pole zestawu reguł jest *kolor*, dostępne w celu filtrowania dodatkowych wartości są wartościami dla tego pola - czerwony, Black i tak dalej.

Do liczbowych i daty/godziny tylko wartości, można jawnie ustawić wartości dla pola zestaw reguł (na przykład `facet=Rating,values:1|2|3|4|5`). Lista wartości jest dozwolony dla tych typów pól ułatwiają oddzielenie aspekt wyniki do zakresów (albo na podstawie wartości liczbowych lub okresów zakresy). 

**Domyślnie możesz mieć tylko jeden poziom Nawigacja aspektowa** 

Jak wspomniano, istnieje bezpośrednią obsługę zagnieżdżania zestawów reguł w hierarchii. Domyślnie Nawigacja aspektowa w usłudze Azure Search obsługuje tylko jeden poziom filtrów. Jednak istnieją obejścia. Możesz zakodować strukturze hierarchicznej aspekt `Collection(Edm.String)` jeden wpis punktu danej hierarchii. Zastosowanie tego obejścia wykracza poza zakres tego artykułu. 

### <a name="querying-tips"></a>Porady dotyczące zapytań
**Sprawdzanie poprawności pól**

W przypadku tworzenia listy aspektami dynamicznie oparte na niezaufane dane użytkowników, należy zweryfikować, że nazwy pól aspektowa są prawidłowe. Lub znak ucieczki nazwy, podczas tworzenia adresów URL przy użyciu `Uri.EscapeDataString()` platformie .NET lub odpowiedniej wartości wyrażonej w wybraną platformę.

### <a name="filtering-tips"></a>Wskazówki dotyczące filtrowania
**Zwiększ dokładność wyszukiwania za pomocą filtrów**

Użyj filtrów. Jeśli korzystasz tylko wyrażeń wyszukiwania samodzielnie, analiza słowotwórcza może spowodować, że dokument ma zostać zwrócona, że nie znajduje się wartość aspektu dokładne w jednym z jej pól.

**Zwiększyć wydajność wyszukiwania przy użyciu filtrów**

Filtry zawęzić zestaw dokumentów Release candidate wyszukiwania i wyłączyć je z klasyfikacji. Jeśli masz duży zbiór dokumentów, często za pomocą selektywne aspekt Przechodzenie do szczegółów zapewnia lepszą wydajność.
  
**Filtruj tylko pola aspektowa**

W aspektowa Przechodzenie do szczegółów zazwyczaj chcesz obejmujący tylko dokumenty zawierające wartość aspektu w określonym polu (aspektowa), nie w dowolnym miejscu na wszystkie pola z możliwością wyszukiwania. Dodawanie filtru wzmacnia pola docelowego, kierując usługi wyszukiwanie tylko w aspektowa pole o pasującej wartości.

**TRIM aspekt wyniki za pomocą więcej filtrów**

Aspekt wyniki są znalezionych w wynikach wyszukiwania, które odpowiadają okres zestaw reguł dokumentów. W poniższym przykładzie, w wynikach wyszukiwania dla *chmury obliczeniowej*, 254 elementy mają także *wewnętrznego specyfikacji* jako typu zawartości. Elementy nie są zawsze wzajemnie się wykluczają. Jeśli element nie spełnia kryteria obu filtrów, jest liczony w każdej z nich. Ta duplikacja, jest możliwe, podczas tworzenia kategorii wyszukanych informacji na `Collection(Edm.String)` pól, które są często używane do implementowania znakowanie dokumentu.

        Search term: "cloud computing"
        Content type
           Internal specification (254)
           Video (10) 

Ogólnie rzecz biorąc Jeśli okaże się, że wyniki aspekt spójnie jest zbyt duża, zalecamy, aby dodać więcej filtrów umożliwiają użytkownikom więcej opcji zawężaniu zakresu wyszukiwania.

### <a name="tips-about-result-count"></a>Porady dotyczące liczby wyników

**Ogranicz liczbę elementów w nawigacji zestawu reguł**

Dla każdego pola aspektową w drzewie nawigacji ma domyślny limit maksymalnej wartości 10. To ustawienie domyślne sens struktury nawigacji, ponieważ utrzymuje listę wartości do rozmiaru w zarządzaniu. Można zastąpić domyślną przez przypisywanie wartości do zliczenia.

* `&facet=city,count:5` Określa, że tylko pięć pierwszych miast znalezione w prawym górnym pozycjonowane wyniki są zwracane w wyniku tego zestawu reguł. Należy wziąć pod uwagę przykładowe zapytanie za pomocą wyszukiwany termin "Kuwejcie" i 32 dopasowań. Jeśli zapytanie określa `&facet=city,count:5`, tylko pierwsze pięć unikatowy miasta z najbardziej dokumentów w wynikach wyszukiwania są uwzględniane w wynikach zestawu reguł.

Zwróć uwagę rozróżnienie między aspekt wyniki i wyniki wyszukiwania. Wyniki wyszukiwania są wszystkie dokumenty, które pasujących do zapytania. Aspekt wyniki są dopasowań dla każdej wartości zestawu reguł. W tym przykładzie wyniki wyszukiwania zawierają nazwy miast, które nie są na liście reguł klasyfikacji (5 w naszym przykładzie). Wyniki, które są odfiltrowywane za pośrednictwem nawigacji aspektowej, stają się widoczne, gdy wyczyść aspekty, lub wybierz inne aspekty oprócz miasta. 

> [!NOTE]
> Omawianie `count` gdy istnieje więcej niż jeden typ może być mylące. Poniższa tabela zawiera krótkie podsumowanie sposobu używania termin w interfejsu API usługi Azure Search, przykładowy kod i dokumentację. 

* `@colorFacet.count`<br/>
  W kodzie prezentacji parametru liczba powinny być widoczne na zestawu reguł, używany do wyświetlania liczby wyników aspekt. W wynikach aspekt licznik wskazuje liczbę dokumentów, które odpowiadają na okres zestaw reguł lub zakresu.
* `&facet=City,count:12`<br/>
  W zapytaniu zestawu reguł można ustawić liczbę na wartość.  Wartość domyślna wynosi 10, ale można ją ustawić wyższą lub niższą. Ustawienie `count:12` pobiera pierwszych 12 jest zgodna w wynikach aspekt, liczby dokumentów.
* "`@odata.count`"<br/>
  W odpowiedzi na zapytanie ta wartość wskazuje liczbę pasujących elementów w wynikach wyszukiwania. Średnio jest większa niż suma wszystkich wyników aspekt połączone z powodu obecności elementy, które pasują do szukanego terminu, ale mieć żadnych dopasowań wartość zestawu reguł.

**Pobieranie liczby w wynikach zestawu reguł**

Po dodaniu filtru do zapytania aspektowej, warto zachować instrukcji zestawu reguł (na przykład `facet=Rating&$filter=Rating ge 4`). Z technicznego punktu widzenia aspekt = ocena nie jest wymagane, ale jej zachowanie zwraca liczbę wartości reguł klasyfikacji, 4 lub nowszy. Na przykład kliknięcie przycisku "4" i kwerenda zawiera filtr większa lub równa "4", liczba są zwracane dla każdej klasyfikacji, które wynosi 4 lub nowszy.  

**Upewnij się, że możesz uzyskać dokładne aspekt liczby**

W pewnych okolicznościach może się okazać aspekt liczby nie są zgodne z zestawem wyników (zobacz [nawigację Aspektową w usłudze Azure Search (wpis na forum)](https://social.msdn.microsoft.com/Forums/azure/06461173-ea26-4e6a-9545-fbbd7ee61c8f/faceting-on-azure-search?forum=azuresearch)).

Liczba reguł może być niedokładny ze względu na architecture dzielenie na fragmenty. Każdy indeks wyszukiwania ma wiele fragmentów i każdy fragment raporty najważniejsze aspekty N, liczby dokumentów, które następnie są łączone w pojedynczy wynik. Jeśli niektóre fragmenty ma wiele pasujących wartości, podczas gdy inne osoby będą mieć mniej, może się okazać że niektóre wartości aspektu brakuje lub w obszarze zliczane w wynikach.

Mimo że to zachowanie można zmienić w dowolnym momencie, jeśli wystąpi ten problem już dziś, można obejść go przez sztucznie pompowania liczba:\<numer > do dużej liczby, aby wymusić pełną raportowania z każdego fragmentu. Jeśli wartość licznika: jest większe niż lub równa liczbie unikatowych wartości w polu, masz gwarancję dokładne wyniki. Jednak w przypadku wysokiej liczbie dokumentów jest spadek wydajności, więc rozsądnie Użyj tej opcji.

### <a name="user-interface-tips"></a>Wskazówki dotyczące interfejsu użytkownika
**Dodaj etykiety dla każdego pola w nawigacji zestawu reguł**

Etykiety są zazwyczaj definiowane w formularzu lub HTML (`index.cshtml` w przykładowej aplikacji). Brak żadnych interfejsów API w usłudze Azure Search dla etykiet nawigacji zestawu reguł lub inne metadane.

<a name="rangefacets"></a>

## <a name="filter-based-on-a-range"></a>Filtr oparty na zakresie
Wielopoziomowe za pośrednictwem zakresów wartości jest typowym wymogiem aplikacji wyszukiwania. Zakresy są obsługiwane w przypadku danych numerycznych oraz wartości daty/godziny. Możesz dowiedzieć się więcej o każde podejście w [wyszukiwania dokumentów (Azure Search API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).

Usługa Azure Search ułatwia konstrukcji zakresu, zapewniając dwa podejścia do przetwarzania danych w zakresie. W przypadku obu metod usługi Azure Search tworzy odpowiednie zakresy podane dane wejściowe podane. Na przykład w przypadku określenia wartości z zakresu od 10 | 20 | 30, automatycznie tworzy zakresów 0-10, 10-20, 20 – 30. Aplikację można opcjonalnie usunąć wszelkie interwałów, które są puste. 

**Podejście 1: Użyj parametru interwału**  
Aby ustawić cenę aspekty w przyrostach co 10 USD, należy określić: `&facet=price,interval:10`

**Podejście 2: Użyj listy wartości**  
Dla danych liczbowych używając listy wartości.  Należy wziąć pod uwagę zakres aspekt `listPrice` pola renderowany w następujący sposób:

  ![Lista wartości przykładów](media/search-faceted-navigation/Facet-5-Prices.PNG "przykładowej wartości listy")

Aby określić zakres aspekt, podobne do pokazanego na poprzednim zrzucie ekranu, użyj listy wartości:

    facet=listPrice,values:10|25|100|500|1000|2500

Każdy z zakresów powstała przy użyciu 0 jako punktu wyjścia wartość z listy jako punkt końcowy, a następnie przycinana poprzedniej zakresu, należy utworzyć osobne odstępach czasu. Usługa Azure Search wykonuje te czynności jako część nawigacji aspektowej. Nie trzeba napisać kod dla każdego interwału struktury.

### <a name="build-a-filter-for-a-range"></a>Filtr zakresu kompilacji
Aby filtrować na podstawie zakresu z wybraniu dokumentów, można użyć `"ge"` i `"lt"` operatorów w wyrażeniu legalną dwuczęściową, który definiuje punkty końcowe zakresu filtru. Na przykład, jeśli wybierzesz zakres 10-25 dla `listPrice` polu Filtr byłby `$filter=listPrice ge 10 and listPrice lt 25`. W przykładowym kodzie używa wyrażenia filtru **priceFrom** i **priceTo** parametrów umożliwiających ustawianie punktów końcowych. 

  ![Zapytanie o zakres wartości](media/search-faceted-navigation/Facet-6-buildfilter.PNG "zapytania dla zakresu wartości")

<a name="geofacets"></a> 

## <a name="filter-based-on-distance"></a>Filtr oparty na odległości
Typowe Aby wyświetlić filtry ułatwiające wybór magazynu, restauracji lub oparte na swoją bliskość Twojej bieżącej lokalizacji docelowej. Filtr tego typu może wyglądać jak nawigacja aspektowa, ale jest tylko filtr. Mamy tu wymienić dla osób, które konkretnie szukasz implementacji porady dotyczące problemu tego konkretnego projektu.

Istnieją dwa funkcji Geoprzestrzennych w usłudze Azure Search **geo.distance** i **geo.intersects**.

* **Geo.distance** funkcja zwraca odległość w kilometrach między dwoma punktami. Jeden punkt jest polem, a drugi jest stałą przekazanych jako część filtru. 
* **Geo.intersects** funkcja zwraca wartość true, jeśli dany punkt znajduje się w danym wielokąta. Punkt znajduje się pole, a następnie Wielokąt jest określony jako stała lista przekazanych jako część filtru współrzędnych.

Można znaleźć przykłady filtrów w [składnia wyrażenia OData (Azure Search)](query-odata-filter-orderby-syntax.md).

<a name="tryitout"></a>

## <a name="try-the-demo"></a>Wypróbuj wersję demonstracyjną
Pokaz portalu Azure wyszukiwania zadania zawiera przykłady przywołanej w niniejszym artykule.

-   Obejrzeć i przetestować pokaz pracy w [usługi Azure Search zadania Portal Demo](https://azjobsdemo.azurewebsites.net/).

-   Pobierz kod ze [repozytorium przykładów dla platformy Azure w serwisie GitHub](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs).

Podczas pracy z wyników wyszukiwania, obejrzyj adres URL dla zmian w budowa zapytania. Ta aplikacja stanie się Dołącz zestawów reguł do identyfikatora URI, zgodnie z wybraniu każdej z nich.

1. Aby korzystać z funkcji mapowania na aplikacja demonstracyjna, uzyskiwanie klucza usługi mapy Bing z [Centrum deweloperów map Bing](https://www.bingmapsportal.com/). Wklej go w miejsce istniejącego klucza w `index.cshtml` strony. `BingApiKey` Ustawienie w `Web.config` plik nie jest używany. 

2. Uruchom aplikację. Opcjonalne prezentację lub zamknąć okno dialogowe.
   
3. Wprowadź termin wyszukiwania, takie jak "analityka" i kliknij ikonę wyszukiwania. Zapytanie wykonuje szybko.
   
   Strukturę nawigacji aspektowej jest także zwracany w wynikach wyszukiwania. Na stronie wyników wyszukiwania strukturę nawigacji aspektowej obejmuje liczniki dla każdego aspektu wyniku. Nie zestawu reguł są zaznaczone, dzięki czemu zwracane są wszystkie pasujących wyników.
   
   ![Wyniki wyszukiwania przed wybraniem aspektami](media/search-faceted-navigation/faceted-search-before-facets.png "wyniki wyszukiwania przed wybraniem zestawów reguł")

4. Kliknij tytuł biznesowych, lokalizacji lub minimalna wynagrodzenia. Aspekty zostały null początkowa wyszukiwania, ale jak przyjmują wartości wyniki wyszukiwania są usuwane z elementów, które nie odpowiadają.
   
   ![Wyniki wyszukiwania, po wybraniu aspektami](media/search-faceted-navigation/faceted-search-after-facets.png "wyniki wyszukiwania, po wybraniu zestawu reguł")

5. Aby wyczyścić aspektowa zapytanie tak, aby spróbować zachowania inne zapytanie, kliknij `[X]` po wybranych zestawów reguł do wyczyszczenia aspektami.
   
<a name="nextstep"></a>

## <a name="learn-more"></a>Dowiedz się więcej
Obejrzyj [szczegółowe dane w usłudze Azure Search](https://channel9.msdn.com/Events/TechEd/Europe/2014/DBI-B410). Na 45:25 istnieje pokaz implementowania aspektami.

Aby uzyskać więcej szczegółowych informacji w zasadach projektowania nawigacji aspektowej zaleca się następujące łącza:

* [Wzorce projektowe: Nawigacja aspektowa](https://alistapart.com/article/design-patterns-faceted-navigation)
* [Fronton kwestie podczas implementowania wyszukiwanie Aspektowe — część 1 ](https://articles.uie.com/faceted_search2/)

