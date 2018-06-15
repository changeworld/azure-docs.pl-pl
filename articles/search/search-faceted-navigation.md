---
title: Jak zaimplementować nawigacji aspektowej w usłudze Azure Search | Dokumentacja firmy Microsoft
description: Dodawanie nawigacji Aspektowej do aplikacji, które integrują się z usługi Azure Search, usługą wyszukiwania w chmurze hostowanej w systemie Microsoft Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 3/10/2017
ms.author: heidist
ms.openlocfilehash: e00e875619e4ed6800f5739362ff0c52971f6f16
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32195298"
---
# <a name="how-to-implement-faceted-navigation-in-azure-search"></a>Jak wdrożyć nawigację aspektową w usłudze Azure Search
Nawigacji aspektowej jest mechanizm filtrowania, który udostępnia samodzielnego rozwijania szczegółów nawigacji w aplikacji wyszukiwania. Termin "nawigacji aspektowej" mogą być nieznane, ale prawdopodobnie użyto go przed. Jak w poniższym przykładzie pokazano, nawigacji aspektowej ma więcej niż kategorie używane do filtrowania wyników.

 ![Pokaz portalu zadania wyszukiwanie Azure][1]

Nawigacji aspektowej jest punkt wejścia alternatywnych do wyszukiwania. Zapewnia ona wygodnym sposobem, ręcznie wpisując wyrażenia złożone wyszukiwania. Aspekty ułatwia znajdowanie, czego szukasz, zapewniając, że nie pobieraj wyników. Deweloperzy aspekty umożliwiają udostępnianie najbardziej przydatne kryteria wyszukiwania do nawigowania Boże Twojego wyszukiwania. W aplikacji online detalicznej nawigacji aspektowej często jest utworzony za pośrednictwem marki, działów (Kącik buty), rozmiar, ceny, popularność i klasyfikacji. 

Implementowanie nawigacji aspektowej różni się między technologii wyszukiwania. W usłudze Azure Search nawigacji aspektowej jest oparty na etapie zapytania przy użyciu pól, które uprzednio przypisane w schemat.

-   W zapytaniach, które tworzy aplikację, należy wysłać zapytanie *parametry zapytania aspektu* można pobrać wartości filtru aspekt dostępne dla tego dokumentu zestawu wyników.

-   Faktycznie przyciąć wynik dokumentu należy ustawić, aplikacja musi mieć zastosowanie `$filter` wyrażenia.

Przy projektowaniu aplikacji pisania kodu, który konstruuje zapytania stanowi część pracy. Wiele zachowania aplikacji, których można oczekiwać od nawigacji aspektowej są udostępniane przez usługi, w tym wbudowaną obsługę definiowania zakresów i pobieranie liczby wyników zestawu reguł. Usługa obejmuje również za pośrednictwem ustawień domyślnych, które pomagają uniknąć struktury niewygodna nawigacji. 

## <a name="sample-code-and-demo"></a>Przykładowy kod i wersja demonstracyjna
W tym artykule wykorzystano portalu wyszukiwania zadania, na przykład. Przykład jest implementowany jako aplikacji platformy ASP.NET MVC.

