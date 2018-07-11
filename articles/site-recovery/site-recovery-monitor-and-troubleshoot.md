---
title: Monitorowanie i rozwiązywanie problemów z usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: Monitorowanie i rozwiązywanie problemów dotyczących problemów z replikacją usługi Azure Site Recovery i operacje przy użyciu portalu
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
ms.date: 07/06/2018
ms.author: bsiva
ms.openlocfilehash: 84b5bf3be09083a69216802fc7f557de1a7f0ee6
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2018
ms.locfileid: "37917537"
---
# <a name="monitoring-and-troubleshooting-azure-site-recovery"></a>Monitorowanie i rozwiązywanie problemów z usługi Azure Site Recovery

W tym artykule dowiesz się, jak używać usługi Azure Site Recovery w wbudowanych funkcji monitorowania do monitorowania i rozwiązywania problemów. Instrukcje:
> [!div class="checklist"]
> - Pulpit nawigacyjny usługi Azure Site Recovery (strona magazynu — omówienie)
> - Monitorowanie i rozwiązywanie problemów z replikacją
> - Monitorowanie usługi Azure Site Recovery/operacje dotyczące zadań
> - Subskrybowanie powiadomień e-mail

## <a name="using-the-azure-site-recovery-dashboard"></a>Korzystanie z pulpitu nawigacyjnego usługi Azure Site Recovery

Pulpit nawigacyjny usługi Azure Site Recovery na stronie Przegląd magazynu konsoliduje wszystkie informacje monitorowania dla magazynu w obrębie jednej lokalizacji. Rozpocznij na pulpicie nawigacyjnym magazynu i Dowiedz się więcej, aby uzyskać więcej szczegółów, przechodząc do części pulpitu nawigacyjnego. Główne części pulpitu nawigacyjnego usługi Azure Site Recovery są następujące:

### <a name="1-switch-between-azure-backup-and-azure-site-recovery-dashboards"></a>1. Przełączać się między usługi Azure Backup i Azure Site Recovery pulpitów nawigacyjnych

Przełącznik w górnej części strony Przegląd pozwala przełączać się między stron pulpitu nawigacyjnego usługi Site Recovery i kopii zapasowej. Ten wybór dokonanych jest zapamiętanych i ustawiana domyślnie, aby przy następnym otwarciu strony Przegląd magazynu. Wybierz opcję Usługa Site Recovery, aby wyświetlić pulpit nawigacyjny Site Recovery. 

Różne części strony pulpitu nawigacyjnego usługi Azure Site Recovery automatycznie odświeżać co 10 minut, aby pulpit nawigacyjny odzwierciedla najnowszych informacji.

![Funkcje monitorowania na stronie Przegląd usługi Azure Site Recovery](media/site-recovery-monitor-and-troubleshoot/site-recovery-overview-page.png)

### <a name="2-replicated-items"></a>2. Zreplikowane elementy

Sekcja zreplikowanych elementów pulpitu nawigacyjnego przedstawia omówienie stanu replikacji chronionych serwerów w magazynie. 

<table>
<tr>
    <td>W dobrej kondycji</td>
    <td>Replikacja postępuje zwykle dla tych serwerów i żaden błąd lub ostrzeżenie objawy zostały wykryte.</td>
</tr>
<tr>
    <td>Ostrzeżenie </td>
    <td>Wykryto objawy ostrzeżenie, które mogą mieć wpływ na replikację lub wskazująca, że replikacja nie postępuje, zazwyczaj na tych serwerach.</td>
</tr>
<tr>
    <td>Krytyczne</td>
    <td>Co najmniej jeden objawy błąd replikacji krytycznej zostały wykryte na tych serwerach. Następujące objawy błędu są zazwyczaj wskaźniki replikacja albo jest zablokowany lub nie postępuje, tak szybko, jak je zmienić częstotliwość tych serwerów.</td>
