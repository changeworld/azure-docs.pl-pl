---
title: Uruchamianie wiertła odzyskiwania po awarii na platformie Azure za pomocą usługi Azure Site Recovery
description: Dowiedz się, jak uruchomić wiertło odzyskiwania po awarii z lokalnego na platformę Azure, za pomocą usługi Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 5bd9f5316f8b8799633de8c0c84c61424c0e4f4a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79238902"
---
# <a name="run-a-disaster-recovery-drill-to-azure"></a>Uruchamianie próbnego odzyskiwania na platformie Azure

W tym artykule opisano sposób uruchamiania wiertła odzyskiwania po awarii dla komputera lokalnego na platformie Azure przy użyciu usługi [Azure Site Recovery.](site-recovery-overview.md) Próba pozwala zweryfikować działanie strategii replikacji bez ryzyka utraty danych.


Jest to czwarty samouczek z serii, który pokazuje, jak skonfigurować odzyskiwanie po awarii na platformie Azure dla komputerów lokalnych.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie sieci izolowanej do testowego przełączania w tryb failover
> * Przygotowywanie do połączenia z maszyną wirtualną platformy Azure po przejściu do trybu failover
> * Uruchom test pracy awaryjnej dla pojedynczego komputera.

> [!NOTE]
> Samouczki pokazują najprostszą ścieżkę wdrażania dla scenariusza. Jeśli to możliwe używają opcji domyślnych i nie przedstawiają wszystkich możliwych ustawień i ścieżek. Jeśli chcesz dowiedzieć się więcej szczegółów na temat kroków ćwiczeń odzyskiwania po awarii, [zapoznaj się z tym artykułem](site-recovery-test-failover-to-azure.md).

## <a name="before-you-start"></a>Przed rozpoczęciem

Wykonaj poprzednie samouczki:

1. Upewnij się, że [skonfigurowałeś platformę Azure](tutorial-prepare-azure.md) do lokalnego odzyskiwania po awarii maszyn wirtualnych VMware, maszyn wirtualnych funkcji Hyper-V i komputerów fizycznych na platformie Azure.
2. Przygotuj lokalne środowisko [VMware](vmware-azure-tutorial-prepare-on-premises.md) lub [Hyper-V](hyper-v-prepare-on-premises-tutorial.md) do odzyskiwania po awarii. Jeśli konfigurujesz odzyskiwanie po awarii dla serwerów fizycznych, zapoznaj się z [macierzą pomocy technicznej](vmware-physical-secondary-support-matrix.md).
3. Konfigurowanie odzyskiwania po awarii dla [maszyn wirtualnych VMware,](vmware-azure-tutorial.md) [maszyn wirtualnych funkcji Hyper-V](hyper-v-azure-tutorial.md)lub [maszyn fizycznych.](physical-azure-disaster-recovery.md)
 

## <a name="verify-vm-properties"></a>Sprawdzanie właściwości maszyn wirtualnych

