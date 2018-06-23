---
title: Bing jednostronicowej wiadomości wyszukiwania aplikacji | Dokumentacja firmy Microsoft
description: Wyjaśniono, jak użyć interfejsu API wyszukiwania usługi Bing wiadomości w aplikacji jednej strony sieci Web.
services: cognitive-services
author: mikedodaro
manager: ronakshah
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 10/30/2017
ms.author: v-gedod
ms.openlocfilehash: fb8cd24dfdfb03500cc86ee1b1f0126ec044a873
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349221"
---
# <a name="tutorial-single-page-news-search-app"></a>Samouczek: Wyszukiwania wiadomości jednostronicowej aplikacji
Interfejs API wyszukiwania usługi Bing wiadomości umożliwia wyszukiwanie w sieci Web i uzyskiwać wyniki, które dotyczą zapytania wyszukiwania typu wiadomości. W tym samouczku budujemy jednostronicowej aplikacji sieci Web, która używa interfejsu API wyszukiwania usługi Bing wiadomości, aby wyświetlić wyniki wyszukiwania na stronie. Aplikacja zawiera składniki HTML, CSS i JavaScript.

<!-- Remove until we can replace it with sanitized copy
![Single-page Bing News Search app](media/news-search-singlepage.png)
-->

> [!NOTE]
> Nagłówki JSON i HTTP w dolnej części strony, gdy kliknięto Pokaż odpowiedź w formacie JSON i informacje o żądaniu HTTP. Informacje te mogą być przydatne podczas szczegółowego eksplorowania usługi.

Samouczek aplikacji ilustruje sposób:
> [!div class="checklist"]
> * Wykonywanie wywołań interfejsu API wyszukiwania wiadomości Bing w JavaScript
> * Przeniesienie opcje wyszukiwania do interfejsu API wyszukiwania usługi Bing wiadomości
> * Wyświetl wyniki wyszukiwania wiadomości z czterech kategorii: dowolnego typu, business, kondycji lub polityka z przedziały czasu 24 godzin, w zeszłym tygodniu miesiąca lub wszystkie dostępne godziny
> * Przejrzyj wyniki wyszukiwania
> * Dojście Bing interfejsów API i identyfikator subskrypcji klucz klienta
> * Obsługa błędów, które mogą wystąpić

Samouczek strony są całkowicie niezależne; nie używa żadnych platform zewnętrznych, arkuszy stylów lub pliki obrazów. Umożliwia ona używa tylko powszechnie obsługiwane funkcje języka JavaScript i działa z bieżącymi wersjami wszystkie główne przeglądarki sieci Web.

W tym samouczku omówiono zaznaczonych części kodu źródłowego. Pełną [kod źródłowy](tutorial-bing-news-search-single-page-app-source.md) jest dostępna. Aby uruchomić przykład, skopiuj i Wklej kod źródłowy w edytorze tekstu i zapisz go jako `bing.html`.

## <a name="app-components"></a>Składniki aplikacji
Podobnie jak wszelkie jednostronicowej aplikacji sieci Web ten samouczek aplikacji obejmuje trzy części:

> [!div class="checklist"]
> * HTML — definiuje struktury i zawartości strony
> * CSS — definiuje wygląd strony
> * JavaScript — określa zachowanie strony

Większość kodu HTML i CSS jest konwencjonalnej, więc nie omawia samouczka. HTML zawierający formularz wyszukiwania, w którym użytkownik wprowadza zapytania i wybiera opcje wyszukiwania. Formularz jest połączony JavaScript, które faktycznie wykonuje wyszukiwanie przy użyciu `onsubmit` atrybutu `<form>` tagu:

```html
<form name="bing" onsubmit="return newBingNewsSearch(this)">
```
`onsubmit` Obsługi zwraca `false`, co pozwala formularza są przesyłane do serwera. Kod JavaScript działa zbierania niezbędne informacje z formularza i wyszukiwania.

Kod HTML zawiera także działów (HTML `<div>` tagów) gdzie zostaną wyświetlone wyniki wyszukiwania.

