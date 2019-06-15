---
title: Oceń metody — interfejs API usługi Eksploracja wiedzy
titlesuffix: Azure Cognitive Services
description: Dowiedz się, jak użyć tej metody Oceń w wiedzy Exploration Service (KES) interfejsu API.
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: dcfa9bb7931cf3b682bacf722b67acd6d4a370c0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60814108"
---
# <a name="evaluate-method"></a>Oceń — metoda

*Oceny* metoda oblicza i zwraca wyniki wyrażeń zapytań ze strukturą, na podstawie danych indeksu.

Zwykle wyrażenia zostaną uzyskane z odpowiedzi metody interpretację.  Można również utworzyć, wyrażenia zapytania samodzielnie, ale (zobacz [strukturalne wyrażenia zapytania](Expressions.md)).  

## <a name="request"></a>Żądanie 

`http://<host>/evaluate?expr=<expr>&attributes=<attrs>[&<options>]`   

Name (Nazwa)|Wartość|Opis
----|----|----
expr       | Ciąg tekstowy | Wyrażenie zapytań ze strukturą, który wybierze podzbiór jednostek indeksu.
Atrybuty | Ciąg tekstowy | Rozdzielana przecinkami lista atrybutów do uwzględnienia w odpowiedzi.
count      | Liczba (domyślny = 10) | Maksymalna liczba wyników do zwrócenia.
offset     | Liczba (domyślny = 0) | Indeks pierwszego wyniku do zwrócenia.
orderby |   Ciąg tekstowy | Nazwa atrybutu używanego w celu posortowania wyników, a następnie według kolejności sortowania opcjonalne (domyślny = asc): "*attrname*[: (asc&#124;desc)]".  Jeśli nie zostanie określony, wyniki są zwracane przez zmniejszenie prawdopodobieństwa logarytmu naturalnego.
timeout  | Liczba (domyślny = 1000) | Przekroczono limit czasu w milisekundach. Zwracane są tylko wyniki obliczane przed upływem limitu czasu.

Za pomocą *liczba* i *przesunięcie* parametrów, dużą liczbę wyników można uzyskać przyrostowo za pośrednictwem wielu żądań.
  
## <a name="response-json"></a>Odpowiedź (JSON)
JSONPath|Opis
----|----
$.expr | *wyrażenie* parametrów z żądania.
$.entities | Tablica 0 lub więcej jednostek obiektu dopasowywania wyrażeń zapytań ze strukturą. 
$.aborted | Wartość true, jeśli upłynął limit czasu żądania.

Każda jednostka zawiera *logprob* wartości i wartości żądanych atrybutów.

## <a name="example"></a>Przykład
W tym przykładzie akademickich publikacje następujące żądanie przekazuje wyrażenia zapytań ze strukturą (potencjalnie z danych wyjściowych *interpretacji* żądania) i pobiera kilka atrybutów góra 2 dopasowania jednostki:

`http://<host>/evaluate?expr=Composite(Author.Name=='jaime teevan')&attributes=Title,Y,Author.Name,Author.Id&count=2`

Odpowiedź zawiera 2 pierwszych ("liczba = 2") najprawdopodobniej dopasowania jednostek.  Dla każdej jednostki tytuł, roku, imię i nazwisko autora i atrybuty Identyfikatora Autor są zwracane.  Należy pamiętać, że jak wartości atrybutów struktury złożone odpowiada sposób, w jaki są one określone w pliku danych. 

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
