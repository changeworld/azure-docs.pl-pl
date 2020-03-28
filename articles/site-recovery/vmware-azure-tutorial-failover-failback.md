---
title: Maszyny wirtualne vmware w wersji Azure w wersji Fail over na platformie Azure za pomocą usługi Site Recovery
description: Dowiedz się, jak awaryjnie przeczesywać maszyny wirtualne VMware na platformie Azure w usłudze Azure Site Recovery
ms.service: site-recovery
ms.topic: tutorial
ms.date: 12/16/2019
ms.custom: MVC
ms.openlocfilehash: 8501bb1a998eb08984a118bfa5d52d1e3f3e4f84
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75498078"
---
# <a name="fail-over--vmware-vms"></a>Maszyny wirtualne VMware w wersji fail over

W tym artykule opisano, jak w trybie fail over lokalnej maszyny wirtualnej VMware (VM) do platformy Azure z [usługi Azure Site Recovery](site-recovery-overview.md).

Jest to piąty samouczek z serii, który pokazuje, jak skonfigurować odzyskiwanie po awarii na platformie Azure dla komputerów lokalnych.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Sprawdź, czy właściwości maszyny Wirtualnej VMware są zgodne z wymaganiami platformy Azure.
> * Przebłaśnia określonych maszyn wirtualnych w wersji Azure w wersji fail0.

> [!NOTE]
> Samouczki pokazują najprostszą ścieżkę wdrażania dla scenariusza. W miarę możliwości używają opcji domyślnych i nie wyświetlają wszystkich możliwych ustawień i ścieżek. Jeśli chcesz dowiedzieć się więcej o pracy awaryjnej, zobacz [Maszyny wirtualne trybu failover i serwery fizyczne](site-recovery-failover.md).

