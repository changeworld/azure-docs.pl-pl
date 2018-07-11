---
title: Tryb failover i powrót po awarii maszyn wirtualnych funkcji Hyper-V, replikowanych do platformy Azure z usługą Site Recovery | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przełączać awaryjnie maszyny wirtualne funkcji Hyper-V, na platformie Azure i powrót po awarii do lokacji lokalnej, usługa Azure Site Recovery
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: raynew
ms.openlocfilehash: f758939964045ed373703a211d4cbef00f0e42e7
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2018
ms.locfileid: "37919550"
---
# <a name="failover-and-failback-hyper-v-vms-replicated-to-azure"></a>Tryb failover i powrotu po awarii maszyn wirtualnych funkcji Hyper-V replikowanych na platformie Azure

W tym samouczku opisano sposób trybu failover maszyny Wirtualnej funkcji Hyper-V do platformy Azure. Po przełączeniu w tryb failover, można przeprowadzić powrotu po awarii do lokacji lokalnej po udostępnieniu. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Sprawdź, czy właściwości maszyny Wirtualnej funkcji Hyper-V, aby sprawdzić są zgodne z wymaganiami platformy Azure
> * Przełączanie do trybu failover na platformie Azure
> * Powrót po awarii z platformy Azure do środowiska lokalnego
> * Replikacja odwrotna lokalnych maszyn wirtualnych, aby uruchomić ponownie replikację do platformy Azure

W tym samouczku jest to piąty samouczek z serii. Przyjęto założenie, że zostały już wykonane zadania z poprzednich samouczków.    

1. [Przygotowywanie platformy Azure](tutorial-prepare-azure.md)
2. [Przygotowywanie lokalnej funkcji Hyper-V](tutorial-prepare-on-premises-hyper-v.md)
3. Konfigurowanie odzyskiwania po awarii dla [maszyn wirtualnych funkcji Hyper-V](tutorial-hyper-v-to-azure.md), lub [maszyn wirtualnych funkcji Hyper-V zarządzane w chmurach programu System Center VMM](tutorial-hyper-v-vmm-to-azure.md)
4. [Uruchamianie próbnego odzyskiwania po awarii](tutorial-dr-drill-azure.md)

## <a name="prepare-for-failover-and-failback"></a>Przygotowanie do trybu failover i powrotu po awarii

Upewnij się, że na maszynie Wirtualnej nie ma żadnych migawek i czy lokalna maszyna wirtualna została wyłączona podczas powrotu po awarii. Pomaga zapewnić spójność danych podczas replikacji. Nie włączysz lokalnej maszyny Wirtualnej podczas powrotu po awarii. 

Tryb failover i powrotu po awarii są trzy etapy:

1. **Tryb failover na platformie Azure**: maszyny wirtualne funkcji Hyper-V trybu Failover z lokacji lokalnej do platformy Azure.
2. **Powrót po awarii do środowiska lokalnego**: maszyny wirtualne platformy Azure w tryb Failover do lokacji lokalnej po udostępnieniu lokacji lokalnej. Uruchamia, synchronizowanie danych z platformy Azure do środowiska lokalnego i po zakończeniu, zapewnia maszyn wirtualnych lokalnie.  
3. **Replikacja odwrotna lokalnych maszyn wirtualnych**: po nie udało się ponownie w środowisku lokalnym, odwrotnej replikacji lokalnych maszyn wirtualnych, aby rozpocząć replikowanie ich na platformę Azure.

## <a name="verify-vm-properties"></a>Sprawdzanie właściwości maszyn wirtualnych

