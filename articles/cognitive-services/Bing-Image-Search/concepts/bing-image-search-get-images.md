---
title: Pobieranie obrazów z sieci web za pomocą interfejsu API wyszukiwania obrazów Bing | Dokumentacja firmy Microsoft
description: Użyj interfejsu API wyszukiwania obrazów Bing, aby wyszukać i pobrać odpowiednie obrazy z sieci web.
services: cognitive-services
author: aahill
manager: cgronlun
ms.assetid: AB1B9898-C94A-4B59-91A1-8623C94BA3D4
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: conceptual
ms.date: 8/8/2018
ms.author: aahi
ms.openlocfilehash: c379cc2dfbe53f83e4d79500cd947879407c8d55
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2018
ms.locfileid: "40082607"
---
# <a name="getting-images-from-the-web-with-the-bing-image-search-api"></a>Pobieranie obrazów z sieci web za pomocą interfejsu API wyszukiwania obrazów Bing

Za pomocą interfejsu API REST wyszukiwania obrazów Bing, możesz pobrać z sieci web, które są powiązane z termin wyszukiwania użytkownika, wysyłając następujące żądanie GET obrazów:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

> [!IMPORTANT]
> * Wszystkie żądania muszą być wykonane z serwera, a nie od klienta.
> * Jeśli po raz pierwszy wywołanie dowolnego z interfejsami API wyszukiwania Bing, nie dołączaj nagłówek identyfikator klienta. Tylko zawierać identyfikator klienta, jeśli został wcześniej wywołania interfejsu API Bing, który zwrócony identyfikator klienta dla kombinacji urządzenia i użytkownika.
> * Obrazy muszą być wyświetlone w kolejności dostarczonej w odpowiedzi.

## <a name="getting-images-from-a-specific-web-domain"></a>Pobieranie obrazów z domeny sieci web.

Aby uzyskać obrazy z określonej domeny, należy użyć operatora [site:](http://msdn.microsoft.com/library/ff795613.aspx) zapytania.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

> [!NOTE]
> Odpowiedzi na zapytania przy użyciu `site:` operator może zawierać treści dla dorosłych niezależnie od wartości [bezpieczne wyszukiwanie](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#safesearch) ustawienie. Używaj tylko `site:` Jeśli masz świadomość zawartości w domenie.

Poniższy przykład pokazuje, jak z witryny ContosoSailing.com pobrać małe obrazy, które usługa Bing odnalazła w zeszłym tygodniu.  

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&size=small&freshness=week&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="filtering-images"></a>Filtrowanie obrazów

 Domyślnie interfejsu API wyszukiwania obrazów zwróci wszystkie obrazy, które są istotne dla kwerendy. Jeśli chcesz filtrować obrazy, które Wyszukiwarka Bing zwróci (na przykład, aby zwrócić tylko obrazy tła transparant lub konkretnego rozmiaru), można użyć następujących parametrów zapytania:

* [aspect](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#aspect) — filtrowanie obrazów według współczynnika proporcji (na przykład obrazy standardowe lub panoramiczne)
* [color](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#color) — filtrowanie obrazów według przeważającego koloru albo czarno-białych
* [freshness](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#freshness) — filtrowanie obrazów według wieku (na przykład obrazy odnalezione przez usługę Bing w minionym tygodniu)
* [height](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#height), [width](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#width) — filtrowanie obrazów według szerokości i wysokości
* [imageContent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagecontent) — filtrowanie obrazów według zawartości (na przykład obrazy zawierające tylko twarz)
* [imageType](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetype) — filtrowanie obrazów według typu (na przykład cliparty, animowane pliki GIF lub przezroczyste tło)
* [license](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#license) — filtrowanie obrazów według typu licencji skojarzonej z witryną
* [size](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#size) — filtrowanie obrazów według rozmiaru, na przykład małych obrazów o rozmiarach do 200x200 pikseli

Aby uzyskać obrazy z określonej domeny, należy użyć operatora [site:](http://msdn.microsoft.com/library/ff795613.aspx) zapytania.

    > [!NOTE]
    > Responses to queries using the `site:` operator may include adult content regardless of the [safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#safesearch) setting. Only use `site:` if you are aware of the content on the domain.

Poniższy przykład pokazuje, jak z witryny ContosoSailing.com pobrać małe obrazy, które usługa Bing odnalazła w zeszłym tygodniu.  

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&size=small&freshness=week&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="bing-image-search-response-format"></a>Format odpowiedzi wyszukiwania obrazów Bing

Zawiera komunikat odpowiedzi z usługi Bing [obrazów](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) odpowiedzi, który zawiera listę obrazów, które usług Azure cognitive services uznane za istotne dla kwerendy. Każdy [obraz](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image) obiektu na liście zawiera następujące informacje dotyczące obrazu: adres URL, jego rozmiar, wymiary, jego format kodowania adresu URL miniaturę obrazu i wymiary miniaturę.

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

Po wywołaniu interfejsu API wyszukiwania obrazów Bing usługa Bing zwraca listę wyników. Lista jest podzestawem całkowitej liczby wyników odpowiednich dla zapytania. Pole `totalEstimatedMatches` odpowiedzi zawiera szacunkową liczbę obrazów, które są dostępne do wyświetlenia. Aby uzyskać szczegółowe informacje dotyczące sposobu przeglądania pozostałych obrazów, zobacz [Stronicowanie obrazów](../paging-images.md).

## <a name="next-steps"></a>Kolejne kroki

Jeśli jeszcze nie nastąpiła interfejsu API wyszukiwania obrazów Bing przed, spróbuj [Szybki Start](../quickstarts/csharp.md). Jeśli szukasz bardziej złożonej, wypróbuj samouczek tworzenia [aplikacji jednej strony sieci web](../tutorial-bing-image-search-single-page-app.md).
