---
title: Dodawanie hierarchii kategorii nawigacji aspektowej
titleSuffix: Azure Cognitive Search
description: Dodaj nawigację aspektową do samodzielnego filtrowania w aplikacjach wyszukiwania, które integrują się z usługą Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 5f4435ca213584fff84f3ddad9bda6f7e06628a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283162"
---
# <a name="how-to-implement-faceted-navigation-in-azure-cognitive-search"></a>Jak zaimplementować nawigację aspektową w usłudze Azure Cognitive Search

Nawigacja aspektowa to mechanizm filtrowania, który zapewnia samodzielną nawigację w trybie szczegółowym w aplikacjach wyszukiwania. Termin "nawigacja fasetowana" może być nieznany, ale prawdopodobnie użyłeś go wcześniej. Jak pokazano w poniższym przykładzie, nawigacji aspektowej jest niczym więcej niż kategorie używane do filtrowania wyników.

 ![Prezentacja portalu wyszukiwania funkcji Azure Cognitive Search](media/search-faceted-navigation/azure-search-faceting-example.png "Prezentacja portalu wyszukiwania funkcji Azure Cognitive Search")

Nawigacja aspektowa jest alternatywnym punktem wejścia do wyszukiwania. Oferuje wygodną alternatywę dla ręcznego wpisywania złożonych wyrażeń wyszukiwania. Aspekty mogą pomóc ci znaleźć to, czego szukasz, zapewniając jednocześnie, że nie uzyskasz żadnych wyników. Jako deweloper aspekty umożliwiają udostępnianie najbardziej przydatnych kryteriów wyszukiwania do poruszania się po indeksie wyszukiwania. W aplikacjach sprzedaży detalicznej online nawigacja fasetowana jest często budowana na markach, działach (dziecięce buty), rozmiarze, cenie, popularności i ocenach. 

Implementowanie nawigacji aspektowej różni się w różnych technologii wyszukiwania. W usłudze Azure Cognitive Search nawigacja fasetowana jest budowana w czasie zapytania przy użyciu pól, które zostały wcześniej przypisane w schemacie.

-   W kwerendach, które tworzy aplikacja, kwerenda musi wysłać *parametry zapytania aspektu,* aby uzyskać dostępne wartości filtru aspektu dla tego zestawu wyników dokumentu.

-   Aby faktycznie przyciąć zestaw wyników dokumentu, `$filter` aplikacja musi również zastosować wyrażenie.

W rozwoju aplikacji, pisanie kodu, który konstruuje kwerendy stanowi większość pracy. Wiele zachowań aplikacji, których można oczekiwać od nawigacji aspektowej są dostarczane przez usługę, w tym wbudowanej obsługi definiowania zakresów i uzyskiwania liczby wyników aspektu. Usługa zawiera również rozsądne ustawienia domyślne, które pomagają uniknąć niewydolnych struktur nawigacji. 

## <a name="sample-code-and-demo"></a>Przykładowy kod i wersja demonstracyjna
W tym artykule użyto portalu wyszukiwania ofert pracy jako przykładu. Przykład jest implementowany jako aplikacja ASP.NET MVC.

