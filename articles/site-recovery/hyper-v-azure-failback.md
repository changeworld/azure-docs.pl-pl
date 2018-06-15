---
title: Uruchom powrotu po awarii do lokacji lokalnej dla maszyn wirtualnych funkcji Hyper-v | Dokumentacja firmy Microsoft
description: Usługa Azure Site Recovery koordynuje replikacji, trybu failover i odzyskiwania maszyn wirtualnych i serwerów fizycznych. Więcej informacji na temat powrotu po awarii z platformy Azure do lokalnego centrum danych.
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 02/14/2018
ms.author: rajanaki
ms.openlocfilehash: d344174ffa290b55386918ae19e2f792bb801a8a
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/22/2018
ms.locfileid: "29467121"
---
# <a name="run-a-failback-for-hyper-v-vms"></a>Uruchom powrotu po awarii dla maszyn wirtualnych funkcji Hyper-V

W tym artykule opisano, jak niepowodzenie maszyn wirtualnych funkcji Hyper-V wstecz chronione przez usługę Site Recovery.

## <a name="prerequisites"></a>Wymagania wstępne
1. Upewnij się, że znasz szczegółowe informacje o [różnego rodzaju powrotu po awarii](concepts-types-of-failback.md) i odpowiedniego ostrzeżenia.
1. Upewnij się, że serwer VMM w lokacji głównej lub serwer hosta funkcji Hyper-V jest podłączony do platformy Azure.
2. Użytkownik powinien wykonał **zatwierdzić** na maszynie wirtualnej.

## <a name="perform-failback"></a>Wykonaj powrót po awarii
Po trybu failover z serwera podstawowego do lokalizacji dodatkowej replikowane maszyny wirtualne nie są chronione przez usługę Site Recovery i dodatkowej lokalizacji obecnie działa jako aktywnej lokalizacji. Niepowodzenie tworzenia kopii maszyny wirtualne w planie odzyskiwania, uruchomić planowanego trybu failover z lokacji dodatkowej do podstawowych, w następujący sposób. 
1. Wybierz **plany odzyskiwania** > *recoveryplan_name*. Kliknij przycisk **pracy awaryjnej** > **planowanego trybu Failover**.
2. Na **potwierdzić planowanego trybu Failover** wybierz lokalizacja źródłowa i docelowa. Należy pamiętać, kierunku trybu failover. Jeśli pracy awaryjnej z podstawowej działał jako oczekiwać i wszystkich maszyn wirtualnych znajdują się w lokalizacji dodatkowej, jest to wyłącznie w celach informacyjnych.
3. W przypadku powrotu po awarii z platformy Azure, wybierz ustawienia w **synchronizacji danych**:
    - **Synchronizowanie danych przed pracy awaryjnej (tylko zmiany różnicowe Synchronizuj)**— tej opcji minimalizuje przestojów maszyn wirtualnych, jak synchronizacji bez zamykania je. Wykonywane następujące czynności:
        - Faza 1: Wykonuje migawkę maszyny wirtualnej na platformie Azure i kopiuje go do lokalnego hosta funkcji Hyper-V. Komputer nadal działają na platformie Azure.
        - Faza 2: Zamyka maszyny wirtualnej platformy Azure, dzięki czemu ma nowych zmian występuje. Ostateczny zestaw zmian, które zmiany są przekazywane do serwera lokalnego i na lokalnej maszynie wirtualnej jest uruchomiona.

    - **Synchronizacja danych w trybie failover tylko (pełnego pobierania)**— ta opcja jest szybsze.
        - Ta opcja jest szybsze, ponieważ oczekuje się, że większość dysku został zmieniony i nie chcemy poświęcić czas obliczenia sumy kontrolnej. Wykonuje operację pobierania na dysku. Jest również przydatne w przypadku lokalnego maszyny wirtualnej został usunięty.
        - Firma Microsoft zaleca, możesz użyć tej opcji, jeśli działała Azure jakiś czas (miesiąc lub więcej) lub lokalnej maszyny wirtualnej został usunięty. Ta opcja nie wykonać wszelkie obliczenia sumy kontrolnej.


