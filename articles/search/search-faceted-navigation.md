---
title: Jak wdrożyć nawigację aspektową w hierarchii kategorii — Azure Search
description: Dodaj nawigację aspektów do aplikacji, które integrują się z Azure Search, hostowanej usługi wyszukiwania w chmurze w Microsoft Azure.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 8e325abf1f58458d2fa035c8c8f081173efb0e65
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2019
ms.locfileid: "69649891"
---
# <a name="how-to-implement-faceted-navigation-in-azure-search"></a>Jak wdrożyć nawigację aspektową w usłudze Azure Search
Nawigacja aspektowa jest mechanizmem filtrowania, który zapewnia samodzielną nawigację rozwijaną w aplikacjach wyszukiwania. Termin "Nawigacja aspektowa" może być nieznajomy, ale prawdopodobnie był wcześniej używany. Jak pokazano na poniższym przykładzie, Nawigacja aspektowa nie jest większa niż kategorie używane do filtrowania wyników.

 ![Demonstracja portalu zadań Azure Search](media/search-faceted-navigation/azure-search-faceting-example.png "Demonstracja portalu zadań Azure Search")

Nawigacja aspektowa jest alternatywnym punktem wejścia do przeszukiwania. Oferuje wygodną alternatywę do pisania złożonych wyrażeń wyszukiwania. Zestawy reguł mogą pomóc w znalezieniu tego, czego szukasz, przy jednoczesnym zapewnieniu braku wyników. Jako deweloper, zestawy reguł umożliwiają uwidocznienie najbardziej przydatnych kryteriów wyszukiwania dla nawigowania po indeksie wyszukiwania. W przypadku aplikacji sieci Web w trybie online Nawigacja aspektów jest często tworzona w oparciu o marki, działy (buty), rozmiar, cenę, popularność i klasyfikacje. 

Wdrożenie nawigacji aspektowej różni się w różnych technologiach wyszukiwania. W Azure Search Nawigacja aspektowa jest tworzona w czasie zapytania przy użyciu pól, które wcześniej zostały przypisane do schematu.

-   W zapytaniach tworzonych przez aplikację zapytanie musi wysyłać *parametry zapytania zestawu reguł* , aby uzyskać dostępne wartości filtru aspektów dla tego zestawu wyników dokumentu.

-   Aby faktycznie przyciąć zestaw wyników dokumentu, aplikacja musi również zastosować wyrażenie `$filter`.

Podczas tworzenia aplikacji pisanie kodu, który tworzy zapytania, stanowi zbiorczą część pracy. Wiele zachowań aplikacji, które można oczekiwać od nawigacji aspektowej, jest udostępnianych przez usługę, w tym wbudowana obsługa definiowania zakresów i pobierania liczb dla wyników aspektów. Usługa zawiera również rozsądne wartości domyślne, które pomagają uniknąć nieporęczny struktur nawigacyjnych. 

## <a name="sample-code-and-demo"></a>Przykładowy kod i Demonstracja
W tym artykule jest na przykład wykorzystywany Portal wyszukiwania zadań. Przykład jest zaimplementowany jako aplikacja ASP.NET MVC.

