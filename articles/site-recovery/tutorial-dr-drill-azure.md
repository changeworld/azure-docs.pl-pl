---
title: Uruchamianie próbnego odzyskiwania po awarii maszyn lokalnych na platformę Azure przy użyciu usługi Azure Site Recovery | Microsoft Docs
description: Informacje o uruchamianiu próbnego odzyskiwania po awarii ze środowiska lokalnego na platformę Azure przy użyciu usługi Azure Site Recovery
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 06/04/2018
ms.author: raynew
ms.openlocfilehash: d1b6dec122672e4f6260105f7b50af2cd7369947
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34737110"
---
# <a name="run-a-disaster-recovery-drill-to-azure"></a>Uruchamianie próbnego odzyskiwania na platformie Azure

Usługa [Azure Site Recovery](site-recovery-overview.md) przyczynia się do strategii związanej z ciągłością biznesową i odzyskiwaniem po awarii (BCDR, business continuity and disaster recovery) przez zapewnienie niezawodnego działania aplikacji biznesowych podczas planowanych lub nieplanowanych przestojów. Usługa Site Recovery zarządza odzyskiwaniem po awarii maszyn lokalnych i maszyn wirtualnych platformy Azure, a także organizuje to odzyskiwanie. Obejmuje to replikację, przechodzenie w tryb failover i odzyskiwanie.

- Jest to czwarty samouczek z serii opisującej, jak skonfigurować odzyskiwanie po awarii na platformie Azure dla lokalnych maszyn wirtualnych WMware. Przyjęto, że użytkownik zakończył pierwsze dwa samouczki:
    - W [pierwszym samouczku](tutorial-prepare-azure.md) skonfigurowaliśmy składniki Azure potrzebne do odzyskiwania po awarii w przypadku maszyn wirtualnych VMware.
    - W [drugim samouczku](vmware-azure-tutorial-prepare-on-premises.md) przygotowaliśmy składniki lokalne do odzyskiwania po awarii oraz sprawdziliśmy wymagania wstępne.
    - W [trzecim samouczku](vmware-azure-tutorial.md) skonfigurowaliśmy i włączyliśmy replikację dla naszych lokalnych maszyn wirtualnych VMware.
- Samouczki mają za zadanie przedstawić najprostszą ścieżkę wdrożenia dla scenariusza. Jeśli to możliwe, używają opcji domyślnych i nie przedstawiają wszystkich możliwych ustawień i ścieżek. 


W tym artykule pokazano, jak uruchomić próbne odzyskiwanie po awarii dla maszyny lokalnej na platformie Azure, korzystając z testowego przełączania w tryb failover. Próba pozwala zweryfikować działanie strategii replikacji bez ryzyka utraty danych. Instrukcje:

> [!div class="checklist"]
> * Konfigurowanie sieci izolowanej do testowego przełączania w tryb failover
> * Przygotowywanie do połączenia z maszyną wirtualną platformy Azure po przejściu do trybu failover
> * Uruchamianie testowego przełączania w tryb failover dla pojedynczej maszyny

W tym samouczku przedstawiono konfigurację odzyskiwania po awarii maszyn wirtualnych VMware na platformę Azure, przy czym wykorzystano najprostsze ustawienia. Jeśli chcesz lepiej poznać kroki testowego przełączania w tryb failover, przeczytaj [How To Guide](site-recovery-test-failover-to-azure.md) (Przewodnik z instrukcjami).

## <a name="verify-vm-properties"></a>Sprawdzanie właściwości maszyn wirtualnych

Przed uruchomieniem testowego przełączenia w tryb failover sprawdź właściwości maszyny wirtualnej VMware i upewnij się, że maszyna wirtualna Hyper-V[hyper-v-azure-support-matrix.md#replicated-vms], [maszyna wirtualna VMware lub serwer fizyczny](vmware-physical-azure-support-matrix.md#replicated-machines) spełnia wymagania platformy Azure.

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
> [Uruchamianie trybu failover i powrotu po awarii dla lokalnych maszyn wirtualnych VMware](vmware-azure-tutorial-failover-failback.md).
