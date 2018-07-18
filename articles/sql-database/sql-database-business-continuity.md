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
ms.date: 07/16/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: dfea1587cddbf7440771ca7007928f7e4054f61a
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2018
ms.locfileid: "39092294"
---
# <a name="overview-of-business-continuity-with-azure-sql-database"></a>Omówienie zagadnień dotyczących ciągłości działalności biznesowej zapewnianej przez usługę Azure SQL Database

W tym omówieniu opisano możliwości usługi Azure SQL Database w zakresie zapewniania ciągłości działalności biznesowej i odzyskiwania po awarii. Dowiedz się więcej na temat opcji, zalecenia i samouczki dotyczące odzyskiwania po wystąpieniu zdarzeń powodujących zakłócenia, które mogą spowodować utratę danych lub spowodować, że bazy danych i aplikacja staną się niedostępne. Dowiedz się, co należy zrobić po błędzie użytkownika lub aplikacji wpływa na integralność danych, region platformy Azure wystąpiła awaria lub aplikacja wymaga konserwacji.

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>Funkcje usługi SQL Database, których można użyć, aby zapewnić ciągłość działalności biznesowej

Usługa SQL Database udostępnia kilka funkcji zapewniających ciągłość działalności biznesowej, w tym zautomatyzowane kopie zapasowe oraz opcjonalną replikację bazy danych. Każda z tych funkcji ma różne charakterystyki dotyczące szacowanego czasu odzyskiwania (ERT, estimated recovery time) i potencjalnej utraty danych dotyczących ostatnich transakcji. Po zapoznaniu się z tymi opcjami można dokonać między nimi wyboru, a także — w większości przypadków — użyć ich razem w ramach różnych scenariuszy. Podczas opracowywania planu zapewniania ciągłości działalności biznesowej należy zrozumieć znaczenie maksymalnego dopuszczalnego czasu oczekiwania na pełne odzyskanie aplikacji po wystąpieniu zdarzenia powodującego zakłócenia — jest to cel czasu odzyskiwania (RTO, recovery time objective). Należy również zrozumieć maksymalną ilość danych najnowszych aktualizacji (przedział czasu) aplikacja może tolerować momencie odzyskiwania po wystąpieniu zdarzenia powodującego zakłócenia — jest to cel punktu odzyskiwania (RPO).

W poniższej tabeli porównano wartości ERT i RPO dla każdej warstwy usług dla trzech najbardziej typowych scenariuszy.

| Możliwości | Podstawowa | Standardowa (Standard) | Premium  | Ogólne zastosowanie | Krytyczne dla działania firmy
| --- | --- | --- | --- |--- |--- |
| Przywracanie do punktu w czasie z kopii zapasowej |Dowolny punkt przywracania w ciągu ostatnich 7 dni |Dowolny punkt przywracania w ciągu ostatnich 35 dni |Dowolny punkt przywracania w ciągu ostatnich 35 dni |Dowolny punkt przywracania w ciągu skonfigurowanego okresu (maksymalnie 35 dni)|Dowolny punkt przywracania w ciągu skonfigurowanego okresu (maksymalnie 35 dni)|
| Przywracanie geograficzne z kopii zapasowych z replikacją geograficzną |ERT < 12 godz., RPO < 1 godz. |ERT < 12 godz., RPO < 1 godz. |ERT < 12 godz., RPO < 1 godz. |ERT < 12 godz., RPO < 1 godz.|ERT < 12 godz., RPO < 1 godz.|
| Przywracanie z magazynu usługi Azure Backup |ERT < 12 godz., RPO < 1 tydz. |ERT < 12 godz., RPO < 1 tydz. |ERT < 12 godz., RPO < 1 tydz. |ERT < 12 godz., RPO < 1 tydz.|ERT < 12 godz., RPO < 1 tydz.|
| Aktywna replikacja geograficzna |ERT < 30 s, RPO < 5 s |ERT < 30 s, RPO < 5 s |ERT < 30 s, RPO < 5 s |ERT < 30 s, RPO < 5 s|ERT < 30 s, RPO < 5 s|

