---
title: Wysyłanie i przy użyciu lokalnych firm interfejsu API wyszukiwania Bing, zapytań i odpowiedzi | Dokumentacja firmy Microsoft
titleSuffix: Azure Cognitive Services
description: Aby dowiedzieć się, jak wysyłać i zapytania wyszukiwania za pomocą lokalnych firm interfejsu API wyszukiwania Bing, należy użyć w tym artykule.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh; v-gedod
ms.openlocfilehash: 79219de775be96f35bd11ac85640efcc4a04a93b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60582454"
---
# <a name="sending-and-using-bing-local-business-search-api-queries-and-responses"></a>Wysyłanie i przy użyciu lokalnych firm interfejsu API wyszukiwania Bing, zapytań i odpowiedzi

Wyniki lokalnego można uzyskać z lokalnych firm interfejsu API wyszukiwania Bing, wysyłając zapytanie wyszukiwania do punktu końcowego i tym `Ocp-Apim-Subscription-Key` nagłówka, który jest wymagany. Wraz z dostępnych [nagłówki](local-search-reference.md#headers) i [parametry](local-search-reference.md#query-parameters), wyszukiwania, które można dostosować, określając [granicach geograficznych](specify-geographic-search.md) dla obszarów, które mają być wyszukiwane i [kategorie](local-search-query-response.md) miejsc zwracane.

## <a name="creating-a-request"></a>Tworzenie żądania

Aby wysłać żądanie do lokalnych firm interfejsu API wyszukiwania Bing, należy dołączyć termin wyszukiwania, aby `q=` parametru przed dodaniem go do punktu końcowego interfejsu API i łącznie `Ocp-Apim-Subscription-Key` nagłówka. Na przykład:

`https://api.cognitive.microsoft.com/bing/localbusinesses/v7.0/search?q=restaurant+in+Bellevue`

Poniżej przedstawiono pełne żądanie składni adresu URL. Zobacz lokalnych firm interfejsu API wyszukiwania Bing [przewodników Szybki Start](quickstarts/local-quickstart.md)i odwoływać zawartość [nagłówki](local-search-reference.md#headers) i [parametry](local-search-reference.md#query-parameters) więcej informacji na temat wysyłania żądań. 

Aby uzyskać informacji na temat kategorii lokalnego wyszukiwania, zobacz [wyszukać kategorie lokalnych firm interfejsu API wyszukiwania Bing](local-categories.md).

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search[?q][&localCategories][&cc][&mkt][&safesearch][&setlang][&count][&first][&localCircularView][&localMapView]
```

## <a name="using-responses"></a>Przy użyciu odpowiedzi

Odpowiedzi JSON z lokalnych firm interfejsu API wyszukiwania Bing zawiera `SearchResponse` obiektu. Interfejs API zwróci odpowiednie wyniki wyszukiwania w `places` pola. Jeśli żadne wyniki nie zostaną znalezione, `places` pola nie zostaną uwzględnione w odpowiedzi.

```
{
   "_type": "SearchResponse",
   "queryContext": {
      "originalQuery": "restaurant in Bellevue"
   },
   "places": {
      "totalEstimatedMatches": 10,
. . . 
```

### <a name="search-result-attributes"></a>Atrybuty wynik wyszukiwania

Zwrócone przez interfejs API wyniki JSON zawierają następujące atrybuty:

* _type
* adres
* entityPresentationInfo
* Geograficzna
* id
* name
* routeablePoint
* Telefon
* url

Aby uzyskać ogólne informacje na temat nagłówków, parametrów, kody na rynku, obiekty odpowiedzi, błędów itd., zobacz [lokalnego API wyszukiwania Bing w wersji 7](local-search-reference.md) odwołania.

> [!NOTE]
> Użytkownik lub innych firm w Twoim imieniu może nie używać, zachować, przechowywania, pamięci podręcznej, udostępnić, dystrybucji żadnych danych z lokalnego interfejsu API wyszukiwania na potrzeby testowania, tworzenia, szkolenia, rozpowszechniania lub udostępniania dowolnej usługi firmy Microsoft lub funkcji. 


## <a name="example-json-response"></a>Przykładowa odpowiedź JSON

Następującą odpowiedź JSON zawiera wyniki wyszukiwania z określonymi przez zapytanie `?q=restaurant+in+Bellevue`.

```json
Vary: Accept-Encoding
BingAPIs-TraceId: 5376FFEB65294E24BB9F91AD70545826
BingAPIs-SessionId: 06ED7CEC80F746AA892EDAAC97CB0CB4
X-MSEdge-ClientID: 112C391E72C0624204153594738C63DE
X-MSAPI-UserState: aeab
BingAPIs-Market: en-US
X-Search-ResponseInfo: InternalResponseTime=659,MSDatacenter=CO4
X-MSEdge-Ref: Ref A: 5376FFEB65294E24BB9F91AD70545826 Ref B: BY3EDGE0306 Ref C: 2018-10-16T16:26:15Z
apim-request-id: fe54f585-7c54-4bf5-8b92-b9bede2b710a
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
x-content-type-options: nosniff
Cache-Control: max-age=0, private
Date: Tue, 16 Oct 2018 16:26:15 GMT
P3P: CP="NON UNI COM NAV STA LOC CURa DEVa PSAa PSDa OUR IND"
Content-Length: 978
Content-Type: application/json; charset=utf-8
Expires: Tue, 16 Oct 2018 16:25:15 GMT

{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "restaurant Bellevue"
  },
  "places": {
    "totalEstimatedMatches": 50,
    "value": [{
      "_type": "LocalBusiness",
      "id": "https:\/\/cognitivegblppe.azure-api.net\/api\/v7\/#Places.0",
      "name": "Facing East Taiwanese Restaurant",
      "url": "http:\/\/litadesign.wix.com\/facingeastrestaurant",
      "entityPresentationInfo": {
        "entityScenario": "ListItem",
        "entityTypeHints": ["Place", "LocalBusiness", "Restaurant"]
      },
      "geo": {
        "latitude": 47.6199188232422,
        "longitude": -122.202796936035
      },
      "routablePoint": {
        "latitude": 47.6199188232422,
        "longitude": -122.201713562012
      },
      "address": {
        "streetAddress": "1075 Bellevue Way NE Ste B2",
        "addressLocality": "Bellevue",
        "addressRegion": "WA",
        "postalCode": "98004",
        "addressCountry": "US",
        "neighborhood": "Bellevue",
        "text": "1075 Bellevue Way NE Ste B2, Bellevue, WA 98004"
      },
      "telephone": "(425) 688-2986"
    }],
    "searchAction": {
      "location": [{
        "name": "Bellevue, Washington"
      }],
      "query": "restaurant"
    }
  }
}
 
```

## <a name="throttling-requests"></a>Ograniczanie żądań

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]


## <a name="next-steps"></a>Kolejne kroki
- [Lokalne wyszukiwanie firm Szybki Start](quickstarts/local-quickstart.md)
- [Lokalnych firm wyszukiwania Java Szybki Start](quickstarts/local-search-java-quickstart.md)
- [Lokalny węzeł wyszukiwania firm Szybki Start](quickstarts/local-search-node-quickstart.md)
- [Lokalnych firm wyszukiwania Python Szybki Start](quickstarts/local-search-python-quickstart.md)
