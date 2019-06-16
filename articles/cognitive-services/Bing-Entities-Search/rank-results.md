---
title: Przy użyciu klasyfikacji do wyświetlenia odpowiedzi — wyszukiwanie jednostek Bing
titlesuffix: Azure Cognitive Services
description: Dowiedz się, jak używać funkcji Klasyfikacja do wyświetlania odpowiedzi, które zwraca interfejs API wyszukiwania jednostek Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 9e2a4075436145a0cc185b7ab1b406fa8d27b8e3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60309338"
---
# <a name="using-ranking-to-display-entity-search-results"></a>Przy użyciu klasyfikacji do wyświetlenia wyników wyszukiwania jednostek  

Każda jednostka wyszukiwania odpowiedź zawiera [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse) odpowiedzi, który określa sposób musisz wyświetlić wyniki wyszukiwania zwracane przez interfejs API wyszukiwania jednostek Bing. Odpowiedź klasyfikacji grupuje wyniki do bieguna linii głównej i zawartość paska bocznego. Wynik bieguna jest wynikiem najważniejsze lub wyraźną i powinny być wyświetlane jako pierwsze. Jeśli nie są wyświetlane wyniki pozostałe w przypadku tradycyjnych linii głównej i paska bocznego format, musisz podać linii głównej zawartości bardziej widoczny niż zawartość pasku bocznym. 
  
W każdej grupie [elementów](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankinggroup-items) tablicy identyfikuje zawartość musi znajdować się w podanej kolejności. Każdy element zawiera zidentyfikować wynik w odpowiedzi na dwa sposoby.  
 

|Pole | Opis  |
|---------|---------|
|`answerType` i `resultIndex` | `answerType` identyfikuje odpowiedzi (jednostki lub umieść) i `resultIndex` identyfikuje wynik w ramach tej odpowiedzi (na przykład jednostka). Indeks zaczyna się od 0.|
|`value`    | `value` Zawiera identyfikator, który jest zgodny z Identyfikatorem odpowiedzi lub wyniku w odpowiedzi. Odpowiedź na pytanie lub wyniki zawierają identyfikator, ale nie oba jednocześnie. |
  
Za pomocą `answerType` i `resultIndex` jest procesem dwuetapowym. Najpierw za pomocą `answerType` do identyfikowania odpowiedzi, który zawiera wyników do wyświetlenia. Następnie użyj `resultIndex` do indeksu na wyniki tej odpowiedzi, aby uzyskać wynik do wyświetlenia. ( `answerType` Wartością jest nazwa pola w [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#searchresponse) obiektu.) Jeśli masz powinien wyświetlić wyników wszystkich odpowiedzi ze sobą, odpowiedź klasyfikacji elementu nie zawiera `resultIndex` pola.

Przy użyciu Identyfikatora wymaga jest zgodny z Identyfikatorem klasyfikacji o identyfikatorze odpowiedzi lub jednego z jego wyniki. Jeśli obiekt odpowiedzi zawiera `id` pola, wyświetlić wyniki wszystkich odpowiedzi ze sobą. Na przykład jeśli `Entities` obiekt zawiera `id` pola, Wyświetl wszystkie artykuły jednostki razem. Jeśli `Entities` nie ma obiektu `id` pola, a następnie każda jednostka zawiera `id` pola i odpowiedzi klasyfikacji napisana jednostki z wynikami miejsc.  
  
## <a name="ranking-response-example"></a>Klasyfikacja przykład odpowiedzi

Poniżej pokazano przykład [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse).
  
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

Na podstawie tej klasyfikacji odpowiedzi, paska bocznego umożliwią wyświetlanie wyników dwie jednostki związane z Jimi Hendrix.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Tworzenie jednostronicowej aplikacji internetowej](tutorial-bing-entities-search-single-page-app.md)