-   Zobacz i testowania pokaz pracy w [Azure Search zadania portalu pokaz](http://azjobsdemo.azurewebsites.net/).

-   Pobierz kod z [repozytorium Azure-próbek w serwisie GitHub](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs).

## <a name="get-started"></a>Rozpoczęcie pracy
Jeśli zaczynasz wyszukiwania programowanie, rozważmy nawigacji aspektowej najlepiej zawiera możliwości samodzielnego wyszukiwania. Jest to typ przechodzenia wyszukiwania zależy, wstępnie zdefiniowanych filtrów, używany do szybkiego zawężanie dół wyników wyszukiwania za pomocą akcji punktu i kliknij. 

### <a name="interaction-model"></a>Model interakcji

Środowiska wyszukiwania w nawigacji aspektowej jest iteracyjną, więc warto zacząć od zrozumienia go jako sekwencja zapytań, które ujawniać w odpowiedzi na działania użytkownika.

Punkt początkowy jest strony aplikacji, która dostarcza nawigacji aspektowej zwykle umieszczane w obwodzie. Nawigacji aspektowej jest często strukturze drzewa z pól wyboru dla każdej wartość lub tekst aktywne. 

1. Zapytanie wysyłane do usługi Azure Search określa strukturę nawigacji aspektowej za pośrednictwem jednego lub więcej parametrów zapytania zestawu reguł. Na przykład zapytanie może obejmować `facet=Rating`, być może z `:values` lub `:sort` opcję, aby ulepszyć prezentacji.
2. Warstwa prezentacji renderuje stronę wyszukiwania, która zapewnia nawigacji aspektowej przy użyciu aspektami określonymi dla żądania.
3. Podana struktura nawigacji aspektowej, która obejmuje klasyfikacji, możesz kliknąć przycisk "4" Aby wskazać, że mają być wyświetlane tylko produkty z klasyfikacją z 4 lub nowszej. 
4. W odpowiedzi skrypt aplikacja wysyła kwerendę, która obejmuje `$filter=Rating ge 4` 
5. Strony, zawierającej zestaw wyników obniżona, zawierający tylko te elementy, które spełniają kryteria nowe aktualizacje warstwę prezentacji (w tym przypadku produktów oceną 4 i nowsze).

Zestaw reguł, to parametr zapytania, ale nie należy mylić z zapytania w danych wejściowych. Nigdy nie jest używany jako kryteriów wyboru w zapytaniu. Zamiast tego należy traktować aspektu parametry zapytania jako danych wejściowych w strukturze nawigacji Wstecz wchodzącej w odpowiedzi. Dla każdego parametru zapytania aspektu podane przez użytkownika usługi Azure Search oblicza liczbę dokumenty są częściowe wyniki dla każdej wartości aspektu.

Powiadomienie `$filter` w kroku 4. Filtr jest ważnym aspektem nawigacji aspektowej. Mimo że aspekty i filtry są niezależne od w interfejsie API, należy zarówno do dostarczenia środowisko, które mają. 

### <a name="app-design-pattern"></a>Wzorzec projektowania aplikacji

W kodzie aplikacji wzorzec jest użycie aspektu parametry zapytania do zwrócenia strukturze nawigacji aspektowej wraz z aspektu wyników, a także wyrażenia $filter.  Wyrażenie filtru obsługuje zdarzenie click na wartość zestawu reguł. Pomyśl o `$filter` zwracane wyrażenie jako kodzie rzeczywiste przycinanie wyników wyszukiwania do warstwy prezentacji. Biorąc pod uwagę aspektu kolorów, klikając kolor czerwony jest implementowane za pośrednictwem `$filter` wyrażenia, który wybiera tylko te elementy, które mają kolor czerwony. 

### <a name="query-basics"></a>Podstawowe informacje o zapytań

W usłudze Azure Search, żądanie jest określany za pośrednictwem jednego lub więcej parametrów zapytania (zobacz [dokumenty wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) opis każdego z nich). Żaden z parametrów zapytania nie jest wymagana, ale użytkownik musi mieć co najmniej jeden w kolejności dla zapytania jest nieprawidłowy.

Dokładność, zrozumienie, jak możliwość filtrowania trafień nie ma znaczenia, odbywa się za pośrednictwem jednego lub obu tych wyrażeń:

-   **Szukaj =**  
    Wartość tego parametru oznacza akceptację wyrażenie. Może być elementu text lub wyrażenie złożone wyszukiwania, które zawiera wiele warunków i operatory. Na serwerze wyrażenia służy do wyszukiwania pełnotekstowego, zapytanie pól z możliwością wyszukiwania w indeksie dopasowanie warunki zwracania wyników według rangi. Jeśli ustawisz `search` równej null, zapytania wykonywania znajduje się nad całym indeksie (czyli `search=*`). W tym przypadku inne elementy zapytania, takie jak `$filter` lub oceniania profilu, są podstawowym czynników wpływających na dokumenty, które są zwracane `($filter`) i w jakiej kolejności (`scoringProfile` lub `$orderby`).

-   **$filter =**  
    Filtr jest zaawansowanym mechanizmem służącym do ograniczania rozmiaru wyniki wyszukiwania na podstawie wartości atrybutów określonego dokumentu. A `$filter` jest obliczana jako pierwsza, następuje logiki tworzenie aspektów, które generuje dostępne wartości oraz liczby odpowiednich dla każdej wartości

Wyrażenia złożone wyszukiwania zmniejszenie wydajności zapytania. Jeśli to możliwe, należy używać wyrażeń dobrze skonstruowany filtrowania zwiększyć dokładność i zwiększyć wydajność przeszukiwania.

Aby lepiej zrozumieć, jak filtr dodaje większą dokładność, porównaj wyrażenia złożone zawierającej wyrażenie filtru:

-   `GET /indexes/hotel/docs?search=lodging budget +Seattle –motel +parking`
-   `GET /indexes/hotel/docs?search=lodging&$filter=City eq ‘Seattle’ and Parking and Type ne ‘motel’`

Oba zapytania są prawidłowe, ale druga jest wyższego poziomu, jeśli szukasz z systemem innym niż motele postojowego w Seattle.
-   Pierwsza kwerenda zależy od tych słów są wymienione lub nie są wymienione w polach ciąg, takie jak nazwa, opis i inne pola zawierające dane z możliwością wyszukiwania.
-   Drugi zapytanie wyszukuje dokładne dopasowania na danych strukturalnych i prawdopodobnie będzie bardziej dokładne.

Upewnij się, że każda akcja użytkownika za pośrednictwem struktury nawigacji aspektowej towarzyszy zawęzić wyniki wyszukiwania w aplikacji, które obejmują nawigacji aspektowej. Aby zawęzić wyniki, użyj wyrażenia filtru.

