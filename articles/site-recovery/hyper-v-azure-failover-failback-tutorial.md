---
title: Konfigurowanie trybu failover maszyn wirtualnych funkcji Hyper-V na platformie Azure w usłudze Azure Site Recovery
description: Dowiedz się, jak w pracy w stanie fail over Maszyny wirtualne funkcji Hyper-V na platformie Azure za pomocą usługi Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 12/16/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 03826abf6da94859c510f4c127dfce035aa79370
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75498161"
---
# <a name="fail-over-hyper-v-vms-to-azure"></a>Maszyny wirtualne funkcji Hyper V w wersji Fail-V w obszarze Azure

W tym samouczku opisano, jak awaryjnie przeczesywać maszyny wirtualne z programem Hyper V na platformie Azure za pomocą [usługi Azure Site Recovery.](site-recovery-overview.md) Po wprowadzeniu maszyny w tryb failover można przywrócić ją po awarii do lokacji lokalnej, gdy ta będzie znów dostępna. Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Sprawdź właściwości maszyny Wirtualnej funkcji Hyper-V, aby sprawdzić zgodność z wymaganiami platformy Azure.
> * Przebłaśnia określonych maszyn wirtualnych w wersji Azure w wersji fail0.


Jest to piąty samouczek z tej serii. Założono w nim, że zostały już wykonane zadania z poprzednich samouczków.    

1. [Przygotowywanie platformy Azure](tutorial-prepare-azure.md)
2. [Przygotowywanie lokalnej funkcji Hyper-V](tutorial-prepare-on-premises-hyper-v.md)
3. Konfigurowanie odzyskiwania po awarii dla [maszyn wirtualnych funkcji Hyper-V](tutorial-hyper-v-to-azure.md) lub dla [maszyn wirtualnych funkcji Hyper-V zarządzanych w chmurach programu System Center VMM](tutorial-hyper-v-vmm-to-azure.md)
4. [Uruchamianie próbnego odzyskiwania po awarii](tutorial-dr-drill-azure.md)

