---
title: Ciągłość działalności biznesowej w chmurze — odzyskiwanie bazy danych — usługa SQL Database | Microsoft Docs
description: Dowiedz się, w jaki sposób usługa Azure SQL Database obsługuje ciągłość działalności biznesowej w chmurze i odzyskiwanie bazy danych oraz pomaga zapewnić działanie aplikacji o kluczowym znaczeniu w chmurze.
keywords: business continuity,cloud business continuity,database disaster recovery,database recovery
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: conceptual
ms.workload: On Demand
ms.date: 04/04/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: 9149405e2778557a94815812fdf4966d38a3149c
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2018
ms.locfileid: "36308459"
---
# <a name="overview-of-business-continuity-with-azure-sql-database"></a>Omówienie zagadnień dotyczących ciągłości działalności biznesowej zapewnianej przez usługę Azure SQL Database

W tym omówieniu opisano możliwości usługi Azure SQL Database w zakresie zapewniania ciągłości działalności biznesowej i odzyskiwania po awarii. Więcej informacji na temat opcji, zalecenia i samouczki dotyczące odzyskiwania z destrukcyjne zdarzenia, które mogą spowodować utratę danych lub spowodować bazy danych i aplikacji staną się niedostępne. Dowiedz się, co robić, gdy błąd użytkownika albo aplikacji wpływa na integralność danych, region platformy Azure ma awarii lub aplikacja wymaga konserwacji.

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>Funkcje usługi SQL Database, których można użyć, aby zapewnić ciągłość działalności biznesowej

Usługa SQL Database udostępnia kilka funkcji zapewniających ciągłość działalności biznesowej, w tym zautomatyzowane kopie zapasowe oraz opcjonalną replikację bazy danych. Każda z tych funkcji ma różne charakterystyki dotyczące szacowanego czasu odzyskiwania (ERT, estimated recovery time) i potencjalnej utraty danych dotyczących ostatnich transakcji. Po zapoznaniu się z tymi opcjami można dokonać między nimi wyboru, a także — w większości przypadków — użyć ich razem w ramach różnych scenariuszy. Podczas opracowywania planu zapewniania ciągłości działalności biznesowej należy zrozumieć znaczenie maksymalnego dopuszczalnego czasu oczekiwania na pełne odzyskanie aplikacji po wystąpieniu zdarzenia powodującego zakłócenia — jest to cel czasu odzyskiwania (RTO, recovery time objective). Należy także zrozumieć maksymalna ilość danych ostatnie aktualizacje (interwał czasu) aplikacja może tolerować utraty podczas odzyskiwania po zdarzeniu destrukcyjne — jest to cel punktu odzyskiwania (RPO).

W poniższej tabeli porównano Wstaw i cel punktu odzyskiwania dla każdej warstwy usługi dla trzech najbardziej typowych scenariuszy.

| Możliwości | Podstawowa | Standardowa (Standard) | Premium  | Ogólne zastosowanie | Krytyczne dla działania firmy
| --- | --- | --- | --- |--- |--- |
| Przywracanie do punktu w czasie z kopii zapasowej |Dowolny punkt przywracania w ciągu ostatnich 7 dni |Dowolny punkt przywracania w ciągu ostatnich 35 dni |Dowolny punkt przywracania w ciągu ostatnich 35 dni |Dowolny punkt przywracania w skonfigurowanym okresie (do 35 dni)|Dowolny punkt przywracania w skonfigurowanym okresie (do 35 dni)|
| Geograficznie — Przywracanie z kopii zapasowych z replikacją geograficzną |ERT < 12 godz., RPO < 1 godz. |ERT < 12 godz., RPO < 1 godz. |ERT < 12 godz., RPO < 1 godz. |ERT < 12 godz., RPO < 1 godz.|ERT < 12 godz., RPO < 1 godz.|
| Przywracanie z magazynu usługi Azure Backup |ERT < 12 godz., RPO < 1 tydz. |ERT < 12 godz., RPO < 1 tydz. |ERT < 12 godz., RPO < 1 tydz. |ERT < 12 godz., RPO < 1 tydz.|ERT < 12 godz., RPO < 1 tydz.|
| Aktywna replikacja geograficzna |ERT < 30 s, RPO < 5 s |ERT < 30 s, RPO < 5 s |ERT < 30 s, RPO < 5 s |ERT < 30 s, RPO < 5 s|ERT < 30 s, RPO < 5 s|

