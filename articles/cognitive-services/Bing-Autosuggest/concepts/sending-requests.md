---
title: Wysyłanie żądań do interfejsu API autosuggest usługi Bing
titleSuffix: Azure Cognitive Services
description: Interfejs API automatycznego sugerowania Bing zwraca listę proponowanych zapytań na podstawie częściowego ciągu zapytania w polu wyszukiwania. Dowiedz się więcej o wysyłaniu żądań.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: scottwhi
ms.openlocfilehash: d479548e682e814345e13d9416d08ec453f90304
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74072862"
---
# <a name="sending-requests-to-the-bing-autosuggest-api"></a>Wysyłanie żądań do interfejsu API autosuggest usługi Bing.

Jeśli aplikacja wysyła zapytania do dowolnego z interfejsów API wyszukiwania Bing, możesz użyć interfejsu API automatycznego sugerowania Bing w celu ulepszenia środowiska wyszukiwania dla użytkowników. Interfejs API automatycznego sugerowania Bing zwraca listę proponowanych zapytań na podstawie częściowego ciągu zapytania w polu wyszukiwania. Gdy znaki są wprowadzane w polu wyszukiwania w aplikacji, można wyświetlać sugestie na liście rozwijanej. Ten artykuł służy do dowiedzieć się więcej o wysyłaniu żądań do tego interfejsu API. 

## <a name="bing-autosuggest-api-endpoint"></a>Punkt końcowy interfejsu API automatycznego przełączania Bing

**Interfejs API automatycznego sugerowania Bing** zawiera jeden punkt końcowy, który zwraca listę sugerowanych zapytań z częściowego wyszukiwanego hasła.

Aby uzyskać sugerowane zapytania przy użyciu interfejsu `GET` API Bing, wyślij żądanie do następującego punktu końcowego. Użyj nagłówków i parametrów adresu URL, aby zdefiniować dalsze specyfikacje.

