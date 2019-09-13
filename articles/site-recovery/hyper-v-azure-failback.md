---
title: Uruchamianie powrotu po awarii podczas awarii maszyn wirtualnych funkcji Hyper-v z platformy Azure do lokacji lokalnej | Microsoft Docs
description: Informacje o sposobie powrotu maszyn wirtualnych funkcji Hyper-V do lokacji lokalnej podczas odzyskiwania po awarii na platformie Azure przy użyciu usługi Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 09/12/2019
ms.author: rajanaki
ms.openlocfilehash: 07ecc8547ab155600bccfd1ad8f1ecbb58a18fa3
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70931840"
---
# <a name="run-a-failback-for-hyper-v-vms"></a>Uruchamianie powrotu po awarii dla maszyn wirtualnych funkcji Hyper-V

W tym artykule opisano sposób powrotu do awarii maszyn wirtualnych funkcji Hyper-V chronionych przez Site Recovery.

## <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że zostały przeczytane szczegółowe informacje o [różnych typach powrotu po awarii](concepts-types-of-failback.md) i odpowiednich zastrzeżeniach.
- Upewnij się, że serwer VMM lokacji głównej lub serwer hosta funkcji Hyper-V jest połączony z platformą Azure.
- Należy wykonać zatwierdzenie na maszynie wirtualnej.
- Upewnij się, że używasz konta magazynu do replikacji, a nie do dysków zarządzanych. Powrót po awarii maszyn wirtualnych funkcji Hyper-V replikowanych za pomocą zarządzania dyskami nie jest obsługiwany.

## <a name="perform-failback"></a>Wykonaj powrót po awarii
Po przełączeniu w tryb failover z lokalizacji podstawowej do pomocniczej zreplikowane maszyny wirtualne nie są chronione przez Site Recovery, a lokacja dodatkowa działa jako aktywna lokalizacja. Aby zakończyć przywracanie maszyn wirtualnych w planie odzyskiwania, uruchom planowane przejście w tryb failover z lokacji dodatkowej do podstawowego w następujący sposób. 
1. Wybierz pozycję **plany** > odzyskiwania*recoveryplan_name*. Kliknij pozycję**planowana**praca awaryjna w trybie failover. > 
2. Na stronie **Potwierdzanie planowanej pracy w trybie failover** wybierz lokalizację źródłową i docelową. Zanotuj kierunek trybu failover. Jeśli przejście w tryb failover z podstawowego zadziałało zgodnie z oczekiwaniami, a wszystkie maszyny wirtualne znajdują się w lokalizacji pomocniczej, jest to tylko informacje.
3. W przypadku powrotu po awarii z platformy Azure wybierz ustawienia w obszarze **Synchronizacja danych**:
    - **Synchronizuj dane przed przełączeniem w tryb failover (Synchronizuj tylko zmiany różnicowe)** — ta opcja minimalizuje przestoje maszyn wirtualnych, gdy synchronizacja nie zostanie wyłączona. Wykonuje następujące czynności:
        - Etap 1: Wykonuje migawkę maszyny wirtualnej na platformie Azure i kopiuje ją do lokalnego hosta funkcji Hyper-V. Maszyna kontynuuje działanie na platformie Azure.
        - Etap 2: Zamyka maszynę wirtualną na platformie Azure, aby nie pojawiły się żadne nowe zmiany. Końcowy zestaw zmian różnicowych jest transferowany na serwer lokalny, a lokalna maszyna wirtualna jest uruchamiana.

    - **Synchronizuj dane tylko podczas pracy w trybie failover (pełne pobieranie)** — ta opcja jest szybsza.
        - Ta opcja jest szybsza, ponieważ oczekuje się, że większość dysku uległa zmianie i nie chcemy poświęcać czasu na obliczanie sum kontrolnych. Pobiera dysk. Jest on również przydatny, gdy maszyna wirtualna Premium została usunięta.
        - Zalecamy użycie tej opcji, jeśli używasz platformy Azure przez pewien czas (co miesiąc lub więcej) lub maszyna wirtualna Premium została usunięta. Ta opcja nie wykonuje żadnych obliczeń sum kontrolnych.