-   Zobacz i przetestuj pokaz roboczy online w wersji [demonstracyjnej portalu zadań Azure Search](https://azjobsdemo.azurewebsites.net/).

-   Pobierz kod z [repozytorium Azure-Samples w witrynie GitHub](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs).

## <a name="get-started"></a>Rozpocznij
Jeśli dopiero zaczynasz opracowywanie aplikacji, najlepszym sposobem na podejście do nawigowania po aspektach jest wyświetlenie możliwości wyszukiwania z własnym kierowaniem. Jest to typ środowiska wyszukiwania przechodzenia do szczegółów na podstawie wstępnie zdefiniowanych filtrów, służący do szybkiego zawężania wyników wyszukiwania przez akcje typu punkt-kliknięcie. 

### <a name="interaction-model"></a>Model interakcji

Środowisko wyszukiwania dla nawigacji aspektowej jest iteracyjne, więc zacznijmy od poznania ich jako sekwencji zapytań, które unfold w odpowiedzi na działania użytkownika.

Punkt początkowy to Strona aplikacji, która zapewnia nawigację aspektową, zwykle umieszczaną na obwodzie. Nawigacja aspektowa jest często strukturą drzewa, z polami wyboru dla każdej wartości lub z tekstem, który można kliknąć. 

1. Zapytanie wysyłane do Azure Search określa strukturę nawigacyjną aspektów za pośrednictwem co najmniej jednego parametru zapytania zestawu reguł. Na przykład, zapytanie może zawierać `facet=Rating`, być może z `:values` lub `:sort` opcji, aby dokładniej udoskonalić prezentację.
2. Warstwa prezentacji renderuje stronę wyszukiwania, która zapewnia nawigację aspektową przy użyciu zestawów reguł określonych w żądaniu.
3. Uwzględniając strukturę nawigacyjną aspektów obejmującą klasyfikację, kliknij "4", aby wskazać, że mają być pokazywane tylko produkty z klasyfikacją 4 lub wyższą. 
4. W odpowiedzi aplikacja wysyła zapytanie zawierające `$filter=Rating ge 4` 
5. Warstwa prezentacji aktualizuje stronę, pokazując zredukowany zestaw wyników zawierający tylko te elementy, które spełniają nowe kryteria (w tym przypadku produkty klasyfikowane od 4 do góry).

Aspekt jest parametrem zapytania, ale nie jest mylić z danymi wejściowymi zapytania. Nigdy nie jest używana jako kryterium wyboru w zapytaniu. Zamiast tego należy traktować parametry zapytania zestawu jako dane wejściowe do struktury nawigacji, która wraca do odpowiedzi. Dla każdego podania parametru kwerendy zestawu reguł Azure Search oblicza, ile dokumentów znajduje się w częściowych wynikach dla każdej wartości aspektu.

Zwróć uwagę na `$filter` w kroku 4. Filtr jest ważnym aspektem nawigacji aspektowej. Chociaż aspekty i filtry są niezależne w interfejsie API, potrzebne są oba te funkcje. 

### <a name="app-design-pattern"></a>Wzorzec projektowy aplikacji

W kodzie aplikacji wzorzec ma używać parametrów zapytania zestawu, aby zwrócić strukturę nawigacyjną aspektów wraz z wynikami aspektów oraz wyrażeniem $filter.  Wyrażenie filtru obsługuje zdarzenie kliknięcia dla wartości aspektu. Wyrażenie `$filter` należy traktować jako kod związany z rzeczywistym przycinaniem wyników wyszukiwania do warstwy prezentacji. Mając zestaw reguł kolorów, kliknij kolor czerwony jest implementowany przez wyrażenie `$filter`, które wybiera tylko te elementy, które mają kolor czerwony. 

### <a name="query-basics"></a>Podstawowe informacje o zapytaniach

W Azure Search żądanie jest określone za pomocą co najmniej jednego parametru zapytania (zobacz [dokumentację wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) , aby zapoznać się z opisem każdego z nich). Żaden z parametrów zapytania nie jest wymagany, ale musisz mieć co najmniej jeden, aby zapytanie było prawidłowe.

Precyzja zrozumiała jako możliwość odfiltrowania nieistotnych trafień jest realizowana za pomocą jednego lub obu tych wyrażeń:

-   **Wyszukaj =**  
    Wartość tego parametru stanowi wyrażenie wyszukiwania. Może to być pojedynczy fragment tekstu lub złożone wyrażenie wyszukiwania, które zawiera wiele warunków i operatorów. Na serwerze wyrażenie wyszukiwania służy do wyszukiwania pełnotekstowego, zapytania do pól z możliwością wyszukiwania w indeksie w celu dopasowania warunków, zwracając wyniki w kolejności rangi. Jeśli ustawisz wartość `search` na null, wykonanie zapytania znajduje się nad całym indeksem (czyli `search=*`). W takim przypadku inne elementy zapytania, takie jak `$filter` lub profil oceniania, stanowią podstawowe czynniki wpływające na to, które dokumenty są zwracane `($filter`) i w jakiej kolejności (`scoringProfile` lub `$orderby`).

-   **$filter =**  
    Filtr jest zaawansowanym mechanizmem służącym do ograniczania rozmiaru wyników wyszukiwania na podstawie wartości określonych atrybutów dokumentu. @No__t_0 jest oceniane jako pierwszy, a następnie logiki aspektów generujących dostępne wartości i odpowiadające im zliczenia dla każdej wartości

Złożone wyrażenia wyszukiwania powodują zmniejszenie wydajności zapytania. Jeśli to możliwe, użyj dobrze skonstruowanych wyrażeń filtru, aby zwiększyć precyzję i zwiększyć wydajność zapytań.

Aby lepiej zrozumieć, jak filtr zwiększa precyzję, porównaj złożone wyrażenie wyszukiwania z jednym, które zawiera wyrażenie filtru:

-   `GET /indexes/hotel/docs?search=lodging budget +Seattle –motel +parking`
-   `GET /indexes/hotel/docs?search=lodging&$filter=City eq ‘Seattle’ and Parking and Type ne ‘motel’`

Oba zapytania są prawidłowe, ale druga jest najwyższa, jeśli szukasz Motels z parkami w Seattle.
-   Pierwsze zapytanie opiera się na tych konkretnych słowach, które są wymienione lub nie są wymienione w polach ciągów, takich jak nazwa, opis i inne pole zawierające dane, które można przeszukiwać.
-   Drugie zapytanie szuka dokładnych dopasowań danych strukturalnych i prawdopodobnie jest znacznie dokładniejsze.

