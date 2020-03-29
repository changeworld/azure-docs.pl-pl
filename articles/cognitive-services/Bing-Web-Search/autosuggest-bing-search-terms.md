---
title: Terminy wyszukiwania automatycznego zasyłania - Interfejs API wyszukiwania w sieci Bing
titleSuffix: Azure Cognitive Services
description: Sparuj interfejs API wyszukiwania w sieci Web Bing z interfejsem API autosuggest bing, aby zapewnić użytkownikom ulepszone środowisko wyszukiwania.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/03/2019
ms.author: aahi
ms.openlocfilehash: 0fb62966c78eb19c1daf9294efba786a267ae200
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "66384859"
---
# <a name="autosuggest-bing-search-terms-in-your-application"></a>Automatyczne zasmucenie terminów wyszukiwania Bing w aplikacji

Jeśli udostępniasz pole wyszukiwania, w którym użytkownik wprowadza wyszukiwany termin, użyj [interfejsu API automatycznego sugerowania Bing](../bing-autosuggest/get-suggested-search-terms.md) w celu ulepszenia jego działania. Interfejs API zwraca sugerowane ciągi zapytań na podstawie częściowych wyszukiwanych terminów w miarę wpisywania ich przez użytkownika.

Po wprowadzeniu przez użytkownika wyszukiwanego terminu musi on być zakodowany adres URL przed ustawieniem parametru zapytania [q.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query) Na przykład jeśli użytkownik wprowadzi frazę *sailing dinghies*, ustaw parametr `q` na wartość `sailing+dinghies` lub `sailing%20dinghies`.

Jeśli termin zapytania zawiera błąd pisowni, odpowiedź wyszukiwania zawiera [obiekt QueryContext.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#querycontext) Obiekt ten zawiera pierwotną pisownię oraz poprawioną pisownię użytą przez usługę Bing podczas wyszukiwania.

```json
"queryContext": {
    "originalQuery": "sialing dingy for sale",
    "alteredQuery": "sailing dinghy for sale",
    "alterationOverrideQuery": "+sialing +dingy for sale"
}
```

Te informacje można użyć, aby poinformować użytkownika, że zmodyfikowano ich ciąg zapytania podczas wyświetlania wyników wyszukiwania.

![Przykład środowiska użytkownika kontekstu kwerendy](./media/cognitive-services-bing-web-api/bing-query-context.PNG)  

## <a name="next-steps"></a>Następne kroki  

* Przejrzyj [przykładowe odpowiedzi interfejsu API wyszukiwania w sieci Web](search-responses.md)usługi Bing .  

## <a name="see-also"></a>Zobacz też  

* [Odwołanie do interfejsu API wyszukiwania w sieci Web usługi Bing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)
