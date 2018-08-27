---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: include
ms.custom: include file
ms.date: 08/16/2018
ms.author: diberry
ms.openlocfilehash: 419f15901b665b43b850922f77bd32d7aac8d3a2
ms.sourcegitcommit: ebb460ed4f1331feb56052ea84509c2d5e9bd65c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/24/2018
ms.locfileid: "42920650"
---
Przykładowy plik wypowiedzi **utterances.json**, następuje po określonym formacie. 

`text` Pole zawiera tekst wypowiedź przykładu. `intentName` Pola musi odpowiadać nazwa intencji istniejącej aplikacji usługi LUIS. Pole `entityLabels` jest wymagane. Jeśli nie chcesz dodać etykietę dowolnej jednostki, należy podać pustą tablicę.

Jeśli tablica entityLabels nie jest pusty, `startCharIndex` i `endCharIndex` należy oznaczyć jednostki określonych w `entityName` pola. Indeks jest liczony od zera, co oznacza, że 6 w górnym przykład odwołuje się do "S" z Seattle, bez spacji przed wielkiej litery S. Rozpoczyna się lub kończy się na etykiecie na spację w tekście, wywołanie interfejsu API, aby dodać wypowiedzi nie powiedzie się.

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