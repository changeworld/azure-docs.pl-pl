---
title: Conversation Learner przepływ sterowania — Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Dowiedz się więcej o przepływie sterowania Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 3ec839c1a930ffbe73989149360f1b02866a3c50
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705306"
---
## <a name="control-flow"></a>Przepływ sterowania

W tym dokumencie opisano przepływ sterowania Conversation Learner (CL), jak pokazano na poniższym diagramie.

![](media/controlflow.PNG)

1. Użytkownik wprowadza termin lub frazę w bot, na przykład "co to jest Pogoda w Seattle?".
1. CL przekazuje dane wejściowe użytkownika do modelu uczenia maszynowego, który wyodrębnia jednostki
   - Ten model jest kompilowany przez Conversation Learner i hostowany przez www.luis.ai
1. Wszystkie Wyodrębnione jednostki i tekst wejściowy użytkownika są przesyłane do metody wywołania zwrotnego wykrywania jednostki w kodzie bot.
    - Ten kod może ustawiać/czyścić wartości jednostek lub nimi manipulować
1. Sieć CL neuronowych następnie pobiera dane wyjściowe wyodrębniania jednostek oraz dane wejściowe użytkownika i ocenia wszystkie akcje zdefiniowane w bot
   - W tym przykładzie najwyższa akcja prawdopodobieństwa polega na określeniu prognozy pogody:

     ![](media/controlflow_forecast.PNG)

1. Wybrana akcja w tym przypadku wymaga wywołania interfejsu API w celu pobrania prognozy pogody. 
1. Ten interfejs API, który został zarejestrowany przy użyciu CL. Metoda addwywołania zwrotnego jest następnie wywoływana.  Wynik tego interfejsu API jest następnie zwracany użytkownikowi jako komunikat — na przykład "Sunny z wysoką 67."
1. Wywołanie jest następnie nawiązywane w sieci neuronowych, aby określić następną akcję na podstawie poprzedniego kroku.
1. Sieć neuronowych przewiduje następny zestaw możliwych akcji, a wybrana akcja jest prezentowana użytkownikowi, w tym przypadku coś innego? '.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Jak uczyć się Conversation Learner](./how-to-teach-cl.md)
