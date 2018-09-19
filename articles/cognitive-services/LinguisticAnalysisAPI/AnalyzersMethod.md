---
title: Metoda Analyzers - Lingistic interfejsu API analizy
titlesuffix: Azure Cognitive Services
description: Analizatory interfejsu API REST zawiera listę analizatorów, które są obecnie obsługiwane przez interfejsu API analizy językowej.
services: cognitive-services
author: RichardSunMS
manager: cgronlun
ms.service: cognitive-services
ms.component: linguistic-analysis
ms.topic: conceptual
ms.date: 06/30/2016
ms.author: lesun
ms.openlocfilehash: b443bbd6377f0720c8be86bbe2b7a3e8ab8cb880
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2018
ms.locfileid: "46129016"
---
# <a name="analyzers-method"></a>Metoda analyzers

**Analizatory** interfejsu API REST zawiera listę analizatory są obecnie obsługiwane przez usługę.
Odpowiedź zawiera ich [nazwy](Analyzer-Names.md) i języków obsługiwanych przez każdą (na przykład "en" w języku angielskim).

## <a name="request-parameters"></a>Parametry żądania
Brak

<br>

## <a name="response-parameters"></a>Parametrów odpowiedzi
Name (Nazwa) | Typ | Opis
-----|------|--------------
Języki | Lista ciągów | Lista dwóch kodów literę ISO języka, dla których można użyć tego analizatora.
id   | ciąg | Unikatowy identyfikator dla tego analizatora
rodzaj | ciąg | szerokie typ analizatora tutaj
Specyfikacja | ciąg | Nazwa specyfikacji używane dla tego analizatora
Implementacja | ciąg | Opis modelu i/lub algorytm, za ten analizatora

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