</tr>
<tr>
    <td>Nie dotyczy</td>
    <td>Serwery, które nie są obecnie oczekuje się replikację, takich jak serwery, które zostały przełączone w tryb failover.</td>
</tr>
</table>

Aby wyświetlić listę serwerów chronionych filtrowane według stanu replikacji, kliknij opis kondycji replikacji obok pierścieniowej. Widok, który wszystkie łącza w tytułach sekcji to skrót umożliwiający magazynu można znaleźć na stronie zreplikowanych elementów. Użyj widoku, że wszystkie łącza, aby wyświetlić listę wszystkich serwerów w magazynie.

### <a name="3-failover-test-success"></a>3. Pomyślny test pracy awaryjnej

Sekcja Powodzenie testu pracy awaryjnej pulpitu nawigacyjnego przedstawia rozpad maszyn wirtualnych w magazynie, w oparciu o stan testu w tryb failover. 

<table>
<tr>
    <td>Test jest zalecany</td>
    <td>Maszyny wirtualne, których nie zainstalowano udanego testu trybu awaryjnego od czasu osiągnęła stanie chronionym.</td>
</tr>
<tr>
    <td>Wykonano pomyślnie</td>
    <td>Maszyny wirtualne, które były co najmniej jeden pomyślnego testowego przejścia w tryb failover.</td>
</tr>
<tr>
    <td>Nie dotyczy</td>
    <td>Maszyny wirtualne, które nie są aktualnie kwalifikuje się do testowania trybu failover. Przykłady: przełączone w tryb failover serwery, serwery, dla których początkowa replikacja jest w toku, serwery, dla których przejścia w tryb failover jest w toku, serwery, dla których test trybu failover jest już w toku.</td>
</tr>
</table>

Kliknij stan testu trybu failover, obok pierścieniowej, więc Przejrzyj listę rzeczy, chronionych serwerów na podstawie stanu testu trybu failover.
 
> [!IMPORTANT]
> Najlepszym rozwiązaniem jest zalecane jest wykonanie testu trybu failover na chronionych serwerach co najmniej raz na każde sześć miesięcy. Wykonywanie testu trybu failover bez szkodliwe umożliwia testowanie trybu failover serwerów i aplikacji do środowiska izolowanego i pomoże Ci ocenić swojej gotowości ciągłości działania firmy.

 Operacja test trybu failover na serwerze lub planu odzyskiwania jest uznawany za pomyślny, tylko wtedy, gdy operacja oczyszczania testu trybu failover i operacji testowania trybu failover została ukończona pomyślnie.

### <a name="4-configuration-issues"></a>4. Problemy z konfiguracją

W sekcji problemy konfiguracji to lista problemów, które mogą mieć wpływ na możliwość pomyślnie trybu failover maszyny wirtualne. Klasy problemy wymienione w tej sekcji są następujące:
 - **Brak konfiguracji:** chronionymi serwerami, brak wymaganych konfiguracji, takich jak sieć odzyskiwania lub odzyskiwania grupy zasobów.
 - **Brak zasobów:** skonfigurowane zasobów docelowych/odzyskiwania nie został znaleziony lub nie jest dostępna w ramach subskrypcji. Na przykład zasób został usunięty lub została zmigrowana do innej subskrypcji lub grupy zasobów. Następujące konfiguracje docelowego/odzyskiwania są monitorowane pod kątem dostępności: docelowa grupa zasobów, docelowy wirtualnej sieci i podsieci, dziennika/docelowego konta magazynu, docelowy zestaw dostępności, docelowy adres IP.
 - **Limit przydziału subskrypcji:** saldo limitu przydziału zasobów dostępnych subskrypcji jest porównywana saldo, trzeba mieć możliwość przełączenia wszystkich maszyn wirtualnych w magazynie. Jeśli dostępne saldo znajduje się niewystarczające, jest zgłaszany saldo za mały limit przydziału. Limity przydziału dla następujących zasobów platformy Azure są monitorowane: liczba rdzeni maszyn wirtualnych, liczba rdzeni rodziny maszyn wirtualnych, liczba kart (NIC) interfejsu sieciowego.
 - **Aktualizacje oprogramowania:** dostępność nowych aktualizacji oprogramowania, wygasa wersje oprogramowania.

