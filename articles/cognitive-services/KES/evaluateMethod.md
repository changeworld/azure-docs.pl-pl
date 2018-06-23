---
title: Oceniają metodę w interfejsie API usługi eksploracji wiedzy | Dokumentacja firmy Microsoft
description: Dowiedz się, jak użyć tej metody Evaluate w wiedzy eksploracji usługi (KES) interfejsu API w usługach kognitywnych.
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: fc3d73b326b565cfe40d1b82cc419357b28a801a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347060"
---
# <a name="evaluate-method"></a>Ocena — metoda
*Oceny* metody ocenia i zwraca dane wyjściowe wyrażenia zapytania strukturalnych na podstawie danych indeksu.

Zazwyczaj odpowiedzi do metody interpret uzyskany wyrażenie.  Ale można również utworzyć wyrażenia zapytania samodzielnie (zobacz [Structured Query — wyrażenie](Expressions.md)).  

## <a name="request"></a>Żądanie 
`http://<host>/evaluate?expr=<expr>&attributes=<attrs>[&<options>]`   

Name (Nazwa)|Wartość|Opis
----|----|----
wyrażenie       | Ciąg tekstowy | Wyrażenie Structured query wybiera podzbiór jednostek indeksu.
Atrybuty | Ciąg tekstowy | Rozdzielana przecinkami lista atrybutów do uwzględnienia w odpowiedzi.
count      | Numer (domyślne = 10) | Maksymalna liczba wyników do zwrócenia.
Przesunięcie     | Numer (domyślne = 0) | Indeks pierwszego wyniku do zwrócenia.
OrderBy |   Ciąg tekstowy | Nazwa atrybutu używane do sortowania wyników, następuje opcjonalny sortowania (domyślne = asc): "*attrname*[: (asc&#124;desc)]".  Jeśli nie zostanie określony, wyniki są zwracane przez zmniejszenie prawdopodobieństwa logarytm naturalny.
timeout  | Numer (domyślne = 1000) | Limit czasu w milisekundach. Zwracane są tylko wyniki obliczane przed upływem limitu czasu.

Przy użyciu *liczba* i *przesunięcie* parametrów dużej liczby wyników można uzyskać przyrostowo przez wiele żądań.
  
## <a name="response-json"></a>Odpowiedź (JSON)
JSONPath|Opis
----|----
$.expr | *wyrażenie* parametrów z żądania.
$.entities | Tablica 0 lub więcej jednostek obiekt zgodnego wyrażeniem structured query. 
$.aborted | Wartość true, jeśli upłynął limit czasu żądania.

Każda jednostka zawiera *logprob* wartości i wartości wymaganych atrybutów.

## <a name="example"></a>Przykład
W przykładzie academic publikacji następujące żądania przekazuje wyrażenia zapytania strukturalnych (potencjalnie z danych wyjściowych *interpretowania* żądania) i pobiera kilka atrybutów góra 2 dopasowania jednostek:

`http://<host>/evaluate?expr=Composite(Author.Name=='jaime teevan')&attributes=Title,Y,Author.Name,Author.Id&count=2`

Odpowiedź zawiera górnej 2 ("count = 2") najprawdopodobniej dopasowania jednostek.  Dla każdej jednostki tytuł, rok, imię i nazwisko autora i atrybuty Identyfikatora autora są zwracane.  Należy zauważyć, jak wartości atrybutów struktury złożone zgodny sposób są one określone w pliku danych. 

```json
{
  "expr": "Composite(Author.Name=='jaime teevan')",
  "entities": 
  [
    {
      "logprob": -6.645,
      "Ti": "personalizing search via automated analysis of interests and activities",
      "Y": 2005,
      "Author": [
        {
          "Name": "jaime teevan",
          "Id": 1968481722
        },
        {
          "Name": "susan t dumais",
          "Id": 676500258
        },
        {
          "Name": "eric horvitz",
          "Id": 1470530979
        }
      ]
    },
    {
      "logprob": -6.764,
      "Ti": "the perfect search engine is not enough a study of orienteering behavior in directed search",
      "Y": 2004,
      "Author": [
        {
          "Name": "jaime teevan",
          "Id": 1982462162
        },
        {
          "Name": "christine alvarado",
          "Id": 2163512453
        },
        {
          "Name": "mark s ackerman",
          "Id": 2055132526
        },
        {
          "Name": "david r karger",
          "Id": 2012534293
        }
      ]
    }
  ]
}
```