### <a name="use-point-in-time-restore-to-recover-a-database"></a>Użyj w momencie przywracania, aby odzyskać bazę danych

SQL Database automatycznie przeprowadza kombinację co tydzień pełne kopie zapasowe, różnicowe kopie zapasowe co godzinę i transakcji kopie zapasowe dzienników co 5 - 10 minut, aby chronić swoją firmę przed utratą danych. Jeśli używasz [modelu zakupu opartego na jednostkach DTU](sql-database-service-tiers-dtu.md), a następnie te kopie zapasowe są przechowywane w magazynu RA-GRS przez 35 dni dla baz danych w warstwach standardowa i Premium oraz 7 dni w przypadku baz danych w warstwie podstawowa. Jeśli okres przechowywania w warstwie usług nie spełnia Twoich wymagań biznesowych, można go zwiększyć, [zmieniając warstwę usług](sql-database-single-database-scale.md). Jeśli używasz [modelu zakupu opartego na rdzeniach wirtualnych](sql-database-service-tiers-vcore.md), przechowywania kopii zapasowych jest możliwość konfiguracji do 35 dni w ogólnego przeznaczenia i krytyczne warstwy biznesowej. Pełne oraz różnicowe kopie zapasowe bazy danych są także replikowane do [sparowanego centrum danych](../best-practices-availability-paired-regions.md) w celu ochrony przed awarią centrum danych. Aby uzyskać więcej informacji, zobacz [automatycznych kopiach zapasowych](sql-database-automated-backups.md).

Maksymalna obsługiwana w momencie przywracania (Odzyskiwanie) okres przechowywania nie jest wystarczający dla aplikacji, można go rozszerzyć, konfigurując długoterminowe zasady przechowywania (od lewej do prawej) dla baz danych. Aby uzyskać więcej informacji, zobacz [zautomatyzowane kopie zapasowe](sql-database-automated-backups.md) i [długoterminowego przechowywania kopii zapasowych](sql-database-long-term-retention.md).