W aplikacjach, które zawierają nawigację aspektową, upewnij się, że każde działanie użytkownika w strukturze nawigacji aspektowej towarzyszy zawężaniu wyników wyszukiwania. Aby zawęzić wyniki, użyj wyrażenia filtru.

<a name="howtobuildit"></a>

## <a name="build-a-faceted-navigation-app"></a>Tworzenie aplikacji nawigacyjnej aspektowej
Należy zaimplementować nawigację aspektową za pomocą Azure Search w kodzie aplikacji, która kompiluje żądanie wyszukiwania. Nawigacja aspektowa opiera się na elementach w schemacie zdefiniowanym wcześniej.

Wstępnie zdefiniowane w indeksie wyszukiwania jest atrybutem indeksu `Facetable [true|false]`, ustawionym dla wybranych pól, aby włączyć lub wyłączyć ich użycie w strukturze nawigacji aspektowej. Bez `"Facetable" = true` nie można używać pola w nawigacji aspektu.

Warstwa prezentacji w kodzie udostępnia środowisko użytkownika. Powinien on wystawić składniki nawigacji aspektowej, takie jak etykieta, wartości, pola wyboru i liczba. Interfejs API REST Azure Search jest platformą niezależny od, dlatego należy używać dowolnego języka i platformy. Ważną kwestią jest dołączenie elementów interfejsu użytkownika, które obsługują odświeżanie przyrostowe, z zaktualizowanym stanem interfejsu użytkownika w miarę zaznaczania każdego dodatkowego aspektu. 

W czasie zapytania, kod aplikacji tworzy żądanie, które zawiera `facet=[string]`, parametr żądania, który zawiera pole, według którego ma nawiązać aspekt. Zapytanie może mieć wiele aspektów, takich jak `&facet=color&facet=category&facet=rating`, każda z nich oddzielona znakiem handlowego "i" (&).

Kod aplikacji musi również utworzyć wyrażenie `$filter`, aby obsłużyć zdarzenia kliknięcia w nawigacji aspektowej. @No__t_0 zmniejsza wyniki wyszukiwania przy użyciu wartości aspektu jako kryterium filtru.

Azure Search zwraca wyniki wyszukiwania na podstawie co najmniej jednego wprowadzonego terminu oraz aktualizacji struktury nawigacji aspektowej. W Azure Search Nawigacja aspektowa jest jednopoziomową budową, z wartościami aspektów i liczbą wyników dla każdego z nich.

W poniższych sekcjach omówiono, jak skompilować każdą część.

<a name="buildindex"></a>

## <a name="build-the-index"></a>Kompiluj indeks
Aspektowanie jest włączone w zależności od pola w indeksie za pośrednictwem tego atrybutu indeksu: `"Facetable": true`.  
Wszystkie typy pól, które mogą być używane w nawigacji aspektowej, są domyślnie `Facetable`. Takie typy pól obejmują `Edm.String`, `Edm.DateTimeOffset` i wszystkie typy pól liczbowych (zasadniczo, wszystkie typy pól są elementami do, z wyjątkiem `Edm.GeographyPoint`, które nie mogą być używane w nawigacji aspektowej). 

Podczas kompilowania indeksu najlepszym rozwiązaniem dla nawigacji aspektowej jest jawne wyłączenie reguł dla pól, które nigdy nie powinny być używane jako zestaw reguł.  W szczególności pola ciągów dla pojedynczych wartości, takie jak identyfikator lub nazwa produktu, powinny być ustawione na `"Facetable": false`, aby zapobiec przypadkowemu (i nieskutecznym) użyciu w nawigacji aspektowej. Włączenie tworzenia aspektów, gdy nie jest to potrzebne, pozwala zachować rozmiar indeksu jako mały i zwykle poprawia wydajność.

Poniżej znajduje się część schematu przykładowej aplikacji demonstracyjnej portalu zadań, która została przycięta, aby zmniejszyć rozmiar:

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

Jak widać w przykładowym schemacie, `Facetable` jest wyłączone dla pól ciągów, które nie powinny być używane jako aspekty, takie jak wartości identyfikatorów. Włączenie tworzenia aspektów, gdy nie jest to potrzebne, pozwala zachować rozmiar indeksu jako mały i zwykle poprawia wydajność.

> [!TIP]
> Najlepszym rozwiązaniem jest uwzględnienie pełnego zestawu atrybutów indeksu dla każdego pola. Mimo że `Facetable` jest domyślnie włączona dla niemal wszystkich pól, to w celu ustawienia każdego atrybutu może pomóc w zawieszeniu się, jakie są konsekwencje każdej decyzji dotyczącej schematu. 

<a name="checkdata"></a>

