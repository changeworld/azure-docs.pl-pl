---
title: Automatycznego sugerowania punktu końcowego | Dokumentacja firmy Microsoft
description: Podsumowanie API automatycznego sugerowania punktu końcowego.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-autosuggest
ms.topic: article
ms.date: 12/05/2017
ms.author: v-gedod
ms.openlocfilehash: 5aaddd09006cb6f1812bb6ae213a2f5e6720fb1b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348945"
---
# <a name="autosuggest-endpoint"></a>Automatycznego sugerowania punktu końcowego

**API automatycznego sugerowania** zawiera jeden punkt końcowy, który zwraca listę sugerowane zapytania z częściowa wyszukiwanego terminu.

## <a name="endpoint"></a>Endpoint

Aby uzyskać sugerowane zapytań przy użyciu interfejsu API Bing, Wyślij `GET` żądanie następujący punkt końcowy. Umożliwia dalsze Definiowanie specyfikacji w nagłówki i parametry adresu URL.

**Punkt końcowy:** zwraca wyszukiwania sugestie jako wyniki JSON, które są istotne dane wejściowe użytkownika zdefiniowane przez `?q=""`.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/Suggestions 
```

Aby uzyskać więcej informacji dotyczących nagłówków, parametry, kody rynku, obiekty odpowiedzi, błędów itd., zobacz [API automatycznego sugerowania usługi Bing w wersji 7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference) odwołania.

## <a name="response-json"></a>Odpowiedź JSON

Odpowiedzi na żądanie Autosugerowanie dołącza wyniki, jako obiektów JSON. Przykłady wyników analizy można znaleźć [samouczek](tutorials/autosuggest.md) i [kod źródłowy](tutorials/autosuggest-source.md).

## <a name="next-steps"></a>Kolejne kroki

**Bing** interfejsów API obsługi akcji wyszukiwania, które zwracają wyniki zgodnie z ich typu. Wszystkie punkty końcowe wyszukiwania zwracają wyniki w postaci obiektów JSON odpowiedzi.
Wszystkie punkty końcowe obsługi zapytań, które zwracają określonego języka i/lub lokalizacji geograficznej, szerokość i wyszukiwania usługi radius.

Aby uzyskać pełne informacje na temat parametrów obsługiwanych przez każdy punkt końcowy zobacz strony podręcznika dla każdego typu.
Przykłady podstawowego żądania przy użyciu interfejsu API automatycznego sugerowania można znaleźć [automatycznego sugerowania poradniki Szybki Start](https://docs.microsoft.com/azure/cognitive-services/Bing-Autosuggest).