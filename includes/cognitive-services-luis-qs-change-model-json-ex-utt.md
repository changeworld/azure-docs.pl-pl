---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: luis
ms.topic: include
ms.custom: include file
ms.date: 08/16/2018
ms.author: diberry
ms.openlocfilehash: a1b0afce31d7202c38b049addf546350ff347719
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47044172"
---
Przykładowy plik wypowiedzi **utterances.json** ma określony format. 

Pole `text` zawiera tekst przykładowej wypowiedzi. Pole `intentName` musi odpowiadać nazwie istniejącej wypowiedzi w aplikacji LUIS. Pole `entityLabels` jest wymagane. Jeśli nie chcesz dodawać etykiet do żadnych jednostek, podaj pustą tablicę.

Jeśli tablica entityLabels nie jest pusta, elementy `startCharIndex` i `endCharIndex` muszą oznaczać jednostkę określoną w polu `entityName`. Indeks jest liczony od zera, co oznacza, że wartość 6 w pierwszym przykładzie odwołuje się do litery „S” wyrazu Seattle, a nie do spacji przed wielką literą S. Jeśli etykieta w tekście będzie rozpoczynać się lub kończyć spacją, wywołanie interfejsu API w celu dodania wypowiedzi nie powiedzie się.

```JSON
[
  {
    "text": "go to Seattle today",
    "intentName": "BookFlight",
    "entityLabels": [
      {
        "entityName": "Location::LocationTo",
        "startCharIndex": 6,
        "endCharIndex": 12
      }
    ]
  },
  {
    "text": "purple dogs are difficult to work with",
    "intentName": "BookFlight",
    "entityLabels": []
  }
]
```