Tych automatycznych kopii zapasowych bazy danych można użyć, aby odzyskać bazę danych po wystąpieniu różnych zdarzeń powodujących zakłócenia, zarówno w obrębie centrum danych, jak również do innego centrum danych. W przypadku korzystania z automatycznych kopii zapasowych bazy danych szacowany czas odzyskiwania zależy od kilku czynników, w tym łącznej liczby jednocześnie odzyskiwanych baz danych w tym samym regionie, rozmiaru bazy danych, rozmiaru dziennika transakcji oraz przepustowości sieci. Czas odzyskiwania jest zazwyczaj mniej niż 12 godzin. Może trwać dłużej, aby odzyskać bazę danych lub bardzo dużych active. Aby uzyskać więcej informacji o czasie odzyskiwania, zobacz [bazy danych czas odzyskiwania](sql-database-recovery-using-backups.md#recovery-time). Podczas odzyskiwania do innego obszaru danych potencjalna utrata danych jest ograniczona do 1 godziny przez magazyn geograficznie nadmiarowy w ramach tworzonych co godzinę różnicowych kopii zapasowych bazy danych.

> [!IMPORTANT]
> Aby przeprowadzić odzyskiwanie za pomocą automatycznych kopii zapasowych, użytkownik musi być członkiem roli Współautor programu SQL Server lub właścicielem subskrypcji — zobacz [RBAC: Built-in roles](../role-based-access-control/built-in-roles.md) (RBAC: role wbudowane). Odzyskiwanie można przeprowadzić za pomocą witryny Azure Portal, programu PowerShell lub interfejsu API REST. Nie można używać języka Transact-SQL.
>

Zautomatyzowanych kopii zapasowych jako mechanizmu odzyskiwania i zapewniania ciągłości działalności biznesowej należy używać, jeśli aplikacja:

* Nie jest traktowana jako aplikacja o kluczowym znaczeniu.
* Nie ma powiązanej umowy SLA - przestój 24 godzin lub już nie poniesienia odpowiedzialności finansowej.
* Ma niską częstotliwość zmian danych (mała liczba transakcji na godzinę) i utrata zmian z maksymalnie jednej godziny jest akceptowalna.
* Jej koszt ma znaczenie.

Jeśli wymagane jest szybsze odzyskiwanie, użyj [aktywnej replikacji geograficznej](sql-database-geo-replication-overview.md) (omówionej poniżej). Jeśli potrzebujesz można było odzyskanie danych starszych niż 35 dni, należy użyć [długoterminowego przechowywania](sql-database-long-term-retention.md). 

### <a name="use-active-geo-replication-and-auto-failover-groups-to-reduce-recovery-time-and-limit-data-loss-associated-with-a-recovery"></a>Użyj aktywnej replikacji geograficznej i automatyczny tryb failover grupy, aby skrócić czas odzyskiwania i ograniczyć utratę danych związaną z odzyskiwaniem

Oprócz używania kopii zapasowych bazy danych do odzyskiwania bazy danych w przypadku zakłócenia działania firmy wystąpienia, można użyć [aktywnej replikacji geograficznej](sql-database-geo-replication-overview.md) skonfigurować bazę danych można mieć maksymalnie cztery odczytu pomocniczych baz danych w wybranych regionach świadczenia. Te pomocnicze bazy danych są stale synchronizowane z podstawową bazą danych przy użyciu mechanizmu replikacji asynchronicznej. Ta funkcja jest używana do ochrony przed zakłóceniami działania firmy w przypadku awarii centrum danych lub podczas uaktualniania aplikacji. Aktywna replikacja geograficzna może służyć także do zapewnienia lepszej wydajności zapytań tylko do odczytu geograficznie rozproszonym użytkownikom.

Aby włączyć automatyczne i przezroczysty tryb failover replikacji geograficznej bazy danych należy zorganizować do grup przy użyciu [automatyczny tryb failover grupy](sql-database-geo-replication-overview.md) funkcji SQL Database.

Jeśli podstawowa baza danych przejdzie do trybu offline nieoczekiwanie lub należy uwzględnić go w trybie offline dla czynności konserwacyjnych, możliwe jest szybkie wypromowanie pomocniczej do roli podstawowej (nazywane również przejścia w tryb failover) i skonfigurować aplikacje, aby nawiązać połączenie z wypromowaną podstawową. Jeśli aplikacja łączy się z bazami danych, przy użyciu odbiornika grupy trybu failover, nie trzeba zmienić konfigurację ciąg połączenia SQL po włączeniu trybu failover. Planowane przejście w tryb failover nie spowoduje utraty żadnych danych. W przypadku nieplanowanego przejścia w tryb failover może wystąpić utrata niewielkiej ilości danych dotyczących tylko najnowszych transakcji ze względu na charakter replikacji asynchronicznej. Przy użyciu grup automatyczny tryb failover, można dostosować zasady trybu failover, aby zminimalizować potencjalnej utraty danych. Po przejściu w tryb failover możliwy jest późniejszy powrót po awarii — albo zgodnie z planem, albo gdy centrum danych powróci do trybu online. We wszystkich przypadkach użytkownicy doświadczą krótkiego przestoju i będą musieli ponownie nawiązać połączenie.

> [!IMPORTANT]
> Aby korzystać z aktywnej replikacji geograficznej i automatyczny tryb failover grupy, możesz być właścicielem subskrypcji lub mieć uprawnienia administracyjne w programie SQL Server. Można skonfigurować, a także w trybie Failover przy użyciu platformy Azure portal, programu PowerShell lub interfejsu API REST przy użyciu uprawnień subskrypcji platformy Azure lub przy użyciu języka Transact-SQL z uprawnieniami do programu SQL Server.
> 

Użyj aktywnej replikacji geograficznej i automatyczny tryb failover grupy, jeśli Twoja aplikacja spełnia dowolne z poniższych kryteriów:

* Ma kluczowe znaczenie.
* Obejmuje ją umowa dotycząca poziomu usług (SLA), zgodnie z którą przestój nie może być dłuższy niż 24 godziny.
* Może spowodować przestój odpowiedzialności finansowej.
* Ma wysoki współczynnik zmian danych i utrata zmian z okresu jednej godziny jest niedopuszczalna.
* Dodatkowy koszt związany z aktywną replikacją geograficzną jest niższy niż potencjalna odpowiedzialność finansowa i powiązane straty biznesowe.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-protecting-important-DBs-from-regional-disasters-is-easy/player]
>