Problemy z konfiguracją w (inne niż dostępności aktualizacji oprogramowania), są wykrywane przez operację okresowe modułu sprawdzania poprawności, która jest domyślnie uruchamiany co 12 godzin. Można wymusić operacji modułu sprawdzania poprawności, aby natychmiast uruchomić, klikając ikonę odświeżania obok pozycji *problemy z konfiguracją* nagłówek sekcji.

Kliknij łącza, aby uzyskać więcej informacji o liście problemach i maszyn wirtualnych, ich wpływ. Problemy wpływające na określone maszyny wirtualne, aby uzyskać więcej informacji można uzyskać, klikając **wymaga uwagi** łącze w kolumnie docelowej konfiguracji dla maszyny wirtualnej. Szczegółowe informacje obejmują zalecenia dotyczące sposobu może rozwiązać wykryte problemy.

### <a name="5-error-summary"></a>5. Souhrn chyb

Sekcja podsumowania błędu pokazuje symptomy błąd aktualnie aktywnej replikacji, które mogą mieć wpływ na replikację serwerów w magazynie, wraz z liczbą jednostek objęte wpływem ze względu na każdy błąd.

Objawy błąd replikacji dla serwerów w stan kondycji replikacji krytyczna lub poważna są widoczne w tabeli podsumowania błędu. 

- Błędy wpływające na ochronę lokalnych składników infrastruktury, takie jak bez otrzymania pulsu dostawcy usługi Azure Site Recovery uruchomionej na lokalny serwer konfiguracji, serwer programu VMM lub hosta funkcji Hyper-V są wymienione na początku podsumowanie błędów sekcja
- Objawy błąd replikacji wywierania wpływu na chronionych serwerach znajduje się obok. Wpisy tabeli podsumowania błędu są sortowane, zmniejszając kolejności ważność błędu, a następnie dzięki skróceniu kolejności liczby serwerów objęte wpływem.
 

> [!NOTE]
> 
>  Wiele objawy błąd replikacji można zaobserwować na jednym serwerze. W przypadku wielu objawy błędu na jednym serwerze każdy błąd objaw jest traktowany tego serwera na liście serwerów znajdujących się na których to dotyczy. Po rozwiązaniu podstawowego problemu skutkuje objawem może być błąd pozwala zwiększyć parametry replikacji i błąd zostanie usunięte z maszyny wirtualnej.
>
> > [!TIP]
> Liczba serwerów objęte wpływem jest to wygodny sposób, aby zrozumieć, jeśli jeden podstawowy problem może mieć wpływ na wielu serwerach. Na przykład błędem sieci potencjalnie może mieć wpływ na wszystkich serwerach replikacji z lokacji lokalnej do platformy Azure. Ten widok umożliwia szybkie przekazywanie tego ustalania, czy jeden podstawowy problem naprawi replikacji dla wielu serwerów.
>

### <a name="6-infrastructure-view"></a>6. Widok infrastruktury

Widok infrastruktury zawiera scenariusz poddanie wizualną reprezentację infrastrukturalnych elementów związanych z replikacją. Przedstawia on również wizualnie kondycję połączenia między różnymi serwerami i między serwerami i usługami platformy Azure zaangażowanych w replikacji. 

Zielona linia wskazuje, że połączenie jest w dobrej kondycji, podczas czerwona linia z ikoną nałożone błąd wskazuje na istnienie jednego lub więcej objawy błędu wpływ na łączność między składniki zaangażowane. Kursor myszy na ikonie błędu, w wierszu pokazuje błąd i liczby jednostek objęte wpływem. 

Klikając ikonę błędu pokazuje filtrowana lista jednostek objęte wpływem na błędy.

![Widok infrastruktury odzyskiwania lokacji (magazyn)](media/site-recovery-monitor-and-troubleshoot/site-recovery-vault-infra-view.png)

> [!TIP]
> Upewnij się, że składniki infrastruktury lokalnej (jako serwera konfiguracji dodatkowych serwerów przetwarzania, replikowanie maszyn wirtualnych VMware, hosty funkcji Hyper-V, serwery programu VMM) są uruchomione w najnowszej wersji oprogramowania usługi Azure Site Recovery. Aby można było korzystać ze wszystkich funkcji widoku infrastruktury, musisz działać [22 pakiet zbiorczy aktualizacji](https://support.microsoft.com/help/4072852) lub nowszy dla usługi Azure Site Recovery

Aby użyć widoku infrastruktury, wybierz odpowiedni scenariusz replikacji (maszyn wirtualnych platformy Azure, serwer fizyczny/maszyn wirtualnych VMware lub Hyper-V) w zależności od środowiska źródłowego. Widok infrastruktury przedstawione na stronie Przegląd magazynu jest zagregowany widok dla magazynu. Możesz przejść do dalsze do poszczególnych składników, klikając pola wyboru.

Widok infrastruktury ograniczone do kontekstu jednego maszynie replikującej jest dostępna na stronie przeglądu replikowanego elementu. Aby przejść do strony Przegląd replikacji serwera, przejdź do zreplikowane elementy w menu magazynu i wybierz serwer, aby wyświetlić szczegóły.

### <a name="infrastructure-view---faq"></a>Widok infrastruktury — często zadawane pytania

**PYTANIE** Dlaczego nie widzę widok infrastruktury w przypadku mojej maszyny Wirtualnej? </br>
**ODPOWIEDŹ** Funkcja widoku infrastruktury jest dostępna tylko dla maszyn wirtualnych, które jest replikowana na platformie Azure. Ta funkcja nie jest obecnie dostępna dla maszyn wirtualnych, które jest replikowana między lokacjami lokalnymi.

**PYTANIE** Dlaczego liczby maszyn wirtualnych w systemie innym niż łączna liczba widok infrastruktury magazynu jest wyświetlany w wykres pierścieniowy zreplikowane elementy?</br>
**ODPOWIEDŹ** Widok infrastruktury magazynu jest w zakresie scenariusze replikacji. Tylko maszyny wirtualne z uczestnictwa w tym scenariuszu aktualnie wybranego replikacji są uwzględniane w łącznej liczby maszyn wirtualnych w widoku infrastruktury. Ponadto wybrany scenariusz tylko maszyny wirtualne, które są aktualnie skonfigurowane do replikacji na platformę Azure uwzględniono w łącznej liczby maszyn wirtualnych w widoku infrastruktury (przykład Fo: kopii maszyn wirtualnych replikowanych w tryb failover maszyn wirtualnych do lokacji w środowisku lokalnym nie są uwzględnione w widok infrastruktury.)

**PYTANIE** Dlaczego jest liczba zreplikowane elementy wyświetlane w szufladzie essentials na stronie Przegląd różni się od łącznej liczby zreplikowane elementy wyświetlane na wykresie pierścieniowym na pulpicie nawigacyjnym?</br>
**ODPOWIEDŹ** Tylko maszyny wirtualne, dla których Replikacja początkowa została zakończona są objęte liczby wyświetlane w szufladzie essentials. Całkowita pierścieniowy zreplikowane elementy nie obejmuje wszystkich maszyn wirtualnych w magazynie, w tym serwery dla których początkowa replikacja jest w toku.

**PYTANIE** Scenariusze replikacji, która jest dostępna dla widok infrastruktury? </br>
**ODPOWIEDŹ**
>[!div class="mx-tdBreakAll"]
>|Scenariusz replikacji  | Stan maszyny Wirtualnej  | Widok infrastruktury dostępne  |
>|---------|---------|---------|
>|Maszyny wirtualne replikowane między dwoma lokacjami lokalnymi     | -        | Nie      |
>|Wszyscy     | W trybie Failover         |  Nie       |
>|Maszyny wirtualne replikację między dwoma regionami platformy Azure     | Replikacja początkowa w toku lub chronione         | Yes         |
>|Maszyny wirtualne VMware, replikowanych do platformy Azure     | Replikacja początkowa w toku lub chronione        | Yes        |
>|Maszyny wirtualne VMware, replikowanych do platformy Azure     | Przełączone w tryb failover maszyn wirtualnych, które są replikowane z powrotem do lokacji programu VMware w środowisku lokalnym         | Nie        |
>|Maszyny wirtualne funkcji Hyper-V replikowanych do platformy Azure     | Replikacja początkowa w toku lub chronione        | Yes       |
>|Maszyny wirtualne funkcji Hyper-V replikowanych do platformy Azure     | W trybie Failover / powrotu po awarii w toku        |  Nie       |


### <a name="7-recovery-plans"></a>7. Plany odzyskiwania

W sekcji planów odzyskiwania pokazuje liczbę planów odzyskiwania w magazynie. Kliknij liczbę, aby zapoznać się z listą planów odzyskiwania, Utwórz nowe plany odzyskiwania lub edytować istniejące zadania. 

### <a name="8-jobs"></a>8. Zadania

Zadania usługi Azure Site Recovery śledzić stan operacji usługi Azure Site Recovery. Większość operacji w usłudze Azure Site Recovery jest wykonywana asynchronicznie, przy użyciu zadania śledzenia, używane do śledzenia postępu dla operacji.  Aby dowiedzieć się, jak monitorować stan operacji, zobacz [monitora usługi Azure Recovery zadań/operacji lokacji](#monitor-azure-site-recovery-jobsoperations) sekcji.

Ta sekcja zadań pulpitu nawigacyjnego zawiera następujące informacje:

<table>
<tr>
    <td>Niepowodzenie</td>
    <td>Niepowodzenie zadania usługi Azure Site Recovery w ostatnich 24 godzinach</td>
</tr>
<tr>
    <td>W toku</td>
    <td>Usługa Azure zadania usługi Site Recovery, które są obecnie w toku</td>
</tr>
<tr>
    <td>Oczekiwanie na dane wejściowe</td>
    <td>Zadania usługi Azure Site Recovery, które są obecnie wstrzymana, oczekiwanie na dane wejściowe od użytkownika.</td>
</tr>
</table>

Wyświetl wszystko łącze obok nagłówka sekcji to skrót umożliwiający przejdź na stronę listy zadań.

## <a name="monitor-and-troubleshoot-replication-issues"></a>Monitorowanie i rozwiązywanie problemów z replikacją

Oprócz tych informacji, na stronie pulpitu nawigacyjnego magazynu możesz uzyskać dodatkowe szczegóły i informacje dotyczące rozwiązywania problemów na stronie listy maszyn wirtualnych i na stronie szczegółów maszyny wirtualnej. Można wyświetlić listę chronionych maszyn wirtualnych w magazynie, wybierając **zreplikowane elementy** opcji w menu magazynu. Można też pobrać do filtrowana lista elementów chronionych, klikając znajdujących się w zakresie skróty, dostępne na stronie pulpitu nawigacyjnego magazynu.

![Usługa Site Recovery zreplikowane elementy widoku listy](media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-list-view.png)

Opcja filtra na stronie zreplikowanych elementów listy umożliwia zastosowanie różnych filtrów, takich jak kondycja replikacji i zasady replikacji. 

Selektor kolumn, opcja pozwala określić dodatkowe kolumny do wyświetlenia, takich jak cel punktu odzyskiwania, problemy z konfiguracją docelowego i błędy replikacji. Możesz zainicjować operacji na maszynie wirtualnej lub Wyświetl błędy wpływające na ochronę maszyny wirtualnej, klikając prawym przyciskiem myszy w określonym wierszu listy maszyn.

Aby przejść dalej, wybierz maszynę wirtualną, klikając ją. Spowoduje to otwarcie strony szczegółów maszyny wirtualnej. Strona przeglądu, w obszarze Szczegóły maszyny wirtualnej zawiera pulpit nawigacyjny, gdzie znajdują się dodatkowe informacje odnoszące się do maszyny. 

Na stronie Przegląd na maszynie replikującej znajdziesz:
- Cel punktu odzyskiwania (cel punktu odzyskiwania): bieżący cel punktu odzyskiwania dla maszyny wirtualnej i czas ostatniego obliczane cel punktu odzyskiwania.
- Najnowsze dostępne punkty odzyskiwania dla maszyny
- Problemy z konfiguracją, jeśli wszystkie, które mogą mieć wpływ na gotowość trybu failover maszyny. Kliknij link, aby uzyskać więcej szczegółów.
- Szczegóły błędu: Lista objawy błąd replikacji na maszynie, wraz z możliwych przyczyn i zalecane korygowania funkcję
- Zdarzenia: Chronologiczną listę ostatnie zdarzenia wpływające na ochronę na maszynie. Szczegóły błędu znajdują się na maszynie objawy błędu obecnie zauważalne, zdarzenia jest historycznym różnych zdarzeń, które mogą mieć wpływ na maszyny, w tym objawy błędu, które wcześniej zostały umknęło Twojej maszyny.
- Widok infrastruktury w przypadku maszyn replikowanych do platformy Azure

![Usługa Site Recovery zreplikowany element szczegóły/Przegląd](media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-details.png)

W menu akcji w górnej części strony zawiera opcje, aby wykonywać różne operacje, takie jak testowanie trybu failover na maszynie wirtualnej. Przycisk szczegółów błędu, w menu Akcja pozwala zobaczyć wszystkie błędy aktualnie aktywne, w tym błędy replikacji, problemy z konfiguracją i ostrzeżenia najlepsze rozwiązania na podstawie konfiguracji dla maszyny wirtualnej.

> [!TIP]
> Jak cel punktu odzyskiwania lub cel punktu odzyskiwania jest inny niż najnowszy dostępny punkt przywracania
> 
>Usługa Azure Site Recovery używa wielu procesów asynchronicznego krok do replikowania maszyn wirtualnych na platformie Azure. W kroku przedostatni replikacji ostatnie zmiany na maszynie wirtualnej wraz z metadanymi są kopiowane do dziennika/konto magazynu pamięci podręcznej. Gdy te zmiany, oraz tag do identyfikowania możliwych do odzyskania punktu została zapisana do konta magazynu w regionie docelowym, usługi Azure Site Recovery zawiera informacje niezbędne do wygenerowania możliwe do odzyskania punktu dla maszyny wirtualnej. W tym momencie zmiany tej pory przekazane do konta magazynu zostały spełnione cel punktu odzyskiwania. Innymi słowy, cel punktu odzyskiwania dla maszyny wirtualnej na tym etapie wyrażona w jednostkach czasu, jest równy czas, jaki upłynął od sygnatura czasowa odpowiadająca punktowi możliwe do odzyskania.
>
>Usługa Azure Site Recovery, działający w tle, wybierze przekazywane dane z konta magazynu i zastosuje je na dyskach repliki utworzony dla maszyny wirtualnej. Następnie generuje punkt odzyskiwania i udostępnienie tego punktu odzyskiwania w trybie failover. Najnowszy dostępny punkt przywracania wskazuje sygnatura czasowa odpowiadający najnowszy punkt odzyskiwania, które już zostały przetworzone i zastosowane do dysków repliki.
>> [!WARNING]
> Niesymetryczne zegara lub czasu systemu są nieprawidłowe w replikującej maszynie źródłowej lub serwerach infrastruktury lokalnej będzie pochylanie obliczona wartość celu punktu odzyskiwania. Aby zapewnić dokładne raportowanie cel punktu odzyskiwania wartości upewnij się, że zegar systemowy na serwerach, które są zaangażowane w replikacji dokładne. 
>

## <a name="monitor-azure-site-recovery-jobsoperations"></a>Monitorowanie usługi Azure Site Recovery/operacje dotyczące zadań

Usługa Azure Site Recovery wykonuje operacje, które określisz asynchronicznie. Przykłady można wykonać operacji włączania replikacji, tworzenie planu odzyskiwania, przetestować tryb failover, zaktualizować ustawienia replikacji itp. Każda z tych operacja ma odpowiednie zadanie, które służy do śledzenia i inspekcji operacji. Obiekt zadania ma wszystkie niezbędne informacje wymagane do śledzenia stanu i postępu operacji. Ze strony zadania można śledzić stan różnych operacjami usługi Site Recovery dla magazynu. 

Aby wyświetlić listę zadania usługi Site Recovery dla magazynu, przejdź **monitorowanie i raporty** części menu magazynu i wybierz zadania > zadania usługi Site Recovery. Wybierz zadanie z listy zadań na tej stronie, klikając ją, aby uzyskać więcej szczegółów na temat określonego zadania. Jeśli zadanie nie zostało ukończone pomyślnie, lub zawiera błędy, po skorygowaniu błędu i możliwych widać więcej informacji, klikając przycisk szczegółów błędu w górnej części strony szczegółów zadania (również dostępnego ze strony listy zadań, klikając prawym przyciskiem myszy na powiodło się zadanie). Użyj opcji filtru w menu akcji w górnej części strony listy zadań, aby filtrować listę na podstawie konkretnych kryteriów i użyj przycisku eksportu, aby wyeksportować szczegóły wybranych zadań do programu excel. Widok listy zadań można również uzyskać dostęp za pomocą skrótu, które są dostępne na stronie pulpitu nawigacyjnego Site Recovery. 

 W przypadku operacji wykonywanych w witrynie Azure portal utworzonego zadania oraz ich bieżący stan również można śledzić w sekcji powiadomienia (ikonę dzwonka w prawym górnym rogu) w witrynie Azure Portal.

## <a name="subscribe-to-email-notifications"></a>Subskrybowanie powiadomień e-mail

Funkcja powiadomienia e-mail wbudowane umożliwia subskrybować otrzymywanie powiadomień e-mail dla zdarzeń krytycznych. Jeśli subskrypcję, powiadomienia e-mail są wysyłane do następujących zdarzeń:
- Kondycja replikacji maszynie replikującej pogarszania jakości na krytyczny.
- Brak łączności między składnikami infrastruktury w środowisku lokalnym i usługą Azure Site Recovery. Wykryto łączności usługą Site Recovery w ramach usługi lokalnych składników infrastruktury, takich jak serwer konfiguracji (VMware) lub System Center Virtual Machine Manager(Hyper-V) zarejestrowany w magazynie przy użyciu mechanizmu pulsu.
- Niepowodzeń operacji trybu failover, jeśli istnieje.

Aby subskrybować otrzymywać powiadomienia e-mail dla usługi Azure Site Recovery, przejdź **monitorowanie i raporty** części menu magazynu oraz:
1. Wybierz alerty i zdarzenia > Site Recovery zdarzenia.
2. Wybierz pozycję "Powiadomienia E-mail" w menu u góry strony zdarzeń, który jest otwierany.
3. Użyj Kreatora powiadomień e-mail, aby włączyć lub wyłączyć powiadomienia e-mail i wybierz adresatów powiadomienia. Użytkownik może określić, że wszyscy administratorzy subskrypcji będą wysyłane powiadomienia, i/lub Podaj listę adresów e-mail do wysyłania powiadomień. 
