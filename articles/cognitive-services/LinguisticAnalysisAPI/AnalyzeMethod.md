---
title: Analizowanie metoda - interfejsu API analizy językowej
titlesuffix: Azure Cognitive Services
description: Jak użyć metody analizy interfejsu API analizy językowej do analizowania określonych danych wejściowych języka naturalnego.
services: cognitive-services
author: RichardSunMS
manager: cgronlun
ms.service: cognitive-services
ms.component: linguistic-analysis
ms.topic: conceptual
ms.date: 12/13/2016
ms.author: lesun
ROBOTS: NOINDEX
ms.openlocfilehash: 87df00ae5ca12b168f2e1c03850da2e94cec350b
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2018
ms.locfileid: "48239306"
---
# <a name="analyze-method"></a>Metoda analyze

> [!IMPORTANT]
> Wersja zapoznawcza analizy językowej została zlikwidowana 9 sierpnia 2018 r. Firma Microsoft zaleca używanie [moduły analizy tekstu w usłudze Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics) do przetwarzania tekstu i analizy.

**Analizowanie** interfejsu API REST jest używany do analizowania danych wejściowych danego języka naturalnego.
Który może obejmować tylko znajdowanie [zdania i tokeny](Sentences-and-Tokens.md) w tym dane wejściowe, znajdowanie [tagów części mowy](POS-tagging.md), lub znajdowania [drzewa constitutency](Constituency-Parsing.md).
Można określić wyników, wybierając odpowiednie analizatorów.
Aby wyświetlić listę wszystkich dostępnych analizatorów, Przyjrzyj się  **[analizatory](AnalyzersMethod.md)**.

Uwaga: należy także określić język, w ciągu wejściowym.

**Punkt końcowy REST:**
```
https://westus.api.cognitive.microsoft.com/linguistics/v1.0/analyze
```
<br>

## <a name="request-parameters"></a>Parametry żądania

Name (Nazwa) | Typ | Wymagane | Opis
-----|-------|----------|------------
**Język**    | ciąg | Yes | Dwuliterowa kod języka ISO ma być używany do analizy. Na przykład "en" jest angielski.
**analyzerIds** | Lista ciągów | Yes | Lista identyfikatorów GUID analizatorów, aby zastosować. Zobacz dokumentację analizatorów, aby uzyskać więcej informacji.
**Tekst**        | ciąg | Yes | Nieprzetworzone dane wejściowe do analizy. Może to być krótkiego ciągu, takiej jak słowo lub frazę, pełną zdania, akapitu pełną lub discourse.

## <a name="response-json"></a>Odpowiedź (JSON)

Tablica wyników analizy, po jednym dla każdego atrybutu określona w żądaniu.

Wyniki wyglądają następująco:

Name (Nazwa) | Typ | Opis
-----|------|--------------
analyzerId | ciąg | Analizator określony identyfikator GUID
wynik | obiekt | wynik analizatora

Należy pamiętać, że typ wyniku zależy od typu analizatora danych wejściowych.

### <a name="tokens-response-json"></a>Tokeny odpowiedzi (JSON)

Name (Nazwa) | Typ | Opis
-----|------|-------------
wynik | Lista obiektów zdania | granice zdania zidentyfikowany w tekście |
wynik [x]. Przesunięcie | Int | każde zdanie początkowe przesunięcie znaku |
wynik [x]. Len | Int | Długość w znakach każdego zdania |
wynik [x]. Tokeny | Lista obiektów tokenu | Token granice wskazane w zdaniu |
wynik [x]. Tokeny [t]. Przesunięcie | Int | początkowe przesunięcie znaku tokenu |
wynik [x]. Tokeny [t]. Len | Int | Długość w znakach tokenu |
wynik [x]. Tokeny [t]. RawToken | ciąg | znak wewnątrz ten token, zanim normalizacji |
wynik [x]. Tokeny [t]. NormalizedToken | ciąg | znormalizowana postać znaku bezpieczne użycie [drzewo analizy](Constituency-Parsing.md); na przykład znak nawiasu otwierającego "(" staje się - LRB - |

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

Treści żądania: Ładunek JSON
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
