---
title: Uruchamianie próbnego odzyskiwania po awarii maszyn lokalnych na platformę Azure przy użyciu usługi Azure Site Recovery | Microsoft Docs
description: Informacje o uruchamianiu próbnego odzyskiwania po awarii ze środowiska lokalnego na platformę Azure przy użyciu usługi Azure Site Recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/03/2018
ms.author: raynew
ms.openlocfilehash: fa66e47715940584259e5cf555f3f6cd6f07e267
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37437216"
---
# <a name="run-a-disaster-recovery-drill-to-azure"></a>Uruchamianie próbnego odzyskiwania na platformie Azure

W tym artykule pokazano, jak uruchomić próbne odzyskiwanie po awarii dla maszyny lokalnej na platformie Azure, korzystając z testowego przełączania w tryb failover. Próba pozwala zweryfikować działanie strategii replikacji bez ryzyka utraty danych.

Jest to czwarty samouczek z serii opisującej, jak skonfigurować odzyskiwanie po awarii na platformie Azure dla lokalnych maszyn wirtualnych VMware lub Hyper-V.

W tym samouczku przyjęto założenie, że wykonano trzy pierwsze samouczki: 
    - W [pierwszym samouczku](tutorial-prepare-azure.md) [przygotowano składniki Azure](tutorial-prepare-azure.md) potrzebne do odzyskiwania po awarii w przypadku maszyn wirtualnych VMware lub Hyper-V.
    - W drugim samouczku przygotowano składniki lokalne do odzyskiwania po awarii w przypadku maszyn wirtualnych [VMware](vmware-azure-tutorial-prepare-on-premises.md) lub [Hyper-V](hyper-v-prepare-on-premises-tutorial.md).
    - W trzecim samouczku zostanie skonfigurowana i włączona replikacja lokalnych [maszyn wirtualnych VMware](vmware-azure-tutorial.md), [maszyn wirtualnych Hyper-V z programem System Center VMM](hyper-v-vmm-azure-tutorial.md) lub [Maszyn wirtualnych Hyper-V bez programu VMM](hyper-v-azure-tutorial.md).
- Samouczki mają za zadanie przedstawić najprostszą ścieżkę wdrożenia dla scenariusza. Jeśli to możliwe, używają opcji domyślnych i nie przedstawiają wszystkich możliwych ustawień i ścieżek. We wszystkich tych samouczkach skonfigurowano usługę Site Recovery przy użyciu najprostszych ustawień, stosując wartości domyślne, gdzie było to możliwe. Jeśli chcesz lepiej poznać kroki testowego przełączania w tryb failover, przeczytaj [How To Guide](site-recovery-test-failover-to-azure.md) (Przewodnik z instrukcjami).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie sieci izolowanej do testowego przełączania w tryb failover
> * Przygotowywanie do połączenia z maszyną wirtualną platformy Azure po przejściu do trybu failover
> * Uruchamianie testowego przełączania w tryb failover dla pojedynczej maszyny

Ten samouczek umożliwia opanowanie następujących czynności:

## <a name="verify-vm-properties"></a>Sprawdzanie właściwości maszyn wirtualnych

