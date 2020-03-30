---
title: Tworzenie grupy miejsc docelowych zbliżeniowych za pomocą portalu
description: Dowiedz się, jak utworzyć grupę miejsc docelowych zbliżeniowych za pomocą witryny Azure Portal.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73180253"
---
# <a name="create-a-proximity-placement-group-using-the-portal"></a>Tworzenie grupy miejsc docelowych zbliżeniowych za pomocą portalu

Aby maszyny wirtualne były jak najbliżej, osiągając możliwie najniższe opóźnienie, należy wdrożyć je w [grupie miejsc docelowych w pobliżu](co-location.md#proximity-placement-groups).

Grupa miejsc docelowych zbliżeniowych jest logicznym grupowaniem używanym w celu upewnienia się, że zasoby obliczeniowe platformy Azure są fizycznie zlokalizowane blisko siebie. Grupy miejsc docelowych zbliżeniowych są przydatne w przypadku obciążeń, w których wymagane jest małe opóźnienia.


## <a name="create-the-proximity-placement-group"></a>Tworzenie grupy miejsc docelowych zbliżeniowych

1. Wpisz **grupę miejsc docelowych zbliżeniowych** w wyszukiwaniu.
1. W obszarze **Usługi** w wynikach wyszukiwania wybierz pozycję **Grupy miejsc docelowych zbliżeniowych**.
1. Na stronie **Grupy miejsc docelowych zbliżeniowych** wybierz pozycję **Dodaj**.
1. Na karcie **Podstawy** w obszarze **Szczegóły projektu**upewnij się, że wybrano poprawną subskrypcję.
1. W **grupie zasobów** wybierz pozycję **Utwórz nową,** aby utworzyć nową grupę, lub wybierz istniejącą grupę zasobów z listy rozwijanej.
1. W **obszarze region** wybierz lokalizację, w której ma zostać utworzona grupa miejsc docelowych zbliżeniowych.
1. W **obszarze Nazwa grupy umieszczania zbliżeniowego** wpisz nazwę, a następnie wybierz pozycję **Przejrzyj + utwórz**.
1. Po przejściu weryfikacji wybierz pozycję **Utwórz,** aby utworzyć grupę miejsc docelowych zbliżeniowych.

    ![Zrzut ekranu przedstawiający tworzenie grupy miejsc docelowych zbliżeniowych](./media/ppg/ppg.png)


## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

1. Podczas tworzenia maszyny Wirtualnej w portalu przejdź do karty **Zaawansowane.** 
1. W **zaznaczeniu grupy miejsc docelowych zbliżeniowych** wybierz właściwą grupę miejsc docelowych. 

    ![Zrzut ekranu przedstawiający sekcję grupy miejsc docelowych zbliżeniowych podczas tworzenia nowej maszyny wirtualnej w portalu](./media/ppg/vm-ppg.png)

1. Po zakończeniu dokonywania wszystkich innych wymaganych wyborów wybierz pozycję **Przejrzyj + utwórz**.
1. Po przejściu sprawdzania poprawności wybierz pozycję **Utwórz,** aby wdrożyć maszynę wirtualną w grupie miejsc docelowych.




## <a name="next-steps"></a>Następne kroki

Można również użyć [programu Azure PowerShell](proximity-placement-groups.md) do tworzenia grup miejsc docelowych zbliżeniowych.

