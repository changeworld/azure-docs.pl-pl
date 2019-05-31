---
title: Tryb failover podczas odzyskiwania po awarii przy użyciu usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o za pośrednictwem serwerów fizycznych i maszyn wirtualnych podczas odzyskiwania po awarii przy użyciu usługi Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 05/30/2019
ms.author: raynew
ms.openlocfilehash: a02a2be7fb3ed942b1359949e18ba7d3dee824ae
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399967"
---
# <a name="fail-over-vms-and-physical-servers"></a>W tryb failover maszyn wirtualnych i serwerów fizycznych 

W tym artykule opisano, jak do trybu failover maszyny wirtualne i fizyczne serwery są chronione przez usługę Site Recovery.

## <a name="prerequisites"></a>Wymagania wstępne
1. Przed wykonaniem przejścia w tryb failover, wykonaj [testowanie trybu failover](site-recovery-test-failover-to-azure.md) aby upewnić się, że wszystko działa zgodnie z oczekiwaniami.
1. [Przygotuj sieci](site-recovery-network-design.md) w miejscu docelowym, przed wykonaniem przejścia w tryb failover.  

Skorzystaj z poniższej tabeli, aby dowiedzieć się o opcji pracy awaryjnej, dostarczanych przez usługę Azure Site Recovery. Te opcje są także wyświetlane w przypadku pracy awaryjnej w różnych scenariuszy.

| Scenariusz | Wymagania odzyskiwania aplikacji | Przepływ pracy dla funkcji Hyper-V | Przepływ pracy dla oprogramowania VMware
|---|--|--|--|
|Planowany tryb failover z powodu przestojów nadchodzących centrum danych| Zerową utratę danych dla aplikacji, gdy przeprowadzane jest planowane działanie| Dla funkcji Hyper-V usługi ASR replikuje dane, z częstotliwością kopiowania, który jest określony przez użytkownika. Planowanego trybu Failover jest używany do zastąpienia częstotliwość i replikować ostatecznych zmian przed przejścia w tryb failover jest inicjowane. <br/> <br/> 1.    Planowanie okna obsługi, zgodnie z proces zarządzania zmianami Twojej firmy. <br/><br/> 2. powiadamianie użytkowników o nadchodzących przestojów. <br/><br/> 3. Przełącz do trybu offline aplikacji przeznaczonych dla użytkownika.<br/><br/>4 zainicjować planowana praca awaryjna przy użyciu portalu ASR. Maszyna wirtualna w środowisku lokalnym jest automatyczne zamknięcie.<br/><br/>Skuteczne stosowanie utracie = 0 <br/><br/>Dziennik punktów odzyskiwania jest również udostępniany w okresie przechowywania dla użytkownika, który chce użyć starszego punktu odzyskiwania. (24 godziny przechowywania dla funkcji Hyper-V). Jeśli replikacja jest zatrzymana, poza przedział czasu okna przechowywania, klienci mogą nadal mogli trybu failover przy użyciu najnowsze dostępne punkty odzyskiwania. | Dla oprogramowania VMware usługi ASR replikuje danych stale przy użyciu punktu dystrybucji list CRL. Tryb failover zapewnia możliwość pracy awaryjnej do najnowszych danych (w tym zamknięcie aplikacji po)<br/><br/> 1. Planowanie okna obsługi, zgodnie z proces zarządzania zmianami <br/><br/>2. powiadamianie użytkowników o nadchodzących przestojów <br/><br/>3.    Przełącz do trybu offline aplikacji przeznaczonych dla użytkownika. <br/><br/>4.  Inicjowanie planowanego trybu Failover przy użyciu portalu ASR do najnowszego punktu, gdy aplikacja jest w trybie offline. Użyj opcji "Nieplanowany tryb Failover" w portalu i wybierz najnowszy punkt dla trybu failover. Maszyna wirtualna w środowisku lokalnym jest automatyczne zamknięcie.<br/><br/>Skuteczne stosowanie utracie = 0 <br/><br/>Dziennik punktów odzyskiwania w okresie przechowywania jest udostępniana dla klienta, który chce użyć starszego punktu odzyskiwania. (72 godziny przechowywania dla oprogramowania VMware). Jeśli replikacja jest zatrzymana, poza przedział czasu okna przechowywania, klienci mogą nadal mogli trybu failover przy użyciu najnowsze dostępne punkty odzyskiwania.
|Tryb failover z powodu przestojów nieplanowane centrum danych (naturalnym lub IT po awarii) | Minimalną utratą danych dla aplikacji | 1. zainicjowanie plan BCP organizacji <br/><br/>2. Zainicjuj nieplanowany tryb Failover przy użyciu portalu ASR do najnowszej lub punktu z okna przechowywania (dziennik).| 1. Zainicjuj plan BCP w organizacji. <br/><br/>2.  Zainicjuj nieplanowany tryb Failover przy użyciu portalu ASR do najnowszej lub punktu z okna przechowywania (dziennik).


