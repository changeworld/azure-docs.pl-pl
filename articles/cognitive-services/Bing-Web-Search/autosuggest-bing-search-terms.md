---
title: Automatyczne sugerowanie wyszukiwane terminy - API wyszukiwania w Internecie Bing
titleSuffix: Azure Cognitive Services
description: Para interfejs API wyszukiwania Bing w sieci Web za pomocą API automatycznego sugerowania Bing, aby umożliwić użytkownikom środowisko rozszerzone wyniki wyszukiwania.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 8/13/2018
ms.author: aahi
ms.openlocfilehash: 540c8c19d1c5ab371588b8a2092b72187382488f
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55874366"
---
# <a name="autosuggest-bing-search-terms-in-your-application"></a>Automatyczne sugerowanie Bing terminy wyszukiwania w aplikacji

Jeśli udostępniasz pole wyszukiwania, w którym użytkownik wprowadza wyszukiwany termin, użyj [interfejsu API automatycznego sugerowania Bing](../bing-autosuggest/get-suggested-search-terms.md) w celu ulepszenia jego działania. Interfejs API zwraca sugerowane ciągi zapytań na podstawie częściowych wyszukiwanych terminów w miarę wpisywania ich przez użytkownika.

Gdy użytkownik wprowadzi wyszukiwany termin, musi być zakodowany przed w adresie URL [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#query) parametr zapytania ma wartość. Na przykład jeśli użytkownik wprowadzi frazę *sailing dinghies*, ustaw parametr `q` na wartość `sailing+dinghies` lub `sailing%20dinghies`.

Jeśli warunek kwerendy zawiera błąd pisowni, odpowiedź wyszukiwania zawiera [QueryContext](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#querycontext) obiektu. Obiekt ten zawiera pierwotną pisownię oraz poprawioną pisownię użytą przez usługę Bing podczas wyszukiwania.

```json
"queryContext": {
    "originalQuery": "sialing dingy for sale",
    "alteredQuery": "sailing dinghy for sale",
    "alterationOverrideQuery": "+sialing +dingy for sale"
}
```

Aby umożliwić użytkownik wie, że ich ciąg zapytania został zmodyfikowany podczas wyświetlania wyników wyszukiwania, można użyć tych informacji.

![Przykład środowiska użytkownika w kontekście zapytania](./media/cognitive-services-bing-web-api/bing-query-context.PNG)  

## <a name="next-steps"></a>Kolejne kroki  

* Przejrzyj przykładowe [odpowiedzi interfejsu API wyszukiwania w sieci Web Bing](search-responses.md).  

## <a name="see-also"></a>Zobacz także  

* [Dokumentacja interfejsu API wyszukiwania w sieci Web Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference)
