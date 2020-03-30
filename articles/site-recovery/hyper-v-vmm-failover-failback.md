---
title: Konfigurowanie pracy awaryjnej/powrotu po awarii do dodatkowej lokacji funkcji Hyper-V za pomocą usługi Azure Site Recovery
description: Dowiedz się, jak w pracy awaryjnej maszyn wirtualnych funkcji Hyper-V do lokacji dodatkowej lokalnej i po awarii z powrotem do lokacji głównej podczas odzyskiwania po awarii za pomocą usługi Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: d31355bcb0ce42874c19988738ba06138c7a0b7c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74082600"
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-your-secondary-on-premises-site"></a>Maszyny wirtualne funkcji Hyper V w wersji fail over i po awarii są replikowane do dodatkowej lokacji lokalnej

Usługa [Azure Site Recovery](site-recovery-overview.md) zarządza replikacją, trybem failover i powrotem po awarii maszyn lokalnych i maszyn wirtualnych platformy Azure oraz aranżuje ich replikację, tryb failover i powrót po awarii maszyn lokalnych i maszyn wirtualnych platformy Azure.

W tym artykule opisano, jak w trybie fail over maszyny Wirtualnej funkcji Hyper-V zarządzanej w chmurze Menedżera maszyn wirtualnych (VMM) w centrum systemu do dodatkowej lokacji programu VMM. Po wprowadzeniu maszyny w tryb failover można przywrócić ją po awarii do lokacji lokalnej, gdy ta będzie znów dostępna. W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Przełączenie maszyny wirtualnej funkcji Hyper-V z podstawowej chmury programu VMM do dodatkowej chmury programu VMM
> * Ponowne przetłaczanie z lokacji dodatkowej do lokacji podstawowej i powrót po awarii
> * Opcjonalnie ponownie rozpocznij replikację z podstawowej do pomocniczej

## <a name="failover-and-failback"></a>Praca w trybie failover i powrót po awarii

Trójmias awaryjny i powrót po awarii ma trzy etapy:

1. **Przeładuj awaryjnie do lokacji dodatkowej:** Komputery awaryjne z lokacji głównej do pomocniczego.
2. **Powrót po awarii z lokacji dodatkowej:** Replikuj maszyny wirtualne z pomocniczych do podstawowych i uruchom planowaną przekłędnie awaryjne, aby przywrócić po awarii.
3. Po planowanej pracy awaryjnej opcjonalnie ponownie rozpocznij replikację z lokacji głównej do pomocniczej.


## <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że ukończono [ćwiczenie odzyskiwania po awarii,](hyper-v-vmm-test-failover.md) aby sprawdzić, czy wszystko działa zgodnie z oczekiwaniami.
- Aby zakończyć powrót po awarii, upewnij się, że podstawowe i pomocnicze serwery programu VMM są połączone z funkcją Odzysk lokacji.



## <a name="run-a-failover-from-primary-to-secondary"></a>Uruchamianie pracy awaryjnej z podstawowej na pomocniczą

Można uruchomić regularne lub planowane pracy awaryjnej dla maszyn wirtualnych funkcji Hyper-V.

- Użyj zwykłej pracy awaryjnej w przypadku nieoczekiwanych awarii. Po uruchomieniu tego trybu failover, site recovery tworzy maszynę wirtualną w lokacji dodatkowej i zasila ją. Utrata danych może wystąpić w zależności od oczekujących danych, które nie zostały zsynchronizowane.
- Planowana przewijarnia awaryjna może służyć do konserwacji lub podczas oczekiwanej awarii. Ta opcja zapewnia zero utraty danych. Po wyzwoleniu planowanej pracy awaryjnej źródłowe maszyny wirtualne są zamykane. Niezsynchronizowane dane są synchronizowane, a funkcja failover jest wyzwalana. 
- 
  W tej procedurze opisano sposób uruchamiania zwykłej pracy awaryjnej.


1. W **ustawieniach** > **replikowane elementy** kliknij maszynę wirtualną > trybu **failover**.
1. Wybierz **zamknij komputer przed rozpoczęciem pracy awaryjnej,** jeśli chcesz, aby usługa Site Recovery próbowała wykonać zamknięcie źródłowych maszyn wirtualnych przed wyzwoleniem pracy awaryjnej. Usługa Site Recovery spróbuje również zsynchronizować dane lokalne, które nie zostały jeszcze wysłane do lokacji dodatkowej, przed wyzwoleniem pracy awaryjnej. Należy zauważyć, że tryb failover jest kontynuowany, nawet jeśli zamknięcie nie powiedzie się. Postęp pracy awaryjnej można śledzić na stronie **Zadania.**
2. Teraz powinieneś być w stanie zobaczyć maszynę wirtualną w dodatkowej chmurze programu VMM.
3. Po zweryfikowaniu maszyny Wirtualnej, **Zatwierdzanie** pracy awaryjnej. To działanie usuwa wszystkie dostępne punkty odzyskiwania.

> [!WARNING]
> **Nie anuluj trybu failover po rozpoczęciu przełączania**: przed uruchomieniem trybu failover replikacja maszyny wirtualnej zostanie zatrzymana. Jeśli anulujesz tryb failover po rozpoczęciu przełączania, zostanie ono zatrzymane, ale maszyna wirtualna nie zostanie ponownie zreplikowana.  


## <a name="reverse-replicate-and-failover"></a>Odwróć replikację i przerój w błąd

Rozpocznij replikowanie z lokacji dodatkowej do lokacji podstawowej i wróć po awarii do lokacji głównej. Po ponownym uruchomieniu maszyn wirtualnych w lokacji głównej można je zreplikować do lokacji dodatkowej.  

 
1. Kliknij maszynę wirtualną, > kliknij **przycisk Odwróć replikację**.
2. Po zakończeniu zadania kliknij maszynę wirtualną >w **pracy awaryjnej**, sprawdź kierunek pracy awaryjnej (z pomocniczej chmury programu VMM) i wybierz lokalizacje źródłowe i docelowe. 
4. Zainicjuj tryb failover. Na karcie **Zadania** można śledzić postęp trybu failover.
5. W podstawowego kontrolera VMM cloud sprawdź, czy maszyna wirtualna jest dostępna.
6. Jeśli chcesz ponownie rozpocząć replikowanie podstawowej maszyny Wirtualnej z powrotem do lokacji dodatkowej, kliknij **przycisk Odwróć replikację**.

## <a name="next-steps"></a>Następne kroki
[Przejrzyj krok](hyper-v-vmm-disaster-recovery.md) replikowania maszyn wirtualnych z programem Hyper-V do lokacji dodatkowej.