<a name="howtobuildit"></a>

## <a name="build-a-faceted-navigation-app"></a>Tworzenie aplikacji nawigacji aspektowej
Zaimplementowaniem nawigacji aspektowej z usługi Azure Search w kodzie aplikacji, która tworzy żądania wyszukiwania. Nawigacji aspektowej zależy od elementów schematu wcześniej zdefiniowany.

Wstępnie zdefiniowane w wyszukiwaniu indeks jest `Facetable [true|false]` atrybutu indeksu, ustaw dla wybranych pól, aby włączyć lub wyłączyć ich użycie w strukturze nawigacji aspektowej. Bez `"Facetable" = true`, nie można użyć pola w nawigacji zestawu reguł.

Warstwa prezentacji w kodzie udostępnia środowisko użytkownika. Powinien on listę elementów składowych nawigacji aspektowej, takie jak etykiety, wartości pola wyboru i liczby. Interfejs API REST usługi Azure Search jest niezależny od platformy, należy więc niezależnie od języka i ma platformy. Ważne jest uwzględnienie obsługuje odświeżania przyrostowych, o zaktualizowany stan interfejsu użytkownika, ponieważ wybrano każdego aspektu dodatkowe elementy interfejsu użytkownika. 

Na etapie zapytania kod aplikacji tworzy żądanie zawiera `facet=[string]`, parametr żądania, który zawiera pola do zestawu reguł przez. Kwerenda może mieć wiele aspektów, takich jak `&facet=color&facet=category&facet=rating`, każda z nich oddzielone ampersand (&) znaków.

Kod aplikacji należy również tworzyć `$filter` wyrażenia do obsługi zdarzeń kliknij w nawigacji aspektowej. A `$filter` ogranicza wyniki wyszukiwania, za pomocą wartości aspektu jako kryteria filtrowania.

Usługa Azure Search zwraca wyniki wyszukiwania na podstawie co najmniej jeden warunków, które należy wprowadzić, wraz z aktualizacji w strukturze nawigacji aspektowej. W usłudze Azure Search nawigacji aspektowej jest konstrukcji poziomie, z wartości aspektu liczby i liczbę wyników znajdują się dla każdej z nich.

W poniższych sekcjach traktujemy bliższe spojrzenie na sposób tworzenia każdej części.

<a name="buildindex"></a>

## <a name="build-the-index"></a>Tworzenie indeksu
Tworzenie aspektów jest włączona na podstawie przez pola w indeksie, za pomocą tego atrybutu indeksu: `"Facetable": true`.  
Wszystkie typy pól, które prawdopodobnie mogą być używane w nawigacji aspektowej są `Facetable` domyślnie. Takie typy pól `Edm.String`, `Edm.DateTimeOffset`i wszystkie typy pól liczbowych (zasadniczo wszystkie typy pól są tworzenie aspektów, z wyjątkiem `Edm.GeographyPoint`, nie można użyć w nawigacji aspektowej). 

Podczas tworzenia indeksu, najlepszym rozwiązaniem w nawigacji aspektowej jest jawnie wyłączyć tworzenie aspektów dla pola, które nie mogą być używane jako zestaw reguł.  W szczególności pól ciągów dla pojedynczych wartości, takich jak identyfikator lub nazwa produktu, należy wybrać opcję `"Facetable": false` przed ich użyciem przypadkowemu (i nieskuteczne) w nawigacji aspektowej. Włączanie tworzenie aspektów poza których nie należy go pomaga zachować mały rozmiar indeksu i zwykle zwiększa wydajność.

Poniżej przedstawiono część schematu dla aplikacji przykładowej pokaz portalu zadania przycięty niektórych atrybutów, aby zmniejszyć rozmiar:

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

Jak widać w schemacie próbki `Facetable` jest wyłączona w przypadku pól ciągów, które nie powinny być używane jako aspektów, takich jak wartości Identyfikatora. Włączanie tworzenie aspektów poza których nie należy go pomaga zachować mały rozmiar indeksu i zwykle zwiększa wydajność.

> [!TIP]
> Jako najlepsze rozwiązanie obejmuje pełny zestaw atrybutów indeksu dla każdego pola. Mimo że `Facetable` jest domyślnie włączona dla prawie wszystkich pól, celowo ustawienie każdego atrybutu może pomóc w przemyślenie skutki każdej decyzji schematu. 

<a name="checkdata"></a>

## <a name="check-the-data"></a>Sprawdzenie, czy dane
Jakość danych ma bezpośredni wpływ na czy strukturze nawigacji aspektowej zostaje zgodnie z oczekiwaniami do. Wpływa to również na łatwość tworzenia filtry, aby zmniejszyć zestaw wyników.

Jeśli chcesz aspektu marki lub ceny każdego dokumentu powinna zawierać wartości dla *BrandName* i *ProductPrice* które są prawidłowe, spójności i produktywności jako opcja filtra.

Poniżej przedstawiono kilka przypomnienia elementów, które należy usunąć dla:

* Dla każdego pola, który ma zostać aspektu przez Spróbuj odpowiedzieć sobie zawiera wartości, które są odpowiednie jako filtry w samodzielnego wyszukiwania. Wartości powinna być krótka, opisową i dostatecznie wyróżniające do oferują możliwość wyboru między opcjami konkurencyjnych.
* Błędów pisowni lub prawie pasujących wartości. Jeśli aspektu na kolor i wartości pola zawierają pomarańczowy i Ornage (błąd), zestaw reguł na podstawie pola koloru odebrania czy obu.
* Wielkość tekstu mieszanego można także zrobić spustoszenie w nawigacji aspektowej pomarańczowy i kolor pomarańczowy występujących w dwóch różnych wartości. 
* Pojedynczy i mnogą wersję taką samą wartość może spowodować aspektu osobne dla każdego.

Jak można wyobrazić sobie starannością w przygotowaniu danych jest ważnym aspektem skutecznego nawigacji aspektowej.

<a name="presentationlayer"></a>

## <a name="build-the-ui"></a>Tworzenie interfejsu użytkownika
Pracy z warstwą prezentacji może pomóc odkryć wymagania, które mogły pominięte, w przeciwnym razie i zrozumieć, jakie funkcje są niezbędne do wyników wyszukiwania.

W nawigacji aspektowej strony sieci web lub aplikacji wyświetla strukturze nawigacji aspektowej wykrywa danych wejściowych użytkownika na stronie i wstawia zmienione elementy. 

Dla aplikacji sieci web AJAX to powszechnie używane w warstwie prezentacji, ponieważ umożliwia odświeżanie zmiany przyrostowe. Można także użyć programu ASP.NET MVC lub inne platformy wizualizacji, które mogą połączyć się z usługi Azure Search za pośrednictwem protokołu HTTP. Przykładowej aplikacji, do których odwołuje się w tym artykule — **Azure Search zadania portalu pokaz** — się aplikacji platformy ASP.NET MVC.

W przykładzie nawigacji aspektowej jest oparty na stronę wyników wyszukiwania. Poniższy przykład pobranych z `index.cshtml` strona wyników pliku przykładowej aplikacji, struktura pokazuje statycznego HTML do wyświetlania na wyszukiwanie nawigacji aspektowej. Lista zestawów reguł jest zbudowany lub odbudować dynamicznie podczas przesyłania wyszukiwanego terminu, lub wybierz lub wyczyść zestaw reguł.

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

Poniższy fragment kodu z `index.cshtml` stronę dynamicznie tworzy HTML do wyświetlenia pierwszego zestawu reguł biznesowych tytuł. Podobne funkcje dynamicznie utworzyć kod HTML pod kątem innych aspektów. Każdego aspektu ma etykiety i liczba, która wyświetla liczbę elementów znalezionych dla tego wyniku aspekt.

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
> Podczas projektowania strony wyników wyszukiwania, pamiętaj, aby dodać mechanizm wyczyszczenie aspektami. Jeśli dodajesz pola wyboru, można łatwo Zobacz jak wyczyścić filtry. Dla innych układów może być konieczne wzorzec stron nadrzędnych lub innym rozwiązaniem creative. Na przykład w portalu wyszukiwania zadania przykładowej aplikacji, można kliknąć `[X]` po wybranego aspektu wyczyść aspekt.

<a name="buildquery"></a>

## <a name="build-the-query"></a>Tworzenie zapytania
Kod, który zapisu do tworzenia zapytania należy określić wszystkie części prawidłowe zapytanie, takich jak wyrażenia wyszukiwania, aspekty, filtry oceniania profile — wszystko można sformułować żądanie. W tej sekcji możemy Poznaj gdzie aspekty mieści się w zapytaniu i używania filtrów z aspektami dostarczyć zestaw wyników zmniejszenie.

Zwróć uwagę, że aspekty są integralną w tej przykładowej aplikacji. Środowisko wyszukiwania pokaz portalu zadania jest zaprojektowana dla nawigacji aspektowej i filtry. Wyraźne umieszczania nawigacji aspektowej na stronie przedstawiono jego znaczenie. 

Przykładem jest często dobrym miejscem do rozpoczęcia. Poniższy przykład pobranych z `JobsSearch.cs` pliku, żądanie, które tworzy aspektu nawigacji na podstawie tytuł biznesowych, lokalizacji, Księgowanie typu i wynagrodzenie co najmniej kompilacji. 

```cs
SearchParameters sp = new SearchParameters()
{
  ...
  // Add facets
  Facets = new List<String>() { "business_title", "posting_type", "level", "salary_range_from,interval:50000" },
};
```

Parametr zapytania aspektu ma wartość pola i w zależności od typu danych, mogą być dodatkowe parametry funkcjom rozdzielana przecinkami lista zawierająca `count:<integer>`, `sort:<>`, `interval:<integer>`, i `values:<list>`. Lista wartości jest obsługiwana dla danych liczbowych, podczas konfigurowania zakresów. Zobacz [wyszukiwania dokumentów (interfejsu API usługi Azure Search)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) szczegóły użycia.

