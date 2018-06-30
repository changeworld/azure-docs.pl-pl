---
title: Monitorowanie i rozwiązywanie problemów z usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: Monitorowanie i rozwiązywanie problemów z replikacją usługi Azure Site Recovery i operacje przy użyciu portalu
services: site-recovery
documentationcenter: ''
author: bsiva
manager: abhemraj
editor: raynew
ms.assetid: ''
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 02/22/2018
ms.author: bsiva
ms.openlocfilehash: 9a979cc940e2133e3dff3bb7dd4c5f2ec53fea7a
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37109505"
---
# <a name="monitoring-and-troubleshooting-azure-site-recovery"></a>Monitorowanie i rozwiązywanie problemów z usługi Azure Site Recovery

W tym artykule Dowiedz się jak używać usługi Azure Site Recovery w wbudowanych funkcji monitorowania do monitorowania i rozwiązywania problemów. Instrukcje:
> [!div class="checklist"]
> - Pulpit nawigacyjny usługi Azure Site Recovery (strona magazynu — omówienie)
> - Monitorowanie i rozwiązywanie problemów z replikacją
> - Monitor usługi Azure Site Recovery zadania/Operations
> - Subskrybowanie powiadomień e-mail

## <a name="using-the-azure-site-recovery-dashboard"></a>Przy użyciu pulpitu nawigacyjnego usługi Azure Site Recovery

Na pulpicie nawigacyjnym usługi Azure Site Recovery na stronie Przegląd magazynu konsoliduje wszystkich informacji o monitorowaniu dla magazynu w jednej lokalizacji. Rozpocznij na pulpicie nawigacyjnym magazynu i lepsze zapoznanie się z Aby uzyskać więcej informacji, przejdź do części pulpitu nawigacyjnego. Główne elementy pulpitu nawigacyjnego usługi Azure Site Recovery, są następujące:

### <a name="1-switch-between-azure-backup-and-azure-site-recovery-dashboards"></a>1. Przełączać się między kopia zapasowa Azure i usługi Azure Site Recovery pulpity nawigacyjne

Przełącznik przełączania w górnej części strony Przegląd pozwala przełączać się między stron pulpitu nawigacyjnego dla usługi Site Recovery i kopii zapasowej. Tego wyboru dokonanych jest zapamiętanych i domyślnie przy następnym otwarciu strony Przegląd magazynu. Wybierz opcję usługi Site Recovery, aby wyświetlić pulpit nawigacyjny usługi Site Recovery. 

Różne części strony pulpitu nawigacyjnego usługi Azure Site Recovery automatycznie odświeżyć co 10 minut, dzięki czemu pulpit nawigacyjny odzwierciedla najnowszych informacji.

![Funkcje monitorowania na stronie przeglądu usługi Azure Site Recovery](media/site-recovery-monitor-and-troubleshoot/site-recovery-overview-page.png)

### <a name="2-replicated-items"></a>2. Zreplikowanych elementów

Sekcja zreplikowanych elementów pulpitu nawigacyjnego przedstawia przegląd kondycji replikacji z chronionych serwerów w magazynie. 

<table>
<tr>
    <td>W dobrej kondycji</td>
    <td>Zwykle postępuje replikacji dla tych serwerów i bez błędu lub wykryto objawy ostrzeżenie.</td>
</tr>
<tr>
    <td>Ostrzeżenie </td>
    <td>Wykryto objawy ostrzeżenia, które mogą mieć wpływ na replikację wskazująca, że replikacja nie jest zwykle postępu na tych serwerach.</td>
</tr>
<tr>
    <td>Krytyczne</td>
    <td>Wykryto co najmniej jeden objawy błąd replikacji krytycznej na tych serwerach. Tych symptomów błąd jest zwykle wskaźniki replikacji albo jest zablokowany lub nie jest wykonywane tak szybko, jak dane zmienić częstotliwość tych serwerów.</td>
</tr>
<tr>
    <td>Nie dotyczy</td>
    <td>Serwery, które nie są obecnie oczekiwano replikacji, takich jak serwery, które przejścia w tryb failover.</td>
</tr>
</table>

Aby wyświetlić listę serwerów chronionych filtrowane według stanu replikacji, kliknij przycisk opis kondycji replikacji obok pierścień. Żądany widok wszystkich link obok tytuł sekcji jest skrót do strony zreplikowanych elementów magazynu. Użyj widoku się, że wszystkie łącze, aby wyświetlić listę wszystkich serwerów w magazynie.

### <a name="3-failover-test-success"></a>3. Powodzenie test trybu failover

Sekcja Powodzenie testu pracy awaryjnej pulpitu nawigacyjnego przedstawia rozpad maszyn wirtualnych w magazynie, na podstawie stanu trybu failover testu. 

<table>
<tr>
    <td>Zaleca się test</td>
    <td>Maszyny wirtualne, których nie zainstalowano pomyślnie test trybu failover od czasu osiągnęła stan chronionych.</td>
</tr>
<tr>
    <td>Wykonana pomyślnie</td>
    <td>Maszyny wirtualne, które miały co najmniej jeden pomyślnie testu pracy w trybie Failover.</td>
</tr>
<tr>
    <td>Nie dotyczy</td>
    <td>Maszyny wirtualne, które nie są obecnie kwalifikują się do testowania trybu failover. Przykłady: nie powiodło się za pośrednictwem serwerów, serwery, dla których Replikacja początkowa jest w toku, serwery, dla których trybu failover jest w toku, serwery, dla których testowy tryb failover jest już w toku.</td>
</tr>
</table>

Kliknij stan pracy awaryjnej testów obok pierścień, więc zapoznać się z listą serwerów chronionych na podstawie stanu testowego trybu failover.
 
> [!IMPORTANT]
> Najlepszym rozwiązaniem zalecane jest wykonanie testowego trybu failover na serwerach chronionych co najmniej co sześć miesięcy. Wykonanie testowego trybu failover z systemem innym niż destrukcyjne umożliwia testowanie trybu failover serwerów i aplikacji do środowiska izolowanego i pomoże Ci ocenić Twoje gotowości ciągłości biznesowej.

 Operacja test trybu failover na serwerze lub plan odzyskiwania jest uznawany za pomyślny, dopiero po zarówno operację testowania pracy awaryjnej i operacji oczyszczania testowania trybu failover została ukończona pomyślnie.

### <a name="4-configuration-issues"></a>4. Problemy z konfiguracją

W sekcji problemy konfiguracji Lista problemów, które mogą mieć wpływ na możliwość pomyślnie pracy awaryjnej maszyn wirtualnych. Występują następujące klasy problemów wymienione w tej sekcji:
 - **Brak konfiguracji:** chronionymi serwerami brakujące wymagane konfiguracje, takie jak sieć odzyskiwania lub odzyskiwania grupy zasobów.
 - **Brak zasobów:** skonfigurowane zasobów docelowych do odzyskiwania danych nie została odnaleziona lub nie jest dostępna w subskrypcji. Na przykład zasób został usunięty lub został zmigrowany do innej subskrypcji lub grupy zasobów. Następujące konfiguracje docelowych do odzyskiwania danych są monitorowane pod kątem dostępności: docelowa grupa zasobów, docelowych wirtualnych sieci i podsieci, dziennika/docelowe konto magazynu, docelowego zestawu dostępności, docelowy adres IP.
 - **Limit przydziału dla subskrypcji:** równowadze przydziału zasobów dostępnych subskrypcji jest porównywana saldo musi być w stanie failover wszystkich maszyn wirtualnych w magazynie. Jeśli zostanie znaleziony za mało dostępnego salda, jest zgłaszany saldo przydział jest niewystarczający. Przydziały dla następujących zasobów platformy Azure są monitorowane: liczba rdzeni maszyn wirtualnych, liczba rdzeni rodziny maszyn wirtualnych, liczba kart (NIC) interfejsu sieci.
 - **Aktualizacje oprogramowania:** dostępność nowych aktualizacji oprogramowania, wygasa wersje oprogramowania.

Wykryto problemy z konfiguracją (inne niż dostępności aktualizacji oprogramowania), za pomocą operacji okresowe modułu sprawdzania poprawności, która domyślnie uruchamiane co 12 godzin. Możesz wymusić operacji modułu sprawdzania poprawności, aby natychmiast uruchomić, klikając ikonę odświeżania obok pozycji *problemy z konfiguracją* nagłówek sekcji.

Kliknij łącza, aby uzyskać więcej informacji dotyczących wymienionych problemów i ich wpływ maszyn wirtualnych. W przypadku problemów wpływających na określone maszyny wirtualne, więcej informacji można uzyskać, klikając **wymaga uwagi** łącze w kolumnie konfiguracji docelowego dla maszyny wirtualnej. Szczegóły obejmuje zalecany sposób można skorygować wykrytych problemów.

### <a name="5-error-summary"></a>5. Podsumowanie błędów

Sekcja podsumowania błędu zawiera symptomy błąd aktualnie aktywna replikacja może mieć wpływ na replikacji serwerów w magazynie, wraz z liczbą wpływ na podmioty ze względu na każdym błędzie.

Dla serwerów w stan kondycji replikacji krytyczna lub poważna objawy błąd replikacji można wyświetlić w tabeli podsumowania błędów. 

- Błędy wpływające na lokalnej infrastruktury składniki, takie jak z systemem innym niż odbieranie pulsu od dostawcy usługi Azure Site Recovery uruchomionych na serwerze konfiguracji lokalnego, serwer programu VMM lub hosta funkcji Hyper-V są wyświetlane na początku podsumowania błędu sekcja
- Objawy błąd replikacji wpływu na chronione serwery jest wyświetlane obok. Wpisów tabeli podsumowania błędu są sortowane dzięki skróceniu kolejność ważność błędu, a następnie dzięki skróceniu kolejności liczby wpływ na serwerach.
 

> [!NOTE]
> 
>  Wiele objawy błąd replikacji można zaobserwować na jednym serwerze. Jeśli istnieje wiele objawy błąd na jednym serwerze objaw każdego błędu zlicza tego serwera na liście jego wpływ na serwerach. Po rozwiązaniu podstawowego problemu, co powoduje błąd objaw poprawy parametry replikacji, a błąd są czyszczone z maszyny wirtualnej.
>
> > [!TIP]
> Liczba serwerów wpływ na to wygodny sposób, aby zrozumieć, jeśli pojedynczy podstawowego problemu może mieć wpływ na wielu serwerach. Na przykład błąd sieci może wpłynąć wszystkie serwery replikacji z lokacji lokalnej na platformie Azure. Ten widok przekazuje szybko tego ustalania, czy jeden podstawowego problemu naprawi replikacji dla wielu serwerów.
>

### <a name="6-infrastructure-view"></a>6. Widok infrastruktury

Widok infrastruktury zapewnia scenariusz rozsądnego wizualną reprezentację infrastrukturalne elementów związanych z replikacji. On również wizualnie przedstawia kondycję połączenia między różnymi serwerami i między serwerami i związane z replikacją usług Azure. 

Zielony wiersza wskazuje, czy połączenie jest w dobrej kondycji, podczas linii czerwoną ikoną błędu nałożone oznacza istnienie jednego lub więcej objawy błąd wpływu na łączność między składniki zaangażowane. Kursor myszy na ikonie błąd w wierszu zawiera błąd i liczbę jednostek objęte wpływem. 

Kliknięcie ikony błędu zawiera filtrowana lista wpływ na podmioty na błędy.

![Widok infrastruktury odzyskiwania lokacji (magazyn)](media/site-recovery-monitor-and-troubleshoot/site-recovery-vault-infra-view.png)

> [!TIP]
> Upewnij się, że składniki infrastruktury lokalnej (serwerze konfiguracji serwerów dodatkowych procesów replikowanie maszyn wirtualnych VMware, hosty funkcji Hyper-V, serwery VMM) są uruchomione najnowszą wersję oprogramowania usługi Azure Site Recovery. Aby móc korzystać ze wszystkich funkcji widoku infrastruktury, musisz korzystać z [zbiorczy 22](https://support.microsoft.com/help/4072852) lub nowsze dla usługi Azure Site Recovery

Aby użyć widoku infrastruktury, wybierz odpowiednie replikacji scenariusza (maszyn wirtualnych platformy Azure, programu VMware server maszyny wirtualnej/fizycznej lub funkcji Hyper-V) w zależności od środowiska źródłowego. Widok infrastruktury przedstawione na stronie przeglądu magazynu jest zagregowany widok magazynu. Można przejść do szczegółów dalsze do pojedynczych składników, klikając pola.

Widok infrastruktury, zakres w kontekście jednej maszynie replikacji jest dostępny na stronie Przegląd zreplikowanych elementów. Aby przejść do strony Przegląd replikacji serwera, przejdź do zreplikowanych elementów w menu magazynu i wybierz serwer, aby wyświetlić szczegóły.

### <a name="infrastructure-view---faq"></a>Widok infrastruktury — często zadawane pytania

**PYTANIE** Dlaczego nie widzę widoku infrastruktury dla moich maszyny Wirtualnej? </br>
**ODPOWIEDŹ** Infrastruktura widoku funkcja jest dostępna tylko dla maszyn wirtualnych, które są replikowane do platformy Azure. Funkcja nie jest obecnie dostępna dla maszyn wirtualnych, które jest replikowana między lokacjami lokalnymi.

**PYTANIE** Dlaczego liczby maszyn wirtualnych w widoku infrastruktury magazynu inny niż łączna liczba jest wyświetlana w pierścień zreplikowanych elementów?</br>
**ODPOWIEDŹ** Widok infrastruktury magazynu jest ograniczone w zależności od scenariuszy replikacji. Tylko maszyny wirtualne z uczestnictwa w scenariuszu aktualnie zaznaczonego replikacji są uwzględnione w liczbie maszyn wirtualnych w widoku infrastruktury. Ponadto dla wybranego scenariusza, tylko maszyny wirtualne, które są aktualnie skonfigurowane do replikacji do platformy Azure są uwzględnione w liczbie maszyn wirtualnych w widoku infrastruktury (przykład Fo: przejścia w tryb failover maszyny wirtualnej, Utwórz kopię maszyn wirtualnych replikowanych do lokacji lokalnej nie znajdują się w widoku infrastruktury.)

**PYTANIE** Dlaczego jest liczba replikowanych elementy(ów) w szufladzie essentials na stronie Przegląd różnych z łącznej liczby zreplikowanych elementów wyświetlane na wykresie pierścień na pulpicie nawigacyjnym?</br>
**ODPOWIEDŹ** Tylko maszyny wirtualne, dla których Replikacja początkowa została ukończona są uwzględnione w liczbie wyświetlany w szufladzie essentials. Całkowita liczba pierścień zreplikowanych elementów obejmuje wszystkie maszyny wirtualne w magazynie, włącznie z serwerami dla których Replikacja początkowa jest w toku.

**PYTANIE** Scenariusze replikacji, która jest dostępna dla widoku infrastruktury? </br>
**ODPOWIEDŹ**
>[!div class="mx-tdBreakAll"]
>|Scenariusz replikacji  | Stan maszyny Wirtualnej  | Widok infrastruktury dostępne  |
>|---------|---------|---------|
>|Maszyn wirtualnych replikowanych między dwoma lokacjami lokalnymi     | -        | Nie      |
>|Wszyscy     | Przełączenie do trybu failover         |  Nie       |
>|Maszyn wirtualnych replikowanych między dwóch regionach platformy Azure     | Replikacja początkowa w toku lub chronione         | Yes         |
>|Maszyny wirtualne VMware replikację do platformy Azure     | Replikacja początkowa w toku lub chronione        | Yes        |
>|Maszyny wirtualne VMware replikację do platformy Azure     | Przełączenie do trybu failover replikowane do lokacji oprogramowania VMware lokalne maszyny wirtualne         | Nie        |
>|Maszyny wirtualne funkcji Hyper-V replikację do platformy Azure     | Replikacja początkowa w toku lub chronione        | Yes       |
>|Maszyny wirtualne funkcji Hyper-V replikację do platformy Azure     | Przełączenie do trybu failover / powrotu po awarii w toku        |  Nie       |


### <a name="7-recovery-plans"></a>7. Plany odzyskiwania

W sekcji planów odzyskiwania informuje o liczbie planów odzyskiwania w magazynie. Kliknij liczbę, aby zapoznać się z listą plany odzyskiwania, Utwórz nowe plany odzyskiwania lub edytować istniejące. 

### <a name="8-jobs"></a>8. Zadania

Zadania usługi Azure Site Recovery śledzenie stanu operacji usługi Azure Site Recovery. Większość operacji w usłudze Azure Site Recovery są wykonywane asynchronicznie, za pomocą zadania śledzenia używane do śledzenia postęp operacji.  Aby dowiedzieć się, jak monitorować stan operacji, zobacz [Monitor Azure lokacji zadania/operacje odzyskiwania](#monitor-azure-site-recovery-jobsoperations) sekcji.

W tej sekcji zadań pulpitu nawigacyjnego przedstawiono następujące informacje:

<table>
<tr>
    <td>Niepowodzenie</td>
    <td>Niepowodzenie zadania usługi Azure Site Recovery w ostatnich 24 godzinach</td>
</tr>
<tr>
    <td>W toku</td>
    <td>Azure zadania usługi Site Recovery, które są obecnie w toku</td>
</tr>
<tr>
    <td>Trwa oczekiwanie na dane wejściowe</td>
    <td>Azure Site Recovery zadania, które są obecnie wstrzymane oczekiwanie na dane wejściowe użytkownika.</td>
</tr>
</table>

Wyświetl wszystkie łącze obok nagłówek sekcji jest skrót, aby przejść do strony listy zadań.

## <a name="monitor-and-troubleshoot-replication-issues"></a>Monitorowanie i rozwiązywanie problemów z replikacją

Oprócz informacji dostępnych na stronie pulpitu nawigacyjnego magazynu możesz uzyskać dodatkowe szczegóły i informacje dotyczące rozwiązywania problemów, na stronie listy maszyn wirtualnych i na stronie Szczegóły maszyny wirtualnej. Możesz wyświetlić listę chronionych maszyn wirtualnych w magazynie, wybierając **elementy replikowane** opcję w menu magazynu. Alternatywnie Ci na listę filtrowane chronione elementy klikając zakresami skróty dostępne na stronie pulpitu nawigacyjnego magazynu.

![Usługa Site Recovery replikowane widoku listy elementów](media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-list-view.png)

Opcja filtra na stronie zreplikowanych elementów listy umożliwia zastosowanie różnych filtrów, takich jak kondycję replikacji i zasad replikacji. 

Selektor kolumn, opcja umożliwia określenie dodatkowych kolumn do pokazania, takich jak cel punktu odzyskiwania, problemy z konfiguracją docelowych i błędy replikacji. Możesz zainicjować operacji na maszynie wirtualnej lub wyświetlanie błędów wpływające na maszynie wirtualnej przez kliknięcie prawym przyciskiem myszy określonego wiersza listy maszyn.

Aby przejść dalej, wybierz maszynę wirtualną, klikając go. Spowoduje to otwarcie strony szczegółów maszyny wirtualnej. Strony Przegląd, w obszarze szczegółów maszyny wirtualnej zawiera pulpit nawigacyjny znajdziesz dodatkowych informacji dotyczących komputera. 

Na stronie Przegląd replikacji maszyny można znaleźć:
- Cel punktu odzyskiwania (cel punktu odzyskiwania): bieżący cel punktu odzyskiwania dla maszyny wirtualnej i czas ostatniego obliczana cel punktu odzyskiwania.
- Najnowsze dostępne punkty odzyskiwania dla komputera
- Problemy konfiguracji, jeśli wszystkie, które mogą mieć wpływ na gotowość trybu failover maszyny. Kliknij łącze, aby uzyskać więcej szczegółów.
- Szczegóły błędu: Lista objawy błąd replikacji na maszynie wraz z możliwych przyczyn i zalecane korygowaniami
- Zdarzenia: Chronologicznej lista ostatnie zdarzenia wpływające na tym komputerze. Szczegóły błędu zawiera objawy obecnie zauważalne błąd na komputerze, zdarzenia jest rekord historycznych różnych zdarzeń, które mogą mieć wpływ na maszyny, w tym symptomy błędów, które wcześniej zostały zauważyć maszyny.
- Widok infrastruktury dla maszyn replikowanych do platformy Azure

![Przegląd szczegóły towarów replikowane usługi Site Recovery](media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-details.png)

Menu akcji w górnej części strony zawiera opcje, aby wykonywać różne operacje, takie jak przetestować tryb failover na maszynie wirtualnej. Przycisk Szczegóły błędu, w menu Akcja pozwala zobaczyć wszystkie błędy aktualnie aktywne w tym błędy replikacji, problemy z konfiguracją i ostrzeżenia najlepszych rozwiązań na podstawie konfiguracji maszyny wirtualnej.

> [!TIP]
> Czym różni się cel punktu odzyskiwania lub RPO z najnowszym dostępnym punktem odzyskiwania?
> 
>Usługa Azure Site Recovery używa wielu proces asynchroniczny krok do replikowania maszyn wirtualnych na platformie Azure. W kroku przedostatni replikacji ostatnie zmiany na maszynie wirtualnej oraz metadane są kopiowane do konta magazynu pamięci podręcznej/dziennika. Gdy te zmiany, oraz tag do identyfikowania możliwych do odzyskania punktu został zapisany do konta magazynu w regionie docelowej, usługi Azure Site Recovery ma informacje niezbędne do generowania możliwych do odzyskania punktu dla maszyny wirtualnej. W tym momencie zmian dotychczasowych przekazany do konta magazynu zostały spełnione cel punktu odzyskiwania. Innymi słowy, cel punktu odzyskiwania dla maszyny wirtualnej na tym etapie wyrażona w jednostkach czasu, jest równa ilość czasu, jaki upłynął od znacznik czasu odpowiadającą punktowi możliwych do odzyskania.
>
>Usługa Azure Site Recovery, działających w tle, odbiera przekazywane dane z konta magazynu i zastosuje je na dyski repliki utworzone dla maszyny wirtualnej. Następnie generuje punkt odzyskiwania i udostępnia tego punktu odzyskiwania w trybie failover. Najnowszego punktu odzyskiwania wskazuje sygnatura czasowa odpowiadający najnowszy punkt odzyskiwania, który został już przetwarzane i zastosowane do repliki dysków.
>> [!WARNING]
> Spowodowałoby zafałszowanie zegara lub czasu systemu są nieprawidłowe w replikacji maszyny źródłowej lub serwery infrastruktury lokalnej będzie pochylanie obliczona wartość celu punktu odzyskiwania. Aby upewnić się, dokładne raportowanie RPO wartości, upewnij się, że zegara systemowego na serwerach objętego replikacji są prawidłowe. 
>

## <a name="monitor-azure-site-recovery-jobsoperations"></a>Monitor usługi Azure Site Recovery zadania/Operations

Usługa Azure Site Recovery wykonuje operacje, które określisz asynchronicznie. Przykłady można wykonać operacji włączania replikacji, tworzenie planu odzyskiwania, testowanie trybu failover, zaktualizować ustawienia replikacji itp. Każdej z takich operacji ma odpowiedniego zadania, które służy do śledzenia i inspekcji operacji. Obiekt zadania zawiera wszystkich informacji wymaganych do można śledzić stan i postęp operacji. Ze strony zadania można śledzić stan różnych operacjami usługi Site Recovery dla magazynu. 

Aby wyświetlić listę zadań odzyskiwania lokacji magazynu Przejdź **monitorowanie i raporty** części menu magazynu i wybierz opcję zadania > zadania usługi Site Recovery. Wybierz zadanie na liście zadań na stronie, kliknij go, aby uzyskać więcej szczegółów na określone zadanie. Jeśli zadanie nie została ukończona pomyślnie, lub zawiera błędy, możesz wyświetlić więcej informacji na błędu i możliwych działań naprawczych, klikając przycisk Szczegóły błędu w górnej części strony szczegółów zadania (również dostępnym ze strony listy zadań, klikając prawym przyciskiem myszy na nie powiodło się zadanie). Użyj opcji filtrowania w menu Akcja w górnej części strony listy zadań, aby filtrować listę na podstawie określonych kryteriów i użyj przycisku eksportu, aby wyeksportować szczegóły wybranych zadań do programu excel. Można także przejść do widoku listy zadań za pomocą skrótu, które są dostępne na stronie pulpitu nawigacyjnego usługi Site Recovery. 

 Dla operacji, które można wykonywać w portalu Azure utworzone zadanie i jego bieżący stan również można śledzić w sekcji powiadomienia (ikonę dzwonka na górnym rogu) z portalu Azure.

## <a name="subscribe-to-email-notifications"></a>Subskrybowanie powiadomień e-mail

Funkcja powiadomienia e-mail w wbudowane umożliwia Subskrybuj, aby otrzymywać powiadomienia e-mail dla zdarzeń krytycznych. Jeśli subskrypcję, powiadomienia e-mail są wysyłane do następujących zdarzeń:
- Kondycję replikacji maszyny replikacji pogorszenia jakości związku na krytyczny.
- Brak łączności między składnikami infrastruktury lokalnej i usługa Azure Site Recovery. Wykryto łączności usługi z składników infrastruktury lokalnej, takich jak serwer konfiguracji (VMware) lub System Center Virtual Machine Manager(Hyper-V) zarejestrowany w magazynie usługi Site Recovery przy użyciu mechanizmu pulsu.
- Niepowodzeń operacji trybu failover, jeśli istnieje.

Aby subskrybować otrzymywać powiadomienia pocztą e-mail dla usługi Azure Site Recovery, przejdź **monitorowanie i raporty** części menu magazynu oraz:
1. Wybierz alerty i zdarzenia > zdarzeń odzyskiwania lokacji.
2. Wybierz "Powiadomienia E-mail" w menu u góry strony zdarzenia, który jest otwarty.
3. Aby włączyć lub wyłączyć powiadomienia e-mail i wybierz adresatów powiadomień, należy użyć Kreatora powiadomień e-mail. Może określić, czy wszyscy administratorzy subskrypcji można wysłać powiadomienia, i/lub Podaj listę adresów e-mail, aby wysyłać powiadomienia do. 
