---
title: Tryb failover i powrót po awarii maszyn wirtualnych funkcji Hyper-V, replikowane do dodatkowego centrum danych podczas odzyskiwania po awarii przy użyciu usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przełączyć w tryb failover maszyny wirtualne funkcji Hyper-V do lokacji dodatkowej w środowisku lokalnym i powrócić po awarii lokacji głównej podczas odzyskiwania po awarii przy użyciu usługi Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: raynew
ms.openlocfilehash: 39b2e4f37abe77439410fa4a83e06a0ca7941787
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66397992"
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-your-secondary-on-premises-site"></a>Tryb failover i powrót po awarii maszyn wirtualnych funkcji Hyper-V replikowany do lokacji dodatkowej w środowisku lokalnym

[Usługi Azure Site Recovery](site-recovery-overview.md) usługa zarządza i organizuje replikację, tryb failover i powrotu po awarii maszyn lokalnych i maszyn wirtualnych (VM).

W tym artykule opisano sposób awaryjnego przełączania maszyny Wirtualnej funkcji Hyper-V zarządzane w chmurze programu System Center Virtual Machine Manager (VMM) do lokacji dodatkowej programu VMM. Po wprowadzeniu maszyny w tryb failover można przywrócić ją po awarii do lokacji lokalnej, gdy ta będzie znów dostępna. W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Tryb failover maszyny Wirtualnej funkcji Hyper-V z chmury programu VMM podstawowej do dodatkowej chmury VMM
> * Ponowne włączanie ochrony z lokacji dodatkowej do podstawowej i powrót po awarii
> * Opcjonalnie uruchomić replikację z podstawowej do dodatkowej ponownie

## <a name="failover-and-failback"></a>Praca w trybie failover i powrót po awarii

Tryb failover i powrotu po awarii ma trzy etapy:

1. **Do lokacji dodatkowej pracy awaryjnej**: Pracy awaryjnej maszyn z lokacji głównej do regionu pomocniczego.
2. **Powrót po awarii z lokacji dodatkowej**: Replikowanie maszyn wirtualnych z dodatkowej do głównej, a następnie uruchomić planowanego trybu failover do powrotu po awarii.
3. Po włączeniu planowanego trybu failover opcjonalnie Uruchom replikacji z lokacji podstawowej do pomocniczej ponownie.


## <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że zostały wykonane [próbnego odzyskiwania po awarii](hyper-v-vmm-test-failover.md) do sprawdzenia, czy wszystko działa zgodnie z oczekiwaniami.
- Aby wykonać powrót po awarii, upewnij się, że podstawowych i pomocniczych serwerów programu VMM są podłączone do odzyskiwania lokacji.



## <a name="run-a-failover-from-primary-to-secondary"></a>Uruchamianie trybu failover z podstawowej do dodatkowej

Możesz uruchomić regularnie lub planowanego trybu failover dla maszyn wirtualnych funkcji Hyper-V.

- Regularne przejścia w tryb failover na użytek awarii. Po uruchomieniu tego rodzaju tryb failover, Site Recovery umożliwia utworzenie maszyny Wirtualnej w lokacji dodatkowej i obsługuje go w górę. Oczekujące dane, które nie zostały zsynchronizowane w zależności od może wystąpić utrata danych.
- Planowanego trybu failover może służyć w celu przeprowadzenia konserwacji lub w trakcie oczekiwanego awarii. Ta opcja zapewnia zerową utratę danych. Po wyzwoleniu planowanego trybu failover, źródłowe maszyny wirtualne są zamknięte. Niezsynchronizowane dane są synchronizowane, a następnie przełączenie w tryb failover zostanie wywołany. 
- 
  Ta procedura opisuje sposób regularnego tryb failover.


1. W obszarze **Ustawienia** > **Zreplikowane elementy** kliknij kolejno pozycje maszyna wirtualna > **Tryb failover**.
1. Wybierz **Zamknij maszynę przed rozpoczęciem pracy awaryjnej** Jeśli chcesz, aby Usługa Site Recovery ma spróbować przeprowadzić zamknięcie źródłowych maszyn wirtualnych przed wyzwoleniem trybu failover. Usługa Site Recovery również podejmie próbę synchronizacji danych w środowisku lokalnym, który jeszcze nie został wysłany do lokacji dodatkowej przed wyzwoleniem trybu failover. Należy pamiętać, że tryb failover będzie kontynuowane, nawet jeśli zamknięcie nie powiedzie się. Na stronie **Zadania** można śledzić postęp trybu failover.
2. Teraz można wyświetlić maszyny Wirtualnej w chmurze programu VMM dodatkowej.
3. Po upewnieniu się maszyna wirtualna, **zatwierdzić** przełączenie w tryb failover. To działanie usuwa wszystkie dostępne punkty odzyskiwania.

> [!WARNING]
> **Nie anuluj trybu failover, który jest w toku**: Przed rozpoczęciem pracy w trybie failover zatrzymywana jest replikacja maszyny wirtualnej. Jeśli anulujesz tryb failover po rozpoczęciu przełączania, zostanie ono zatrzymane, ale maszyna wirtualna nie zostanie ponownie zreplikowana.  


## <a name="reverse-replicate-and-failover"></a>Replikacja odwrotna i trybu failover

Uruchamianie replikacji z lokacji dodatkowej do podstawowej i powrót po awarii do lokacji głównej. Po maszyny wirtualne są uruchomione ponownie w lokacji głównej, możesz replikować je do lokacji dodatkowej.  

 
1. Kliknij maszynę Wirtualną > kliknij **replikacja odwrotna**.
2. Po zakończeniu zadania, kliknij maszynę Wirtualną > w **trybu Failover**Sprawdź wybrano kierunek trybu failover (z dodatkowych chmury programu VMM), a wybierz lokalizacje źródłowe i docelowe. 
4. Zainicjuj tryb failover. Na karcie **Zadania** można śledzić postęp trybu failover.
5. W chmurze podstawowej programu VMM Sprawdź, czy maszyna wirtualna jest dostępna.
6. Jeśli chcesz uruchomić ponownie replikacji podstawowej maszyny Wirtualnej do lokacji dodatkowej, polecenie **replikacja odwrotna**.

## <a name="next-steps"></a>Kolejne kroki
[Przejrzyj kroku](hyper-v-vmm-disaster-recovery.md) replikowania maszyn wirtualnych funkcji Hyper-V do lokacji dodatkowej.