Wraz z aspektów żądanie sformułowane przez aplikację powinny również tworzyć filtry służące do zawęzić zestaw dokumentów kandydata na podstawie zaznaczenia wartość aspektu. Magazynu roweru nawigacji aspektowej zawiera wskazówki dotyczące pytania, takich jak *jakie kolory, producenci i typy rowerów są dostępne?*. Filtrowanie odpowiedzi na pytania, takie jak *które dokładne rowerów są oznaczone kolorem czerwonym, rowerów górski, w tym ceny zakresu?*. Po kliknięciu przycisku "Red", aby wskazać, powinny być wyświetlane tylko czerwony produktów zawiera zapytanie dalej wysyła aplikacji `$filter=Color eq ‘Red’`.

Poniższy fragment kodu z `JobsSearch.cs` strony dodaje wybrane tytuł biznesowych do filtru w przypadku wybrania wartości z zestawu reguł biznesowych tytułu.

```cs
if (businessTitleFacet != "")
  filter = "business_title eq '" + businessTitleFacet + "'";
```

<a name="tips"></a> 

## <a name="tips-and-best-practices"></a>Wskazówki i najlepsze rozwiązania

### <a name="indexing-tips"></a>Indeksowanie porady
**Poprawy efektywności indeksu, jeśli nie korzystasz z pola wyszukiwania**

Jeśli aplikacja używa nawigacji aspektowej wyłącznie (to znaczy, nie pole wyszukiwania), można oznaczyć jako `searchable=false`, `facetable=true` do tworzenia mniejszych indeksu. Ponadto indeksowania występuje tylko dla wartości aspektu całego, nie dzielenie wyrazów lub indeksowania składniki word wielu wartości.

**Określ pola, które może służyć jako aspekty**

Odwołaj się, że schemat indeksu określa pola, które są dostępne do użycia jako zestaw reguł. Zakładając, że pole jest tworzenie aspektów, zapytanie określa, które pola do zestawu reguł przez. Pole, według której jesteś tworzenie aspektów zawiera wartości, które są wyświetlane poniżej etykiety. 

Wartości, które pojawiają się w każdej etykiety są pobierane z indeksu. Na przykład, jeśli pole aspektu jest *kolor*, dostępne do filtrowania dodatkowe wartości są wartości dla tego pola - czerwony, czarne i tak dalej.

Liczbowe DateTime wartości i tylko wtedy, można jawnie ustawione wartości w polu aspektu (na przykład `facet=Rating,values:1|2|3|4|5`). Lista wartości jest dozwolone dla tych typów pola uprościć rozdzielenie aspektu wyniki do zakresy ciągłe (albo zakresy na podstawie wartości liczbowe lub okresów). 

**Domyślnie może istnieć tylko jeden poziom poufności informacji** 

Jak wspomniano, istnieje bezpośrednią obsługę zagnieżdżania aspekty w hierarchii. Domyślnie nawigacji aspektowej w usłudze Azure Search obsługuje tylko jeden poziom filtrów. Jednak istnieć obejścia. Może zakodować strukturę hierarchiczną aspektu w `Collection(Edm.String)` z jednego wpisu punktu danej hierarchii. Wdrażanie tego rozwiązania wykracza poza zakres tego artykułu. 

### <a name="querying-tips"></a>Wykonywanie zapytania porady
**Sprawdzanie poprawności pola**

W przypadku tworzenia listy aspekty dynamicznie oparte na danych wejściowych użytkownika niezaufanych, należy zweryfikować, że nazwy pól aspektowej są prawidłowe. Lub wprowadzić nazwy podczas kompilowania przy użyciu adresów URL `Uri.EscapeDataString()` .NET lub równoważnej w wybranej platformy.

### <a name="filtering-tips"></a>Wskazówki dotyczące filtrowania
**Pozwala zwiększyć dokładność wyszukiwania z filtrami**

Używanie filtrów. Jeśli użytkownik korzysta tylko wyrażeniach wyszukiwania wyłącznie danych może spowodować, że dokument ma zostać zwrócona, że nie znajduje się wartość aspektu dokładne w jednym z jego pól.

**Zwiększyć wydajność wyszukiwania z filtrami**

Filtry zawęzić zestaw dokumentów kandydata do wyszukiwania i wykluczyć je z klasyfikacji. Jeśli masz dużego zestawu dokumentów, często przy użyciu selektywnego aspektu przechodzenia zapewnia lepszą wydajność.
  
**Tylko szlifowanych pola filtru**

W aspektowej przechodzenia zwykle można tylko obejmują dokumentów, których wartość aspektu w określonym polu (aspektowej), nie w dowolnym miejscu we wszystkich pól z możliwością wyszukiwania. Dodawanie filtru wzmacnia pola docelowego, kierując usługi wyszukiwania tylko w polu aspektowej dla pasującej wartości.