Przed włączeniem trybu failover Sprawdź właściwości maszyny Wirtualnej i upewnij się, że maszyna wirtualna spełnia z [wymagania dotyczące usługi Azure](hyper-v-azure-support-matrix.md#replicated-vms).

W obszarze **Chronione elementy** kliknij kolejno pozycje **Zreplikowane elementy** > Maszyna wirtualna.

2. Okienko **Zreplikowany element** zawiera podsumowanie informacji na temat maszyny wirtualnej, jej kondycję oraz najnowsze dostępne punkty odzyskiwania. Kliknij przycisk **Właściwości**, aby wyświetlić więcej szczegółów.

3. W obszarze **Obliczenia i sieć** można zmodyfikować nazwę platformy Azure, grupę zasobów, rozmiar docelowy, [zestaw dostępności](../virtual-machines/windows/tutorial-availability-sets.md) i ustawienia dysku zarządzanego.

4. Możesz wyświetlać i modyfikować ustawienia sieciowe, w tym sieć/podsieć, w której zlokalizowana będzie maszyna wirtualna na platformie Azure po wejściu w tryb failover, oraz adres IP, który będzie do niej przypisany.

5. W obszarze **Dyski** można znaleźć informacje dotyczące systemu operacyjnego i dysków danych maszyny wirtualnej.

## <a name="failover-to-azure"></a>Tryb failover na platformie Azure

1. W **ustawienia** > **zreplikowane elementy**, kliknij maszynę Wirtualną > **trybu Failover**.
2. W **trybu Failover**, wybierz opcję **najnowsze** punktu odzyskiwania. 
3. Wybierz pozycję **Zamknij maszynę przed rozpoczęciem pracy w trybie failover**. Usługa Site Recovery próbuje przeprowadzić zamknięcie źródłowych maszyn wirtualnych przed wyzwoleniem trybu failover. Przełączanie do trybu failover będzie kontynuowane, nawet jeśli zamknięcie nie powiedzie się. Na stronie **Zadania** można śledzić postęp trybu failover.
4. Po upewnieniu się, tryb failover, kliknij przycisk **zatwierdzić**. Usuwa wszystkie dostępne punkty odzyskiwania.

> [!WARNING]
> **Nie Anuluj trybu failover w toku**: Anulowanie w toku, zostanie ono zatrzymane, ale maszyna wirtualna nie zostanie ponownie zreplikowana.

## <a name="failback-azure-vm-to-on-premises-and-reverse-replicate-the-on-premises-vm"></a>Maszyny Wirtualnej platformy Azure podczas powrotu po awarii do środowiska lokalnego i odwrotnej replikacji lokalnej maszyny Wirtualnej

Operacja powrotu po awarii jest zasadniczo przejściu w tryb failover z platformy Azure do lokacji lokalnej i w replikacja odwrotna ponownie uruchamia replikację maszyn wirtualnych z lokacji lokalnej do platformy Azure.

1. W **ustawienia** > **zreplikowane elementy**, kliknij maszynę Wirtualną > **planowanego trybu Failover**.
2. W **potwierdzić planowanego trybu Failover**Sprawdź wybrano kierunek trybu failover (z platformy Azure), a wybierz lokalizacje źródłowe i docelowe.
3. Wybierz **synchronizować dane przed włączeniem trybu failover (należy zsynchronizować tylko zmiany różnicowe)**. Ta opcja minimalizuje przestoju maszyny Wirtualnej, ponieważ synchronizuje bez zamykania maszyny Wirtualnej.
4. Zainicjuj tryb failover. Można śledzić postęp trybu failover **zadań** kartę.
5. Po początkowej danych odbywa się synchronizacja i wszystko jest gotowe do zamknięcia kliknij maszyn wirtualnych platformy Azure **zadania** > planowanej pracy awaryjnej zadania nazwa-> **zakończenie pracy awaryjnej**. Jej zamknięcie maszyny Wirtualnej platformy Azure, przesyła najnowsze zmiany w środowisku lokalnym i uruchamia lokalną maszynę Wirtualną.
6. Zaloguj się do lokalnej maszyny Wirtualnej w celu sprawdzenia, czy jest on dostępny, zgodnie z oczekiwaniami.
7. Lokalna maszyna wirtualna jest teraz w **zatwierdzania oczekujących** stanu. Kliknij przycisk **zatwierdzić**. Usuwa maszyny wirtualne platformy Azure i jej dysków i przygotowuje lokalną maszynę Wirtualną do replikacji odwrotnej.
Aby rozpocząć replikacji lokalnej maszyny Wirtualnej na platformie Azure, należy włączyć **replikacja odwrotna**. Wyzwala Replikacja zmian różnicowych, które miały miejsce od maszyny Wirtualnej platformy Azure został wyłączony.  
