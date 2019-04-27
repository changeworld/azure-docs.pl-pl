---
title: Przechodzenie do trybu failover i powrót po awarii maszyn wirtualnych VMware i serwerów fizycznych podczas odzyskiwania po awarii na platformie Azure za pomocą usługi Site Recovery | Microsoft Docs
description: Dowiedz się, jak wprowadzić maszyny wirtualne VMware i fizyczne serwery w tryb failover na platformie Azure i przywrócić je po awarii do lokacji lokalnej podczas odzyskiwania po awarii na platformie Azure za pomocą usługi Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 9206e751fadab7a09c696fbe262aecdde002ae74
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60565739"
---
# <a name="fail-over-and-fail-back-vmware-vms"></a>Tryb failover i powrót po awarii maszyn wirtualnych VMware

W tym artykule opisano sposób awaryjnego przełączania lokalnych zasobów programu VMware maszyny Wirtualnej na platformie Azure [usługi Azure Site Recovery](site-recovery-overview.md) usługi. 

Jest to piąty samouczek z serii, która pokazuje, jak skonfigurować odzyskiwanie po awarii na platformie Azure dla maszyn lokalnych.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Sprawdzanie właściwości maszyny VMware pod kątem zgodności z wymaganiami platformy Azure
> * Przełączanie do trybu failover na platformie Azure


> [!NOTE]
> W samouczkach pokazano to najprostsza ścieżka wdrażania scenariusza. Jeśli to możliwe, używają opcji domyślnych i nie przedstawiają wszystkich możliwych ustawień i ścieżek. Jeśli chcesz dowiedzieć się więcej o trybie failover szczegółowo [zapoznaj się z tym artykułem](site-recovery-failover.md).

## <a name="before-you-start"></a>Przed rozpoczęciem
Wykonaj poprzednich samouczków:

1. Upewnij się, że masz [Konfigurowanie platformy Azure](tutorial-prepare-azure.md) do odzyskiwania po awarii lokalnych maszyn wirtualnych VMware, maszyny wirtualne funkcji Hyper-V i maszyn fizycznych do platformy Azure.
2. Przygotowywanie lokalnej [VMware](vmware-azure-tutorial-prepare-on-premises.md) lub [funkcji Hyper-V](hyper-v-prepare-on-premises-tutorial.md) środowiska na potrzeby odzyskiwania po awarii. Jeśli podczas konfigurowania odzyskiwania po awarii dla serwerów fizycznych, zapoznaj się z [macierz obsługi](vmware-physical-secondary-support-matrix.md).
3. Konfigurowanie odzyskiwania po awarii dla [maszyny wirtualne VMware](vmware-azure-tutorial.md), [maszyn wirtualnych funkcji Hyper-V](hyper-v-azure-tutorial.md), lub [maszyn fizycznych](physical-azure-disaster-recovery.md).
4. Uruchom [próbnego odzyskiwania po awarii](tutorial-dr-drill-azure.md) aby upewnić się, że wszystko działa zgodnie z oczekiwaniami.


## <a name="failover-and-failback"></a>Praca w trybie failover i powrót po awarii

Przełączanie do trybu failover i powrót po awarii odbywa się w czterech etapach:

1. **Przełączenie do trybu failover na platformie Azure**: Gdy ulegnie awarii lokacji podstawowej w środowisku lokalnym, pracy awaryjnej maszyn na platformę Azure. Po przejściu w tryb failover maszyn wirtualnych platformy Azure są tworzone z replikowanych danych.
2. **Ponowne włączanie ochrony maszyn wirtualnych na platformie Azure**: Na platformie Azure ponownie włączyć ochronę maszyny wirtualne Azure, aby rozpoczynały się replikację maszyn wirtualnych programu VMware w środowisku lokalnym. Lokalna maszyna wirtualna jest wyłączona podczas ponownego włączania ochrony, aby pomóc w zapewnieniu spójności danych.
3. **Przełączenie do trybu failover w lokacji lokalnej**: Gdy działa z lokacją lokalną i uruchomiona, uruchom tryb failover, powrót po awarii z platformy Azure.
4. **Ponowne włączanie ochrony lokalnych maszyn wirtualnych**: Po danych ma powrót po awarii, ponowne włączanie ochrony lokalnych maszyn wirtualnych, do których użytkownik powrót po awarii, w tak, aby rozpoczęcia replikacji do platformy Azure.

## <a name="verify-vm-properties"></a>Sprawdzanie właściwości maszyn wirtualnych

