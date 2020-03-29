---
title: Używanie rankingu do wyświetlania odpowiedzi - Wyszukiwanie jednostek Bing
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak używać rankingu do wyświetlania odpowiedzi zwracanych przez interfejs API wyszukiwania jednostek Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 110cef117683b20170649a231226c8193496edf3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68423918"
---
# <a name="using-ranking-to-display-entity-search-results"></a>Używanie klasyfikacji do wyświetlania wyników wyszukiwania encji  

Każda odpowiedź wyszukiwania jednostek zawiera odpowiedź [RankingResponse,](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse) która określa sposób wyświetlania wyników wyszukiwania zwracanych przez interfejs API wyszukiwania jednostek Bing. Grupy odpowiedzi rankingu są uwzględniane w zawartości bieguna, linii głównej i paska bocznego. Wynik bieguna jest najważniejszym lub widocznym wynikiem i powinien być wyświetlany jako pierwszy. Jeśli pozostałe wyniki nie są wyświetlane w tradycyjnym formacie linii głównej i paska bocznego, należy zapewnić zawartość linii głównej większą widoczność niż zawartość paska bocznego. 
  
W ramach każdej grupy [Items](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankinggroup-items) tablicy identyfikuje kolejność, w których zawartość musi pojawić się w. Każdy element zawiera dwa sposoby identyfikowania wyniku w odpowiedzi.  
 

|Pole | Opis  |
|---------|---------|
|`answerType` i `resultIndex` | `answerType`identyfikuje odpowiedź (podmiot lub miejsce) `resultIndex` i identyfikuje wynik w ramach tej odpowiedzi (na przykład jednostki). Indeks zaczyna się od 0.|
|`value`    | `value`Zawiera identyfikator, który pasuje do identyfikatora odpowiedzi lub wyniku w odpowiedzi. Odpowiedź lub wyniki zawierają identyfikator, ale nie oba. |
  
Korzystanie `answerType` z `resultIndex` i jest procesem dwuetapowym. Najpierw służy `answerType` do identyfikowania odpowiedzi zawierającej wyniki do wyświetlenia. Następnie `resultIndex` użyj indeksowania wyników tej odpowiedzi, aby uzyskać wynik do wyświetlenia. (Wartość `answerType` to nazwa pola w obiekcie [SearchResponse).](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#searchresponse) Jeśli masz wyświetlać wszystkie wyniki odpowiedzi razem, element odpowiedzi rankingowej nie `resultIndex` zawiera tego pola.

Użycie identyfikatora wymaga dopasowania identyfikatora rankingu do identyfikatora odpowiedzi lub jednego z jej wyników. Jeśli obiekt odpowiedzi `id` zawiera pole, wyświetl wszystkie wyniki odpowiedzi razem. Na przykład jeśli `Entities` obiekt `id` zawiera pole, wyświetlić wszystkie elementy artykuły razem. Jeśli `Entities` obiekt nie zawiera `id` pola, każda encja zawiera `id` pole, a odpowiedź klasyfikacji miesza encje z wynikami Miejsca.  
  
## <a name="ranking-response-example"></a>Przykład odpowiedzi na klasyfikację

Poniżej przedstawiono przykład [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse).
  
```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "Jimi Hendrix"
  },
  "entities": { ... },
  "rankingResponse": {
    "sidebar": {
      "items": [
        {
          "answerType": "Entities",
          "resultIndex": 0,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Entities.0"
          }
        },
        {
          "answerType": "Entities",
          "resultIndex": 1,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Entities.1"
          }
        }
      ]
    }
  }
}
```

Na podstawie tej odpowiedzi rankingowej na pasku bocznym będą wyświetlane wyniki dwóch jednostek związanych z Jimim Hendrixem.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie jednostronicowej aplikacji sieci Web](tutorial-bing-entities-search-single-page-app.md)
