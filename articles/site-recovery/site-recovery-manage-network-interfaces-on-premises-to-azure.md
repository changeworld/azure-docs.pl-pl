---
title: Zarządzanie kartami sieciowymi dla lokalnego odzyskiwania po awarii za pomocą usługi Azure Site Recovery
description: W tym artykule opisano sposób zarządzania interfejsami sieciowymi dla lokalnego odzyskiwania po awarii na platformie Azure za pomocą usługi Azure Site Recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/9/2019
ms.author: mayg
ms.openlocfilehash: 2a4752b501e40f9e8a4f3bc82cb2533c11f9e526
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73954604"
---
# <a name="manage-vm-network-interfaces-for-on-premises-disaster-recovery-to-azure"></a>Zarządzanie interfejsami sieciowymi maszyn wirtualnych w celu lokalnego odzyskiwania po awarii na platformie Azure

Maszyna wirtualna (VM) na platformie Azure musi mieć co najmniej jeden interfejs sieciowy dołączony do niej. Może mieć tyle interfejsów sieciowych dołączonych do niego, jak obsługuje rozmiar maszyny Wirtualnej.

Domyślnie pierwszy interfejs sieciowy dołączony do maszyny wirtualnej platformy Azure jest zdefiniowany jako podstawowy interfejs sieciowy. Wszystkie inne interfejsy sieciowe na maszynie wirtualnej są pomocniczymi interfejsami sieciowymi. Ponadto domyślnie cały ruch wychodzący z maszyny wirtualnej jest wysyłany adres IP, który jest przypisany do podstawowej konfiguracji IP podstawowego interfejsu sieciowego.

W środowisku lokalnym maszyny wirtualne lub serwery mogą mieć wiele interfejsów sieciowych dla różnych sieci w środowisku. Różne sieci są zwykle używane do wykonywania określonych operacji, takich jak uaktualnienia, konserwacja i dostęp do Internetu. Podczas migracji lub niepowodzenia na platformie Azure ze środowiska lokalnego należy pamiętać, że interfejsy sieciowe na tej samej maszynie wirtualnej muszą być połączone z tą samą siecią wirtualną.

Domyślnie usługa Azure Site Recovery tworzy tyle interfejsów sieciowych na maszynie wirtualnej platformy Azure, ile jest połączonych z serwerem lokalnym. Można uniknąć tworzenia nadmiarowych interfejsów sieciowych podczas migracji lub pracy awaryjnej, edytując ustawienia interfejsu sieciowego w ustawieniach replikowanej maszyny wirtualnej.

## <a name="select-the-target-network"></a>Wybierz sieć docelową

W przypadku maszyn wirtualnych i fizycznych oraz maszyn wirtualnych funkcji Hyper-V (bez menedżera maszyn wirtualnych system center) można określić docelową sieć wirtualną dla poszczególnych maszyn wirtualnych. W przypadku maszyn wirtualnych funkcji Hyper-V zarządzanych za pomocą menedżera maszyn wirtualnych należy użyć [mapowania sieci](site-recovery-network-mapping.md) do mapowania sieci maszyn wirtualnych na źródłowym serwerze menedżera maszyn wirtualnych i docelowych sieciach platformy Azure.

1. W obszarze **Elementy replikowane** w przechowalni usług odzyskiwania wybierz dowolny element replikowany, aby uzyskać dostęp do ustawień tego zreplikowanego elementu.

2. Wybierz kartę **Obliczeń i Sieć,** aby uzyskać dostęp do ustawień sieci dla zreplikowanego elementu.

3. W **obszarze Właściwości sieci**wybierz sieć wirtualną z listy dostępnych interfejsów sieciowych.

    ![Ustawienia sieciowe](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/compute-and-network.png)

Modyfikowanie sieci docelowej wpływa na wszystkie interfejsy sieciowe dla tej konkretnej maszyny wirtualnej.

W przypadku chmur Menedżera maszyn wirtualnych modyfikowanie mapowania sieci ma wpływ na wszystkie maszyny wirtualne i ich interfejsy sieciowe.

## <a name="select-the-target-interface-type"></a>Wybierz typ interfejsu docelowego

W sekcji **Interfejsy sieciowe** **okienka Obliczeń i Sieci** można wyświetlać i edytować ustawienia interfejsu sieciowego. Można również określić typ docelowego interfejsu sieciowego.

- Podstawowy interfejs **sieciowy** jest wymagany do pracy awaryjnej.
- Wszystkie inne wybrane interfejsy sieciowe, jeśli istnieją, są **pomocniczymi** interfejsami sieciowymi.
- Wybierz **opcję Nie należy używać,** aby wykluczyć interfejs sieciowy z tworzenia w trybie failover.

Domyślnie po włączeniu replikacji usługa Site Recovery wybiera wszystkie wykryte interfejsy sieciowe na serwerze lokalnym. Oznacza jeden jako **podstawowy,** a wszystkie inne jako **drugorzędne**. Wszystkie kolejne interfejsy dodane na serwerze lokalnym są oznaczone **domyślnie Nie używaj.** Podczas dodawania większej liczby interfejsów sieciowych upewnij się, że wybrany jest odpowiedni rozmiar docelowy maszyny wirtualnej platformy Azure, aby pomieścić wszystkie wymagane interfejsy sieciowe.

## <a name="modify-network-interface-settings"></a>Modyfikowanie ustawień interfejsu sieciowego

Można zmodyfikować podsieć i adres IP interfejsów sieciowych replikowanego elementu. Jeśli adres IP nie zostanie określony, usługa Site Recovery przypisze następny dostępny adres IP z podsieci do interfejsu sieciowego w czasie pracy awaryjnej.

1. Wybierz dowolny dostępny interfejs sieciowy, aby otworzyć ustawienia interfejsu sieciowego.

2. Wybierz żądaną podsieć z listy dostępnych podsieci.

3. Wprowadź żądany adres IP (zgodnie z wymaganiami).

    ![Ustawienia interfejsu sieciowego](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/network-interface-settings.png)

4. Wybierz **przycisk OK,** aby zakończyć edycję i powrócić do **okienka Obliczeń i Sieci.**

5. Powtórz kroki 1-4 dla innych interfejsów sieciowych.

6. Wybierz **pozycję Zapisz,** aby zapisać wszystkie zmiany.

## <a name="next-steps"></a>Następne kroki
  [Dowiedz się więcej](../virtual-network/virtual-network-network-interface-vm.md) o interfejsach sieciowych dla maszyn wirtualnych platformy Azure.
