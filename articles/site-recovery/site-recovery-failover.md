---
title: Tryb failover podczas odzyskiwania po awarii przy użyciu Azure Site Recovery | Microsoft Docs
description: Dowiedz się więcej o przełączaniu maszyn wirtualnych i serwerów fizycznych podczas odzyskiwania po awarii przy użyciu usługi Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 06/30/2019
ms.author: raynew
ms.openlocfilehash: da55d83665792f6ea2f4c78aa2a6c3ca26c39233
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/05/2019
ms.locfileid: "70383188"
---
# <a name="fail-over-vms-and-physical-servers"></a>Praca awaryjna maszyn wirtualnych i serwerów fizycznych 

W tym artykule opisano sposób pracy w trybie failover maszyn wirtualnych i serwerów fizycznych chronionych przez program Site Recovery.

## <a name="prerequisites"></a>Wymagania wstępne
1. Przed przełączeniem w tryb failover wykonaj [test pracy w trybie failover](site-recovery-test-failover-to-azure.md) , aby upewnić się, że wszystko działa zgodnie z oczekiwaniami.
1. [Przygotuj sieć](site-recovery-network-design.md) w lokalizacji docelowej przed przełączeniem w tryb failover.  

Skorzystaj z poniższej tabeli, aby dowiedzieć się więcej na temat opcji trybu failover dostępnych w Azure Site Recovery. Te opcje są również wyszczególnione dla różnych scenariuszy trybu failover.

| Scenariusz | Wymaganie odzyskiwania aplikacji | Przepływ pracy dla funkcji Hyper-V | Przepływ pracy dla programu VMware
|---|--|--|--|
|Planowana praca w trybie failover z powodu nadchodzącego przestoju centrum danych| Zero utraty danych aplikacji w przypadku wykonywania planowanego działania| W przypadku funkcji Hyper-V usługa ASR replikuje dane przy częstotliwości kopiowania określonej przez użytkownika. Planowana praca w trybie failover służy do przesłonięcia częstotliwości i replikowania końcowych zmian przed zainicjowaniem trybu failover. <br/> <br/> 1. Zaplanuj okno obsługi zgodnie z procesem zarządzania zmianami w firmie. <br/><br/> 2. Powiadamiaj użytkowników o nadchodzącym przestoju. <br/><br/> 3. Przełącz aplikację dostępną dla użytkowników do trybu offline.<br/><br/>4. Zainicjuj planowaną pracę w trybie failover przy użyciu portalu usługi ASR. Lokalna maszyna wirtualna jest automatycznie zamykana.<br/><br/>Efektywna utrata danych aplikacji = 0 <br/><br/>Dziennik punktów odzyskiwania jest również udostępniany w oknie przechowywania dla użytkownika, który chce użyć starszego punktu odzyskiwania. (przechowywanie przez 24 godziny dla funkcji Hyper-V). Jeśli replikacja została zatrzymana poza przedziałem czasu w oknie przechowywania, klienci mogą nadal mieć możliwość przejścia w tryb failover przy użyciu najnowszych dostępnych punktów odzyskiwania. | W przypadku oprogramowania VMware usługa ASR replikuje dane ciągle przy użyciu CDP. Tryb failover daje użytkownikowi możliwość przejścia do trybu failover z najnowszymi danymi (w tym po zamknięciu aplikacji)<br/><br/> 1. Planowanie okna obsługi zgodnie z procesem zarządzania zmianami <br/><br/>2. Powiadom użytkowników o nadchodzącym przestoju <br/><br/>3. Przełącz aplikację dostępną dla użytkowników do trybu offline.<br/><br/>4. Zainicjuj planowaną pracę w trybie failover przy użyciu portalu usługi ASR do ostatniego punktu po przełączeniu aplikacji w tryb offline. Użyj opcji "Planowana praca w trybie failover" w portalu i wybierz najnowszy punkt do przejścia w tryb failover. Lokalna maszyna wirtualna jest automatycznie zamykana.<br/><br/>Efektywna utrata danych aplikacji = 0 <br/><br/>Dziennik punktów odzyskiwania w oknie przechowywania jest udostępniany klientowi, który chce użyć starszego punktu odzyskiwania. (72 godzin przechowywania dla oprogramowania VMware). Jeśli replikacja została zatrzymana poza przedziałem czasu w oknie przechowywania, klienci mogą nadal mieć możliwość przejścia w tryb failover przy użyciu najnowszych dostępnych punktów odzyskiwania.
|Przejście w tryb failover z powodu nieplanowanego przestoju centrum danych (klęska żywiołowa lub awarii) | Minimalna utrata danych dla aplikacji | 1. Inicjowanie planu BCP organizacji <br/><br/>2. Zainicjuj nieplanowaną pracę w trybie failover przy użyciu portalu usługi ASR do najnowszej wersji lub punktu z okna przechowywania (Dziennik).| 1. Zainicjuj plan BCP w organizacji. <br/><br/>2. Zainicjuj nieplanowaną pracę w trybie failover przy użyciu portalu usługi ASR do najnowszej wersji lub punktu z okna przechowywania (Dziennik).


