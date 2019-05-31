---
title: Pobieranie obrazów z Internetu — interfejs API wyszukiwania obrazów Bing
titleSuffix: Azure Cognitive Services
description: Aby wyszukać i pobrać odpowiednie obrazy z sieci web, należy użyć interfejsu API wyszukiwania obrazów Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: AB1B9898-C94A-4B59-91A1-8623C94BA3D4
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: f169f969a1acf4cefc8cee27f74a99730491176a
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389412"
---
# <a name="get-images-from-the-web-with-the-bing-image-search-api"></a>Pobieranie obrazów z sieci web za pomocą interfejsu API wyszukiwania obrazów Bing

Korzystając z interfejsu API REST wyszukiwania obrazów Bing, można uzyskać z sieci web, które są związane z wyszukiwanym terminem, wysyłając następujące żądanie GET obrazów:

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

## <a name="get-images-from-a-specific-web-domain"></a>Pobieranie obrazów z domeny sieci web.

Aby uzyskać obrazy z określonej domeny, należy użyć operatora [site:](https://msdn.microsoft.com/library/ff795613.aspx) zapytania.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

> [!NOTE]
> Odpowiedzi na zapytania przy użyciu `site:` operator może zawierać treści dla dorosłych niezależnie od wartości [bezpieczne wyszukiwanie](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#safesearch) ustawienie. Używaj tylko `site:` Jeśli masz świadomość zawartości w domenie.

Poniższy przykład pokazuje, jak z witryny ContosoSailing.com pobrać małe obrazy, które usługa Bing odnalazła w zeszłym tygodniu.  

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&size=small&freshness=week&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="filter-images"></a>Filtruj obrazy

 Domyślnie interfejsu API wyszukiwania obrazów zwraca wszystkie obrazy, które są istotne dla kwerendy. Jeśli chcesz filtrować obrazy, które Wyszukiwarka Bing zwróci (na przykład, aby zwrócić tylko obrazy z przezroczystym tłem lub konkretnego rozmiaru), należy użyć poniższych parametrów zapytania:

* [aspekt](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#aspect)— Filtruj obrazy przez współczynnik proporcji (na przykład obrazy standardowy lub szerokiego ekranu).
* [kolor](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#color)— Filtruj obrazy dominujący kolor lub czarno-biały.
* [świeżości](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#freshness)— Filtruj obrazy od wieku (na przykład obrazy odnalezione przez usługę Bing w ostatnim tygodniu).
* [wysokość](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#height), [szerokość](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#width)— Filtruj obrazy, szerokość i wysokość.
* [imageContent](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagecontent)— Filtruj obrazy według zawartości (na przykład, obrazy, które pokazują tylko osoby twarzy).
* [imageType](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype)— Filtruj obrazy według typu (na przykład clipart, animowanych plików GIF lub przezroczyste tło).
* [Licencja](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#license)— Filtruj obrazy według typu licencji skojarzony z witryną.
* [rozmiar](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#size)— obrazy filtru według rozmiaru, takich jak małe obrazy maksymalnie 200 x 200 pikseli.

Aby uzyskać obrazy z określonej domeny, należy użyć operatora [site:](https://msdn.microsoft.com/library/ff795613.aspx) zapytania.

 > [!NOTE]
 > Odpowiedzi na zapytania przy użyciu `site:` operator może zawierać treści dla dorosłych niezależnie od wartości [bezpieczne wyszukiwanie](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#safesearch) ustawienie. Używaj tylko `site:` Jeśli masz świadomość zawartości w domenie.

Poniższy przykład pokazuje, jak uzyskać małe obrazy z ContosoSailing.com, które Bing wykryte w ostatnim tygodniu.  

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&size=small&freshness=week&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="bing-image-search-response-format"></a>Format odpowiedzi wyszukiwania obrazów Bing

Zawiera komunikat odpowiedzi z usługi Bing [obrazów](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) odpowiedzi, który zawiera listę obrazów, które usług Cognitive Services uznane za istotne dla kwerendy. Każdy [obraz](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) obiektu na liście zawiera następujące informacje dotyczące obrazu: adres URL, jego rozmiar, wymiary, jego format kodowania adresu URL miniaturę obrazu i wymiary miniaturę.

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

Po wywołaniu interfejsu API wyszukiwania obrazów Bing usługa Bing zwraca listę wyników. Lista jest podzestawem całkowitej liczby wyników odpowiednich dla zapytania. Pole `totalEstimatedMatches` odpowiedzi zawiera szacunkową liczbę obrazów, które są dostępne do wyświetlenia. Aby uzyskać szczegółowe informacje na temat do strony za pośrednictwem interfejsu rest obrazów, zobacz [stronicowania obrazów](../paging-images.md).

## <a name="next-steps"></a>Kolejne kroki

Jeśli jeszcze nie nastąpiła interfejsu API wyszukiwania obrazów Bing przed, spróbuj [Szybki Start](../quickstarts/csharp.md). Jeśli szukasz bardziej złożonej, spróbuj samouczka, aby utworzyć [aplikacji jednej strony sieci web](../tutorial-bing-image-search-single-page-app.md).
