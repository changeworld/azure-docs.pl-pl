---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: 07e7cc991f127bf4bb4f466c0108962786e45bce
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68423577"
---
Zwróć uwagę na suwak **Próg prawdopodobieństwa** w lewym okienku karty **Wydajność.** Jest to poziom zaufania, że przewidywanie musi mieć, aby uznać za poprawne (do celów obliczania precyzji i odwołania). 

Podczas interpretowania wywołań przewidywanie z wysokim progiem prawdopodobieństwa, mają tendencję&mdash;do zwracania wyników z dużą precyzją kosztem odwołania wykryte klasyfikacje są poprawne, ale wiele pozostaje niewykryte. Próg niskiego prawdopodobieństwa&mdash;powoduje, że większość rzeczywistych klasyfikacji jest wykrywana, ale w ramach tego zestawu jest więcej fałszywych alarmów. Mając to na uwadze, należy ustawić próg prawdopodobieństwa zgodnie z określonymi potrzebami projektu. Później, gdy otrzymujesz wyniki prognozowania po stronie klienta, należy użyć tej samej wartości progu prawdopodobieństwa, jak użyto w tym miejscu.