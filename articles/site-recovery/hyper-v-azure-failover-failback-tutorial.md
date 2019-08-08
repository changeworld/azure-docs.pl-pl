---
title: Przechodzenie do trybu failover i powrót po awarii maszyn wirtualnych funkcji Hyper-V podczas odzyskiwania po awarii na platformie Azure za pomocą usługi Site Recovery | Microsoft Docs
description: Dowiedz się, jak przejść do trybu failover i powrócić po awarii maszyn wirtualnych funkcji Hyper-V podczas odzyskiwania po awarii na platformie Azure za pomocą usługi Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 08/07/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 4b9680b00905126d261562d7bec64bb931c1cda3
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2019
ms.locfileid: "68845709"
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-azure"></a>Przechodzenie do trybu failover i powrót po awarii maszyn wirtualnych funkcji Hyper-V replikowanych na platformie Azure

W tym samouczku omówiono sposób wprowadzania maszyny wirtualnej funkcji Hyper-V w tryb failover na platformie Azure. Po wprowadzeniu maszyny w tryb failover można przywrócić ją po awarii do lokacji lokalnej, gdy ta będzie znów dostępna. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Sprawdzanie właściwości maszyny Hyper-V pod kątem zgodności z wymaganiami platformy Azure
> * Przełączanie do trybu failover na platformie Azure
> * Powrót po awarii z platformy Azure do środowiska lokalnego
> * Odwrotna replikacja lokalnych maszyn wirtualnych w celu ponownego rozpoczęcia replikacji na platformie Azure

Jest to piąty samouczek z tej serii. Założono w nim, że zostały już wykonane zadania z poprzednich samouczków.    

1. [Przygotowywanie platformy Azure](tutorial-prepare-azure.md)
2. [Przygotowywanie lokalnej funkcji Hyper-V](tutorial-prepare-on-premises-hyper-v.md)
3. Konfigurowanie odzyskiwania po awarii dla [maszyn wirtualnych funkcji Hyper-V](tutorial-hyper-v-to-azure.md) lub dla [maszyn wirtualnych funkcji Hyper-V zarządzanych w chmurach programu System Center VMM](tutorial-hyper-v-vmm-to-azure.md)
4. [Uruchamianie próbnego odzyskiwania po awarii](tutorial-dr-drill-azure.md)

## <a name="prepare-for-failover-and-failback"></a>Przygotowanie do przełączenia w tryb failover i powrotu po awarii

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

## <a name="failover-to-azure"></a>Przełączanie do trybu failover na platformie Azure

1. W obszarze **Ustawienia** > **Zreplikowane elementy** kliknij kolejno pozycje maszyna wirtualna > **Tryb failover**.
2. W obszarze **Tryb failover** wybierz **Najnowszy** punkt odzyskiwania. 
3. Wybierz pozycję **Zamknij maszynę przed rozpoczęciem pracy w trybie failover**. Usługa Site Recovery próbuje zamknąć źródłowe maszyny wirtualne przed wyzwoleniem trybu failover. Przełączanie do trybu failover będzie kontynuowane, nawet jeśli zamknięcie nie powiedzie się. Na stronie **Zadania** można śledzić postęp trybu failover.
4. Po weryfikacji przełączenia do trybu failover kliknij przycisk **Zatwierdź**. To działanie usuwa wszystkie dostępne punkty odzyskiwania.

> [!WARNING]
> **Nie anuluj trybu failover, który jest w toku**: jeśli anulujesz tryb failover po rozpoczęciu przełączania, zostanie ono zatrzymane, ale maszyna wirtualna nie zostanie ponownie zreplikowana.

## <a name="failback-azure-vm-to-on-premises-and-reverse-replicate-the-on-premises-vm"></a>Powrót maszyn wirtualnych platformy Azure po awarii do środowiska lokalnego i odwrotna replikacja lokalnych maszyn wirtualnych

Powrót po awarii polega zasadniczo na przełączeniu maszyn wirtualnych platformy Azure w tryb failover do środowiska lokalnego, natomiast odwrotna replikacja to ponowne rozpoczęcie replikacji lokalnych maszyn wirtualnych na platformie Azure.

1. W obszarze **Ustawienia** > **Zreplikowane elementy** kliknij kolejno maszynę wirtualną i pozycję **Planowana praca w trybie failover**.
2. W obszarze **Potwierdź planowaną pracę w trybie failover** sprawdź kierunek przełączenia w tryb failover (z platformy Azure) i wybierz lokalizację źródłową oraz docelową.
3. Zaznacz pole **Synchronizuj dane przed przejściem do trybu failover (synchronizuj tylko zmiany różnicowe)** . Ta opcja minimalizuje czas przestoju maszyny wirtualnej, ponieważ synchronizacja jest prowadzona bez wyłączania maszyny wirtualnej.
4. Zainicjuj tryb failover. Na karcie **Zadania** można śledzić postęp trybu failover.
5. Po zakończeniu początkowej synchronizacji danych, gdy wszystko będzie gotowe do zamknięcia maszyn wirtualnych platformy Azure, kliknij pozycję **Zadania** > nazwa zadania planowanej pracy w trybie failover > **Zakończ pracę w trybie failover**. Spowoduje to zamknięcie maszyny wirtualnej platformy Azure, przekazanie najnowszych zmian do środowiska lokalnego i uruchomienie lokalnych maszyn wirtualnych.
6. Zaloguj się na lokalnej maszynie wirtualnej, aby sprawdzić, czy działa zgodnie z oczekiwaniami.
7. Lokalna maszyna wirtualna jest teraz w stanie **Oczekiwanie na zatwierdzenie**. Kliknij przycisk **Zatwierdź**. Spowoduje to usunięcie maszyn wirtualnych platformy Azure i ich dysków oraz przygotowanie lokalnych maszyn wirtualnych do odwrotnej replikacji.
Aby rozpocząć replikację lokalnych maszyn wirtualnych na platformę Azure, włącz funkcję **Replikacja odwrotna**. Zostanie uruchomiona replikacja zmian różnicowych, które miały miejsce od momentu wyłączenia maszyny wirtualnej platformy Azure.  