4. Jeśli szyfrowanie danych jest włączone dla chmury, w **kluczu szyfrowania** wybierz certyfikat, który został wystawiony po włączeniu szyfrowania danych podczas instalacji dostawcy na serwerze programu VMM.
5. Zainicjuj tryb failover. Na karcie **Zadania** można śledzić postęp trybu failover.
6. W przypadku wybrania opcji synchronizowania danych przed przełączeniem w tryb failover po zakończeniu początkowej synchronizacji danych i przygotowaniu do zamknięcia maszyn wirtualnych na platformie Azure kliknij pozycję **zadania** > nazwa zadania > **zakończyć pracę w trybie failover**. Spowoduje to zamknięcie maszyny platformy Azure, przeniesienie najnowszych zmian na lokalną maszynę wirtualną i uruchomienie maszyny wirtualnej lokalnie.
7. Możesz teraz zalogować się do maszyny wirtualnej, aby sprawdzić, czy jest ona dostępna zgodnie z oczekiwaniami.
8. Maszyna wirtualna jest w stanie oczekiwania na zatwierdzenie. Kliknij przycisk Zatwierdź, aby zatwierdzić przejście w tryb failover.
9. Aby zakończyć powrót po awarii, kliknij pozycję **replikacja odwrotna** , aby rozpocząć ochronę maszyny wirtualnej w lokacji głównej.


Postępuj zgodnie z tymi procedurami, aby powrócić po awarii do oryginalnej lokacji głównej. W tej procedurze opisano sposób uruchamiania planowanej pracy w trybie failover dla planu odzyskiwania. Alternatywnie można uruchomić tryb failover dla pojedynczej maszyny wirtualnej na karcie **Virtual Machines** .


## <a name="failback-to-an-alternate-location-in-hyper-v-environment"></a>Powrót po awarii do alternatywnej lokalizacji w środowisku funkcji Hyper-V
Jeśli zainstalowano ochronę między [lokacją funkcji Hyper-V i platformą Azure](site-recovery-hyper-v-site-to-azure.md) , musisz mieć możliwość powrotu po awarii z platformy Azure do alternatywnej lokalizacji lokalnej. Jest to przydatne, jeśli trzeba skonfigurować nowy sprzęt lokalny. Oto jak to zrobić.

1. W przypadku konfigurowania nowego sprzętu Zainstaluj system Windows Server 2012 R2 i rolę funkcji Hyper-V na serwerze.
2. Utwórz przełącznik sieci wirtualnej o takiej samej nazwie, jak na oryginalnym serwerze.
3. Wybierz **pozycję chronione elementy** -> **Grupa** ->  \<ochrony ProtectionGroupName >-> VirtualMachineName > chcesz wrócić po awarii i wybierz pozycję Planowana praca w trybie failover.\<
4. W obszarze **Potwierdź planowane przejście w tryb failover** wybierz pozycję **Utwórz lokalną maszynę wirtualną, jeśli nie istnieje**.
5. W polu Nazwa hosta * * wybierz nowy serwer hosta funkcji Hyper-V, na którym chcesz umieścić maszynę wirtualną.
6. W obszarze Synchronizacja danych zalecamy wybranie opcji synchronizowania danych przed przełączeniem w tryb failover. Pozwala to zminimalizować przestoje maszyn wirtualnych, które są synchronizowane bez wyłączania ich. Wykonuje następujące czynności:

    - Etap 1: Wykonuje migawkę maszyny wirtualnej na platformie Azure i kopiuje ją do lokalnego hosta funkcji Hyper-V. Maszyna kontynuuje działanie na platformie Azure.
    - Etap 2: Zamyka maszynę wirtualną na platformie Azure, aby nie pojawiły się żadne nowe zmiany. Końcowy zestaw zmian jest transferowany na serwer lokalny, a lokalna maszyna wirtualna jest uruchamiana.
    