Przed uruchomieniem testowego przełączenia w tryb failover sprawdź właściwości maszyny wirtualnej i upewnij się, że [maszyna wirtualna Hyper-V](hyper-v-azure-support-matrix.md#replicated-vms) lub [maszyna wirtualna VMware](vmware-physical-azure-support-matrix.md#replicated-machines) spełnia wymagania platformy Azure.

1. W obszarze **Chronione elementy** kliknij kolejno pozycje **Zreplikowane elementy** > i Maszyna wirtualna.
2. Okienko **Zreplikowany element** zawiera podsumowanie informacji na temat maszyny wirtualnej, jej kondycję oraz najnowsze dostępne punkty odzyskiwania. Kliknij przycisk **Właściwości**, aby wyświetlić więcej szczegółów.
3. W obszarze **Obliczenia i sieć** można zmodyfikować nazwę platformy Azure, grupę zasobów, rozmiar docelowy, zestaw dostępności i ustawienia dysku zarządzanego.
4. Możesz wyświetlać i modyfikować ustawienia sieciowe, w tym sieć/podsieć, w której zlokalizowana będzie maszyna wirtualna na platformie Azure po wejściu w tryb failover, oraz adres IP, który będzie do niej przypisany.
5. W obszarze **Dyski** można znaleźć informacje dotyczące systemu operacyjnego i dysków danych maszyny wirtualnej.

## <a name="run-a-test-failover-for-a-single-vm"></a>Uruchamianie testu trybu failover dla pojedynczej maszyny wirtualnej

Po uruchomieniu próby przejścia w tryb failover wykonywane są następujące operacje:

1. Uruchamiane jest sprawdzanie wymagań wstępnych, aby upewnić się, że zostały spełnione wszystkie warunki przejścia w tryb failover.
2. Tryb failover przetwarza dane, aby umożliwić utworzenie maszyny wirtualnej platformy Azure. Jeśli zostanie wybrany najnowszy punkt odzyskiwania, punkt odzyskiwania zostanie utworzony na podstawie danych.
3. Tworzona jest maszyna wirtualna platformy Azure przy użyciu danych przetworzonych w poprzednim kroku.

Uruchom testowe przełączenie w tryb failover w następujący sposób:

1. W obszarze **Ustawienia** > **Elementy zreplikowane** kliknij pozycje Maszyna wirtualna > **+Testowe przełączenie w tryb failover**.
2. Na potrzeby tego samouczka wybierz punkt odzyskiwania **Najnowszy przetworzony**. Spowoduje to przełączenie maszyny wirtualnej w tryb failover do najnowszego dostępnego punktu w czasie. Wyświetlana jest sygnatura czasowa. Ta opcja zapewnia niską wartość celu czasu odzyskiwania (RTO, Recovery Time Objective), ponieważ nie trzeba poświęcać czasu na przetwarzanie danych.
3. W obszarze **Test pracy w trybie failover** wybierz sieć platformy Azure, z którą maszyny wirtualne platformy Azure zostaną połączone po przejściu w tryb failover.
4. Kliknij przycisk **OK**, aby rozpocząć tryb failover. Możesz śledzić postęp, klikając maszynę wirtualną i otwierając jej właściwości. Możesz też kliknąć zadanie **Testowe przełączenie w tryb failover** po wybraniu pozycji nazwa magazynu > **Ustawienia** > **Zadania** >
   **Zadania usługi Site Recovery**.
5. Po zakończeniu trybu failover w obszarze Azure Portal > **Maszyny wirtualne** będzie widoczna replika maszyny wirtualnej platformy Azure. Upewnij się, że maszyna wirtualna ma prawidłowy rozmiar, jest połączona z odpowiednią siecią i jest uruchomiona.
6. Powinno być teraz możliwe nawiązanie połączenia ze zreplikowaną maszyną wirtualną na platformie Azure.
7. Aby usunąć maszyny wirtualne platformy Azure utworzone podczas testowego przełączania w tryb failover, kliknij pozycję **Wyczyść test pracy w trybie failover** na maszynie wirtualnej. W obszarze **Uwagi** zarejestruj i zapisz wszelkie obserwacje związane z testem pracy w trybie failover.

W niektórych scenariuszach tryb failover wymaga dodatkowego przetwarzania, którego przeprowadzenie zajmuje około 8–10 minut. Dłuższy czas testowego przełączania w tryb failover może występować w przypadku maszyn VMware z systemem Linux, maszyn wirtualnych VMware, które nie mają włączonej usługi DHCP, oraz maszyn wirtualnych VMware, które nie mają następujących sterowników rozruchowych: storvsc, vmbus, storflt, intelide, atapi.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Przechodzenie do trybu failover i powrót po awarii lokalnych maszyn wirtualnych VMware](vmware-azure-tutorial-failover-failback.md).
> [Run a failover and failback for on-premises Hyper-V VMs (Przechodzenie do trybu failover i powrót po awarii lokalnych maszyn wirtualnych Hyper-V)](hyper-v-azure-failover-failback-tutorial.md).
