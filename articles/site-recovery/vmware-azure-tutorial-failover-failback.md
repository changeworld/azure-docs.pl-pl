---
title: Przechodzenie do trybu failover i powrót po awarii maszyn wirtualnych VMware i serwerów fizycznych podczas odzyskiwania po awarii na platformie Azure za pomocą usługi Site Recovery | Microsoft Docs
description: Dowiedz się, jak przełączać maszyny wirtualne VMware i serwery fizyczne na platformę Azure w trybie failover oraz jak w przypadku powrotu po awarii do lokacji lokalnej, podczas odzyskiwania danych na platformie Azure za pomocą Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: tutorial
ms.date: 08/22/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 852193e137eab10d1e46c5ba6ae6636d530095be
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972190"
---
# <a name="fail-over-and-fail-back-vmware-vms"></a>Przełączenie w tryb failover i powrót po awarii maszyn wirtualnych VMware

W tym artykule opisano, jak przełączyć lokalną maszynę wirtualną VMware do [Azure Site Recovery](site-recovery-overview.md).

Jest to piąty samouczek w serii, który pokazuje, jak skonfigurować odzyskiwanie po awarii na platformie Azure dla maszyn lokalnych.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Sprawdź, czy właściwości maszyny wirtualnej VMware są zgodne z wymaganiami platformy Azure.
> * Uruchom tryb failover na platformie Azure.

> [!NOTE]
> Samouczki pokazują najprostszą ścieżkę wdrożenia dla scenariusza. Korzystają one z opcji domyślnych, tam gdzie to możliwe, i nie wyświetlają wszystkich możliwych ustawień i ścieżek. Aby uzyskać szczegółowe informacje na temat pracy awaryjnej, zobacz [maszyny wirtualne i serwery fizyczne](site-recovery-failover.md)w trybie failover.

## <a name="before-you-start"></a>Przed rozpoczęciem

Wykonaj poprzednie samouczki:

1. Upewnij się, że [skonfigurowano platformę Azure](tutorial-prepare-azure.md) do lokalnego odzyskiwania po awarii maszyn wirtualnych VMware, maszyn wirtualnych funkcji Hyper-V i maszyn fizycznych na platformie Azure.
2. Przygotuj lokalne środowisko [VMware](vmware-azure-tutorial-prepare-on-premises.md) lub [funkcji Hyper-V](hyper-v-prepare-on-premises-tutorial.md) na potrzeby odzyskiwania po awarii. W przypadku konfigurowania odzyskiwania po awarii dla serwerów fizycznych zapoznaj się z [matrycą pomocy technicznej](vmware-physical-secondary-support-matrix.md).
3. Skonfiguruj odzyskiwanie po awarii dla maszyn [wirtualnych VMware](vmware-azure-tutorial.md), maszyn [wirtualnych funkcji Hyper-V](hyper-v-azure-tutorial.md)lub [maszyn fizycznych](physical-azure-disaster-recovery.md).
4. Uruchom [drążenie odzyskiwania po awarii](tutorial-dr-drill-azure.md) , aby upewnić się, że wszystko działa zgodnie z oczekiwaniami.

## <a name="failover-and-failback"></a>Praca w trybie failover i powrót po awarii

Przełączanie do trybu failover i powrót po awarii odbywa się w czterech etapach:

1. **Przechodzenie w tryb failover na platformie Azure:** Gdy lokalna lokacja główna przejdzie w stan awarii, komputery są w trybie failover na platformie Azure. Po przejściu w tryb failover maszyny wirtualne platformy Azure są tworzone na podstawie replikowanych danych.
2. **Ponowne włączanie ochrony maszyn wirtualnych platformy Azure:** Na platformie Azure ponownie Włącz ochronę maszyn wirtualnych platformy Azure, aby umożliwić ich replikowanie z powrotem do lokalnych maszyn wirtualnych programu VMware. Lokalna maszyna wirtualna jest wyłączona podczas ponownej ochrony, aby zapewnić spójność danych.
3. **Przełączenie w tryb failover do lokalnego:** Gdy lokacja lokalna jest uruchomiona, uruchom tryb failover w celu powrotu po awarii z platformy Azure.
4. **Ponowne włączanie ochrony lokalnych maszyn wirtualnych:** Po awarii danych ponownie Włącz ochronę lokalnych maszyn wirtualnych, do których nie powiodła się z powrotem, aby rozpocząć replikację do platformy Azure.

## <a name="verify-vm-properties"></a>Sprawdzanie właściwości maszyn wirtualnych

