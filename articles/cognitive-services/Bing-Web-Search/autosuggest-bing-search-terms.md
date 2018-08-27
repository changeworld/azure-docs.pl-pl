---
title: Automatyczne sugerowanie Bing wyszukiwanych terminów
titleSuffix: Microsoft Cognitive Services
description: Para interfejs API wyszukiwania Bing w sieci Web za pomocą API automatycznego sugerowania Bing, aby zapewnić użytkownikom udoskonalone środowisko wyszukiwania.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 8/13/2018
ms.author: erhopf
ms.openlocfilehash: df8a57b3136abfacce971f4d01ccb2296dfa784c
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/24/2018
ms.locfileid: "42889463"
---
# <a name="autosuggest-bing-search-terms"></a>Automatyczne sugerowanie Bing wyszukiwanych terminów

Jeśli udostępniasz pole wyszukiwania, w którym użytkownik wprowadza wyszukiwany termin, użyj [interfejsu API automatycznego sugerowania Bing](../bing-autosuggest/get-suggested-search-terms.md) w celu ulepszenia jego działania. Interfejs API zwraca sugerowane ciągi zapytań na podstawie częściowych wyszukiwanych terminów w miarę wpisywania ich przez użytkownika.

Gdy użytkownik wprowadzi wyszukiwany termin, musi być zakodowany przed w adresie URL [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#query) parametr zapytania ma wartość. Na przykład jeśli użytkownik wprowadzi frazę *sailing dinghies*, ustaw parametr `q` na wartość `sailing+dinghies` lub `sailing%20dinghies`.

Jeśli warunek kwerendy zawiera błąd pisowni, odpowiedź wyszukiwania zawiera [QueryContext](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#querycontext) obiektu. Obiekt zawiera oryginalną pisownię i poprawiony pisowni, który Bing używany do wyszukiwania.

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
