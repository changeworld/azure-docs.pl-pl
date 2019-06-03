---
title: Przechodzenie do trybu failover i powrót po awarii maszyn wirtualnych VMware i serwerów fizycznych podczas odzyskiwania po awarii na platformie Azure za pomocą usługi Site Recovery | Microsoft Docs
description: Dowiedz się w tryb failover maszyn wirtualnych VMware i serwerów fizycznych na platformę Azure oraz powrócić po awarii lokacji lokalnej podczas odzyskiwania po awarii na platformie Azure przy użyciu usługi Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 854e09600440c28f7505a016566ea0f05e994f5f
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66418315"
---
# <a name="fail-over-and-fail-back-vmware-vms"></a>Tryb failover i powrót po awarii maszyn wirtualnych VMware

W tym artykule opisano sposób awaryjnego przełączania maszyny wirtualnej VMware w środowisku lokalnym (VM) do [usługi Azure Site Recovery](site-recovery-overview.md).

Jest to piąty samouczek z serii, która pokazuje, jak skonfigurować odzyskiwanie po awarii na platformie Azure dla maszyn lokalnych.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Sprawdź, czy właściwości maszyny Wirtualnej VMware są zgodne z wymaganiami platformy Azure.
> * Uruchamianie trybu failover na platformie Azure.

> [!NOTE]
> W samouczkach pokazano to najprostsza ścieżka wdrażania scenariusza. Użyj opcji domyślnych, jeśli jest to możliwe, a nie pokazuj wszystkich możliwych ustawień i ścieżki. Jeśli chcesz dowiedzieć się więcej o trybie failover szczegółowo, zobacz [w tryb failover maszyn wirtualnych i serwerów fizycznych](site-recovery-failover.md).

## <a name="before-you-start"></a>Przed rozpoczęciem

Wykonaj poprzednich samouczków:

1. Upewnij się, że masz [Konfigurowanie platformy Azure](tutorial-prepare-azure.md) do odzyskiwania po awarii lokalnych maszyn wirtualnych VMware, maszyny wirtualne funkcji Hyper-V i maszyn fizycznych do platformy Azure.
2. Przygotowywanie lokalnej [VMware](vmware-azure-tutorial-prepare-on-premises.md) lub [funkcji Hyper-V](hyper-v-prepare-on-premises-tutorial.md) środowiska na potrzeby odzyskiwania po awarii. Jeśli podczas konfigurowania odzyskiwania po awarii dla serwerów fizycznych, zapoznaj się z [macierz obsługi](vmware-physical-secondary-support-matrix.md).
3. Konfigurowanie odzyskiwania po awarii dla [maszyny wirtualne VMware](vmware-azure-tutorial.md), [maszyn wirtualnych funkcji Hyper-V](hyper-v-azure-tutorial.md), lub [maszyn fizycznych](physical-azure-disaster-recovery.md).
4. Uruchom [próbnego odzyskiwania po awarii](tutorial-dr-drill-azure.md) aby upewnić się, że wszystko działa zgodnie z oczekiwaniami.

## <a name="failover-and-failback"></a>Praca w trybie failover i powrót po awarii

Przełączanie do trybu failover i powrót po awarii odbywa się w czterech etapach:

1. **Failover na platformie Azure:** Gdy ulegnie awarii lokacji podstawowej w środowisku lokalnym, pracy awaryjnej maszyn na platformę Azure. Po przejściu w tryb failover maszyn wirtualnych platformy Azure są tworzone z replikowanych danych.
2. **Ponowne włączanie ochrony maszyn wirtualnych platformy Azure:** Na platformie Azure ponownie włączyć ochronę maszyn wirtualnych platformy Azure, aby rozpoczynały się replikację maszyn wirtualnych programu VMware w środowisku lokalnym. Lokalna maszyna wirtualna jest wyłączona podczas ponownego włączania ochrony, aby pomóc w zapewnieniu spójności danych.
3. **Awaryjnie do środowiska lokalnego:** Gdy działa z lokacją lokalną i uruchomiona, uruchom tryb failover, powrót po awarii z platformy Azure.
4. **Ponowne włączanie ochrony lokalnych maszyn wirtualnych:** Po danych ma powrót po awarii, ponowne włączanie ochrony lokalnych maszyn wirtualnych, do których użytkownik powrót po awarii, w tak, aby rozpoczęcia replikacji do platformy Azure.

## <a name="verify-vm-properties"></a>Sprawdzanie właściwości maszyn wirtualnych

