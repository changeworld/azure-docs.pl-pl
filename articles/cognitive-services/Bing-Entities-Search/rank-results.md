---
title: Aby wyświetlić odpowiedzi przy użyciu klasyfikacji | Dokumentacja firmy Microsoft
description: Przedstawia użycie klasyfikacji, aby wyświetlić odpowiedzi, które zwraca interfejs API wyszukiwania usługi Bing jednostki.
services: cognitive-services
author: v-jerkin
manager: ehansen
ms.assetid: BBF87972-B6C3-4910-BB52-DE90893F6C71
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: article
ms.date: 12/12/2017
ms.author: v-jerkin
ms.openlocfilehash: 53354c0f78419a37e8896bb4d00e0d7aebf32203
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37059996"
---
# <a name="using-ranking-to-display-results"></a>Aby wyświetlić wyniki przy użyciu klasyfikacji  

Każda odpowiedź wyszukiwania jednostki zawiera [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse) odpowiedzi, podobnie jak w odpowiedzi wyszukiwania usługi Bing w sieci Web, który określa sposób musisz wyświetlić wyniki wyszukiwania. Odpowiedź klasyfikacji grupuje wyniki do bieguna połączeniach, a zawartość paska bocznego. Wynik bieguna jest wynikiem najważniejsze lub wyraźne i powinny być wyświetlane jako pierwsze. Jeśli nie są wyświetlane wyniki pozostałych w tradycyjny połączeniach i format paska bocznego, musisz podać zawartości połączeniach nowszej widoczność niż zawartość paska bocznego. 
  
W każdej grupie [elementów](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankinggroup-items) tablicy identyfikuje zawartość musi występować w określonej kolejności. Każdy element udostępnia dwa sposoby, aby zidentyfikować wyniku w odpowiedzi.  
  
-   `answerType` i `resultIndex` — `answerType` pole identyfikuje odpowiedzi (jednostka lub miejscu) i `resultIndex` identyfikuje wyniku w odpowiedzi (na przykład jednostka). Indeks się od zera.  
  
-   `value` — `value` Pole zawiera identyfikator, który jest zgodny z Identyfikatorem odpowiedzi lub wyniku w odpowiedzi. Odpowiedź na pytanie lub wyniki zawierają identyfikator, ale nie oba.  
  
Przy użyciu Identyfikatora wymaga jest zgodny z Identyfikatorem klasyfikacji o identyfikatorze odpowiedzi lub jednego z jego wyniki. Jeśli obiekt odpowiedzi zawiera `id` pola, Wyświetl wyniki wszystkich odpowiedzi ze sobą. Na przykład jeśli `Entities` obiekt zawiera `id` pola, wyświetlać wszystkie artykuły podmioty ze sobą. Jeśli `Entities` nie ma obiektu `id` pola każdej jednostki zawiera `id` pola i odpowiedzi klasyfikacji łączy jednostek z wyników miejsca.  
  
Przy użyciu `answerType` i `resultIndex` jest procesem dwuetapowym. Najpierw użyj `answerType` do identyfikowania odpowiedzi, który zawiera wyników do wyświetlenia. Użyć `resultIndex` do indeksu w tym odpowiedzi wyników można pobrać wyników do wyświetlenia. ( `answerType` Wartość jest nazwą pola w [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#searchresponse) obiektu.) Jeśli jest powinien, aby wyświetlić wyniki wszystkich odpowiedzi ze sobą, odpowiedzi klasyfikacji elementu nie zawiera `resultIndex` pola.

## <a name="ranking-response-example"></a>Klasyfikacja przykład odpowiedzi

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

W oparciu o tę odpowiedź klasyfikacji, paska bocznego może wyświetlić wyniki dwie jednostki powiązane z Jimi Hendrix.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Samouczek wyszukiwania usługi Bing jednostki](tutorial-bing-entities-search-single-page-app.md)
