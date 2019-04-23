---
title: Ciągłość działalności biznesowej w chmurze — odzyskiwanie bazy danych — usługa SQL Database | Microsoft Docs
description: Dowiedz się, w jaki sposób usługa Azure SQL Database obsługuje ciągłość działalności biznesowej w chmurze i odzyskiwanie bazy danych oraz pomaga zapewnić działanie aplikacji o kluczowym znaczeniu w chmurze.
keywords: business continuity,cloud business continuity,database disaster recovery,database recovery
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 04/04/2019
ms.openlocfilehash: dfa5d4cb2d782f1466329300157a64fd17765460
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59797697"
---
# <a name="overview-of-business-continuity-with-azure-sql-database"></a>Omówienie zagadnień dotyczących ciągłości działalności biznesowej zapewnianej przez usługę Azure SQL Database

**Ciągłość prowadzenia działalności biznesowej** w usłudze Azure SQL Database odnosi się do mechanizmów, zasady i procedury, które umożliwiają firmie do kontynuowania działania w przypadku przerwy w działaniu, zwłaszcza w celu jego infrastruktury obliczeniowej. W większości przypadków usługi Azure SQL Database będzie obsługiwać szkodliwe zdarzenia, które mogą mieć miejsce w środowisku chmury i zachować swoje aplikacje i procesów biznesowych działających. Istnieją jednak pewne szkodliwe zdarzenia, które nie mogą być obsługiwane przez usługę SQL Database takich jak:

- Użytkownik przypadkowo usunięty lub zaktualizowany wiersz w tabeli.
- Złośliwy osoba atakująca pomyślnie usunąć dane i usuniesz bazę danych.
- Trzęsienie ziemi spowodowane awarii zasilania i tymczasowego wyłączenia centrum danych.

Takiej sytuacji nie mogą być kontrolowane przez usługę Azure SQL Database, dlatego należy używać funkcji ciągłości działania w usłudze SQL Database, która umożliwia odzyskiwanie danych i Zachowaj działanie swoich aplikacji.

W tym omówieniu opisano możliwości usługi Azure SQL Database w zakresie zapewniania ciągłości działalności biznesowej i odzyskiwania po awarii. Dowiedz się więcej na temat opcji, zalecenia i samouczki dotyczące odzyskiwania po wystąpieniu zdarzeń powodujących zakłócenia, które mogą spowodować utratę danych lub spowodować, że bazy danych i aplikacja staną się niedostępne. Dowiedz się, co należy zrobić po błędzie użytkownika lub aplikacji wpływa na integralność danych, region platformy Azure wystąpiła awaria lub aplikacja wymaga konserwacji.

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>Funkcje usługi SQL Database, których można użyć, aby zapewnić ciągłość działalności biznesowej

Istnieją cztery główne scenariusze potencjalnych przerw w działaniu, z punktu widzenia bazy danych:

- Lokalnych awarii sprzętu lub oprogramowania wpływających na węzeł bazy danych, takiej jak awaria dysku.
- Uszkodzeniu lub usunięciu danych zazwyczaj przyczyną błędu aplikacji lub ludzkiego błędu. Takie błędy są wewnętrznie specyficzne dla aplikacji, a nie, ponieważ reguła być wykryte lub automatycznie skorygowane przez infrastrukturę.
- Po awarii centrum danych, prawdopodobnie spowodowane klęskami żywiołowymi. Ten scenariusz wymaga pewien stopień nadmiarowości geograficznej aplikacji przejścia w tryb failover do alternatywnego centrum danych.
- Błędy uaktualnienia lub konserwację, nieprzewidziane problemy, które występują podczas planowanej uaktualnienia lub konserwację aplikacji lub bazy danych może wymagać szybkiego wycofania do stanu poprzedniego bazy danych.

SQL Database udostępnia kilka funkcji ciągłości działania, w tym zautomatyzowane kopie zapasowe oraz opcjonalną replikację bazy danych, która pozwala ograniczyć tych scenariuszy. Najpierw należy zrozumieć, jak bazy danych SQL [architektura wysokiej dostępności](sql-database-high-availability.md) zapewnia dostępność przez 99,99% dostępności i odporności na niektóre szkodliwe zdarzenia, które mogą mieć wpływ na konkretnego procesu biznesowego.
Następnie informacje na temat dodatkowych mechanizmów, które umożliwiają odzyskanie szkodliwe zdarzenia, które nie mogą być obsługiwane przez architekturę wysokiej dostępności bazy danych SQL, takich jak:

- [Tabele danych czasowych](sql-database-temporal-tables.md) umożliwiają przywrócenie wersje wiersza z dowolnego punktu w czasie.
- [Wbudowane automatyczne kopie zapasowe](sql-database-automated-backups.md) i [punktu przywracania do określonego](sql-database-recovery-using-backups.md#point-in-time-restore) umożliwia przywrócenie pełnej bazy danych do pewnego momentu w czasie w ciągu ostatnich 35 dni.
- Możesz [przywrócić usuniętą bazę](sql-database-recovery-using-backups.md#deleted-database-restore) do punktu, w którym został usunięty, jeśli **bazy danych programu SQL server nie został usunięty**.
- [Długoterminowe przechowywanie kopii zapasowych](sql-database-long-term-retention.md) pozwala na bieżąco kopie zapasowe do 10 lat.
- [Aktywna replikacja geograficzna](sql-database-active-geo-replication.md) pozwala na tworzenie replik z możliwością odczytu i ręcznego trybu failover do dowolnej repliki w przypadku uaktualniania data center awarii lub aplikacji.
- [Automatyczny tryb failover grupy](sql-database-auto-failover-group.md#auto-failover-group-terminology-and-capabilities) umożliwia to aplikacji automatyczne odzyskiwanie w przypadku awarii centrum danych.

Każda z tych funkcji ma różne charakterystyki dotyczące szacowanego czasu odzyskiwania (ERT, estimated recovery time) i potencjalnej utraty danych dotyczących ostatnich transakcji. Po zapoznaniu się z tymi opcjami można dokonać między nimi wyboru, a także — w większości przypadków — użyć ich razem w ramach różnych scenariuszy. Podczas opracowywania planem ciągłości biznesowej, należy zrozumieć maksymalnego dopuszczalnego czasu oczekiwania na pełne odzyskanie aplikacji po wystąpieniu zdarzenia powodującego zakłócenia. Czas wymagany do aplikacji w celu przeprowadzenia pełnego odzyskania jest określany jako cel czasu odzyskiwania (RTO). Należy również zrozumieć maksymalny okres najnowszych aktualizacji danych (przedział czasu) aplikacja może tolerować momencie odzyskiwania po wystąpieniu zdarzenia powodującego zakłócenia. Okres aktualizacji, które mogą umożliwić utratę jest określany jako cel punktu odzyskiwania (RPO).

W poniższej tabeli porównano wartości ERT i RPO dla każdej warstwy usług dla najbardziej typowych scenariuszy.

| Możliwości | Podstawowa | Standardowa (Standard) | Premium | Ogólne zastosowanie | Krytyczne dla działania firmy
| --- | --- | --- | --- |--- |--- |
| Przywracanie do punktu w czasie z kopii zapasowej |Dowolny punkt przywracania w ciągu siedmiu dni |Dowolny punkt przywracania w ciągu ostatnich 35 dni |Dowolny punkt przywracania w ciągu ostatnich 35 dni |Dowolny punkt przywracania w ciągu skonfigurowanego okresu (maksymalnie 35 dni)|Dowolny punkt przywracania w ciągu skonfigurowanego okresu (maksymalnie 35 dni)|
| Przywracanie geograficzne z kopii zapasowych z replikacją geograficzną |ERT < 12 godz.<br> RPO < 1 godz. |ERT < 12 godz.<br>RPO < 1 godz. |ERT < 12 godz.<br>RPO < 1 godz. |ERT < 12 godz.<br>RPO < 1 godz.|ERT < 12 godz.<br>RPO < 1 godz.|
| Grupy automatycznego trybu failover |Cel czasu odzyskiwania = 1 godz.<br>RPO < 5 s |Cel czasu odzyskiwania = 1 godz.<br>RPO < 5 s |Cel czasu odzyskiwania = 1 godz.<br>RPO < 5 s |Cel czasu odzyskiwania = 1 godz.<br>RPO < 5 s|Cel czasu odzyskiwania = 1 godz.<br>RPO < 5 s|
| Tryb failover ręczne bazy danych |ERT = 30 s<br>RPO < 5 s |ERT = 30 s<br>RPO < 5 s |ERT = 30 s<br>RPO < 5 s |ERT = 30 s<br>RPO < 5 s|ERT = 30 s<br>RPO < 5 s|

> [!NOTE]
> *Ręczne bazy danych w tryb failover* odwołuje się do trybu failover dla pojedynczej bazy danych do jego replikowanej geograficznie pomocniczej za pomocą [nieplanowany tryb](sql-database-active-geo-replication.md#active-geo-replication-terminology-and-capabilities).

## <a name="recover-a-database-to-the-existing-server"></a>Odzyskiwanie bazy danych do istniejącego serwera

SQL Database automatycznie przeprowadza kombinację tworzenia pełnych kopii zapasowych co tydzień, różnicowe kopie zapasowe, zazwyczaj wykonywane co 12 godzin i dziennika kopii zapasowych co 5 – 10 minut, aby chronić swoją firmę przed utratą danych transakcji. Kopie zapasowe są przechowywane w magazynu RA-GRS do 35 dni dla wszystkich warstwach usługi, z wyjątkiem następujących warstwach usługi podstawowa jednostek DTU przechowywania kopii zapasowych przez 7 dni. Aby uzyskać więcej informacji, zobacz [automatycznych kopiach zapasowych](sql-database-automated-backups.md). Możesz przywrócić istniejącego formularza bazy danych automatycznych kopii zapasowych do wcześniejszego punktu w czasie jako nową bazę danych na tym samym serwerze bazy danych SQL przy użyciu witryny Azure portal, programu PowerShell lub interfejsu API REST. Aby uzyskać więcej informacji, zobacz [punktu w czasie przywracania](sql-database-recovery-using-backups.md#point-in-time-restore).

Maksymalna obsługiwana w momencie przywracania (Odzyskiwanie) okres przechowywania nie jest wystarczający dla aplikacji, można go rozszerzyć, konfigurując długoterminowe zasady przechowywania (od lewej do prawej) dla baz danych. Aby uzyskać więcej informacji, zobacz [długoterminowego przechowywania kopii zapasowych](sql-database-long-term-retention.md).

Tych automatycznych kopii zapasowych bazy danych można użyć, aby odzyskać bazę danych po wystąpieniu różnych zdarzeń powodujących zakłócenia, zarówno w obrębie centrum danych, jak również do innego centrum danych. Czas odzyskiwania jest zazwyczaj mniej niż 12 godzin. Może trwać dłużej, aby odzyskać bazę danych lub bardzo dużych active. W przypadku korzystania z automatycznych kopii zapasowych bazy danych szacowany czas odzyskiwania zależy od kilku czynników, w tym łącznej liczby jednocześnie odzyskiwanych baz danych w tym samym regionie, rozmiaru bazy danych, rozmiaru dziennika transakcji oraz przepustowości sieci. Aby uzyskać więcej informacji o czasie odzyskiwania, zobacz [bazy danych czas odzyskiwania](sql-database-recovery-using-backups.md#recovery-time). W przypadku odzyskiwania do innego obszaru danych, potencjalnej utraty danych jest ograniczona do 1 godziny przy użyciu geograficznie nadmiarowych kopii zapasowych.

Zautomatyzowane tworzenie kopii zapasowych i [w momencie przywracania](sql-database-recovery-using-backups.md#point-in-time-restore) jako mechanizm ciągłość działalności biznesowej i odzyskiwania firm Jeśli aplikacji:

- Nie jest traktowana jako aplikacja o kluczowym znaczeniu.
- Nie ma powiązanej umowy SLA - przestój 24 godzin lub już nie poniesienia odpowiedzialności finansowej.
- Ma niską częstotliwość zmian danych (mała liczba transakcji na godzinę) i utrata zmian z maksymalnie jednej godziny jest akceptowalna.
- Jej koszt ma znaczenie.

Jeśli wymagane jest szybsze odzyskiwanie, użyj [aktywnej replikacji geograficznej](sql-database-active-geo-replication.md) lub [automatyczny tryb failover grupy](sql-database-auto-failover-group.md). Jeśli potrzebujesz można było odzyskanie danych starszych niż 35 dni, należy użyć [długoterminowego przechowywania](sql-database-long-term-retention.md).

## <a name="recover-a-database-to-another-region"></a>Odzyskiwanie bazy danych do innego regionu

Sporadycznie centrum danych platformy Azure może mieć awarię. Taka awaria powoduje zakłócenia działania firmy, które mogą trwać tylko kilka minut, ale mogą też trwać wiele godzin.

- Jedną z opcji jest oczekiwanie, aż baza danych powróci do trybu online po zakończeniu awarii centrum danych. Takie rozwiązanie sprawdza się dla aplikacji, w przypadku których baza danych może być w trybie offline. Może to na przykład dotyczyć projektu tworzenia oprogramowania lub bezpłatnej wersji próbnej, nad którymi nie trzeba pracować na bieżąco. Centrum danych po awarii nie wiesz, jak długo może trwać awarii, więc ta opcja działa tylko, jeśli nie potrzebujesz bazy danych od pewnego czasu.
- Innym rozwiązaniem jest Przywracanie bazy danych na dowolnym serwerze w dowolnym regionie świadczenia usługi Azure przy użyciu [kopie zapasowe bazy danych magazynu geograficznie nadmiarowego](sql-database-recovery-using-backups.md#geo-restore) (Przywracanie geograficzne). Przywracanie geograficzne korzysta z geograficznie nadmiarowej kopii zapasowej jako źródła i może służyć do odzyskiwania bazy danych, nawet jeśli bazy danych lub centrum danych jest niedostępna z powodu awarii.
- Na koniec można szybko odzyskać sprawności po awarii, jeśli zostały skonfigurowane przy użyciu pomocniczej geograficznej [aktywnej replikacji geograficznej](sql-database-active-geo-replication.md) lub [automatyczny tryb failover grupy](sql-database-auto-failover-group.md) dla baz danych lub bazy danych. W zależności od wybranego tych technologii można użyć trybu failover ręcznych lub automatycznych. Podczas pracy awaryjnej, sama zajmuje tylko kilka sekund, usługa potrwa co najmniej 1 godzina, aby ją uaktywnić. Jest to konieczne upewnić się, aby przejść do trybu failover jest uzasadnione skali awarii. Ponadto przełączenie w tryb failover może spowodować utratę danych małych ze względu na charakter replikacji asynchronicznej. Zobacz tabelę w tym artykule, aby uzyskać szczegółowe informacje automatyczny tryb failover RTO i RPO.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-protecting-important-DBs-from-regional-disasters-is-easy/player]
>
> [!IMPORTANT]
> Aby korzystać z aktywnej replikacji geograficznej i automatyczny tryb failover grupy, możesz być właścicielem subskrypcji lub mieć uprawnienia administracyjne w programie SQL Server. Można skonfigurować, a także w trybie Failover przy użyciu platformy Azure portal, programu PowerShell lub interfejsu API REST przy użyciu uprawnień subskrypcji platformy Azure lub przy użyciu języka Transact-SQL z uprawnieniami do programu SQL Server.

Użyj grup automatyczny tryb failover, jeśli Twoja aplikacja spełnia dowolne z poniższych kryteriów:

- Ma kluczowe znaczenie.
- Ma Umowa dotycząca poziomu usług (SLA), który nie zezwala na 12 godzin lub więcej przestojów.
- Może spowodować przestój odpowiedzialności finansowej.
- Ma wysoki współczynnik danych zmiany i godzinę utraty danych nie jest dopuszczalna.
- Dodatkowy koszt związany z aktywną replikacją geograficzną jest niższy niż potencjalna odpowiedzialność finansowa i powiązane straty biznesowe.

Po wykonaniu działania czas potrzebny do odzyskania i ilość utraconych danych zależy od tego, jak zdecydujesz się używać tych funkcji ciągłości działania w aplikacji. W rzeczywistości można użyć kombinacji kopii zapasowych bazy danych i aktywnej replikacji geograficznej w zależności od wymagań aplikacji. Omówienie zagadnień dotyczących projektowania aplikacji dla autonomicznych baz danych i pul elastycznych za pomocą tych funkcji ciągłości działania, zobacz [projektowania aplikacji do odzyskiwania po awarii w chmurze](sql-database-designing-cloud-solutions-for-disaster-recovery.md) i [elastyczne Strategie odzyskiwania po awarii w puli](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).


Omówienie kroków, aby odzyskać za pomocą kopii zapasowych bazy danych lub aktywnej replikacji geograficznej można znaleźć w poniższych sekcjach. Aby uzyskać szczegółowy opis kroków, w tym planowanie wymagań, kroki po odzyskiwaniu i informacji o sposobie symulacji awarii, aby wykonać odzyskiwanie po awarii, zobacz [odzyskiwanie bazy danych SQL po awarii](sql-database-disaster-recovery.md).

### <a name="prepare-for-an-outage"></a>Przygotowanie do awarii

Niezależnie od używanej funkcji zapewniania ciągłości działalności biznesowej należy:

- Zidentyfikować i przygotować serwer docelowy, w tym reguły zapory IP poziom serwera, logowania i uprawnienia na poziomie bazy danych master.
- Określić sposób przekierowania klientów i aplikacji klienckich na nowy serwer.
- Udokumentować inne zależności, takie jak ustawienia inspekcji i alerty

Jeśli nie przygotowywania prawidłowo, przeniesienie aplikacji trybu online po przejściu w tryb failover lub odzyskiwanie bazy danych zajmuje dodatkowy czas, które mogą również wymagać rozwiązywania problemów w czasie obciążenia — to niefortunne połączenie.

### <a name="fail-over-to-a-geo-replicated-secondary-database"></a>Failover do pomocniczej bazy danych replikowanej geograficznie

Jeśli używasz aktywnej replikacji geograficznej lub automatyczny tryb failover grupy jako mechanizm odzyskiwania można skonfigurować zasady automatycznej pracy awaryjnej lub użyć [ręczne nieplanowany tryb failover](sql-database-active-geo-replication-portal.md#initiate-a-failover). Po zainicjowaniu przełączenie w tryb failover spowoduje, że pomocniczej do nowej podstawowej i gotowa do rejestrowania nowych transakcji i odpowiadania na zapytania — z minimalną utratą danych dla danych nie zostały jeszcze zreplikowane. Aby uzyskać informacje dotyczące projektowania procesu pracy awaryjnej, zobacz [projektowania aplikacji do odzyskiwania po awarii w chmurze](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

> [!NOTE]
> Gdy centrum danych powróci do trybu online stare kolory podstawowe ponownie połączyć się z nową podstawową i automatycznie stają się pomocniczych baz danych. Konieczne jest przeniesienie podstawowej kopii do oryginalnej regionu, możesz ręcznie zainicjować planowanego trybu failover (powrót po awarii).

### <a name="perform-a-geo-restore"></a>Wykonaj operację przywracania geograficznego

Korzystając z automatycznych kopii zapasowych z magazynu geograficznie nadmiarowego (domyślnie włączony), można odzyskać bazy danych przy użyciu [geoprzywracanie](sql-database-disaster-recovery.md#recover-using-geo-restore). Odzyskiwanie ma zwykle miejsce w ciągu 12 godzin — z utratą danych maksymalnie jedną godzinę, zależą od podczas ostatniej kopii zapasowej dziennika zostało wykonane, a replikowane. Do momentu ukończenia odzyskiwania baza danych nie może rejestrować żadnych transakcji ani odpowiadać na żadne zapytania. Należy zauważyć, że funkcja przywracania geograficznego tylko przywraca bazę danych do ostatniego dostępnego punktu w czasie.

> [!NOTE]
> Jeśli centrum danych powróci do trybu online przed przełączeniem aplikacji za pośrednictwem do odzyskanej bazy danych, można anulować odzyskiwanie.

### <a name="perform-post-failover--recovery-tasks"></a>Wykonywanie zadań po przejściu do trybu failover lub po odzyskiwaniu

Po odzyskaniu za pomocą dowolnego mechanizmu odzyskiwania należy wykonać następujące zadania dodatkowe, zanim będzie możliwe ponowne rozpoczęcie pracy przez użytkowników i aplikacje:

- Przekieruj klientów i aplikacje klienckie na nowy serwer i przywróconą bazę danych
- Upewnij się, odpowiednie reguły zapory IP w poziomie serwera są stosowane dla użytkowników połączyć lub użyj [zapór na poziomie bazy danych](sql-database-firewall-configure.md#manage-server-level-ip-firewall-rules-using-the-azure-portal) włączyć odpowiednie zasady.
- Zapewnij użycie odpowiednich danych logowania i uprawnień na poziomie głównej bazy danych (lub użyj [użytkowników, którzy się w niej znajdują](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable))
- W razie potrzeby skonfiguruj inspekcję
- W razie potrzeby skonfiguruj alerty

> [!NOTE]
> Jeśli używasz grupy trybu failover i nawiązać połączenie z bazami danych, przy użyciu lstener odczytu i zapisu, przekierowanie po włączeniu trybu failover nastąpi automatycznie i w sposób niewidoczny dla użytkownika do aplikacji.

## <a name="upgrade-an-application-with-minimal-downtime"></a>Uaktualnianie aplikacji przy minimalnych przestojach

Czasami aplikacja musi przełączone do trybu offline z powodu zaplanowanej konserwacji, takich jak uaktualnienie aplikacji. [Zarządzanie uaktualnieniami aplikacji](sql-database-manage-application-rolling-upgrade.md) w tym artykule opisano sposób użycia aktywnej replikacji geograficznej w celu umożliwienia przeprowadzania uaktualnienia równoległych aplikacji w chmurze skrócić czas przestoju podczas uaktualnień i zapewnić ścieżkę odzyskiwania w przypadku, gdyby coś poszło źle.

## <a name="next-steps"></a>Kolejne kroki

Omówienie zagadnień dotyczących projektowania aplikacji dla autonomicznych baz danych i pul elastycznych, zobacz [projektowania aplikacji do odzyskiwania po awarii w chmurze](sql-database-designing-cloud-solutions-for-disaster-recovery.md) i [Strategie odzyskiwania po awarii w puli elastycznej](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
