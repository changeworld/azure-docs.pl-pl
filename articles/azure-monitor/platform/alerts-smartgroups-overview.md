---
title: Grupy inteligentne
description: Inteligentne grupy to agregacje alertów, które pomagają zmniejszyć hałas alertów
ms.topic: conceptual
ms.subservice: alerts
ms.date: 05/15/2018
ms.openlocfilehash: 05b05f8bc079bb3768ac2f1a03593bc9260b41aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665497"
---
# <a name="smart-groups"></a>Grupy inteligentne

Częstym wyzwaniem, przed którym stoją w przypadku alarmów, jest przesiewanie przez hałas, aby dowiedzieć się, co faktycznie ma znaczenie - inteligentne grupy mają być rozwiązaniem tego problemu.  

Grupy inteligentne są tworzone automatycznie przy użyciu algorytmów uczenia maszynowego do łączenia powiązanych alertów, które reprezentują jeden problem.  Po utworzeniu alertu algorytm dodaje go do nowej grupy inteligentnej lub istniejącej grupy inteligentnej na podstawie informacji, takich jak wzorce historyczne, podobne właściwości i podobna struktura. Na przykład, jeśli % procesora CPU na kilku maszynach wirtualnych w ramach subskrypcji jednocześnie wzrośnie, co prowadzi do wielu pojedynczych alertów, a takie alerty wystąpiły razem w dowolnym momencie w przeszłości, alerty te zostaną prawdopodobnie zgrupowane w jedną grupę inteligentną, co sugeruje potencjalną powszechną przyczynę. Oznacza to, że dla kogoś rozwiązywania alertów, inteligentne grupy nie tylko pozwala im zmniejszyć hałas poprzez zarządzanie powiązanych alertów jako pojedynczej jednostki zagregowane, ale także prowadzi ich do możliwych typowych przyczyn ich alertów.

Obecnie algorytm uwzględnia tylko alerty z tej samej usługi monitora w ramach subskrypcji. Dzięki tej konsolidacji inteligentne grupy mogą zmniejszyć hałas alarmowy nawet o 99%. Możesz wyświetlić przyczynę, dla której alerty zostały uwzględnione w grupie na stronie szczegółów grupy inteligentnej.

Można wyświetlić szczegóły grup inteligentnych i ustawić stan podobnie jak można z alertów. Każdy alert jest członkiem jednej i tylko jednej grupy inteligentnej. 

## <a name="smart-group-state"></a>Stan inteligentnej grupy

Stan grupy inteligentnej jest podobną koncepcją do stanu alertu, która umożliwia zarządzanie procesem rozpoznawania na poziomie grupy inteligentnej. Podobnie jak w przypadku stanu alertu, po utworzeniu inteligentnej grupy ma stan **Nowy,** który można zmienić na **potwierdzone** lub **zamknięte**.

Obsługiwane są następujące stany grup inteligentnych.

| Stan | Opis |
|:---|:---|
| Nowa | Problem został właśnie wykryty i nie został jeszcze zweryfikowany. |
| Potwierdzony | Administrator przejrzał inteligentną grupę i rozpoczął nad nią pracę. |
| Zamknięte | Problem został rozwiązany. Po zamknięciu inteligentnej grupy można ją ponownie otworzyć, zmieniając ją na inny. |

[Dowiedz się, jak zmienić stan grupy inteligentnej.](https://aka.ms/managing-alert-smart-group-states)

> [!NOTE]
>  Zmiana stanu grupy inteligentnej nie zmienia stanu alertów poszczególnych członków.

## <a name="smart-group-details-page"></a>Strona szczegółów inteligentnej grupy

Strona szczegółów grupy inteligentnej jest wyświetlana po wybraniu grupy inteligentnej. Zawiera szczegółowe informacje na temat grupy inteligentnej, w tym rozumowanie, które zostało użyte do utworzenia grupy, i umożliwia zmianę jej stanu.
 
![Inteligentne szczegóły grupy](media/alerts-smartgroups-overview/smart-group-detail.png)


Strona szczegółów grupy inteligentnej zawiera następujące sekcje.

| Sekcja | Opis |
|:---|:---|
| Alerty | Wyświetla listę poszczególnych alertów, które znajdują się w grupie inteligentnej. Wybierz alert, aby otworzyć stronę szczegółów alertu. |
| Historia | Wyświetla listę każdej akcji podjętej przez grupę inteligentną i wszelkich zmian, które zostały wprowadzone do niej. Jest to obecnie ograniczone do zmian stanu i zmiany członkostwa alertów. |

## <a name="smart-group-taxonomy"></a>Taksonomia inteligentna grupa

Nazwa grupy inteligentnej jest nazwą pierwszego alertu. Nie można utworzyć ani zmienić nazwy grupy inteligentnej.

## <a name="next-steps"></a>Następne kroki

- [Zarządzanie grupami inteligentnymi](https://aka.ms/managing-smart-groups)
- [Zmienianie stanu alertu i grupy inteligentnej](https://aka.ms/managing-alert-smart-group-states)