### <a name="use-point-in-time-restore-to-recover-a-database"></a>Użyj w momencie przywracania, aby odzyskać bazę danych

Baza danych SQL automatycznie wykonuje kombinacji pełnej bazy danych kopii zapasowych co tydzień, co godzinę kopii zapasowych różnicowej bazy danych i transakcji kopii zapasowych dziennika co pięć - dziesięć minut do ochrony przed utratą danych firmy. Jeśli używasz [model kupna na podstawie jednostek dtu w warstwie](sql-database-service-tiers-dtu.md), a następnie te kopie zapasowe są przechowywane w magazynie RA-GRS 35 dni w przypadku baz danych w warstwach usług standardowa i Premium i 7 dni w przypadku baz danych w warstwie podstawowej usługi. Jeśli okres przechowywania w warstwie usług nie spełnia Twoich wymagań biznesowych, można go zwiększyć, [zmieniając warstwę usług](sql-database-single-database-scale.md). Jeśli używasz [na podstawie vCore model kupna (wersja zapoznawcza)](sql-database-service-tiers-vcore.md), przechowywania kopii zapasowych jest konfigurowane w górę celu 35 dni w ogólnego przeznaczenia i krytyczne warstwy biznesowej. Pełne oraz różnicowe kopie zapasowe bazy danych są także replikowane do [sparowanego centrum danych](../best-practices-availability-paired-regions.md) w celu ochrony przed awarią centrum danych. Aby uzyskać więcej informacji, zobacz [kopie zapasowe bazy danych automatyczne](sql-database-automated-backups.md).

Maksymalny okres przechowywania PITR obsługiwanych jest niewystarczająca dla aplikacji, można go rozszerzyć Konfigurując zasady długoterminowego przechowywania (od lewej do prawej) dla baz danych. Aby uzyskać więcej informacji, zobacz [Długoterminowe przechowywanie](sql-database-long-term-retention.md).

