---
title: Aplikacja sieci web jednej strony wyszukiwania usługi Bing jednostki | Dokumentacja firmy Microsoft
description: Przedstawia sposób użycia interfejsu API wyszukiwania usługi Bing jednostki w jednej strony aplikacji sieci Web.
services: cognitive-services
author: v-jerkin
manager: ehansen
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: article
ms.date: 12/08/2017
ms.author: v-jerkin
ms.openlocfilehash: 91c60913cd806baf100e5511cbf59299bf9a84f0
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349232"
---
# <a name="tutorial-single-page-web-app"></a>Samouczek: Jednostronicowej aplikacji sieci web

Interfejs API wyszukiwania usługi Bing jednostki umożliwia wyszukiwanie w sieci Web, aby uzyskać informacje o *jednostek* i *miejsca.* Aby zażądać typu wyników lub zarówno w określonego zapytania. Poniżej znajdują się definicje miejsca i jednostek.

|||
|-|-|
|Jednostki|Dobrze znane osoby, miejsca i rzeczy, które możesz znaleźć według nazwy|
|Miejsca|Restauracji, hotele i innych firm lokalnych, które Znajdź według nazwy *lub* według typu (restauracji włoska)|

W tym samouczku budujemy jednostronicowej aplikacji sieci Web, która używa interfejsu API wyszukiwania usługi Bing jednostki, aby wyświetlić wyniki wyszukiwania po prawej stronie. Aplikacja zawiera składniki HTML, CSS i JavaScript.

Interfejs API umożliwia priorytety wyniki według lokalizacji. W aplikacji mobilnej możesz poprosić urządzenie pod kątem własną lokalizację. W aplikacji sieci Web, można użyć `getPosition()` funkcji. Jednak to wywołanie działa tylko w kontekstach bezpiecznego i może nie zapewniać dokładnej lokalizacji. Ponadto użytkownik może chcieć wyszukiwania dla jednostek w pobliżu lokalizacji innych niż własne.

Aplikacji w związku z tym wywołuje na usługi mapy Bing, aby uzyskać współrzędne geograficzne z lokalizacji wprowadzonych przez użytkownika. Użytkownik może następnie wprowadzić nazwę dzielnicę ("kręci miejsca") lub adresem pełnej lub częściowej ("nowego Jorku") i interfejsu API map Bing zawiera współrzędne.

<!-- Remove until we can replace with a sanitized version.
![[Single-page Bing Entity Search app]](media/entity-search-spa-demo.png)
-->

> [!NOTE]
> Nagłówki JSON i HTTP w dolnej części strony ujawnić odpowiedź w formacie JSON i informacje o żądaniu HTTP po kliknięciu. Informacje te są przydatne podczas szczegółowego eksplorowania usługi.

Samouczek aplikacji ilustruje sposób:

> [!div class="checklist"]
> * Wykonywać wywołanie interfejsu API Bing encji wyszukiwania w języku JavaScript
> * Wykonaj mapy Bing `locationQuery` wywołanie interfejsu API w języku JavaScript
> * Przeniesienie opcje wyszukiwania na wywołania interfejsu API
> * Wyświetl wyniki wyszukiwania
> * Obsługa usługi Bing interfejsów API i identyfikator subskrypcji klucze klientów
> * Uwzględniać wszelkie błędy, które mogą wystąpić

Samouczek strony są całkowicie niezależne; nie używa żadnych platform zewnętrznych, arkuszy stylów lub nawet pliki obrazów. Umożliwia ona używa tylko powszechnie obsługiwane funkcje języka JavaScript i działa z bieżącymi wersjami wszystkie główne przeglądarki sieci Web.

W tym samouczku omówiono tylko wybrane części kodu źródłowego. Pełny kod źródłowy jest dostępny [na osobnej stronie](tutorial-bing-entities-search-single-page-app-source.md). Skopiuj i wklej ten kod w edytorze tekstu i zapisz go jako `bing.html`.

> [!NOTE]
> W tym samouczku jest podobne do [samouczek aplikacji wyszukiwania usługi Bing w sieci Web jednej strony](../Bing-Web-Search/tutorial-bing-web-search-single-page-app.md), ale dotyczy tylko wyniki wyszukiwania jednostki.

## <a name="app-components"></a>Składniki aplikacji

Tak jak każda aplikacja sieci Web jednej strony samouczek aplikacja obejmuje trzy części:

> [!div class="checklist"]
> * HTML — definiuje struktury i zawartości strony
> * CSS — definiuje wygląd strony
> * JavaScript — określa zachowanie strony