4. Jeśli szyfrowanie danych jest włączone dla chmury, w **klucza szyfrowania** wybierz certyfikat, który został wystawiony, włączenie szyfrowania danych podczas instalacji dostawcy na serwerze programu VMM.
5. Inicjuj tryb failover. Możesz śledzić postęp trybu failover **zadania** kartę.
6. Jeśli wybrano opcję, aby zsynchronizować dane przed przejście w tryb failover, po ukończeniu synchronizacji początkowej danych i wszystko jest gotowe wyłączyć maszyny wirtualne na platformie Azure, kliknij przycisk **zadania** Nazwa zadania planowany tryb failover **Przejdź w tryb Failover**. Zamyka maszyny platformy Azure, przesyła najnowsze zmiany na lokalnej maszynie wirtualnej i uruchomieniu maszyny Wirtualnej lokalną.
7. Możesz teraz dziennika na maszynie wirtualnej, aby go zweryfikować jest dostępna, zgodnie z oczekiwaniami.
8. Maszyna wirtualna jest w stan oczekiwania na zatwierdzenie. Kliknij przycisk **zatwierdzania** do zatwierdzenia pracy awaryjnej.
9. Teraz, aby dokończyć kliknij powrotu po awarii **odwrócić replikację** do uruchomienia ochrony maszyny wirtualnej w lokacji głównej.


Postępuj zgodnie z poniższymi procedurami, aby powrót po awarii do oryginalnego lokacji głównej. W tej procedurze opisano sposób uruchamiania planowany tryb failover planu odzyskiwania. Alternatywnie można uruchomić trybu failover dla jednej maszyny wirtualnej na **maszyn wirtualnych** kartę.


## <a name="failback-to-an-alternate-location-in-hyper-v-environment"></a>Powrót po awarii do alternatywnej lokalizacji w środowisku funkcji Hyper-V
Jeśli została wdrożona ochrona między [lokacji funkcji Hyper-V i Azure](site-recovery-hyper-v-site-to-azure.md) masz możliwość powrotu po awarii z platformy Azure do lokalizacji alternatywnej lokalnymi. Jest to przydatne, jeśli chcesz skonfigurować nowy sprzęt lokalnymi. Oto jak to zrobić.

1. Jeśli podczas konfigurowania nowego sprzętu systemu Windows Server 2012 R2 i roli Hyper-V należy zainstalować na serwerze.
2. Utwórz przełącznik sieci wirtualnej o tej samej nazwie, która była na oryginalnym serwerze.
3. Wybierz **chronione elementy** -> **grupy ochrony**  ->  <ProtectionGroupName>  ->  <VirtualMachineName> chcesz wykonaj powrót po awarii, a następnie wybierz **planowane Tryb failover**.
4. W **potwierdzić planowanego trybu Failover** wybierz **Utwórz lokalną maszynę wirtualną, jeśli nie istnieje**.
5. W polu Nazwa hosta ** wybierz nowy serwer hosta funkcji Hyper-V, na którym chcesz umieścić maszynę wirtualną.
6. W synchronizacji danych, firma Microsoft zaleca, wybierz opcję **synchronizować dane przed przejście w tryb failover**. Minimalizuje czas przestoju w przypadku maszyn wirtualnych, jak synchronizacji bez zamykania je. Wykonuje następujące zadania:

    - Faza 1: Wykonuje migawkę maszyny wirtualnej na platformie Azure i kopiuje go do lokalnego hosta funkcji Hyper-V. Komputer nadal działają na platformie Azure.
    - Faza 2: Zamyka maszyny wirtualnej platformy Azure, dzięki czemu ma nowych zmian występuje. Ostateczny zestaw zmian są przekazywane do serwera lokalnego i zostanie uruchomiona na lokalnej maszynie wirtualnej.
    