**TRIM aspekt wyniki z więcej filtrów**

Aspekt wyniki są znalezionych w wynikach wyszukiwania, spełniających warunek aspektu dokumentów. W poniższym przykładzie w wynikach wyszukiwania *chmura obliczeniowa*, 254 elementy mają także *wewnętrzny specyfikacji* jako typu zawartości. Elementy nie są zawsze wykluczają się wzajemnie. Jeśli element nie spełnia kryteria obu filtrów, jest liczony w każdej z nich. To duplikatów, jest możliwe, gdy tworzenie aspektów na `Collection(Edm.String)` pola, które są często używane do implementowania znakowanie dokumentu.

        Search term: "cloud computing"
        Content type
           Internal specification (254)
           Video (10) 

Ogólnie rzecz biorąc Jeśli okaże się, że wyniki aspektu spójnie są za duże, zalecamy, aby dodać więcej filtrów umożliwić użytkownikom dodatkowe opcje dotyczące zawężanie wyszukiwania.

### <a name="tips-about-result-count"></a>Porady dotyczące liczba wyników

**Ogranicz liczbę elementów w obszarze nawigacji aspekt**

Dla każdego pola aspektowej w drzewie nawigacji brak domyślnego limitu 10 wartości. To ustawienie domyślne sens struktury nawigacji, ponieważ utrzymuje wartości listy do rozmiarów. Domyślne można przesłonić, przypisywanie wartości do zliczenia.

* `&facet=city,count:5` Określa, że tylko pierwszych pięć miast znalezione w szczycie rankingu wyniki są zwracane jako wynik zestaw reguł. Należy wziąć pod uwagę przykładowe zapytanie z wyszukiwany termin "lotnisku" i 32 dopasowań. Jeśli zapytanie określa `&facet=city,count:5`, tylko pierwsze pięć unikatowy miejscowości z najbardziej dokumentów w wynikach wyszukiwania są uwzględnione w wynikach zestawu reguł.

Powiadomienie różnicy między aspektu wyników i wyników wyszukiwania. Wyniki wyszukiwania są wszystkie dokumenty, które odpowiada na kwerendę. Aspekt wyniki są dopasowań dla każdej wartości aspektu. W tym przykładzie wyniki wyszukiwania zawierają nazwy Miasto, które nie są na liście aspektu klasyfikacji (5 w tym przykładzie). Wyniki, które są odfiltrowywane przez nawigacji aspektowej stają się widoczne, gdy wyczyść aspekty, lub wybierz inne aspekty oprócz miasta. 

> [!NOTE]
> Omawianie `count` Jeśli istnieje więcej niż jednego typu może być mylące. Poniższa tabela zawiera krótkie podsumowanie sposobu używania termin interfejsu API usługi Azure Search, przykładowy kod i dokumentacji. 

* `@colorFacet.count`<br/>
  W kodzie prezentacji parametru liczba powinna zostać wyświetlona na aspekt umożliwia wyświetlenie liczby wyników aspekt. W wynikach aspektu liczba wskazuje liczbę dokumentów, które odpowiadają na aspektu termin lub zakresu.
* `&facet=City,count:12`<br/>
  W zapytaniu aspektu liczba można ustawić na wartość.  Wartość domyślna to 10, ale można ją ustawić większej lub mniejszej. Ustawienie `count:12` pobiera pasuje do góry 12 w aspektu wyniki według liczby dokumentu.
* „`@odata.count`”<br/>
  W odpowiedzi na zapytanie ta wartość wskazuje liczbę pasujących elementów w wynikach wyszukiwania. Średnia jest większy niż suma wszystkich wyników aspektu połączone z powodu obecności elementów, które pasują do szukanego terminu, ale ma odpowiedników wartość zestawu reguł.

**Pobierz liczy się w wynikach aspekt**

Po dodaniu filtr do zapytania aspektowej możesz chcieć zachować instrukcji aspektu (na przykład `facet=Rating&$filter=Rating ge 4`). Z technicznego punktu widzenia aspektu = klasyfikacji nie jest wymagane, ale on zwraca liczbę wartości aspektu klasyfikacje, 4 lub nowszy. Na przykład jeśli zapytanie zawiera filtr większe lub równe "4", po kliknięciu przycisku "4" liczby są zwracane dla każdej klasyfikacji, który jest 4 lub nowszy.  

**Upewnij się, że get aspekt dokładne liczby**