W tym samouczku nie obejmuje większość HTML i CSS szczegółowo, ponieważ są one proste.

HTML zawierający formularz wyszukiwania, w którym użytkownik wprowadza zapytania i wybiera opcje wyszukiwania. Formularz jest połączony JavaScript, które faktycznie wykonuje wyszukiwanie według `<form>` znacznika `onsubmit` atrybutu:

```html
<form name="bing" onsubmit="return newBingEntitySearch(this)">
```

`onsubmit` Obsługi zwraca `false`, co pozwala formularza są przesyłane do serwera. Kod JavaScript jest rzeczywiście pracy zbierania niezbędne informacje z formularza i wyszukiwania.

Wyszukiwanie jest wykonywane w dwóch fazach. Najpierw Jeśli użytkownik wprowadził ograniczenie lokalizacji, zapytania mapy Bing odbywa się przekonwertować go na współrzędne. Następnie wywołania zwrotnego dla tego zapytania dotyczącego wyszukiwania usługi Bing jednostki.

Kod HTML zawiera także działów (HTML `<div>` tagów) gdzie zostaną wyświetlone wyniki wyszukiwania.

## <a name="managing-subscription-keys"></a>Zarządzanie kluczami subskrypcji

> [!NOTE]
> Ta aplikacja wymaga kluczy subskrypcji dla interfejsu API wyszukiwania usługi Bing i interfejsu API map Bing. Można użyć [wersji próbnej klucza wyszukiwania usługi Bing](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) i [podstawowy klucz mapy Bing](https://www.microsoft.com/maps/create-a-bing-maps-key).

Aby uniknąć konieczności dołączania klucze subskrypcji wyszukiwania usługi Bing i interfejsu API map Bing w kodzie, używamy magazynu trwałego przeglądarki je przechowywać. Jeśli nie była przechowywana albo klucza, możemy monit o podanie go i zapisze go na przyszłość. Jeśli klucz później został odrzucony przez interfejs API, możemy unieważnienie przechowywanych klucza, użytkownik jest pytany dla niego po ich dalej wyszukiwania.

Definiujemy `storeValue` i `retrieveValue` funkcje programu wykorzystujące albo `localStorage` obiektu (Jeśli przeglądarka obsługuje) lub plik cookie. Nasze `getSubscriptionKey()` funkcja używa tych funkcji do przechowywania i pobierania klucza użytkownika.

```javascript
// cookie names for data we store
SEARCH_API_KEY_COOKIE = "bing-search-api-key";
MAPS_API_KEY_COOKIE   = "bing-maps-api-key";
CLIENT_ID_COOKIE      = "bing-search-client-id";

// API endpoints
SEARCH_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/entities";
MAPS_ENDPOINT   = "http://dev.virtualearth.net/REST/v1/Locations";

// ... omitted definitions of storeValue() and retrieveValue()

// get stored API subscription key, or prompt if it's not found
function getSubscriptionKey(cookie_name, key_length, api_name) {
    var key = retrieveValue(cookie_name);
    while (key.length !== key_length) {
        key = prompt("Enter " + api_name + " API subscription key:", "").trim();
    }
    // always set the cookie in order to update the expiration date
    storeValue(cookie_name, key);
    return key;
}

function getMapsSubscriptionKey() {
    return getSubscriptionKey(MAPS_API_KEY_COOKIE, 64, "Bing Maps");
}

function getSearchSubscriptionKey() {
    return getSubscriptionKey(SEARCH_API_KEY_COOKIE, 32, "Bing Search");
}
```

Kod HTML `<body>` zawiera tag `onload` atrybut, który wywołuje `getSearchSubscriptionKey()` i `getMapsSubscriptionKey()` po zakończeniu ładowania strony. Te wywołania udostępniają natychmiast Monituj użytkownika o ich kluczy, jeśli ich nie zostały jeszcze wprowadzone.

```html
<body onload="document.forms.bing.query.focus(); getSearchSubscriptionKey(); getMapsSubscriptionKey();">
```

## <a name="selecting-search-options"></a>Wybranie opcji wyszukiwania

![[Formularza wyszukiwania usługi Bing jednostki]](media/entity-search-spa-form.png)

Formularz HTML są dostępne następujące kontrolki:

| | |
|-|-|
|`where`|Menu rozwijane wyboru rynku (lokalizacji i language) używane do wyszukiwania.|
|`query`|Pole tekstowe, w którym ma zostać wprowadź terminy wyszukiwania.|
|`safe`|Pole wyboru wskazującą, czy bezpieczne wyszukiwanie jest włączona (ogranicza wyniki "dla dorosłych")|
|`what`|Menu wyboru do wyszukiwania jednostek i/lub miejsca.|
|`mapquery`|Pole tekstowe, w którym użytkownik może wprowadzić adres pełnej lub częściowej, punkty, itp., ułatwiające zwracany dokładniejsze wyniki wyszukiwania usługi Bing jednostki.|

> [!NOTE]
> Wyniki miejsca są obecnie dostępne tylko w Stanach Zjednoczonych. `where` i `what` menu ma kodu w celu wymuszenia tego ograniczenia. Jeśli wybierzesz rynku-US, a wybrano miejsca w `what` menu `what` zmienia się na inną wartość. Po wybraniu miejsca, a wybrano rynku-US w `where` menu `where` zmiany w Stanach Zjednoczonych.

Nasze funkcji JavaScript `bingSearchOptions()` konwertuje te pola ciągu zapytania z częściowa interfejsu API wyszukiwania usługi Bing.

```javascript
// build query options from the HTML form
function bingSearchOptions(form) {

    var options = [];
    options.push("mkt=" + form.where.value);
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "off"));
    if (form.what.selectedIndex) options.push("responseFilter=" + form.what.value);
    return options.join("&");
}
```

Na przykład może być funkcji bezpieczne wyszukiwanie `strict`, `moderate`, lub `off`, z `moderate` jest wartość domyślna. Ale naszego formularza jest pole wyboru ma tylko dwa stany. Konwertuje kod JavaScript, to ustawienie jako `strict` lub `off` (nie używamy `moderate`).

`mapquery` Pola nie jest obsługiwane w `bingSearchOptions()` ponieważ jest on używany dla zapytania lokalizacji usługi mapy Bing, nie dla wyszukiwania usługi Bing jednostki.

## <a name="obtaining-a-location"></a>Uzyskiwanie lokalizacji

Oferuje interfejsu API map Bing [ `locationQuery` metody](//msdn.microsoft.com/library/ff701711.aspx), której używamy w celu znalezienia zakres i wprowadza geograficznej lokalizacji użytkownika. Tych współrzędnych są następnie przekazywane API wyszukiwania usługi Bing jednostki z żądaniem użytkownika. Wyniki wyszukiwania ustalić ich priorytety, jednostki i miejsca, które wkrótce określonej lokalizacji.

Firma Microsoft nie może uzyskać dostępu do interfejsu API map Bing przy użyciu zwykłego `XMLHttpRequest` kwerendy w aplikacji sieci Web, ponieważ usługa nie obsługuje zapytań cross-origin. Na szczęście obsługuje on JSONP ("P" jest "o"). Zwykłe odpowiedź JSON opakowane w wywołaniu funkcji jest odpowiedzi JSONP. Żądanie zostało utworzone przez wstawianie za pomocą `<script>` tag do dokumentu. (Ładowanie skryptów nie podlega zasady zabezpieczeń przeglądarki.)

`bingMapsLocate()` Funkcja tworzy i wstawia `<script>` tagu dla zapytania. `jsonp=bingMapsCallback` Segmentu ciągu zapytania określa nazwę funkcji, które ma być wywoływana z odpowiedzią.

```javascript
function bingMapsLocate(where) {

    where = where.trim();
    var url = MAPS_ENDPOINT + "?q=" + encodeURIComponent(where) + 
                "&jsonp=bingMapsCallback&maxResults=1&key=" + getMapsSubscriptionKey();

    var script = document.getElementById("bingMapsResult")
    if (script) script.parentElement.removeChild(script);

    // global variable holds reference to timer that will complete the search if the maps query fails
    timer = setTimeout(function() {
        timer = null;
        var form = document.forms.bing;
        bingEntitySearch(form.query.value, "", bingSearchOptions(form), getSearchSubscriptionKey());
    }, 5000);

    script = document.createElement("script");
    script.setAttribute("type", "text/javascript");
    script.setAttribute("id", "bingMapsResult");
    script.setAttribute("src", url);
    script.setAttribute("onerror", "BingMapsCallback(null)");
    document.body.appendChild(script);

    return false;
}
```

> [!NOTE]
> Jeśli nie odpowiada interfejsu API map Bing, `bingMapsCallBack()` nigdy nie została wywołana funkcja. Zwykle oznacza to, że `bingEntitySearch()` nie jest wywoływana, i nie są wyświetlane wyniki wyszukiwania jednostki. Aby temu zapobiec, `bingMapsLocate()` także ustawia czasomierz w celu wywołania `bingEntitySearch()` po 5 sekund. Funkcja wywołania zwrotnego, aby uniknąć dwukrotnie wyszukiwania jednostki jest logiki.

Po wykonaniu kwerendy `bingMapsCallback()` funkcja jest wywoływana, zgodnie z żądaniem.

```javascript
function bingMapsCallback(response) {

    if (timer) {    // we beat the timer; stop it from firing
        clearTimeout(timer);
        timer = null;
    } else {        // the timer beat us; don't do anything
        return; 
    }

    var location = "";
    var name = "";
    var radius = 1000;

    if (response) {
        try {
            if (response.statusCode === 401) {
                invalidateMapsKey();
            } else if (response.statusCode === 200) {
                var resource = response.resourceSets[0].resources[0];
                var coords   = resource.point.coordinates;
                name         = resource.name;

                // the radius is the largest of the distances between the location and the corners
                // of its bounding box (in case it's not in the center) with a minimum of 1 km
                try {
                    var bbox    = resource.bbox;
                    radius  = Math.max(haversineDistance(bbox[0], bbox[1], coords[0], coords[1]),
                                       haversineDistance(coords[0], coords[1], bbox[2], bbox[1]),
                                       haversineDistance(bbox[0], bbox[3], coords[0], coords[1]),
                                       haversineDistance(coords[0], coords[1], bbox[2], bbox[3]), 1000);
                } catch(e) {  }
                var location = "lat:" + coords[0] + ";long:" + coords[1] + ";re:" + Math.round(radius);
            }
        }
        catch (e) { }   // response is unexpected. this isn't fatal, so just don't provide location
    }

    var form = document.forms.bing;
    if (name) form.mapquery.value = name;
    bingEntitySearch(form.query.value, location, bingSearchOptions(form), getSearchSubscriptionKey());

}
```

Wraz z współrzędne geograficzne, wymaga wyszukiwania usługi Bing jednostki *radius* wskazujące dokładność informacji o lokalizacji. Firma Microsoft obliczenia przy użyciu usługi radius *obwiedni* podanej w odpowiedzi usługi mapy Bing. Pole jest prostokąt otaczający całej lokalizacji. Na przykład, jeśli użytkownik wprowadzi `NYC`, wynik zawiera około centralnej współrzędne nowego Jorku i obwiedni, który obejmuje miasta. 

Firma Microsoft najpierw obliczyć odległości od podstawowego współrzędne do każdego z czterech narożników obwiedni przy użyciu funkcji `haversineDistance()` (tego nie pokazano). Używamy największy z tych czterech odległości jako promień. Minimalny promień jest kilometr. Ta wartość jest używana również jako domyślny, jeśli nie obwiedni podano w odpowiedzi.

Po otrzymaniu współrzędne i usługi radius, następnie nazywamy `bingEntitySearch()` do rzeczywistego przeszukiwania.

## <a name="performing-the-search"></a>W wyszukiwaniu

Podanej kwerendy, lokalizacji, ciąg opcji oraz klucz interfejsu API `BingEntitySearch()` funkcja sprawia, że żądania wyszukiwania usługi Bing jednostki.

```javascript
// perform a search given query, location, options string, and API keys
function bingEntitySearch(query, latlong, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("pole", "mainline", "sidebar", "_json", "_http", "error");

    var request = new XMLHttpRequest();
    var queryurl = SEARCH_ENDPOINT + "?q=" + encodeURIComponent(query) + "&" + options;

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

    if (latlong) request.setRequestHeader("X-Search-Location", latlong);

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
            if (jsobj._type === "SearchResponse") {
                renderSearchResults(jsobj);
            } else {
                renderErrorMessage("No search results in JSON response");
            }
        } else {
            renderErrorMessage("Empty response (are you sending too many requests too quickly?)");
        }
    if (divHidden("pole") && divHidden("mainline") && divHidden("sidebar")) 
        showDiv("noresults", "No results.<p><small>Looking for restaurants or other local businesses? Those currently areen't supported outside the US.</small>");
    }

    // Any other HTTP status is an error
    else {
        // 401 is unauthorized; force re-prompt for API key for next request
        if (this.status === 401) invalidateSearchKey();

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
> Powiodło się żądanie HTTP jest *nie* musi to oznaczać, że całego wyszukiwania zakończyło się pomyślnie. Jeśli wystąpi błąd podczas operacji wyszukiwania, interfejsu API wyszukiwania usługi Bing jednostki zwraca kod stanu 200 HTTP i zawiera informacje o błędzie w odpowiedzi JSON. Ponadto jeśli żądanie zostało ograniczone szybkości, interfejsu API zwraca pustą odpowiedź.

Większość kodu w obu tych funkcji jest przeznaczona do obsługi błędów. Błędy mogą wystąpić w następujących etapów:

|Etap|Potencjalne błędy|Obsługiwane przez|
|-|-|-|
|Obiekt żądania tworzenia JavaScript|Nieprawidłowy adres URL|`try`/`catch` Blok|
|W żądaniu skierowanym|Błędy sieciowe, połączenia zostało przerwane|`error` i `abort` procedury obsługi zdarzeń|
|W wyszukiwaniu|Nieprawidłowe żądania, nieprawidłowy JSON, limity szybkości|testów w `load` obsługi zdarzeń|

Błędy są obsługiwane przez wywołanie metody `renderErrorMessage()` z żadnych szczegółów znane o tym błędzie. Odpowiedź przeszedł pełny gauntlet błąd testów, nazywamy `renderSearchResults()` Aby wyświetlić wyniki wyszukiwania na stronie.

## <a name="displaying-search-results"></a>Wyświetlanie wyników wyszukiwania

Interfejs API wyszukiwania usługi Bing jednostki [wymaga wyświetlić wyniki w kolejności określonej](use-display-requirements.md). Ponieważ interfejsu API może zwracać dwa różne rodzaje odpowiedzi, nie wystarcza do iteracji najwyższego poziomu `Entities` lub `Places` kolekcji w odpowiedzi JSON i wyświetlić wyniki. (Tylko jeden typ wyników, należy użyć `responseFilter` parametr zapytania.)

Zamiast tego używamy `rankingResponse` kolekcji w wynikach wyszukiwania w celu uporządkowania wyników do wyświetlenia. Ten obiekt odwołuje się do elementów w `Entitiess` i/lub `Places` kolekcji.

`rankingResponse` może zawierać maksymalnie trzech zbiorów wyników wyszukiwania, wyznaczony `pole`, `mainline`, i `sidebar`. 

`pole`, jeśli istnieje, jest najbardziej odpowiednie wyniki wyszukiwania i powinna być wyróżniane. `mainline` odnosi się do zbiorczego wyników wyszukiwania. Wyniki połączeniach powinien być wyświetlany natychmiast po `pole` (lub, jeśli najpierw `pole` nie jest obecny). 

Na koniec. `sidebar` odnosi się do wyników wyszukiwania pomocniczych. Może być wyświetlany w rzeczywistych paska bocznego lub po prostu po wynikach połączeniach. Wybraliśmy to drugie samouczek aplikacji.

Każdy element `rankingResponse` kolekcji odnosi się do elementów wyników wyszukiwania rzeczywiste na dwa sposoby różne, ale równoważnej.

| | |
|-|-|
|`id`|`id` Wygląda jak adres URL, ale nie powinna być używana dla łącza. `id` Zgodny typ wyniku klasyfikacji `id` albo wyszukiwania wynik elementu w kolekcji odpowiedzi *lub* całej odpowiedzi kolekcji (takie jak `Entities`).
|`answerType`<br>`resultIndex`|`answerType` Odwołuje się do kolekcji odpowiedzi najwyższego poziomu zawierający wynik (na przykład `Entities`). `resultIndex` Odwołuje się do wyniku indeks w tej kolekcji. Jeśli `resultIndex` jest pominięty, wynik Klasyfikacja odnosi się do całą kolekcję.

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

    // for each possible section, render the resuts from that section
    for (section in {pole: 0, mainline: 0, sidebar: 0}) {
        if (results.rankingResponse[section])
            showDiv(section, renderResultsItems(section, results));
    }
}
```

## <a name="rendering-result-items"></a>Renderowanie elementów wyników

W naszym JavaScript kodu jest obiektem `searchItemRenderers`, zawierający *renderowania:* funkcje, które generują kod HTML dla każdego rodzaju wynik wyszukiwania.

```javascript
searchItemRenderers = { 
    entities: function(item) { ... },
    places: function(item) { ... }
}
```

Funkcja renderowania może przyjmować następujące parametry:

| | |
|-|-|
|`item`|Obiektu JavaScript zawierający właściwości elementu, takie jak adres URL i jego opis.|
|`index`|Indeks elementu wyników w swojej kolekcji.|
|`count`|Liczba elementów w kolekcji elementów wyników wyszukiwania.|

`index` i `count` parametry mogą służyć do liczba wyników do wygenerowania specjalne HTML na początku lub na końcu kolekcji, aby wstawić podziały wiersza po określonej liczby elementów i tak dalej. Jeśli mechanizm renderujący nie muszą tej funkcji, nie musisz zaakceptować te dwa parametry. W rzeczywistości nie analizujemy je w programy renderujące samouczek aplikacji.

Spójrzmy bliższe spojrzenie na `entities` renderowania:

```javascript
    entities: function(item) {
        var html = [];
        html.push("<p class='entity'>");
        if (item.image) {
            var img = item.image;
            if (img.hostPageUrl) html.push("<a href='" + img.hostPageUrl + "'>");
            html.push("<img src='" + img.thumbnailUrl +  "' title='" + img.name + "' height=" + img.height + " width= " + img.width + ">");
            if (img.hostPageUrl) html.push("</a>");
            if (img.provider) {
                var provider = img.provider[0];
                html.push("<small>Image from ");
                if (provider.url) html.push("<a href='" + provider.url + "'>");
                html.push(provider.name ? provider.name : getHost(provider.url));
                if (provider.url) html.push("</a>");
                html.push("</small>");
            }
        }
        html.push("<p>");
        if (item.entityPresentationInfo) {
            var pi = item.entityPresentationInfo;
            if (pi.entityTypeHints || pi.entityTypeDisplayHint) {
                html.push("<i>");
                if (pi.entityTypeDisplayHint) html.push(pi.entityTypeDisplayHint);
                else if (pi.entityTypeHints) html.push(pi.entityTypeHints.join("/"));
                html.push("</i> - ");
            }
        }
        html.push(item.description);
        if (item.webSearchUrl) html.push("&nbsp;<a href='" + item.webSearchUrl + "'>More</a>")
        if (item.contractualRules) {
            html.push("<p><small>");
            var rules = [];
            for (var i = 0; i < item.contractualRules.length; i++) {
                var rule = item.contractualRules[i];
                var link = [];
                if (rule.license) rule = rule.license;
                if (rule.url) link.push("<a href='" + rule.url + "'>");
                link.push(rule.name || rule.text || rule.targetPropertyName + " source");
                if (rule.url) link.push("</a>");
                rules.push(link.join(""));
            }
            html.push("License: " + rules.join(" - "));
            html.push("</small>");
        }
        return html.join("");
    }, // places renderer omitted
```

Funkcja renderowania naszych jednostki:

> [!div class="checklist"]
> * Tworzy HTML `<img>` tag, aby wyświetlić miniaturę obrazu, jeśli istnieje. 
> * Tworzy HTML `<a>` tagu prowadzący do strony, który zawiera obraz.
> * Tworzy opis, który zawiera informacje o obrazie i lokacji, który nie znajduje się na.
> * Zawiera klasyfikacji jednostki wyświetlanie podpowiedzi, jeśli istnieją.
> * Zawiera łącze do wyszukiwania usługi Bing, aby uzyskać więcej informacji o obiekcie.
> * Są wyświetlane informacje o licencji lub autorstwa wymagane przez źródeł danych.

## <a name="persisting-client-id"></a>Utrwalanie identyfikator klienta

Odpowiedzi z interfejsy API wyszukiwania usługi Bing mogą obejmować `X-MSEdge-ClientID` nagłówek, który powinien zostać odesłany do interfejsu API z kolejnych żądań. Jeśli wiele interfejsy API wyszukiwania usługi Bing są używane, ten sam identyfikator klienta należy używać z wszystkich z nich, jeśli to możliwe.

Zapewnianie `X-MSEdge-ClientID` nagłówka umożliwia interfejsy API usługi Bing w celu skojarzenia wszystkich wyszukiwania użytkownika, która ma dwa istotne zalety.

Po pierwsze umożliwia Bing aparatu wyszukiwania w celu dotyczą wyszukiwania, aby znaleźć wyników spełniających kryteria lepiej użytkownika wcześniejszą kontekstu. Jeśli użytkownik został wcześniej wyszukiwane terminy związane z prowadzenia, na przykład nowsze Wyszukaj "doków" preferencyjnie może zwrócić informacje o miejscach do dock żaglowe.

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
> [Dokumentacja interfejsu API Bing encji wyszukiwania](//docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference)

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API map Bing](//msdn.microsoft.com/library/dd877180.aspx)
