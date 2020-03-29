---
title: Pobierz obrazy z internetu - Interfejs API wyszukiwania obrazów Bing
titleSuffix: Azure Cognitive Services
description: Użyj interfejsu API wyszukiwania obrazów Bing, aby wyszukać i uzyskać odpowiednie obrazy z sieci Web.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: AB1B9898-C94A-4B59-91A1-8623C94BA3D4
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 309bbca762149f8804742d9ef02d4c3e8dfcdc6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67542765"
---
# <a name="get-images-from-the-web-with-the-bing-image-search-api"></a>Pobierz obrazy z sieci Web za pomocą interfejsu API wyszukiwania obrazów Bing

Korzystając z interfejsu API REST wyszukiwania obrazów Bing, można uzyskać obrazy z sieci Web, które są związane z wyszukiwanym terminem, wysyłając następujące żądanie GET:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Użyj parametru [zapytania q](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query) dla wyszukiwago hasła zakodowanego w adresie URL. Na przykład, jeśli wprowadzisz *pontony żeglarskie,* ustaw `q` `sailing+dinghies` lub `sailing%20dinghies`.

> [!IMPORTANT]
> * Wszystkie żądania muszą być wykonane z serwera, a nie od klienta.
> * Jeśli po raz pierwszy wywołujesz dowolny z interfejsów API wyszukiwania Bing, nie dołączaj nagłówka identyfikatora klienta. Identyfikator klienta należy uwzględnić tylko wtedy, gdy wcześniej był nazywany interfejsem API usługi Bing, który zwrócił identyfikator klienta dla kombinacji użytkownika i urządzenia.

## <a name="get-images-from-a-specific-web-domain"></a>Pobierz obrazy z określonej domeny sieci Web

Aby uzyskać obrazy z określonej domeny, należy użyć operatora [site:](https://msdn.microsoft.com/library/ff795613.aspx) zapytania.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

> [!NOTE]
> Odpowiedzi na zapytania przy `site:` użyciu operatora może zawierać treści dla dorosłych, niezależnie od [safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#safesearch) ustawienie. Używaj `site:` tylko wtedy, gdy znasz zawartość domeny.

## <a name="filter-images"></a>Filtrowanie obrazów

 Domyślnie interfejs API wyszukiwania obrazów zwraca wszystkie obrazy, które są istotne dla kwerendy. Jeśli chcesz filtrować obrazy zwracane przez bing (na przykład, aby zwrócić tylko obrazy o przezroczystym tle lub określonym rozmiarze), użyj następujących parametrów kwerendy:

* [akcesji](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#aspect)— filtruj obrazy według współczynnika proporcji (na przykład obrazy standardowe lub szerokoekranowe).
* [kolor](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#color)— filtruj obrazy według dominującego koloru lub czerni i bieli.
* [świeżość](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#freshness)— filtruj obrazy według wieku (na przykład obrazy odkryte przez Bing w ubiegłym tygodniu).
* [wysokość](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#height), [szerokość](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#width)— filtruj obrazy według szerokości i wysokości.
* [imageContent](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagecontent)— filtruj obrazy według zawartości (na przykład obrazy, które pokazują tylko twarz danej osoby).
* [imageType](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype)— filtruj obrazy według typu (na przykład obiekty clipart, animowane pliki GIF lub przezroczyste tła).
* [licencja](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#license)— filtrowanie obrazów według typu licencji skojarzonej z witryną.
* [rozmiar](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#size)— filtruj obrazy według rozmiaru, na przykład małe obrazy o wymiarach do 200 x 200 pikseli.

Aby uzyskać obrazy z określonej domeny, należy użyć operatora [site:](https://msdn.microsoft.com/library/ff795613.aspx) zapytania.

W poniższym przykładzie pokazano, jak uzyskać małe obrazy z ContosoSailing.com, które Bing odkrył w ubiegłym tygodniu.  

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&size=small&freshness=week&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="bing-image-search-response-format"></a>Format odpowiedzi wyszukiwania obrazów Bing

Komunikat odpowiedzi z usługi Bing zawiera odpowiedź [obrazów,](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) która zawiera listę obrazów, które usługi Cognitive Services uznane za istotne dla kwerendy. Każdy obiekt [Image](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) na liście zawiera następujące informacje o obrazie: adres URL, jego rozmiar, jego wymiary, format kodowania, adres URL miniatury obrazu i wymiary miniatury.

> [!NOTE]
> * Obrazy muszą być wyświetlane w kolejności podanej w odpowiedzi.
> * Ponieważ formaty i parametry adresów URL mogą ulec zmianie bez powiadomienia, użyj wszystkich adresów URL w stanie określonym w stanie niejaka. Nie należy przyjmować zależności od formatu adresu URL lub parametrów, z wyjątkiem przypadków, gdy zaznaczono.

```json
{
    "name": "Rich Passage Sailing Dinghy",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=73118C8B4E3...",
    "thumbnailUrl": "https:\/\/tse1.mm.bing.net\/th?id=OIP.GNarK7m...",
    "datePublished": "2011-10-29T11:26:00",
    "contentUrl": "http:\/\/www.bing.com\/cr?IG=73118C8B4E3D4C3...",
    "hostPageUrl": "http:\/\/www.bing.com\/cr?IG=73118C8B4E3D4C3687...",
    "contentSize": "79239 B",
    "encodingFormat": "jpeg",
    "hostPageDisplayUrl": "en.contoso.org\/wiki\/File:Rich_Passage...",
    "width": 526,
    "height": 688,
    "thumbnail": {
        "width": 229,
        "height": 300
    },
    "imageInsightsToken": "ccid_GNarK7ma*mid_CCF85447ADA6...",
    "insightsSourcesSummary": {
        "shoppingSourcesCount": 0,
        "recipeSourcesCount": 0
    },
    "imageId": "CCF85447ADA6FFF9E96E7DF0B796F7A86E34593",
    "accentColor": "376094"
},
```

Po wywołaniu interfejsu API wyszukiwania obrazów Bing usługa Bing zwraca listę wyników. Lista jest podzestawem całkowitej liczby wyników odpowiednich dla zapytania. Pole `totalEstimatedMatches` odpowiedzi zawiera szacunkową liczbę obrazów, które są dostępne do wyświetlenia. Aby uzyskać szczegółowe informacje na temat sposobu przechodzenia do pozostałych obrazów, zobacz [Obrazy stronicowania](../paging-images.md).

## <a name="next-steps"></a>Następne kroki

Jeśli nie wypróbowano wcześniej interfejsu API wyszukiwania obrazów Bing, spróbuj uruchomić [program Szybki start](../quickstarts/csharp.md). Jeśli szukasz czegoś bardziej złożonego, wypróbuj samouczek, aby utworzyć [jednostronicową aplikację internetową](../tutorial-bing-image-search-single-page-app.md).
