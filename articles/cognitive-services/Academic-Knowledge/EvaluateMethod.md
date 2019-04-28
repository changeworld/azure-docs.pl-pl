---
title: Oceń metody — interfejs Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Aby zwrócić zestaw obiektów akademickich oparte na wyrażeniu zapytania, należy użyć metody Evaluate.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: d2e628fb7fc502ef9ba81d20680d66f24fd7d138
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61339100"
---
# <a name="evaluate-method"></a>Oceń — metoda

**Oceny** interfejsu API REST służy do zwracania zbiór obiektów akademickich oparte na wyrażeniu zapytania.
<br>

**Punkt końcowy REST:**  
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/evaluate? 
```   
<br>

## <a name="request-parameters"></a>Parametry żądania  

Name (Nazwa)     | Wartość | Wymagana?  | Opis
-----------|-----------|---------|--------
**expr**       | Ciąg tekstowy | Yes | Wyrażenie zapytania, które określa jednostki, która ma zostać zwrócony.
**model**      | Ciąg tekstowy | Nie  | Nazwa modelu, który chcesz zbadać.  Obecnie ma domyślnie wartość *najnowsze*.        
**Atrybuty** | Ciąg tekstowy | Nie<br>Wartość domyślna: Identyfikator | Listę rozdzielonych przecinkami, która określa wartości atrybutów, które znajdują się w odpowiedzi. Nazwy atrybutów jest rozróżniana wielkość liter.
**count**        | Liczba | Nie<br>Domyślne: 10 | Liczba wyników do zwrócenia.
**offset**     | Liczba |   Nie<br>Domyślne: 0    | Indeks pierwszego wyniku do zwrócenia.
**orderby** |   Ciąg tekstowy | Nie<br>Wartość domyślna: dzięki skróceniu PRAWDPD | Nazwa atrybutu, który jest używany na potrzeby sortowania jednostki. Opcjonalnie rosnąco/malejąco może być określona. Format to: *name: asc* lub *name: desc*.
  
 <br>

## <a name="response-json"></a>Odpowiedź (JSON)

Name (Nazwa) | Opis
-------|-----   
**expr** |  *Expr* parametrów z żądania.
**Jednostki** |  Tablica 0 lub więcej jednostek, które dopasowane wyrażenia zapytania. Każda jednostka zawiera wartość prawdopodobieństwa logarytmu naturalnego i wartości innych wymaganych atrybutów.
**aborted** | Wartość true, jeśli upłynął limit czasu żądania.

<br>

#### <a name="example"></a>Przykład:
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/evaluate?expr=
Composite(AA.AuN=='jaime teevan')&count=2&attributes=Ti,Y,CC,AA.AuN,AA.AuId
```
<br>Zwykle wyrażenia zostaną uzyskane z odpowiedzią **interpretacji** metody.  Można również utworzyć, wyrażenia zapytania samodzielnie, ale (zobacz [składni wyrażeń zapytania](QueryExpressionSyntax.md)).  
  
Za pomocą *liczba* i *przesunięcie* parametrów, dużą liczbę wyników można uzyskać bez wysyłania pojedyncze żądanie odpowiedź wyniki w ogromnych (i potencjalnie wolno).  W tym przykładzie żądania używane wyrażenie interpretacji pierwszy z **interpretacji** odpowiedzi interfejsu API jako *expr* wartość. *Count = 2* parametr określa, są żądane wyniki 2 obiektu. I *atrybuty = Ti, Y, DW, AA. AuN, AA. AuId* parametr wskazuje, są żądane tytuł, rok, liczba cytatu, imię i nazwisko autora i autor identyfikator dla każdego wyniku.  Zobacz [atrybutów jednostki](EntityAttributes.md) listę atrybutów.
  
```JSON
{
  "expr": "Composite(AA.AuN=='jaime teevan')",
  "entities": 
  [
    {
      "logprob": -15.08,
      "Ti": "personalizing search via automated analysis of interests and activities",
      "Y": 2005,
      "CC": 372,
      "AA": [
        {
          "AuN": "jaime teevan",
          "AuId": 1968481722
        },
        {
          "AuN": "susan t dumais",
          "AuId": 676500258
        },
        {
          "AuN": "eric horvitz",
          "AuId": 1470530979
        }
      ]
    },
    {
      "logprob": -15.389,
      "Ti": "the perfect search engine is not enough a study of orienteering behavior in directed search",
      "Y": 2004,
      "CC": 237,
      "AA": [
        {
          "AuN": "jaime teevan",
          "AuId": 1982462162
        },
        {
          "AuN": "christine alvarado",
          "AuId": 2163512453
        },
        {
          "AuN": "mark s ackerman",
          "AuId": 2055132526
        },
        {
          "AuN": "david r karger",
          "AuId": 2012534293
        }
      ]
    }
  ]
}
 ```
 
