---
title: Wyszukiwanie w sieci Web Bing jednostronicowej aplikacji sieci Web | Dokumentacja firmy Microsoft
description: Przedstawia sposób użycia interfejsu API wyszukiwania usługi Bing sieci Web w aplikacji jednej strony sieci Web.
services: cognitive-services
author: v-jerkin
manager: ehansen
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 10/04/2017
ms.author: v-jerkin
ms.openlocfilehash: f22e38a1d6ee4042684b9822b58669bed6fe29a0
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349020"
---
# <a name="tutorial-single-page-web-app"></a>Samouczek: Aplikacji jednej strony sieci Web

Interfejs API wyszukiwania usługi Bing sieci Web umożliwia wyszukiwanie w sieci Web i uzyskiwać wyniki z różnych typów istotne dla kwerendy wyszukiwania. W tym samouczku budujemy jednostronicowej aplikacji sieci Web, która używa interfejsu API wyszukiwania usługi Bing sieci Web, aby wyświetlić wyniki wyszukiwania po prawej stronie. Aplikacja zawiera składniki HTML, CSS i JavaScript.

<!-- Remove until this can be replaced with a sanitized version.
![[Single-page Bing Web Search app]](media/cognitive-services-bing-web-api/web-search-spa-demo.png)
-->

> [!NOTE]
> Nagłówki JSON i HTTP w dolnej części strony ujawnić odpowiedź w formacie JSON i informacje o żądaniu HTTP po kliknięciu. Informacje te są przydatne podczas szczegółowego eksplorowania usługi.

Samouczek aplikacji ilustruje sposób:

> [!div class="checklist"]
> * Wykonywać wywołanie interfejsu API Bing sieci Web wyszukiwania w języku JavaScript
> * Przeniesienie opcje wyszukiwania do interfejsu API wyszukiwania usługi Bing sieci Web
> * Wyświetl sieci Web, grup dyskusyjnych, obrazu i wyniki wyszukiwania wideo
> * Przejrzyj wyniki wyszukiwania
> * Dojście Bing interfejsów API i identyfikator subskrypcji klucz klienta
> * Obsługa błędów, które mogą wystąpić

Samouczek strony są całkowicie niezależne; nie używa żadnych platform zewnętrznych, arkuszy stylów lub nawet pliki obrazów. Umożliwia ona używa tylko powszechnie obsługiwane funkcje języka JavaScript i działa z bieżącymi wersjami wszystkie główne przeglądarki sieci Web.

W tym samouczku omówiono tylko wybrane części kodu źródłowego. Pełny kod źródłowy jest dostępny [na osobnej stronie](tutorial-bing-web-search-single-page-app-source.md). Skopiuj i wklej ten kod w edytorze tekstu i zapisz go jako `bing.html`.

## <a name="app-components"></a>Składniki aplikacji

Tak jak każda aplikacja sieci Web jednej strony samouczek aplikacja obejmuje trzy części:

> [!div class="checklist"]
> * HTML — definiuje struktury i zawartości strony
> * CSS — definiuje wygląd strony
> * JavaScript — określa zachowanie strony

W tym samouczku nie obejmuje większość HTML i CSS szczegółowo, ponieważ są one proste.

HTML zawierający formularz wyszukiwania, w którym użytkownik wprowadza zapytania i wybiera opcje wyszukiwania. Formularz jest połączony JavaScript, które faktycznie wykonuje wyszukiwanie według `<form>` znacznika `onsubmit` atrybutu:

```html
<form name="bing" onsubmit="return newBingWebSearch(this)">
```

`onsubmit` Obsługi zwraca `false`, co pozwala formularza są przesyłane do serwera. Kod JavaScript jest rzeczywiście pracy zbierania niezbędne informacje z formularza i wyszukiwania.

Kod HTML zawiera także działów (HTML `<div>` tagów) gdzie zostaną wyświetlone wyniki wyszukiwania.

## <a name="managing-subscription-key"></a>Zarządzanie subskrypcją klucza

Aby uniknąć konieczności dołączania klucz interfejsu API wyszukiwania usługi Bing subskrypcji w kodzie, używamy przeglądarki trwałego magazynu do przechowywania klucza. Jeśli klucz nie jest przechowywana, możemy monit o podanie klucza użytkownika i zapisze go na przyszłość. Jeśli klucz później został odrzucony przez interfejs API, możemy unieważnienie przechowywanych klucza, użytkownik będzie monitowany ponownie.