[Dowiedz się więcej o](failover-failback-overview.md#types-of-failover) różnych typach pracy awaryjnej. Jeśli chcesz przebiec awaryjnie wiele maszyn wirtualnych w planie odzyskiwania, zapoznaj się z [tym artykułem](site-recovery-failover.md).

## <a name="prepare-for-failover"></a>Przygotowanie do przejścia do trybu failover 
Upewnij się, że na maszynie wirtualnej nie znajdują się żadne migawki oraz że podczas powrotu po awarii lokalna maszyna wirtualna jest wyłączona. Pomaga to zapewnić spójność danych podczas replikacji. Nie należy włączać lokalnej maszyny wirtualnej podczas powrotu po awarii. 

Przełączanie do trybu failover i powrót po awarii odbywa się w trzech etapach:

1. **Przejście do trybu failover na platformie Azure**: przełączenie maszyn wirtualnych funkcji Hyper-V w tryb failover, z lokacji lokalnej na platformę Azure.
2. **Powrót po awarii do środowiska lokalnego**: przełączenie maszyn wirtualnych platformy Azure w tryb failover do lokacji lokalnej, gdy będzie ona już dostępna. Na tym etapie rozpoczyna się synchronizacja danych z platformy Azure do środowiska lokalnego, a po jej zakończeniu uruchamiane są lokalne maszyny wirtualne.  
3. **Odwrotna replikacja lokalnych maszyn wirtualnych**: po powrocie po awarii do środowiska lokalnego następuje odwrotna replikacja lokalnych maszyn wirtualnych w celu ponownego rozpoczęcia replikacji na platformie Azure.

## <a name="verify-vm-properties"></a>Sprawdzanie właściwości maszyn wirtualnych

Przed przełączeniem do trybu failover sprawdź właściwości maszyny wirtualnej i upewnij się, że maszyna wirtualna spełnia [wymagania platformy Azure](hyper-v-azure-support-matrix.md#replicated-vms).

W obszarze **Chronione elementy** kliknij kolejno pozycje **Zreplikowane elementy** > Maszyna wirtualna.

1. Okienko **Zreplikowany element** zawiera podsumowanie informacji na temat maszyny wirtualnej, jej kondycję oraz najnowsze dostępne punkty odzyskiwania. Kliknij przycisk **Właściwości**, aby wyświetlić więcej szczegółów.

1. W obszarze **Obliczenia i sieć** można zmodyfikować nazwę platformy Azure, grupę zasobów, rozmiar docelowy, [zestaw dostępności](../virtual-machines/windows/tutorial-availability-sets.md) i ustawienia dysku zarządzanego.

1. Możesz wyświetlać i modyfikować ustawienia sieciowe, w tym sieć/podsieć, w której zlokalizowana będzie maszyna wirtualna na platformie Azure po wejściu w tryb failover, oraz adres IP, który będzie do niej przypisany.

1. W obszarze **Dyski** można znaleźć informacje dotyczące systemu operacyjnego i dysków danych maszyny wirtualnej.

## <a name="fail-over-to-azure"></a>Przełączenie do trybu failover na platformie Azure

1. W **obszarze Ustawienia** > **replikowane elementy**kliknij maszynę wirtualną > trybu **failover**.
2. W obszarze **Tryb failover** wybierz **Najnowszy** punkt odzyskiwania. 
3. Wybierz pozycję **Zamknij maszynę przed rozpoczęciem pracy w trybie failover**. Usługa Site Recovery próbuje zamknąć źródłowe maszyny wirtualne przed wyzwoleniem trybu failover. Przełączanie do trybu failover będzie kontynuowane, nawet jeśli zamknięcie nie powiedzie się. Postęp pracy awaryjnej można śledzić na stronie **Zadania.**
4. Po weryfikacji przełączenia do trybu failover kliknij przycisk **Zatwierdź**. To działanie usuwa wszystkie dostępne punkty odzyskiwania.

> [!WARNING]
> **Nie należy anulować trybu failover po rozpoczęciu przełączania**: w takim przypadku przełączanie zostanie zatrzymane, ale maszyna wirtualna nie zostanie ponownie zreplikowana.

## <a name="connect-to-failed-over-vm"></a>Łączenie się z maszyną wirtualną po awarii

1. Jeśli chcesz połączyć się z maszynami wirtualnymi platformy Azure po przełączeniu w stan failover przy użyciu protokołu RDP (Remote Desktop Protocol) i secure shell (SSH), [sprawdź, czy wymagania zostały spełnione.](failover-failback-overview.md#connect-to-azure-after-failover)
2. Po przełączeniu w stan failover przejdź do maszyny Wirtualnej i sprawdź poprawność, [łącząc się z](../virtual-machines/windows/connect-logon.md) nią.
3. Użyj **zmień punktu odzyskiwania,** jeśli chcesz użyć innego punktu odzyskiwania po pracy awaryjnej. Po zatwierdzeniu pracy awaryjnej w następnym kroku ta opcja nie będzie już dostępna.
4. Po weryfikacji wybierz **commit,** aby sfinalizować punkt odzyskiwania maszyny Wirtualnej po przewijanie w stan failover.
5. Po zatwierdzeniu wszystkie inne dostępne punkty odzyskiwania zostaną usunięte. Ten krok kończy pracy awaryjnej.

>[!TIP]
> Jeśli po przejściu w stan failover wystąpią jakiekolwiek problemy z łącznością, postępuj zgodnie z [przewodnikiem rozwiązywania problemów](site-recovery-failover-to-azure-troubleshoot.md).


## <a name="next-steps"></a>Następne kroki

Po przewijaniu w błąd ponownie przeceniaj maszyny wirtualne platformy Azure, aby replikować z platformy Azure do lokalnego. Następnie po maszyny wirtualne są ponownie cekrowane i replikacji do lokacji lokalnej, powrót po awarii z platformy Azure, gdy jesteś gotowy.
