---
title: Typy struktury i odpowiedzi odpowiedzi interfejsu API wyszukiwania Bing w sieci Web
titleSuffix: Azure Cognitive Services
description: Więcej informacji na temat typów odpowiedzi i odpowiedzi używany przez API wyszukiwania w Internecie Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 07fb655af25fe590effcb885e7b366346724b50a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60642824"
---
# <a name="bing-web-search-api-response-structure-and-answer-types"></a>Typy struktury i odpowiedzi odpowiedzi interfejsu API wyszukiwania Bing w sieci Web  

Podczas wyszukiwania w Internecie Bing wysyłać żądania wyszukiwania, zwraca [ `SearchResponse` ](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#searchresponse) obiektu w treści odpowiedzi. Obiekt zawiera pola dla każdej odpowiedzi, który Bing określany został istotne dla kwerendy. Ten przykład ilustruje obiekt odpowiedzi, jeśli Bing zwrócone wszystkie odpowiedzi:

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

Zazwyczaj wyszukiwania w Internecie Bing zwraca podzbiór odpowiedzi. Na przykład, jeśli został wyszukiwanego terminu *dinghies prowadzenia*, odpowiedź może zawierać `webPages`, `images`, i `rankingResponse`. Jeśli nie znasz [responseFilter](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#responsefilter) umożliwiające filtrowanie stron sieci Web, odpowiedź zawsze zawiera `webpages` i `rankingResponse` odpowiedzi.

## <a name="webpages-answer"></a>Odpowiedzi stron sieci Web

[Stron sieci Web](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#webanswer) odpowiedzi zawiera listę linków do stron sieci Web, który określany wyszukiwania w Internecie Bing, odpowiednich do kwerendy. Każdy [strony sieci web](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#webpage) na liście będzie zawierać: Nazwa strony, adres url, Wyświetl adres URL, jest krótki opis zawartości i Data Bing znaleźć zawartość.

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

Użyj `name` i `url` utworzyć hiperłącze, które powoduje otwarcie strony sieci Web.

<!-- Remove until this can be replaced with a sanitized version.
The following shows an example of how you might display the webpage in a search results page.

![Rendered webpage example](./media/cognitive-services-bing-web-api/bing-rendered-webpage-example.PNG)
-->

## <a name="images-answer"></a>Obrazy odpowiedzi

[Obrazów](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) odpowiedzi zawiera listę obrazów, które Bing traktować odpowiednich do kwerendy. Każdy [obraz](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image) na liście zawiera adres URL obrazu, jego rozmiar, wymiary i jego format kodowania. Obiekt obrazu zawiera także adres URL miniatury obrazu i wymiary tej miniatury.

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

W zależności od urządzenia użytkownika będą zwykle wyświetlić podzbiór miniatury, z opcją użytkownikowi [stronie za pośrednictwem](paging-webpages.md) pozostałych obrazów.

<!-- Remove until this can be replaced with a sanitized version.
![List of thumbnail images](./media/cognitive-services-bing-web-api/bing-web-image-thumbnails.PNG)
-->

Możesz również powiększać miniatury, gdy użytkownik zatrzyma nad nimi wskaźnik myszy. Pamiętaj, aby po powiększeniu obrazu podać informacje o jego źródle. Na przykład, przez wyodrębnianie hosta z `hostPageDisplayUrl` i wyświetlanie ich znajdującej się poniżej obrazu. Aby uzyskać informacje na temat zmieniania rozmiaru miniatur, zobacz [Zmiana rozmiaru i przycinanie miniatur](./resize-and-crop-thumbnails.md).

<!-- Remove until this can be replaced with a sanitized version.
![Expanded view of thumbnail image](./media/cognitive-services-bing-web-api/bing-web-image-thumbnail-expansion.PNG)
-->

Jeśli użytkownik kliknie miniatury, użyj `webSearchUrl` do wykonania użytkownika do usługi Bing przez strony wyników wyszukiwania obrazów, zawierającą Kolaż obrazów.

Aby uzyskać szczegółowe informacje o odpowiedzi obrazu i obrazów, zobacz [interfejsu API wyszukiwania obrazów](../bing-image-search/search-the-web.md).

## <a name="related-searches-answer"></a>Powiązane wyszukiwania odpowiedzi

[RelatedSearches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#searchresponse-relatedsearches) odpowiedzi zawiera listę najpopularniejszych powiązanych zapytań wprowadzone przez innych użytkowników. Każdy [zapytania](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#query_obj) na liście zawiera ciąg zapytania (`text`), ciąg zapytania z znaków wyróżnianie trafień (`displayText`) oraz adres URL (`webSearchUrl`) na stronie wyników wyszukiwania Bing dla tego zapytania.

```json
{
    "text": "dinghy racing teams",
    "displayText": "dinghy racing teams",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=96C4CF214A0..."
}, ...
```

Użyj `displayText` ciągu zapytania i `webSearchUrl` adres URL, aby utworzyć hiperłącze, które powoduje otwarcie wyszukiwania Bing strony zapytanie powiązane z wyników. Można także użyć `text` ciągu kwerendy API wyszukiwania w Internecie zapytania i wyświetlić wyniki, samodzielnie.

Aby uzyskać informacje dotyczące obsługi podświetlenia znaczników w `displayText`, zobacz [wyróżnianie trafień](./hit-highlighting.md).

Poniżej przedstawiono przykład użycia powiązanych zapytań w Bing.com.

![Przykład powiązane wyszukiwania w witrynie Bing](./media/cognitive-services-bing-web-api/bing-web-rendered-relatedsearches.GIF)

## <a name="videos-answer"></a>Odpowiedź filmów wideo

[Wideo](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos) odpowiedzi zawiera listę wideo Bing traktować odpowiednich do kwerendy. Każdy [wideo](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video) na liście zawiera adres URL filmu wideo, jego czas trwania, wymiary i jego format kodowania. Obiekt wideo zawiera także adres URL miniatury wideo i wymiary tej miniatury.

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

W zależności od urządzenia użytkownika będą zwykle wyświetlić podzbiór wideo z opcją dla użytkownika wyświetlić pozostałe filmów wideo. Zostanie wyświetlony się miniaturę filmu wideo o długości wideo opis (nazwa) i uznanie autorstwa (wydawcy).

<!-- Remove until this can be replaced with a sanitized version.
![List of video thumbnails](./media/cognitive-services-bing-web-api/bing-web-video-thumbnails.PNG)
-->

Użytkownik zatrzyma się miniaturę program `motionThumbnailUrl` grać nieco miniatury wideo. Pamiętaj o nadaniu atrybutu wyświetlonej miniaturze ruchu.

<!-- Remove until this can be replaced with a sanitized version.
![Motion thumbnail of a video](./media/cognitive-services-bing-web-api/bing-web-video-motion-thumbnail.PNG)
-->

Po kliknięciu miniatury będą dostępne następujące opcje wyświetlania filmu wideo:

- Użyj `hostPageUrl` Aby obejrzeć film wideo w witrynie sieci Web hosta (na przykład działanie serwisu YouTube)
- Użyj `webSearchUrl` do wyświetlania wideo w przeglądarce wideo Bing
- Użyj `embedHtml` Osadzanie wideo w swoich potrzeb

Aby uzyskać szczegółowe informacje o wideo odpowiedzi i filmy wideo, zobacz [interfejsu API wyszukiwania wideo](../bing-video-search/search-the-web.md).

## <a name="news-answer"></a>Odpowiedzi na wiadomości

[Wiadomości](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#news) odpowiedzi zawiera listę artykuły z wiadomościami, które Bing traktować odpowiednich do kwerendy. Każdy [artykułu z wiadomościami](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#newsarticle) na liście zawiera nazwę, opis i adres URL do artykułu w witrynie internetowej hosta. Jeśli artykuł zawiera obraz, obiekt zawiera miniaturę obrazu.

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

W zależności od urządzenia użytkownika może wyświetlić podzbiór artykuły z opcją dla użytkownika wyświetlić pozostałe artykuły. Użyj właściwości `name` i `url`, aby utworzyć hiperlink prowadzący użytkownika do artykułu w witrynie hosta. Jeśli artykuł zawiera obraz, upewnij się, możesz klikać obrazie za pomocą `url`. Pamiętaj, aby podać źródło artykułu przy użyciu właściwości `provider`.

<!-- Remove until this can be replaced with a sanitized version.
The following shows an example of how you might display articles in a search results page.

![List of news articles](./media/cognitive-services-bing-web-api/bing-web-news-list.PNG)
-->

Aby uzyskać szczegółowe informacje o wiadomości odpowiedzi i artykuły z wiadomościami, zobacz [interfejsu API wyszukiwania wiadomości](../bing-news-search/search-the-web.md).

## <a name="computation-answer"></a>Obliczenie odpowiedzi

Jeśli użytkownik wprowadzi wyrażeniu matematycznym lub kwerendę konwersję jednostek, odpowiedź może zawierać [obliczeń](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#computation) odpowiedzi. `computation` Odpowiedzi zawiera znormalizowane wyrażenie i jego wynik.

Kwerenda konwersji jednostki jest zapytanie, które konwertuje jedną jednostkę. Na przykład *ile stopy w metrach 10?* lub *ile łyżkach w piłce 1/4?*

Pokazano w poniższym `computation` odpowiedzi *ile stopy w metrach 10?*

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
    "expression": "10 meters",
    "value": "32.808399 feet"
}, ...
```

Poniżej pokazano przykłady zapytań matematyczne i odpowiadające im `computation` odpowiedzi.

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

Wyrażenie matematyczne może zawierać następujących symboli:

|Symbol|Opis|
|------------|-----------------|
|+|Dodanie|
|-|Odejmowanie|
|/|Dzielenie|
|*|Mnożenie|
|^|Zasilania|
|!|Silnia|
|.|Decimal|
|()|Pierwszeństwo grupowania|
|[]|Funkcja|

Wyrażenie matematyczne może zawierać następujących stałych:

|Symbol|Opis|
|------------|-----------------|
|Pi|3.14159...|
|Stopień|Stopień|
|Czy mogę|Liczba urojone|
|e|e, 2.71828...|
|GoldenRatio|Złoty współczynnik 1.61803...|

Wyrażenie matematyczne może zawierać następujące funkcje:

|Symbol|Opis|
|------------|-----------------|
|Sortowanie|Pierwiastek kwadratowy|
|SIN [x], Cos [x], Tan [x]<br />CSC [x] s [x] Cot [x]|Funkcje trygonometryczne (z argumentami w radianach)|
|ArcSin[x], ArcCos[x], ArcTan[x]<br />ArcCsc[x], ArcSec[x], ArcCot[x]|Odwrotny funkcje trygonometryczne (w radianach, dzięki czemu wyników)|
|EXP [x] E ^ x|Funkcja wykładnicza|
|Dziennik [x]|Logarytm naturalny|
|SINH — [x], [x] Cosh Tanh [x]<br />Csch [x], [x] Sech Coth [x]|Funkcje hiperboliczne|
|ArcSinh[x], ArcCosh[x], ArcTanh[x]<br />ArcCsch [x], [x] ArcSech ArcCoth [x]|Odwrotne funkcje hiperboliczne|

Wyrażenia matematyczne, które zawierają zmienne (na przykład 4 x + 6 = 18, gdzie x jest zmienna) nie są obsługiwane.

## <a name="timezone-answer"></a>Strefa czasowa odpowiedzi

Jeśli użytkownik wprowadzi zapytania daty lub czasu, odpowiedź może zawierać [strefa czasowa](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#timezone) odpowiedzi. Ta odpowiedź obsługuje jawnych lub niejawnych zapytania. Niejawne zapytania, takie jak *która godzina?*, zwraca czas lokalny na podstawie lokalizacji użytkownika. Jawne zapytania, takie jak *także czas jest w Seattle?*, zwraca czas lokalny dla Seattle, WA.

`timeZone` Odpowiedzi zawiera nazwę lokalizacji, bieżąca data i Godzina UTC w określonej lokalizacji i przesunięcie czasu UTC. Jeśli granic lokalizacji znajduje się w wielu strefach czasowych, odpowiedź zawiera bieżąca data i Godzina UTC wszystkich stref czasowych w granicach. Na przykład ponieważ stan Florida mieści się w dwóch strefach czasowych, odpowiedź zawiera lokalne datę i godzinę zarówno stref czasowych.  

Jeśli zapytanie żąda czas stanu lub kraju, Bing określa podstawowy miasta w obrębie granicy geograficznej lokalizacji i zwraca go w `primaryCityTime` pola. Jeśli granica zawiera wiele stref czasowych, pozostałe stref czasowych są zwracane w `otherCityTimes` pola.

W poniższym przykładzie przedstawiono zapytania zwracające `timeZone` odpowiedzi.

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

## <a name="spellsuggestion-answer"></a>SpellSuggestion odpowiedzi

Jeśli Bing okaże się, że użytkownik może mieć przeznaczone do Wyszukaj coś innego, odpowiedź zawiera [SpellSuggestions](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#spellsuggestions) obiektu. Na przykład, jeśli użytkownik wyszukuje *pióra carlos*, Bing może określić użytkownik prawdopodobnie ma zamiast wyszukiwać Carlos Pena (oparte na ostatnich wyszukiwania przez inne osoby z *pióra carlos*). Poniżej przedstawiono przykładową odpowiedź pisowni.

```json
"spellSuggestions": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#SpellSuggestions",
    "value": [{
        "text": "carlos pena",
        "displayText": "carlos pena"
    }]
}, ...
```

Poniżej przedstawiono, jak korzysta z sugestii sprawdzania pisowni Bing.

![Przykład sugestii sprawdzania pisowni Bing](./media/cognitive-services-bing-web-api/bing-web-spellingsuggestion.GIF)  

## <a name="response-headers"></a>Nagłówki odpowiedzi

Odpowiedzi z interfejsu API wyszukiwania w sieci Web Bing może zawierać następujące nagłówki:

|||
|-|-|
|`X-MSEdge-ClientID`|Unikatowy identyfikator, który Bing został przypisany do użytkownika|
|`BingAPIs-Market`|Na rynku, który został użyty do spełnienia żądania|
|`BingAPIs-TraceId`|Wpis dziennika na serwerze interfejsu API Bing dla tego żądania (w przypadku pomocy technicznej)|

Jest to szczególnie ważne utrwalić identyfikator klienta i przywrócić go z kolejnymi żądaniami. Gdy to zrobisz, wyszukiwania korzystania z wcześniejszych kontekście Klasyfikacja wyników wyszukiwania, a także zapewnić spójne środowisko użytkownika.

Jednak gdy wywołujesz API wyszukiwania w Internecie Bing poziomu języka JavaScript w przeglądarce wbudowane funkcje zabezpieczeń (między źródłami CORS) może uniemożliwić dostęp do wartości tych nagłówków.

Aby uzyskać dostęp do nagłówków, może wykonać żądania interfejsu API wyszukiwania Bing w sieci Web za pośrednictwem serwera proxy mechanizmu CORS. Odpowiedź z tego serwera proxy zawiera nagłówek `Access-Control-Expose-Headers`, który zezwala na nagłówki odpowiedzi i udostępnia je dla języka JavaScript.

To proste zainstalować serwer proxy CORS, aby zezwolić na naszych [samouczek aplikacji](tutorial-bing-web-search-single-page-app.md) nagłówki opcjonalne klienta dostępu do. Najpierw [zainstaluj platformę Node.js](https://nodejs.org/en/download/), jeśli jeszcze jej nie masz. Potem wprowadź poniższe polecenie w wierszu polecenia.

    npm install -g cors-proxy-server

Następnie Zmień punkt końcowy interfejsu API wyszukiwania Bing w sieci Web w pliku HTML, aby:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

Na koniec uruchom serwer proxy CORS za pomocą następującego polecenia:

    cors-proxy-server

Podczas korzystania z aplikacji samouczka pozostaw okno polecenia otwarte, ponieważ jego zamknięcie spowoduje zatrzymanie serwera proxy. W rozwijanej sekcji nagłówków HTML poniżej wyników wyszukiwania można teraz zobaczyć nagłówek `X-MSEdge-ClientID` (pomiędzy innymi) i sprawdzić, czy jest on taki sam dla każdego żądania.

## <a name="response-headers-in-production"></a>Nagłówki odpowiedzi w środowisku produkcyjnym

Podejście proxy CORS, które są opisane w poprzedniej odpowiedzi jest odpowiednia dla rozwoju, testowania i nauki.

W środowisku produkcyjnym należy obsługiwać skryptu po stronie serwera w tej samej domenie co strony sieci Web, który używa API wyszukiwania w Internecie Bing. Ten skrypt powinien wykonywać wywołania interfejsów API na żądanie z JavaScript strony sieci Web i przekazać wszystkie wyniki, włącznie z nagłówkami, z powrotem do klienta. Ponieważ dwóch zasobów (strony i skryptów) udostępnić punkt początkowy, mechanizm CORS nie jest używany i specjalnych nagłówków są dostępne dla JavaScript na stronie sieci Web.

Takie podejście chroni także klucz interfejsu API przed narażenia na wartość publiczne, ponieważ tylko skrypt po stronie serwera, należy go. Skrypt można użyć innej metody, aby upewnić się, że żądanie jest autoryzowane.

Poniżej przedstawiono, jak korzysta z sugestii sprawdzania pisowni Bing.

![Przykład sugestii sprawdzania pisowni Bing](./media/cognitive-services-bing-web-api/bing-web-spellingsuggestion.GIF)  

## <a name="next-steps"></a>Kolejne kroki  

* Przegląd [ograniczanie żądań](throttling-requests.md) dokumentacji.  

## <a name="see-also"></a>Zobacz także  

* [Dokumentacja interfejsu API wyszukiwania w sieci Web Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference)