Definiujemy `storeValue` i `retrieveValue` funkcje programu wykorzystujące albo `localStorage` obiektu (Jeśli przeglądarka obsługuje) lub plik cookie. Nasze `getSubscriptionKey()` funkcja używa tych funkcji do przechowywania i pobierania klucza użytkownika.

```javascript
// cookie names for data we store
API_KEY_COOKIE   = "bing-search-api-key";
CLIENT_ID_COOKIE = "bing-search-client-id";

BING_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/search";

// ... omitted definitions of storeValue() and retrieveValue()

// get stored API subscription key, or prompt if it's not found
function getSubscriptionKey() {
    var key = retrieveValue(API_KEY_COOKIE);
    while (key.length !== 32) {
        key = prompt("Enter Bing Search API subscription key:", "").trim();
    }
    // always set the cookie in order to update the expiration date
    storeValue(API_KEY_COOKIE, key);
    return key;
}
```

Kod HTML `form` tag `onsubmit` wywołania `bingWebSearch` funkcja zwraca wyniki wyszukiwania. `bingWebSearch` używa `getSubscriptionKey` do uwierzytelniania każdego zapytania. Jak pokazano w poprzednim definicji `getSubscriptionKey` monituje użytkownika o klucz, jeśli klucz nie został wprowadzony. Klucz jest następnie przechowywany dla dalszego użytku przez aplikację.

```html
<form name="bing" onsubmit="this.offset.value = 0; return bingWebSearch(this.query.value, 
    bingSearchOptions(this), getSubscriptionKey())">
```

## <a name="selecting-search-options"></a>Wybranie opcji wyszukiwania

![[Formularza wyszukiwania usługi Bing w sieci Web]](media/cognitive-services-bing-web-api/web-search-spa-form.png)

Formularz HTML zawiera elementy z następujących nazw:

| | |
|-|-|
| `where` | Menu rozwijane wyboru rynku (lokalizacji i language) używane do wyszukiwania. |
| `query` | Pole tekstowe, w którym ma zostać wprowadź terminy wyszukiwania. |
| `what` | Pola wyboru promowania określonej rodzaje wyników. Na przykład podwyższania poziomu obrazów, zwiększa klasyfikacji obrazów. |
| `when` | Menu rozwijane opcjonalnie ograniczania wyszukiwanie do ostatniego dnia, tygodnia lub miesiąca. |
| `safe` | Pole wyboru, oznaczająca, czy funkcja bezpieczne wyszukiwanie w usłudze Bing filtrowanie wyników "dla dorosłych". |
| `count` | Ukryte pole. Liczba wyników wyszukiwania do zwrócenia na każdym żądaniu. Zmień, aby wyświetlić mniej lub więcej wyników na stronie. |
| `offset` | Ukryte pole. Przesunięcie pierwszego wyniku wyszukiwania w żądaniu; służący do stronicowania. Jest resetowany do `0` na nowe żądanie. |

> [!NOTE]
> Wyszukiwania usługi Bing w sieci Web oferuje wiele więcej parametrów zapytania. Firma Microsoft korzysta z tylko niektóre z nich w tym miejscu.

Funkcja JavaScript `bingSearchOptions()` konwertuje tych pól do formatu wymaganego przez interfejs API wyszukiwania usługi Bing.

```javascript
// build query options from the HTML form
function bingSearchOptions(form) {

    var options = [];
    options.push("mkt=" + form.where.value);
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "off"));
    if (form.when.value.length) options.push("freshness=" + form.when.value);
    var what = [];
    for (var i = 0; i < form.what.length; i++) 
        if (form.what[i].checked) what.push(form.what[i].value);
    if (what.length) {
        options.push("promote=" + what.join(","));
        options.push("answerCount=9");
    }
    options.push("count=" + form.count.value);
    options.push("offset=" + form.offset.value);
    options.push("textDecorations=true");
    options.push("textFormat=HTML");
    return options.join("&");
}
```