**Punkt końcowy:** Zwraca sugestie wyszukiwania jako wyniki JSON, które są `?q=""`istotne dla danych wejściowych użytkownika zdefiniowanych przez program .

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/Suggestions 
```

Aby uzyskać szczegółowe informacje na temat nagłówków, parametrów, kodów rynku, obiektów odpowiedzi, błędów itp., zobacz odwołanie do [interfejsu API interfejsu API w wersji 7 automatycznego przełączania Bing.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference)

Interfejsy API **usługi Bing** obsługują akcje wyszukiwania, które zwracają wyniki zgodnie z ich typem.Wszystkie punkty końcowe wyszukiwania zwracają wyniki jako obiekty odpowiedzi JSON.
Wszystkie punkty końcowe obsługują kwerendy, które zwracają określony język i/lub lokalizację według długości geograficznej, szerokości geograficznej i promienia wyszukiwania.

Aby uzyskać pełne informacje na temat parametrów obsługiwanych przez każdy punkt końcowy, zobacz strony odwołań dla każdego typu.
Aby zapoznać się z przykładami podstawowych żądań korzystających z interfejsu API autosuggest, zobacz [Autosuggest Quickstarts](https://docs.microsoft.com/azure/cognitive-services/Bing-Autosuggest).

## <a name="bing-autosuggest-api-requests"></a>Żądania interfejsu API automatycznego zasysania usłudze Bing

> [!NOTE]
> * Żądania do interfejsu API autosuggestu Bing muszą używać protokołu HTTPS.

Zalecamy, aby wszystkie żądania pochodziły z serwera. Dystrybucja klucza jako część aplikacji klienckiej zapewnia więcej możliwości złośliwego dostępu innych firm. Ponadto wykonywanie połączeń z serwera zapewnia pojedynczy punkt uaktualnienia dla przyszłych aktualizacji.

Żądanie musi określać parametr zapytania [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query), który zawiera częściowy termin wyszukiwany przez użytkownika. Chociaż jest to opcjonalne, żądanie powinno również określać parametr zapytania [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#mkt), który identyfikuje rynek, z którego mają pochodzić wyniki. Aby uzyskać listę opcjonalnych parametrów zapytania, zobacz [Query Parameters (Parametry zapytania)](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query-parameters). Wszystkie wartości parametrów zapytania muszą być zakodowane w adresie URL.

Żądanie musi określać nagłówek [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#subscriptionkey). Mimo że jest to opcjonalne, zachęcamy, aby określić również następujące nagłówki:

- [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#useragent)
- [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientid)
- [X-Search-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientip)
- [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#location)

Nagłówki adresu IP klienta i lokalizacji są ważne z punktu widzenia zwracania zawartości odpowiedniej dla danej lokalizacji.

Aby uzyskać listę wszystkich nagłówków żądań i odpowiedzi, zobacz [Nagłówki](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#headers).

> [!NOTE]
> Po wywołaniu interfejsu API automatycznego pośrednika obsługi języka Bing z języka JavaScript wbudowane funkcje zabezpieczeń przeglądarki mogą uniemożliwić dostęp do wartości tych nagłówków.

Aby rozwiązać ten problem, można wykonać żądanie interfejsu API autosuggest Bing za pośrednictwem serwera proxy CORS. Odpowiedź z tego serwera proxy zawiera nagłówek `Access-Control-Expose-Headers`, który zezwala na nagłówki odpowiedzi i udostępnia je dla języka JavaScript.

Łatwo jest zainstalować serwer proxy CORS, aby umożliwić naszej [aplikacji samouczka](../tutorials/autosuggest.md) dostęp do opcjonalnych nagłówków klienta. Najpierw [zainstaluj platformę Node.js](https://nodejs.org/en/download/), jeśli jeszcze jej nie masz. Potem wprowadź poniższe polecenie w wierszu polecenia.

    npm install -g cors-proxy-server

Następnie zmień punkt końcowy interfejsu API automatycznego przełączania zgodnie z użyczeniem usługi Bing w pliku HTML na:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/Suggestions

Na koniec uruchom serwer proxy CORS za pomocą następującego polecenia:

    cors-proxy-server

Podczas korzystania z aplikacji samouczka pozostaw okno polecenia otwarte, ponieważ jego zamknięcie spowoduje zatrzymanie serwera proxy. W rozwijanej sekcji nagłówków HTML poniżej wyników wyszukiwania można teraz zobaczyć nagłówek `X-MSEdge-ClientID` (pomiędzy innymi) i sprawdzić, czy jest on taki sam dla każdego żądania.

Żądania powinny zawierać wszystkie sugerowane parametry zapytania i nagłówki. 

W poniższym przykładzie przedstawiono żądanie, które zwraca sugerowane ciągi zapytania dla terminu *sail*.

> ```http
> GET https://api.cognitive.microsoft.com/bing/v7.0/suggestions?q=sail&mkt=en-us HTTP/1.1
> Ocp-Apim-Subscription-Key: 123456789ABCDE
> X-MSEdge-ClientIP: 999.999.999.999
> X-Search-Location: lat:47.60357;long:-122.3295;re:100
> X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
> Host: api.cognitive.microsoft.com
> ```

Jeśli jest to Twoje pierwsze wywoływanie dowolnego z interfejsów API Bing, nie dołączaj nagłówka identyfikatora klienta. Nagłówek identyfikatora klienta należy uwzględnić tylko wtedy, gdy interfejs API Bing został już wywołany i usługa Bing zwróciła identyfikator klienta dla kombinacji użytkownika i urządzenia.

Następująca grupa sugestii sieci Web jest odpowiedzią na powyższe żądanie. Grupa zawiera listę sugestii zapytań wyszukiwania, z `displayText` `query`każdą `url` sugestią, w tym , i pole.

Pole `displayText` zawiera sugerowane zapytanie, używane do wypełnienia listy rozwijanej w polu wyszukiwania. Należy wyświetlić wszystkie sugestie, które zawiera odpowiedź, w podanej kolejności.  

Jeśli użytkownik wybierze kwerendę z listy rozwijanej, można jej użyć do wywołania jednego z [interfejsów API wyszukiwania Bing](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/bing-api-comparison?toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fbing-autosuggest%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json) i wyświetlenia wyników `url` samodzielnie lub wysłania użytkownika do strony wyników usługi Bing przy użyciu zwróconego pola.

[!INCLUDE [cognitive-services-bing-url-note](../../../../includes/cognitive-services-bing-url-note.md)]

```json
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "Suggestions",
    "queryContext" : {
        "originalQuery" : "sail"
    },
    "suggestionGroups" : [{
        "name" : "Web",
        "searchSuggestions" : [{
            "url" : "https:\/\/www.bing.com\/search?q=sailing+lessons+seattle&FORM=USBAPI",
            "displayText" : "sailing lessons seattle",
            "query" : "sailing lessons seattle",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailor+moon+news&FORM=USBAPI",
            "displayText" : "sailor moon news",
            "query" : "sailor moon news",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailor+jack%27s+lincoln+city&FORM=USBAPI",
            "displayText" : "sailor jack's lincoln city",
            "query" : "sailor jack's lincoln city",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailing+anarchy&FORM=USBAPI",
            "displayText" : "sailing anarchy",
            "query" : "sailing anarchy",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailboats+for+sale&FORM=USBAPI",
            "displayText" : "sailboats for sale",
            "query" : "sailboats for sale",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailstn.mylabsplus.com&FORM=USBAPI",
            "displayText" : "sailstn.mylabsplus.com",
            "query" : "sailstn.mylabsplus.com",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailusfood&FORM=USBAPI",
            "displayText" : "sailusfood",
            "query" : "sailusfood",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailboats+for+sale+seattle&FORM=USBAPI",
            "displayText" : "sailboats for sale seattle",
            "query" : "sailboats for sale seattle",
            "searchKind" : "WebSearch"
        }]
    }]
}
```

## <a name="next-steps"></a>Następne kroki

- [Czym jest funkcja automatycznego sugerowania Bing?](../get-suggested-search-terms.md)
- [Bing Autosuggest API v7 reference (Dokumentacja dotycząca automatycznego sugerowania Bing w wersji 7)](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference)
- [Uzyskiwanie sugerowanych wyszukiwanych haseł z interfejsu API automatycznego sugerowania usługi Bing](get-suggestions.md)