- Zobacz i przetestuj działające demo w trybie online w [witrynie Azure Cognitive Search Job Portal Demo](https://aka.ms/azjobsdemo).

- Pobierz kod z [repozytorium przykładów platformy Azure w usłudze GitHub](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs).

## <a name="get-started"></a>Wprowadzenie
Jeśli dopiero zaczynasz yć się w poszukiwaniu, najlepszym sposobem myślenia o nawigacji aspektowej jest to, że pokazuje możliwości samodzielnego wyszukiwania. Jest to rodzaj szczegółowego środowiska wyszukiwania, opartego na wstępnie zdefiniowanych filtrach, służącego do szybkiego zawężenia wyników wyszukiwania za pomocą akcji typu "wskaż i kliknij". 

### <a name="interaction-model"></a>Model interakcji

Środowisko wyszukiwania dla nawigacji aspektowej jest iteracyjne, więc zacznijmy od zrozumienia go jako sekwencji zapytań, które rozwijają się w odpowiedzi na akcje użytkownika.

Punktem wyjścia jest strona aplikacji, która zapewnia nawigacji aspektowej, zazwyczaj umieszczone na obrzeżach. Nawigacja aspektowa jest często strukturą drzewa, z polami wyboru dla każdej wartości lub klikalnym tekstem. 

1. Kwerenda wysłana do usługi Azure Cognitive Search określa strukturę nawigacji aspektowej za pomocą co najmniej jednego parametru zapytania aspektu. Na przykład kwerenda `facet=Rating`może zawierać `:values` , `:sort` być może z lub opcji do dalszego uściślania prezentacji.
2. Warstwa prezentacji renderuje stronę wyszukiwania, która zapewnia nawigację aspektową, przy użyciu aspektów określonych w żądaniu.
3. Biorąc pod uwagę strukturę nawigacji aspektowej, która zawiera klasyfikację, kliknij "4", aby wskazać, że powinny być wyświetlane tylko produkty o ocenie 4 lub wyższej. 
4. W odpowiedzi aplikacja wysyła zapytanie, które zawiera`$filter=Rating ge 4` 
5. Warstwa prezentacji aktualizuje stronę, wyświetlając zestaw wyników o obniżonej jakości, zawierający tylko te elementy, które spełniają nowe kryteria (w tym przypadku produkty o obniżonej jakości 4 i nowszych).

Aspekt jest parametrem kwerendy, ale nie należy mylić go z wprowadzaniem kwerendy. Nigdy nie jest używany jako kryteria wyboru w kwerendzie. Zamiast tego pomyśl o parametrach zapytania aspektu jako dane wejściowe do struktury nawigacji, która powraca w odpowiedzi. Dla każdego parametru zapytania aspektu, który podasz, usługa Azure Cognitive Search ocenia, ile dokumentów znajduje się w wynikach częściowych dla każdej wartości aspektu.

Zwróć `$filter` uwagę na krok 4. Filtr jest ważnym aspektem nawigacji aspektowej. Mimo że aspekty i filtry są niezależne w interfejsie API, należy zarówno dostarczyć środowisko, które zamierzasz. 

### <a name="app-design-pattern"></a>Wzór projektu aplikacji

W kodzie aplikacji wzorzec jest użycie parametrów zapytania aspektu do zwrócenia struktury nawigacji aspektowej wraz z wynikami aspektu oraz wyrażenie $filter.  Wyrażenie filtru obsługuje zdarzenie kliknięcia na wartości aspektu. Wyrażenie należy `$filter` potraktować jako kod, który powodowanie rzeczywistego przycinania wyników wyszukiwania jest zwracane do warstwy prezentacji. Biorąc pod uwagę kolory aspekt, kliknięcie koloru `$filter` Czerwony jest implementowane za pomocą wyrażenia, które wybiera tylko te elementy, które mają kolor czerwony. 

### <a name="query-basics"></a>Podstawowe informacje o kwerendach

W usłudze Azure Cognitive Search żądanie jest określone za pomocą co najmniej jednego parametru zapytania (zobacz [Wyszukiwanie dokumentów](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) w celu uzyskania opisu każdego z nich). Żaden z parametrów kwerendy nie są wymagane, ale musi mieć co najmniej jeden, aby kwerenda była prawidłowa.

Precyzja, rozumiana jako zdolność do odfiltrowywać nieistotnych trafień, jest osiągana za pomocą jednego lub obu z tych wyrażeń:

-   **szukaj=**  
    Wartość tego parametru stanowi wyrażenie wyszukiwania. Może to być pojedynczy fragment tekstu lub złożone wyrażenie wyszukiwania, które zawiera wiele terminów i operatorów. Na serwerze wyrażenie wyszukiwania jest używane do wyszukiwania pełnotekstowego, wykonywania zapytań do przeszukiwania pól w indeksie w celu dopasowania terminów, zwracania wyników w kolejności rangi. Jeśli ustawiono `search` wartość null, wykonanie kwerendy jest `search=*`w całym indeksie (czyli ). W takim przypadku inne elementy kwerendy, `$filter` takie jak profil lub profil oceniania, `($filter`są podstawowymi`scoringProfile` czynnikami wpływającymi na zwracane dokumenty) i w jakiej kolejności ( lub `$orderby`).

-   **$filter=**  
    Filtr jest zaawansowanym mechanizmem ograniczania rozmiaru wyników wyszukiwania na podstawie wartości określonych atrybutów dokumentu. A `$filter` jest oceniany najpierw, a następnie logiki aspektowania, która generuje dostępne wartości i odpowiadające im liczby dla każdej wartości

Złożone wyrażenia wyszukiwania zmniejszają wydajność kwerendy. Jeśli to możliwe, użyj dobrze skonstruowanych wyrażeń filtru, aby zwiększyć precyzję i zwiększyć wydajność kwerendy.

Aby lepiej zrozumieć, jak filtr dodaje większą precyzję, porównaj złożone wyrażenie wyszukiwania z wyrażeniem zawierającym wyrażenie filtru:

-   `GET /indexes/hotel/docs?search=lodging budget +Seattle –motel +parking`
-   `GET /indexes/hotel/docs?search=lodging&$filter=City eq 'Seattle' and Parking and Type ne 'motel'`

Oba zapytania są ważne, ale drugi jest lepszy, jeśli szukasz nie-motele z parkingu w Seattle.
-   Pierwsze zapytanie opiera się na tych określonych wyrazów są wymienione lub nie wymienione w polach ciągu, takich jak nazwa, opis i inne pole zawierające dane z możliwością wyszukiwania.
-   Drugie zapytanie wyszukuje dokładne dopasowania danych strukturalnych i może być znacznie dokładniejsze.