[Dowiedz się więcej o](failover-failback-overview.md#types-of-failover) różnych typach pracy awaryjnej. Jeśli chcesz przebiec awaryjnie wiele maszyn wirtualnych w planie odzyskiwania, zapoznaj się z [tym artykułem](site-recovery-failover.md).

## <a name="before-you-start"></a>Przed rozpoczęciem

Wykonaj poprzednie samouczki:

1. Upewnij się, że [skonfigurowałeś platformę Azure](tutorial-prepare-azure.md) do lokalnego odzyskiwania po awarii maszyn wirtualnych VMware, maszyn wirtualnych funkcji Hyper-V i komputerów fizycznych na platformie Azure.
2. Przygotuj lokalne środowisko [VMware](vmware-azure-tutorial-prepare-on-premises.md) do odzyskiwania po awarii. 
3. Skonfiguruj odzyskiwanie po awarii dla [maszyn wirtualnych VMware](vmware-azure-tutorial.md).
4. Uruchom [ćwiczenie odzyskiwania po awarii,](tutorial-dr-drill-azure.md) aby upewnić się, że wszystko działa zgodnie z oczekiwaniami.

## <a name="verify-vm-properties"></a>Sprawdzanie właściwości maszyn wirtualnych

Przed uruchomieniem pracy awaryjnej sprawdź właściwości maszyny Wirtualnej, aby upewnić się, że maszyny wirtualne spełniają [wymagania platformy Azure.](vmware-physical-azure-support-matrix.md#replicated-machines)

Sprawdź właściwości w następujący sposób:

1. W **obszarze Elementy chronione**wybierz pozycję Elementy **replikowane**, a następnie wybierz maszynę wirtualną, którą chcesz zweryfikować.

2. Okienko **Zreplikowany element** zawiera podsumowanie informacji na temat maszyny wirtualnej, jej kondycję oraz najnowsze dostępne punkty odzyskiwania. Wybierz **właściwości,** aby wyświetlić więcej szczegółów.

3. W **oknie Obliczeniowo-Sieciowym**można w razie potrzeby zmodyfikować następujące właściwości:
    * Nazwa platformy Azure
    * Grupa zasobów
    * Rozmiar docelowy
    * [Zestaw dostępności](../virtual-machines/windows/tutorial-availability-sets.md)
    * Ustawienia dysku zarządzanego

4. Można wyświetlać i modyfikować ustawienia sieciowe, w tym:

    * Sieć i podsieć, w których będzie znajdować się maszyna wirtualna platformy Azure po przemiń awaryjnych.
    * Adres IP, który zostanie do niego przypisany.

5. W obszarze **Dyski** można znaleźć informacje dotyczące systemu operacyjnego i dysków danych maszyny wirtualnej.

## <a name="run-a-failover-to-azure"></a>Przełączanie do trybu failover na platformie Azure

1. W **obszarze Ustawienia** > **replikowane elementy**wybierz maszynę wirtualną, którą chcesz przeminąć w pracy awaryjnej, a następnie wybierz pozycję **Przewijanie awaryjne**.
2. W obszarze **Tryb failover** wybierz **Punkt odzyskiwania**, do którego przełączenie w tryb failover ma zostać wykonane. Możesz użyć jednej z następujących opcji:
   * **Najnowszy**: ta opcja najpierw przetwarza wszystkie dane wysyłane do usługi Site Recovery. Zapewnia najniższy cel punktu odzyskiwania (RPO), ponieważ maszyna wirtualna platformy Azure utworzona po przełączeniu w błąd ma wszystkie dane, które zostały zreplikowane do usługi Site Recovery po wyzwoleniu pracy awaryjnej.
   * **Najnowsze przetworzone:** Ta opcja kończy się niepowodzeniem maszyny Wirtualnej do najnowszego punktu odzyskiwania przetwarzanego przez odzyskiwanie witryny. Ta opcja zapewnia niski cel czasu odzyskiwania (cel czasu odzyskiwania), ponieważ nie spędza się czasu na przetwarzaniu nieprzetwochanych danych.
   * **Najnowsze spójne z aplikacjami:** Ta opcja kończy się niepowodzeniem maszyny Wirtualnej do najnowszego punktu odzyskiwania spójnego z aplikacją przetwarzanego przez odzyskiwanie witryny.
   * **Niestandardowe:** Ta opcja umożliwia określenie punktu odzyskiwania.

3. Wybierz **zamknij komputer przed rozpoczęciem pracy awaryjnej,** aby spróbować zamknąć źródłowe maszyny wirtualne przed wyzwoleniem pracy awaryjnej. Tryb failover jest kontynuowany, nawet jeśli zamknięcie nie powiedzie się. Postęp pracy awaryjnej można śledzić na stronie **Zadania.**

W niektórych scenariuszach praca awaryjna wymaga dodatkowego przetwarzania, które trwa około 8 do 10 minut. Można zauważyć dłuższe czasy pracy awaryjnej testu dla:

* Maszyny wirtualne VMware z uruchomieniem wersji usługi mobilności starszej niż 9.8.
* Serwery fizyczne.
* Maszyny wirtualne VMware z systemem Linux.
* Maszyny wirtualne funkcji Hyper V chronione jako serwery fizyczne.
* Maszyny wirtualne VMware, które nie mają włączonej usługi DHCP.
* Maszyny wirtualne VMware, które nie mają następujących sterowników rozruchowych: storvsc, vmbus, storflt, intelide, atapi.

> [!WARNING]
> Nie anuluj pracy awaryjnej w toku. Przed rozpoczęciem pracy w trybie failover zatrzymywana jest replikacja maszyny wirtualnej. Jeśli anulujesz tryb failover po rozpoczęciu przełączania, zostanie ono zatrzymane, ale maszyna wirtualna nie zostanie ponownie zreplikowana.

## <a name="connect-to-failed-over-vm"></a>Łączenie się z maszyną wirtualną po awarii

1. Jeśli chcesz połączyć się z maszynami wirtualnymi platformy Azure po przełączeniu w stan failover przy użyciu protokołu RDP (Remote Desktop Protocol) i secure shell (SSH), [sprawdź, czy wymagania zostały spełnione](ailover-failback-overview.md#connect-to-azure-after-failover).
2. Po przełączeniu w stan failover przejdź do maszyny Wirtualnej i sprawdź poprawność, [łącząc się z](../virtual-machines/windows/connect-logon.md) nią.
3. Użyj **zmień punktu odzyskiwania,** jeśli chcesz użyć innego punktu odzyskiwania po pracy awaryjnej. Po zatwierdzeniu pracy awaryjnej w następnym kroku ta opcja nie będzie już dostępna.
4. Po weryfikacji wybierz **commit,** aby sfinalizować punkt odzyskiwania maszyny Wirtualnej po przewijanie w stan failover.
5. Po zatwierdzeniu wszystkie inne dostępne punkty odzyskiwania zostaną usunięte. Ten krok kończy pracy awaryjnej.

>[!TIP]
> Jeśli po przejściu w stan failover wystąpią jakiekolwiek problemy z łącznością, postępuj zgodnie z [przewodnikiem rozwiązywania problemów](site-recovery-failover-to-azure-troubleshoot.md).

## <a name="next-steps"></a>Następne kroki

Po przewijanie w stan failover, ponownierotect maszyn wirtualnych platformy Azure do lokalnego. Następnie po maszyny wirtualne są ponownie cekrowane i replikacji do lokacji lokalnej, powrót po awarii z platformy Azure, gdy jesteś gotowy.

> [!div class="nextstepaction"]
> [Ponowne wycofywanie maszyn wirtualnych](vmware-azure-reprotect.md)
> platformy Azure po[awarii z platformy Azure](vmware-azure-failback.md)