## <a name="check-the-data"></a>Sprawdź dane
Jakość danych ma bezpośredni wpływ na to, czy struktura nawigacji aspektów materializuje się zgodnie z oczekiwaniami. Ma także wpływ na łatwość konstruowania filtrów, aby zmniejszyć zestaw wyników.

Jeśli chcesz zastosować zestaw reguł według marki lub ceny, każdy dokument powinien zawierać wartości dla *MarkName* i *ProductPrice* , które są prawidłowe, spójne i produktywne jako opcja filtru.

Poniżej przedstawiono kilka przypomnień o tym, co należy zrobić:

* Dla każdego pola, według którego chcesz nawiązać zestaw, Zaproponuj sobie, czy zawiera on wartości, które są odpowiednie jako filtry w odniesieniu do samodzielnego wyszukiwania. Wartości powinny być krótkie, opisowe i dostatecznie odróżniane, aby zapewnić wyraźny wybór między konkurencyjnymi opcjami.
* Błędy pisowni lub niemal pasujące wartości. Jeśli jest to zestaw reguł dla kolorów, a wartości pól to pomarańczowe i Ornage (błąd pisowni), zestaw reguł opartych na polu koloru będzie się znajdować w obu.
* Mieszany tekst Case może również Wreak destabilizować w nawigacji aspektowej, a pomarańcze i pomarańczowa pojawiają się jako dwie różne wartości. 
* Pojedyncze i plural wersje tej samej wartości mogą spowodować oddzielny zestaw reguł dla każdego z nich.

Jak można wyobrazić, staranność przygotowywania danych jest ważnym aspektem efektywnej nawigacji aspektowej.

<a name="presentationlayer"></a>

## <a name="build-the-ui"></a>Tworzenie interfejsu użytkownika
Praca z powrotem z warstwy prezentacji może pomóc w odwróceniu wymagań, które mogą zostać pominięte w przeciwnym razie, i zrozumieć, które funkcje są niezbędne dla środowiska wyszukiwania.

W ramach nawigacji aspektowej, Strona sieci Web lub aplikacja wyświetla strukturę nawigacyjną aspektów, wykrywa dane wejściowe użytkownika na stronie i wstawia zmienione elementy. 

W przypadku aplikacji sieci Web AJAX jest często używana w warstwie prezentacji, ponieważ umożliwia odświeżanie zmian przyrostowych. Można również użyć ASP.NET MVC lub dowolnej innej platformy wizualizacji, która może nawiązać połączenie z usługą Azure Search za pośrednictwem protokołu HTTP. Przykładowa aplikacja, do której odwołuje się w tym artykule — **demonstracja Azure Search portalu zadań** — stanie się aplikacją ASP.NET MVC.

W przykładzie Nawigacja aspektowa jest wbudowana na stronę wyników wyszukiwania. Poniższy przykład, pobrany z `index.cshtml` pliku przykładowej aplikacji, pokazuje statyczną strukturę HTML do wyświetlania aspektów nawigacyjnych na stronie wyników wyszukiwania. Lista zestawów reguł została skompilowana lub odtworzona dynamicznie podczas przesyłania terminu wyszukiwania lub zaznaczania lub czyszczenia aspektu.

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

Poniższy fragment kodu ze strony `index.cshtml` dynamicznie kompiluje kod HTML w celu wyświetlenia pierwszego zestawu reguł, tytułu biznesowego. Podobne funkcje dynamicznie kompilują kod HTML dla innych aspektów. Każdy aspekt ma etykietę i liczbę, która wyświetla liczbę elementów znalezionych dla tego wyniku zestawu.

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
> Podczas projektowania strony wyników wyszukiwania Pamiętaj, aby dodać mechanizm czyszczenia aspektów. Po dodaniu pól wyboru można łatwo zobaczyć, jak wyczyścić filtry. W przypadku innych układów może być potrzebny wzorzec nawigacji lub inne podejście. Na przykład w przykładowej aplikacji Portal wyszukiwania zadań możesz kliknąć `[X]` po wybraniu zestawu reguł, aby wyczyścić zestaw reguł.

<a name="buildquery"></a>

## <a name="build-the-query"></a>Tworzenie zapytania
Kod napisany dla kwerend tworzących powinien określać wszystkie części prawidłowego zapytania, w tym wyrażenia wyszukiwania, zestawy reguł, filtry, profile oceniania — wszystko użyte do sformułowania żądania. W tej sekcji eksplorujemy, gdzie zestawy reguł mieszczą się w zapytaniu i jak filtry są używane z aspektami, aby dostarczyć zredukowany zestaw wyników.

Zwróć uwagę, że aspekty są integralną częścią tej przykładowej aplikacji. Środowisko wyszukiwania w demonstracji portalu zadań zostało zaprojektowane z założeniami nawigacji i filtrów aspektów. Widoczne rozmieszczenie aspektowej nawigacji na stronie przedstawia jego znaczenie. 

