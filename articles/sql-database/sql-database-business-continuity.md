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
ms.date: 06/25/2019
ms.openlocfilehash: 2f3723e0a1b14edd6f516f3cc080501bea80d486
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442050"
---
# <a name="overview-of-business-continuity-with-azure-sql-database"></a>Omówienie zagadnień dotyczących ciągłości działalności biznesowej zapewnianej przez usługę Azure SQL Database

**Ciągłość prowadzenia działalności biznesowej** w usłudze Azure SQL Database odnosi się do mechanizmów, zasady i procedury, które umożliwiają firmie do kontynuowania działania w przypadku przerwy w działaniu, zwłaszcza w celu jego infrastruktury obliczeniowej. W większości przypadków usługi Azure SQL Database będzie obsługiwać szkodliwe zdarzenia, które mogą mieć miejsce w środowisku chmury i zachować swoje aplikacje i procesów biznesowych działających. Istnieją jednak pewne szkodliwe zdarzenia, które nie mogą być obsługiwane przez usługę SQL Database automatycznie takich jak:

- Użytkownik przypadkowo usunięty lub zaktualizowany wiersz w tabeli.
- Złośliwy osoba atakująca pomyślnie usunąć dane i usuniesz bazę danych.
- Trzęsienie ziemi spowodowane awarii zasilania i tymczasowego wyłączenia centrum danych.

W tym omówieniu opisano możliwości usługi Azure SQL Database w zakresie zapewniania ciągłości działalności biznesowej i odzyskiwania po awarii. Dowiedz się więcej na temat opcji, zalecenia i samouczki dotyczące odzyskiwania po wystąpieniu zdarzeń powodujących zakłócenia, które mogą spowodować utratę danych lub spowodować, że bazy danych i aplikacja staną się niedostępne. Dowiedz się, co należy zrobić po błędzie użytkownika lub aplikacji wpływa na integralność danych, region platformy Azure wystąpiła awaria lub aplikacja wymaga konserwacji.

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>Funkcje usługi SQL Database, których można użyć, aby zapewnić ciągłość działalności biznesowej

Istnieją cztery główne scenariusze potencjalnych przerw w działaniu, z punktu widzenia bazy danych:

- Lokalnych awarii sprzętu lub oprogramowania wpływających na węzeł bazy danych, takiej jak awaria dysku.
- Uszkodzeniu lub usunięciu danych zazwyczaj przyczyną błędu aplikacji lub ludzkiego błędu. Takie błędy są specyficzne dla aplikacji i zazwyczaj nie mogą zostać wykryte przez usługę bazy danych.
- Po awarii centrum danych, prawdopodobnie spowodowane klęskami żywiołowymi. Ten scenariusz wymaga pewien stopień nadmiarowości geograficznej aplikacji przejścia w tryb failover do alternatywnego centrum danych.
- Uaktualnianie lub obsługi błędów, nieprzewidziane problemy, które występują podczas planowanej konserwacji infrastruktury lub uaktualnienia mogą wymagać szybkiego wycofania do stanu poprzedniego bazy danych.

Aby uniknąć lokalnego sprzętu i oprogramowania błędy, usługa SQL Database udostępnia [architektura wysokiej dostępności](sql-database-high-availability.md), która gwarantuje automatycznego odzyskiwania sprawności po tych błędów za pomocą 99.995% umowy SLA zapewniającej dostępność.  

Aby chronić swoją firmę przed utratą danych, SQL Database automatycznie tworzy pełne kopie zapasowe bazy danych co tydzień, różnicowe kopie zapasowe co 12 godzin, a transakcja kopie zapasowe dzienników co 5 – 10 minut. Kopie zapasowe są przechowywane w magazynu RA-GRS dla co najmniej 7 dni w przypadku wszystkich warstw usługi. Wszystkie warstwy usługi, z wyjątkiem Basic obsługuje okres przechowywania kopii zapasowej można konfigurować w momencie przywracania, maksymalnie 35 dni. 

