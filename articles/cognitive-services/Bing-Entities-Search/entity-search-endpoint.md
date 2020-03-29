---
title: Punkt końcowy interfejsu API wyszukiwania jednostek Bing
titleSuffix: Azure Cognitive Services
description: Interfejs API wyszukiwania jednostek Bing ma jeden punkt końcowy, który zwraca jednostki z sieci Web na podstawie kwerendy. Te wyniki wyszukiwania są zwracane w JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: e7f5af42d0bb0079746cc9e64b621adfebd565d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74072667"
---
# <a name="bing-entity-search-api-endpoint"></a>Punkt końcowy interfejsu API wyszukiwania jednostek Bing


Interfejs API wyszukiwania jednostek Bing ma jeden punkt końcowy, który zwraca jednostki z sieci Web na podstawie kwerendy. Te wyniki wyszukiwania są zwracane w JSON.

## <a name="get-entity-results-from-the-endpoint"></a>Pobierz wyniki encji z punktu końcowego

Aby uzyskać wyniki jednostki przy użyciu `GET` interfejsu API **Bing,** wyślij żądanie do następującego punktu końcowego. Użyj [nagłówków](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#headers) i [parametrów zapytania,](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query-parameters) aby dostosować żądanie wyszukiwania. Żądania wyszukiwania mogą być `?q=` wysyłane przy użyciu parametru.

```cURL
 GET https://api.cognitive.microsoft.com/bing/v7.0/entities
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Czym jest interfejs API wyszukiwania jednostek Bing?](overview.md)

## <a name="see-also"></a>Zobacz też 

Aby uzyskać więcej informacji na temat nagłówków, parametrów, kodów rynku, obiektów odpowiedzi, błędów i innych, zobacz artykuł referencyjny [interfejsu API wyszukiwania jednostek Bing w wersji 7.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference)
