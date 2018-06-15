---
title: Tryb failover, a kończyć się niepowodzeniem z powrotem maszyn wirtualnych funkcji Hyper-V replikowany do centrum danych w dodatkowej z usługą Site Recovery | Dokumentacja firmy Microsoft
description: Dowiedz się, jak działają maszyny wirtualne funkcji Hyper-V do lokacji dodatkowej lokalnymi i powrót po awarii do lokacji głównej, z usługą Azure Site Recovery
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 05/02/2018
ms.author: raynew
ms.openlocfilehash: ecb0b9395ce7071442ddf0dd976e1ca57b8be906
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
ms.locfileid: "34161145"
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-your-secondary-on-premises-site"></a>Tryb failover, a kończyć się niepowodzeniem z powrotem maszyn wirtualnych funkcji Hyper-V replikowany do lokacji dodatkowej lokalnymi

[Usługi Azure Site Recovery](site-recovery-overview.md) usługi zarządza i organizuje replikację, tryb failover i powrotu po awarii maszyn lokalnych i Azure maszynach wirtualnych (VM).

W tym artykule opisano sposób w tryb failover maszyny Wirtualnej funkcji Hyper-V zarządzane w chmurze programu System Center Virtual Machine Manager (VMM), do dodatkowej lokacji programu VMM. Po wprowadzeniu maszyny w tryb failover można przywrócić ją po awarii do lokacji lokalnej, gdy ta będzie znów dostępna. W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Tryb failover maszyny Wirtualnej funkcji Hyper-V z głównej chmury VMM do dodatkowej chmury VMM
> * Włącz ponownie ochronę z lokacji dodatkowej do podstawowych i wykonaj powrót po awarii
> * Opcjonalnie rozpocząć replikację z podstawowej do dodatkowej ponownie

## <a name="failover-and-failback"></a>Praca w trybie failover i powrót po awarii

Tryb failover i powrotu po awarii ma trzy etapy:

1. **Przełączyć do lokacji dodatkowej**: awaryjnie maszyny z lokacji podstawowej do lokacji dodatkowej.
2. **Niepowodzenie z lokacji dodatkowej**: replikowanie maszyn wirtualnych z pomocniczego podstawowym, a następnie uruchom planowany tryb failover do wykonaj powrót po awarii.
3. Po planowanego trybu failover opcjonalnie Uruchom replikacji z lokacji podstawowej do lokacji dodatkowej ponownie.


## <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że udało Ci się ukończyć [wyszczególniania odzyskiwania po awarii](hyper-v-vmm-test-failover.md) do sprawdzenia, czy wszystko działa zgodnie z oczekiwaniami.
- Aby ukończyć powrotu po awarii, upewnij się, czy podstawowego i pomocniczego serwera programu VMM są podłączone do usługi Site Recovery.



## <a name="run-a-failover-from-primary-to-secondary"></a>Uruchom pracy awaryjnej z podstawowej do dodatkowej

Możesz uruchomić zwykłe i planowanego trybu failover dla maszyn wirtualnych funkcji Hyper-V.

- Użyj regularne pracy awaryjnej dla wystąpienia nieoczekiwanych awarii. Po uruchomieniu tej pracy awaryjnej usługi Site Recovery tworzy maszyny Wirtualnej w lokacji dodatkowej i uprawnień go w górę. Oczekujące dane, które nie zostały zsynchronizowane w zależności od może wystąpić utrata danych.
- Planowany tryb failover może służyć konserwacji lub podczas oczekiwanych awarii. Ta opcja umożliwia zerowej utraty danych. Po wyzwoleniu planowanego trybu failover, źródłowe maszyny wirtualne są zamknięte. Niezsynchronizowane dane są synchronizowane i trybu failover zostanie wywołany. 
- 
W tej procedurze opisano sposób uruchamiania regularne trybu failover.


1. W obszarze **Ustawienia** > **Zreplikowane elementy** kliknij kolejno pozycje maszyna wirtualna > **Tryb failover**.
1. Wybierz **Zamknij maszynę przed rozpoczęciem pracy awaryjnej** Jeśli chcesz, aby usługi Site Recovery, aby spróbować wykonać zamknięcie źródłowe maszyny wirtualne przed wyzwolenie pracy awaryjnej. Usługa Site Recovery również spróbować zsynchronizować danych lokalnych, które jeszcze nie zostało wysłane do lokacji dodatkowej, aby mogło nastąpić wyzwolenie pracy awaryjnej. Należy pamiętać, że czy tryb failover będzie kontynuowane, nawet w przypadku zamknięcia nie powiedzie się. Na stronie **Zadania** można śledzić postęp trybu failover.
2. Teraz można wyświetlić maszynę Wirtualną w chmurze programu VMM dodatkowej.
3. Po zweryfikowaniu maszyny Wirtualnej, **zatwierdzić** pracy awaryjnej. To działanie usuwa wszystkie dostępne punkty odzyskiwania.

> [!WARNING]
> **Nie anuluj trybu failover po rozpoczęciu przełączania**: przed uruchomieniem trybu failover replikacja maszyny wirtualnej zostanie zatrzymana. Jeśli anulujesz tryb failover po rozpoczęciu przełączania, zostanie ono zatrzymane, ale maszyna wirtualna nie zostanie ponownie zreplikowana.  


## <a name="reverse-replicate-and-failover"></a>Replikacja odwrotna i trybu failover

Rozpoczęcia replikacji z lokacji dodatkowej do podstawowych i powrót po awarii do lokacji głównej. Po maszyny wirtualne są uruchomione ponownie w lokacji głównej, możesz replikować je do lokacji dodatkowej.  

 
1. Kliknij maszynę Wirtualną > kliknij **odwrócić replikację**.
2. Po zakończeniu zadania kliknij maszynę Wirtualną > w **pracy awaryjnej**, sprawdź kierunek pracy awaryjnej (od dodatkowej chmury VMM) i wybierz lokalizacja źródłowa i docelowa. 
4. Inicjuj tryb failover. Możesz śledzić postęp trybu failover **zadania** kartę.
5. W głównej chmury VMM Sprawdź, czy maszyna wirtualna jest dostępna.
6. Jeśli chcesz rozpocząć replikację podstawowej maszyny Wirtualnej z powrotem do lokacji dodatkowej ponownie polecenie **odwrócić replikację**.

## <a name="next-steps"></a>Kolejne kroki
[Przejrzyj kroku](hyper-v-vmm-disaster-recovery.md) replikowania maszyn wirtualnych funkcji Hyper-V z lokacją dodatkową.