W aplikacjach, które zawierają nawigacji aspektowej, upewnij się, że każda akcja użytkownika za pomocą struktury nawigacji aspektowej towarzyszy zawężenie wyników wyszukiwania. Aby zawęzić wyniki, użyj wyrażenia filtru.

<a name="howtobuildit"></a>

## <a name="build-a-faceted-navigation-app"></a>Tworzenie aplikacji do nawigacji aspektowej
Implementujesz nawigacji aspektowej za pomocą usługi Azure Cognitive Search w kodzie aplikacji, który tworzy żądanie wyszukiwania. Nawigacja aspektowa opiera się na elementach w schemacie, które zostały zdefiniowane wcześniej.

Wstępnie zdefiniowane w indeksie `Facetable [true|false]` wyszukiwania jest atrybut indeksu, ustawiony na wybranych polach, aby włączyć lub wyłączyć ich użycie w strukturze nawigacji aspektowej. Bez `"Facetable" = true`, pole nie może być używane w nawigacji aspektu.

Warstwa prezentacji w kodzie zapewnia środowisko użytkownika. Należy wyświetlić listę części składowych nawigacji aspektowej, takich jak etykieta, wartości, pola wyboru i count. Interfejs API REST usługi Azure Cognitive Search jest niezależny od platformy, więc użyj dowolnego języka i platformy, który chcesz. Ważną rzeczą jest dołączenie elementów interfejsu użytkownika, które obsługują odświeżanie przyrostowe, ze zaktualizowanym stanem interfejsu użytkownika, jak każdy dodatkowy aspekt jest zaznaczony. 

W czasie kwerendy kod aplikacji tworzy `facet=[string]`żądanie, które zawiera parametr żądania, który zapewnia pole do aspektu przez. Kwerenda może mieć wiele aspektów, takich jak `&facet=color&facet=category&facet=rating`, każdy oddzielony znakiem ampersand (&).

Kod aplikacji musi `$filter` również skonstruować wyrażenie do obsługi zdarzeń kliknięcia w nawigacji aspektowej. A `$filter` zmniejsza wyniki wyszukiwania, używając wartości aspektu jako kryteriów filtrowania.

Usługa Azure Cognitive Search zwraca wyniki wyszukiwania, na podstawie jednego lub więcej terminów, które można wprowadzić, wraz z aktualizacjami struktury nawigacji aspektowej. W usłudze Azure Cognitive Search nawigacja fasetowana jest konstrukcją jednopoziomową, z wartościami aspektu i liczbami wyników znalezionych dla każdego z nich.

W poniższych sekcjach przyjrzymy się bliżej, jak zbudować każdą część.

<a name="buildindex"></a>

## <a name="build-the-index"></a>Tworzenie indeksu
Fasetowanie jest włączone w połówce pola w indeksie, `"Facetable": true`za pomocą tego atrybutu indeksu: .  
Domyślnie są `Facetable` używane wszystkie typy pól, które mogą być używane w nawigacji aspektowej. Takie typy `Edm.String` `Edm.DateTimeOffset`pól obejmują , i wszystkie typy pól liczbowych `Edm.GeographyPoint`(zasadniczo wszystkie typy pól są typy typu facetable z wyjątkiem , które nie mogą być używane w nawigacji aspektowej). 

Podczas tworzenia indeksu najlepszym rozwiązaniem dla nawigacji aspektowej jest jawnie wyłączyć aspektowanie dla pól, które nigdy nie powinny być używane jako aspekt.  W szczególności pola ciągu dla wartości pojedynczegotonu, takich jak identyfikator `"Facetable": false` lub nazwa produktu, powinny być ustawione, aby zapobiec ich przypadkowemu (i nieskuteczne) użycie w nawigacji aspektowej. Wyłączenie aspektowania w miejscu, w którym nie jest potrzebne, pomaga zachować mały rozmiar indeksu i zazwyczaj poprawia wydajność.

Poniżej znajduje się część schematu dla przykładowej aplikacji Demo portalu pracy, przycięte niektórych atrybutów, aby zmniejszyć rozmiar:

```json
{
  ...
  "name": "nycjobs",
  "fields": [
    { "name": "id",                 "type": "Edm.String",              "searchable": false, "filterable": false, ... "facetable": false, ... },
    { "name": "job_id",             "type": "Edm.String",              "searchable": false, "filterable": false, ... "facetable": false, ... },
    { "name": "agency",              "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "posting_type",        "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "num_of_positions",    "type": "Edm.Int32",              "searchable": false, "filterable": true, ...  "facetable": true, ...  },
    { "name": "business_title",      "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "civil_service_title", "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "title_code_no",       "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "level",               "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "salary_range_from",   "type": "Edm.Int32",              "searchable": false, "filterable": true, ...  "facetable": true, ...  },
    { "name": "salary_range_to",     "type": "Edm.Int32",              "searchable": false, "filterable": true, ...  "facetable": true, ...  },
    { "name": "salary_frequency",    "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "work_location",       "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
…
    { "name": "geo_location",        "type": "Edm.GeographyPoint",     "searchable": false, "filterable": true, ...  "facetable": false, ... },
    { "name": "tags",                "type": "Collection(Edm.String)", "searchable": true,  "filterable": true, ...  "facetable": true, ...  }
  ],
…
}
```