## <a name="recover-a-database-after-a-user-or-application-error"></a>Odzyskiwanie bazy danych po błędzie użytkownika lub aplikacji

Nikt nie jest doskonały! Użytkownik może przypadkowo usunąć pewne dane, nieodwracalnie usunąć ważną tabelę lub nawet usunąć całą bazę danych. Możliwe jest również przypadkowe zastąpienie przez aplikację poprawnych danych błędnymi danymi w wyniku wady aplikacji.

W tym scenariuszu opcje odzyskiwania są następujące.

### <a name="perform-a-point-in-time-restore"></a>Wykonanie przywracania do punktu w czasie
Możliwe jest użycie zautomatyzowanych kopii zapasowych, aby odzyskać kopię bazy danych do znanego prawidłowego punktu w czasie, pod warunkiem, że ten punkt w czasie przypada na okres przechowywania bazy danych. Po przywróceniu bazy danych można albo zastąpić oryginalną bazę danych przywróconą bazą danych, albo skopiować potrzebne dane z przywróconych danych do oryginalnej bazy danych. Jeśli baza danych używa aktywnej replikacji geograficznej, zaleca się skopiowanie wymaganych danych z przywróconej kopii do oryginalnej bazy danych. Jeśli zastąpić oryginalną bazę danych przywróconą bazą danych, należy ponownie skonfigurować i zsynchronizować aktywną replikację geograficzną (co może być bardzo czasochłonne dla dużych baz danych). Gdy to spowoduje przywrócenie bazy danych do ostatniego dostępnego punktu w czasie, przywracanie pomocniczej geograficznej do dowolnego punktu w czasie nie jest obecnie obsługiwane.

