---
title: Struktura odpowiedzi interfejsu API wyszukiwania w sieci Web usługi Bing i typy odpowiedzi
titleSuffix: Azure Cognitive Services
description: Podczas wysyłania wyszukiwania w sieci Web Bing żądanie wyszukiwania, zwraca `SearchResponse` obiekt w treści odpowiedzi.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 95ebfaef863a1fa05e8a5d3b46fca9659c61f6b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74110621"
---
# <a name="bing-web-search-api-response-structure-and-answer-types"></a>Struktura odpowiedzi interfejsu API wyszukiwania w sieci Web usługi Bing i typy odpowiedzi  

Podczas wysyłania wyszukiwania w sieci Web Bing żądanie wyszukiwania, zwraca [`SearchResponse`](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#searchresponse) obiekt w treści odpowiedzi. Obiekt zawiera pole dla każdej odpowiedzi, która według binga była istotna dla kwerendy. W tym przykładzie ilustruje obiekt odpowiedzi, jeśli Bing zwrócił wszystkie odpowiedzi:

```json
{
    "_type": "SearchResponse",
    "queryContext": {...},
    "webPages": {...},
    "images": {...},
    "relatedSearches": {...},
    "videos": {...},
    "news": {...},
    "spellSuggestion": {...},
    "computation": {...},
    "timeZone": {...},
    "rankingResponse": {...}
}, ...
```

Zazwyczaj wyszukiwanie w sieci Web Bing zwraca podzbiór odpowiedzi. Na przykład, jeśli terminem zapytania był *rejs pontonów,* odpowiedź może zawierać `webPages`, `images`i `rankingResponse`. Jeśli nie użyto [responseFilter](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#responsefilter) do odfiltrowania stron `webpages` sieci `rankingResponse` Web, odpowiedź zawsze zawiera i odpowiedzi.

[!INCLUDE [cognitive-services-bing-url-note](../../../includes/cognitive-services-bing-url-note.md)]

## <a name="webpages-answer"></a>Odpowiedź na strony sieci Web

Odpowiedź [na strony sieci Web](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webanswer) zawiera listę łączy do stron sieci Web, które według wyszukiwania bing web search były istotne dla kwerendy. Każda [strona internetowa](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webpage) na liście będzie zawierać: nazwę strony, adres URL, wyświetlany adres URL, krótki opis zawartości i datę, w którym Bing znalazł zawartość.

```json
{
    "id": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0",
    "name": "Dinghy sailing",
    "url": "https:\/\/www.bing.com\/cr?IG=3A43CA5...",
    "displayUrl": "https:\/\/en.contoso.com\/wiki\/Dinghy_sailing",
    "snippet": "Dinghy sailing is the activity of sailing small boats...",
    "dateLastCrawled": "2017-04-05T16:25:00"
}, ...
```

Użyj `name` `url` i utwórz hiperłącze, które przeniesie użytkownika na stronę sieci Web.

<!-- Remove until this can be replaced with a sanitized version.
The following shows an example of how you might display the webpage in a search results page.

![Rendered webpage example](./media/cognitive-services-bing-web-api/bing-rendered-webpage-example.PNG)
-->

## <a name="images-answer"></a>Odpowiedź na obrazy

Odpowiedź [obrazów](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) zawiera listę obrazów, które bing myśli były istotne dla kwerendy. Każdy [obraz](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) na liście zawiera adres URL obrazu, jego rozmiar, jego wymiary i format kodowania. Obiekt obrazu zawiera także adres URL miniatury obrazu i wymiary tej miniatury.

```json
{
    "name": "Rich Passage Sailing Dinghy",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=3A43CA5CA64...",
    "thumbnailUrl": "https:\/\/tse1.mm.bing.net\/th?id=OIP....",
    "datePublished": "2011-10-29T11:26:00",
    "contentUrl": "http:\/\/upload.contoso.com\/sailing\/...",
    "hostPageUrl": "http:\/\/www.bing.com\/cr?IG=3A43CA5CA6464....",
    "contentSize": "79239 B",
    "encodingFormat": "jpeg",
    "hostPageDisplayUrl": "http:\/\/en.contoso.com\/wiki\/File...",
    "width": 526,
    "height": 688,
    "thumbnail": {
        "width": 229,
        "height": 300
    },
    "insightsSourcesSummary": {
        "shoppingSourcesCount": 0,
        "recipeSourcesCount": 0
    }
}, ...
```

W zależności od urządzenia użytkownika zazwyczaj wyświetlany jest podzbiór miniatur z opcją przechodzenia przez użytkownika do [strony pozostałych](paging-webpages.md) obrazów.

<!-- Remove until this can be replaced with a sanitized version.
![List of thumbnail images](./media/cognitive-services-bing-web-api/bing-web-image-thumbnails.PNG)
-->

Możesz również powiększać miniatury, gdy użytkownik zatrzyma nad nimi wskaźnik myszy. Pamiętaj, aby po powiększeniu obrazu podać informacje o jego źródle. Na przykład, wyodrębniając hosta i `hostPageDisplayUrl` wyświetlając go pod obrazem. Aby uzyskać informacje na temat zmieniania rozmiaru miniatur, zobacz [Zmiana rozmiaru i przycinanie miniatur](./resize-and-crop-thumbnails.md).

<!-- Remove until this can be replaced with a sanitized version.
![Expanded view of thumbnail image](./media/cognitive-services-bing-web-api/bing-web-image-thumbnail-expansion.PNG)
-->

Jeśli użytkownik kliknie miniaturę, użyj, `webSearchUrl` aby zabrać użytkownika do strony wyników wyszukiwania Bing dla obrazów, która zawiera kolaż obrazów.

Aby uzyskać szczegółowe informacje na temat odpowiedzi obrazu i obrazów, zobacz [Interfejs API wyszukiwania obrazów](../bing-image-search/search-the-web.md).

## <a name="related-searches-answer"></a>Odpowiedź na powiązane wyszukiwania

Odpowiedź [relatedSearches](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#searchresponse-relatedsearches) zawiera listę najpopularniejszych zapytań powiązanych wykonanych przez innych użytkowników. Każda [kwerenda](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query_obj) na liście zawiera`text`ciąg zapytania ( ), ciąg`displayText`zapytania ze`webSearchUrl`znakami wyróżniania trafień ( ) i adres URL ( ) na stronie wyników wyszukiwania Bing dla tej kwerendy.

```json
{
    "text": "dinghy racing teams",
    "displayText": "dinghy racing teams",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=96C4CF214A0..."
}, ...
```

Użyj `displayText` ciągu zapytania `webSearchUrl` i adresu URL, aby utworzyć hiperłącze, które prowadzi użytkownika do strony wyników wyszukiwania Bing dla powiązanej kwerendy. Można również użyć `text` ciągu zapytania we własnej kwerendzie interfejsu API wyszukiwania w sieci Web i wyświetlić wyniki samodzielnie.

Aby uzyskać informacje o tym, jak `displayText`obsługiwać znaczniki podświetlania w , zobacz [Wyróżnianie trafień](../bing-web-search/hit-highlighting.md).

Poniżej przedstawiono przykład użycia zapytań powiązanych w Bing.com.

![Przykład powiązanych wyszukiwań w u bingu](./media/cognitive-services-bing-web-api/bing-web-rendered-relatedsearches.GIF)

## <a name="videos-answer"></a>Odpowiedź na filmy

Odpowiedź [na filmy](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) zawiera listę filmów, które bing uważali za istotne dla zapytania. Każdy [film wideo](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video) na liście zawiera adres URL filmu, jego czas trwania, jego wymiary i format kodowania. Obiekt wideo zawiera także adres URL miniatury wideo i wymiary tej miniatury.

```json
{
    "name": "Sailing dinghy",
    "description": "Northwind Traders is a 12 foot gunter rigged...",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=1CAE739681D84...",
    "thumbnailUrl": "https:\/\/tse2.mm.bing.net\/th?id=OVP.wsKiL...",
    "datePublished": "2013-11-06T01:56:28",
    "publisher": [{
        "name": "Fabrikam"
    }],
    "contentUrl": "https:\/\/www.fabrikam.com\/watch?v=MrVBWZpJjX",
    "hostPageUrl": "https:\/\/www.bing.com\/cr?IG=1CAE739681D8400DB...",
    "encodingFormat": "mp4",
    "hostPageDisplayUrl": "https:\/\/www.fabrikam.com\/watch?v=MrBWZpJjXo",
    "width": 1280,
    "height": 720,
    "duration": "PT3M47S",
    "motionThumbnailUrl": "https:\/\/tse2.mm.bing.net\/th?id=OM.oa...",
    "embedHtml": "<iframe width=\"1280\" height=\"720\" src=\"http:\/\/www....><\/iframe>",
    "allowHttpsEmbed": true,
    "viewCount": 19089,
    "thumbnail": {
        "width": 300,
        "height": 168
    },
    "allowMobileEmbed": true,
    "isSuperfresh": false
}, ...
```

W zależności od urządzenia użytkownika zazwyczaj wyświetlany jest podzbiór filmów z opcją wyświetlania pozostałych filmów. Chcesz wyświetlić miniaturę filmu z długością, opisem (nazwą) i atrybucją (wydawca).

<!-- Remove until this can be replaced with a sanitized version.
![List of video thumbnails](./media/cognitive-services-bing-web-api/bing-web-video-thumbnails.PNG)
-->

Gdy użytkownik najedzie kursorem `motionThumbnailUrl` na miniaturę, możesz odtworzyć miniaturową wersję filmu. Pamiętaj o nadaniu atrybutu wyświetlonej miniaturze ruchu.

<!-- Remove until this can be replaced with a sanitized version.
![Motion thumbnail of a video](./media/cognitive-services-bing-web-api/bing-web-video-motion-thumbnail.PNG)
-->

Po kliknięciu miniatury będą dostępne następujące opcje wyświetlania filmu wideo:

- Użyj, `hostPageUrl` aby wyświetlić film w witrynie hosta (na przykład YouTube)
- Użyj `webSearchUrl` do wyświetlania wideo w przeglądarce wideo Bing
- Użyj, `embedHtml` aby osadzić film we własnym doświadczeniu

Aby uzyskać szczegółowe informacje na temat odpowiedzi wideo i klipów wideo, zobacz [Interfejs API wyszukiwania wideo](../bing-video-search/search-the-web.md).

## <a name="news-answer"></a>Odpowiedź na wiadomości

Odpowiedź [wiadomości](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#news) zawiera listę artykułów, które Bing myśli były istotne dla zapytania. Każdy [artykułu z wiadomościami](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle) na liście zawiera nazwę, opis i adres URL do artykułu w witrynie internetowej hosta. Jeśli artykuł zawiera obraz, obiekt zawiera miniaturę obrazu.

```json
{
    "name": "WC Sailing Qualifies for America Trophy with...",
    "url": "http:\/\/www.bing.com\/cr?IG=3445EEF15DAF4FFFBF7...",
    "image": {
        "contentUrl": "http:\/\/www.contoso.com\/sports\/sail...",
        "thumbnail": {
            "contentUrl": "https:\/\/www.bing.com\/th?id=ON.1...",
            "width": 400,
            "height": 272
        }
    },
    "description": "The WC sailing team qualified for a...",
    "provider": [{
        "_type": "Organization",
        "name": "contoso.com"
    }],
    "datePublished": "2017-04-16T21:56:00"
}, ...
```

W zależności od urządzenia użytkownika należy wyświetlić podzbiór artykułów z wiadomościami z opcją wyświetlania pozostałych artykułów przez użytkownika. Użyj właściwości `name` i `url`, aby utworzyć hiperlink prowadzący użytkownika do artykułu w witrynie hosta. Jeśli artykuł zawiera obraz, należy go `url`kliknąć za pomocą programu . Pamiętaj, aby podać źródło artykułu przy użyciu właściwości `provider`.

<!-- Remove until this can be replaced with a sanitized version.
The following shows an example of how you might display articles in a search results page.

![List of news articles](./media/cognitive-services-bing-web-api/bing-web-news-list.PNG)
-->

Aby uzyskać szczegółowe informacje na temat odpowiedzi na wiadomości i artykułów z wiadomościami, zobacz [Interfejs API wyszukiwania wiadomości](../bing-news-search/search-the-web.md).

## <a name="computation-answer"></a>Odpowiedź obliczeń

Jeśli użytkownik wprowadzi wyrażenie matematyczne lub kwerendę konwersji jednostkowej, odpowiedź może zawierać odpowiedź [obliczeń.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#computation) Odpowiedź `computation` zawiera znormalizowane wyrażenie i jego wynik.

Kwerenda konwersji jednostek to kwerenda konwertowana na jedną jednostkę na inną. Na przykład, *Ile stóp w 10 metrów?* lub *Ile łyżek w 1/4 szklanki?*

Poniżej przedstawiono `computation` odpowiedź na *Ile stóp w 10 metrów?*

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
    "expression": "10 meters",
    "value": "32.808399 feet"
}, ...
```

Poniżej przedstawiono przykłady zapytań matematycznych i odpowiadające im `computation` odpowiedzi.

```
Query: (5+3)(10/2)+8
Encoded query: %285%2B3%29%2810%2F2%29%2B8
```

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
    "expression": "((5+3)*(10\/2))+8",
    "value": "48"
}
```

```
Query: sqrt(4^2+8^2)
Encoded query: sqrt%284^2%2B8^2%29
```

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
    "expression": "sqrt((4^2)+(8^2))",
    "value": "8.94427191"
}
```

```
Query: 30 6/8 - 18 8/16
Encoded query: 30%206%2F8%20-%2018%208%2F16
```

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#WolframAlpha",
    "expression": "30 6\/8-18 8\/16",
    "value": "12.25"
}
```

```
Query: 8^2+11^2-2*8*11*cos(37)
Encoded query: 8^2%2B11^2-2*8*11*cos%2837%29
```

```json
"computation": {
        "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
        "expression": "(8^2)+(11^2)-(2*8*11*cos(37))",
        "value": "44.4401502"
}
```

Wyrażenie matematyczne może zawierać następujące symbole:

|Symbol|Opis|
|------------|-----------------|
|+|Dodawanie|
|-|Odejmowanie|
|/|Dzielenie|
|*|Mnożenie|
|^|Zasilanie|
|!|Silnia|
|.|Wartość dziesiętna|
|()|Grupowanie pierwszeństwa|
|[]|Funkcja|

Wyrażenie matematyczne może zawierać następujące stałe:

|Symbol|Opis|
|------------|-----------------|
|Pi|3.14159...|
|Stopnia|Stopnia|
|mogę|Liczba wyimaginowana|
|e|e, 2.71828...|
|GoldenRatio (GoldenRatio)|Złoty stosunek, 1,61803...|

Wyrażenie matematyczne może zawierać następujące funkcje:

|Symbol|Opis|
|------------|-----------------|
|Sortowanie|Pierwiastek kwadratowy|
|Sin[x], Cos[x], Tan[x]<br />Csc[x], Sec[x], Łóżeślanie[x]|Funkcje trygonometryczne (z argumentami w radianach)|
|ArcSin[x], ArcCos[x], ArcTan[x]<br />ArcCsc[x], ArcSec[x], ArcCot[x]|Odwrotne funkcje trygonometryczne (dające wyniki w radianach)|
|Exp[x], E^x|Funkcja wykładnicza|
|Dziennik[x]|Logarytm naturalny|
|Sinh[x], Cosh[x], Tanh[x]<br />Csch[x], Sech[x], Coth[x]|Funkcje hiperboliczne|
|ArcSinh[x], ArcCosh[x], ArcTanh[x]<br />ArcCsch[x], ArcSech[x], ArcCoth[x]|Odwrotne funkcje hiperboliczne|

Wyrażenia matematyczne zawierające zmienne (na przykład 4x+6=18, gdzie x jest zmienną) nie są obsługiwane.

## <a name="timezone-answer"></a>Odpowiedź TimeZone

Jeśli użytkownik wprowadzi kwerendę o godzinie lub dacie, odpowiedź może zawierać odpowiedź [TimeZone.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#timezone) Ta odpowiedź obsługuje zapytania niejawne lub jawne. Zapytanie niejawne, takie jak *What time is it?*, zwraca czas lokalny na podstawie lokalizacji użytkownika. Jawne zapytanie, takie jak *O której godzinie jest w Seattle?*, zwraca czas lokalny dla Seattle, WA.

Odpowiedź `timeZone` zawiera nazwę lokalizacji, bieżącą datę i godzinę UTC w określonej lokalizacji oraz przesunięcie UTC. Jeśli granica lokalizacji znajduje się w wielu strefach czasowych, odpowiedź zawiera bieżącą datę i godzinę UTC wszystkich stref czasowych w granicach. Na przykład ponieważ stan Floryda mieści się w dwóch strefach czasowych, odpowiedź zawiera lokalną datę i godzinę obu stref czasowych.  

Jeśli kwerenda żąda czasu stanu lub kraju/regionu, Bing określa miasto podstawowe w granicach geograficznych `primaryCityTime` lokalizacji i zwraca go w polu. Jeśli granica zawiera wiele stref czasowych, pozostałe strefy `otherCityTimes` czasowe są zwracane w polu.

Poniżej przedstawiono przykładowe `timeZone` zapytania, które zwracają odpowiedź.

```
Query: What time is it?

"timeZone": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
    "primaryCityTime": {
        "location": "Redmond, Washington, United States",
        "time": "2015-10-27T08:38:12.1189231Z",
        "utcOffset": "UTC-7"
    }
}

Query: What time is it in the Pacific time zone?

"timeZone": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
    "primaryCityTime": {
        "location": "Pacific Time Zone",
        "time": "2015-10-23T12:33:19.0728146Z",
        "utcOffset": "UTC-7"
    }
}

Query: Time in Florida?

"timeZone": {
        "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
        "primaryCityTime": {
            "location": "Tallahassee, Florida, United States",
            "time": "2015-10-23T13:04:56.6774389Z",
            "utcOffset": "UTC-4"
        },
        "otherCityTimes": [{
            "location": "Pensacola",
            "time": "2015-10-23T12:04:56.6664294Z",
            "utcOffset": "UTC-5"
        }]
}

Query: What time is it in the U.S.

"timeZone": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
    "primaryCityTime": {
        "location": "Washington, D.C., United States",
        "time": "2015-10-23T15:27:59.8892745Z",
        "utcOffset": "UTC-4"
    },
    "otherCityTimes": [{
        "location": "Honolulu",
        "time": "2015-10-23T09:27:59.8892745Z",
        "utcOffset": "UTC-10"
    },
    {
        "location": "Anchorage",
        "time": "2015-10-23T11:27:59.8892745Z",
        "utcOffset": "UTC-8"
    },
    {
        "location": "Phoenix",
        "time": "2015-10-23T12:27:59.8892745Z",
        "utcOffset": "UTC-7"
    },
    {
        "location": "Los Angeles",
        "time": "2015-10-23T12:27:59.8942788Z",
        "utcOffset": "UTC-7"
    },
    {
        "location": "Denver",
        "time": "2015-10-23T13:27:59.8812681Z",
        "utcOffset": "UTC-6"
    },
    {
        "location": "Chicago",
        "time": "2015-10-23T14:27:59.8892745Z",
        "utcOffset": "UTC-5"
    }]
}
```

## <a name="spellsuggestion-answer"></a>Odpowiedź na pisownię

Jeśli Bing określa, że użytkownik może mieć zamiar wyszukać coś innego, odpowiedź zawiera [SpellSuggestions](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#spellsuggestions) obiektu. Na przykład, jeśli użytkownik wyszukuje *carlos pen*, Bing może ustalić, że użytkownik prawdopodobnie zamierzał wyszukać Carlos Pena zamiast (na podstawie wcześniejszych wyszukiwań przez innych *carlos pen*). Poniżej przedstawiono przykładową odpowiedź na zaklęcie.

```json
"spellSuggestions": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#SpellSuggestions",
    "value": [{
        "text": "carlos pena",
        "displayText": "carlos pena"
    }]
}, ...
```

## <a name="response-headers"></a>Nagłówki odpowiedzi

Odpowiedzi z interfejsu API wyszukiwania w sieci Web usługi Bing mogą zawierać następujące nagłówki:

|||
|-|-|
|`X-MSEdge-ClientID`|Unikatowy identyfikator przypisany przez bing do użytkownika|
|`BingAPIs-Market`|Rynek, który został użyty do spełnienia wniosku|
|`BingAPIs-TraceId`|Wpis dziennika na serwerze interfejsu API usługi Bing dla tego żądania (dla pomocy technicznej)|

Jest szczególnie ważne, aby utrwalić identyfikator klienta i zwrócić go z kolejnymi żądaniami. Po wykonaniu tej tej funkcji wyszukiwanie użyje kontekstu z przeszłości w wynikach wyszukiwania rankingowego, a także zapewni spójne środowisko użytkownika.

Jednak po wywołaniu interfejsu API wyszukiwania w sieci Web Bing z języka JavaScript wbudowane funkcje zabezpieczeń przeglądarki (CORS) mogą uniemożliwić dostęp do wartości tych nagłówków.

Aby uzyskać dostęp do nagłówków, można wykonać żądanie interfejsu API wyszukiwania w sieci Web usługi Bing za pośrednictwem serwera proxy CORS. Odpowiedź z tego serwera proxy zawiera nagłówek `Access-Control-Expose-Headers`, który zezwala na nagłówki odpowiedzi i udostępnia je dla języka JavaScript.

Łatwo jest zainstalować serwer proxy CORS, aby umożliwić naszej [aplikacji samouczka](tutorial-bing-web-search-single-page-app.md) dostęp do opcjonalnych nagłówków klienta. Najpierw [zainstaluj platformę Node.js](https://nodejs.org/en/download/), jeśli jeszcze jej nie masz. Potem wprowadź poniższe polecenie w wierszu polecenia.

    npm install -g cors-proxy-server

Następnie zmień punkt końcowy interfejsu API wyszukiwania w sieci Web usługi Bing w pliku HTML na:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

Na koniec uruchom serwer proxy CORS za pomocą następującego polecenia:

    cors-proxy-server

Podczas korzystania z aplikacji samouczka pozostaw okno polecenia otwarte, ponieważ jego zamknięcie spowoduje zatrzymanie serwera proxy. W rozwijanej sekcji nagłówków HTML poniżej wyników wyszukiwania można teraz zobaczyć nagłówek `X-MSEdge-ClientID` (pomiędzy innymi) i sprawdzić, czy jest on taki sam dla każdego żądania.

## <a name="response-headers-in-production"></a>Nagłówki odpowiedzi w produkcji

Podejście serwera proxy CORS opisane w poprzedniej odpowiedzi jest odpowiednie do rozwoju, testowania i uczenia się.

W środowisku produkcyjnym należy hostować skrypt po stronie serwera w tej samej domenie co strona sieci Web, która używa interfejsu API wyszukiwania w sieci Web Bing. Ten skrypt powinien nawiązywać wywołania interfejsu API na żądanie ze strony sieci Web JavaScript i przekazywać wszystkie wyniki, w tym nagłówki, z powrotem do klienta. Ponieważ dwa zasoby (strona i skrypt) współużytkują pochodzenie, mechanizm CORS nie jest używany, a specjalne nagłówki są dostępne dla języka JavaScript na stronie sieci Web.

Takie podejście chroni również klucz interfejsu API przed narażeniem na działanie opinii publicznej, ponieważ potrzebuje go tylko skrypt po stronie serwera. Skrypt można użyć innej metody, aby upewnić się, że żądanie jest autoryzowane.

Poniżej przedstawiono, jak Bing używa sugestii pisowni.

![Przykład sugestii pisowni Bing](./media/cognitive-services-bing-web-api/bing-web-spellingsuggestion.GIF)  

## <a name="next-steps"></a>Następne kroki  

* Przejrzyj dokumentację [ograniczania żądań.](throttling-requests.md)  

## <a name="see-also"></a>Zobacz też  

* [Odwołanie do interfejsu API wyszukiwania w sieci Web usługi Bing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)
