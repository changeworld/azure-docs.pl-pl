---
title: Dostosowywanie i sugerowanie zapytań wyszukiwania obrazów — interfejs API wyszukiwania obrazów Bing
titleSuffix: Azure Cognitive Services
description: Dowiedz się więcej o dostosowywaniu zapytań wysyłanych do interfejsu API wyszukiwania obrazów Bing.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67542742"
---
# <a name="customize-and-suggest-image-search-queries"></a>Dostosowywanie i sugerowanie zapytań wyszukiwania obrazów

Ten artykuł służy do dostosowywania zapytań i sugerowania wyszukiwanych haseł do wysyłania do interfejsu API wyszukiwania obrazów Bing.

## <a name="suggest-search-terms"></a>Zaproponuj wyszukiwane hasła

Jeśli aplikacja ma pole wyszukiwania, w którym są wprowadzane wyszukiwane terminy, możesz użyć [interfejsu API autosuggest Bing,](../../bing-autosuggest/get-suggested-search-terms.md) aby poprawić środowisko. Interfejs API może wyświetlać sugerowane wyszukiwane hasła w czasie rzeczywistym. Interfejs API zwraca sugerowane ciągi zapytań na podstawie częściowych wyszukiwanych haseł i usług Cognitive Services.

## <a name="pivot-the-query"></a>Przestawianie kwerendy

Jeśli bing może segmentować oryginalną kwerendę `pivotSuggestions`wyszukiwania, zwracany obiekt [Obrazy](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) zawiera . Sugestie przestawne mogą być wyświetlane użytkownikowi jako opcjonalne terminy wyszukiwania. Na przykład, jeśli oryginalną kwerendą był *Microsoft Surface,* Bing może podzielić kwerendę na *firmy Microsoft* i *Surface* i dostarczyć sugerowane przestawne dla każdego z nich. Te sugestie mogą być wyświetlane jako opcjonalne terminy kwerendy dla użytkownika.

W poniższym przykładzie przedstawiono sugestie przestawne dla *tabletu Microsoft Surface:*  

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

Pole `pivotSuggestions` zawiera listę segmentów (elementów bazowych), na które zostało podzielone oryginalne zapytanie. Dla każdego elementu bazowego odpowiedź zawiera listę obiektów [Query](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query_obj) zawierających sugerowane zapytania. Pole `text` zawiera sugerowaną kwerendę. To `displayText` pole zawiera termin, który zastępuje przestawianie w oryginalnej kwerendzie. Przykładem jest data wydania tabletu Surface.

Jeśli ciąg zapytania przestawnego jest tym, `text` czego `thumbnail` użytkownik szuka, użyj pól i pól, aby wyświetlić ciągi zapytania przestawnego. Kliknij miniaturę i tekst, `webSearchUrl` używając adresu `searchLink` URL lub adresu URL. Służy `webSearchUrl` do wysyłania użytkownika do wyników wyszukiwania Bing. Jeśli podasz własną stronę `searchLink`wyników, użyj .

<!-- Need a sanitized version of the image
The following shows an example of the pivot queries.

![Pivot suggestions](./media/cognitive-services-bing-images-api/bing-image-pivotsuggestion.GIF)
-->

## <a name="expand-the-query"></a>Rozwiń kwerendę

Jeśli usługa Bing może rozwinąć zapytanie w celu zawężenia kryteriów oryginalnego wyszukiwania, obiekt [Images](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) zawiera pole `queryExpansions`. Na przykład, jeśli kwerenda była *Microsoft Surface*, rozwinięte kwerendy mogą być:
- Microsoft Surface **Pro 3**.
- Microsoft Surface **RT**.
- Microsoft Surface **Phone**.
- Microsoft Surface **Hub**.

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

Pole `queryExpansions` zawiera listę obiektów [Query](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query_obj). Pole `text` zawiera kwerendę rozwiniętą. Pole `displayText` zawiera termin rozszerzenia. Jeśli ciąg zapytania rozwinięty jest to, czego `text` użytkownik `thumbnail` szuka, użyj pól i do wyświetlania rozszerzonych ciągów zapytania. Kliknij miniaturę i tekst, `webSearchUrl` używając adresu `searchLink` URL lub adresu URL. Służy `webSearchUrl` do wysyłania użytkownika do wyników wyszukiwania Bing. jeśli podasz własną stronę `searchLink`wyników, użyj .

<!-- Removing until we can replace with a sanitized image.
The following shows an example Bing implementation that uses expanded queries. If the user clicks the Microsoft Surface Pro 3 link, they're taken to the Bing search results page, which shows them images of the Pro 3.

![Query expansion suggestions](./media/cognitive-services-bing-images-api/bing-image-queryexpansion.GIF)
-->


## <a name="throttling-requests"></a>Ograniczanie żądań

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Następne kroki

Jeśli nie wypróbowano wcześniej interfejsu API wyszukiwania obrazów Bing, spróbuj uruchomić [program Szybki start](../quickstarts/csharp.md). Jeśli szukasz czegoś bardziej złożonego, wypróbuj samouczek, aby utworzyć [jednostronicową aplikację internetową](../tutorial-bing-image-search-single-page-app.md).