Na przykład `SafeSearch` parametr w wywołaniu interfejsu API rzeczywiste może być `strict`, `moderate`, lub `off`, z `moderate` jest wartość domyślna. Naszego formularza nie korzysta jednak pole wyboru, która ma tylko dwa stany. Konwertuje kod JavaScript, to ustawienie jako `strict` lub `off` (`moderate` nie jest używany).

Jeśli dowolny z **Podnieś poziom** zaznaczono pola wyboru, będziemy również dodać `answerCount` parametr do zapytania. `answerCount` jest wymagany w przypadku używania `promote` parametru. Firma Microsoft wystarczy ustawić `9` (liczba typów wyników obsługiwana przez interfejs API wyszukiwania usługi Bing sieci Web) aby upewnić się, uzyskujemy maksymalna dopuszczalna liczba typów wyników.

> [!NOTE]
> Wspieranie typ wyniku nie *zagwarantować* że wyniki wyszukiwania zawierają takie wynik. Zamiast podwyższania poziomu zwiększa klasyfikację te rodzaje wyników względem ich zwykle klasyfikacji. Aby ograniczyć wyszukiwanie do określonego rodzaju wyników, należy użyć `responseFilter` parametr zapytania, lub zadzwoń dokładniej punktu końcowego, takie jak Bing obraz wyszukiwania lub wiadomości Bing.

Możemy również wysyłać `textDecoration` i `textFormat` zapytania parametry, aby spowodować, że wyrażenie wyszukiwania, aby być wklejenie w wynikach wyszukiwania. Te wartości są zapisane na stałe w skrypcie.

## <a name="performing-the-request"></a>Wykonywanie żądania

Zapytanie, ciąg opcje i klucz interfejsu API `BingWebSearch` używa `XMLHttpRequest` obiektu do wysłania żądania do punktu końcowego wyszukiwania usługi Bing w sieci Web.

```javascript
// perform a search given query, options string, and API key
function bingWebSearch(query, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("pole", "mainline", "sidebar", "_json", "_http", "paging1", "paging2", "error");

    var request = new XMLHttpRequest();
    var queryurl = BING_ENDPOINT + "?q=" + encodeURIComponent(query) + "&" + options;

    // open the request
    try {
        request.open("GET", queryurl);
    } 
    catch (e) {
        renderErrorMessage("Bad request (invalid URL)\n" + queryurl);
        return false;
    }

    // add request headers
    request.setRequestHeader("Ocp-Apim-Subscription-Key", key);
    request.setRequestHeader("Accept", "application/json");
    var clientid = retrieveValue(CLIENT_ID_COOKIE);
    if (clientid) request.setRequestHeader("X-MSEdge-ClientID", clientid);
    
    // event handler for successful response
    request.addEventListener("load", handleBingResponse);
    
    // event handler for erorrs
    request.addEventListener("error", function() {
        renderErrorMessage("Error completing request");
    });

    // event handler for aborted request
    request.addEventListener("abort", function() {
        renderErrorMessage("Request aborted");
    });

    // send the request
    request.send();
    return false;
}
```

Po pomyślnym ukończeniu żądania HTTP, wywołania języka JavaScript naszych `load` program obsługi zdarzeń, `handleBingResponse()` funkcja obsłużyć pomyślnego żądania HTTP GET do interfejsu API. 

```javascript
// handle Bing search request results
function handleBingResponse() {
    hideDivs("noresults");

    var json = this.responseText.trim();
    var jsobj = {};

    // try to parse JSON results
    try {
        if (json.length) jsobj = JSON.parse(json);
    } catch(e) {
        renderErrorMessage("Invalid JSON response");
        return;
    }

    // show raw JSON and HTTP request
    showDiv("json", preFormat(JSON.stringify(jsobj, null, 2)));
    showDiv("http", preFormat("GET " + this.responseURL + "\n\nStatus: " + this.status + " " + 
        this.statusText + "\n" + this.getAllResponseHeaders()));

    // if HTTP response is 200 OK, try to render search results
    if (this.status === 200) {
        var clientid = this.getResponseHeader("X-MSEdge-ClientID");
        if (clientid) retrieveValue(CLIENT_ID_COOKIE, clientid);
        if (json.length) {
            if (jsobj._type === "SearchResponse" && "rankingResponse" in jsobj) {
                renderSearchResults(jsobj);
            } else {
                renderErrorMessage("No search results in JSON response");
            }
        } else {
            renderErrorMessage("Empty response (are you sending too many requests too quickly?)");
        }
    }

    // Any other HTTP response is an error
    else {
        // 401 is unauthorized; force re-prompt for API key for next request
        if (this.status === 401) invalidateSubscriptionKey();

        // some error responses don't have a top-level errors object, so gin one up
        var errors = jsobj.errors || [jsobj];
        var errmsg = [];

        // display HTTP status code
        errmsg.push("HTTP Status " + this.status + " " + this.statusText + "\n");

        // add all fields from all error responses
        for (var i = 0; i < errors.length; i++) {
            if (i) errmsg.push("\n");
            for (var k in errors[i]) errmsg.push(k + ": " + errors[i][k]);
        }

        // also display Bing Trace ID if it isn't blocked by CORS
        var traceid = this.getResponseHeader("BingAPIs-TraceId");
        if (traceid) errmsg.push("\nTrace ID " + traceid);

        // and display the error message
        renderErrorMessage(errmsg.join("\n"));
    }
}
```

