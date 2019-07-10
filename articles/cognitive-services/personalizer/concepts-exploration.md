---
title: Eksploracja - Personalizer
titleSuffix: Azure Cognitive Services
description: Z badań Personalizer jest w stanie kontynuować dostarczanie dobre wyniki nawet przy wzroście zmiany zachowania użytkowników. Wybranie ustawienia Eksploracja jest to decyzja biznesowa o część interakcji użytkownika, aby zapoznać się z, w celu ulepszenia modelu.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: edjez
ms.openlocfilehash: ebb59b6bb7c36f4558b2bd63d2d55fa95823c4c3
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722466"
---
# <a name="exploration-and-exploitation"></a>Eksploracja i eksploatacji

Z badań Personalizer jest w stanie kontynuować dostarczanie dobre wyniki nawet przy wzroście zmiany zachowania użytkowników.

Gdy Personalizer odbierze rangi wywołanie, zwraca RewardActionID, albo:
* Używa eksploatacji w celu dopasowania najbardziej prawdopodobne zachowania użytkowników oparte na bieżącym modelu uczenia maszynowego.
* Używa eksploracji, który nie jest zgodny z akcji, która ma największe prawdopodobieństwo w liczbie wymiarów.

<!--
Returning the most probable action is called *exploit* behavior. Returning a different action is called *exploration*.
-->
Personalizer aktualnie używa algorytmu o nazwie *epsilon zachłanne* do zbadania. 

## <a name="choosing-an-exploration-setting"></a>Wybierając ustawienie eksploracji

Można skonfigurować procent ruchu sieciowego na potrzeby eksploracji w witrynie Azure portal **ustawienia** strona Personalizer. To ustawienie określa procent rangi wywołań, wykonujących eksploracji. 

Personalizer Określa, czy eksplorować lub wykorzystać z tym prawdopodobieństwem przy każdym wywołaniu rangi. To różni się od zachowania w niektórych A / B struktur, które blokują traktowania na określone identyfikatory użytkowników.

## <a name="best-practices-for-choosing-an-exploration-setting"></a>Najlepsze rozwiązania dotyczące wybierania ustawienie eksploracji

<!--
@edjez - you say what not to do, but make no recommendations of what **to** do. 
-->

Wybranie ustawienia Eksploracja jest to decyzja biznesowa o część interakcji użytkownika, aby zapoznać się z, w celu ulepszenia modelu. 

Ustawienie wartości zero spowoduje negate — wiele z zalet Personalizer. To ustawienie, Personalizer używa nie interakcji użytkownika do odnajdywania lepszej interakcji użytkownika. Prowadzi to do modelu stagnację, odejściem od tego stanu i ostatecznie obniżenia wydajności.

To ustawienie, które jest zbyt duża będą negate — zalety learning od zachowania użytkowników. Ustawienie go na 100% oznacza stałej losowe i wszelkich nauczony zachowań użytkowników nie może mieć wpływ na wynik.

Jest ważne, nie należy zmieniać zachowanie aplikacji, w zależności od tego, czy widzisz Personalizer Eksplorowanie czy jest wykorzystanie. Może to prowadzić do uczenia się odchyleń, które ostatecznie zmniejszy się potencjalnej wydajności.

## <a name="next-steps"></a>Kolejne kroki

[Uczenia przez wzmacnianie](concepts-reinforcement-learning.md) 