Tych automatycznych kopii zapasowych bazy danych można użyć, aby odzyskać bazę danych po wystąpieniu różnych zdarzeń powodujących zakłócenia, zarówno w obrębie centrum danych, jak również do innego centrum danych. W przypadku korzystania z automatycznych kopii zapasowych bazy danych szacowany czas odzyskiwania zależy od kilku czynników, w tym łącznej liczby jednocześnie odzyskiwanych baz danych w tym samym regionie, rozmiaru bazy danych, rozmiaru dziennika transakcji oraz przepustowości sieci. Czas odzyskiwania jest zazwyczaj mniej niż 12 godzin. Może trwać dłużej odzyskać bazę danych bardzo duże lub aktywny. Aby uzyskać więcej informacji na temat czasu odzyskiwania, zobacz [bazy danych czasu odzyskiwania](sql-database-recovery-using-backups.md#recovery-time). Podczas odzyskiwania do innego obszaru danych potencjalna utrata danych jest ograniczona do 1 godziny przez magazyn geograficznie nadmiarowy w ramach tworzonych co godzinę różnicowych kopii zapasowych bazy danych.

> [!IMPORTANT]
> Aby przeprowadzić odzyskiwanie za pomocą automatycznych kopii zapasowych, użytkownik musi być członkiem roli Współautor programu SQL Server lub właścicielem subskrypcji — zobacz [RBAC: Built-in roles](../role-based-access-control/built-in-roles.md) (RBAC: role wbudowane). Odzyskiwanie można przeprowadzić za pomocą witryny Azure Portal, programu PowerShell lub interfejsu API REST. Nie można używać języka Transact-SQL.
>

Zautomatyzowanych kopii zapasowych jako mechanizmu odzyskiwania i zapewniania ciągłości działalności biznesowej należy używać, jeśli aplikacja:

* Nie jest traktowana jako aplikacja o kluczowym znaczeniu.
* Nie ma powiązania umowy SLA - przestoju 24 godziny lub już nie powoduje odpowiedzialności finansowych.
* Ma niską częstotliwość zmian danych (mała liczba transakcji na godzinę) i utrata zmian z maksymalnie jednej godziny jest akceptowalna.
* Jej koszt ma znaczenie.

Szybsze odzyskiwania, należy użyć [aktywna replikacja geograficzna](sql-database-geo-replication-overview.md) (omówione dalej). Jeśli chcesz mieć możliwość odzyskania danych z sprzed 35 dni okresu, użyj [przechowywania długoterminowego](sql-database-long-term-retention.md). 

### <a name="use-active-geo-replication-and-auto-failover-groups-in-preview-to-reduce-recovery-time-and-limit-data-loss-associated-with-a-recovery"></a>Użyj active grup — replikacja geograficzna i pracy awaryjnej automatycznego (w podglądzie) skrócić czas odzyskiwania i ograniczyć utraty danych odzyskiwania

Oprócz sytuacji zakłóceń firm przy użyciu kopii zapasowych bazy danych odzyskiwania bazy danych, możesz użyć [aktywna replikacja geograficzna](sql-database-geo-replication-overview.md) skonfigurować mieć maksymalnie cztery czytelny dodatkowej bazy danych w regionach wybraną bazę danych. Te pomocnicze bazy danych są stale synchronizowane z podstawową bazą danych przy użyciu mechanizmu replikacji asynchronicznej. Ta funkcja służy do ochrony przed przerw w działaniu biznesowe w przypadku awarii centrum danych lub podczas uaktualniania aplikacji. Aktywna replikacja geograficzna może również zapewnić lepszą wydajność zapytań dla zapytania tylko do odczytu użytkownikom geograficznie.

Aby włączyć zautomatyzowane i przezroczysty tryb failover należy zorganizować baz danych z replikacją geograficzną do grupy przy użyciu [grupy pracy awaryjnej automatycznie](sql-database-geo-replication-overview.md) funkcji bazy danych SQL (w — wersja zapoznawcza).

Jeśli podstawowa baza danych przejdzie do trybu offline nieoczekiwanie lub należy przełączyć go w tryb offline dla działań konserwacji, można szybko promowania pomocniczego serwera podstawowego (nazywanych również trybu failover) i konfigurowanie aplikacji do nawiązania połączenia awansowana podstawowej. Jeśli aplikacja nawiązuje połączenie z bazami danych za pomocą trybu failover odbiornika grupy, nie trzeba zmienić konfigurację ciągu połączenia SQL po pracy awaryjnej. Planowane przejście w tryb failover nie spowoduje utraty żadnych danych. W przypadku nieplanowanego przejścia w tryb failover może wystąpić utrata niewielkiej ilości danych dotyczących tylko najnowszych transakcji ze względu na charakter replikacji asynchronicznej. Przy użyciu grup pracy awaryjnej automatycznego (w — wersja zapoznawcza), można dostosować zasady trybu failover, aby zminimalizować ryzyko utraty danych. Po przejściu w tryb failover możliwy jest późniejszy powrót po awarii — albo zgodnie z planem, albo gdy centrum danych powróci do trybu online. We wszystkich przypadkach użytkownicy doświadczą krótkiego przestoju i będą musieli ponownie nawiązać połączenie.

> [!IMPORTANT]
> Aby użyć aktywna replikacja geograficzna i pracy awaryjnej automatycznie grupy (w — wersja zapoznawcza), należy być właścicielem subskrypcji lub uprawnienia administracyjne w programie SQL Server. Można skonfigurować i pracy awaryjnej przy użyciu platformy Azure portalu, programu PowerShell lub interfejsu API REST, korzystając z uprawnień subskrypcji platformy Azure lub przy użyciu języka Transact-SQL z uprawnieniami serwera SQL.
> 

Użyj active grup — replikacja geograficzna i pracy awaryjnej automatycznego (w wersji zapoznawczej), jeśli aplikacja spełnia dowolną z tych kryteriów:

* Ma kluczowe znaczenie.
* Obejmuje ją umowa dotycząca poziomu usług (SLA), zgodnie z którą przestój nie może być dłuższy niż 24 godziny.
* Może spowodować przestój finansowych odpowiedzialności.
* Ma wysoki współczynnik zmian danych i utrata zmian z okresu jednej godziny jest niedopuszczalna.
* Dodatkowy koszt związany z aktywną replikacją geograficzną jest niższy niż potencjalna odpowiedzialność finansowa i powiązane straty biznesowe.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-protecting-important-DBs-from-regional-disasters-is-easy/player]
>

