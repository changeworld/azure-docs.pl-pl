---
title: Punkt końcowy interfejsu API wyszukiwania jednostek Bing
titlesuffix: Azure Cognitive Services
description: Dowiedz się więcej o punkt końcowy interfejsu API wyszukiwania jednostek Bing i wysyłać żądania do niego.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: v-gedod
ms.openlocfilehash: cae65c8fb7deb9f68a297de2058a86249b60136b
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/06/2019
ms.locfileid: "55753984"
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
> [Co to jest interfejs API wyszukiwania jednostek Bing?](overview.md)

## <a name="see-also"></a>Zobacz także 

Aby uzyskać więcej informacji na temat nagłówków, parametry, kody na rynku, obiekty odpowiedzi, błędów i innych, zobacz [interfejs API wyszukiwania jednostek Bing w wersji 7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference) artykule dotyczącym struktury.
