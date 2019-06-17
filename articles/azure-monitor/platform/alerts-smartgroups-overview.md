---
title: Grupy inteligentne
description: Inteligentne grup czy agregacji alerty ułatwiające ograniczenia liczby niepotrzebnych alertów
author: anantr
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: anantr
ms.subservice: alerts
ms.openlocfilehash: e0bef0fc4f4b61add24c243af0dac64933ad5bab
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60346340"
---
# <a name="smart-groups"></a>Grupy inteligentne
Typowe wyzwanie sterowaną obsłudze alertów jest ignorowały szumu, aby sprawdzić faktycznie rzeczach — inteligentne grupy mają na celu rozwiązanie tego problemu.  

Inteligentne grupy są tworzone automatycznie za pomocą algorytmów uczenia maszynowego, aby połączyć powiązanych alertów, które reprezentują pojedynczego problemu.  Po utworzeniu alertu algorytm dodaje go do nowej grupy inteligentne lub istniejącej grupy inteligentne w oparciu o informacje, takie jak historyczne wzorce, podobne właściwości i podobną strukturę. Na przykład, jeśli % wykorzystania procesora CPU na wielu maszynach wirtualnych w ramach subskrypcji gwałtowne wzrosty jednocześnie, co prowadzi do wielu poszczególnych alertów, a sugerujące, jeśli takie alerty wystąpiły ze sobą w dowolnym momencie w przeszłości te alerty będą prawdopodobnie pogrupowane w jednej grupie inteligentnych, a Typowe potencjalną główną przyczynę. Oznacza to, że niepowołanym Rozwiązywanie problemów z alertami, inteligentne grup nie tylko umożliwia im szumu dzięki zarządzaniu powiązanych alertów jako pojedyncza jednostka zagregowane, jego również przeprowadzi kierunku możliwości typowych głównych przyczyn ich alertów.

Obecnie algorytm uwzględnia alertów z tej samej usługi monitor w ramach subskrypcji. Inteligentne grup może zmniejszyć do 99% liczby niepotrzebnych alertów za pośrednictwem dzięki konsolidacji. Można wyświetlić przyczynę, że alerty zostały uwzględnione w grupie na stronie szczegółów grupy inteligentne.

Możesz wyświetlić szczegóły grupy inteligentne i Ustaw stan podobnie jak można się z alertami. Każdy alert jest elementem członkowskim grupy jeden i tylko jeden inteligentne. 

## <a name="smart-group-state"></a>Stan grupy inteligentne
Stan grupy inteligentne jest koncepcja podobne do stanu alertu, co pozwala na zarządzanie procesem rozwiązania na poziomie grupy inteligentne. Podobnie jak w stan alertu, gdy tworzona jest grupa inteligentne, ma ona **New** stanu, który może zostać zmieniony na opcję **potwierdzono** lub **zamknięte**.

Obsługiwane są następujące stany inteligentne grupy.

| Stan | Opis |
|:---|:---|
| Nowa | Problem został wykryty i jeszcze nie została sprawdzona. |
| Potwierdzone | Administrator ma przejrzane grupy inteligentne i do pracy w niej. |
| Zamknięte | Problem został rozwiązany. Po zamknięciu inteligentne grupy możesz otworzyć go ponownie, zmieniając go do innego stanu. |

[Dowiedz się, jak można zmienić stanu grupy inteligentne.](https://aka.ms/managing-alert-smart-group-states)

> [!NOTE]
>  Zmiana stanu inteligentne grupy nie powoduje zmiany stanu alerty poszczególnych elementów członkowskich.

## <a name="smart-group-details-page"></a>Strona szczegółów grupy inteligentne

Po wybraniu grupy inteligentne, zostanie wyświetlona strona szczegółów grupy inteligentne. Zapewnia szczegółowe informacje o inteligentne grupy, w tym przyczyny, dla których został użyty do utworzenia grupy, która umożliwia zmianę stanu.
 
![Szczegóły grupy inteligentne](media/alerts-smartgroups-overview/smart-group-detail.png)


Strona szczegółów grupy inteligentnych zawiera następujące sekcje.

| `Section` | Opis |
|:---|:---|
| Alerty | Zawiera listę poszczególnych alertów, które znajdują się w grupie inteligentne. Wybierz alert, aby otworzyć jego stronę szczegółów alertu. |
| Historia | Wyświetla listę każdej akcji podjętej przez inteligentny grupy i wszelkie zmiany, które zostały wprowadzone. To jest obecnie ograniczona do zmiany stanu i zmiany członkostwa alertu. |

## <a name="smart-group-taxonomy"></a>Inteligentne grupy ani Taksonomia

Nazwa grupy inteligentne nazywa się jej pierwszy alert. Nie można utworzyć lub zmienić nazwę grupy inteligentne.

## <a name="next-steps"></a>Kolejne kroki

- [Zarządzanie grupami inteligentne](https://aka.ms/managing-smart-groups)
- [Zmień swój stan alertu i inteligentne grupy](https://aka.ms/managing-alert-smart-group-states)


