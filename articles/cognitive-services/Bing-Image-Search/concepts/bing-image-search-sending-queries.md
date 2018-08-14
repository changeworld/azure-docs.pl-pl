---
title: Wysyłanie zapytań do interfejsu API wyszukiwania obrazów Bing | Dokumentacja firmy Microsoft
description: Więcej informacji na temat wysyłania i dostosowywanie zapytania wyszukiwania wysłane do interfejsu API wyszukiwania obrazów Bing.
services: cognitive-services
author: aahill
manager: cgronlun
ms.assetid: C2862E98-8BCC-423B-9C4A-AC79A287BE38
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: conceptual
ms.date: 8/8/2018
ms.author: aahi
ms.openlocfilehash: d74f59ffcf095e639686a3ada3b09dac988fc544
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2018
ms.locfileid: "40082608"
---
# <a name="sending-queries-to-the-bing-image-search-api"></a>Wysyłanie zapytań do interfejsu API wyszukiwania obrazów Bing

Interfejs API wyszukiwania obrazów Bing udostępnia środowisko podobne do Bing.com/Images, umożliwiając Wysyłanie zapytania wyszukiwania użytkowników do usługi Bing i uzyskanie listy odpowiednie obrazy.

## <a name="using-and-suggesting-search-terms"></a>Przy użyciu i sugerowanie wyszukiwanych terminów

Po wprowadzeniu terminu wyszukiwania termin przed ustawieniem kodowanie adresu URL [ **q** ](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query) parametr zapytania. Na przykład, jeśli użytkownik wprowadzi *dinghies prowadzenia*ustaw `q` do `sailing+dinghies` lub `sailing%20dinghies`.

Jeśli aplikacja ma pole wyszukiwania, w którym zostały wprowadzone w wyszukiwane terminy, możesz użyć [interfejs API automatycznego sugerowania Bing](../../bing-autosuggest/get-suggested-search-terms.md) Aby ulepszyć środowisko pracy, wyświetlając sugerowane wyszukiwane terminy w czasie rzeczywistym. Interfejs API zwraca sugerowane zapytania ciągów na podstawie częściowe wyszukiwane terminy i usług Azure cognitive services.

## <a name="pivoting-the-query"></a>Stosowanie elementów bazowych zapytania

Jeśli Bing możesz posegmentować oryginalne zapytanie wyszukiwania, zwrócony [obrazów](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) obiektu będzie zawierać `pivotSuggestions` mogą być wyświetlane jako opcjonalny wyszukiwane terminy do użytkownika. Na przykład, jeśli oryginalne zapytanie zostało *Microsoft Surface*, Bing może podzielić zapytanie w *Microsoft* i *powierzchni* i podaj sugerowaną tabele przestawne dla każdego. Te mogą być wyświetlane jako terminów zapytania opcjonalne dla użytkownika.

W poniższym przykładzie pokazano pivot propozycje *Microsoft Surface*:  

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

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Kolejne kroki

Jeśli jeszcze nie nastąpiła interfejsu API wyszukiwania obrazów Bing przed, spróbuj [Szybki Start](../quickstarts/csharp.md). Jeśli szukasz bardziej złożonej, wypróbuj samouczek tworzenia [aplikacji jednej strony sieci web](../tutorial-bing-image-search-single-page-app.md).
