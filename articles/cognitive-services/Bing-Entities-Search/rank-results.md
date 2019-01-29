---
title: Przy użyciu klasyfikacji do wyświetlenia odpowiedzi — wyszukiwanie jednostek Bing
titlesuffix: Azure Cognitive Services
description: Pokazuje, jak używać funkcji Klasyfikacja do wyświetlania odpowiedzi, które zwraca interfejs API wyszukiwania jednostek Bing.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: aahi
ms.openlocfilehash: f0cae32acf2db62a5d3c060ea944b1131252beda
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55195923"
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