Jak widać w przykładowym schemacie, jest wyłączony dla pól ciągów, `Facetable` które nie powinny być używane jako aspekty, takie jak wartości identyfikatora. Wyłączenie aspektowania w miejscu, w którym nie jest potrzebne, pomaga zachować mały rozmiar indeksu i zazwyczaj poprawia wydajność.

> [!TIP]
> Najlepszym rozwiązaniem jest uwzględnij pełny zestaw atrybutów indeksu dla każdego pola. Chociaż `Facetable` jest domyślnie włączony dla prawie wszystkich pól, celowo ustawienie każdego atrybutu może pomóc w przemyśleniu implikacji każdej decyzji schematu. 

<a name="checkdata"></a>

## <a name="check-the-data"></a>Sprawdź dane
Jakość danych ma bezpośredni wpływ na to, czy struktura nawigacji aspektowej materializuje się zgodnie z oczekiwaniami. Wpływa również na łatwość konstruowania filtrów, aby zmniejszyć zestaw wyników.

Jeśli chcesz aspekt według marki lub ceny, każdy dokument powinien zawierać wartości *brandname* i *ProductPrice,* które są prawidłowe, spójne i produktywne jako opcja filtru.

Oto kilka przypomnień, co do szorowania:

* Dla każdego pola, które chcesz aspekt przez, zadać sobie pytanie, czy zawiera wartości, które są odpowiednie jako filtry w wyszukiwaniu własnym. Wartości powinny być krótkie, opisowe i wystarczająco charakterystyczne, aby zapewnić jasny wybór między konkurencyjnymi opcjami.
* Błędy pisowni lub prawie pasujące wartości. Jeśli aspekt na Kolor, a wartości pól obejmują Pomarańczowy i Ornage (błąd ortograficzny), aspekt oparty na color pole będzie odebrać zarówno.
* Mieszany tekst przypadku może również siać spustoszenie w nawigacji aspektowej, z pomarańczowym i pomarańczowym pojawiającym się jako dwie różne wartości. 
* Pojedyncze i mnogie wersje tej samej wartości może spowodować oddzielny aspekt dla każdego.

Jak można sobie wyobrazić, pracowitość w przygotowaniu danych jest istotnym aspektem skutecznej nawigacji aspektowej.

<a name="presentationlayer"></a>

## <a name="build-the-ui"></a>Tworzenie interfejsu użytkownika
Praca z warstwą prezentacji może pomóc odkryć wymagania, które mogą zostać pominięte w przeciwnym razie, i zrozumieć, które funkcje są niezbędne do wyszukiwania.

Jeśli chodzi o nawigację aspektową, strona sieci web lub aplikacji wyświetla strukturę nawigacji aspektowej, wykrywa dane wejściowe użytkownika na stronie i wstawia zmienione elementy. 

W przypadku aplikacji sieci web ajax jest powszechnie używany w warstwie prezentacji, ponieważ umożliwia odświeżanie zmian przyrostowych. Można również użyć ASP.NET MVC lub innej platformy wizualizacji, która może łączyć się z usługą Azure Cognitive Search za pośrednictwem protokołu HTTP. Przykładowa aplikacja, do którego odwołuje się w tym artykule — **demo portalu azure cognitive search job portal** — jest ASP.NET aplikacją MVC.

W przykładzie nawigacji aspektowej jest wbudowany w stronę wyników wyszukiwania. Poniższy przykład, zaczerpnięty z `index.cshtml` pliku przykładowej aplikacji, przedstawia statyczną strukturę HTML do wyświetlania nawigacji aspektowej na stronie wyników wyszukiwania. Lista aspektów jest budowana lub przebudowywana dynamicznie podczas przesyłania wyszukiwanego terminu lub wybierania lub wyczyszczenie aspektu.

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

Poniższy fragment kodu ze `index.cshtml` strony dynamicznie tworzy kod HTML, aby wyświetlić pierwszy aspekt, Tytuł biznesowy. Podobne funkcje dynamicznie budować HTML dla innych aspektów. Każdy aspekt ma etykietę i liczbę, która wyświetla liczbę elementów znalezionych dla tego wyniku aspektu.

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
> Podczas projektowania strony wyników wyszukiwania należy pamiętać o dodaniu mechanizmu oczyszczania aspektów. Jeśli dodasz pola wyboru, możesz łatwo zobaczyć, jak wyczyścić filtry. W przypadku innych układów może być potrzebny wzór nasadki lub inne kreatywne podejście. Na przykład w przykładowej aplikacji portalu wyszukiwania `[X]` zadań można kliknąć po wybranym fasetze, aby wyczyścić aspekt.

<a name="buildquery"></a>