## <a name="recover-a-database-after-a-user-or-application-error"></a>Odzyskiwanie bazy danych po błędzie użytkownika lub aplikacji

Nie ma doskonałe! Użytkownik może przypadkowo usunąć pewne dane, nieodwracalnie usunąć ważną tabelę lub nawet usunąć całą bazę danych. Możliwe jest również przypadkowe zastąpienie przez aplikację poprawnych danych błędnymi danymi w wyniku wady aplikacji.

W tym scenariuszu opcje odzyskiwania są następujące.

### <a name="perform-a-point-in-time-restore"></a>Wykonanie przywracania do punktu w czasie
Możliwe jest użycie zautomatyzowanych kopii zapasowych, aby odzyskać kopię bazy danych do znanego prawidłowego punktu w czasie, pod warunkiem, że ten punkt w czasie przypada na okres przechowywania bazy danych. Po przywróceniu bazy danych można albo zastąpić oryginalną bazę danych przywróconą bazą danych, albo skopiować potrzebne dane z przywróconych danych do oryginalnej bazy danych. Jeśli baza danych używa aktywna replikacja geograficzna, zaleca się kopiowanie wymaganych danych z przywróconej kopii do oryginalnej bazy danych. Można zastąpić oryginalnej bazy danych przywróconej bazy danych, musisz skonfigurować ponownie i przeprowadź ponowną synchronizację aktywna replikacja geograficzna (co może potrwać dość duże bazy danych). Przywrócenie bazy danych do ostatniego dostępnego punktu w czasie, przywracanie dodatkowej geograficzna do dowolnego punktu w czasie nie jest obecnie obsługiwane.

Aby uzyskać więcej informacji i poznać szczegółowe kroki przywracania bazy danych do punktu w czasie za pomocą witryny Azure Portal lub programu PowerShell, zobacz informacje dotyczące [przywracania do punktu w czasie](sql-database-recovery-using-backups.md#point-in-time-restore). Nie można przeprowadzić odzyskiwania za pomocą języka Transact-SQL.

### <a name="restore-a-deleted-database"></a>Przywracanie usuniętej bazy danych
Jeśli baza danych została usunięta, ale serwer logiczny nie został usunięty, można przywrócić usuniętą bazę danych do punktu, w którym została ona usunięta. Spowoduje to przywrócenie kopii zapasowej bazy danych na ten sam logiczny serwer SQL, z którego została ona usunięta. Bazę danych można przywrócić przy użyciu oryginalnej nazwy; można też nadać jej nową nazwę.

Aby uzyskać więcej informacji i poznać szczegółowe kroki przywracania usuniętej bazy danych za pomocą witryny Azure Portal lub programu PowerShell, zobacz informacje dotyczące [przywracania usuniętej bazy danych](sql-database-recovery-using-backups.md#deleted-database-restore). Nie można przeprowadzić przywracania za pomocą języka Transact-SQL.

> [!IMPORTANT]
> Jeśli usunięto serwer logiczny, nie można odzyskać usuniętej bazy danych.


### <a name="restore-backups-from-long-term-retention"></a>Przywracanie kopii zapasowych z długoterminowego przechowywania

Jeśli baza danych jest skonfigurowany w celu przechowywania długoterminowego wystąpiła utrata danych poza okresem przechowywania bieżącego automatycznego tworzenia kopii zapasowych, można przywrócić z pełnej kopii zapasowej w magazynie od lewej do prawej, do nowej bazy danych. W takim momencie można albo zastąpić oryginalną bazę danych przywróconą bazą danych, albo skopiować potrzebne dane z przywróconej bazy danych do oryginalnej bazy danych. Jeśli chcesz pobrać starą wersję bazy danych przed uaktualnieniem najważniejszych aplikacji, spełnia żądania z audytorów lub prawnym, można utworzyć bazy danych przy użyciu pełnej kopii zapasowej zapisać w magazynie kopii zapasowej Azure.  Aby uzyskać więcej informacji, zobacz [Długoterminowe przechowywanie](sql-database-long-term-retention.md).

## <a name="recover-a-database-to-another-region-from-an-azure-regional-data-center-outage"></a>Odzyskiwanie bazy danych do innego regionu podczas awarii regionalnego centrum danych platformy Azure
<!-- Explain this scenario -->

Sporadycznie centrum danych platformy Azure może mieć awarię. Taka awaria powoduje zakłócenia działania firmy, które mogą trwać tylko kilka minut, ale mogą też trwać wiele godzin.

* Jedną z opcji jest oczekiwanie, aż baza danych powróci do trybu online po zakończeniu awarii centrum danych. Takie rozwiązanie sprawdza się dla aplikacji, w przypadku których baza danych może być w trybie offline. Może to na przykład dotyczyć projektu tworzenia oprogramowania lub bezpłatnej wersji próbnej, nad którymi nie trzeba pracować na bieżąco. Centrum danych po awarii nie wiesz, jak długo może ostatniej awarii, więc ta opcja działa tylko, jeśli nie potrzebujesz bazy danych przez pewien czas.
* Innym rozwiązaniem jest albo błąd za pośrednictwem innego regionu danych Jeśli używasz aktywna replikacja geograficzna lub Przywróć bazę danych za pomocą kopii zapasowej bazy danych z magazynu geograficznie nadmiarowego (geograficznie przywracania). Tryb failover trwa tylko kilka sekund podczas odzyskiwania bazy danych z kopii zapasowych zajmuje godzin.

Po wykonaniu akcji, jak długo trwa odzyskanie i utraty danych, ile ponosisz zależy od tego, jak zdecydujesz się używać tych funkcjach ciągłości biznesowej w aplikacji. W rzeczywistości można użyć kombinacji kopie zapasowe bazy danych i aktywna replikacja geograficzna w zależności od wymagań aplikacji. Dyskusję na temat zagadnień dotyczących projektowania aplikacji na potrzeby autonomicznych baz danych i pul elastycznych za pomocą tych funkcji zapewniania ciągłości działalności biznesowej zawierają tematy [Design an application for cloud disaster recovery](sql-database-designing-cloud-solutions-for-disaster-recovery.md) (Projektowanie aplikacji pod kątem odzyskiwania po awarii w chmurze) i [Elastic Pool disaster recovery strategies](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md) (Strategie odzyskiwania po awarii dotyczące pul elastycznych).

Poniższe sekcje zawierają omówienie kroków, aby odzyskać za pomocą kopii zapasowych bazy danych lub aktywna replikacja geograficzna. Aby uzyskać szczegółowy opis kroków, takich jak wymagania, kroki odzyskiwania po i informacje o sposobie symulacji awarii wykonać drążenie odzyskiwania po awarii, zobacz [odzyskanie bazy danych SQL awarii](sql-database-disaster-recovery.md).

### <a name="prepare-for-an-outage"></a>Przygotowanie do awarii
Niezależnie od używanej funkcji zapewniania ciągłości działalności biznesowej należy:

* Zidentyfikować i przygotować serwer docelowy, w tym reguły zapory na poziomie serwera, dane logowania i uprawnienia na poziomie głównej bazy danych.
* Określić sposób przekierowania klientów i aplikacji klienckich na nowy serwer.
* Udokumentować inne zależności, takie jak ustawienia inspekcji i alerty

Jeżeli nie poprawnie, Przywracanie aplikacji online po przejściu w tryb failover i odzyskiwanie bazy danych zajmuje dodatkowy czas i może również wymagać rozwiązywania problemów w czasie akcent — Nieprawidłowa kombinacja.

### <a name="fail-over-to-a-geo-replicated-secondary-database"></a>Awaryjnie pomocniczej bazy danych replikacją geograficzną
Jeśli używasz aktywna replikacja geograficzna i pracy awaryjnej automatycznie grupy (w podglądzie) jako mechanizm Twojej odzyskiwania można skonfigurować zasady automatycznej pracy awaryjnej lub użyć [ręcznego przełączania trybu failover](sql-database-disaster-recovery.md#fail-over-to-geo-replicated-secondary-server-in-the-failover-group). Po zainicjowaniu tryb failover powoduje, że pomocniczy nową podstawową i gotowe do rejestrowania nowych transakcji i odpowiadały na zapytania — przy minimalnej utracie danych nie zostały przekazane. Aby uzyskać informacje dotyczące projektowania proces trybu failover, zobacz [projektowania aplikacji do chmury odzyskiwania po awarii](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

> [!NOTE]
> Gdy centrum danych powróci do trybu online starego kolory podstawowe automatycznie ponownie połączyć się z nową podstawową i stają się pomocniczej bazy danych. Konieczne jest przeniesienie głównej wstecz do oryginalnego regionu, możesz ręcznie zainicjować planowanego trybu failover (powrót po awarii). 
> 

### <a name="perform-a-geo-restore"></a>Wykonaj operację przywracania geo
Jeśli używasz automatyczne kopie zapasowe z magazynu geograficznie nadmiarowego replikacji z mechanizmu odzyskiwania [zainicjować odzyskiwanie bazy danych, używając funkcji przywracania geograficznie](sql-database-disaster-recovery.md#recover-using-geo-restore). Odzyskiwanie ma zwykle miejsce w ciągu 12 godzin, z utratą danych maksymalnie z jednej godziny, w zależności od tego, kiedy została wykonana i zreplikowana ostatnia tworzona co godzinę różnicowa kopia zapasowa. Do momentu ukończenia odzyskiwania baza danych nie może rejestrować żadnych transakcji ani odpowiadać na żadne zapytania. Przywrócenie bazy danych do ostatniego dostępnego punktu w czasie, przywracanie dodatkowej geograficzna do dowolnego punktu w czasie nie jest obecnie obsługiwane.

> [!NOTE]
> Jeśli centrum danych powróci do trybu online przed aktywacją aplikacji za pośrednictwem odzyskanej bazy danych, możesz anulować odzyskiwania.  
>
>

### <a name="perform-post-failover--recovery-tasks"></a>Wykonywanie zadań po przejściu do trybu failover lub po odzyskiwaniu
Po odzyskaniu za pomocą dowolnego mechanizmu odzyskiwania należy wykonać następujące zadania dodatkowe, zanim będzie możliwe ponowne rozpoczęcie pracy przez użytkowników i aplikacje:

* Przekieruj klientów i aplikacje klienckie na nowy serwer i przywróconą bazę danych
* Zapewnij użycie odpowiednich reguł zapory na poziomie serwera na potrzeby nawiązywania połączenia przez użytkowników (lub użyj [zapór na poziomie bazy danych](sql-database-firewall-configure.md#creating-and-managing-firewall-rules))
* Zapewnij użycie odpowiednich danych logowania i uprawnień na poziomie głównej bazy danych (lub użyj [użytkowników, którzy się w niej znajdują](https://msdn.microsoft.com/library/ff929188.aspx))
* W razie potrzeby skonfiguruj inspekcję
* W razie potrzeby skonfiguruj alerty

## <a name="upgrade-an-application-with-minimal-downtime"></a>Uaktualnianie aplikacji przy minimalnych przestojach
Czasami aplikacji należy podjąć w trybie offline z powodu zaplanowanej konserwacji, takich jak uaktualnienie aplikacji. [Zarządzanie uaktualnieniami aplikacji](sql-database-manage-application-rolling-upgrade.md) informacje dotyczące używania aktywna replikacja geograficzna umożliwia stopniowe aplikacji chmury w celu zminimalizowania przestojów podczas uaktualniania i podaj ścieżkę odzyskiwania, jeśli jakaś nieprawidłowość. 

## <a name="next-steps"></a>Kolejne kroki
Dyskusję na temat zagadnień dotyczących projektowania aplikacji na potrzeby autonomicznych baz danych i pul elastycznych zawierają tematy [Design an application for cloud disaster recovery](sql-database-designing-cloud-solutions-for-disaster-recovery.md) (Projektowanie aplikacji pod kątem odzyskiwania po awarii w chmurze) i [Elastic Pool disaster recovery strategies](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md) (Strategie odzyskiwania po awarii dotyczące pul elastycznych).
