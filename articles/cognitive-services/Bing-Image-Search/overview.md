---
title: Czym jest funkcja wyszukiwania obrazów Bing? | Microsoft Docs
description: Dowiedz się, jak używać interfejsu API wyszukiwania obrazów Bing do przeszukiwania Internetu pod kątem obrazów.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 1446AD8B-A685-4F5F-B4AA-74C8E9A40BE9
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: overview
ms.date: 10/11/2017
ms.author: scottwhi
ms.openlocfilehash: 457707065059b5cb83c9d2b81f5d073cf1c89b84
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "36336523"
---
# <a name="what-is-bing-image-search"></a>Czym jest funkcja wyszukiwania obrazów Bing?

Interfejs API wyszukiwania obrazów Bing udostępnia środowisko podobne do funkcji [Obrazy Bing](https://www.bing.com/images), umożliwiając wysyłanie zapytania wyszukiwania użytkownika do usługi Bing i odbieranie listy odpowiednich obrazów.

Jeśli tworzysz stronę z wynikami wyszukiwania składającą się z samych obrazów w celu znalezienia obrazów odpowiednich dla zapytania wyszukiwania użytkownika, wywołaj ten interfejs API zamiast wywoływać bardziej ogólny [interfejs API wyszukiwania w Internecie](../bing-web-search/search-the-web.md). Jeśli chcesz oprócz obrazów otrzymać także inne typy zawartości, takie jak strony internetowe, wiadomości i wideo, wywołaj interfejs API wyszukiwania w Internecie.

## <a name="suggesting--using-search-terms"></a>Sugerowanie i używanie wyszukiwanych terminów

Jeśli udostępniasz pole wyszukiwania, w którym użytkownik wprowadza wyszukiwany termin, użyj [interfejsu API automatycznego sugerowania Bing](../bing-autosuggest/get-suggested-search-terms.md) w celu ulepszenia jego działania. Interfejs API zwraca sugerowane ciągi zapytań na podstawie częściowych wyszukiwanych terminów w miarę wpisywania ich przez użytkownika.

Gdy użytkownik wprowadzi wyszukiwany termin, zakoduj go w formacie URL przed ustawieniem parametru zapytania [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query). Na przykład jeśli użytkownik wprowadzi frazę *sailing dinghies*, ustaw parametr `q` na wartość `sailing+dinghies` lub `sailing%20dinghies`.

## <a name="getting-images"></a>Uzyskiwanie obrazów

Aby uzyskać z Internetu obrazy powiązane z wyszukiwanym terminem użytkownika, wyślij następujące żądanie GET:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Wszystkie żądania muszą pochodzić z serwera. Nie można wykonywać wywołań z klienta.

Jeśli jest to Twoje pierwsze wywoływanie dowolnego z interfejsów API Bing, nie dołączaj nagłówka identyfikatora klienta. Identyfikator klienta należy uwzględnić tylko wtedy, gdy interfejs API Bing został już wywołany i usługa Bing zwróciła identyfikator klienta dla kombinacji użytkownika i urządzenia.

Aby uzyskać obrazy z określonej domeny, należy użyć operatora [site:](http://msdn.microsoft.com/library/ff795613.aspx) zapytania.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

Odpowiedź zawiera obiekt [Images](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) z listą obrazów, które usługa Bing uznała za odpowiednie dla zapytania. Każdy obiekt [Image](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image) na liście zawiera adres URL obrazu, jego rozmiar, wymiary oraz format kodowania. Obiekt obrazu zawiera także adres URL miniatury obrazu i wymiary tej miniatury.

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

Możesz wyświetlić kolaż ze wszystkich miniatur obrazów albo wyświetlić podzestaw miniatur. Jeśli wyświetlisz podzestaw, daj użytkownikowi opcję do wyświetlenia pozostałych obrazów. Obrazy musisz wyświetlać w kolejności podanej w odpowiedzi.

Możesz również powiększać miniatury, gdy użytkownik zatrzyma nad nimi wskaźnik myszy. Pamiętaj, aby po powiększeniu obrazu podać informacje o jego źródle. Możesz to zrobić na przykład przez wyodrębnienie hosta z parametru [hostPageDisplayUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-hostpagedisplayurl) i wyświetlenie go pod obrazem. Aby uzyskać informacje na temat zmieniania rozmiaru miniatur, zobacz [Zmiana rozmiaru i przycinanie miniatur](./resize-and-crop-thumbnails.md).

<!-- Removing image until we can replace it with a sanatized version.
![Expanded view of thumbnail image](../bing-web-search/media/cognitive-services-bing-web-api/bing-web-image-thumbnail-expansion.PNG)
-->

Jeśli użytkownik kliknie miniaturę, możesz użyć parametru [contentUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-contenturl) do wyświetlenia użytkownikowi obrazu w pełnym rozmiarze. Pamiętaj o podaniu źródła obrazu.

Jeśli wartość `shoppingSourcesCount` lub `recipeSourcesCount` jest większa od zera, dodaj do miniatury odpowiedni znaczek, taki jak wózek sklepowy, w celu wskazania, że dla elementu na obrazie istnieje możliwość zrobienia zakupów lub uzyskania przepisów.

<!-- this is a sanitized version but we're removing all images for now until everything is sanitized.
![Shopping sources badge](./media/cognitive-services-bing-images-api/bing-images-shopping-source.PNG)
-->

Aby uzyskać szczegółowe informacje dotyczące obrazu, takie jak strony internetowe zawierające ten obraz lub osoby, które zostały rozpoznane na obrazie, użyj parametru [imageInsightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-imageinsightstoken). Aby uzyskać więcej informacji, zobacz [Szczegółowe informacje o obrazach](./image-insights.md).

## <a name="filtering-images"></a>Filtrowanie obrazów

 Domyślnie interfejs API wyszukiwania obrazów zwraca wszystkie obrazy odpowiednie dla zapytania. Jeśli jednak będą interesować Cię tylko obrazy z przezroczystym tłem lub obrazy o konkretnym rozmiarze, możesz użyć następujących parametrów zapytania do przefiltrowania obrazów zwracanych przez usługę Bing.  

- [aspect](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#aspect) — filtrowanie obrazów według współczynnika proporcji (na przykład obrazy standardowe lub panoramiczne)
- [color](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#color) — filtrowanie obrazów według przeważającego koloru albo czarno-białych
- [freshness](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#freshness) — filtrowanie obrazów według wieku (na przykład obrazy odnalezione przez usługę Bing w minionym tygodniu)
- [height](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#height), [width](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#width) — filtrowanie obrazów według szerokości i wysokości
- [imageContent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagecontent) — filtrowanie obrazów według zawartości (na przykład obrazy zawierające tylko twarz)
- [imageType](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetype) — filtrowanie obrazów według typu (na przykład cliparty, animowane pliki GIF lub przezroczyste tło)
- [license](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#license) — filtrowanie obrazów według typu licencji skojarzonej z witryną
- [size](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#size) — filtrowanie obrazów według rozmiaru, na przykład małych obrazów o rozmiarach do 200x200 pikseli

Aby uzyskać obrazy z określonej domeny, należy użyć operatora [site:](http://msdn.microsoft.com/library/ff795613.aspx) zapytania.

> [!NOTE]
> W zależności od zapytania, jeśli używasz operatora `site:`, istnieje ryzyko, że odpowiedź będzie zawierać zawartość dla dorosłych niezależnie od ustawienia [safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#safesearch). Operatora `site:` używaj tylko wtedy, gdy znasz zawartość witryny i w swoim scenariuszu uwzględniasz możliwość pojawienia się zawartości dla dorosłych.

Poniższy przykład pokazuje, jak z witryny ContosoSailing.com pobrać małe obrazy, które usługa Bing odnalazła w zeszłym tygodniu.  

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&size=small&freshness=week&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="pivoting-the-query"></a>Stosowanie elementów bazowych zapytania

Jeśli usługa Bing można podzielić oryginalne zapytanie wyszukiwania na segmenty, obiekt [Images](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) zawiera pole `pivotSuggestions`. Jeśli na przykład oryginalnym zapytaniem jest fraza *Microsoft Surface*, usługa Bing może podzielić zapytanie na segmenty *Microsoft* i *Surface*.  

W poniższym przykładzie przedstawiono propozycje elementów bazowych dla zapytania *Microsoft Surface*.  

```json
{
    "_type": "Images",
    "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=microsoft%20surface&FORM=OIIARP",
    "totalEstimatedMatches": 1000,
    "value": [...],
    "queryExpansions": [...],
    "pivotSuggestions": [{
        "pivot": "microsoft",
        "suggestions": [{
            "text": "Contoso Surface",
            "displayText": "Contoso",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=OtterBox+Surface&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=Contoso...",
                    "searchLink": "https:\/\/api.cognitive.microsoft.com\/api...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Contoso+Surface..."
            }
        },
        {
            "text": "Adatum Surface",
            "displayText": "Adatum",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Adatum+Surface&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Adatum+Surface&pid=Ap..."
            }
        },
        ...
        ]
    },
    {
        "pivot": "surface",
        "suggestions": [{
            "text": "Microsoft Surface4",
            "displayText": "Surface4",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface...",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft..."
            }
        },
        {
            "text": "Microsoft Tablet",
            "displayText": "Tablet",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Tablet&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Microsoft+Tablet..."
            }
        },
        ...
    ],
    "nextOffsetAddCount": 0
}
```

Następnie możesz wyświetlić użytkownikowi opcjonalne wyszukiwane terminy w przypadku, gdy interesują użytkownika.

Pole `pivotSuggestions` zawiera listę segmentów (elementów bazowych), na które zostało podzielone oryginalne zapytanie. Dla każdego elementu bazowego odpowiedź zawiera listę obiektów [Query](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query_obj) zawierających sugerowane zapytania. Pole `text` zawiera sugerowane zapytanie, a pole `displayText` zawiera termin, który zastępuje element bazowy w oryginalnym zapytaniu. Na przykład: Release Date of Surface.

Możesz użyć pól `text` i `thumbnail` w celu wyświetlenia bazowych ciągów zapytania użytkownikowi w przypadku, gdy bazowy ciąg zapytania naprawdę jest tym, czego użytkownik szuka. Zmień miniatury i tekst w elementy klikalne za pomocą adresu URL `webSearchUrl` lub adresu URL `searchLink`. Użyj parametru `webSearchUrl`, aby odesłać użytkownika do wyników wyszukiwania w usłudze Bing, albo parametru `searchLink`, jeśli udostępniasz własną stronę z wynikami.

<!-- Need a sanitized version of the image
The following shows an example of the pivot queries.

![Pivot suggestions](./media/cognitive-services-bing-images-api/bing-image-pivotsuggestion.GIF)
-->

## <a name="expanding-the-query"></a>Rozwijanie zapytania

Jeśli usługa Bing może rozwinąć zapytanie w celu zawężenia kryteriów oryginalnego wyszukiwania, obiekt [Images](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) zawiera pole `queryExpansions`. Na przykład jeśli zapytaniem była fraza *Microsoft Surface*, rozwiniętymi zapytaniami mogą być następujące frazy: Microsoft Surface **Pro 3**, Microsoft Surface **RT**, Microsoft Surface **Phone** i Microsoft Surface **Hub**.

W poniższym przykładzie pokazano rozwinięte zapytania dla frazy *Microsoft Surface*.

```json
{
    "_type": "Images",
    "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=microsoft%20surface...",
    "totalEstimatedMatches": 1000,
    "value": [...],
    "queryExpansions":  [{
        "text": "Microsoft Surface Pro 3",
        "displayText": "Pro 3",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+Pro+3...",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=Microsoft...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+Pro+3..."
        }
    },
    {
        "text": "Microsoft Surface RT",
        "displayText": "RT",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+RT...",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+RT..."
        }
    },
    {
        "text": "Microsoft Surface Phone",
        "displayText": "Phone",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+Phone",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+Phone..."
        }
    }],
    "pivotSuggestions": [...],
    "nextOffsetAddCount": 0
}
```

Pole `queryExpansions` zawiera listę obiektów [Query](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query_obj). Pole `text` zawiera rozwinięte zapytanie, a pole `displayText` zawiera termin będący rozwinięciem. Pól `text` i `thumbnail` można użyć do wyświetlenia rozwiniętych ciągów zapytania użytkownikowi w przypadku, gdy rozwinięte ciągi zapytania naprawdę są tym, czego użytkownik szuka. Zmień miniatury i tekst w elementy klikalne za pomocą adresu URL `webSearchUrl` lub adresu URL `searchLink`. Użyj parametru `webSearchUrl`, aby odesłać użytkownika do wyników wyszukiwania w usłudze Bing, albo parametru `searchLink`, jeśli udostępniasz własną stronę z wynikami.

<!-- Removing until we can replace with a sanitized image.
The following shows an example Bing implementation that uses expanded queries. If the user clicks the Microsoft Surface Pro 3 link, they're taken to the Bing search results page, which shows them images of the Pro 3.

![Query expansion suggestions](./media/cognitive-services-bing-images-api/bing-image-queryexpansion.GIF)
-->

## <a name="throttling-requests"></a>Ograniczanie żądań

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Następne kroki

Aby szybko rozpocząć pracę z pierwszym żądaniem, zobacz [szybki start z językiem C#](quickstarts/csharp.md).

Zapoznaj się z dokumentacją [interfejsu API wyszukiwania obrazów Bing w wersji 7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference). Dokumentacja zawiera listę punktów końcowych, nagłówków i parametrów zapytań, które są stosowane w żądaniach wyników wyszukiwania. Zawiera także definicje obiektów odpowiedzi.

Aby zwiększyć wygodę korzystania z pola wyszukiwania, zobacz [interfejs API automatycznego sugerowania Bing](../bing-autosuggest/get-suggested-search-terms.md). Gdy użytkownik wprowadza swój termin zapytania, możesz wywołać ten interfejs API w celu uzyskania powiązanych terminów zapytania, które były używane przez inne osoby.

Nie zapomnij przeczytać [wymagań w zakresie korzystania z usługi Bing i wyświetlania danych z niej](./useanddisplayrequirements.md), aby nie złamać żadnych reguł dotyczących korzystania z wyników wyszukiwania.

Po wywołaniu interfejsu API wyszukiwania obrazów Bing usługa Bing zwraca listę wyników. Lista jest podzestawem całkowitej liczby wyników odpowiednich dla zapytania. Pole `totalEstimatedMatches` odpowiedzi zawiera szacunkową liczbę obrazów, które są dostępne do wyświetlenia. Aby uzyskać szczegółowe informacje dotyczące sposobu przeglądania pozostałych obrazów, zobacz [Stronicowanie obrazów](./paging-images.md).