Aby uzyskać więcej informacji i poznać szczegółowe kroki przywracania bazy danych do punktu w czasie za pomocą witryny Azure Portal lub programu PowerShell, zobacz informacje dotyczące [przywracania do punktu w czasie](sql-database-recovery-using-backups.md#point-in-time-restore). Nie można przeprowadzić odzyskiwania za pomocą języka Transact-SQL.

### <a name="restore-a-deleted-database"></a>Przywracanie usuniętej bazy danych
Jeśli baza danych została usunięta, ale serwer logiczny nie został usunięty, można przywrócić usuniętą bazę danych do punktu, w którym została ona usunięta. Spowoduje to przywrócenie kopii zapasowej bazy danych na ten sam logiczny serwer SQL, z którego została ona usunięta. Bazę danych można przywrócić przy użyciu oryginalnej nazwy; można też nadać jej nową nazwę.

Aby uzyskać więcej informacji i poznać szczegółowe kroki przywracania usuniętej bazy danych za pomocą witryny Azure Portal lub programu PowerShell, zobacz informacje dotyczące [przywracania usuniętej bazy danych](sql-database-recovery-using-backups.md#deleted-database-restore). Nie można przeprowadzić przywracania za pomocą języka Transact-SQL.

> [!IMPORTANT]
> Jeśli usunięto serwer logiczny, nie można odzyskać usuniętej bazy danych.


### <a name="restore-backups-from-long-term-retention"></a>Przywracanie kopii zapasowych długoterminowym przechowywaniu

Jeśli utrata danych miała miejsce poza bieżącym okresem przechowywania dla zautomatyzowanych kopii zapasowych i baza danych jest skonfigurowana do długoterminowego przechowywania danych, można przywrócić z pełnej kopii zapasowej w magazynie od lewej do prawej, do nowej bazy danych. W takim momencie można albo zastąpić oryginalną bazę danych przywróconą bazą danych, albo skopiować potrzebne dane z przywróconej bazy danych do oryginalnej bazy danych. Jeśli konieczne jest pobranie starej wersji bazy danych przed istotnego uaktualnienia aplikacji, spełnieniem żądania audytorów lub wykonaniem nakazu prawnego, można utworzyć bazę danych przy użyciu pełnej kopii zapasowej zapisać w magazynie kopii zapasowych Azure.  Aby uzyskać więcej informacji, zobacz [Długoterminowe przechowywanie](sql-database-long-term-retention.md).

## <a name="recover-a-database-to-another-region-from-an-azure-regional-data-center-outage"></a>Odzyskiwanie bazy danych do innego regionu podczas awarii regionalnego centrum danych platformy Azure
<!-- Explain this scenario -->

Sporadycznie centrum danych platformy Azure może mieć awarię. Taka awaria powoduje zakłócenia działania firmy, które mogą trwać tylko kilka minut, ale mogą też trwać wiele godzin.

* Jedną z opcji jest oczekiwanie, aż baza danych powróci do trybu online po zakończeniu awarii centrum danych. Takie rozwiązanie sprawdza się dla aplikacji, w przypadku których baza danych może być w trybie offline. Może to na przykład dotyczyć projektu tworzenia oprogramowania lub bezpłatnej wersji próbnej, nad którymi nie trzeba pracować na bieżąco. Centrum danych po awarii nie wiesz, jak długo może trwać awarii, więc ta opcja działa tylko, jeśli nie potrzebujesz bazy danych od pewnego czasu.
* Innym rozwiązaniem jest albo awarii za pośrednictwem do innego regionu danych korzystając z aktywnej replikacji geograficznej lub przeprowadzenie odzyskiwania bazy danych za pomocą kopii zapasowych bazy danych magazynu geograficznie nadmiarowego (Przywracanie geograficzne). Tryb failover trwa tylko kilka sekund, podczas odzyskiwania bazy danych z kopii zapasowej zajmuje.

Po wykonaniu działania czas potrzebny do odzyskania i ilość utraconych danych zależy od tego, jak zdecydujesz się używać tych funkcji ciągłości działania w aplikacji. W rzeczywistości można użyć kombinacji kopii zapasowych bazy danych i aktywnej replikacji geograficznej w zależności od wymagań aplikacji. Dyskusję na temat zagadnień dotyczących projektowania aplikacji na potrzeby autonomicznych baz danych i pul elastycznych za pomocą tych funkcji zapewniania ciągłości działalności biznesowej zawierają tematy [Design an application for cloud disaster recovery](sql-database-designing-cloud-solutions-for-disaster-recovery.md) (Projektowanie aplikacji pod kątem odzyskiwania po awarii w chmurze) i [Elastic Pool disaster recovery strategies](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md) (Strategie odzyskiwania po awarii dotyczące pul elastycznych).

Omówienie kroków, aby odzyskać za pomocą kopii zapasowych bazy danych lub aktywnej replikacji geograficznej można znaleźć w poniższych sekcjach. Aby uzyskać szczegółowy opis kroków, w tym planowanie wymagań, kroki po odzyskiwaniu i informacji o sposobie symulacji awarii, aby wykonać odzyskiwanie po awarii, zobacz [odzyskiwanie bazy danych SQL po awarii](sql-database-disaster-recovery.md).

### <a name="prepare-for-an-outage"></a>Przygotowanie do awarii
Niezależnie od używanej funkcji zapewniania ciągłości działalności biznesowej należy:

* Zidentyfikować i przygotować serwer docelowy, w tym reguły zapory na poziomie serwera, dane logowania i uprawnienia na poziomie głównej bazy danych.
* Określić sposób przekierowania klientów i aplikacji klienckich na nowy serwer.
* Udokumentować inne zależności, takie jak ustawienia inspekcji i alerty

Jeśli nie przygotowywania prawidłowo, przeniesienie aplikacji trybu online po przejściu w tryb failover lub odzyskiwanie bazy danych zajmuje dodatkowy czas, które mogą również wymagać rozwiązywania problemów w czasie obciążenia — to niefortunne połączenie.

### <a name="fail-over-to-a-geo-replicated-secondary-database"></a>Failover do pomocniczej bazy danych replikowanej geograficznie
Jeśli jako mechanizm odzyskiwania używają aktywna replikacja geograficzna i grupy automatyczny tryb failover, można skonfigurować zasady automatycznej pracy awaryjnej lub użyć [ręczna praca awaryjna](sql-database-disaster-recovery.md#fail-over-to-geo-replicated-secondary-server-in-the-failover-group). Po zainicjowaniu przełączenie w tryb failover spowoduje, że pomocniczej do nowej podstawowej i gotowa do rejestrowania nowych transakcji i odpowiadania na zapytania — z minimalną utratą danych dla danych nie zostały jeszcze zreplikowane. Aby uzyskać informacje dotyczące projektowania procesu pracy awaryjnej, zobacz [projektowania aplikacji do odzyskiwania po awarii w chmurze](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

> [!NOTE]
> Gdy centrum danych powróci do trybu online stare kolory podstawowe ponownie połączyć się z nową podstawową i automatycznie stają się pomocniczych baz danych. Konieczne jest przeniesienie podstawowej kopii do oryginalnej regionu, możesz ręcznie zainicjować planowanego trybu failover (powrót po awarii). 
> 

### <a name="perform-a-geo-restore"></a>Wykonaj operację przywracania geograficznego
Jeśli używane są zautomatyzowane kopie zapasowe przy użyciu replikacji dla magazynu geograficznie nadmiarowego jako mechanizm odzyskiwania, [zainicjować odzyskiwanie bazy danych przy użyciu przywracania geograficznego](sql-database-disaster-recovery.md#recover-using-geo-restore). Odzyskiwanie ma zwykle miejsce w ciągu 12 godzin, z utratą danych maksymalnie z jednej godziny, w zależności od tego, kiedy została wykonana i zreplikowana ostatnia tworzona co godzinę różnicowa kopia zapasowa. Do momentu ukończenia odzyskiwania baza danych nie może rejestrować żadnych transakcji ani odpowiadać na żadne zapytania. Gdy to spowoduje przywrócenie bazy danych do ostatniego dostępnego punktu w czasie, przywracanie pomocniczej geograficznej do dowolnego punktu w czasie nie jest obecnie obsługiwane.

> [!NOTE]
> Jeśli centrum danych powróci do trybu online przed przełączeniem aplikacji za pośrednictwem do odzyskanej bazy danych, można anulować odzyskiwanie.  
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
Czasami aplikacja musi przełączone do trybu offline z powodu zaplanowanej konserwacji, takich jak uaktualnienie aplikacji. [Zarządzanie uaktualnieniami aplikacji](sql-database-manage-application-rolling-upgrade.md) w tym artykule opisano sposób użycia aktywnej replikacji geograficznej w celu umożliwienia przeprowadzania uaktualnienia równoległych aplikacji w chmurze skrócić czas przestoju podczas uaktualnień i zapewnić ścieżkę odzyskiwania w przypadku, gdyby coś poszło źle. 

## <a name="next-steps"></a>Kolejne kroki
Dyskusję na temat zagadnień dotyczących projektowania aplikacji na potrzeby autonomicznych baz danych i pul elastycznych zawierają tematy [Design an application for cloud disaster recovery](sql-database-designing-cloud-solutions-for-disaster-recovery.md) (Projektowanie aplikacji pod kątem odzyskiwania po awarii w chmurze) i [Elastic Pool disaster recovery strategies](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md) (Strategie odzyskiwania po awarii dotyczące pul elastycznych).
