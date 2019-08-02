---
title: Oceń metodę — Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Użyj metody szacowania, aby zwrócić zestaw jednostek akademickich opartych na wyrażeniu zapytania.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 69e701d6727e5410b71e6cf8fbe20a1cd038ddb0
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705008"
---
# <a name="evaluate-method"></a>Metoda szacowania

Interfejs API REST **szacowania** służy do zwracania zestawu jednostek akademickich na podstawie wyrażenia zapytania.
<br>

**Punkt końcowy REST:**  
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/evaluate? 
```   
<br>

## <a name="request-parameters"></a>Parametry żądania  

Name (Nazwa)     | Value | Wymagane?  | Opis
-----------|-----------|---------|--------
**expr**       | Ciąg tekstowy | Tak | Wyrażenie zapytania określające, które jednostki należy zwrócić.
**model**      | Ciąg tekstowy | Nie  | Nazwa modelu, który ma być wysyłany do zapytania.  Obecnie wartość domyślna to *Najnowsza*.        
**Attributes** | Ciąg tekstowy | Nie<br>wartooć Id | Rozdzielana przecinkami lista określająca wartości atrybutów, które są zawarte w odpowiedzi. W nazwach atrybutów jest rozróżniana wielkość liter.
**count**        | Number | Nie<br>Domyślne: 10 | Liczba wyników do zwrócenia.
**offset**     | Number |   Nie<br>Domyślne: 0    | Indeks pierwszego wyniku do zwrócenia.
**orderby** |   Ciąg tekstowy | Nie<br>Domyślne: przez zmniejszenie wyniku | Nazwa atrybutu, który jest używany do sortowania jednostek. Opcjonalnie można określić rosnąco/malejąco. Format to: *name: ASC* lub *name: DESC*.
  
 <br>

## <a name="response-json"></a>Odpowiedź (JSON)

Name (Nazwa) | Opis
-------|-----   
**expr** |  Parametr *Expr* z żądania.
**obiekty** |  Tablica zawierająca 0 lub więcej jednostek, które pasują do wyrażenia zapytania. Każda jednostka zawiera naturalną wartość prawdopodobieństwa dziennika oraz wartości innych żądanych atrybutów.
**aborted** | Ma wartość true, jeśli upłynął limit czasu żądania.

<br>

#### <a name="example"></a>Przykład:
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/evaluate?expr=
Composite(AA.AuN=='jaime teevan')&count=2&attributes=Ti,Y,CC,AA.AuN,AA.AuId
```
<br>Zwykle wyrażenie zostanie uzyskane z odpowiedzi na metodę **interpretacji** .  Ale można również tworzyć wyrażenia zapytania samodzielnie (patrz [składnia wyrażenia zapytania](QueryExpressionSyntax.md)).  
  
Korzystając z parametrów *Count* i *offset* , można uzyskać dużą liczbę wyników bez wysyłania pojedynczego żądania, które powoduje znaczną (i potencjalnie wolną) odpowiedź.  W tym przykładzie żądanie używało wyrażenia jako pierwszej interpretacji z odpowiedzi interpretuje interfejs API jako wartość *wyrażenia* . Parametr *Count = 2* określa, że żądane są 2 wyniki jednostek. I *atrybuty = TI, Y, CC, AA. AuN, AA. Parametr AuId* wskazuje, że dla każdego wyniku żądano tytułu, roku, liczby cytatów, nazwy autora i identyfikatora autora.  Zobacz [atrybuty jednostki](EntityAttributes.md) dla listy atrybutów.
  
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
 