## <a name="build-the-query"></a>Tworzenie kwerendy
Kod, który piszesz dla kwerend budowlanych, powinien określać wszystkie części prawidłowej kwerendy, w tym wyrażenia wyszukiwania, aspekty, filtry, profile oceniania — wszystko używane do formułowania żądania. W tej sekcji zbadamy, gdzie aspekty pasują do kwerendy i jak filtry są używane z aspektami, aby zapewnić zestaw wyników o zmniejszonym.

Należy zauważyć, że aspekty są integralną częścią tej przykładowej aplikacji. Środowisko wyszukiwania w demo portalu pracy jest zaprojektowany wokół nawigacji aspektowej i filtrów. Widoczne położenie nawigacji aspektowej na stronie pokazuje jej znaczenie. 

Przykładem jest często dobre miejsce do rozpoczęcia. Poniższy przykład, zaczerpnięty z `JobsSearch.cs` pliku, tworzy żądanie, które tworzy nawigacji aspekt na podstawie tytuł firmy, lokalizacja, typ księgowania i minimalne wynagrodzenie. 

```cs
SearchParameters sp = new SearchParameters()
{
  ...
  // Add facets
  Facets = new List<String>() { "business_title", "posting_type", "level", "salary_range_from,interval:50000" },
};
```

Parametr zapytania aspektowego jest ustawiony na pole i w zależności od typu danych może być `count:<integer>`dodatkowo `sort:<>` `interval:<integer>`sparametryzowany przez listę rozdzielanych przecinkami, która zawiera , , i `values:<list>`. Lista wartości jest obsługiwana dla danych liczbowych podczas konfigurowania zakresów. Szczegółowe informacje o [użyciu można znaleźć w dokumentach wyszukiwania (azure cognitive search API).](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)

Wraz z aspektami żądanie sformułowane przez aplikację należy również utworzyć filtry, aby zawęzić zestaw dokumentów kandydata na podstawie wyboru wartości aspektu. W przypadku sklepu rowerowego nawigacja fasetowana zawiera wskazówki na pytania, takie jak *Jakie kolory, producenci i rodzaje rowerów są dostępne?*. Filtrowanie odpowiedzi na pytania, takie jak *Które dokładnie rowery są czerwone, rowery górskie, w tym przedziale cenowym?*. Po kliknięciu przycisku "Czerwony", aby wskazać, że powinny być `$filter=Color eq 'Red'`wyświetlane tylko czerwone produkty, następna kwerenda wysyłana przez aplikację zawiera .

Poniższy fragment kodu ze `JobsSearch.cs` strony dodaje wybrany tytuł firmy do filtru, jeśli wybierzesz wartość z aspektu Tytuł firmy.

```cs
if (businessTitleFacet != "")
  filter = "business_title eq '" + businessTitleFacet + "'";
```

<a name="tips"></a> 

## <a name="tips-and-best-practices"></a>Wskazówki i najlepsze rozwiązania

### <a name="indexing-tips"></a>Wskazówki dotyczące indeksowania
**Zwiększ wydajność indeksu, jeśli nie korzystasz z pola wyszukiwania**

Jeśli aplikacja używa nawigacji aspektowej wyłącznie (czyli bez pola wyszukiwania), można `searchable=false` `facetable=true` oznaczyć pole jako , aby uzyskać bardziej kompaktowy indeks. Ponadto indeksowanie występuje tylko na całe wartości aspektu, bez podziału wyrazu lub indeksowania części składowych wartości wielowyrazowej.

**Określanie pól, które mogą być używane jako fasety**

Przypomnijmy, że schemat indeksu określa, które pola są dostępne do użycia jako aspekt. Zakładając, że pole jest facetable, kwerenda określa, które pola do aspektu przez. Pole, za pomocą którego są aspektowe zawiera wartości, które pojawiają się poniżej etykiety. 

Wartości, które pojawiają się pod każdą etykietą są pobierane z indeksu. Na przykład, jeśli polem aspektu jest *Kolor*, wartości dostępne dla dodatkowego filtrowania są wartościami dla tego pola - Czerwony, Czarny i tak dalej.

Tylko dla wartości liczbowych i DateTime można jawnie ustawić wartości `facet=Rating,values:1|2|3|4|5`w polu aspektu (na przykład ). Lista wartości jest dozwolona dla tych typów pól, aby uprościć rozdzielanie wyników aspektu na ciągłe zakresy (albo zakresy oparte na wartościach liczbowych lub okresach). 

**Domyślnie można mieć tylko jeden poziom nawigacji aspektowej** 

Jak wspomniano, nie ma bezpośredniego wsparcia dla zagnieżdżania aspektów w hierarchii. Domyślnie nawigacji aspektowej w usłudze Azure Cognitive Search obsługuje tylko jeden poziom filtrów. Istnieją jednak obejścia. Można zakodować strukturę aspektu `Collection(Edm.String)` hierarchicznego w jednym punkcie wejścia na hierarchię. Implementowanie tego obejścia wykracza poza zakres tego artykułu. 