7. Kliknij znacznik wyboru, aby rozpocząć pracę w trybie failover (powrót po awarii).
8. Po zakończeniu początkowej synchronizacji i przygotowaniu się do zamykania maszyny wirtualnej na platformie Azure kliknij kolejno pozycje **zadania** > \<planowane przejście w tryb failover > > **zakończeniu pracy w trybie failover**. Spowoduje to zamknięcie maszyny platformy Azure, przeniesienie najnowszych zmian na lokalną maszynę wirtualną i jej uruchomienie.
9. Możesz zalogować się do lokalnej maszyny wirtualnej, aby sprawdzić, czy wszystko działa zgodnie z oczekiwaniami. Następnie kliknij przycisk Zatwierdź, aby zakończyć pracę w trybie failover. Zatwierdzenie powoduje usunięcie maszyny wirtualnej platformy Azure i jej dysków i przygotowanie maszyny wirtualnej do ponownego ochrony.
10. Kliknij pozycję **replikacja odwrotna** , aby rozpocząć ochronę lokalnej maszyny wirtualnej.

    > [!NOTE]
    > Jeśli zadanie powrotu po awarii zostanie anulowane podczas wykonywania kroku synchronizacji danych, lokalna maszyna wirtualna stanie się uszkodzona. Wynika to z faktu, że synchronizacja danych kopiuje najnowsze dane z dysków maszyny wirtualnej platformy Azure na dyski danych Premium i dopóki synchronizacja nie zostanie zakończona, dane dysku mogą nie być w stanie spójnym. Jeśli maszyna wirtualna Premium jest uruchamiana po anulowaniu synchronizacji danych, uruchomienie jej może być niemożliwe. Wyzwól ponownie tryb failover w celu ukończenia synchronizacji danych.


## <a name="why-is-there-no-button-called-failback"></a>Dlaczego nie ma przycisku o nazwie powrót po awarii?
W portalu nie ma żadnego jawnego gestu o nazwie powrót po awarii. Powrót po awarii to krok, w którym powrócisz do lokacji głównej. Zgodnie z definicją powrót po awarii jest w przypadku przejścia w tryb failover maszyn wirtualnych z odzyskiwania do podstawowego.

Po zainicjowaniu trybu failover blok informuje o kierunku przenoszenia maszyn wirtualnych, jeśli kierunek jest z platformy Azure do lokalnego, jest to powrót po awarii.

## <a name="why-is-there-only-a-planned-failover-gesture-to-failback"></a>Dlaczego do powrotu po awarii jest dostępny tylko planowany gest przełączania w tryb failover?
Platforma Azure jest środowiskiem o wysokiej dostępności, a maszyny wirtualne są zawsze dostępne. Powrót po awarii to planowana aktywność, w której podjęto decyzję o skróceniu przestoju, dzięki czemu obciążenia mogą zacząć działać lokalnie. Nie spowoduje to utraty danych. W związku z tym dostępny jest tylko planowany gest przełączania w tryb failover, który spowoduje wyłączenie maszyn wirtualnych na platformie Azure, pobranie najnowszych zmian i upewnienie się, że nie ma utraty danych.

## <a name="do-i-need-a-process-server-in-azure-to-failback-to-hyper-v"></a>Czy do powrotu po awarii do funkcji Hyper-v jest potrzebny serwer przetwarzania na platformie Azure?
Nie, serwer przetwarzania jest wymagany tylko w przypadku ochrony maszyn wirtualnych VMware. Nie są wymagane żadne dodatkowe składniki do wdrożenia w przypadku ochrony/powrotu po awarii maszyn wirtualnych funkcji Hyper-v.


## <a name="time-taken-to-failback"></a>Czas trwania powrotu po awarii
Czas potrzebny do ukończenia synchronizacji danych i rozruch maszyny wirtualnej zależy od różnych czynników. Aby uzyskać wgląd w czas trwania, wyjaśnimy, co się dzieje podczas synchronizacji danych.

Synchronizacja danych pobiera migawkę dysków maszyny wirtualnej i uruchamia ją w bloku i oblicza jej sumę kontrolną. Ta obliczona suma kontrolna jest wysyłana do lokalnego w celu porównania z lokalną sumą kontrolną tego samego bloku. Na wypadek dopasowania sum kontrolnych blok danych nie jest przesyłany. Jeśli nie jest zgodny, blok danych jest przekazywany do lokalnego. Ten czas transferu zależy od dostępnej przepustowości. Szybkość sumy kontrolnej to kilka GB na minutę. 

Aby przyspieszyć pobieranie danych, można skonfigurować agenta MARS do używania większej liczby wątków do zrównoleglanie pobierania. Zapoznaj się z [dokumentem tutaj](https://support.microsoft.com/en-us/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage) , jak zmienić wątki pobierania w agencie.


## <a name="next-steps"></a>Następne kroki

Po **zatwierdzeniu**można zainicjować *replikację odwrotną*. Spowoduje to rozpoczęcie ochrony maszyny wirtualnej z poziomu lokalnego z powrotem na platformę Azure. Spowoduje to Replikowanie zmian tylko od momentu wyłączenia maszyny wirtualnej na platformie Azure, co powoduje wysłanie tylko zmian różnicowych.
