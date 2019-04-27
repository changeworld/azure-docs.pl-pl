---
title: Punkt końcowy interfejsu API wyszukiwania jednostek Bing
titlesuffix: Azure Cognitive Services
description: Dowiedz się więcej o punkt końcowy interfejsu API wyszukiwania jednostek Bing i wysyłać żądania do niego.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: v-gedod
ms.openlocfilehash: 3c2aa4b22c8e679f73692978d9e1f8009f11a46b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60598519"
---
# <a name="bing-entity-search-api-endpoint"></a>Punkt końcowy interfejsu API wyszukiwania jednostek Bing


Interfejs API wyszukiwania jednostek Bing ma jeden punkt końcowy, który zwraca jednostki z sieci Web na podstawie zapytania. Te wyniki wyszukiwania są zwracane w formacie JSON.

## <a name="get-entity-results-from-the-endpoint"></a>Uzyskaj wyniki obiektu z punktu końcowego

Można pobrać jednostki wyników za pomocą **interfejsu API Bing**, Wyślij `GET` żądanie następujący punkt końcowy. Użyj [nagłówki](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#headers) i [parametry zapytania](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#query-parameters) dostosować żądanie wyszukiwania. Żądania wyszukiwania mogą być wysyłane przy użyciu `?q=` parametru.

```cURL
 GET https://api.cognitive.microsoft.com/bing/v7.0/entities
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Czym jest interfejs API wyszukiwania jednostek Bing?](overview.md)

## <a name="see-also"></a>Zobacz także 

Aby uzyskać więcej informacji na temat nagłówków, parametry, kody na rynku, obiekty odpowiedzi, błędów i innych, zobacz [interfejs API wyszukiwania jednostek Bing w wersji 7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference) artykule dotyczącym struktury.
