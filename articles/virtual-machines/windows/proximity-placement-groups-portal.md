---
title: Tworzenie grupy umieszczania w sąsiedztwie przy użyciu portalu
description: Dowiedz się, jak utworzyć grupę umieszczania w sąsiedztwie przy użyciu Azure Portal.
services: virtual-machines
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: cynthn
ms.openlocfilehash: 8512d9b701242dc686d49bbe56e8a2059f14ee3d
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73180253"
---
# <a name="create-a-proximity-placement-group-using-the-portal"></a>Tworzenie grupy umieszczania w sąsiedztwie przy użyciu portalu

Aby zapewnić, że maszyny wirtualne będą możliwie jak najbliżej, osiągając najniższe możliwe opóźnienie, należy wdrożyć je w obrębie [grupy umieszczania sąsiedztwa](co-location.md#proximity-placement-groups).

Grupa umieszczania bliskości jest grupą logiczną używaną w celu upewnienia się, że zasoby obliczeniowe platformy Azure znajdują się fizycznie blisko siebie. Grupy umieszczania zbliżeniowe są przydatne w przypadku obciążeń, w których jest wymagane małe opóźnienia.


## <a name="create-the-proximity-placement-group"></a>Tworzenie grupy umieszczania zbliżeniowe

1. W polu wyszukiwania wpisz **bliską grupę umieszczania** .
1. W obszarze **usługi** w wynikach wyszukiwania wybierz pozycję **grupy położenia zbliżeniowe**.
1. Na stronie **grupy umieszczania sąsiedztwa** wybierz pozycję **Dodaj**.
1. Na karcie **podstawy** w obszarze **szczegóły projektu**upewnij się, że wybrano poprawną subskrypcję.
1. W obszarze **Grupa zasobów** wybierz pozycję **Utwórz nową** , aby utworzyć nową grupę, lub wybierz istniejącą grupę zasobów z listy rozwijanej.
1. W **obszarze region** wybierz lokalizację, w której ma zostać utworzona grupa umieszczania sąsiedztwa.
1. W polu **Nazwa grupy położenia zbliżeniowe** wpisz nazwę, a następnie wybierz pozycję **Przegląd + Utwórz**.
1. Po zakończeniu walidacji wybierz pozycję **Utwórz** , aby utworzyć grupę umieszczania zbliżeniowe.

    ![Zrzut ekranu przedstawiający tworzenie grupy umieszczania zbliżeniowe](./media/ppg/ppg.png)


## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

1. Podczas tworzenia maszyny wirtualnej w portalu przejdź do karty **Zaawansowane** . 
1. W wybranej **grupie umieszczania sąsiedztwa** Wybierz poprawną grupę umieszczania. 

    ![Zrzut ekranu przedstawiający sekcję Grupa umieszczania sąsiedztwa podczas tworzenia nowej maszyny wirtualnej w portalu](./media/ppg/vm-ppg.png)

1. Po zakończeniu wprowadzania wszystkich innych wymaganych opcji wybierz pozycję **Przegląd + Utwórz**.
1. Po przeprowadzeniu walidacji wybierz pozycję **Utwórz** , aby wdrożyć maszynę wirtualną w grupie umieszczania.




## <a name="next-steps"></a>Następne kroki

Można również użyć [Azure PowerShell](proximity-placement-groups.md) do tworzenia grup umieszczania sąsiedztwa.