> [!IMPORTANT]
> Powiodło się żądanie HTTP jest *nie* musi to oznaczać, że całego wyszukiwania zakończyło się pomyślnie. Jeśli wystąpi błąd podczas operacji wyszukiwania, interfejsu API wyszukiwania usługi Bing sieci Web zwraca kod stanu 200 HTTP i zawiera informacje o błędzie w odpowiedzi JSON. Ponadto jeśli żądanie zostało ograniczone szybkości, interfejsu API zwraca pustą odpowiedź.

Większość kodu w obu tych funkcji jest przeznaczona do obsługi błędów. Błędy mogą wystąpić w następujących etapów:

|Etap|Potencjalne błędy|Obsługiwane przez|
|-|-|-|
|Obiekt żądania tworzenia JavaScript|Nieprawidłowy adres URL|`try`/`catch` Blok|
|W żądaniu skierowanym|Błędy sieciowe, połączenia zostało przerwane|`error` i `abort` procedury obsługi zdarzeń|
|W wyszukiwaniu|Nieprawidłowe żądania, nieprawidłowy JSON, limity szybkości|testów w `load` obsługi zdarzeń|

Błędy są obsługiwane przez wywołanie metody `renderErrorMessage()` z żadnych szczegółów znane o tym błędzie. Odpowiedź przeszedł pełny gauntlet błąd testów, nazywamy `renderSearchResults()` Aby wyświetlić wyniki wyszukiwania na stronie.

## <a name="displaying-search-results"></a>Wyświetlanie wyników wyszukiwania

Interfejs API wyszukiwania usługi Bing Web [wymaga wyświetlić wyniki w kolejności określonej](useanddisplayrequirements.md). Ponieważ interfejsu API może zwracać różne rodzaje odpowiedzi, nie wystarcza do iteracji najwyższego poziomu `WebPages` kolekcji w odpowiedzi JSON i wyświetlić wyniki. (Tylko jeden rodzaj wyników, należy użyć `responseFilter` parametru zapytania lub innym punktem końcowym wyszukiwania usługi Bing.)

Zamiast tego używamy `rankingResponse` w wynikach wyszukiwania w celu uporządkowania wyników do wyświetlenia. Ten obiekt odwołuje się do elementów w `WebPages` `News`, `Images`, i/lub `Videos` kolekcje, lub w innych kolekcji najwyższego poziomu odpowiedzi w formacie JSON odpowiedzi.

`rankingResponse` może zawierać maksymalnie trzech zbiorów wyników wyszukiwania, wyznaczony `pole`, `mainline`, i `sidebar`. 

`pole`, jeśli istnieje, jest najbardziej odpowiednie wyniki wyszukiwania i powinna być wyróżniane. `mainline` odnosi się do zbiorczego wyników wyszukiwania. Wyniki połączeniach powinien być wyświetlany natychmiast po `pole` (lub, jeśli najpierw `pole` nie jest obecny). 

Na koniec. `sidebar` odnosi się do wyników wyszukiwania pomocniczych. Często te wyniki są powiązane wyszukiwania lub obrazów. Jeśli to możliwe te wyniki powinien być wyświetlany w rzeczywistych paska bocznego. Jeśli ekran limity pasek boczny niepraktyczne (na przykład na urządzeniu przenośnym), powinno występować po `mainline` wyników.

