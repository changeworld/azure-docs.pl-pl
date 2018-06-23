---
title: Metoda analizatorów Lingistic analizy interfejsu API | Dokumentacja firmy Microsoft
description: Analizatory interfejsu API REST zawiera listę analizatorów, które są obecnie obsługiwane przez usługę w kognitywnych usług firmy Microsoft.
services: cognitive-services
author: RichardSunMS
manager: wkwok
ms.service: cognitive-services
ms.component: linguistic-analysis
ms.topic: article
ms.date: 06/30/2016
ms.author: lesun
ms.openlocfilehash: 3fc243a0da77c5bae9009929f2b82e1353347752
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346925"
---
# <a name="analyzers-method"></a>Analizatory — metoda

**Analizatorów** interfejsu API REST zawiera listę analizatorów obecnie obsługiwane przez usługę.
Odpowiedź zawiera ich [nazwy](Analyzer-Names.md) i języków obsługiwanych przez każdą (na przykład "en" w języku angielskim).

## <a name="request-parameters"></a>Parametry żądania
Brak

<br>

## <a name="response-parameters"></a>Parametry odpowiedzi
Name (Nazwa) | Typ | Opis
-----|------|--------------
Języki | Lista ciągów | Lista dwóch kodów litery ISO języka, dla których można użyć tego analizatora.
id   | ciąg | Unikatowy identyfikator dla tego analizatora
rodzaj | ciąg | szerokie typu analizatora tutaj
Specyfikacja | ciąg | Nazwa specyfikacji używany dla tego analizatora
Implementacja | ciąg | Opis modelu i/lub algorytm za tego analizatora

<br>
## <a name="example"></a>Przykład
Pobierz /analyzers

Odpowiedź: JSON
```json
[
    {
        "id": "22A6B758-420F-4745-8A3C-46835A67C0D2",
        "languages": ["en"],
        "kind": "Constituency_Tree",  
        "specification": "PennTreebank3", 
        "implementation": "SplitMerge"
    }, 
    {
        "id" : "4FA79AF1-F22C-408D-98BB-B7D7AEEF7F04",
        "languages": ["en"],
        "kind": "POS_Tags", 
        "specification": "PennTreebank3", 
        "implementation": "cmm"
    },
    {
        "id" : "08EA174B-BFDB-4E64-987E-602F85DA7F72",
        "languages": ["en"],
        "kind": "Tokens", 
        "specification":"PennTreebank3", 
        "implementation": "regexes"
    } 
]
```