## <a name="managing-subscription-key"></a>Zarządzanie subskrypcją klucza

Aby uniknąć konieczności dołączania klucz interfejsu API wyszukiwania usługi Bing subskrypcji w kodzie, używamy przeglądarki trwałego magazynu do przechowywania klucza. Przed klucz jest przechowywany, możemy monit o podanie klucza użytkownika. Jeśli klucz później został odrzucony przez interfejs API, możemy unieważnienie przechowywanych klucza, użytkownik będzie monitowany ponownie.

Definiujemy `storeValue` i `retrieveValue` funkcje programu wykorzystujące albo `localStorage` obiektu (nie wszystkie przeglądarki obsługują) lub plik cookie. `getSubscriptionKey()` Funkcja używa tych funkcji do przechowywania i pobierania klucza użytkownika.

``` javascript
// Cookie names for data we store
API_KEY_COOKIE   = "bing-search-api-key";
CLIENT_ID_COOKIE = "bing-search-client-id";

// Bing Search API endpoint
BING_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/news";

// ... omitted definitions of storeValue() and retrieveValue()
// Browsers differ in their support for persistent storage by 
// local HTML files. See the source code for browser-specific
// options.

// Get stored API subscription key, or 
// prompt if it's not found.
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
Kod HTML `<form>` tag `onsubmit` wywołania `bingWebSearch` funkcja zwraca wyniki wyszukiwania. `bingWebSearch` używa `getSubscriptionKey()` do uwierzytelniania każdego zapytania. Jak pokazano w poprzednim definicji `getSubscriptionKey` monituje użytkownika o klucz, jeśli klucz nie został wprowadzony. Klucz jest następnie przechowywany dla dalszego użytku przez aplikację.

```html
<form name="bing" onsubmit="this.offset.value = 0; return bingWebSearch(this.query.value, 
    bingSearchOptions(this), getSubscriptionKey())">
