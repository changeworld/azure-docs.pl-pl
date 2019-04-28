---
title: Analizowanie metoda - interfejsu API analizy językowej
titlesuffix: Azure Cognitive Services
description: Jak użyć metody analizy interfejsu API analizy językowej do analizowania określonych danych wejściowych języka naturalnego.
services: cognitive-services
author: RichardSunMS
manager: nitinme
ms.service: cognitive-services
ms.subservice: linguistic-analysis
ms.topic: conceptual
ms.date: 12/13/2016
ms.author: lesun
ROBOTS: NOINDEX
ms.openlocfilehash: 02c41e2510fd77f4bb65143faf62737f0985d2b7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61401190"
---
# <a name="analyze-method"></a>Metoda analyze

> [!IMPORTANT]
> Wersja zapoznawcza analizy językowej została wycofana 9 sierpnia 2018 r. W celu przetwarzania i analizy tekstu zalecamy korzystanie z [modułów analizy tekstu w usłudze Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics).

**Analizowanie** interfejsu API REST jest używany do analizowania danych wejściowych danego języka naturalnego.
Który może obejmować tylko znajdowanie [zdania i tokeny](Sentences-and-Tokens.md) w tym dane wejściowe, znajdowanie [tagów części mowy](POS-tagging.md), lub znajdowania [drzewa fraz](Constituency-Parsing.md).
Można określić wyników, wybierając odpowiednie analizatorów.
Aby wyświetlić listę wszystkich dostępnych analizatorów, Przyjrzyj się  **[analizatory](AnalyzersMethod.md)**.

Uwaga: należy także określić język, w ciągu wejściowym.

**Punkt końcowy REST:**
```
https://westus.api.cognitive.microsoft.com/linguistics/v1.0/analyze
```
<br>

## <a name="request-parameters"></a>Parametry żądania

Name (Nazwa) | Type | Wymagane | Opis
-----|-------|----------|------------
**Język**    | string | Yes | Dwuliterowa kod języka ISO ma być używany do analizy. Na przykład "en" jest angielski.
**analyzerIds** | Lista ciągów | Yes | Lista identyfikatorów GUID analizatorów, aby zastosować. Zobacz dokumentację analizatorów, aby uzyskać więcej informacji.
**Tekst**        | string | Yes | Nieprzetworzone dane wejściowe do analizy. Może to być krótkiego ciągu, takiej jak słowo lub frazę, pełną zdania, akapitu pełną lub discourse.

## <a name="response-json"></a>Odpowiedź (JSON)

Tablica wyników analizy, po jednym dla każdego atrybutu określona w żądaniu.

Wyniki wyglądają następująco:

Name (Nazwa) | Typ | Opis
-----|------|--------------
analyzerId | string | Analizator określony identyfikator GUID
wynik | obiekt | wynik analizatora

Należy pamiętać, że typ wyniku zależy od typu analizatora danych wejściowych.

### <a name="tokens-response-json"></a>Tokeny odpowiedzi (JSON)

Name (Nazwa) | Typ | Opis
-----|------|-------------
wynik | Lista obiektów zdania | granice zdania zidentyfikowany w tekście |
result[x].Offset | int | każde zdanie początkowe przesunięcie znaku |
result[x].Len | int | Długość w znakach każdego zdania |
result[x].Tokens | Lista obiektów tokenu | Token granice wskazane w zdaniu |
wynik [x]. Tokeny [t]. Przesunięcie | int | początkowe przesunięcie znaku tokenu |
result[x].Tokens[y].Len | int | Długość w znakach tokenu |
wynik [x]. Tokeny [t]. RawToken | string | znak wewnątrz ten token, zanim normalizacji |
wynik [x]. Tokeny [t]. NormalizedToken | string | znormalizowana postać znaku bezpieczne użycie [drzewo analizy](Constituency-Parsing.md); na przykład znak nawiasu otwierającego "(" staje się - LRB - |

Przykładowe dane wejściowe: "to jest test. Witaj. "
Przykładowa odpowiedź JSON:
```json
[
  {
    "Len": 15,
    "Offset": 0,
    "Tokens": [
      {
        "Len": 4,
        "NormalizedToken": "This",
        "Offset": 0,
        "RawToken": "This"
      },
      {
        "Len": 2,
        "NormalizedToken": "is",
        "Offset": 5,
        "RawToken": "is"
      },
      {
        "Len": 1,
        "NormalizedToken": "a",
        "Offset": 8,
        "RawToken": "a"
      },
      {
        "Len": 4,
        "NormalizedToken": "test",
        "Offset": 10,
        "RawToken": "test"
      },
      {
        "Len": 1,
        "NormalizedToken": ".",
        "Offset": 14,
        "RawToken": "."
      }
    ]
  },
  {
    "Len": 6,
    "Offset": 16,
    "Tokens": [
      {
        "Len": 5,
        "NormalizedToken": "Hello",
        "Offset": 16,
        "RawToken": "Hello"
      },
      {
        "Len": 1,
        "NormalizedToken": ".",
        "Offset": 21,
        "RawToken": "."
      }
    ]
  }
]
```


### <a name="pos-tags-response-json"></a>Położenie znaczników odpowiedzi (JSON)

Wynik jest listę list ciągów.
Dla każdego zdania znajduje się lista tagów punktu sprzedaży, jeden tag punktu sprzedaży dla każdego tokenu.
Aby znaleźć token odpowiadający każdego znacznika punktu sprzedaży, należy poprosić o na obiekt tokenizacji.

### <a name="constituency-tree-response-json"></a>Fraz drzewa odpowiedzi (JSON)

Wynikiem jest lista ciągów, drzewo analizy jeden dla każdego zdania w danych wejściowych.
Drzew analizy są reprezentowane w formularzu ujęty w nawiasy.

## <a name="example"></a>Przykład

`POST /analyze`

Treść żądania: Ładunek JSON
```json
{
  "language": "en",
  "analyzerIds": [
    "4FA79AF1-F22C-408D-98BB-B7D7AEEF7F04",
    "22A6B758-420F-4745-8A3C-46835A67C0D2" ],
  "text": "Hi, Tom! How are you today?"
}
```

Odpowiedź: JSON
```json
[
  {
    "analyzerId": "4FA79AF1-F22C-408D-98BB-B7D7AEEF7F04",
    "result": [ ["NNP",",","NNP","."], ["WRB","VBP","PRP","NN","."] ]
  },
  {
    "analyzerId": "22A6B758-420F-4745-8A3C-46835A67C0D2",
    "result":["(TOP (S (NNP Hi) (, ,) (NNP Tom) (. !)))","(TOP (SBARQ (WHADVP (WRB How)) (SQ (VP (VBP are)) (NP (PRP you)) (NN today) (. ?))))"]
  }
]
```