W pewnych okolicznościach, można znaleźć aspektu liczby nie są zgodne zestawów wyników (zobacz [nawigacji Aspektowej w usłudze Azure Search (wpis na forum)](https://social.msdn.microsoft.com/Forums/azure/06461173-ea26-4e6a-9545-fbbd7ee61c8f/faceting-on-azure-search?forum=azuresearch)).

Aspekt liczniki mogą być niedokładne z uwagi na architekturę dzielenia na fragmenty. Każdy indeks ma wiele odłamków i każdego niezależnych raporty aspektami top N według liczby dokumentów, które następnie są łączone w pojedynczy wynik. Jeśli niektóre odłamków ma wiele pasujących wartości, podczas gdy inne zawierają mniej, może się okazać czy niektóre wartości aspektu brakuje lub w obszarze zliczany w wynikach.

Mimo że to zachowanie można zmienić w dowolnym momencie, jeśli wystąpi ten problem dzisiaj, można obejść go przez sztucznie pompowania licznika:<number> do wielu, aby wymusić pełne raportowania z każdym niezależnego fragmentu. Jeśli wartość licznika: jest większa niż lub równa liczbie unikatowych wartości w polu, masz gwarancję prawidłowych wyników. Jednak w przypadku wysokiej liczby dokumentów jest zmniejszenie wydajności, więc należy rozważnie Użyj tej opcji.

### <a name="user-interface-tips"></a>Wskazówki dotyczące interfejsu użytkownika
**Dodawanie etykiet dla każdego pola w aspekt nawigacji**

Etykiety są zazwyczaj definiowane w formularzu lub HTML (`index.cshtml` w przykładowej aplikacji). Brak żadnego interfejsu API w usłudze Azure Search etykiet nawigacji zestawu reguł lub innych metadanych.

<a name="rangefacets"></a>

## <a name="filter-based-on-a-range"></a>Filtr na podstawie zakresu
Tworzenie aspektów zakresach wartości jest typowe wymagania aplikacji wyszukiwania. Zakresy są obsługiwane dla danych liczbowych i wartości daty/godziny. Więcej o każde podejście w [wyszukiwania dokumentów (interfejsu API usługi Azure Search)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).

Wyszukiwanie Azure ułatwia konstrukcji zakresu, zapewniając dwa podejścia do przetwarzania danych zakresu. Dla obu podejść usługi Azure Search tworzy odpowiednie zakresy podane dane wejściowe, dostarczona przez Ciebie. Na przykład jeśli określisz wartości z zakresu od 10 | 20 | 30, automatycznie tworzy zakres 0-10, 10-20, 20 – 30. Aplikację można opcjonalnie usunąć żadnych interwałów, które są puste. 

**Metoda 1: Użyj parametru Interwał**  
Aby ustawić aspekty ceny w przyrostach 10 zł, należy określić: `&facet=price,interval:10`

**Metoda 2: Użyj listy wartości**  
Dla danych liczbowych używając listy wartości.  Należy wziąć pod uwagę zakres aspektu `listPrice` pola renderowane w następujący sposób:

  ![Lista przykładów wartości][5]

Aby określić zakres aspektu, tak jak w poprzednim zrzut ekranu, należy użyć listy wartości:

    facet=listPrice,values:10|25|100|500|1000|2500

Każdego zakresu jest utworzony przy użyciu 0 jako punkt początkowy, wartości na liście jako punktu końcowego, a następnie przycinana zakresu poprzedniego można utworzyć osobne odstępach czasu. Usługa wyszukiwanie Azure wykonuje te czynności w ramach nawigacji aspektowej. Nie trzeba napisać kod dla każdego interwału struktury.

### <a name="build-a-filter-for-a-range"></a>Utworzyć filtr dla zakresu
Aby filtrować dokumentów na podstawie zakresu zostanie wybrana, można użyć `"ge"` i `"lt"` operatorów w wyrażeniu dwóch elementów, który definiuje punkty końcowe zakresu filtru. Na przykład, jeśli zakres 10-25 `listPrice` polu Filtr byłby `$filter=listPrice ge 10 and listPrice lt 25`. W przykładowym kodzie używa wyrażenia filtru **priceFrom** i **priceTo** parametrów, aby ustawić punkty końcowe. 

  ![Zapytanie dla zakresu wartości][6]

<a name="geofacets"></a> 

## <a name="filter-based-on-distance"></a>Filtr oparty na odległość
Typowe wyświetlić filtry ułatwiające wybranego magazynu, restauracji lub oparte na jego bliskości bieżącej lokalizacji docelowej. Chociaż ten typ filtru może wyglądać nawigacji aspektowej, jest tylko filtr. Możemy tu wspomina dla osób, które kto w szczególności szukasz implementacji wskazówki dotyczące tego problemu konkretnego projektu.

Dostępne są dwie funkcje dane geograficzne w usłudze Azure Search **geo.distance** i **geo.intersects**.

* **Geo.distance** funkcja zwraca w kilometrach odległość między dwoma punktami. Pole jest jeden punkt i innych jest stałą przekazanych jako część filtru. 
* **Geo.intersects** funkcja zwraca wartość true, jeśli dany punkt znajduje się w danym wielokąta. Pole jest punkt i Wielokąt jest określony jako stała lista współrzędne przekazanych jako część filtru.

Można znaleźć przykłady filtru w [składni wyrażeń OData (Azure Search)](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

<a name="tryitout"></a>

## <a name="try-the-demo"></a>Wypróbuj wersję demonstracyjną
Pokaz Portal Azure Search zadania zawiera przykłady w tym artykule.

-   Zobacz i testowania pokaz pracy w [Azure Search zadania portalu pokaz](http://azjobsdemo.azurewebsites.net/).

-   Pobierz kod z [repozytorium Azure-próbek w serwisie GitHub](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs).

Podczas pracy z wynikami wyszukiwania, obejrzyj adres URL dla zmian w konstrukcji zapytania. Aby dołączyć aspekty do identyfikatora URI podczas zaznaczania poszczególnych odbywa się tej aplikacji.

1. Aby korzystać z funkcji mapowania aplikacji demonstracyjnej, uzyskać klucz usługi mapy Bing z [Centrum deweloperów map Bing](https://www.bingmapsportal.com/). Wklej go za pośrednictwem istniejącego klucza w `index.cshtml` strony. `BingApiKey` w `Web.config` plik nie jest używany. 

2. Uruchom aplikację. Zapoznaj się z narzędziem opcjonalne lub zamknąć okno dialogowe.
   
3. Wprowadź wyszukiwany termin, takie jak "analityka" i kliknij ikonę wyszukiwania. Zapytanie wykonuje szybko.
   
   Struktura nawigacji aspektowej jest także zwracany w wynikach wyszukiwania. W wynikach wyszukiwania struktura nawigacji aspektowej zawiera liczniki dla każdego aspektu wyniku. Nie aspekty nie wybrano, więc wszystkie pasujących wyników są zwracane.
   
   ![Wyniki wyszukiwania przed wybraniem aspektów][11]

4. Kliknij tytuł biznesowych, lokalizacji lub minimalna wynagrodzenia. Aspekty była zerowa na początkowe wyszukiwanie, ale ponieważ one na wartościach, wyniki wyszukiwania są usuwane z elementów, które nie odpowiadają.
   
   ![Wyniki wyszukiwania po wybraniu aspektów][12]

5. Aby wyczyścić aspektowej zapytania, dzięki czemu można spróbować zachowania inne zapytanie, kliknij `[X]` po wybranego aspektami wyczyść aspektami.
   
<a name="nextstep"></a>

## <a name="learn-more"></a>Dowiedz się więcej
Obejrzyj [nowości wyszukiwanie Azure](http://channel9.msdn.com/Events/TechEd/Europe/2014/DBI-B410). W 45:25 Brak pokaz implementowania aspektami.

Aby uzyskać więcej szczegółowych informacji o zasadach projektowania dla nawigacji aspektowej zaleca się następujące łącza:

* [Projektowanie dla szlifowanych wyszukiwania](http://www.uie.com/articles/faceted_search/)
* [Wzorce projektowania: Poufności](http://alistapart.com/article/design-patterns-faceted-navigation)


<!--Anchors-->
[How to build it]: #howtobuildit
[Build the presentation layer]: #presentationlayer
[Build the index]: #buildindex
[Check for data quality]: #checkdata
[Build the query]: #buildquery
[Tips on how to control faceted navigation]: #tips
[Faceted navigation based on range values]: #rangefacets
[Faceted navigation based on GeoPoints]: #geofacets
[Try it out]: #tryitout

<!--Image references-->
[1]: ./media/search-faceted-navigation/azure-search-faceting-example.PNG
[2]: ./media/search-faceted-navigation/Facet-2-CSHTML.PNG
[3]: ./media/search-faceted-navigation/Facet-3-schema.PNG
[4]: ./media/search-faceted-navigation/Facet-4-SearchMethod.PNG
[5]: ./media/search-faceted-navigation/Facet-5-Prices.PNG
[6]: ./media/search-faceted-navigation/Facet-6-buildfilter.PNG
[7]: ./media/search-faceted-navigation/Facet-7-appstart.png
[8]: ./media/search-faceted-navigation/Facet-8-appbike.png
[9]: ./media/search-faceted-navigation/Facet-9-appbikefaceted.png
[10]: ./media/search-faceted-navigation/Facet-10-appTitle.png
[11]: ./media/search-faceted-navigation/faceted-search-before-facets.png
[12]: ./media/search-faceted-navigation/faceted-search-after-facets.png

<!--Link references-->
[Designing for Faceted Search]: http://www.uie.com/articles/faceted_search/
[Design Patterns: Faceted Navigation]: http://alistapart.com/article/design-patterns-faceted-navigation
[Create your first application]: search-create-first-solution.md
[OData expression syntax (Azure Search)]: https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search
[Azure Search Adventure Works Demo]: https://azuresearchadventureworksdemo.codeplex.com/
[http://www.odata.org/documentation/odata-version-2-0/overview/]: http://www.odata.org/documentation/odata-version-2-0/overview/ 
[Faceting on Azure Search forum post]: ../faceting-on-azure-search.md?forum=azuresearch
[Search Documents (Azure Search API)]: https://docs.microsoft.com/rest/api/searchservice/Search-Documents

