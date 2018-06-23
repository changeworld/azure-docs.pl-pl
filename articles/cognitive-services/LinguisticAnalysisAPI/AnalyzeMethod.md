---
title: Analizowanie metoda w interfejsie API językową analiza | Dokumentacja firmy Microsoft
description: Sposób użycia metody analizy językową analiza interfejsu API do analizowania określone dane wejściowe języka naturalnego.
services: cognitive-services
author: RichardSunMS
manager: wkwok
ms.service: cognitive-services
ms.component: linguistic-analysis
ms.topic: article
ms.date: 12/13/2016
ms.author: lesun
ms.openlocfilehash: b17a00f31845bfa05572dff7ca94e9a1ffd69586
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347108"
---
# <a name="analyze-method"></a>Analizuj — metoda

**Analizowanie** interfejsu API REST jest używany do analizowania wprowadzania danego języka naturalnego.
Które mogą dotyczyć tylko znajdowanie [zdań i tokeny](Sentences-and-Tokens.md) w tym danych wejściowych, znalezienia [tagów części z mowy](POS-tagging.md), lub znajdowania [drzewa constitutency](Constituency-Parsing.md).
Można określić wyników, wybierając odpowiednie analizatorów.
Aby wyświetlić listę wszystkich dostępnych analizatorów, obejrzyj  **[analizatorów](AnalyzersMethod.md)**.

Należy pamiętać, że trzeba określić język ciągu wejściowego.

**Punkt końcowy REST:**
```
https://westus.api.cognitive.microsoft.com/linguistics/v1.0/analyze
```
<br>

## <a name="request-parameters"></a>Parametry żądania

Name (Nazwa) | Typ | Wymagane | Opis
-----|-------|----------|------------
**język**    | ciąg | Yes | Kod języka ISO do zastosowania w przypadku analizy dwóch list. Na przykład "en" jest angielski.
**analyzerIds** | Lista ciągów | Yes | Lista identyfikatorów GUID z analizatorów do zastosowania. Zobacz dokumentację analizatorów, aby uzyskać więcej informacji.
**tekst**        | ciąg | Yes | Nieprzetworzone dane wejściowe do analizy. Może to być krótkich ciągów, takich jak wyraz lub frazę, pełny zdania, lub pełne akapitu lub discourse.

<br>
## <a name="response-json"></a>Odpowiedź (JSON)
Tablica analizy danych wyjściowych, po jednej dla każdego atrybutu określony w żądaniu.

Wyniki wyglądają następująco:

Name (Nazwa) | Typ | Opis
-----|------|--------------
analyzerId | ciąg | Analizator określony identyfikator GUID
Wynik | obiekt | wynik analizatora

Należy zwrócić uwagę na to, czy typ wyniku zależy od typu wejściowych analizatora.

### <a name="tokens-response-json"></a>Odpowiedzi tokeny (JSON)

Name (Nazwa) | Typ | Opis
-----|------|-------------
Wynik | Lista obiektów zdanie | granice zdanie zidentyfikowany w tekście |
wynik [x]. Przesunięcie | int | początkowe przesunięcie znaku każdego zdania |
wynik [x]. Długość | int | Długość w znakach każdego zdania |
wynik [x]. Tokeny | Lista obiektów tokenu | granice tokenu w zdaniu |
wynik [x]. Tokeny [y]. Przesunięcie | int | początkowe przesunięcie znaku tokenu |
wynik [x]. Tokeny [y]. Długość | int | Długość w znakach tokenu |
wynik [x]. Tokeny [y]. RawToken | ciąg | znak wewnątrz tokenu, przed normalizacji |
wynik [x]. Tokeny [y]. NormalizedToken | ciąg | znormalizowana postać znaku, bezpieczne [drzewo analizy](Constituency-Parsing.md); na przykład znak nawiasu otwierającego "(" staje się - LRB - |

Przykładowe dane wejściowe: "to jest test. Witaj. "
Przykład JSON odpowiedzi:
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
Dla każdego zdania znajduje się lista tagów POS jeden tag POS dla każdego tokenu.
Aby znaleźć tokenu odpowiadający tagami POS, należy poprosić o na obiekt tokenizacji.

### <a name="constituency-tree-response-json"></a>Wyborczy drzewa odpowiedzi (JSON)

Wynik jest lista ciągów, drzewo analizy jednej dla każdego zdania znalezione w danych wejściowych.
Drzewa analizy są reprezentowane w formularzu ujętego w nawiasy.

<br>

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