```
## <a name="selecting-search-options"></a>Wybranie opcji wyszukiwania
Na poniższej ilustracji przedstawiono pole tekstowe zapytania i opcje definiujące wyszukiwania wiadomości o fundusze służbowe.

![Opcje wyszukiwania wiadomości Bing](media/news-search-categories.png)

Formularz HTML zawiera elementy z następujących nazw:

|Element|Opis|
|-|-|
| `where` | Menu rozwijane wyboru rynku (lokalizacji i language) używane do wyszukiwania. |
| `query` | Pole tekstowe do wprowadzania z warunkami wyszukiwania. |
| `category` | Pola wyboru promowania określonej rodzaje wyników. Na przykład podwyższania poziomu kondycji, zwiększa oceniania kondycji wiadomości. |
| `when` | Menu rozwijane opcjonalnie ograniczania wyszukiwanie do ostatniego dnia, tygodnia lub miesiąca. |
| `safe` | Pole wyboru, oznaczająca, czy funkcja bezpieczne wyszukiwanie Bing filtrowanie wyników "dla dorosłych". |
| `count` | Ukryte pole. Liczba wyników wyszukiwania do zwrócenia na każdym żądaniu. Zmień, aby wyświetlić mniej lub więcej wyników na stronie. |
| `offset`|  Ukryte pole. Przesunięcie pierwszego wyniku wyszukiwania w żądaniu; służący do stronicowania. Jest resetowany do `0` na nowe żądanie. |

> [!NOTE]
> Wyszukiwania usługi Bing w sieci Web oferuje inne parametry zapytania. Firma Microsoft korzysta z tylko niektóre z nich.

``` javascript
// build query options from the HTML form
function bingSearchOptions(form) {

    var options = [];
    options.push("mkt=" + form.where.value);
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "off"));
    if (form.when.value.length) options.push("freshness=" + form.when.value);

    for (var i = 0; i < form.category.length; i++) {
        if (form.category[i].checked) {
            category = form.category[i].value;
            break;
        }
    }
    if (category.valueOf() != "all".valueOf()) { 
        options.push("category=" + category); 
        }
    options.push("count=" + form.count.value);
    options.push("offset=" + form.offset.value);
    return options.join("&");
}
```

Na przykład `SafeSearch` parametr w wywołaniu interfejsu API rzeczywiste może być `strict`, `moderate`, lub `off`, z `moderate` jest wartość domyślna. Naszego formularza nie korzysta jednak pole wyboru, która ma tylko dwa stany. Konwertuje kod JavaScript, to ustawienie jako `strict` lub `off` (`moderate` nie jest używany).

## <a name="performing-the-request"></a>Wykonywanie żądania
Zapytanie, ciąg opcje i klucz interfejsu API `BingNewsSearch` używa `XMLHttpRequest` obiektu do wysłania żądania do punktu końcowego wyszukiwania usługi Bing wiadomości.

```javascript
// perform a search given query, options string, and API key
function bingNewsSearch(query, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("results", "related", "_json", "_http", "paging1", "paging2", "error");

    var request = new XMLHttpRequest();
     if (category.valueOf() != "all".valueOf()) {
        var queryurl = BING_ENDPOINT + "/search?" + "?q=" + encodeURIComponent(query) + "&" + options;
    }
    else
    {
        if (query){
        var queryurl = BING_ENDPOINT + "?q=" + encodeURIComponent(query) + "&" + options;
        }
        else {
            var queryurl = BING_ENDPOINT + "?" + options;
        }
    }

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
Po pomyślnym ukończeniu żądania HTTP, wywołania języka JavaScript `load` program obsługi zdarzeń, `handleBingResponse()` funkcja obsłużyć pomyślnego żądania HTTP GET do interfejsu API. 

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
            if (jsobj._type === "News") {
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
> Powiodło się żądanie HTTP jest *nie* musi to oznaczać, że całego wyszukiwania zakończyło się pomyślnie. Jeśli wystąpi błąd podczas operacji wyszukiwania, interfejsu API wyszukiwania usługi Bing wiadomości zwraca kod stanu 200 HTTP i zawiera informacje o błędzie w odpowiedzi JSON. Ponadto jeśli żądanie zostało ograniczone szybkości, interfejsu API zwraca pustą odpowiedź.

Większość kodu w obu tych funkcji jest przeznaczona do obsługi błędów. Błędy mogą wystąpić w następujących etapów:

|Etap|Potencjalne błędy|Obsługiwane przez|
|-|-|-|
|Tworzenie obiektu żądania JavaScript|Nieprawidłowy adres URL|`try`/`catch` Blok|
|W żądaniu skierowanym|Błędy sieciowe, połączenia zostało przerwane|`error` i `abort` procedury obsługi zdarzeń|
|W wyszukiwaniu|Nieprawidłowe żądania, nieprawidłowy JSON, limity szybkości|testów w `load` obsługi zdarzeń|

Błędy są obsługiwane przez wywołanie metody `renderErrorMessage()` z żadnych szczegółów znane o tym błędzie. Odpowiedź przeszedł pełny gauntlet błąd testów, nazywamy `renderSearchResults()` Aby wyświetlić wyniki wyszukiwania na stronie.

## <a name="displaying-search-results"></a>Wyświetlanie wyników wyszukiwania
Funkcja main do wyświetlania wyników wyszukiwania jest `renderSearchResults()`. Ta funkcja przyjmuje JSON zwrócony przez usługę wyszukiwania wiadomości Bing i renderuje wyniki wiadomości i powiązane wyszukiwania, jeśli istnieje.

```javascript
// render the search results given the parsed JSON response
    function renderSearchResults(results) {

    // add Prev / Next links with result count
    var pagingLinks = renderPagingLinks(results);
    showDiv("paging1", pagingLinks);
    showDiv("paging2", pagingLinks);

    showDiv("results", renderResults(results.value));
    if (results.relatedSearches)
        showDiv("sidebar", renderRelatedItems(results.relatedSearches));
}
```
Wyniki wyszukiwania głównego są zwracane jako najwyższego poziomu `value` obiektu w odpowiedzi JSON. Możemy przekazać do naszej funkcji `renderResults()`, który iteruje po ich i wywołuje funkcję oddzielne do renderowania każdej pozycji w kodzie HTML. Wynikowa HTML jest zwracana do `renderSearchResults()`, w którym znajduje się `results` dzielenia na stronie.

```javascript
function renderResults(items) {
    var len = items.length;
    var html = [];
    if (!len) {
        showDiv("noresults", "No results.");
        hideDivs("paging1", "paging2");
        return "";
    }
    for (var i = 0; i < len; i++) {
        html.push(searchItemRenderers.news(items[i], i, len));
    }
    return html.join("\n\n");
}
```
Zwraca interfejs API wyszukiwania usługi Bing wiadomości maksymalnie cztery różne rodzaje wyniki, każdej z nich w jego własnej obiektu najwyższego poziomu. Oto one:

|Relacji|Opis|
|-|-|
|`pivotSuggestions`|Kwerendy, które Zamień słowo pivot pierwotnego wyszukiwania innego. Na przykład wyszukiwania "red kwiaty" pivot word może być "red", a sugestię pivot może "żółty kwiaty".|
|`queryExpansions`|Kwerendy, które zawęzić pierwotnego wyszukiwania przez dodanie więcej warunków. Na przykład, jeśli wyszukiwanie "Microsoft Surface" rozszerzenia zapytania może być "Microsoft Surface Pro."|
|`relatedSearches`|Kwerendy, które również zostały wprowadzone przez innych użytkowników, którzy wprowadzona pierwotnego wyszukiwania. Na przykład jeśli wyszukiwanie "Roman instalacji" powiązane wyszukiwania może być "patrz hasło Mt. Ds. Saint."|
|`similarTerms`|Kwerendy, które są podobne w rozumieniu do pierwotnego wyszukiwania. Na przykład jeśli wyszukiwanie "szkoły" podobny okres może być "education."|

Jak poprzednio widziano w `renderSearchResults()`, możemy renderowania tylko `relatedItems` sugestie i miejscu powstałe w ten sposób łączy na pasku bocznym strony.

## <a name="rendering-result-items"></a>Renderowanie elementów wyników

W języku JavaScript kod obiektu, `searchItemRenderers`, zawiera *renderowania:* funkcje, które generują kod HTML dla każdego rodzaju wynik wyszukiwania.

```javascript
searchItemRenderers = {
    news: function(item) { ... },
    webPages: function (item) { ... }, 
    images: function(item, index, count) { ... },
    relatedSearches: function(item) { ... }
}
```
Funkcja renderowania może zaakceptować następujące parametry:

|Parametr|Opis|
|-|-|
|`item`| Obiektu JavaScript zawierający właściwości elementu, takie jak adres URL i jego opis.|
|`index`| Indeks elementu wyników w swojej kolekcji.|
|`count`| Liczba elementów w kolekcji elementów wyników wyszukiwania.|

`index` i `count` parametry mogą służyć do liczba wyników do wygenerowania specjalne HTML na początku lub na końcu kolekcji, aby wstawić podziały wiersza po określonej liczby elementów i tak dalej. Jeśli mechanizm renderujący nie muszą tej funkcji, nie musisz zaakceptować te dwa parametry.

`news` Renderowania przedstawiono w poniższym fragmencie kodu javascript:
```javascript
    // render news story
    news: function (item) {
        var html = [];
        html.push("<p class='news'>");
        if (item.image) {
            width = 60;
            height = Math.round(width * item.image.thumbnail.height / item.image.thumbnail.width);
            html.push("<img src='" + item.image.thumbnail.contentUrl +
                "&h=" + height + "&w=" + width + "' width=" + width + " height=" + height + ">");
        }
        html.push("<a href='" + item.url + "'>" + item.name + "</a>");
        if (item.category) html.push(" - " + item.category);
        if (item.contractualRules) {    // MUST display source attributions
            html.push(" (");
            var rules = [];
            for (var i = 0; i < item.contractualRules.length; i++)
                rules.push(item.contractualRules[i].text);
                html.push(rules.join(", "));
                html.push(")");
            }
        html.push(" (" + getHost(item.url) + ")");
        html.push("<br>" + item.description);
        return html.join("");
    },
```
Funkcja renderowania wiadomości:
> [!div class="checklist"]
> * Tworzy tag akapitu i przypisuje go do `news` klasy, a wypchnięcia jej do tablicy html.
> * Oblicza rozmiar miniatur obrazu (szerokość jest ustalony na 60 pikseli, wysokość obliczone proporcjonalnie).
> * Tworzy HTML `<img>` tag, aby wyświetlić obraz miniatury. 
> * Tworzy HTML `<a>` tagi, które łącze do obrazu i strona, która go zawiera.
> * Tworzy opis, który zawiera informacje o obrazie i lokacji, który nie znajduje się na.

Rozmiaru miniatur jest używany zarówno w `<img>` tagu i `h` i `w` pól w adresie URL miniatury. [Miniatur usługi Bing](resize-and-crop-thumbnails.md) następnie dostarcza Miniatura dokładnie tego rozmiaru.

## <a name="persisting-client-id"></a>Utrwalanie identyfikator klienta
Odpowiedzi z interfejsy API wyszukiwania usługi Bing mogą obejmować `X-MSEdge-ClientID` nagłówek, który powinien zostać odesłany do interfejsu API z kolejnych żądań. Jeśli wiele interfejsy API wyszukiwania usługi Bing są używane, ten sam identyfikator klienta należy używać z wszystkich z nich, jeśli to możliwe.

Zapewnianie `X-MSEdge-ClientID` nagłówka umożliwia interfejsy API usługi Bing w celu skojarzenia wszystkich wyszukiwania użytkownika, która ma dwa istotne zalety.

Po pierwsze umożliwia Bing aparatu wyszukiwania w celu dotyczą wyszukiwania, aby znaleźć wyników spełniających kryteria lepiej użytkownika wcześniejszą kontekstu. Jeśli użytkownik został wcześniej wyszukiwane terminy związane z prowadzenia, na przykład nowsze Wyszukaj "węzłów" może preferencyjnie zwracają informacje o używane w prowadzenia węzłów.

Po drugie Bing mogą losowo wybierać użytkownikom wystąpić nowe funkcje, zanim staną się ogólnie dostępne. Podanie Identyfikatora klienta z każdym żądaniem zapewnia użytkowników, którzy zawsze wyświetlać tę funkcję Zobacz go. Bez Identyfikatora klienta użytkownik może zobaczyć funkcji pojawiają się i znikają, pozornie losowo, w wynikach wyszukiwania.

Zasady zabezpieczeń przeglądarki (CORS) może uniemożliwiać `X-MSEdge-ClientID` nagłówka jest dostępne dla języka JavaScript. To ograniczenie występuje, gdy różne źródła ze strony, którego żąda on ma odpowiedzi wyszukiwania. W środowisku produkcyjnym należy spełnić te zasady, obsługując skryptu po stronie serwera, który wykonuje wywołanie interfejsu API w tej samej domenie co stronę sieci Web. Ponieważ skrypt ma to samo źródło jako strona sieci Web `X-MSEdge-ClientID` nagłówka jest następnie udostępniana JavaScript.

> [!NOTE]
> W środowisku produkcyjnym aplikacji sieci Web należy wykonać po stronie serwera żądania. W przeciwnym razie wartość klucza interfejsu API wyszukiwania usługi Bing musi być uwzględniona na stronie sieci Web, gdy są one dostępne dla każdego, kto widoków źródła. Dla wszystkich użycia są rozliczane interfejsu API klucza subskrypcji nawet żądań przez osoby nieupoważnione, dlatego ważne jest, aby nie uwidacznia klucz.

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
> [Dokumentacja interfejsu API wyszukiwania wiadomości Bing](//docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference)