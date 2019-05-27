---
title: Monitorowanie usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: Monitorowanie i rozwiązywanie problemów dotyczących problemów z replikacją usługi Azure Site Recovery i operacje przy użyciu portalu
author: raynew
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: raynew
ms.openlocfilehash: 8a3c9a4fb1b43c1d00d66fee3548ccd10a1e075e
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65969375"
---
# <a name="monitor-site-recovery"></a>Monitor Usługa Site Recovery

W tym artykule dowiesz się, jak używać usługi Azure Site Recovery w wbudowanych funkcji monitorowania do monitorowania i rozwiązywania problemów. 

## <a name="use-the-dashboard"></a>Korzystanie z pulpitu nawigacyjnego

1. W magazynie kliknij **Przegląd** aby otworzyć pulpit nawigacyjny Site Recovery. Brak stron pulpitu nawigacyjnego, zarówno usługa Site Recovery i tworzenie kopii zapasowych i przełączać się między nimi.

    ![Pulpit nawigacyjny odzyskiwania lokacji](./media/site-recovery-monitor-and-troubleshoot/dashboard.png)

2.  Pulpit nawigacyjny konsoliduje wszystkie informacje monitorowania dla magazynu w obrębie jednej lokalizacji. Na pulpicie nawigacyjnym możesz przejść do szczegółów różnych obszarów. 

    ![Pulpit nawigacyjny odzyskiwania lokacji](./media/site-recovery-monitor-and-troubleshoot/site-recovery-overview-page.png).

3. Na **zreplikowane elementy**, kliknij przycisk **Wyświetl wszystko** aby zobaczyć wszystkie serwery w magazynie.
4. Przejść do szczegółów, klikając pozycję Szczegóły stanu w każdej sekcji. W **widok infrastruktury**, informacje monitorowania można sortować według rodzaju replikujesz maszyny.

## <a name="monitor-replicated-items"></a>Monitorowanie replikowane elementy

Sekcja zreplikowane elementy nie pokazuje kondycję wszystkich maszyn, które mają włączoną w magazynie replikację.

**State** | **Szczegóły**
--- | ---
W dobrej kondycji | Zwykle jest wykonywana replikacja. Nie błąd lub ostrzeżenie objawów są wykrywane.
Ostrzeżenie | Objawy ostrzeżenia, które mogą mieć wpływ na replikację są wykrywane.
Krytyczne | Wykryto co najmniej jeden objawy błąd replikacji krytycznej.<br/><br/> Następujące objawy błędu są zazwyczaj wskaźniki replikacja została zablokowana lub nie postępuje, tak szybko, jak współczynnik zmian danych.
Nie dotyczy | Serwery, które nie są obecnie powinien być replikacji. Może to obejmować maszyn, które zostały przełączone w tryb failover.

## <a name="monitor-test-failovers"></a>Monitor testu pracy w trybie Failover

Możesz wyświetlić stan testu trybu failover dla maszyn w magazynie.

- Firma Microsoft zaleca się uruchomienie testu trybu failover na replikowanych maszynach co najmniej raz na każde sześć miesięcy. Jest to sposób, aby sprawdzić, czy tryb failover działa zgodnie z oczekiwaniami, bez zakłócania działania środowiska produkcyjnego. 
- Testowanie trybu failover jest uznawany za pomyślny, tylko wtedy, gdy czyszczenie trybu failover i po przełączeniu w tryb failover została ukończona pomyślnie.

**State** | **Szczegóły**
--- | ---
Test jest zalecany | Maszyny, które nie mam testowy tryb failover, ponieważ ochrona została włączona.
Wykonano pomyślnie | Maszyny z lub osiągania sukcesów testowych trybów Failover.
Nie dotyczy | Maszyny, które nie są aktualnie kwalifikuje się do testowania trybu failover. Na przykład maszyny, przełączone w tryb failover, mają początkowej replikacji i testowania trybu failover i pracy awaryjnej w toku.

## <a name="monitor-configuration-issues"></a>Problemy z konfiguracją monitorem

**Problemy z konfiguracją** sekcja pokazuje listę problemów, które mogą mieć wpływ na możliwość pomyślnie w trybie Failover.

- Problemy z konfiguracją w (z wyjątkiem dostępności aktualizacji oprogramowania), są wykrywane przez operację okresowe modułu sprawdzania poprawności, która jest domyślnie uruchamiany co 12 godzin. Można wymusić operacji modułu sprawdzania poprawności, aby natychmiast uruchomić, klikając ikonę odświeżania obok pozycji **problemy z konfiguracją** nagłówek sekcji.
- Kliknij łącza, aby uzyskać więcej szczegółów. W przypadku problemów wpływających na określonych maszyn, kliknij przycisk **wymaga uwagi** w **docelowe konfiguracje** kolumny. Szczegółowe informacje obejmują zaleceniami związanymi z korygowaniem.

**State** | **Szczegóły**
--- | ---
Brak konfiguracji | Brak wymaganych ustawień, takich jak sieć odzyskiwania lub grupy zasobów.
Brak zasobów | Określony zasób, nie można odnaleźć lub nie jest dostępna w ramach subskrypcji. Na przykład zasób został usunięty lub migracji. Monitorowanych zasobów uwzględnione w docelowej grupie zasobów, docelowej sieci wirtualnej/podsieci, dziennika/docelowego konta magazynu, docelowy zestaw dostępności, docelowy adres IP.
Limit przydziału subskrypcji |  Saldo limitu przydziału zasobów dostępnych subskrypcji jest porównywana saldo potrzebne do trybu failover wszystkich maszyn w magazynie.<br/><br/> Jeśli nie ma wystarczającej liczby zasobów, jest zgłaszany saldo za mały limit przydziału.<br/><br/> Przydziały monitorowania liczby rdzeni maszyn wirtualnych, liczba rdzeni rodziny maszyn wirtualnych, liczby karta sieciowa interfejsów sieciowych.
Aktualizacje oprogramowania | Dostępność nowych aktualizacji oprogramowania oraz informacje o wygasające wersje oprogramowania.


## <a name="monitoring-errors"></a>Monitorowanie błędów 
**Souhrn chyb** sekcji opisano objawy aktualnie aktywnych błędów, które mogą mieć wpływ na replikację serwerów w magazynie i liczbę maszyn objęte wpływem.

- Na początku sekcji, są wyświetlane błędy, wpływ na składniki infrastruktury lokalnej. Na przykład bez otrzymaniu pulsu dostawcy usługi Azure Site Recovery uruchomionej na lokalny serwer konfiguracji, serwer programu VMM lub hosta funkcji Hyper-V.
- Następnie objawy błąd replikacji mogących mieć wpływ na replikowanych serwerów są wyświetlane.
- Wpisy tabeli są sortowane, zmniejszając kolejności ważność błędu, a następnie dzięki skróceniu kolejności liczba maszyn, których to dotyczy.
- Liczba serwerów objęte wpływem to wygodny sposób, aby dowiedzieć się, czy pojedynczy podstawowy problem może mieć wpływ na wielu komputerach. Na przykład błędem sieci potencjalnie może mieć wpływ na wszystkie maszyny replikowanie do platformy Azure. 
- Wiele błędy replikacji mogą występować na jednym serwerze. W tym przypadku objaw każdy błąd zlicza tego serwera na liście serwerów znajdujących się na których to dotyczy. Po rozwiązaniu problemu poprawić parametry replikacji, a błąd zostanie usunięte z komputera.

## <a name="monitor-the-infrastructure"></a>Monitorowanie infrastruktury.

**Widok infrastruktury** przedstawiono składniki infrastruktury zaangażowanych w replikacji i kondycję połączenia między serwerami i usługami platformy Azure.

- Zielona linia wskazuje, że połączenie jest w dobrej kondycji.
- Czerwona linia z ikoną nałożone błąd wskazuje na istnienie objawy błędu co najmniej jeden tego mieć wpływ na łączność.
-  Umieść wskaźnik myszy nad ikonie błędu, aby pokazać błąd i liczby jednostek objęte wpływem. Kliknij ikonę filtrowana lista jednostek objęte wpływem.

    ![Widok infrastruktury odzyskiwania lokacji (magazyn)](./media/site-recovery-monitor-and-troubleshoot/site-recovery-vault-infra-view.png)

## <a name="tips-for-monitoring-the-infrastructure"></a>Porady dotyczące monitorowania infrastruktury

- Upewnij się, że składniki infrastruktury lokalnej (serwer konfiguracji, proces serwery, serwery programu VMM, hosty funkcji Hyper-V, maszyn VMware) działają najnowszych wersji dostawcy usługi Site Recovery i/lub agentów.
- Aby korzystać ze wszystkich funkcji w widoku infrastruktury, powinien być uruchomiony [22 pakiet zbiorczy aktualizacji](https://support.microsoft.com/help/4072852) tych składników.
- Aby użyć widoku infrastruktury, wybierz scenariusz replikacji odpowiednie w Twoim środowisku. Można przejść do szczegółów w widoku, aby uzyskać więcej informacji. W poniższej tabeli przedstawiono scenariusze, które są reprezentowane.

    **Scenariusz** | **State**  | **Wyświetl dostępne?**
    --- |--- | ---
    **Replikacja między lokacjami lokalnymi** | Wszystkie stany | Nie 
    **Replikacja maszyn wirtualnych usługi Azure między regionami platformy Azure**  | Replikacja włączona początkowej replikacji w toku | Tak
    **Replikacja maszyn wirtualnych usługi Azure między regionami platformy Azure** | W trybie Failover / powrotu po awarii | Nie   
    **Replikacja VMware do platformy Azure** | Replikacja włączona początkowej replikacji w toku | Tak     
    **Replikacja VMware do platformy Azure** | Nie można ponownie w tryb failover lub nie powiodła się | Nie      
    **Replikacja funkcji Hyper-V do platformy Azure** | Nie można ponownie w tryb failover lub nie powiodła się | Nie

- Aby wyświetlić widok infrastruktury dla pojedynczej maszyny replikowania, w menu magazynu kliknij **zreplikowane elementy**i wybierz serwer.  

### <a name="common-questions"></a>Często zadawane pytania


**Dlaczego liczby maszyn wirtualnych w systemie innym niż łączna liczba widok infrastruktury magazynu jest wyświetlany w zreplikowanych elementów?**

Widok infrastruktury magazynu jest w zakresie scenariusze replikacji. Tylko maszyny w scenariuszu aktualnie wybranego replikacji znajdują się w liczbę elementów w widoku. Ponadto zliczamy jedynie maszyny wirtualne, które są skonfigurowane do replikacji do platformy Azure. Nie powiodło się maszyn w tryb failover lub maszyny replikowanie do lokacji lokalnej nie są liczone w widoku.

**Dlaczego jest liczba zreplikowane elementy wyświetlane w szufladzie Essentials różni się od łącznej liczby zreplikowane elementy na pulpicie nawigacyjnym?**

Tylko maszyny, dla których Replikacja początkowa została zakończona są objęte liczby wyświetlane w szufladzie Essentials. Zreplikowane elementy łączna obejmuje wszystkie maszyny w w magazynie, łącznie z tymi dla których początkowa replikacja jest w toku.


## <a name="monitor-recovery-plans"></a>Monitor planów odzyskiwania

W **sekcji plany odzyskiwania** Przejrzyj wiele planów, Utwórz nowe plany i modyfikować istniejące.  

## <a name="monitor-jobs"></a>Monitorowanie zadań

**Zadań** sekcji odzwierciedla stan operacjami usługi Site Recovery.

- Większość operacji w usłudze Azure Site Recovery jest wykonywana asynchronicznie, przy użyciu zadania śledzenia są tworzone i używane do śledzenia postępu operacji. 
- Obiekt zadania zawiera wszystkie informacje, które musisz śledzić stan i postęp operacji. 

Monitorowanie zadań w następujący sposób:

1. Na pulpicie nawigacyjnym > **zadań** sekcji zostanie wyświetlone podsumowanie zadań, które zostały wykonane, są w toku lub Oczekiwanie na dane wejściowe, w ciągu ostatnich 24 godzin. Możesz kliknąć dowolny stan, aby uzyskać więcej informacji na temat istotnych zadań.
2. Kliknij przycisk **Wyświetl wszystkie** Aby wyświetlić wszystkie zadania w ciągu ostatnich 24 godzin.

    > [!NOTE]
    > Informacje o zadaniu jest także dostępny w menu magazynu > **zadania usługi Site Recovery**. 

2. W **zadania usługi Site Recovery** zostanie wyświetlona lista, listy zadań. W górnym menu, które mogą uzyskać szczegóły błędu dla poszczególnych zadań przefiltrować listę zadań, w oparciu o określone kryteria i Eksportuj wybrane szczegóły zadania do programu Excel.
3. Możesz przejść do zadania, klikając go. 

## <a name="monitor-virtual-machines"></a>Monitorowanie maszyn wirtualnych

Na pulpicie nawigacyjnym dodatku można monitorować maszyn, na stronie maszyn wirtualnych. 

1. W magazynie kliknij **zreplikowane elementy** można pobrać listy replikowanych maszyn.  Można też uzyskać do filtrowana lista elementów chronionych, klikając dowolny z zakresami skróty na stronie pulpitu nawigacyjnego.

    ![Usługa Site Recovery zreplikowane elementy widoku listy](./media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-list-view.png)

2. Na **zreplikowane elementy** strony, można wyświetlić i filtrować informacje. W menu Akcja u góry można wykonać akcje dla określonego komputera, takich jak uruchamianie testu trybu failover lub wyświetlanie określonych błędów.
3. Kliknij przycisk **kolumn** Aby wyświetlić dodatkowe kolumny, na przykład wyświetlić cel punktu odzyskiwania, przeznaczony dla problemów z konfiguracją oraz błędy replikacji.
4. Kliknij przycisk **filtr** do wyświetlania informacji na podstawie określonych parametrów, takich jak kondycja replikacji lub zasady replikacji w określonej.
5. Kliknij prawym przyciskiem myszy maszynę, aby zainicjować operacji, takich jak testowanie trybu failover dla niego lub aby wyświetlić szczegółowe informacje o błędzie skojarzony z nim.
6. Kliknij maszynę, aby przejść do bardziej szczegółowe informacje dla niego. Szczegółowe informacje obejmują:
   - **Informacje o replikacji**: Bieżący stan i kondycję komputera.
   - **Cel punktu odzyskiwania** (cel punktu odzyskiwania): Bieżący cel punktu odzyskiwania dla maszyny wirtualnej i czas ostatniego obliczane cel punktu odzyskiwania.
   - **Punkty odzyskiwania**: Najnowsze dostępne punkty odzyskiwania dla maszyny.
   - **Gotowość do pracy awaryjnej**: Wskazuje, czy test trybu failover zostało uruchomione dla komputera, wersja agenta uruchomione na maszynie (dla maszyn, na którym jest uruchomiona usługa mobilności) oraz problemów dotyczących konfiguracji.
   - **Błędy**: Lista objawy błąd replikacji obecnie obserwowana na komputera i możliwych przyczyn/działań.
   - **Zdarzenia**: Chronologiczną listę ostatnie zdarzenia wpływające na ochronę na maszynie. Szczegóły błędu pokazuje objawy błędu obecnie zauważalne, gdy zdarzenia jest historycznym problemy, które mają wpływ na maszynie.
   - **Widok infrastruktury**: Pokazuje stan infrastruktury dla scenariusza podczas replikowania maszyn na platformę Azure.

     ![Usługa Site Recovery zreplikowany element szczegóły/Przegląd](./media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-details.png)


### <a name="common-questions"></a>Często zadawane pytania

**Jak cel punktu odzyskiwania jest inny niż najnowszy dostępny punkt przywracania**


- Usługa Site Recovery używa wieloetapowy proces asynchroniczny, aby replikować maszyny do platformy Azure.
- W kroku przedostatni replikacji ostatnie zmiany na maszynie, wraz z metadanymi, są kopiowane do dziennika/konto magazynu pamięci podręcznej.
- Te zmiany, oraz tag, aby zidentyfikować punkt możliwe do odzyskania są zapisywane na koncie magazynu w regionie docelowym.
-  Usługa Site Recovery może wygenerować teraz możliwe do odzyskania punktu dla maszyny wirtualnej.
- W tym momencie zmiany tej pory przekazane do konta magazynu zostały spełnione cel punktu odzyskiwania. Innymi słowy, cel punktu odzyskiwania maszyny w tym momencie jest równa ilość czasu, jaki upłynął od sygnatura czasowa odpowiadająca punktowi możliwe do odzyskania.
- Teraz Usługa Site Recovery wybiera przekazywane dane z konta magazynu i dotyczą dysków repliki utworzony dla maszyny.
- Usługa Site Recovery generuje punkt odzyskiwania i udostępnia tego punktu odzyskiwania w trybie failover. Ten sposób najnowszego dostępnego punktu odzyskiwania wskazuje sygnatura czasowa odpowiadający najnowszy punkt odzyskiwania, które już zostały przetworzone i zastosowane do dysków repliki.

> [!NOTE]
> Niepoprawne systemu na replikującej maszynie źródłowej lub na serwerach infrastruktury lokalnej będzie czasowego obliczona wartość celu punktu odzyskiwania. Dokładne raportowania cel punktu odzyskiwania, upewnij się, czy zegar systemowy jest prawidłowa na wszystkich serwerach i komputerach. 

## <a name="subscribe-to-email-notifications"></a>Subskrybowanie powiadomień e-mail

Można subskrybować otrzymywanie powiadomień e-mail dla tych zdarzeń krytycznych:
 
- Stan krytyczny dla zreplikowanej maszyny.
- Brak łączności między składnikami infrastruktury w środowisku lokalnym i usługą Site Recovery. Wykryto połączenia między serwerami Site Recovery i w środowisku lokalnym, które są rejestrowane w magazynie przy użyciu mechanizmu pulsu.
- Błędy trybu failover.

Subskrybuj w następujący sposób:

W magazynie > **monitorowanie i raporty** kliknij **zdarzeń usługi Site Recovery**.
1. Kliknij przycisk **wiadomości E-mail z powiadomieniami**.
1. W **powiadomienia E-mail**włączymy powiadomienia i określić, kto wysyłać. Możesz wysłać wysłane do wszystkich administratorów subskrypcji powiadomień i adresy opcjonalnie określonej wiadomości e-mail.

    ![Powiadomienia e-mail](./media/site-recovery-monitor-and-troubleshoot/email.png)