Przed uruchomieniem trybu failover Sprawdź właściwości maszyny wirtualnej, aby upewnić się, że maszyny wirtualne spełniają [wymagania platformy Azure](vmware-physical-azure-support-matrix.md#replicated-machines).

Sprawdź właściwości w następujący sposób:

1. W obszarze **chronione elementy**wybierz pozycję **zreplikowane elementy**, a następnie wybierz maszynę wirtualną, którą chcesz zweryfikować.

2. Okienko **Zreplikowany element** zawiera podsumowanie informacji na temat maszyny wirtualnej, jej kondycję oraz najnowsze dostępne punkty odzyskiwania. Wybierz pozycję **Właściwości** , aby wyświetlić więcej szczegółów.

3. W obszarze **obliczenia i sieć**można modyfikować te właściwości zgodnie z wymaganiami:
    * Nazwa platformy Azure
    * Resource group
    * Rozmiar elementu docelowego
    * [Zestaw dostępności](../virtual-machines/windows/tutorial-availability-sets.md)
    * Ustawienia dysku zarządzanego

4. Można wyświetlać i modyfikować ustawienia sieci, w tym:

    * Sieć i podsieć, w której zostanie umieszczona maszyna wirtualna platformy Azure po przejściu do trybu failover.
    * Adres IP, który zostanie przypisany do niego.

5. W obszarze **Dyski** można znaleźć informacje dotyczące systemu operacyjnego i dysków danych maszyny wirtualnej.

## <a name="run-a-failover-to-azure"></a>Przełączanie do trybu failover na platformie Azure

1. W obszarze **Ustawienia** > **zreplikowane elementy**wybierz maszynę wirtualną, która ma zostać przełączona w tryb failover, a następnie wybierz pozycję **Praca awaryjna**.
2. W obszarze **Tryb failover** wybierz **Punkt odzyskiwania**, do którego przełączenie w tryb failover ma zostać wykonane. Możesz użyć jednej z następujących opcji:
   * **Najnowszy**: ta opcja najpierw przetwarza wszystkie dane wysyłane do usługi Site Recovery. Zapewnia najniższy cel punktu odzyskiwania (RPO), ponieważ maszyna wirtualna platformy Azure utworzona po przejściu w tryb failover ma wszystkie dane, które zostały zreplikowane do Site Recovery podczas wyzwolenia trybu failover.
   * **Najnowszy przetworzony**: Ta opcja powoduje, że maszyna wirtualna zostanie przełączona w tryb failover do najnowszego punktu odzyskiwania przetworzonego przez Site Recovery. Ta opcja zapewnia niski RTO (cel czasu odzyskiwania), ponieważ nie trwa przetwarzanie nieprzetworzonych danych.
   * **Najnowszy spójny na poziomie aplikacji**: Ta opcja spowoduje przełączenie maszyny wirtualnej w tryb failover do najnowszego punktu odzyskiwania spójnego na poziomie aplikacji przetworzonego przez Site Recovery.
   * **Niestandardowy**: Ta opcja umożliwia określenie punktu odzyskiwania.

3. Wybierz opcję **Zamknij maszynę przed rozpoczęciem pracy w trybie failover** , aby podjąć próbę zamknięcia źródłowych maszyn wirtualnych przed wyzwoleniem trybu failover. Tryb failover kontynuuje działanie nawet wtedy, gdy wyłączenie nie powiedzie się. Na stronie **Zadania** można śledzić postęp trybu failover.

W niektórych scenariuszach tryb failover wymaga dodatkowego przetwarzania, którego ukończenie trwa od 8 do 10 minut. Możesz zauważyć dłuższe czasy testowania pracy w trybie failover dla:

* Maszyny wirtualne VMware z uruchomioną wersją usługi mobilności starszą niż 9,8.
* Serwery fizyczne.
* Maszyny wirtualne VMware Linux.
* Maszyny wirtualne funkcji Hyper-V chronione jako serwery fizyczne.
* Maszyny wirtualne VMware, które nie mają włączonej usługi DHCP.
* Maszyny wirtualne VMware, które nie mają następujących sterowników rozruchowych: storvsc, VMBus, storflt, Intelide, ATAPI.

> [!WARNING]
> Nie Anuluj trybu failover w toku. Przed rozpoczęciem pracy w trybie failover zatrzymywana jest replikacja maszyny wirtualnej. Jeśli anulujesz tryb failover po rozpoczęciu przełączania, zostanie ono zatrzymane, ale maszyna wirtualna nie zostanie ponownie zreplikowana.

## <a name="connect-to-failed-over-vm"></a>Nawiązywanie połączenia z maszyną wirtualną w trybie failover

1. Jeśli chcesz nawiązać połączenie z maszynami wirtualnymi platformy Azure po przejściu w tryb failover przy użyciu usług Remote Desktop Protocol (RDP) i Secure Shell (SSH), [Sprawdź, czy zostały spełnione wymagania](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).
2. Po przejściu w tryb failover przejdź do maszyny wirtualnej i sprawdź poprawność, [łącząc](../virtual-machines/windows/connect-logon.md) się z nią.
3. Jeśli po przejściu w tryb failover ma zostać użyty inny punkt odzyskiwania, użyj **Zmień punkt odzyskiwania** . Po zatwierdzeniu przejścia w tryb failover w następnym kroku ta opcja nie będzie już dostępna.
4. Po sprawdzeniu poprawności wybierz pozycję Zatwierdź, aby zakończyć punkt odzyskiwania maszyny wirtualnej po przejściu do trybu failover.
5. Po zatwierdzeniu wszystkie pozostałe dostępne punkty odzyskiwania zostaną usunięte. Ten krok powoduje zakończenie pracy w trybie failover.

>[!TIP]
> Jeśli występują problemy z łącznością po przejściu do trybu failover, postępuj zgodnie z [przewodnikiem rozwiązywania problemów](site-recovery-failover-to-azure-troubleshoot.md).

## <a name="next-steps"></a>Następne kroki

Po przejściu w tryb failover ponownie Włącz ochronę maszyn wirtualnych platformy Azure w środowisku lokalnym. Następnie po ponownym włączeniu ochrony maszyn wirtualnych i przeprowadzeniu replikacji do lokacji lokalnej powrót po awarii z platformy Azure zakończy się niepowodzeniem.

> [!div class="nextstepaction"]
> [Ponowne włączanie ochrony maszyn wirtualnych](vmware-azure-reprotect.md)
> platformy Azure[powrót po awarii z platformy Azure](vmware-azure-failback.md)
