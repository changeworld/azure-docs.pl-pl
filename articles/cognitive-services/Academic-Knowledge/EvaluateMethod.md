---
title: Ocena metoda w interfejsie API Academic Knowledge | Dokumentacja firmy Microsoft
description: Użyj metody Evaluate, aby zwrócić zestawu jednostek academic na podstawie wyrażenia zapytania w kognitywnych usług firmy Microsoft.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: 3005ae1f6df042a49db086de4982d8206f6938a4
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346873"
---
# <a name="evaluate-method"></a>Ocena — metoda

**Oceny** interfejsu API REST służy do zwracania zestawu jednostek academic na podstawie wyrażenia zapytania.
<br>

**Punkt końcowy REST:**  
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/evaluate? 
```   
<br>
## <a name="request-parameters"></a>Parametry żądania  
Name (Nazwa)     | Wartość | Wymagana?  | Opis
-----------|-----------|---------|--------
**expr**       | Ciąg tekstowy | Yes | Wyrażenia zapytania, który określa jednostki, która ma zostać zwrócony.
**Model**      | Ciąg tekstowy | Nie  | Nazwa modelu, który chcesz zbadać.  Obecnie, domyślnie przyjmowana jest wartość *najnowsze*.        
**Atrybuty** | Ciąg tekstowy | Nie<br>domyślne: identyfikator | Rozdzielana przecinkami lista określająca wartości atrybutów, które znajdują się w odpowiedzi. W nazwach atrybutów jest uwzględniana wielkość liter.
**Liczba**        | Liczba | Nie<br>Domyślny: 10 | Liczba wyników do zwrócenia.
**offset**     | Liczba |   Nie<br>Domyślna: 0    | Indeks pierwszego wyniku do zwrócenia.
**orderby** |   Ciąg tekstowy | Nie<br>Wartość domyślna: dzięki skróceniu PRAWDPD | Nazwa atrybutu, który jest używany na potrzeby sortowania jednostek. Opcjonalnie rosnąco/malejąco może zostać określony. Format: *name: asc* lub *name: desc*.
  
 <br>
## <a name="response-json"></a>Odpowiedź (JSON)
Name (Nazwa) | Opis
-------|-----   
**expr** |  *Wyrażenie* parametrów z żądania.
**jednostki** |  Tablica 0 lub więcej jednostek, które pasują do wyrażenia zapytania. Każdy podmiot zawiera wartość prawdopodobieństwa logarytm naturalny i wartości innych wymaganych atrybutów.
**zostało przerwane** | Wartość true, jeśli upłynął limit czasu żądania.

<br>
#### <a name="example"></a>Przykład:
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/evaluate?expr=
Composite(AA.AuN=='jaime teevan')&count=2&attributes=Ti,Y,CC,AA.AuN,AA.AuId
```
<br>Zazwyczaj wyrażenia zostaną uzyskane z odpowiedzi na **interpretowania** metody.  Ale można również utworzyć wyrażenia zapytania samodzielnie (zobacz [składnia wyrażeń](QueryExpressionSyntax.md)).  
  
Przy użyciu *liczba* i *przesunięcie* parametrów dużej liczby wyników można uzyskać bez wysyłania pojedyncze żądanie tę odpowiedź wyniki w dużych (i potencjalnie wolno).  W tym przykładzie żądania używane wyrażenie interpretacji pierwszy z **interpretowania** odpowiedzi interfejsu API jako *wyrażenie* wartość. *Count = 2* parametr określa są żądane 2 wyniki jednostki. I *atrybuty = analizy czasowej, Y, DW, AA. AuN, AA. AuId* parametr wskazuje, że tytuł, rok, liczba cytatu, imię i nazwisko autora i identyfikator autora wymagane są na każdy wynik.  Zobacz [atrybuty obiektu](EntityAttributes.md) listę atrybutów.
  
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
 