Przed uruchomieniem trybu failover Sprawdź właściwości maszyny Wirtualnej, aby upewnić się, że maszyny wirtualne spełniają [wymagania dotyczące usługi Azure](vmware-physical-azure-support-matrix.md#replicated-machines).

Sprawdź właściwości w następujący sposób:

1. W **chronione elementy**, wybierz opcję **zreplikowane elementy**, a następnie wybierz maszynę Wirtualną, którą chcesz zweryfikować.

2. Okienko **Zreplikowany element** zawiera podsumowanie informacji na temat maszyny wirtualnej, jej kondycję oraz najnowsze dostępne punkty odzyskiwania. Wybierz **właściwości** Aby wyświetlić więcej szczegółów.

3. W **obliczenia i sieć**, te właściwości można modyfikować zgodnie z potrzebami:
    * Nazwa platformy Azure
    * Grupa zasobów
    * Rozmiar docelowy
    * [Zestaw dostępności](../virtual-machines/windows/tutorial-availability-sets.md)
    * Ustawienia dysku zarządzanego

4. Można wyświetlać i modyfikować ustawienia sieci, w tym:

    * Sieci i podsieci, w którym maszyna wirtualna platformy Azure zostaną umieszczone po włączeniu trybu failover.
    * Adres IP, który zostanie przypisany do niego.

5. W obszarze **Dyski** można znaleźć informacje dotyczące systemu operacyjnego i dysków danych maszyny wirtualnej.

## <a name="run-a-failover-to-azure"></a>Przełączanie do trybu failover na platformie Azure

1. W **ustawienia** > **zreplikowane elementy**, wybierz maszynę Wirtualną, aby w trybie Failover, a następnie wybierz **trybu Failover**.
2. W obszarze **Tryb failover** wybierz **Punkt odzyskiwania**, do którego przełączenie w tryb failover ma zostać wykonane. Możesz użyć jednej z następujących opcji:
   * **Najnowszy**: ta opcja najpierw przetwarza wszystkie dane wysyłane do usługi Site Recovery. Ponieważ maszyny Wirtualnej platformy Azure, który jest tworzony po włączeniu trybu failover zawiera wszystkie dane, które zostały zreplikowane w usłudze Site Recovery podczas pracy w trybie failover zostało wyzwolone, udostępnia najniższy cel punktu odzyskiwania (RPO).
   * **Najnowszy przetworzony**: Ta opcja wprowadza maszynę Wirtualną za pośrednictwem do najnowszego punktu odzyskiwania przetworzonego przez usługę Site Recovery. Ta opcja zapewnia niską wartość (Recovery Time Objective), ponieważ nie jest zużywany czas przetwarzania nieprzetworzonych danych.
   * **Najnowszy spójny na poziomie aplikacji**: Ta opcja wprowadza maszynę Wirtualną za pośrednictwem na najnowszy spójny na poziomie aplikacji punkt przywracania przetworzone przez usługę Site Recovery.
   * **Niestandardowy**: Ta opcja umożliwia określenie punktu odzyskiwania.

3. Wybierz **Zamknij maszynę przed rozpoczęciem pracy awaryjnej** próby zamknij źródłowe maszyny wirtualne przed wyzwoleniem trybu failover. Tryb failover będzie kontynuowane, nawet jeśli zamknięcie nie powiedzie się. Na stronie **Zadania** można śledzić postęp trybu failover.

W niektórych scenariuszach tryb failover wymaga dodatkowego przetwarzania, który trwa około 8 – 10 minut. Dłuższy czas testu trybu failover dla:

* Uruchomiona wersja usługi mobilności starsze niż 9,8 maszyn wirtualnych VMware.
* Serwerów fizycznych.
* Maszyny wirtualne VMware z systemem Linux.
* Maszyny wirtualne funkcji Hyper-V chronionych jako serwerów fizycznych.
* Maszyny wirtualne VMware, które nie mają włączoną usługę DHCP.
* Maszyny wirtualne VMware, które nie mają następujących sterowników rozruchowych: storvsc, vmbus, storflt, intelide, atapi.

> [!WARNING]
> Nie Anuluj trybu failover w toku. Przed rozpoczęciem pracy w trybie failover zatrzymywana jest replikacja maszyny wirtualnej. Jeśli anulujesz tryb failover po rozpoczęciu przełączania, zostanie ono zatrzymane, ale maszyna wirtualna nie zostanie ponownie zreplikowana.

## <a name="connect-to-failed-over-vm"></a>Łączenie z maszyną Wirtualną w trybie failed-over

1. Jeśli chcesz połączyć się z maszynami wirtualnymi platformy Azure po włączeniu trybu failover przy użyciu protokołu RDP (Remote Desktop) i Secure Shell (SSH), [Sprawdź, czy zostały spełnione wymagania](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).
2. Po włączeniu trybu failover, przejdź do maszyny Wirtualnej, a następnie zweryfikuj, [łączenie](../virtual-machines/windows/connect-logon.md) do niego.
3. Użyj **Zmień punkt odzyskiwania** Jeśli chcesz użyć innego punktu odzyskiwania po włączeniu trybu failover. Po zatwierdzeniu trybu failover w następnym kroku tej opcji nie będzie dostępne.
4. Po zakończeniu walidacji wybierz **zatwierdzić** Aby sfinalizować punktu odzyskiwania maszyny wirtualnej po włączeniu trybu failover.
5. Po zatwierdzeniu, zostaną usunięte wszystkie inne dostępne punkty odzyskiwania. Ten krok jest wykonywany przełączenie w tryb failover.

>[!TIP]
> Jeśli napotkasz jakiekolwiek problemy łączności po włączeniu trybu failover, wykonaj [przewodnik rozwiązywania problemów z](site-recovery-failover-to-azure-troubleshoot.md).

## <a name="next-steps"></a>Kolejne kroki

Po przejściu w tryb failover należy ponownie włączyć ochronę maszyn wirtualnych platformy Azure do środowiska lokalnego. Następnie po maszyny wirtualne znajdują się ponownie i replikowane do lokacji lokalnej, awarii powrót po awarii z platformy Azure, gdy wszystko będzie gotowe.

> [!div class="nextstepaction"]
> [Ponowne włączanie ochrony maszyn wirtualnych platformy Azure](vmware-azure-reprotect.md)
> [powrót po awarii z platformy Azure](vmware-azure-failback.md)
