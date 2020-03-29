---
title: Eksploracja - Personalizer
titleSuffix: Azure Cognitive Services
description: Dzięki eksploracji Personalizer jest w stanie nadal dostarczać dobre wyniki, nawet w miarę zmian zachowania użytkownika. Wybór ustawienia eksploracji jest decyzja biznesowa o proporcji interakcji użytkownika do zbadania z, w celu poprawy modelu.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: diberry
ms.openlocfilehash: 0b69c1fb070431ad61858322dce461f6496c35d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73490809"
---
# <a name="exploration-and-exploitation"></a>Eksploracja i eksploatacja

Dzięki eksploracji Personalizer jest w stanie nadal dostarczać dobre wyniki, nawet w miarę zmian zachowania użytkownika.

Gdy Personalizer odbiera wywołanie rangi, zwraca znak RewardActionID, który:
* Używa wykorzystania, aby dopasować najbardziej prawdopodobne zachowanie użytkownika na podstawie bieżącego modelu uczenia maszynowego.
* Używa eksploracji, która nie pasuje do akcji, która ma największe prawdopodobieństwo w randze.

Personalizer obecnie używa algorytmu o nazwie *epsilon chciwy* do zbadania. 

## <a name="choosing-an-exploration-setting"></a>Wybieranie ustawienia eksploracji

Skonfiguruj procent ruchu do użycia do eksploracji na stronie **konfiguracji witryny** azure portal dla personalizatora. To ustawienie określa procent wywołań rangi, które wykonują eksplorację. 

Personalizator określa, czy do zbadania lub wykorzystać z tym prawdopodobieństwem na każde wywołanie rangi. Jest to inne niż zachowanie w niektórych strukturach A/B, które blokują leczenie na określonych identyfikatorach użytkowników.

## <a name="best-practices-for-choosing-an-exploration-setting"></a>Najważniejsze wskazówki dotyczące wyboru ustawienia eksploracji

Wybór ustawienia eksploracji jest decyzja biznesowa o proporcji interakcji użytkownika do zbadania z, w celu poprawy modelu. 

Ustawienie zero zanegować wiele korzyści z Personalizer. Przy tym ustawieniu Personalizer nie używa żadnych interakcji z użytkownikiem, aby odkryć lepsze interakcje z użytkownikiem. Prowadzi to do stagnacji modelu, dryfu i ostatecznie niższej wydajności.

Ustawienie, które jest zbyt wysokie, zanegować korzyści wynikające z uczenia się z zachowania użytkownika. Ustawienie go na 100% oznacza stałą randomizację, a wszelkie wyuczone zachowanie użytkowników nie miałoby wpływu na wynik.

Ważne jest, aby nie zmieniać zachowania aplikacji na podstawie tego, czy zobaczysz, czy Personalizer eksploruje lub wykorzystuje. Doprowadziłoby to do uprzedzeń w nauce, które ostatecznie zmniejszyłyby potencjalną wydajność.

## <a name="next-steps"></a>Następne kroki

[Uczenie przez wzmacnianie](concepts-reinforcement-learning.md) 