Bazy danych SQL Database udostępnia kilka funkcji ciągłości działania, używanych w celu złagodzenia różne scenariusze nieplanowane. 

- [Tabele danych czasowych](sql-database-temporal-tables.md) umożliwiają przywrócenie wersji wiersza z dowolnego punktu w czasie.
- [Wbudowane automatyczne kopie zapasowe](sql-database-automated-backups.md) i [punktu przywracania do określonego](sql-database-recovery-using-backups.md#point-in-time-restore) pozwala przywrócić pełną bazę danych do pewnego momentu w czasie w ciągu skonfigurowanego okresu przechowywania do 35 dni.
- Możesz [przywrócić usuniętą bazę](sql-database-recovery-using-backups.md#deleted-database-restore) do punktu, w którym został usunięty, jeśli **bazy danych programu SQL server nie został usunięty**.
- [Długoterminowe przechowywanie kopii zapasowych](sql-database-long-term-retention.md) pozwala na bieżąco kopie zapasowe do 10 lat.
- [Aktywna replikacja geograficzna](sql-database-active-geo-replication.md) pozwala na tworzenie replik z możliwością odczytu i ręcznego trybu failover do dowolnej repliki w przypadku uaktualniania data center awarii lub aplikacji.
- [Automatyczny tryb failover grupy](sql-database-auto-failover-group.md#auto-failover-group-terminology-and-capabilities) umożliwia to aplikacji automatyczne odzyskiwanie w przypadku awarii centrum danych.

## <a name="recover-a-database-within-the-same-azure-region"></a>Odzyskiwanie bazy danych w tym samym regionie platformy Azure

Można użyć automatycznych kopiach zapasowych, aby przywrócić bazę danych do punktu w czasie w przeszłości. Dzięki temu można odzyskać z uszkodzenia danych, spowodowanych błędami ludzkimi. Przywracanie punktu w czasie służy do tworzenia nowej bazy danych na tym samym serwerze, który reprezentuje stan dane przed błędny zdarzeń. Dla większości operacji przywracania baz danych trwa mniej niż 12 godzin. Może trwać dłużej, aby odzyskać bardzo dużej lub bardzo aktywni albo bazę danych. Aby uzyskać więcej informacji o czasie odzyskiwania, zobacz [bazy danych czas odzyskiwania](sql-database-recovery-using-backups.md#recovery-time). 

Maksymalna obsługiwana okres przechowywania kopii zapasowych w momencie przywracania (Odzyskiwanie) nie jest wystarczający dla aplikacji, można go rozszerzyć, konfigurując długoterminowe zasady przechowywania (od lewej do prawej) dla baz danych. Aby uzyskać więcej informacji, zobacz [długoterminowego przechowywania kopii zapasowych](sql-database-long-term-retention.md).

## <a name="recover-a-database-to-another-azure-region"></a>Odzyskiwanie bazy danych do innego regionu platformy Azure

Sporadycznie centrum danych platformy Azure może mieć awarię. Taka awaria powoduje zakłócenia działania firmy, które mogą trwać tylko kilka minut, ale mogą też trwać wiele godzin.

- Jedną z opcji jest oczekiwanie, aż baza danych powróci do trybu online po zakończeniu awarii centrum danych. Takie rozwiązanie sprawdza się dla aplikacji, w przypadku których baza danych może być w trybie offline. Może to na przykład dotyczyć projektu tworzenia oprogramowania lub bezpłatnej wersji próbnej, nad którymi nie trzeba pracować na bieżąco. Centrum danych po awarii nie wiesz, jak długo może trwać awarii, więc ta opcja działa tylko, jeśli nie potrzebujesz bazy danych od pewnego czasu.
- Innym rozwiązaniem jest Przywracanie bazy danych na dowolnym serwerze w dowolnym regionie świadczenia usługi Azure przy użyciu [kopie zapasowe bazy danych magazynu geograficznie nadmiarowego](sql-database-recovery-using-backups.md#geo-restore) (Przywracanie geograficzne). Przywracanie geograficzne korzysta z geograficznie nadmiarowej kopii zapasowej jako źródła i może służyć do odzyskiwania bazy danych, nawet jeśli bazy danych lub centrum danych jest niedostępna z powodu awarii.
- Na koniec można szybko odzyskać sprawności po awarii, jeśli zostały skonfigurowane przy użyciu pomocniczej geograficznej [aktywnej replikacji geograficznej](sql-database-active-geo-replication.md) lub [automatyczny tryb failover grupy](sql-database-auto-failover-group.md) dla baz danych lub bazy danych. W zależności od wybranego tych technologii można użyć trybu failover ręcznych lub automatycznych. Podczas pracy awaryjnej, sama zajmuje tylko kilka sekund, usługa potrwa co najmniej 1 godzina, aby ją uaktywnić. Jest to konieczne upewnić się, aby przejść do trybu failover jest uzasadnione skali awarii. Ponadto przełączenie w tryb failover może spowodować utratę danych małych ze względu na charakter replikacji asynchronicznej. 

Podczas opracowywania planu zapewniania ciągłości działalności biznesowej należy zrozumieć znaczenie maksymalnego dopuszczalnego czasu oczekiwania na pełne odzyskanie aplikacji po wystąpieniu zdarzenia powodującego zakłócenia. Czas wymagany do aplikacji w celu przeprowadzenia pełnego odzyskania jest określany jako cel czasu odzyskiwania (RTO). Należy również zrozumieć maksymalny okres najnowszych aktualizacji danych (przedział czasu) aplikacja może tolerować utraty, gdy odzyskiwanie po wystąpieniu zdarzenia powodującego zakłócenia nieplanowane. Potencjalna utrata danych jest określany jako cel punktu odzyskiwania (RPO).

Metody odzyskiwania inny oferują różne poziomy RPO i RTO. Wybierz metodę określonego odzyskiwania lub użycie kombinacji metod achicethe odzyskiwania pełnej aplikacji. W poniższej tabeli porównano RPO i RTO poszczególne opcje.

| Metoda odzyskiwania | CEL CZASU ODZYSKIWANIA | RPO |
| --- | --- | --- | 
| Przywracanie geograficzne z kopii zapasowych z replikacją geograficzną | 12 godz. | 1 godz. |
| Grupy automatycznego trybu failover | 1 godz. | 5 s |
| Tryb failover ręczne bazy danych | 30 s | 5 s |

> [!NOTE]
> *Ręczne bazy danych w tryb failover* odwołuje się do trybu failover dla pojedynczej bazy danych do jego replikowanej geograficznie pomocniczej za pomocą [nieplanowany tryb](sql-database-active-geo-replication.md#active-geo-replication-terminology-and-capabilities).
Zobacz tabelę w tym artykule, aby uzyskać szczegółowe informacje automatyczny tryb failover RTO i RPO.


Użyj grup automatyczny tryb failover, jeśli Twoja aplikacja spełnia dowolne z poniższych kryteriów:

- Ma kluczowe znaczenie.
- Ma Umowa dotycząca poziomu usług (SLA), który nie zezwala na 12 godzin lub więcej przestojów.
- Może spowodować przestój odpowiedzialności finansowej.
- Ma wysoki współczynnik danych zmiany i godzinę utraty danych nie jest dopuszczalna.
- Dodatkowy koszt związany z aktywną replikacją geograficzną jest niższy niż potencjalna odpowiedzialność finansowa i powiązane straty biznesowe.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-protecting-important-DBs-from-regional-disasters-is-easy/player]
>

Można użyć kombinacji kopii zapasowych bazy danych i aktywnej replikacji geograficznej w zależności od wymagań aplikacji. Omówienie zagadnień projektowych dla autonomicznych baz danych i pul elastycznych za pomocą tych funkcji ciągłości działania, zobacz [projektowania aplikacji do odzyskiwania po awarii w chmurze](sql-database-designing-cloud-solutions-for-disaster-recovery.md) i [po awarii w puli elastycznej Strategie odzyskiwania](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

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