### <a name="querying-tips"></a>Porady dotyczące zapytań
**Sprawdzanie poprawności pól**

Jeśli tworzysz listę aspektów dynamicznie na podstawie niezaufanych danych wejściowych użytkownika, sprawdź, czy nazwy pól aspektowych są prawidłowe. Można też uniknąć nazw podczas tworzenia adresów URL przy użyciu w domenie `Uri.EscapeDataString()` .NET lub odpowiednika w wybranej platformie.

### <a name="filtering-tips"></a>Porady dotyczące filtrowania
**Zwiększ precyzję wyszukiwania dzięki filtrom**

Użyj filtrów. Jeśli polegasz tylko na wyekslaj tylko wyrażenia, wynikające może spowodować dokument do zwrotu, który nie ma dokładnej wartości aspektu w żadnym z jego pól.

**Zwiększ wydajność wyszukiwania dzięki filtrom**

Filtry zawęzić zestaw dokumentów kandydata do wyszukiwania i wykluczyć je z rankingu. Jeśli masz duży zestaw dokumentów, przy użyciu selektywnego aspektu drążenia często daje lepszą wydajność.
  
**Filtrowanie tylko pól fasetowanych**

W aspektach drążenia w dół, zazwyczaj chcesz uwzględnić tylko dokumenty, które mają wartość aspektu w określonym (aspektowe) pole, a nie w dowolnym miejscu we wszystkich polach z możliwością wyszukiwania. Dodanie filtru wzmacnia pole docelowe, kierując usługę do wyszukiwania tylko w polu aspektowym dla pasującej wartości.

**Przycinanie wyników fasetki z większą liczą filtrami**

Wyniki aspektu są dokumenty znalezione w wynikach wyszukiwania, które pasują do terminu aspektu. W poniższym przykładzie w wynikach wyszukiwania dla *chmury obliczeniowej*254 elementy mają również *wewnętrzną specyfikację* jako typ zawartości. Przedmioty niekoniecznie wykluczają się wzajemnie. Jeśli towar spełnia kryteria obu filtrów, jest liczony w każdym z nich. To powielanie jest możliwe `Collection(Edm.String)` podczas fasetowania w polach, które są często używane do implementowania tagowania dokumentów.

        Search term: "cloud computing"
        Content type
           Internal specification (254)
           Video (10) 

Ogólnie rzecz biorąc, jeśli okaże się, że wyniki aspektu są stale zbyt duże, zaleca się dodanie większej liczby filtrów, aby dać użytkownikom więcej opcji zawężenia wyszukiwania.

### <a name="tips-about-result-count"></a>Wskazówki dotyczące liczby wyników

**Ograniczanie liczby elementów w nawigacji aspektowej**

Dla każdego pola aspektowego w drzewie nawigacji istnieje domyślny limit 10 wartości. Ta wartość domyślna ma sens dla struktur nawigacji, ponieważ utrzymuje listę wartości do rozmiaru łatwego do opanowania. Wartość domyślną można zastąpić, przypisując wartość do zliczenia.

* `&facet=city,count:5`określa, że tylko pięć pierwszych miast znalezionych w najlepszych wynikach rankingowych jest zwracanych jako wynik aspektu. Rozważ przykładowe zapytanie z wyszukiwanym terminem "lotnisko" i 32 dopasowaniami. Jeśli kwerenda `&facet=city,count:5`określa , tylko pierwsze pięć unikatowych miast z największą liczą dokumentów w wynikach wyszukiwania są uwzględniane w wynikach aspektu.

Zwróć uwagę na rozróżnienie między wynikami aspektu a wynikami wyszukiwania. Wyniki wyszukiwania to wszystkie dokumenty pasujące do zapytania. Wyniki aspektu są dopasowania dla każdej wartości aspektu. W przykładzie wyniki wyszukiwania obejmują nazwy miast, które nie znajdują się na liście klasyfikacji aspektu (5 w naszym przykładzie). Wyniki, które są filtrowane za pośrednictwem nawigacji aspektowej stają się widoczne po wyczyszczeniu aspektów lub wybrać inne aspekty oprócz miasta. 

> [!NOTE]
> Omawianie, `count` gdy istnieje więcej niż jeden typ może być mylące. Poniższa tabela zawiera krótkie podsumowanie sposobu użycia tego terminu w interfejsie API usługi Azure Cognitive Search, przykładowym kodzie i dokumentacji. 

* `@colorFacet.count`<br/>
  W kodzie prezentacji powinien zostać wyświetlony parametr count na faset, używany do wyświetlania liczby wyników aspektu. W wynikach aspektu liczba wskazuje liczbę dokumentów, które pasują do terminu lub zakresu aspektu.
* `&facet=City,count:12`<br/>
  W kwerendzie aspektu można ustawić count do wartości.  Wartość domyślna to 10, ale można ją ustawić wyżej lub niżej. Ustawienie `count:12` pobiera 12 najlepszych dopasowań w wynikach aspektu według liczby dokumentów.