Przed uruchomieniem testowego przełączenia w tryb failover sprawdź właściwości maszyny wirtualnej i upewnij się, że [maszyna wirtualna Hyper-V](hyper-v-azure-support-matrix.md#replicated-vms) lub [maszyna wirtualna VMware](vmware-physical-azure-support-matrix.md#replicated-machines) spełnia wymagania platformy Azure.

1. W obszarze **Chronione elementy** kliknij kolejno pozycje **Zreplikowane elementy** > i Maszyna wirtualna.
2. Okienko **Zreplikowany element** zawiera podsumowanie informacji na temat maszyny wirtualnej, jej kondycję oraz najnowsze dostępne punkty odzyskiwania. Kliknij przycisk **Właściwości**, aby wyświetlić więcej szczegółów.
3. W **obszarze Obliczenia i Sieć**można zmodyfikować ustawienia nazwy, grupy zasobów, rozmiaru docelowego, zestawu dostępności i dysku zarządzanego.
4. Możesz wyświetlać i modyfikować ustawienia sieciowe, w tym sieć/podsieć, w której zlokalizowana będzie maszyna wirtualna na platformie Azure po wejściu w tryb failover, oraz adres IP, który będzie do niej przypisany.
5. W obszarze **Dyski** można znaleźć informacje dotyczące systemu operacyjnego i dysków danych maszyny wirtualnej.

## <a name="create-a-network-for-test-failover"></a>Tworzenie sieci do testowania pracy w trybie failover

Do testowania pracy w trybie failover zalecamy wybór sieci izolowanej od sieci produkcyjnej lokacji odzyskiwania, określonej w obszarze ustawień **Obliczenia i sieć** poszczególnych maszyn wirtualnych. Tworzone sieci wirtualne platformy Azure są domyślnie izolowane od innych sieci. Sieć testowa powinna odzwierciedlać sieć produkcyjną:

- Sieć testowa powinna mieć taką samą liczbę podsieci jak sieć produkcyjna. Podsieci powinny mieć takie same nazwy.
- W sieci testowej powinien być używany taki sam zakres adresów IP.
- Zaktualizuj system DNS sieci testowej, używając adresu IP określonego dla maszyny wirtualnej systemu DNS w obszarze ustawień **Obliczenia i sieć**. Aby uzyskać więcej informacji, zobacz sekcję [Zagadnienia dotyczące testowania trybu failover dla usługi Active Directory](site-recovery-active-directory.md#test-failover-considerations).

## <a name="run-a-test-failover-for-a-single-vm"></a>Uruchamianie testu trybu failover dla pojedynczej maszyny wirtualnej

Po uruchomieniu próby przejścia w tryb failover wykonywane są następujące operacje:

1. Uruchamiane jest sprawdzanie wymagań wstępnych, aby upewnić się, że zostały spełnione wszystkie warunki przejścia w tryb failover.
2. Tryb failover przetwarza dane, aby umożliwić utworzenie maszyny wirtualnej platformy Azure. Jeśli wybierzesz najnowszy punkt odzyskiwania, punkt odzyskiwania zostanie utworzony na podstawie danych.
3. Tworzona jest maszyna wirtualna platformy Azure przy użyciu danych przetworzonych w poprzednim kroku.

Uruchom testowe przełączenie w tryb failover w następujący sposób:

1. W **obszarze Ustawienia** > **replikowane elementy**kliknij maszynę wirtualną > **+Test failover**.
2. Na potrzeby tego samouczka wybierz punkt odzyskiwania **Najnowszy przetworzony**. Spowoduje to przełączenie maszyny wirtualnej w tryb failover do najnowszego dostępnego punktu w czasie. Wyświetlana jest sygnatura czasowa. Ta opcja zapewnia niską wartość celu czasu odzyskiwania (RTO, Recovery Time Objective), ponieważ nie trzeba poświęcać czasu na przetwarzanie danych.
3. W obszarze **Test pracy w trybie failover** wybierz sieć platformy Azure, z którą maszyny wirtualne platformy Azure zostaną połączone po przejściu w tryb failover.
4. Kliknij przycisk **OK**, aby rozpocząć tryb failover. Możesz śledzić postęp, klikając maszynę wirtualną i otwierając jej właściwości. Możesz też kliknąć zadanie **Testowe przełączenie w tryb failover** po wybraniu pozycji nazwa magazynu > **Ustawienia** > **Zadania** >
   **Zadania usługi Site Recovery**.
5. Po zakończeniu trybu failover w obszarze Azure Portal > **Maszyny wirtualne** będzie widoczna replika maszyny wirtualnej platformy Azure. Upewnij się, że maszyna wirtualna ma prawidłowy rozmiar, jest połączona z odpowiednią siecią i jest uruchomiona.
6. Powinno być teraz możliwe nawiązanie połączenia ze zreplikowaną maszyną wirtualną na platformie Azure.
7. Aby usunąć maszyny wirtualne platformy Azure utworzone podczas testowego przełączania w tryb failover, kliknij pozycję **Wyczyść test pracy w trybie failover** na maszynie wirtualnej. W **uwagach**należy rejestrować i zapisywać wszystkie obserwacje związane z testem w wersji failover.

W niektórych scenariuszach tryb failover wymaga dodatkowego przetwarzania, którego przeprowadzenie zajmuje około 8–10 minut. Dłuższy czas testowego przełączania w tryb failover może występować w przypadku maszyn VMware z systemem Linux, maszyn wirtualnych VMware, które nie mają włączonej usługi DHCP, oraz maszyn wirtualnych VMware, które nie mają następujących sterowników rozruchowych: storvsc, vmbus, storflt, intelide, atapi.

## <a name="connect-after-failover"></a>Łączenie po przełączeniu awaryjnym

Jeśli chcesz połączyć się z maszynami wirtualnymi platformy Azure przy użyciu protokołu RDP/SSH po przełączeniu w stan failover, [przygotuj się do połączenia](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover). Jeśli wystąpią jakiekolwiek problemy z łącznością po pracy awaryjnej, postępuj zgodnie z przewodnikiem [rozwiązywania problemów.](site-recovery-failover-to-azure-troubleshoot.md)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Uruchamianie pracy awaryjnej i powrotu po awarii dla maszyn wirtualnych VMware](vmware-azure-tutorial-failover-failback.md)
> [Uruchom powiększanie w pracy awaryjnej i po awarii dla maszyn v-VMM Uruchom](hyper-v-azure-failover-failback-tutorial.md)
> [po awarii dla komputerów fizycznych](physical-to-azure-failover-failback.md)
