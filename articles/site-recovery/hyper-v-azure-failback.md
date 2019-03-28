---
title: Uruchamianie powrotu po awarii podczas po awarii maszyn wirtualnych funkcji Hyper-v na platformie Azure do środowiska lokalnego | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zakończyć się niepowodzeniem maszynami wirtualnymi funkcji Hyper-V z powrotem do lokacji lokalnej podczas odzyskiwania po awarii na platformie Azure przy użyciu usługi Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: 4030b1905f8d5b50ef6be3ffa61eda74d8a27951
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541056"
---
# <a name="run-a-failback-for-hyper-v-vms"></a>Uruchamianie powrotu po awarii dla maszyn wirtualnych funkcji Hyper-V

W tym artykule opisano, jak niepowodzenie wstecz funkcji Hyper-V maszyny wirtualne chronione przez usługę Site Recovery.

## <a name="prerequisites"></a>Wymagania wstępne
1. Upewnij się, że znasz szczegółowe informacje o [różnego rodzaju powrotu po awarii](concepts-types-of-failback.md) i odpowiedniego ostrzeżenia.
1. Upewnij się, że serwer VMM w lokacji głównej lub serwerze hosta funkcji Hyper-V jest podłączony do platformy Azure.
2. Zostało przeprowadzone **zatwierdzić** na maszynie wirtualnej.

## <a name="perform-failback"></a>Wykonać powrót po awarii
Po przejściu w tryb failover z serwera podstawowego do lokalizacji dodatkowej replikowane maszyny wirtualne nie są chronione przez usługę Site Recovery i lokalizacji pomocniczej obecnie działa jako aktywną lokalizacją. Niepowodzenie tworzenia kopii maszyn wirtualnych w planie odzyskiwania, uruchomić planowanego trybu failover z lokacji dodatkowej do podstawowej, w następujący sposób. 
1. Wybierz **plany odzyskiwania** > *recoveryplan_name*. Kliknij przycisk **trybu Failover** > **zaplanowanym powrocie po awarii**.
2. Na **potwierdzić planowanego trybu Failover** wybierz lokalizacje źródłowe i docelowe. Należy pamiętać, wybrano kierunek trybu failover. W przypadku pracy awaryjnej z podstawowej pracował jako oczekują, i wszystkie maszyny wirtualne znajdują się w lokalizacji dodatkowej, jest to wyłącznie w celach informacyjnych.
3. W przypadku przechodzenia powrót po awarii z platformy Azure wybierz ustawienia w **synchronizacji danych**:
    - **Synchronizowanie danych przed włączeniem trybu failover (tylko zmiany różnicowe Synchronize)**— tę opcję, minimalizuje czas przestoju w przypadku maszyn wirtualnych, jak synchronizuje się bez ich zamykania. Go wykonuje następujące czynności:
        - Faza 1: Pobiera migawkę maszyny wirtualnej na platformie Azure i kopiuje go do hosta funkcji Hyper-V w środowisku lokalnym. Komputer nadal działających na platformie Azure.
        - Faza 2: Spowoduje to nie nowych zmian zamknięcie maszyny wirtualnej na platformie Azure. Ostateczny zestaw zmian różnicowych jest przekazywany do lokalnego serwera i uruchomienia maszyny wirtualnej w środowisku lokalnym.

    - **Synchronizowanie danych podczas pracy awaryjnej tylko (pełne pobieranie)**— ta opcja jest szybsze.
        - Ta opcja jest szybsze, ponieważ oczekuje się, że większość dysku został zmieniony i nie chcemy poświęcić czas Obliczanie sumy kontrolnej. Wykonuje pobierania dysku. Jest również przydatne, gdy maszyna wirtualna w warstwie Premium zostały usunięte.
        - Firma Microsoft zaleca, użyj tej opcji, jeśli działała Azure od pewnego czasu (miesiąc lub więcej) lub lokalnej maszyny wirtualnej zostało usunięte. Ta opcja nie wykonuje żadnych obliczania sumy kontrolnej.


4. Jeśli szyfrowanie danych jest włączone dla chmury, w **klucza szyfrowania** wybierz certyfikat, który został wystawiony, gdy włączone jest szyfrowanie danych podczas instalacji dostawcy na serwerze programu VMM.
5. Zainicjuj tryb failover. Na karcie **Zadania** można śledzić postęp trybu failover.
6. Jeśli została wybrana opcja synchronizowania danych przed włączeniem trybu failover, po ukończeniu synchronizacji początkowej danych i wszystko będzie gotowe zamknąć maszyny wirtualne na platformie Azure, kliknij przycisk **zadania** > Nazwa zadania >  **Przejdź w tryb Failover**. Zamyka maszyny platformy Azure, przesyła najnowsze zmiany z maszyną wirtualną w środowisku lokalnym i uruchamia lokalną maszynę Wirtualną.
7. Możesz teraz Zaloguj się do maszyny wirtualnej, aby zweryfikować, czy jest dostępna, zgodnie z oczekiwaniami.
8. Maszyna wirtualna jest w stanie oczekiwania zatwierdzeniu. Kliknij przycisk **zatwierdzenia** do zatwierdzenia pracy w trybie failover.
9. Aby sfinalizować powrotu po awarii, kliknij **replikacja odwrotna** objąć ochroną maszynę wirtualną w lokacji głównej.


Postępuj zgodnie z poniższymi procedurami, aby powrócić po awarii do oryginalnej lokacji głównej. Ta procedura opisuje sposób uruchomić planowanego trybu failover planu odzyskiwania. Alternatywnie można uruchomić tryb failover dla pojedynczej maszyny wirtualnej na **maszyn wirtualnych** kartę.


## <a name="failback-to-an-alternate-location-in-hyper-v-environment"></a>Powrót po awarii do innej lokalizacji w środowisku funkcji Hyper-V
Jeśli udało Ci się wdrożyć ochrony między [lokacją funkcji Hyper-V i platformą Azure](site-recovery-hyper-v-site-to-azure.md) masz możliwość powrotu po awarii z platformy Azure do lokalizacji alternatywnej w środowisku lokalnym. Jest to przydatne, jeśli chcesz skonfigurować nowego sprzętu w środowisku lokalnym. Oto, jak to zrobić.

1. Jeśli konfigurujesz nowy sprzęt systemu Windows Server 2012 R2 i roli Hyper-V należy zainstalować na serwerze.
2. Utwórz przełącznik sieci wirtualnej o tej samej nazwie, która była na oryginalnym serwerze.
3. Wybierz **chronione elementy** -> **grupy ochrony** -> \<ProtectionGroupName > -> \<VirtualMachineName > aby powrót po awarii, i wybierz **planowanego trybu Failover**.
4. W **potwierdzić planowanego trybu Failover** wybierz **Utwórz lokalną maszynę wirtualną, jeśli nie istnieje**.
5. W polu Nazwa hosta ** wybierz nowy serwer hosta funkcji Hyper-V, na którym chcesz umieścić maszyny wirtualnej.
6. W synchronizacji danych zaleca się, że wybierasz opcję, aby synchronizować dane przed włączeniem trybu failover. Minimalizuje czas przestoju w przypadku maszyn wirtualnych, jak synchronizuje się bez ich zamykania. Wykonuje następujące zadania:

    - Faza 1: Pobiera migawkę maszyny wirtualnej na platformie Azure i kopiuje go do hosta funkcji Hyper-V w środowisku lokalnym. Komputer nadal działających na platformie Azure.
    - Faza 2: Spowoduje to nie nowych zmian zamknięcie maszyny wirtualnej na platformie Azure. Ostateczny zestaw zmian jest przekazywany do lokalnego serwera i uruchomienia maszyny wirtualnej w środowisku lokalnym.
    
7. Kliknij znacznik wyboru, aby rozpocząć tryb failover (powrót po awarii).
8. Po zakończeniu początkowej synchronizacji i wszystko będzie gotowe, zamknij maszynę wirtualną na platformie Azure, kliknij przycisk **zadania** > \<zadania planowanego trybu failover >> **zakończenie pracy awaryjnej** . Zamyka maszyny platformy Azure, przesyła najnowsze zmiany z maszyną wirtualną w środowisku lokalnym i uruchamia go.
9. Możesz zalogować się do maszyny wirtualnej w środowisku lokalnym, aby sprawdzić, czy wszystko działa zgodnie z oczekiwaniami. Następnie kliknij przycisk **zatwierdzić** na zakończenie pracy w trybie failover. Zatwierdzenie powoduje usunięcie maszyny wirtualnej platformy Azure i jej dysków i przygotowuje maszynę Wirtualną, która ma być ponownie chroniony.
10. Kliknij przycisk **replikacja odwrotna** aby rozpocząć ochronę maszyny wirtualnej w środowisku lokalnym.

    > [!NOTE]
    > Jeśli anulujesz zadanie powrotu po awarii, gdy znajduje się w kroku synchronizacji danych, lokalna maszyna wirtualna będzie w stanie uszkodzenia. Jest to spowodowane synchronizacji danych kopiuje najnowszych danych z dysków maszyny Wirtualnej platformy Azure do dysków z danymi lokalnymi, a ukończenie synchronizacji danych dysku nie może być w spójnym stanie. Jeśli maszyna wirtualna lokalne rozruchu po synchronizacji danych zostało anulowane, może nie uruchomić. Włącz ponownie do zakończenia synchronizacji danych trybu failover.


## <a name="why-is-there-no-button-called-failback"></a>Dlaczego jest brak przycisku o nazwie powrotu po awarii
W portalu jest nie jawnego gestu o nazwie powrotu po awarii. Powrót po awarii jest krokiem, w których możesz wrócić do lokacji głównej. Zgodnie z definicją powrót po awarii jest w przypadku przejścia w tryb failover maszyny wirtualne z odzyskiwania z powrotem do podstawowej.

Po zainicjowaniu przejścia w tryb failover bloku informuje o kierunku, w którym ma zostać przesunięta w przypadku kierunku z platformy Azure do serwera lokalnego, jest powrotu po awarii maszyn wirtualnych.

## <a name="why-is-there-only-a-planned-failover-gesture-to-failback"></a>Dlaczego jest tylko gest planowany tryb failover do powrotu po awarii
Platforma Azure to środowisku o wysokiej dostępności i maszyny wirtualne są zawsze dostępne. Powrót po awarii jest planowane działanie, gdy zdecydujesz się utworzyć małych przestojów obciążeń można uruchomić ponownie uruchamiane lokalnie. To oczekuje bez utraty danych. Dlatego gest planowanego trybu failover jest dostępna, który będzie wyłączanie maszyn wirtualnych na platformie Azure, pobranie najnowszych zmian i upewnij się, że bez utraty danych.

## <a name="do-i-need-a-process-server-in-azure-to-failback-to-hyper-v"></a>Czy potrzebuję, aby serwer przetwarzania na platformie Azure, powrót po awarii dla funkcji Hyper-v?
Nie, serwer przetwarzania jest wymagany tylko wtedy, gdy chronisz maszyny wirtualne VMware. Nie dodatkowe składniki są wymagane do wdrożenia, gdy ochrona/powrotu po awarii maszyn wirtualnych funkcji Hyper-v.


## <a name="time-taken-to-failback"></a>Czas potrzebny do powrotu po awarii
Czas potrzebny do zakończenia synchronizacji danych i uruchom maszynę wirtualną, zależy od różnych czynników. Aby zapewnić wgląd w czas, Wyjaśnijmy, co się dzieje podczas synchronizacji danych.

Synchronizacja danych tworzy migawkę dysków maszyny wirtualnej i zacznie blok po bloku i oblicza jego sumy kontrolnej. To obliczona suma kontrolna są wysyłane do serwera lokalnego do porównania z lokalnej sumy kontrolnej tego samego bloku. W przypadku, gdy odpowiada sumy kontrolne, nie są przenoszone bloku danych. Jeśli nie jest zgodny, blok danych jest przekazywany do środowiska lokalnego. Ten czas transferu jest zależna od przepustowość dostępną. Szybkość suma kontrolna jest kilka gigabajtów na min. 

Aby przyspieszyć proces pobierania danych, można skonfigurować agenta usług MARS zrównoleglić pobierania za pomocą większej liczby wątków. Zapoznaj się [dokumentu w tym miejscu](https://support.microsoft.com/en-us/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage) o sposobie zmieniania wątki pobierania agenta.


## <a name="next-steps"></a>Następne kroki

Po **zatwierdzić**, można inicjować *replikacja odwrotna*. Spowoduje to uruchomienie ochronę maszyny wirtualnej ze środowiska lokalnego do platformy Azure. Będzie to tylko replikować zmiany, ponieważ maszyna wirtualna została wyłączona na platformie Azure i dlatego wysyła tylko różnicowe zmiany.