* "`@odata.count`"<br/>
  W odpowiedzi na kwerendę ta wartość wskazuje liczbę pasujących elementów w wynikach wyszukiwania. Średnio jest większa niż suma wszystkich wyników aspektu połączone, ze względu na obecność elementów, które pasują do wyszukiwanego terminu, ale nie mają dopasowania wartości aspektu.

**Uzyskaj liczbę w wynikach aspektu**

Po dodaniu filtru do kwerendy aspektowej można zachować instrukcję `facet=Rating&$filter=Rating ge 4`aspektu (na przykład ). Technicznie facet=Ocena nie jest potrzebna, ale utrzymywanie go zwraca liczbę wartości aspektu dla ocen 4 i wyższych. Na przykład jeśli klikniesz "4", a kwerenda zawiera filtr dla większych lub równych "4", liczby są zwracane dla każdej oceny, która jest 4 i wyższa.  

**Upewnij się, że masz dokładne liczby facetów**

W pewnych okolicznościach może się okazać, że liczba aspektów nie pasuje do zestawów wyników (zobacz [Nawigacja aspektowa w usłudze Azure Cognitive Search (wpis na forum).](https://social.msdn.microsoft.com/Forums/azure/06461173-ea26-4e6a-9545-fbbd7ee61c8f/faceting-on-azure-search?forum=azuresearch)

Liczba facetów może być niedokładne ze względu na architekturę dzielenia na fragmenty. Każdy indeks wyszukiwania ma wiele fragmentów, a każdy niezależnego fragmentu raportuje górne aspekty N według liczby dokumentów, które są następnie łączone w jeden wynik. Jeśli niektóre fragmenty mają wiele pasujących wartości, podczas gdy inne mają mniej, może się okazać, że niektóre wartości aspektu brakuje lub niedoliczone w wynikach.

Mimo że to zachowanie może ulec zmianie w dowolnym momencie, jeśli napotkasz\<to zachowanie dzisiaj, można obejść go sztucznie zawyżania count: liczba> do dużej liczby, aby wymusić pełne raportowanie z każdego fragmentu. Jeśli wartość count: jest większa lub równa liczbie unikatowych wartości w polu, masz gwarancję dokładnych wyników. Jednak gdy liczba dokumentów jest wysoka, istnieje kara wykonania, więc należy rozsądnie użyć tej opcji.

### <a name="user-interface-tips"></a>Wskazówki dotyczące interfejsu użytkownika
**Dodawanie etykiet dla każdego pola w nawigacji aspektowej**

Etykiety są zazwyczaj definiowane w`index.cshtml` formacie HTML lub formularzu (w przykładowej aplikacji). W usłudze Azure Cognitive Search nie ma interfejsu API dla etykiet nawigacji aspektu ani innych metadanych.

<a name="rangefacets"></a>

## <a name="filter-based-on-a-range"></a>Filtr na podstawie zakresu
Fasetowanie w zakresie wartości jest typowe wymaganie aplikacji wyszukiwania. Zakresy są obsługiwane dla danych liczbowych i wartości DateTime. Więcej informacji na temat każdego podejścia można przeczytać w obszarze [Dokumenty wyszukiwania (azure cognitive search API).](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)

Usługa Azure Cognitive Search upraszcza tworzenie zakresu, udostępniając dwa podejścia do obliczania zakresu. Dla obu podejść usługi Azure Cognitive Search tworzy odpowiednie zakresy, biorąc pod uwagę dane wejściowe, które zostały dostarczone. Na przykład jeśli określisz wartości zakresu 10|20|30, automatycznie utworzy zakresy 0-10, 10-20, 20-30. Aplikacja może opcjonalnie usunąć wszystkie interwały, które są puste. 

**Podejście 1: Użyj parametru interwału**  
Aby ustawić aspekty ceny w przyrostach $10, należy określić:`&facet=price,interval:10`

**Podejście 2: Użyj listy wartości**  
W przypadku danych liczbowych można użyć listy wartości.  Należy wziąć pod `listPrice` uwagę zakres aspektu dla pola, renderowane w następujący sposób:

  ![Przykładowa lista wartości](media/search-faceted-navigation/Facet-5-Prices.PNG "Przykładowa lista wartości")

Aby określić zakres aspektu, taki jak ten na poprzednim zrzucie ekranu, użyj listy wartości:

    facet=listPrice,values:10|25|100|500|1000|2500

Każdy zakres jest zbudowany przy użyciu 0 jako punkt początkowy, wartość z listy jako punkt końcowy, a następnie przycięte z poprzedniego zakresu do tworzenia oddzielnych interwałów. Usługa Azure Cognitive Search wykonuje te czynności w ramach nawigacji aspektowej. Nie trzeba pisać kodu do strukturyzowania każdego interwału.

### <a name="build-a-filter-for-a-range"></a>Tworzenie filtra dla zakresu
Aby filtrować dokumenty na podstawie wybranego zakresu, można użyć operatorów `"ge"` i `"lt"` filtrów w wyrażeniu dwuczęściowym, które definiuje punkty końcowe zakresu. Na przykład, jeśli wybierzesz zakres 10-25 dla `listPrice` `$filter=listPrice ge 10 and listPrice lt 25`pola, filtr będzie . W przykładowym kodzie wyrażenie filtru używa **priceFrom** i **priceTo** parametry, aby ustawić punkty końcowe. 

  ![Zapytanie o zakres wartości](media/search-faceted-navigation/Facet-6-buildfilter.PNG "Zapytanie o zakres wartości")

<a name="geofacets"></a> 

## <a name="filter-based-on-distance"></a>Filtr na podstawie odległości
Często są widoczne filtry ułatwiające wybór sklepu, restauracji lub miejsca docelowego na podstawie bliskości bieżącej lokalizacji. Chociaż ten typ filtru może wyglądać jak nawigacja fasetowana, jest to tylko filtr. Wspominamy o tym tutaj dla tych z Was, którzy szukają specjalnie porady wdrożeniowej dla tego konkretnego problemu projektowego.

Istnieją dwie funkcje geoprzestrzenne w usłudze Azure Cognitive Search, **geo.distance** i **geo.intersects.**

* Funkcja **geo.distance** zwraca odległość w kilometrach między dwoma punktami. Jeden punkt to pole, a drugi jest stałą przekazywany jako część filtru. 
* Funkcja **geo.intersects** zwraca wartość true, jeśli dany punkt znajduje się w obrębie danego wielokąta. Punkt jest polem, a wielokąt jest określony jako stała lista współrzędnych przekazanych jako część filtru.

Przykłady filtrów można znaleźć w [składni wyrażenia OData (Azure Cognitive Search).](query-odata-filter-orderby-syntax.md)

<a name="tryitout"></a>

## <a name="try-the-demo"></a>Wypróbuj wersję demonstracyjną
Prezentacja portalu azure cognitive search job portal zawiera przykłady, do których odwołuje się w tym artykule.

-   Zobacz i przetestuj działające demo w trybie online w [witrynie Azure Cognitive Search Job Portal Demo](https://aka.ms/azjobsdemo).

-   Pobierz kod z [repozytorium przykładów platformy Azure w usłudze GitHub](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs).

Podczas pracy z wynikami wyszukiwania obserwuj adres URL zmian w konstrukcji kwerendy. Ta aplikacja dzieje się dołączyć aspekty do identyfikatora URI, jak wybrać każdy z nich.

1. Aby korzystać z funkcji mapowania aplikacji demonstracyjnej, pobierz klucz Mapy Bing z [Centrum deweloperów map Bing](https://www.bingmapsportal.com/). Wklej go nad istniejącym `index.cshtml` kluczem na stronie. Ustawienie `BingApiKey` w `Web.config` pliku nie jest używane. 

2. Uruchom aplikację. Skorzystaj z opcjonalnej wycieczki lub odrzuć okno dialogowe.
   
3. Wprowadź wyszukiwany termin, na przykład "analityk", i kliknij ikonę Wyszukiwania. Kwerenda jest wykonywana szybko.
   
   Struktura nawigacji aspektowej jest również zwracana z wynikami wyszukiwania. Na stronie wyników wyszukiwania struktura nawigacji aspektowej zawiera liczby dla każdego wyniku aspektu. Nie są wybierane żadne aspekty, więc zwracane są wszystkie pasujące wyniki.
   
   ![Wyniki wyszukiwania przed wybraniem aspektów](media/search-faceted-navigation/faceted-search-before-facets.png "Wyniki wyszukiwania przed wybraniem aspektów")

4. Kliknij tytuł firmy, lokalizację lub minimalną pensję. Aspekty były zerowe podczas wyszukiwania początkowego, ale gdy przyjmują wartości, wyniki wyszukiwania są przycinane z elementów, które nie są już zgodne.
   
   ![Wyniki wyszukiwania po wybraniu aspektów](media/search-faceted-navigation/faceted-search-after-facets.png "Wyniki wyszukiwania po wybraniu aspektów")

5. Aby wyczyścić zapytania aspektowe, dzięki czemu można wypróbować `[X]` różne zachowania kwerendy, kliknij po wybranych aspektów, aby wyczyścić aspekty.
   
<a name="nextstep"></a>

## <a name="learn-more"></a>Dowiedz się więcej
Obejrzyj [usługę Azure Cognitive Search Deep Dive](https://channel9.msdn.com/Events/TechEd/Europe/2014/DBI-B410). O 45:25, jest demo, jak zaimplementować aspekty.

Aby uzyskać więcej szczegółowych informacji na temat zasad projektowania nawigacji aspektowej, zalecamy następujące łącza:

* [Wzorce projektowe: Nawigacja fasetowana](https://alistapart.com/article/design-patterns-faceted-navigation)
* [Front End dotyczy podczas wdrażania faseted wyszukiwania - część 1](https://articles.uie.com/faceted_search2/)