## <a name="run-a-failover"></a>Uruchamianie trybu failover
W tej procedurze opisano sposób uruchamiania trybu failover dla [planu odzyskiwania](site-recovery-create-recovery-plans.md). Alternatywnie można uruchomić tryb failover dla pojedynczej maszyny wirtualnej lub serwera fizycznego ze strony **zreplikowane elementy** , zgodnie z opisem w [tym miejscu](vmware-azure-tutorial-failover-failback.md#run-a-failover-to-azure).


![Tryb failover](./media/site-recovery-failover/Failover.png)

1. Wybierz pozycję **plany** > odzyskiwania*recoveryplan_name*. Kliknij pozycję **tryb failover**
2. Na ekranie **trybu failover** wybierz **punkt odzyskiwania** , do którego ma zostać przejściu w tryb failover. Możesz użyć jednej z następujących opcji:
   1. **Najnowszy**: Ta opcja uruchamia zadanie, najpierw przetwarza wszystkie dane, które zostały wysłane do usługi Site Recovery. Przetwarzanie danych powoduje utworzenie punktu odzyskiwania dla każdej maszyny wirtualnej. Ten punkt odzyskiwania jest używany przez maszynę wirtualną podczas pracy w trybie failover. Ta opcja zapewnia najniższy cel punktu odzyskiwania, ponieważ maszyna wirtualna utworzona po przejściu w tryb failover ma wszystkie dane, które zostały zreplikowane do usługi Site Recovery podczas wyzwolenia trybu failover.
   1. **Najnowszy przetworzony**: Ta opcja powoduje przełączenie w tryb failover wszystkich maszyn wirtualnych planu odzyskiwania do najnowszego punktu odzyskiwania, który został już przetworzony przez usługę Site Recovery. Podczas przeprowadzania testu pracy w trybie failover maszyny wirtualnej jest również pokazywana sygnatura czasowa ostatniego przetworzonego punktu odzyskiwania. Jeśli przejdziesz do trybu failover planu odzyskiwania, możesz przejść do poszczególnych maszyn wirtualnych i przyjrzeć się **najnowszym kafelkom punktów odzyskiwania** , aby uzyskać te informacje. Ponieważ nie ma czasu poświęcanego na przetworzenie nieprzetworzonych danych, ta opcja zapewnia niską RTO (cel czasu odzyskiwania) w trybie failover.
   1. **Najnowszy spójny na poziomie aplikacji**: Ta opcja powoduje przełączenie w tryb failover wszystkich maszyn wirtualnych planu odzyskiwania do najnowszego punktu odzyskiwania spójnego z aplikacją, który został już przetworzony przez usługę Site Recovery. Podczas przeprowadzania testu pracy w trybie failover maszyny wirtualnej jest również wyświetlana sygnatura czasowa najnowszego punktu odzyskiwania spójnego na poziomie aplikacji. Jeśli przejdziesz do trybu failover planu odzyskiwania, możesz przejść do poszczególnych maszyn wirtualnych i przyjrzeć się **najnowszym kafelkom punktów odzyskiwania** , aby uzyskać te informacje.
   1. **Najnowsza przetworzona wiele maszyn wirtualnych**: Ta opcja jest dostępna tylko w przypadku planów odzyskiwania, które mają co najmniej jedną maszynę wirtualną z spójnością obejmującą wiele maszyn wirtualnych. Maszyny wirtualne będące częścią grupy replikacji są w trybie failover do najnowszego wspólnego punktu odzyskiwania z obsługą wiele maszyn wirtualnych. Inne maszyny wirtualne są w trybie failover do ostatniego przetworzonego punktu odzyskiwania.  
   1. **Najnowsza aplikacja obsługująca wiele maszyn wirtualnych — spójna**: Ta opcja jest dostępna tylko w przypadku planów odzyskiwania, które mają co najmniej jedną maszynę wirtualną z spójnością obejmującą wiele maszyn wirtualnych. Maszyny wirtualne będące częścią grupy replikacji są w trybie failover do najnowszego wspólnego punktu odzyskiwania spójnego z aplikacją obejmującą wiele maszyn wirtualnych. Inne maszyny wirtualne przełączenia w tryb failover do najnowszego punktu odzyskiwania spójnego na poziomie aplikacji.
   1. **Niestandardowy**: Jeśli wykonujesz test pracy w trybie failover maszyny wirtualnej, możesz użyć tej opcji do przejścia w tryb failover do określonego punktu odzyskiwania.

      > [!NOTE]
      > Opcja wyboru punktu odzyskiwania jest dostępna tylko po przełączeniu w tryb failover na platformie Azure.
      >
      >


1. Jeśli niektóre maszyny wirtualne w planie odzyskiwania zostały przełączone w tryb failover w poprzednim przebiegu i teraz maszyny wirtualne są aktywne zarówno w lokalizacji źródłowej, jak i docelowej, można użyć opcji **Zmień kierunek** , aby określić kierunek przejścia w tryb failover.
1. Jeśli nastąpi przełączenie w tryb failover na platformie Azure i włączono szyfrowanie danych dla chmury (ma zastosowanie tylko w przypadku chronionych maszyn wirtualnych funkcji Hyper-v z serwera programu VMM), w **kluczu szyfrowania** wybierz certyfikat, który został wystawiony po włączeniu szyfrowania danych podczas Konfiguracja na serwerze programu VMM.
1. Wybierz opcję **Zamknij maszynę przed rozpoczęciem pracy w trybie failover** , jeśli chcesz, aby Site Recovery próbuje wykonać zamknięcie źródłowych maszyn wirtualnych przed wyzwoleniem trybu failover. Tryb failover kontynuuje działanie nawet wtedy, gdy operacja zamykania nie powiedzie się.  

    > [!NOTE]
    > Jeśli maszyny wirtualne funkcji Hyper-v są chronione, opcja zamykania programu próbuje również zsynchronizować dane lokalne, które nie zostały jeszcze wysłane do usługi przed wyzwoleniem trybu failover.
    >
    >

1. Na stronie **Zadania** można śledzić postęp trybu failover. Nawet w przypadku wystąpienia błędów podczas nieplanowanego przejścia w tryb failover plan odzyskiwania zostanie uruchomiony do momentu jego zakończenia.
1. Po przejściu do trybu failover Zweryfikuj maszynę wirtualną, logując się do niej. Jeśli chcesz przełączyć się do innego punktu odzyskiwania maszyny wirtualnej, możesz użyć opcji **Zmień punkt odzyskiwania** .
1. Po poprawnym skonfigurowaniu przełączania maszyny wirtualnej w tryb failover możesz **zatwierdzić** tryb failover. **Zatwierdzenie usuwa wszystkie punkty odzyskiwania dostępne w usłudze** i opcja **Zmień punkt odzyskiwania** nie jest już dostępna.

## <a name="planned-failover"></a>Planowane przełączenie w tryb failover
Maszyny wirtualne/serwery fizyczne chronione za pomocą Site Recovery obsługują również **planowaną pracę w trybie failover**. Planowana praca w trybie failover to zero opcja trybu failover utraty danych. W przypadku wyzwolenia planowanej pracy w trybie failover najpierw zostaną wyłączone źródłowe maszyny wirtualne, a następnie zostanie wyzwolone przejście w tryb failover.

> [!NOTE]
> Podczas pracy w trybie failover maszyn wirtualnych funkcji Hyper-v z jednej lokacji lokalnej do innej lokacji lokalnej, aby wrócić do podstawowej lokacji lokalnej, należy najpierw **replikować** maszynę wirtualną z powrotem do lokacji głównej, a następnie wyzwolić tryb failover. Jeśli podstawowa maszyna wirtualna jest niedostępna, przed rozpoczęciem **replikacji** należy przywrócić maszynę wirtualną z kopii zapasowej.   
 
 
## <a name="failover-job"></a>Zadanie trybu failover

![Tryb failover](./media/site-recovery-failover/FailoverJob.png)

Po wyzwoleniu przejścia w tryb failover obejmuje następujące kroki:

1. Sprawdzanie wymagań wstępnych: Ten krok zapewnia spełnienie wszystkich warunków wymaganych do przełączenia w tryb failover
1. Pracy Ten krok przetwarza dane i ustawia je jako gotowe, aby można było utworzyć maszynę wirtualną platformy Azure. W przypadku wybrania **najnowszego** punktu odzyskiwania ten krok powoduje utworzenie punktu odzyskiwania na podstawie danych wysłanych do usługi.
1. Uruchamianie: Ten krok tworzy maszynę wirtualną platformy Azure przy użyciu danych przetworzonych w poprzednim kroku.

> [!WARNING]
> **Nie Anuluj trybu failover w toku**: Przed rozpoczęciem pracy w trybie failover replikacja maszyny wirtualnej zostanie zatrzymana. Jeśli **anulujesz** zadanie w toku, tryb failover zostanie zatrzymany, ale nie rozpocznie się replikacja maszyny wirtualnej. Nie można ponownie uruchomić replikacji.
>
>

## <a name="time-taken-for-failover-to-azure"></a>Czas trwania przejścia w tryb failover na platformę Azure

W niektórych przypadkach przełączanie do trybu failover maszyn wirtualnych wymaga dodatkowego etapu pośredniego, który przeważnie trwa około 8 do 10 minut. W następujących przypadkach czas przełączenia w tryb failover będzie wyższy niż zwykle:

* Maszyny wirtualne VMware korzystające z wersji starszej niż 9,8
* Serwery fizyczne
* Maszyny wirtualne VMware Linux
* Maszyny wirtualne funkcji Hyper-V chronione jako serwery fizyczne
* Maszyny wirtualne VMware, na których nie ma następujących sterowników jako sterowniki rozruchowe
    * storvsc
    * vmbus
    * storflt
    * Intelide
    * Napęd
* Maszyny wirtualne VMware, które nie mają włączonej usługi DHCP niezależnie od tego, czy korzystają z protokołu DHCP czy statycznych adresów IP

We wszystkich innych przypadkach ten pośredni krok nie jest wymagany i czas potrzebny na przełączenie w tryb failover jest niższy.

## <a name="using-scripts-in-failover"></a>Używanie skryptów w trybie failover
Podczas pracy w trybie failover można zautomatyzować pewne działania. Aby to zrobić, możesz użyć skryptów lub [elementów Runbook usługi Azure Automation](site-recovery-runbook-automation.md) w [planach odzyskiwania](site-recovery-create-recovery-plans.md) .

## <a name="post-failover-considerations"></a>Uwagi dotyczące publikowania w trybie failover
Publikowanie w trybie failover warto wziąć pod uwagę następujące zalecenia:
### <a name="retaining-drive-letter-after-failover"></a>Zachowywanie litery dysku po przejściu do trybu failover
Azure Site Recovery obsługuje przechowywanie liter dysku. [Przeczytaj więcej](vmware-azure-exclude-disk.md#example-1-exclude-the-sql-server-tempdb-disk) na temat tego, jak to robi, gdy zdecydujesz się na wykluczenie niektórych dysków.

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Przygotowanie do połączenia z maszynami wirtualnymi Azure po przejściu do trybu failover

Jeśli chcesz nawiązać połączenie z maszynami wirtualnymi platformy Azure przy użyciu protokołu RDP/SSH po przejściu do trybu failover, upewnij się, że zostały spełnione wymagania podsumowane w [tej](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover) tabeli.

Wykonaj czynności opisane [tutaj](site-recovery-failover-to-azure-troubleshoot.md), aby rozwiązać wszystkie problemy z łącznością po przejściu do trybu failover.


## <a name="next-steps"></a>Następne kroki

> [!WARNING]
> Po przełączeniu maszyn wirtualnych w tryb failover i udostępnieniu lokalnego centrum danych należy ponownie [**włączyć ochronę**](vmware-azure-reprotect.md) maszyn wirtualnych VMware z powrotem do lokalnego centrum danych.

Opcja [**planowanej pracy w trybie failover**](hyper-v-azure-failback.md) umożliwia powrót **po awarii** maszyn wirtualnych funkcji Hyper-v z platformy Azure do stanu lokalnego.

Jeśli maszyna wirtualna funkcji Hyper-v została przełączona w tryb failover do innego lokalnego centrum danych zarządzanego przez serwer programu VMM i jest dostępne podstawowe centrum danych, użyj opcji **odwrotnej replikacji** , aby uruchomić replikację z powrotem do podstawowego centrum danych.