Każdy element `rankingResponse` kolekcji odnosi się do elementów wyników wyszukiwania rzeczywiste na dwa sposoby różne, ale równoważnej.

| | |
|-|-|
|`id`|`id` Wygląda jak adres URL, ale nie powinna być używana dla łącza. `id` Zgodny typ wyniku klasyfikacji `id` albo wyszukiwania wynik elementu w kolekcji odpowiedzi *lub* całej odpowiedzi kolekcji (takie jak `Images`).
|`answerType`, `resultIndex`|`answerType` Odwołuje się do kolekcji odpowiedzi najwyższego poziomu zawierający wynik (na przykład `WebPages`). `resultIndex` Odwołuje się do wyniku indeks w tej kolekcji. Jeśli `resultIndex` jest pominięty, wynik Klasyfikacja odnosi się do całą kolekcję.

> [!NOTE]
> Aby uzyskać więcej informacji w tej części odpowiedzi wyszukiwania, zobacz [wyniki rangę](rank-results.md).

Możesz użyć dowolną wskazaną metodą lokalizowania elementu wynik do którego istnieje odwołanie wyszukiwania jest najbardziej odpowiednim dla aplikacji. W naszym samouczku kodu używamy `answerType` i `resultIndex` zlokalizować każdego wyniku wyszukiwania.

Na koniec nadszedł czas, aby przyjrzeć się naszego funkcja `renderSearchResults()`. Ta funkcja wykonuje iterację na trzech `rankingResponse` kolekcje, które reprezentują sekcjach wyników wyszukiwania. Dla każdej sekcji nazywamy `renderResultsItems()` do renderowania wyniki w tej sekcji.

```javascript
// render the search results given the parsed JSON response
function renderSearchResults(results) {

    // if spelling was corrected, update search field
    if (results.queryContext.alteredQuery) 
        document.forms.bing.query.value = results.queryContext.alteredQuery;

    // add Prev / Next links with result count
    var pagingLinks = renderPagingLinks(results);
    showDiv("paging1", pagingLinks);
    showDiv("paging2", pagingLinks);
    
    // for each possible section, render the resuts from that section
    for (section in {pole: 0, mainline: 0, sidebar: 0}) {
        if (results.rankingResponse[section])
            showDiv(section, renderResultsItems(section, results));
    }
}
```

`renderResultsItems()` z kolei wykonuje iterację na elementy w każdym `rankingResponse` sekcji, mapuje każdy wynik klasyfikacji do wyników wyszukiwania za pomocą funkcji `answerType` i `resultIndex` pól i wywołuje funkcję renderowania odpowiednie do generowania wyników HTML. 

Jeśli `resultIndex` nie został określony dla elementu danej klasyfikacji `renderResultsItems()` przechodzi przez wszystkie wyniki tego typu i wywołuje funkcję renderowania dla każdego elementu. 

W obu przypadkach HTML wynikowe są wstawiane do odpowiedniej `<div>` elementu na stronie.

```javascript
// render search results from rankingResponse object in specified order
function renderResultsItems(section, results) {

    var items = results.rankingResponse[section].items;
    var html = [];
    for (var i = 0; i < items.length; i++) {
        var item = items[i];
        // collection name has lowercase first letter while answerType has uppercase
        // e.g. `WebPages` rankingResult type is in the `webPages` top-level collection
        var type = item.answerType[0].toLowerCase() + item.answerType.slice(1);
        // must have results of the given type AND a renderer for it
        if (type in results && type in searchItemRenderers) {
            var render = searchItemRenderers[type];
            // this ranking item refers to ONE result of the specified type
            if ("resultIndex" in item) {
                html.push(render(results[type].value[item.resultIndex], section));
            // this ranking item refers to ALL results of the specified type
            } else {
                var len = results[type].value.length;
                for (var j = 0; j < len; j++) {
                    html.push(render(results[type].value[j], section, j, len));
                }
            }
        }
    }
    return html.join("\n\n");
}
```

## <a name="rendering-result-items"></a>Renderowanie elementów wyników

W naszym JavaScript kodu jest obiektem `searchItemRenderers`, zawierający *renderowania:* funkcje, które generują kod HTML dla każdego rodzaju wynik wyszukiwania.