Przed uruchomieniem trybu failover Sprawdź właściwości maszyny Wirtualnej i upewnij się, że maszyny wirtualne są zgodne z [wymagania dotyczące usługi Azure](vmware-physical-azure-support-matrix.md#replicated-machines).

Sprawdź właściwości w następujący sposób:

1. W obszarze **Chronione elementy** kliknij kolejno pozycje **Zreplikowane elementy** > Maszyna wirtualna.

2. Okienko **Zreplikowany element** zawiera podsumowanie informacji na temat maszyny wirtualnej, jej kondycję oraz najnowsze dostępne punkty odzyskiwania. Kliknij przycisk **Właściwości**, aby wyświetlić więcej szczegółów.

3. W obszarze **Obliczenia i sieć** można zmodyfikować nazwę platformy Azure, grupę zasobów, rozmiar docelowy, [zestaw dostępności](../virtual-machines/windows/tutorial-availability-sets.md) i ustawienia dysku zarządzanego

4. Możesz wyświetlać i modyfikować ustawienia sieciowe, w tym sieć/podsieć, w której zlokalizowana będzie maszyna wirtualna na platformie Azure po wejściu w tryb failover, oraz adres IP, który będzie do niej przypisany.

5. W obszarze **Dyski** można znaleźć informacje dotyczące systemu operacyjnego i dysków danych maszyny wirtualnej.

## <a name="run-a-failover-to-azure"></a>Przełączanie do trybu failover na platformie Azure

1. W obszarze **Ustawienia** > **Zreplikowane elementy** kliknij kolejno pozycje maszyna wirtualna > **Tryb failover**.
2. W obszarze **Tryb failover** wybierz **Punkt odzyskiwania**, do którego przełączenie w tryb failover ma zostać wykonane. Możesz użyć jednej z następujących opcji:
   - **Najnowszy**: ta opcja najpierw przetwarza wszystkie dane wysyłane do usługi Site Recovery. Zapewnia najniższą wartość celu puntu odzyskiwania, ponieważ maszyna wirtualna platformy Azure utworzona po przejściu do trybu failover zawiera wszystkie dane, które zostały zreplikowane w usłudze Site Recovery do momentu włączenia trybu failover.
   - **Najnowszy przetworzony**: Ta opcja wprowadza maszynę Wirtualną za pośrednictwem do najnowszego punktu odzyskiwania przetworzonego przez usługę Site Recovery. Ta opcja zapewnia niską wartość celu czasu odzyskiwania, ponieważ nie wymaga przetwarzania nieprzetworzonych danych.
   - **Najnowszy spójny na poziomie aplikacji**: ta opcja wprowadza maszynę wirtualną w tryb failover z użyciem najnowszego punktu odzyskiwania przetworzonego przez usługę Site Recovery.
   - **Niestandardowy**: umożliwia określenie punktu odzyskiwania.

3. Wybierz **Zamknij maszynę przed rozpoczęciem pracy awaryjnej** ma spróbować przeprowadzić zamknięcie źródłowych maszyn wirtualnych przed wyzwoleniem trybu failover. Przełączanie do trybu failover będzie kontynuowane, nawet jeśli zamknięcie nie powiedzie się. Na stronie **Zadania** można śledzić postęp trybu failover.

W niektórych scenariuszach tryb failover wymaga dodatkowego przetwarzania, którego przeprowadzenie zajmuje około 8–10 minut. Dłuższy czas testu trybu failover dla:
- Uruchomiona wersja usługi mobilności starsze niż 9,8 maszyn wirtualnych VMware
- Serwerów fizycznych
- Maszyny wirtualne VMware z systemem Linux
- Maszyny wirtualne funkcji Hyper-V chronionych jako serwerów fizycznych
- Maszyny wirtualne VMware, które nie mają usługę DHCP włączony
- Maszyny wirtualne VMware, które nie mają następujących sterowników rozruchowych: storvsc, vmbus, storflt, intelide, atapi.

> [!WARNING]
> **Nie anuluj trybu failover, który jest w toku**: Przed rozpoczęciem pracy w trybie failover zatrzymywana jest replikacja maszyny wirtualnej. Jeśli anulujesz tryb failover po rozpoczęciu przełączania, zostanie ono zatrzymane, ale maszyna wirtualna nie zostanie ponownie zreplikowana.

## <a name="connect-to-failed-over-vm"></a>Połączyć się z przełączone w tryb failover maszyny Wirtualnej

1. Jeśli chcesz nawiązać połączenie przy użyciu protokołu RDP/SSH po przejściu w tryb failover maszyn wirtualnych platformy Azure [Sprawdź te wymagania](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).
2. Po włączeniu trybu failover, przejdź do maszyny Wirtualnej, a następnie zweryfikuj, [łączenie](../virtual-machines/windows/connect-logon.md) do niego.
3. Użyj **Zmień punkt odzyskiwania** Jeśli chcesz użyć innego punktu odzyskiwania po włączeniu trybu failover. Po zatwierdzeniu trybu failover w następnym kroku tej opcji nie będzie dostępne.
4. Po zakończeniu walidacji wybierz polecenie **zatwierdzić** Aby sfinalizować punktu odzyskiwania maszyny wirtualnej po włączeniu trybu failover.
5. Po zatwierdzeniu zostaną usunięte wszystkie inne dostępne punkty odzyskiwania. Na tym kończy się trybu failover.

>[!TIP]
> Jeśli wystąpią problemy z łącznością po włączeniu trybu failover, postępuj zgodnie z tym [przewodnik rozwiązywania problemów z](site-recovery-failover-to-azure-troubleshoot.md).

## <a name="next-steps"></a>Kolejne kroki

Po przejściu w tryb failover należy ponownie włączyć ochronę maszyn wirtualnych platformy Azure do środowiska lokalnego. Następnie po maszyny wirtualne znajdują się ponownie i replikowane do lokacji lokalnej, awarii powrót po awarii z platformy Azure, gdy wszystko będzie gotowe.

> [!div class="nextstepaction"]
> [Ponowne włączanie ochrony maszyn wirtualnych platformy Azure](vmware-azure-reprotect.md)
> [powrót po awarii z platformy Azure](vmware-azure-failback.md) 