7. Kliknij znacznik wyboru, aby rozpocząć tryb failover (powrót po awarii).
8. Po zakończeniu synchronizacji początkowej i wszystko jest gotowe wyłączyć maszynę wirtualną na platformie Azure, kliknij przycisk **zadania** > <planned failover job> > **ukończenia pracy awaryjnej**. Zamyka maszyny platformy Azure, przesyła najnowsze zmiany na lokalnej maszynie wirtualnej i uruchamia go.
9. Można zalogować się do maszyny wirtualnej lokalnego, aby sprawdzić, czy wszystko działa zgodnie z oczekiwaniami. Następnie kliknij przycisk **zatwierdzić** na zakończenie pracy awaryjnej. Zatwierdzania usuwa maszyny wirtualnej platformy Azure i jego dysków i przygotowuje maszynę Wirtualną, która ma być chroniony ponownie.
10. Kliknij przycisk **odwrócić replikację** aby rozpocząć ochronę na lokalnej maszynie wirtualnej.

    > [!NOTE]
    > Jeśli anulujesz zadaniem powrotu po awarii, gdy znajduje się w kroku synchronizacji danych, lokalnej maszyny Wirtualnej będzie w stanie uszkodzenia. To dlatego synchronizacji danych kopiuje najnowsze dane z dysków maszyny Wirtualnej platformy Azure na lokalnych dyskach danych, a dopiero po zakończeniu synchronizacji, dane dysku mogą nie być w stanie spójności. Jeśli lokalnych na maszynie Wirtualnej rozruchu po synchronizacji danych zostało anulowane, może nie uruchomić. Zdarzenia trybu failover w celu wykonania synchronizacji danych.


## <a name="why-is-there-no-button-called-failback"></a>Dlaczego jest ma przycisku o nazwie powrotu po awarii
W portalu Brak nie jawnego gestu o nazwie powrotu po awarii. Powrót po awarii jest to krok, gdzie możesz wrócić do lokacji głównej. Zgodnie z definicją powrotu po awarii jest podczas pracy awaryjnej należy maszyn wirtualnych z odzyskiwania z powrotem do podstawowej.

Po zainicjowaniu tryb failover bloku informuje o kierunek, w którym ma zostać przesunięta w przypadku kierunku z platformy Azure do środowiska lokalnego, jest powrót po awarii maszyn wirtualnych.

## <a name="why-is-there-only-a-planned-failover-gesture-to-failback"></a>Dlaczego jest tylko gestu planowany tryb failover do powrotu po awarii
Azure to środowisko o wysokiej dostępności i maszyny wirtualne są zawsze dostępne. Powrót po awarii jest planowane działanie decyzję dotyczącą podjąć przestoju małych obciążeń można uruchomić ponownie uruchamiane lokalnie. To oczekuje bez utraty danych. Dlatego jest dostępna tylko gestu planowanego trybu failover, które wyłączyć maszyny wirtualne na platformie Azure, pobrać najnowsze zmiany i upewnij się, że istnieje nie powoduje utraty danych.

## <a name="do-i-need-a-process-server-in-azure-to-failback-to-hyper-v"></a>Należy serwer przetwarzania na platformie Azure do powrotu po awarii do funkcji Hyper-v?
Nie, serwer przetwarzania jest wymagany tylko wtedy, gdy chronisz maszyny wirtualne VMware. Żadne dodatkowe składniki są wymagane do wdrożenia, gdy ochrona/powrotu po awarii maszyn wirtualnych funkcji Hyper-v.


## <a name="time-taken-to-failback"></a>Czas potrzebny na powrót po awarii
Czas potrzebny na zakończenie synchronizacji danych i rozruchu maszyny wirtualnej zależy od różnych czynników. Aby dać wgląd w terminie, firma Microsoft opisano, co się dzieje podczas synchronizacji danych.

Synchronizacja danych tworzy migawkę dysków maszyny wirtualnej i rozpoczyna się sprawdzanie blok po bloku i oblicza jego sumy kontrolnej. To obliczeniowe sumy kontrolnej są wysyłane do środowiska lokalnego do porównania z lokalnymi sumy kontrolnej z tym samym bloku. W przypadku sum kontrolnych są zgodne, nie jest przenoszona bloku danych. Jeśli nie jest zgodny, bloku danych jest przenoszona do środowiska lokalnego. Ten czas transferu jest zależna od przepustowość. Szybkość sumy kontrolnej to kilka GB na min. 

W celu przyspieszenia pobierania danych, można skonfigurować na potrzeby większej liczby wątków parallelize pobieranie agenta MARS. Zapoznaj się [dokumentu tutaj](https://support.microsoft.com/en-us/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage) dotyczące zmieniania wątki pobierania agenta.


## <a name="next-steps"></a>Następne kroki

Po **zatwierdzić**, można zainicjować *odwrócić replikację*. Spowoduje to uruchomienie ochrony maszyny wirtualnej z lokalnego do platformy Azure. Ponieważ maszyna wirtualna została wyłączona na platformie Azure i dlatego wysyła tylko zmiany różnicowe spowoduje to tylko zreplikowanie zmian.