```javascript
// render functions for various types of search results
searchItemRenderers = { 
    webPages: function(item) { ... },
    news: function(item) { ... },
    images: function(item, section, index, count) { ... },
    videos: function(item, section, index, count) { ... },
    relatedSearches: function(item, section, index, count) { ... }
}
```

> [!NOTE]
> Samouczek aplikacji ma renderowania dla stron sieci Web, wiadomości, obrazów, klipów wideo i wyszukiwań pokrewnych. Własnej aplikacji musi renderowania dla dowolnego rodzaju wyników, który może zostać wyświetlony, które mogą obejmować obliczenia, sugestie dotyczące pisowni, jednostek, stref czasowych i definicje.

Niektóre funkcje naszych renderowania akceptować tylko `item` parametrów: obiekt JavaScript, który reprezentuje wynik wyszukiwania pojedynczego. Zaakceptuj inne dodatkowe parametry, które mogą być używane do renderowania elementów inaczej w różnych kontekstach. (Moduł renderowania, który nie używa tych informacji nie musisz zaakceptować te parametry.)

W kontekście argumentach są:

| | |
|-|-|
|`section`|W sekcji wyników (`pole`, `mainline`, lub `sidebar`), w której element jest wyświetlany.
|`index`<br>`count`|Dostępna, gdy `rankingResponse` element określa, że wszystkie wyniki w danej kolekcji mają być wyświetlane; `undefined` inaczej. Te parametry odbierać indeks elementu w obrębie swojej kolekcji i całkowitą liczbę elementów w tej kolekcji. Możesz tych informacji, aby liczba wyników, aby wygenerować inny kod HTML dla pierwszego lub ostatniego wyniku i tak dalej.|

W naszym samouczku aplikacji zarówno `images` i `relatedSearches` renderowania umożliwia dostosowywanie generują kod HTML argumentów kontekstu. Spójrzmy bliższe spojrzenie na `images` renderowania:

```javascript
searchItemRenderers = { 
    // render image result using thumbnail
    images: function(item, section, index, count) {
        var height = 60;
        var width = Math.round(height * item.thumbnail.width / item.thumbnail.height);
        var html = [];
        if (section === "sidebar") {
            if (index) html.push("<br>");
        } else {
            if (!index) html.push("<p class='images'>");
        }
        html.push("<a href='" + item.hostPageUrl + "'>");
        var title = escape(item.name) + "\n" + getHost(item.hostPageDisplayUrl);
        html.push("<img src='"+ item.thumbnailUrl + "&h=" + height + "&w=" + width + 
            "' height=" + height + " width=" + width + " title='" + title + "' alt='" + title + "'>");
        html.push("</a>");
        return html.join("");
    }, // other renderers omitted
}
```

Nasze funkcja renderowania obrazu:

> [!div class="checklist"]
> * Oblicza rozmiar miniatur obrazu (szerokość zmienia się, gdy wysokość jest ustalone w pikselach 60).
> * Wstawia kod HTML, który poprzedza wynik obrazu, w zależności od kontekstu.
> * Tworzy HTML `<a>` tagu prowadzący do strony zawierającej obraz.
> * Tworzy HTML `<img>` tag, aby wyświetlić obraz miniatury. 

Renderowanie obrazu używa `section` i `index` zmienne, aby wyświetlić wyniki inaczej w zależności od miejsca występowania. Podział wiersza (`<br>` tagu) między dodaje wyniki obraz na pasku bocznym tak, aby kolumna obrazów wyświetlany pasek boczny. W innych częściach powoduje pierwszy obraz `(index === 0)` jest poprzedzony `<p>` tagu. W przeciwnym razie miniatur spoiny sprawdzania i każdej zawijania zgodnie z potrzebami w oknie przeglądarki.

Rozmiaru miniatur jest używany zarówno w `<img>` tagu i `h` i `w` pól w adresie URL miniatury. [Miniatur usługi Bing](resize-and-crop-thumbnails.md) następnie dostarcza Miniatura dokładnie tego rozmiaru. `title` i `alt` atrybutów (tekstowy opis obrazu) są tworzone na podstawie obrazu nazwy i nazwy hosta w adresie URL.

Obrazy są wyświetlane, jak pokazano w wynikach wyszukiwania połączeniach.

