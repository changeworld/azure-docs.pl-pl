---
title: Przepływ sterowania uczeń konwersacji - kognitywnych usług firmy Microsoft | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Więcej informacji na temat przepływu sterowania uczeń konwersacji.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 592ca82db7e0ab3ff89d25b61f38f8b226f3bc86
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348573"
---
## <a name="control-flow"></a>Przepływ sterowania

W tym dokumencie opisano przepływ sterowania uczeń konwersacji (CL) wyświetlaną w poniżej diagramu.

![](media/controlflow.PNG)

1. Użytkownik wprowadza termin lub frazę w bot, na przykład "co to jest pogody w Seattle?"
1. CL przekazuje dane wejściowe użytkownika modelu uczenia maszynowego, która wyodrębnia jednostek
    - Ten model jest kompilowane przez uczeń konwersacji i obsługiwanych przez www.luis.ai
2. Wszelkie wyodrębnione jednostek, a użytkownik wejściowych tekstu, są przekazywane do metody wywołania zwrotnego wykrywania jednostki w kodzie bot.
    - Ten kod może wartości zestawu/clear/manipulowania jednostki
1. Dane wyjściowe wyodrębniania jednostki i dane wejściowe użytkownika oraz wyniki wszystkich akcji określonych w bot bierze sieci neuronowej CL
    - W tym przykładzie najwyższy akcji prawdopodobieństwo jest zapewnienie prognozie pogody:

![](media/controlflow_forecast.PNG)

5. Wybranej akcji, w tym przypadku wymaga wywołania interfejsu API można pobrać prognozie pogody. 
6. Ten interfejs API, który został zarejestrowany przy użyciu CL. Następnie wywoływana jest metoda AddAPICallback.  Wynik tego interfejsu API jest następnie zwracany do użytkownika jako wiadomość — na przykład Sunny z wysokim 67.
7. Następnie jest wykonywane wywołanie do sieci neuronowej ustalenie następnej akcji w poprzednim kroku.
8. Sieci neuronowej następnie prognozuje dalej zestaw możliwych działań, a wybranej akcji jest udostępniana do użytkownika, w tym przypadku "Inaczej?"

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Jak uczy z uczeń konwersacji ](./how-to-teach-cl.md)