## <a name="run-a-failover"></a>Uruchamianie trybu failover
Ta procedura opisuje sposób uruchomić tryb failover dla [planu odzyskiwania](site-recovery-create-recovery-plans.md). Alternatywnie można uruchomić tryb failover dla pojedynczej maszyny wirtualnej lub serwer fizyczny z **zreplikowane elementy** stronie zgodnie z opisem [tutaj](vmware-azure-tutorial-failover-failback.md#run-a-failover-to-azure).


![Tryb failover](./media/site-recovery-failover/Failover.png)

1. Wybierz **plany odzyskiwania** > *recoveryplan_name*. Kliknij przycisk **trybu Failover**
2. Na **trybu Failover** ekranu, wybierz opcję **punkt odzyskiwania** przejścia w tryb failover. Możesz użyć jednej z następujących opcji:
   1. **Najnowszy**: Ta opcja uruchamia zadanie przy pierwszym przetwarzanie wszystkich danych wysłanych do usługi Site Recovery. Przetwarzanie danych tworzy punkt odzyskiwania dla każdej maszyny wirtualnej. Ten punkt odzyskiwania jest używany przez maszynę wirtualną w tryb failover. Ta opcja zapewnia najniższy cel punktu odzyskiwania (cel punktu odzyskiwania) jako maszyna wirtualna utworzone po trybu failover zawiera wszystkie dane, które zostały zreplikowane do usługi Site Recovery podczas pracy w trybie failover zostało wyzwolone.
   1. **Najnowszy przetworzony**: Ta opcja wprowadza się przez wszystkie maszyny wirtualne w planie odzyskiwania do ostatniego punktu odzyskiwania, który został już przetworzony przez usługę Site Recovery. Podczas wykonywania testu trybu failover maszyny wirtualnej, to również wyświetlana jest sygnatura czasowa ostatniego punktu odzyskiwania przetworzonego. Jeśli przeprowadzasz trybu failover planu odzyskiwania, można przejść do poszczególnych maszyn wirtualnych i przyjrzyj się **najnowsze punkty odzyskiwania** Kafelek, aby uzyskać te informacje. Ponieważ nie jest zużywany czas przetwarzania nieprzetworzonych danych, ta opcja zapewnia niski opcji pracy awaryjnej cel czasu odzyskiwania (Recovery Time Objective).
   1. **Najnowszy spójny na poziomie aplikacji**: Ta opcja wprowadza się przez wszystkie maszyny wirtualne w planie odzyskiwania do ostatniego punktu odzyskiwania spójnego na poziomie aplikacji, która została już przetworzona przez usługę Site Recovery. Podczas wykonywania testu trybu failover maszyny wirtualnej, wyświetlane są również sygnaturę czasową najnowszy spójny na poziomie aplikacji punkt przywracania. Jeśli przeprowadzasz trybu failover planu odzyskiwania, można przejść do poszczególnych maszyn wirtualnych i przyjrzyj się **najnowsze punkty odzyskiwania** Kafelek, aby uzyskać te informacje.
   1. **Najnowsze wieloma Maszynami wirtualnymi przetwarzane**: Ta opcja jest dostępna tylko w przypadku planów odzyskiwania, które mają co najmniej jedną maszynę wirtualną z ON spójności wielu maszyn wirtualnych. Punkt maszyn wirtualnych, które są dostępne w ramach trybu failover grupy replikacji do najnowszej wspólnej odzyskiwania zapewniających spójność wielu maszyn wirtualnych. Inne maszyny wirtualne trybu failover, aby ich najnowszy przetworzony punkt przywracania.  
   1. **Najnowsze wieloma Maszynami wirtualnymi spójny na poziomie aplikacji**: Ta opcja jest dostępna tylko w przypadku planów odzyskiwania, które mają co najmniej jedną maszynę wirtualną z ON spójności wielu maszyn wirtualnych. Punkt maszyn wirtualnych, które są dostępne w ramach trybu failover grupy replikacji do najnowszej wspólnej odzyskiwania spójnego z aplikacją wielu maszyn wirtualnych. Inne maszyny wirtualne trybu failover, aby ich najnowszego punktu odzyskiwania spójnego na poziomie aplikacji.
   1. **Niestandardowy**: Jeśli przeprowadzasz test trybu failover maszyny wirtualnej, można użyć tej opcji w tryb failover do określonego punktu odzyskiwania.

      > [!NOTE]
      > Opcja wyboru punktu odzyskiwania jest dostępna tylko w przypadku, gdy są możesz przejść w tryb failover na platformie Azure.
      >
      >


1. Jeśli część maszyn wirtualnych w planie odzyskiwania zostały przełączone w tryb failover podczas poprzedniego uruchomienia, a teraz maszyny wirtualne są aktywne w miejscu źródłowym i docelowym, możesz użyć **zmianę kierunku** opcję, aby określić kierunek, w którym tryb failover powinno mieć miejsce.
1. Jeśli użytkownik przechodzenia w tryb failover na platformie Azure i szyfrowanie danych jest włączone dla chmury (ma zastosowanie tylko wtedy, gdy chronione maszyny wirtualne funkcji Hyper-v z serwerem programu VMM) w **klucza szyfrowania** wybierz certyfikat, który został wystawiony podczas włączania szyfrowanie danych podczas instalacji na serwerze programu VMM.
1. Wybierz **zamknięcie maszyny przed rozpoczęciem pracy awaryjnej** Jeśli chcesz, aby Usługa Site Recovery ma spróbować przeprowadzić zamknięcie źródłowych maszyn wirtualnych przed wyzwoleniem trybu failover. Tryb failover będzie kontynuowane, nawet jeśli zamknięcie nie powiedzie się.  

    > [!NOTE]
    > Jeśli maszyny wirtualne funkcji Hyper-v są chronione, możliwość wyłączenia również próbuje synchronizować dane lokalne, które nie zostały wysłane do usługi przed wyzwoleniem trybu failover.
    >
    >

1. Na stronie **Zadania** można śledzić postęp trybu failover. Nawet jeśli występują błędy podczas nieplanowany tryb failover, plan odzyskiwania uruchamia do czasu jego zakończenia.
1. Po przejściu w tryb failover Zweryfikuj maszynę wirtualną przez rejestrowanie się w nim. Jeśli chcesz się przełączyć do innego punktu odzyskiwania maszyny wirtualnej, a następnie można użyć **Zmień punkt odzyskiwania** opcji.
1. Po poprawnym skonfigurowaniu przełączania maszyny wirtualnej w tryb failover możesz **zatwierdzić** tryb failover. **Zatwierdzenie powoduje usunięcie wszystkich punktów odzyskiwania dostępnych w usłudze** i **Zmień punkt odzyskiwania** opcja nie jest już dostępna.

## <a name="planned-failover"></a>Planowany tryb failover
Maszyny wirtualne/serwery fizyczne chronione za pomocą Site Recovery również obsługę **zaplanowanym powrocie po awarii**. Planowany tryb failover jest zero utraty pracy awaryjnej opcję danych. Po wyzwoleniu planowanego trybu failover, pierwsze źródłowych maszyn wirtualnych jest wyłączona, najnowsze dane są synchronizowane i następnie wyzwoleniu przejścia w tryb failover.

> [!NOTE]
> Podczas pracy w trybie failover maszyn wirtualnych funkcji Hyper-v z jednej lokacji lokalnej do innej lokacji lokalnej powrót do lokacji podstawowej w środowisku lokalnym masz uprzedniego **replikacji odwrotnej** maszyny wirtualnej z powrotem do lokacji głównej i następnie Wyzwól tryb failover. Jeśli podstawowa maszyna wirtualna jest niedostępna, przed uruchomieniem do **replikacji odwrotnej** niezbędnych do przywrócenia maszyny wirtualnej z kopii zapasowej.   
> 
> 
> ## <a name="failover-job"></a>Zadanie trybu failover

![Tryb failover](./media/site-recovery-failover/FailoverJob.png)

Po wyzwoleniu przejścia w tryb failover obejmuje następujące kroki:

1. Sprawdzanie wymagań wstępnych: Ten krok zapewnia, że spełniono wszystkie warunki wymagane dla trybu failover
1. Tryb failover: Ten krok przetwarza dane i sprawia, że gotowy, aby z niej można utworzyć maszynę wirtualną platformy Azure. Jeśli wybrano **najnowsze** punktu odzyskiwania tego kroku jest tworzony punkt odzyskiwania z danych, który został wysłany do usługi.
1. Uruchamianie: Ten krok umożliwia utworzenie maszyny wirtualnej platformy Azure przy użyciu danych przetworzonych w poprzednim kroku.

> [!WARNING]
> **Nie Anuluj będące w toku pracy awaryjnej**: Przed uruchomieniem trybu failover replikacja maszyny wirtualnej zostanie zatrzymana. Jeśli użytkownik **anulować** w toku zadania, zostanie ono zatrzymane, ale maszyna wirtualna nie zostanie uruchomiona do replikacji. Nie można ponownie uruchomić replikację.
>
>

## <a name="time-taken-for-failover-to-azure"></a>Czas poświęcony na tryb failover na platformie Azure

W niektórych przypadkach tryb failover maszyn wirtualnych wymaga dodatkowych pośredniego kroku, który zwykle trwa około 8 – 10 minut. W następujących przypadkach czas potrzebny do trybu failover będzie wyższa niż zwykle:

* Maszyny wirtualne oprogramowania VMware za pomocą usługi mobilności w wersji starszej niż 9,8
* Serwerów fizycznych
* Maszyny wirtualne VMware z systemem Linux
* Maszyny wirtualne funkcji Hyper-V chronionych jako serwerów fizycznych
* Maszyny wirtualne VMware, w której następujące sterowniki nie są obecne jako sterowników rozruchowych
    * storvsc
    * vmbus
    * storflt
    * Intelide
    * ATAPI
* Adresy IP maszyn wirtualnych VMware, które nie mają usługę DHCP włączony niezależnie od tego, czy użytkownicy korzystają z protokołu DHCP lub statyczne

We wszystkich innych przypadkach ten pośredni krok nie jest wymagane i czas pracy w trybie failover jest niższa.

## <a name="using-scripts-in-failover"></a>Korzystanie ze skryptów w tryb Failover
Można zautomatyzować niektóre działania podczas wykonywania przejścia w tryb failover. Można użyć skryptów lub [elementy runbook automatyzacji Azure](site-recovery-runbook-automation.md) w [planów odzyskiwania](site-recovery-create-recovery-plans.md) Aby to zrobić.

## <a name="post-failover-considerations"></a>Zagadnienia dotyczące trybu failover wpis
Po pracy awaryjnej, należy wziąć pod uwagę następujące zalecenia:
### <a name="retaining-drive-letter-after-failover"></a>Przechowywanie literę dysku, po włączeniu trybu failover
Aby zachować litery dysku na maszynach wirtualnych, po włączeniu trybu failover, można ustawić **zasad sieci SAN** dla maszyny wirtualnej, aby **OnlineAll**. [Dowiedz się więcej](https://support.microsoft.com/help/3031135/how-to-preserve-the-drive-letter-for-protected-virtual-machines-that-are-failed-over-or-migrated-to-azure).

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Przygotowanie do połączenia z maszynami wirtualnymi Azure po przejściu do trybu failover

Jeśli chcesz nawiązać połączenie z maszynami wirtualnymi platformy Azure przy użyciu protokołu RDP/SSH po przejściu do trybu failover, upewnij się, że zostały spełnione wymagania podsumowane w [tej](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover) tabeli.

Wykonaj czynności opisane [tutaj](site-recovery-failover-to-azure-troubleshoot.md), aby rozwiązać wszystkie problemy z łącznością po przejściu do trybu failover.


## <a name="next-steps"></a>Kolejne kroki

> [!WARNING]
> Gdy zostały przełączone w tryb failover maszyny wirtualnej i lokalnym centrum danych jest dostępna, wykonaj następujące czynności [ **ponownie włączyć ochronę** ](vmware-azure-reprotect.md) maszyn wirtualnych VMware z powrotem do środowiska lokalnego centrum danych.

Użyj [ **zaplanowanym powrocie po awarii** ](hyper-v-azure-failback.md) opcję **powrotu po awarii** maszyn wirtualnych funkcji Hyper-v do środowiska lokalnego na platformie Azure.

Jeśli użytkownik nie powiodło się za pośrednictwem funkcji Hyper-v maszyny wirtualnej do innego środowiska lokalnego centrum danych zarządzanych przez serwer programu VMM i podstawowe centrum danych jest dostępna, następnie za pomocą **replikacja odwrotna** opcję, aby rozpocząć replikację do danych pierwotnych Centrum.
