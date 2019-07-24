---
title: Punkt końcowy interfejs API wyszukiwania jednostek Bing
titleSuffix: Azure Cognitive Services
description: Dowiedz się więcej o punkcie końcowym interfejs API wyszukiwania jednostek Bing i Wyślij do niego żądania.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 9d08091d0ea6869d13e294e60454f85a84f672ad
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2019
ms.locfileid: "68424057"
---
# <a name="bing-entity-search-api-endpoint"></a>interfejs API wyszukiwania jednostek Bing punkt końcowy


Interfejs API wyszukiwania jednostek Bing ma jeden punkt końcowy, który zwraca jednostki z sieci Web na podstawie zapytania. Te wyniki wyszukiwania są zwracane w formacie JSON.

## <a name="get-entity-results-from-the-endpoint"></a>Pobierz wyniki jednostki z punktu końcowego

Aby uzyskać wyniki jednostki przy użyciu **interfejsu API Bing**, Wyślij `GET` żądanie do poniższego punktu końcowego. Użyj [nagłówków](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#headers) i [parametrów zapytania](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query-parameters) , aby dostosować żądanie wyszukiwania. Żądania wyszukiwania można wysyłać przy użyciu `?q=` parametru.

```cURL
 GET https://api.cognitive.microsoft.com/bing/v7.0/entities
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Czym jest interfejs API wyszukiwania jednostek Bing?](overview.md)

## <a name="see-also"></a>Zobacz także 

Aby uzyskać więcej informacji na temat nagłówków, parametrów, kodów rynkowych, obiektów odpowiedzi, błędów i innych, zobacz artykuł dotyczący [interfejs API wyszukiwania jednostek Bing wersji 7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference) .
