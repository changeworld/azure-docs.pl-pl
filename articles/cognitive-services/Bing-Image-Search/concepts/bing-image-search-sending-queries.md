---
title: Dostosowywanie, a także sugerują zapytania wyszukiwania obrazów — interfejs API wyszukiwania obrazów Bing
titleSuffix: Azure Cognitive Services
description: Dowiedz się więcej o dostosowywaniu kwerend wyszukiwania, wysyłanych do interfejsu API wyszukiwania obrazów Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: C2862E98-8BCC-423B-9C4A-AC79A287BE38
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: aahi
ms.openlocfilehash: d833b017004365e9dad7241e360f42ff41a55883
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542742"
---
# <a name="customize-and-suggest-image-search-queries"></a>Dostosowywanie, a także sugerują zapytania wyszukiwania obrazów

Aby dowiedzieć się, jak dostosować zapytania, a także sugerują terminy wyszukiwania służące do wysyłania do interfejsu API wyszukiwania obrazów Bing, należy użyć w tym artykule.

## <a name="suggest-search-terms"></a>Zaproponuj wyszukiwanych terminów

Jeśli aplikacja ma pole wyszukiwania, w którym zostały wprowadzone w wyszukiwane terminy, możesz użyć [interfejs API automatycznego sugerowania Bing](../../bing-autosuggest/get-suggested-search-terms.md) Aby ulepszyć środowisko pracy. Interfejs API można wyświetlić sugerowane wyszukiwane terminy w czasie rzeczywistym. Interfejs API zwraca ciągów sugerowane zapytania, na podstawie częściowe wyszukiwane terminy i Cognitive Services.

## <a name="pivot-the-query"></a>Przestawianie zapytania

Jeśli Bing możesz posegmentować oryginalne zapytanie wyszukiwania, zwrócony [obrazów](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) obiekt zawiera `pivotSuggestions`. Sugestie obrotu może być wyświetlany jako opcjonalny wyszukiwane terminy do użytkownika. Na przykład, jeśli oryginalne zapytanie zostało *Microsoft Surface*, Bing może podzielić zapytanie w *Microsoft* i *powierzchni* i podaj sugerowaną tabele przestawne dla każdego. Tego rodzaju sugestie mogą być wyświetlane jako terminów zapytania opcjonalne dla użytkownika.

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

Pole `pivotSuggestions` zawiera listę segmentów (elementów bazowych), na które zostało podzielone oryginalne zapytanie. Dla każdego elementu bazowego odpowiedź zawiera listę obiektów [Query](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query_obj) zawierających sugerowane zapytania. `text` Pole zawiera sugerowane zapytania. `displayText` Pole zawiera termin, który zastępuje, obrotu w oryginalnego zapytania. Przykładem jest data powierzchni wydania.

Jeśli użytkownik wyszukiwania ciągu zapytania obrotu, użyj `text` i `thumbnail` ciągów zapytania wyświetlane przestawiania pola. Tworzenie miniatur i tekst możesz klikać przy użyciu `webSearchUrl` adres URL lub `searchLink` adresu URL. Użyj `webSearchUrl` można wysłać użytkownika do wyników wyszukiwania Bing. Jeśli podasz stronę wyników, użyj `searchLink`.

<!-- Need a sanitized version of the image
The following shows an example of the pivot queries.

![Pivot suggestions](./media/cognitive-services-bing-images-api/bing-image-pivotsuggestion.GIF)
-->

## <a name="expand-the-query"></a>Rozwiń zapytanie

Jeśli usługa Bing może rozwinąć zapytanie w celu zawężenia kryteriów oryginalnego wyszukiwania, obiekt [Images](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) zawiera pole `queryExpansions`. Na przykład, jeśli kwerenda ma *Microsoft Surface*, rozwinięty zapytania mogą być:
- Microsoft Surface **Pro 3**.
- Microsoft Surface **RT**.
- Microsoft Surface **Phone**.
- Microsoft Surface **Centrum**.

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

Pole `queryExpansions` zawiera listę obiektów [Query](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query_obj). `text` Pole zawiera rozwinięte zapytania. `displayText` Pole zawiera termin rozszerzenia. Jeśli użytkownik wyszukiwania ciągu zapytania rozszerzonej, użyj `text` i `thumbnail` pól do wyświetlania ciągi zapytań rozwinięty. Tworzenie miniatur i tekst możesz klikać przy użyciu `webSearchUrl` adres URL lub `searchLink` adresu URL. Użyj `webSearchUrl` można wysłać użytkownika do wyników wyszukiwania Bing. Jeśli podasz stronę wyników, użyj `searchLink`.

<!-- Removing until we can replace with a sanitized image.
The following shows an example Bing implementation that uses expanded queries. If the user clicks the Microsoft Surface Pro 3 link, they're taken to the Bing search results page, which shows them images of the Pro 3.

![Query expansion suggestions](./media/cognitive-services-bing-images-api/bing-image-queryexpansion.GIF)
-->


## <a name="throttling-requests"></a>Ograniczanie żądań

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Kolejne kroki

Jeśli jeszcze nie nastąpiła interfejsu API wyszukiwania obrazów Bing przed, spróbuj [Szybki Start](../quickstarts/csharp.md). Jeśli szukasz bardziej złożonej, spróbuj samouczka, aby utworzyć [aplikacji jednej strony sieci web](../tutorial-bing-image-search-single-page-app.md).
