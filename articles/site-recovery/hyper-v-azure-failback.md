---
title: Odzyskiwanie maszyn wirtualnych funkcji Hyper-V po awarii z platformy Azure za pomocą usługi Azure Site Recovery
description: Jak przywrócić maszyny wirtualne funkcji Hyper-V po awarii do lokacji lokalnej z platformy Azure za pomocą usługi Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 09/12/2019
ms.author: rajanaki
ms.openlocfilehash: 4b005ae308576db6fd26fcf079161430b266ec3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281784"
---
# <a name="run-a-failback-for-hyper-v-vms"></a>Uruchamianie powrotu po awarii dla maszyn wirtualnych funkcji Hyper-V

W tym artykule opisano, jak przywrócić maszyny wirtualne platformy Azure po awarii, które zostały utworzone po przeczesywaniu pracy awaryjnej maszyn wirtualnych funkcji Hyper-V z lokacji lokalnej na platformę Azure za pomocą [usługi Azure Site Recovery.](site-recovery-overview.md)

- Powrót maszyn wirtualnych funkcji Hyper-V po awarii z platformy Azure przez uruchomienie planowanego trybu failover z platformy Azure do lokacji lokalnej. Jeśli kierunek pracy awaryjnej jest z platformy Azure do lokalnego, jest uważany za powrót po awarii.
- Ponieważ platforma Azure jest środowiskiem o wysokiej dostępności, a maszyny wirtualne są zawsze dostępne, powrót po awarii z platformy Azure jest zaplanowanym działaniem. Można zaplanować małe przestoje, dzięki czemu obciążenia mogą ponownie rozpocząć uruchamianie lokalnie. 
- Planowane powiększanie po awarii wyłącza maszyny wirtualne na platformie Azure i pobiera najnowsze zmiany. Nie oczekuje się utraty danych.

## <a name="before-you-start"></a>Przed rozpoczęciem

1. [Przejrzyj typy powrotu po awarii,](failover-failback-overview.md#hyper-v-reprotectionfailback) których można użyć — odzyskiwanie oryginalnej lokalizacji i odzyskiwanie lokalizacji alternatywnej.
2. Upewnij się, że maszyny wirtualne platformy Azure używają konta magazynu, a nie dysków zarządzanych. Powrót po awarii maszyn wirtualnych funkcji Hyper-V replikowanych przy użyciu dysków zarządzanych nie jest obsługiwany.
3. Sprawdź, czy lokalny host funkcji Hyper-V (lub serwer programu System Center VMM, jeśli używasz z funkcją site recovery) jest uruchomiony i połączony z platformą Azure. 
4. Upewnij się, że pracy awaryjnej i zatwierdzania są kompletne dla maszyn wirtualnych. Nie trzeba konfigurowania żadnych określonych składników odzyskiwania witryny do powrotu po awarii maszyn wirtualnych funkcji Hyper-V z platformy Azure.
5. Czas potrzebny do ukończenia synchronizacji danych i uruchomienia lokalnej maszyny Wirtualnej będzie zależeć od wielu czynników. Aby przyspieszyć pobieranie danych, można skonfigurować agenta usług odzyskiwania firmy Microsoft do używania większej liczby wątków do równoległości pobierania. [Dowiedz się więcej](https://support.microsoft.com/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage).


## <a name="fail-back-to-the-original-location"></a>Powrót po awarii do pierwotnej lokalizacji

Aby przywrócić maszyny wirtualne funkcji Hyper-V na platformie Azure do oryginalnej lokalnej maszyny Wirtualnej, uruchom planowaną funkcję failover z platformy Azure do lokacji lokalnej w następujący sposób:

1. W przechowalni > **elementy replikowane**wybierz maszynę wirtualną. Kliknij prawym przyciskiem myszy maszynę wirtualną > **planowane trybu failover**. Jeśli plan odzyskiwania zakończy się niepowodzeniem, wybierz nazwę planu i kliknij pozycję **Przewijanie awaryjne** > **planowane trybu failover**.
2. W **opcji Potwierdź planowane przełącznie w stan failover**wybierz lokalizacje źródłowe i docelowe. Zwróć uwagę na kierunek pracy awaryjnej. Jeśli praca awaryjna z podstawowego pracował zgodnie z oczekiwaniami i wszystkie maszyny wirtualne znajdują się w lokalizacji pomocniczej jest to tylko informacje.
3. W **obszarze Synchronizacja danych**wybierz opcję:
    - **Synchronizowanie danych przed trybem failover (synchronizowanie tylko zmian różnicowych)**— ta opcja minimalizuje przestoje maszyn wirtualnych podczas synchronizacji bez zamykania ich.
        - **Faza 1:** Wykonuje migawkę maszyny Wirtualnej platformy Azure i kopiuje ją do lokalnego hosta funkcji Hyper-V. Komputer kontynuuje uruchamianie na platformie Azure.
        - **Faza 2:** Zamyka maszynę wirtualną platformy Azure, tak aby nie wystąpiły żadne nowe zmiany. Ostatni zestaw zmian delta jest przenoszony do serwera lokalnego i lokalna maszyna wirtualna jest uruchamiana.
    - **Synchronizuj dane tylko podczas pracy awaryjnej (pełne pobieranie)**— ta opcja jest szybsza, ponieważ zakładamy, że większość dysku uległa zmianie i nie chcemy spędzać czasu na obliczaniu sum kontrolnych. Ta opcja nie wykonuje żadnych obliczeń sumy kontrolnej.
        - Wykonuje pobieranie dysku. 
        - Zaleca się użycie tej opcji, jeśli używasz platformy Azure od jakiegoś czasu (miesiąc lub dłużej) lub jeśli lokalna maszyna wirtualna zostanie usunięta.

4. Tylko w przypadku programu VMM, jeśli szyfrowanie danych jest włączone dla chmury, w **kluczu szyfrowania**wybierz certyfikat, który został wystawiony po włączeniu szyfrowania danych podczas instalacji dostawcy na serwerze programu VMM.
5. Zainicjuj tryb failover. Na karcie **Zadania** można śledzić postęp trybu failover.
6. Jeśli wybrano opcję synchronizacji danych przed trybem failover, po zakończeniu początkowej synchronizacji danych i gotowości do zamknięcia maszyn wirtualnych na platformie Azure kliknij pozycję **Zadania** > nazwa zadania > **Pełne tryb failover**. Spowoduje to wykonanie następujących czynności:
    - Zamyka komputer platformy Azure.
    - Przenosi najnowsze zmiany do lokalnej maszyny Wirtualnej.
    - Uruchamia lokalną maszynę wirtualną.
7. Teraz można zalogować się do lokalnego komputera maszyn wirtualnych, aby sprawdzić, czy jest on dostępny zgodnie z oczekiwaniami.
8. Maszyna wirtualna jest w stanie oczekiwania na zatwierdzenie. Kliknij **przycisk Zaśliwij,** aby zatwierdzić przetwórt pracy awaryjnej.
9. Aby zakończyć powrót po awarii, kliknij przycisk **Odwróć replikację,** aby ponownie rozpocząć replikowanie lokalnej maszyny Wirtualnej na platformę Azure.



## <a name="fail-back-to-an-alternate-location"></a>Powrót po awarii do lokalizacji alternatywnej 

Powrót po awarii do lokalizacji alternatywnej w następujący sposób:

1. Jeśli konfigurujesz nowy sprzęt, zainstaluj [obsługiwaną wersję systemu Windows](hyper-v-azure-support-matrix.md#replicated-vms)i rolę funkcji Hyper-V na komputerze.
2. Utwórz przełącznik sieci wirtualnej o tej samej nazwie, co na oryginalnym serwerze.
3. **Protection Group** > \<W **obszarze ProtectionGroupGroupGroupGroupName** > > -> \<VirtualMachineName> wybierz maszynę wirtualną, którą chcesz przywrócić po awarii, a następnie wybierz opcję **Planowane tryb failover**.
4. W **trybie Confirm Planned Failover**s wybierz pozycję **Utwórz lokalną maszynę wirtualną, jeśli nie istnieje**.
5. W **obszarze Nazwa hosta**wybierz nowy serwer hosta funkcji Hyper-V, na którym chcesz umieścić maszynę wirtualną.
6. W **obszarze Synchronizacja danych**zaleca się wybranie opcji synchronizacji danych przed przełączeniem pracy awaryjnej. Minimalizuje to przestoje maszyn wirtualnych podczas synchronizacji bez ich zamykania. Wykonuje następujące czynności:
    - **Faza 1:** Wykonuje migawkę maszyny Wirtualnej platformy Azure i kopiuje ją do lokalnego hosta funkcji Hyper-V. Komputer kontynuuje uruchamianie na platformie Azure.
    - **Faza 2:** Zamyka maszynę wirtualną platformy Azure, tak aby nie wystąpiły żadne nowe zmiany. Ostatni zestaw zmian jest przenoszony na serwer lokalny, a lokalna maszyna wirtualna jest uruchamiana.
    
7. Kliknij znacznik wyboru, aby rozpocząć przejście w stan failover (powrót po awarii).
8. Po zakończeniu synchronizacji początkowej i gotowości do zamknięcia maszyny Wirtualnej platformy Azure kliknij pozycję **Zadania** > \<planowane zadanie pracy awaryjnej> > **Zakończenie pracy awaryjnej**. Spowoduje to zamknięcie komputera platformy Azure, przeniesienie najnowszych zmian do lokalnej maszyny Wirtualnej i uruchomienie jej.
9. Możesz zalogować się do lokalnej maszyny Wirtualnej, aby sprawdzić, czy wszystko działa zgodnie z oczekiwaniami.
10. Kliknij **przycisk Zatwierdź,** aby zakończyć przetwórt pracy awaryjnej. Zatwierdzanie usuwa maszynę wirtualną platformy Azure i jej dyski i przygotowuje lokalną maszynę wirtualną, która ma być ponownie chroniona.
10. Kliknij **przycisk Odwróć replikację,** aby rozpocząć replikowanie lokalnej maszyny Wirtualnej na platformę Azure. Tylko zmiany różnicowe, ponieważ maszyna wirtualna została wyłączona na platformie Azure będą replikowane.

    > [!NOTE]
    > Jeśli anulujesz zadanie powrotu po awarii podczas synchronizacji danych, lokalna maszyna wirtualna będzie w stanie uszkodzonym. Jest tak, ponieważ synchronizacja danych kopiuje najnowsze dane z dysków maszyn wirtualnych platformy Azure do lokalnych dysków danych i do czasu zakończenia synchronizacji dane dysku mogą nie być w spójnym stanie. Jeśli lokalna maszyna wirtualna zostanie uruchomiony po anulowaniu synchronizacji danych, może nie zostać uruchomiony. W takim przypadku uruchom ponownie pracy awaryjnej, aby zakończyć synchronizację danych.


## <a name="next-steps"></a>Następne kroki
Po lokalnej maszynie wirtualnej jest replikowanie na platformie Azure, można [uruchomić inny pracy awaryjnej](site-recovery-failover.md) na platformie Azure w razie potrzeby.