Przykład często jest dobrym miejscem do rozpoczęcia. Poniższy przykład, pobrany z pliku `JobsSearch.cs`, kompiluje żądanie, które tworzy nawigację zestawu reguł na podstawie tytułu biznesowego, lokalizacji, typu księgowania i minimalnego wynagrodzenia. 

```cs
SearchParameters sp = new SearchParameters()
{
  ...
  // Add facets
  Facets = new List<String>() { "business_title", "posting_type", "level", "salary_range_from,interval:50000" },
};
```

Parametr zapytania aspektu jest ustawiany na pole i w zależności od typu danych, może być dodatkowo sparametryzowane przez listę rozdzielaną przecinkami, która zawiera `count:<integer>`, `sort:<>`, `interval:<integer>` i `values:<list>`. Lista wartości jest obsługiwana w przypadku danych liczbowych podczas konfigurowania zakresów. Zobacz [dokumenty wyszukiwania (Azure Search API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) , aby uzyskać szczegółowe informacje dotyczące użycia.

Wraz z aspektami, żądanie formułowane przez aplikację powinno również kompilować filtry, aby zawęzić zestaw dokumentów kandydujących na podstawie wyboru wartości aspektu. W przypadku sklepu z rowerem funkcja Nawigacja aspektowa zawiera pytania, na przykład *Informacje o kolorach, producentach i typach rowerów, które są dostępne?* . Filtrowanie odpowiedzi na pytania, na przykład *te, które dokładne rowery to Red, Mountain Bikes, w tym zakresie cenowym?* . Po kliknięciu przycisku "czerwony", aby wskazać, że powinny być wyświetlane tylko czerwone produkty, następne zapytanie wysyłane przez aplikację zawiera `$filter=Color eq ‘Red’`.

Poniższy fragment kodu ze strony `JobsSearch.cs` dodaje wybrany tytuł biznesowy do filtru w przypadku wybrania wartości z zestawu reguł tytułu biznesowego.

```cs
if (businessTitleFacet != "")
  filter = "business_title eq '" + businessTitleFacet + "'";
```

<a name="tips"></a> 

## <a name="tips-and-best-practices"></a>Wskazówki i najlepsze rozwiązania

### <a name="indexing-tips"></a>Porady dotyczące indeksowania
**Zwiększ wydajność indeksu, jeśli nie używasz pola wyszukiwania**

Jeśli aplikacja używa nawigacji aspektowej wyłącznie (to nie pole wyszukiwania), można oznaczyć pole jako `searchable=false`, `facetable=true`, aby utworzyć bardziej kompaktowy indeks. Ponadto indeksowanie odbywa się tylko na wszystkich wartościach aspektów, bez dzielenia wyrazów ani indeksowania części składnika wartości wielowyrazowej.

**Określ, które pola mogą być używane jako aspekty**

Odwołaj, że schemat indeksu określa, które pola są dostępne do użycia jako zestaw reguł. Przy założeniu, że pole jest kroju, zapytanie określa pola, według których ma być określony aspekt. Pole, według którego są aspektami, zawiera wartości, które pojawiają się poniżej etykiety. 

Wartości, które pojawiają się w każdej etykiecie, są pobierane z indeksu. Na przykład, jeśli pole aspekt ma *kolor*, wartości dostępne do dodatkowego filtrowania to wartości dla tego pola — czerwony, czarny i tak dalej.

Dla wartości liczbowych i DateTime można jawnie ustawić wartości w polu aspekt (na przykład `facet=Rating,values:1|2|3|4|5`). Lista wartości jest dozwolona dla tych typów pól, aby uprościć rozdzielenie wyników aspektów do zakresów ciągłych (zakresy w oparciu o wartości liczbowe lub okresy). 

**Domyślnie można mieć tylko jeden poziom nawigacji aspektowej** 

Jak wspomniano, nie ma bezpośredniego wsparcia dla zagnieżdżania aspektów w hierarchii. Domyślnie Nawigacja aspektowa w Azure Search obsługuje tylko jeden poziom filtrów. Istnieją jednak obejścia tego problemu. Hierarchiczną strukturę aspektów można zakodować w `Collection(Edm.String)` z jednym punktem wejścia na hierarchię. Wdrożenie tego obejścia wykracza poza zakres tego artykułu. 

### <a name="querying-tips"></a>Porady dotyczące wykonywania zapytań
**Sprawdzanie poprawności pól**

W przypadku kompilowania listy aspektów dynamicznie na podstawie niezaufanych danych wejściowych użytkownika Sprawdź, czy nazwy pól aspektów są prawidłowe. Możesz również wprowadzić nazwy podczas kompilowania adresów URL przy użyciu `Uri.EscapeDataString()` w programie .NET lub odpowiednika na wybranej platformie.

### <a name="filtering-tips"></a>Wskazówki dotyczące filtrowania
**Zwiększ precyzję wyszukiwania za pomocą filtrów**

Użyj filtrów. Jeśli korzystasz tylko z wyszukiwanych wyrażeń, może to spowodować, że dokument zostanie zwrócony, który nie ma dokładnej wartości aspektu w żadnym z jego pól.

**Zwiększenie wydajności wyszukiwania za pomocą filtrów**

Filtry zawężają zestaw dokumentów kandydujących do wyszukiwania i wykluczają je z klasyfikacji. Jeśli masz duży zestaw dokumentów, za pomocą funkcji szczegółowego przechodzenia do szczegółów, często zapewniasz lepszą wydajność.
  
**Filtruj tylko pola aspektów**

W przypadku przechodzenia do szczegółów, zazwyczaj chcesz uwzględnić tylko dokumenty, które mają wartość aspektu w konkretnym polu (aspekt), nie w dowolnym miejscu dla wszystkich pól, które można przeszukiwać. Dodawanie filtru wzmacnia pole docelowe przez kierowanie usługi do wyszukiwania tylko w polu aspektu dla zgodnej wartości.

**Przytnij wyniki aspektów o większej liczbie filtrów**

Wyniki aspektów są dokumentami znalezionymi w wynikach wyszukiwania, które pasują do warunku aspektu. W poniższym przykładzie, w wynikach wyszukiwania w przypadku *chmury obliczeniowej*, elementy 254 również mają *wewnętrzną specyfikację* jako typ zawartości. Elementy niekoniecznie wykluczają się wzajemnie. Jeśli element spełnia kryteria obu filtrów, jest liczony w każdym z nich. To duplikowanie jest możliwe w przypadku aspektów `Collection(Edm.String)` pól, które są często używane do implementowania tagowania dokumentów.

        Search term: "cloud computing"
        Content type
           Internal specification (254)
           Video (10) 

Ogólnie rzecz biorąc, jeśli okaże się, że wyniki aspektów są stale zbyt duże, zalecamy dodanie kolejnych filtrów, aby zapewnić użytkownikom więcej opcji zawężania wyszukiwania.

### <a name="tips-about-result-count"></a>Porady dotyczące liczby wyników

**Ogranicz liczbę elementów w nawigacji aspektów**

Dla każdego pola aspektu w drzewie nawigacji istnieje domyślny limit 10 wartości. Ta wartość domyślna ma sens dla struktur nawigacyjnych, ponieważ przechowuje listę wartości do rozmiaru możliwego do zarządzania. Ustawienie domyślne można zastąpić, przypisując wartość do zliczenia.

* `&facet=city,count:5` określa, że tylko pięć pierwszych miast znalezionych w górnych wynikach rangi są zwracane jako wynik aspektu. Rozważmy przykładowe zapytanie z wyszukiwanym terminem "lotniska" i 32 dopasowań. Jeśli zapytanie określi `&facet=city,count:5`, w wynikach aspektu są uwzględniane tylko pięć pierwszych unikatowych miast z większością dokumentów w wynikach wyszukiwania.

Zauważ różnice między wynikami aspektów a wynikami wyszukiwania. Wyniki wyszukiwania to wszystkie dokumenty, które pasują do zapytania. Wyniki aspektów są dopasowania dla każdej wartości aspektu. W przykładzie wyniki wyszukiwania obejmują nazwy miast, które nie znajdują się na liście klasyfikacji aspektów (5 w naszym przykładzie). Wyniki odfiltrowane za poorednictwem nawigacji aspektowej stają się widoczne w przypadku wyczyszczenia aspektów lub wybrania innych aspektów oprócz miejscowości. 

> [!NOTE]
> Omawianie `count`, gdy może być myląca więcej niż jeden typ. Poniższa tabela zawiera krótkie podsumowanie sposobu korzystania z tego terminu w Azure Search interfejsie API, przykładowy kod i dokumentacja. 

* `@colorFacet.count`<br/>
  W kodzie prezentacji powinien zostać wyświetlony parametr Count w aspekcie używany do wyświetlania liczby wyników aspektów. W polu wyniki aspektów licznik wskazuje liczbę dokumentów, które pasują do warunku lub zakresu aspektu.
* `&facet=City,count:12`<br/>
  W zapytaniu aspektu można ustawić liczbę na wartość.  Wartość domyślna to 10, ale można ją ustawić na wyższą lub niższą. Ustawienie `count:12` pobiera górne 12 dopasowań w wynikach według liczby dokumentów.
* „`@odata.count`”<br/>
  W odpowiedzi na zapytanie ta wartość wskazuje liczbę pasujących elementów w wynikach wyszukiwania. Średnio jest większa niż suma wszystkich wyników skojarzonych z aspektami, ze względu na obecność elementów zgodnych z wyszukiwanym terminem, ale nie ma dopasowania wartości aspektu.

**Pobierz liczniki w wynikach aspektu**

Po dodaniu filtru do zapytania aspektowego warto zachować instrukcję facet (na przykład `facet=Rating&$filter=Rating ge 4`). Technicznie, Faseta = klasyfikacja nie jest wymagana, ale utrzymywanie zwraca liczbę wartości aspektów dla klasyfikacji 4 i wyższych. Jeśli na przykład klikniesz pozycję "4", a zapytanie zawiera filtr o wartości większej lub równej "4", liczniki są zwracane dla każdej klasyfikacji, która ma wartość 4 lub wyższą.  

**Upewnij się, że otrzymujesz dokładne liczby aspektów**

W pewnych okolicznościach może się okazać, że liczby aspektów nie pasują do zestawów wyników (patrz temat [Nawigacja aspektowa w Azure Search (wpis na forum)](https://social.msdn.microsoft.com/Forums/azure/06461173-ea26-4e6a-9545-fbbd7ee61c8f/faceting-on-azure-search?forum=azuresearch)).

Liczby aspektów mogą być niedokładne ze względu na architekturę fragmentowania. Każdy indeks wyszukiwania ma wiele fragmentów, a każdy fragmentu raportuje pierwsze N zestawów reguł, które są następnie połączone w jeden wynik. Jeśli niektóre fragmentów mają wiele pasujących wartości, a inne mają mniejszą liczbę, może się okazać, że brakuje niektórych wartości aspektów lub są one zliczane w wynikach.

Chociaż takie zachowanie może się zmienić w dowolnym momencie, jeśli wystąpi takie zachowanie dzisiaj, można obejść je przez sztucznie niepłaską liczbę: \<number > do dużej liczby, aby wymusić pełne raportowanie z każdego fragmentuu. Jeśli wartość Count: jest większa lub równa liczbie unikatowych wartości w polu, są gwarantowane dokładne wyniki. Jeśli jednak liczba dokumentów jest wysoka, nastąpi spadek wydajności, dlatego należy użyć tej opcji w rozsądny sposób.

### <a name="user-interface-tips"></a>Wskazówki dotyczące interfejsu użytkownika
**Dodaj etykiety dla każdego pola w nawigacji aspektu**

Etykiety są zwykle zdefiniowane w kodzie HTML lub w postaci (`index.cshtml` w przykładowej aplikacji). Brak interfejsu API w Azure Search dla etykiet nawigacji aspektów lub innych metadanych.

<a name="rangefacets"></a>

## <a name="filter-based-on-a-range"></a>Filtrowanie na podstawie zakresu
Aspektowanie zakresów wartości jest typowym wymaganiem aplikacji wyszukiwania. Zakresy są obsługiwane dla danych liczbowych i wartości typu DateTime. Więcej informacji na temat każdego podejścia można znaleźć w [dokumencie wyszukiwania (Azure Search API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).

Azure Search upraszcza konstruowanie zakresu, dostarczając dwa podejścia do przetwarzania zakresu. W obu podejścia Azure Search tworzy odpowiednie zakresy z uwzględnieniem wprowadzonych danych wejściowych. Na przykład jeśli określisz wartości zakres 10 | 20 | 30, automatycznie tworzone są zakresy 0-10, 10-20, 20-30. Aplikacja może opcjonalnie usunąć wszystkie interwały, które są puste. 

**Podejście 1: Użycie parametru interwału**  
Aby ustawić aspekty cen w przyrostach $10, należy określić: `&facet=price,interval:10`

**Podejście 2. Korzystanie z listy wartości**  
W przypadku danych liczbowych można użyć listy wartości.  Weź pod uwagę zakres aspektów dla `listPrice`go pola, renderowane w następujący sposób:

  ![Lista przykładowych wartości](media/search-faceted-navigation/Facet-5-Prices.PNG "Lista przykładowych wartości")

Aby określić zakres aspektów podobny do przedstawionego na poprzednim zrzucie ekranu, Użyj listy wartości:

    facet=listPrice,values:10|25|100|500|1000|2500

Każdy zakres jest tworzony przy użyciu 0 jako punkt początkowy, wartość z listy jako punkt końcowy, a następnie przycięta z poprzedniego zakresu w celu utworzenia odrębnych interwałów. Azure Search wykonuje te czynności jako część nawigacji aspektowej. Nie trzeba pisać kodu do tworzenia struktury każdego interwału.

### <a name="build-a-filter-for-a-range"></a>Tworzenie filtru dla zakresu
Aby filtrować dokumenty w oparciu o wybrany zakres, można użyć operatorów filtrów `"ge"` i `"lt"` w wyrażeniu dwuczęściowym, które definiuje punkty końcowe zakresu. Na przykład jeśli wybierzesz zakres 10-25 dla pola `listPrice`, filtr zostanie `$filter=listPrice ge 10 and listPrice lt 25`. W przykładowym kodzie wyrażenie filtru używa parametrów **priceFrom** i **priceTo** do ustawiania punktów końcowych. 

  ![Zapytanie dotyczące zakresu wartości](media/search-faceted-navigation/Facet-6-buildfilter.PNG "Zapytanie dotyczące zakresu wartości")

<a name="geofacets"></a> 

## <a name="filter-based-on-distance"></a>Filtrowanie na podstawie odległości
Często można zobaczyć filtry, które ułatwiają wybranie sklepu, restauracji lub lokalizacji docelowej na podstawie bliskości bieżącej lokalizacji. Mimo że ten typ filtru może wyglądać podobnie do nawigacji aspektowej, jest to filtr. Wspominamy o tym w tym miejscu dla osób, które w szczególności szukają porady dotyczącej tego problemu podczas projektowania.

W Azure Search, **lokalizacji geograficznej, odległości** i **lokalizacji geograficznej**, istnieją dwie funkcje geoprzestrzenne.

* Funkcja **Geo. Distance** zwraca odległość w kilometrach między dwoma punktami. Jednym z punktów jest pole, a inne jest stałą przekazaną w ramach filtra. 
* Funkcja **Geo. intersects** zwraca wartość true, jeśli dany punkt znajduje się w obrębie danego wielokąta. Punkt to pole, a Wielokąt jest określony jako stała Lista współrzędnych przenoszona jako część filtru.

Przykłady filtrów można znaleźć w [składni wyrażenia OData (Azure Search)](query-odata-filter-orderby-syntax.md).

<a name="tryitout"></a>

## <a name="try-the-demo"></a>Wypróbuj wersję demonstracyjną
Demonstracja Azure Search portalu zadań zawiera przykłady, do których odwołuje się ten artykuł.

-   Zobacz i przetestuj pokaz roboczy online w wersji [demonstracyjnej portalu zadań Azure Search](https://azjobsdemo.azurewebsites.net/).

-   Pobierz kod z [repozytorium Azure-Samples w witrynie GitHub](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs).

Podczas pracy z wynikami wyszukiwania Obejrzyj adres URL pod kątem zmian w konstruowaniu zapytań. Ta aplikacja ma na celu dołączenie aspektów do identyfikatora URI podczas wybierania każdego z nich.

1. Aby skorzystać z funkcji mapowania aplikacji demonstracyjnej, Pobierz klucz mapy Bing z [Centrum deweloperów map Bing](https://www.bingmapsportal.com/). Wklej ją na istniejącym kluczu na stronie `index.cshtml`. Ustawienie `BingApiKey` w pliku `Web.config` nie jest używane. 

2. Uruchom aplikację. Zapoznaj się z przewodnikiem opcjonalnym lub Odrzuć okno dialogowe.
   
3. Wprowadź termin wyszukiwania, na przykład "analityk", a następnie kliknij ikonę wyszukiwania. Zapytanie jest wykonywane szybko.
   
   Struktura nawigacji aspektów jest również zwracana z wynikami wyszukiwania. Na stronie wynik wyszukiwania struktura nawigacji aspektowa zawiera zliczenia dla każdego wyniku zestawu reguł. Nie wybrano żadnych aspektów, więc zwracane są wszystkie zgodne wyniki.
   
   ![Wyniki wyszukiwania przed wybraniem zestawów reguł](media/search-faceted-navigation/faceted-search-before-facets.png "Wyniki wyszukiwania przed wybraniem zestawów reguł")

4. Kliknij tytuł firmy, lokalizację lub wynagrodzenie minimalne. W wyszukiwaniu początkowym zestawy reguł miały wartość null, ale w miarę ich wartości wyniki wyszukiwania są przycinane z elementów, które nie są już zgodne.
   
   ![Wyniki wyszukiwania po wybraniu aspektów](media/search-faceted-navigation/faceted-search-after-facets.png "Wyniki wyszukiwania po wybraniu aspektów")

5. Aby wyczyścić zapytanie aspektowe, dzięki czemu można wypróbować inne zachowania zapytań, kliknij `[X]` po zaznaczonych zestawach, aby wyczyścić zestawy reguł.
   
<a name="nextstep"></a>

## <a name="learn-more"></a>Dowiedz się więcej
Obejrzyj [Azure Search głębokie szczegółowe](https://channel9.msdn.com/Events/TechEd/Europe/2014/DBI-B410). Na 45:25 znajduje się pokaz dotyczący implementacji aspektów.

Aby uzyskać więcej szczegółowych informacji na temat zasad projektowania dla nawigacji aspektowej, zalecamy następujące linki:

* [Wzorce projektowe: nawigacja Aspektowa](https://alistapart.com/article/design-patterns-faceted-navigation)
* [Zagadnienia dotyczące frontonu podczas implementowania wyszukiwania aspektowego — część 1](https://articles.uie.com/faceted_search2/)