![[Bing obrazu wyniki]](media/cognitive-services-bing-web-api/web-search-spa-images.png)

## <a name="persisting-client-id"></a>Utrwalanie identyfikator klienta

Odpowiedzi z interfejsy API wyszukiwania usługi Bing mogą obejmować `X-MSEdge-ClientID` nagłówek, który powinien zostać odesłany do interfejsu API z kolejnych żądań. Jeśli wiele interfejsy API wyszukiwania usługi Bing są używane, ten sam identyfikator klienta należy używać z wszystkich z nich, jeśli to możliwe.

Zapewnianie `X-MSEdge-ClientID` nagłówka umożliwia interfejsy API usługi Bing w celu skojarzenia wszystkich wyszukiwania użytkownika, która ma dwa istotne zalety.

Po pierwsze umożliwia Bing aparatu wyszukiwania w celu dotyczą wyszukiwania, aby znaleźć wyników spełniających kryteria lepiej użytkownika wcześniejszą kontekstu. Jeśli użytkownik został wcześniej wyszukiwane terminy związane z prowadzenia, na przykład nowsze Wyszukaj "węzłów" może preferencyjnie zwracają informacje o używane w prowadzenia węzłów.

Po drugie Bing mogą losowo wybierać użytkownikom wystąpić nowe funkcje, zanim staną się ogólnie dostępne. Podanie Identyfikatora klienta z każdym żądaniem zapewnia użytkowników, którzy wybrano, aby zobaczyć funkcji zawsze Zobacz go. Bez Identyfikatora klienta użytkownik może zobaczyć funkcji pojawiają się i znikają, pozornie losowo, w wynikach wyszukiwania.

Zasady zabezpieczeń przeglądarki (CORS) może uniemożliwiać `X-MSEdge-ClientID` nagłówka jest dostępne dla języka JavaScript. To ograniczenie występuje, gdy różne źródła ze strony, którego żąda on ma odpowiedzi wyszukiwania. W środowisku produkcyjnym należy spełnić te zasady, obsługując skryptu po stronie serwera, który wykonuje wywołanie interfejsu API w tej samej domenie co stronę sieci Web. Ponieważ skrypt ma to samo źródło jako strona sieci Web `X-MSEdge-ClientID` nagłówka jest następnie udostępniana JavaScript.

> [!NOTE]
> W środowisku produkcyjnym aplikacji sieci Web należy wykonać po stronie serwera żądania mimo to. W przeciwnym razie wartość klucza interfejsu API wyszukiwania usługi Bing musi być uwzględniona na stronie sieci Web, gdy są one dostępne dla każdego, kto widoków źródła. Dla wszystkich użycia są rozliczane interfejsu API klucza subskrypcji nawet żądań przez osoby nieupoważnione, dlatego ważne jest, aby nie uwidacznia klucz.

Do celów programistycznych możesz wprowadzić żądania interfejsu API Bing sieci Web wyszukiwania przez serwer proxy CORS. Odpowiedź z serwera proxy ma `Access-Control-Expose-Headers` Nagłówek tego whitelists nagłówki odpowiedzi i udostępnia je JavaScript.

To proste zainstalować serwer proxy CORS, aby umożliwić samouczek aplikacji dostępu klienta do nagłówka Identyfikatora. Pierwsza strona, jeśli nie masz jeszcze, [instalowania programu Node.js](https://nodejs.org/en/download/). Następnie należy wydać następujące polecenie w oknie poleceń:

    npm install -g cors-proxy-server

Następnie można zmienić punktu końcowego wyszukiwania usługi Bing w sieci Web w pliku w formacie HTML do:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

Na koniec uruchom CORS proxy za pomocą następującego polecenia:

    cors-proxy-server

Pozostaw otwarte okno wiersza poleceń podczas korzystania z samouczka aplikacji; Zamyka okno zatrzymuje serwer proxy. W sekcji nagłówków HTTP rozwijanych poniżej wyniki wyszukiwania, możesz teraz przeglądać `X-MSEdge-ClientID` nagłówka (między innymi) i sprawdź, czy jest taka sama dla każdego żądania.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Visual samouczek aplikacji mobilnej wyszukiwania](computer-vision-web-search-tutorial.md)

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API wyszukiwania sieci Web usługi Bing](//docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference)
