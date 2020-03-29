---
title: Wysyłanie i używanie zapytań i odpowiedzi interfejsu API — wyszukiwanie lokalne firmy Bing
titleSuffix: Azure Cognitive Services
description: Ten artykuł służy do wysyłania zapytań i używania ich za pomocą interfejsu API wyszukiwania lokalnego firmy Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: rosh
ms.openlocfilehash: 25bcdb89002fec4f9b67b091996d7bf80bcf21c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74326720"
---
# <a name="sending-and-using-bing-local-business-search-api-queries-and-responses"></a>Wysyłanie i używanie zapytań i odpowiedzi interfejsu API wyszukiwania lokalnego firmy Bing

Wyniki lokalne można uzyskać z interfejsu API wyszukiwania lokalnego firmy Bing, `Ocp-Apim-Subscription-Key` wysyłając zapytanie wyszukiwania do punktu końcowego i zawierające nagłówek, który jest wymagany. Wraz z [dostępnymi nagłówkami](local-search-reference.md#headers) i [parametrami](local-search-reference.md#query-parameters)wyszukiwania można dostosować, określając [granice geograficzne](specify-geographic-search.md) przeszukiwanego obszaru i [zwracane kategorie](local-search-query-response.md) miejsc.

## <a name="creating-a-request"></a>Tworzenie żądania

Aby wysłać żądanie do interfejsu API wyszukiwania lokalnego firmy `q=` Bing, dołącz wyszukiwany termin do `Ocp-Apim-Subscription-Key` parametru przed dodaniem go do punktu końcowego interfejsu API i z nagłówkiem. Przykład:

`https://api.cognitive.microsoft.com/bing/localbusinesses/v7.0/search?q=restaurant+in+Bellevue`

Pełna składnia adresu URL żądania jest wyświetlana poniżej. Zobacz szybki start interfejsu [API](quickstarts/local-quickstart.md)wyszukiwania lokalnego firmy Bing i informacje o zawartości nagłówków i [parametrów,](local-search-reference.md#headers) aby uzyskać więcej informacji na temat wysyłania żądań. [parameters](local-search-reference.md#query-parameters) 

Aby uzyskać informacje na temat kategorii wyszukiwania lokalnego, zobacz [Kategorie wyszukiwania interfejsu API wyszukiwania lokalnego firmy Bing](local-categories.md).

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search[?q][&localCategories][&cc][&mkt][&safesearch][&setlang][&count][&first][&localCircularView][&localMapView]
```

## <a name="using-responses"></a>Korzystanie z odpowiedzi

Odpowiedzi JSON z interfejsu API wyszukiwania lokalnego firmy Bing zawierają `SearchResponse` obiekt. Interfejs API zwróci odpowiednie wyniki `places` wyszukiwania w tym polu. Jeśli nie zostaną znalezione `places` żadne wyniki, pole nie zostanie uwzględnione w odpowiedzi.

[!INCLUDE [cognitive-services-bing-url-note](../../../includes/cognitive-services-bing-url-note.md)]

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

### <a name="search-result-attributes"></a>Atrybuty wyników wyszukiwania

Wyniki JSON zwracane przez interfejs API obejmują następujące atrybuty:

* _type
* adres
* entityPresentationInfo
* Geo
* id
* name
* routeablePoint
* telefon
* url

Aby uzyskać ogólne informacje na temat nagłówków, parametrów, kodów rynku, obiektów odpowiedzi, błędów itp., zobacz odwołanie do [interfejsu API wyszukiwania lokalnego Bing w wersji 7.](local-search-reference.md)

> [!NOTE]
> Użytkownik lub osoba trzecia w jego imieniu nie może wykorzystywać, przechowywać, przechowywać, buforować, udostępniać ani rozpowszechniać żadnych danych z lokalnego interfejsu API wyszukiwania w celu testowania, opracowywania, szkolenia, rozpowszechniania lub udostępniania jakichkolwiek usług lub funkcji innych firm. 


## <a name="example-json-response"></a>Przykładowa odpowiedź JSON

Następująca odpowiedź JSON zawiera wyniki `?q=restaurant+in+Bellevue`wyszukiwania określone przez kwerendę .

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


## <a name="next-steps"></a>Następne kroki
- [Szybki start wyszukiwania lokalnych firm](quickstarts/local-quickstart.md)
- [Przewodnik Szybki start java wyszukiwania lokalnego firmy](quickstarts/local-search-java-quickstart.md)
- [Szybki start lokalnego węzła wyszukiwania firm](quickstarts/local-search-node-quickstart.md)
- [Szybki start wyszukiwania dla firm lokalnych](quickstarts/local-search-python-quickstart.md)
