---
title: Niestandardowe wyszukiwania punktu końcowego | Dokumentacja firmy Microsoft
description: Podsumowanie punkt końcowy interfejsu API wyszukiwania niestandardowego.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 12/05/2017
ms.author: v-gedod
ms.openlocfilehash: 8d9851f3687a783f52a80a8dffcf2580d4710551
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347868"
---
# <a name="custom-search"></a>Wyszukiwanie niestandardowe
Wyszukiwanie niestandardowe Bing umożliwia utworzenie środowiska wyszukiwania dopasowane do tematów, które są dla Ciebie ważne. Użytkownicy zobaczą, dostosowane do zawartości one interesujących zamiast wyniki wyszukiwania do strony za pośrednictwem wyników wyszukiwania, które mają nie znaczenia zawartość.

## <a name="custom-search-endpoint"></a>Punkt końcowy wyszukiwania niestandardowego
Aby uzyskać wyniki za pomocą interfejsu API wyszukiwania usługi Bing niestandardowe, Wyślij `GET` żądanie następujący punkt końcowy. Umożliwia dalsze Definiowanie specyfikacji w nagłówki i parametry adresu URL.

Punkt końcowy: Zwraca wyszukiwania sugestie jako wyniki JSON, które są istotne dane wejściowe użytkownika zdefiniowane przez `?q=""`.
```  
 GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search  
```

Przykłady, których opisano sposób konfigurowania źródeł wyszukiwanie niestandardowe można znaleźć [samouczek](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/tutorials/custom-search-web-page). Aby uzyskać więcej informacji dotyczących nagłówków, parametry, kody rynku, obiekty odpowiedzi, błędów itd., zobacz [interfejsu API Bing niestandardowe wyszukiwania w wersji 7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference) odwołania.

## <a name="custom-search-response-json"></a>Wyszukiwanie niestandardowe odpowiedzi JSON
Żądania wyszukiwania niestandardowego zwraca wyniki w postaci obiektów JSON, zobacz [obiektów odpowiedzi](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#response-objects). 

## <a name="custom-autosuggest"></a>Niestandardowe automatycznego sugerowania
API automatycznego sugerowania niestandardowe umożliwia wysyłania częściowej wyszukiwany zapytania do usługi Bing i wrócić listę sugerowane zapytania, które można skonfigurować. Z automatycznego sugerowania niestandardowe należy dodać sugestie zwracane przez interfejs API i opcjonalnie Określ, czy zawierają sugestie generowanych przez usługi Bing.

## <a name="custom-autosuggest-endpoint"></a>Niestandardowe automatycznego sugerowania punktu końcowego
Aby poprosić o zapytań niestandardowych, Wyślij żądanie GET:

```
https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions
```  

Uzyskać informacji na temat definiowania sugestie niestandardowych, zobacz [zdefiniować sugestie dotyczące wyszukiwania niestandardowego](define-custom-suggestions.md).

## <a name="custom-image-search"></a>Niestandardowy obraz wyszukiwania
Interfejs API niestandardowy obraz wyszukiwania umożliwia wysyłanie zapytania wyszukiwania do usługi Bing i wrócić listę odpowiednich obrazów z wystąpieniem wyszukiwanie niestandardowe.

## <a name="custom-image-search-endpoint"></a>Punkt końcowy wyszukiwania niestandardowego obrazu
Aby zażądać obrazów z wystąpieniem wyszukiwanie niestandardowe, Wyślij żądanie GET pod następujący adres URL:

```
https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/images/search
```

Informacje o konfigurowaniu wystąpienia wyszukiwania niestandardowego, zobacz [Konfigurowanie środowiska wyszukiwania niestandardowego](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/define-your-custom-view).

## <a name="next-steps"></a>Kolejne kroki
**Bing** interfejsów API obsługi akcji wyszukiwania, które zwracają wyniki zgodnie z ich typu. Wszystkie punkty końcowe wyszukiwania zwracają wyniki w postaci obiektów JSON odpowiedzi.  Wszystkie punkty końcowe obsługi zapytań, które zwracają określonego języka i/lub lokalizacji geograficznej, szerokość i wyszukiwania usługi radius.

Aby uzyskać pełne informacje na temat parametrów obsługiwanych przez każdy punkt końcowy zobacz strony podręcznika dla każdego typu.
Przykłady podstawowego żądania przy użyciu interfejsu API wyszukiwanie niestandardowych można znaleźć [szybkie wyszukiwanie niestandardowe-rozpoczyna się](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/)