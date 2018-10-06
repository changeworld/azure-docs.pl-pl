---
title: Przy użyciu klasyfikacji do wyświetlenia odpowiedzi — wyszukiwanie jednostek Bing
titlesuffix: Azure Cognitive Services
description: Pokazuje, jak używać funkcji Klasyfikacja do wyświetlania odpowiedzi, które zwraca interfejs API wyszukiwania jednostek Bing.
services: cognitive-services
author: v-jerkin
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: v-jerkin
ms.openlocfilehash: 4a336ccaea18ab84464f28aef170ccdc423b216d
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2018
ms.locfileid: "48814597"
---
# <a name="using-ranking-to-display-results"></a>Przy użyciu klasyfikacji, aby wyświetlić wyniki  

Każda jednostka wyszukiwania odpowiedź zawiera [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse) odpowiedzi, podobny do przedstawionego w odpowiedzi wyszukiwania w Internecie Bing, który określa sposób musisz wyświetlić wyniki wyszukiwania. Odpowiedź klasyfikacji grupuje wyniki do bieguna linii głównej i zawartość paska bocznego. Wynik bieguna jest wynikiem najważniejsze lub wyraźną i powinny być wyświetlane jako pierwsze. Jeśli nie są wyświetlane wyniki pozostałe w przypadku tradycyjnych linii głównej i paska bocznego format, musisz podać linii głównej zawartości bardziej widoczny niż zawartość pasku bocznym. 
  
W każdej grupie [elementów](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankinggroup-items) tablicy identyfikuje zawartość musi znajdować się w podanej kolejności. Każdy element zawiera zidentyfikować wynik w odpowiedzi na dwa sposoby.  
  
-   `answerType` i `resultIndex` — `answerType` pole identyfikuje odpowiedzi (jednostki lub umieść) i `resultIndex` identyfikuje wyniku w odpowiedzi (na przykład jednostka). Indeks się od zera.  
  
-   `value` — `value` Pole zawiera identyfikator, który jest zgodny z Identyfikatorem odpowiedzi lub wyniku w odpowiedzi. Odpowiedź na pytanie lub wyniki zawierają identyfikator, ale nie oba jednocześnie.  
  
Przy użyciu Identyfikatora wymaga jest zgodny z Identyfikatorem klasyfikacji o identyfikatorze odpowiedzi lub jednego z jego wyniki. Jeśli obiekt odpowiedzi zawiera `id` pola, wyświetlić wyniki wszystkich odpowiedzi ze sobą. Na przykład jeśli `Entities` obiekt zawiera `id` pola, Wyświetl wszystkie artykuły jednostki razem. Jeśli `Entities` nie ma obiektu `id` pola, a następnie każda jednostka zawiera `id` pola i odpowiedzi klasyfikacji napisana jednostki z wynikami miejsc.  
  
Za pomocą `answerType` i `resultIndex` jest procesem dwuetapowym. Najpierw należy użyć `answerType` do identyfikowania odpowiedzi, który zawiera wyników do wyświetlenia. Następnie możesz użyć `resultIndex` do indeksu na wyniki tej odpowiedzi, aby uzyskać wynik do wyświetlenia. ( `answerType` Wartością jest nazwa pola w [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#searchresponse) obiektu.) Jeśli masz powinien wyświetlić wyników wszystkich odpowiedzi ze sobą, odpowiedź klasyfikacji elementu nie zawiera `resultIndex` pola.

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
> [Samouczek wyszukiwania jednostek Bing](tutorial-bing-entities-search-single-page-app.md)
