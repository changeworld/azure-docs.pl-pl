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
ms.openlocfilehash: dae56e05f01e83f05e75fdf378c0c50679d18728
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/24/2018
ms.locfileid: "42820127"
---
Aby dowiedzieć się, zwraca endpoint prognoz usługi LUIS, należy wyświetlić wyników prognoz w przeglądarce sieci web. Aby zbadać publiczne aplikację, konieczne będzie własny klucz i identyfikator aplikacji. Identyfikator publiczny aplikacji IoT `df67dcdb-c37d-46af-88e1-8b97951ca1c2`, jest dostarczana jako część adresu URL w pierwszym kroku.

Format adresu URL dla **UZYSKAĆ** żądania punktu końcowego jest:

```JSON
https://<region>.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<YOUR-KEY>&q=<user-utterance>
```

1. Punkt końcowy aplikacji IoT publicznej jest w następującym formacie: `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=<YOUR_KEY>&q=turn on the bedroom light`

    Skopiuj adres URL i Zastąp klucz dla wartości `<YOUR_KEY>`.

2. Wklej adres URL w oknie przeglądarki, a następnie naciśnij klawisz Enter. W przeglądarce pojawi się wynik JSON, który wskazuje, że usługa LUIS wykrywa `HomeAutomation.TurnOn` intencji jako najważniejsze intencji i `HomeAutomation.Room` jednostki z wartością `bedroom`.

    ```JSON
    {
      "query": "turn on the bedroom light",
      "topScoringIntent": {
        "intent": "HomeAutomation.TurnOn",
        "score": 0.809439957
      },
      "entities": [
        {
          "entity": "bedroom",
          "type": "HomeAutomation.Room",
          "startIndex": 12,
          "endIndex": 18,
          "score": 0.8065475
        }
      ]
    }
    ```

3. Zmień wartość parametru `q=` w adresie URL na wartość `turn off the living room light`, a następnie naciśnij klawisz Enter. Wynik teraz oznacza, że usługa LUIS `HomeAutomation.TurnOff` intencji jako najważniejsze intencji i `HomeAutomation.Room` jednostki z wartością `living room`. 

    ```JSON
    {
      "query": "turn off the living room light",
      "topScoringIntent": {
        "intent": "HomeAutomation.TurnOff",
        "score": 0.984057844
      },
      "entities": [
        {
          "entity": "living room",
          "type": "HomeAutomation.Room",
          "startIndex": 13,
          "endIndex": 23,
          "score": 0.9619945
        }
      ]
    }
    ```
