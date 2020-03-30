---
title: Ciągłość działania chmury — odzyskiwanie baz danych
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
ms.date: 06/25/2019
ms.openlocfilehash: 4f30bf112175742566c2957d78154e5a7abd1733
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79096868"
---
# <a name="overview-of-business-continuity-with-azure-sql-database"></a>Omówienie zagadnień dotyczących ciągłości działalności biznesowej zapewnianej przez usługę Azure SQL Database

**Ciągłość działania** w usłudze Azure SQL Database odnosi się do mechanizmów, zasad i procedur, które umożliwiają twojej firmie kontynuowanie działalności w obliczu zakłóceń, w szczególności w jej infrastrukturze obliczeniowej. W większości przypadków usługa Azure SQL Database będzie obsługiwać zdarzenia powodujące zakłócenia, które mogą wystąpić w środowisku chmury i utrzymać aplikacje i procesy biznesowe uruchomione. Istnieją jednak pewne zdarzenia zakłócające, które nie mogą być obsługiwane przez bazę danych SQL automatycznie, takie jak:

- Użytkownik przypadkowo usunął lub zaktualizował wiersz w tabeli.
- Złośliwemu osobie atakującej udało się usunąć dane lub upuścić bazę danych.
- Trzęsienie ziemi spowodowało przerwę w dostawie prądu i tymczasowe wyłączone centrum danych.

W tym omówieniu opisano możliwości usługi Azure SQL Database w zakresie zapewniania ciągłości działalności biznesowej i odzyskiwania po awarii. Dowiedz się więcej o opcjach, zaleceniach i samouczkach dotyczących odzyskiwania danych po zdarzeniach zakłócających zakłócenia, które mogą spowodować utratę danych lub spowodować, że baza danych i aplikacja staną się niedostępne. Dowiedz się, co zrobić, gdy błąd użytkownika lub aplikacji wpływa na integralność danych, region platformy Azure ma awarię lub aplikacja wymaga konserwacji.

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>Funkcje usługi SQL Database, których można użyć, aby zapewnić ciągłość działalności biznesowej

Z punktu widzenia bazy danych istnieją cztery główne potencjalne scenariusze zakłóceń:

- Lokalne awarie sprzętu lub oprogramowania wpływające na węzeł bazy danych, takie jak awaria dysku.
- Uszkodzenie lub usunięcie danych zwykle spowodowane przez błąd aplikacji lub błąd ludzki. Takie błędy są specyficzne dla aplikacji i zazwyczaj nie mogą być wykryte przez usługę bazy danych.
- Awaria centrum danych, prawdopodobnie spowodowana klęską żywiołową. W tym scenariuszu wymaga pewnego poziomu nadmiarowości geograficznej z pracy awaryjnej aplikacji do alternatywnego centrum danych.
- Błędy uaktualnienia lub konserwacji, nieoczekiwane problemy występujące podczas planowanej konserwacji infrastruktury lub uaktualnień mogą wymagać szybkiego wycofywania do poprzedniego stanu bazy danych.

Aby ograniczyć lokalne awarie sprzętu i oprogramowania, baza danych SQL zawiera [architekturę wysokiej dostępności,](sql-database-high-availability.md)która gwarantuje automatyczne odzyskiwanie z tych błędów z 99.995% dostępności SLA.  

Aby chronić firmę przed utratą danych, baza danych SQL database automatycznie tworzy pełne kopie zapasowe bazy danych co tydzień, różnicowe kopie zapasowe bazy danych co 12 godzin, a kopie zapasowe dziennika transakcji co 5 - 10 minut. Kopie zapasowe są przechowywane w magazynie RA-GRS przez co najmniej 7 dni dla wszystkich warstw usług. Wszystkie warstwy usług z wyjątkiem podstawowych pomocy technicznej konfigurowalny okres przechowywania kopii zapasowych dla przywracania punktu w czasie, do 35 dni. 

Baza danych SQL zawiera również kilka funkcji ciągłości biznesowej, których można użyć w celu ograniczenia różnych nieplanowanych scenariuszy. 

- [Tabele danych czasowych](sql-database-temporal-tables.md) umożliwiają przywrócenie wersji wiersza z dowolnego punktu w czasie.
- [Wbudowane automatyczne kopie zapasowe](sql-database-automated-backups.md) i [przywracanie w czasie](sql-database-recovery-using-backups.md#point-in-time-restore) umożliwia przywrócenie pełnej bazy danych do pewnego momentu w czasie w skonfigurowanym okresie przechowywania do 35 dni.
- Można [przywrócić usuniętą bazę danych](sql-database-recovery-using-backups.md#deleted-database-restore) do punktu, w którym została usunięta, jeśli **serwer bazy danych SQL nie został usunięty**.
- [Długoterminowe przechowywanie kopii zapasowych](sql-database-long-term-retention.md) umożliwia przechowywanie kopii zapasowych do 10 lat.
- [Aktywna replikacja geograficzna](sql-database-active-geo-replication.md) umożliwia tworzenie replik czytelnych i ręczne przełączenie w tryb failover do dowolnej repliki w przypadku awarii centrum danych lub uaktualnienia aplikacji.
- [Grupa automatycznego trybu failover](sql-database-auto-failover-group.md#auto-failover-group-terminology-and-capabilities) umożliwia aplikacji automatyczne odzyskiwanie w przypadku awarii centrum danych.

## <a name="recover-a-database-within-the-same-azure-region"></a>Odzyskiwanie bazy danych w tym samym regionie platformy Azure

Automatycznych kopii zapasowych bazy danych można użyć, aby przywrócić bazę danych do punktu w czasie w przeszłości. W ten sposób można odzyskać z uszkodzenia danych spowodowane przez błędy ludzkie. Przywracanie punktu w czasie umożliwia utworzenie nowej bazy danych na tym samym serwerze, która reprezentuje stan danych przed zdarzeniem uszkodzenia. W przypadku większości baz danych operacje przywracania trwa mniej niż 12 godzin. Odzyskanie bardzo dużej lub bardzo aktywnej bazy danych może potrwać dłużej. Aby uzyskać więcej informacji na temat czasu odzyskiwania, zobacz [czas odzyskiwania bazy danych](sql-database-recovery-using-backups.md#recovery-time). 

Jeśli maksymalny obsługiwany okres przechowywania kopii zapasowej dla przywracania punktu w czasie (PITR) nie jest wystarczający dla aplikacji, można go rozszerzyć, konfigurując zasady długoterminowego przechowywania (LTR) dla baz danych. Aby uzyskać więcej informacji, zobacz [Długoterminowe przechowywanie kopii zapasowych](sql-database-long-term-retention.md).

## <a name="compare-geo-replication-with-failover-groups"></a>Porównywanie replikacji geograficznej z grupami trybu failover

[Grupy automatycznego trybu failover](sql-database-auto-failover-group.md#auto-failover-group-terminology-and-capabilities) upraszczają wdrażanie i korzystanie z [replikacji geograficznej](sql-database-active-geo-replication.md) oraz dodają dodatkowe możliwości opisane w poniższej tabeli:

|                                              | Replikacja geograficzna | Grupy trybu failover  |
|:---------------------------------------------| :-------------- | :----------------|
| Automatyczne przełączanie w tryb failover                           |     Nie          |      Tak         |
| Jednoczesne tworzenie wielu baz danych w trybie failas  |     Nie          |      Tak         |
| Użytkownik musi zaktualizować parametry połączenia po przełączeniu awaryjnym      |     Tak         |      Nie          |
| Obsługiwane wystąpienie zarządzane                   |     Nie          |      Tak         |
| Może znajdować się w tym samym regionie co             |     Tak         |      Nie          |
| Wiele replik                            |     Tak         |      Nie          |
| Obsługuje skalę odczytu                          |     Tak         |      Tak         |
| &nbsp; | &nbsp; | &nbsp; |


## <a name="recover-a-database-to-the-existing-server"></a>Odzyskiwanie bazy danych na istniejącym serwerze

Sporadycznie centrum danych platformy Azure może mieć awarię. Taka awaria powoduje zakłócenia działania firmy, które mogą trwać tylko kilka minut, ale mogą też trwać wiele godzin.

- Jedną z opcji jest oczekiwanie, aż baza danych powróci do trybu online po zakończeniu awarii centrum danych. Takie rozwiązanie sprawdza się dla aplikacji, w przypadku których baza danych może być w trybie offline. Może to na przykład dotyczyć projektu tworzenia oprogramowania lub bezpłatnej wersji próbnej, nad którymi nie trzeba pracować na bieżąco. Gdy centrum danych ma awarię, nie wiadomo, jak długo może trwać awaria, więc ta opcja działa tylko wtedy, gdy nie potrzebujesz bazy danych przez jakiś czas.
- Inną opcją jest przywrócenie bazy danych na dowolnym serwerze w dowolnym regionie platformy Azure przy użyciu [geograficznie nadmiarowych kopii zapasowych bazy danych](sql-database-recovery-using-backups.md#geo-restore) (przywracanie geograficzne). Przywracanie geograficzne używa geograficznie nadmiarowej kopii zapasowej jako źródła i może służyć do odzyskiwania bazy danych, nawet jeśli baza danych lub centrum danych jest niedostępna z powodu awarii.
- Na koniec można szybko odzyskać po awarii, jeśli skonfigurowano geo-pomocnicze przy użyciu [aktywnej replikacji geograficznej](sql-database-active-geo-replication.md) lub [grupy automatycznego trybu failover](sql-database-auto-failover-group.md) dla bazy danych lub baz danych. W zależności od wybranej technologii można użyć ręcznego lub automatycznego trybu failover. Podczas gdy samo tryb failover trwa tylko kilka sekund, usługa zajmie co najmniej 1 godzinę, aby go aktywować. Jest to konieczne, aby upewnić się, że pracy awaryjnej jest uzasadnione skalą awarii. Ponadto pracy awaryjnej może spowodować utratę danych małe ze względu na charakter replikacji asynchroniiowej. 

Podczas opracowywania planu zapewniania ciągłości działalności biznesowej należy zrozumieć znaczenie maksymalnego dopuszczalnego czasu oczekiwania na pełne odzyskanie aplikacji po wystąpieniu zdarzenia powodującego zakłócenia. Czas wymagany do pełnego odzyskania przez aplikację jest znany jako cel czasu odzyskiwania (RTO). Należy również zrozumieć maksymalny okres ostatnich aktualizacji danych (przedział czasu) aplikacja może tolerować utraty podczas odzyskiwania po nieplanowanym zdarzeniu zakłócające. Potencjalna utrata danych jest znana jako cel punktu odzyskiwania (RPO).

Różne metody odzyskiwania oferują różne poziomy RPO i RTO. Można wybrać określoną metodę odzyskiwania lub użyć kombinacji metod, aby osiągnąć pełne odzyskiwanie aplikacji. W poniższej tabeli porównano rpo i rto każdej opcji odzyskiwania. Grupy automatycznego trybu failover upraszczają wdrażanie i użycie replikacji geograficznej i dodają dodatkowe możliwości opisane w poniższej tabeli.

| Metoda odzyskiwania | Rto | CEL PUNKTU ODZYSKIWANIA |
| --- | --- | --- | 
| Przywracanie geograficzne z replikowanych geograficznie kopii zapasowych | 12 godz. | 1 godz. |
| Grupy automatycznego trybu failover | 1 godz. | 5 s |
| Ręczne tryb failover bazy danych | 30 s | 5 s |

> [!NOTE]
> *Ręczne przełączenie awaryjne bazy danych* odnosi się do pracy awaryjnej pojedynczej bazy danych do jej replikowanej geograficznie pomocniczej przy użyciu [trybu nieplanowanego](sql-database-active-geo-replication.md#active-geo-replication-terminology-and-capabilities).
Zobacz tabelę wcześniej w tym artykule, aby uzyskać szczegółowe informacje na temat automatycznego trybu failover RTO i celu RPO.


Użyj grup automatycznego trybu failover, jeśli aplikacja spełnia którekolwiek z następujących kryteriów:

- Ma kluczowe znaczenie.
- Ma umowę dotyczącą poziomu usług (SLA), która nie pozwala na 12 godzin lub więcej przestojów.
- Przestoje mogą skutkować odpowiedzialnością finansową.
- Ma wysoki wskaźnik zmiany danych i 1 godzina utraty danych jest nie do przyjęcia.
- Dodatkowy koszt związany z aktywną replikacją geograficzną jest niższy niż potencjalna odpowiedzialność finansowa i powiązane straty biznesowe.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-protecting-important-DBs-from-regional-disasters-is-easy/player]
>

W zależności od wymagań aplikacji można użyć kombinacji kopii zapasowych bazy danych i aktywnej replikacji geograficznej. Aby zapoznać się z zagadnieniami projektowymi dla autonomicznych baz danych i pul elastycznych korzystających z tych funkcji ciągłości biznesowej, zobacz [Projektowanie aplikacji do odzyskiwania po awarii w chmurze](sql-database-designing-cloud-solutions-for-disaster-recovery.md) i [strategii odzyskiwania po awarii puli elastycznej.](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)

Poniższe sekcje zawierają omówienie kroków do odzyskania przy użyciu kopii zapasowych bazy danych lub aktywnej replikacji geograficznej. Aby uzyskać szczegółowe kroki, w tym wymagania dotyczące planowania, kroki odzyskiwania księgowania i informacje dotyczące symulowania awarii w celu wykonania ćwiczenia odzyskiwania po awarii, zobacz [Odzyskiwanie bazy danych SQL z awarii](sql-database-disaster-recovery.md).

### <a name="prepare-for-an-outage"></a>Przygotowanie do awarii

Niezależnie od używanej funkcji zapewniania ciągłości działalności biznesowej należy:

- Identyfikowanie i przygotowywanie serwera docelowego, w tym reguł zapory IP na poziomie serwera, logowania i uprawnień na poziomie głównej bazy danych.
- Określić sposób przekierowania klientów i aplikacji klienckich na nowy serwer.
- Udokumentować inne zależności, takie jak ustawienia inspekcji i alerty

Jeśli nie przygotujesz się prawidłowo, przełączenie aplikacji do trybu online po pracy awaryjnej lub odzyskiwanie bazy danych zajmuje dodatkowy czas i prawdopodobnie wymaga również rozwiązywania problemów w czasie stresu - złe połączenie.

### <a name="fail-over-to-a-geo-replicated-secondary-database"></a>Przejmuje się w błąd do pomocniczej bazy danych replikowanej geograficznie

Jeśli jako mechanizm odzyskiwania używasz aktywnej replikacji geograficznej lub grup automatycznego trybu failover, możesz skonfigurować zasadę automatycznego trybu failover lub użyć [ręcznego nieplanowanego trybu failover](sql-database-active-geo-replication-portal.md#initiate-a-failover). Po zainicjowaniu, pracy awaryjnej powoduje, że pomocniczy, aby stać się nowym podstawowym i gotowy do rejestrowania nowych transakcji i odpowiadać na zapytania — przy minimalnej utracie danych nie zostały jeszcze zreplikowane. Aby uzyskać informacje na temat projektowania procesu pracy awaryjnej, zobacz [Projektowanie aplikacji do odzyskiwania po awarii w chmurze](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

> [!NOTE]
> Gdy centrum danych powróci do trybu online, stare prawybory automatycznie ponownie łączą się z nową bazą podstawową i stają się pomocniczymi bazami danych. Jeśli chcesz przenieść podstawowy z powrotem do oryginalnego regionu, można zainicjować planowane pracy awaryjnej ręcznie (powrót po awarii).

### <a name="perform-a-geo-restore"></a>Przeprowadzanie przywracania geograficznego

Jeśli używasz automatycznych kopii zapasowych z magazynem geograficznie nadmiarowym (domyślnie włączonym), możesz odzyskać bazę danych za pomocą [funkcji przywracania geograficznego](sql-database-disaster-recovery.md#recover-using-geo-restore). Odzyskiwanie zwykle odbywa się w ciągu 12 godzin — z utratą danych do jednej godziny zależy od tego, kiedy ostatnia kopia zapasowa dziennika została podjęta i zreplikowana. Do momentu ukończenia odzyskiwania baza danych nie może rejestrować żadnych transakcji ani odpowiadać na żadne zapytania. Uwaga: przywracanie geograficzne przywraca tylko bazę danych do ostatniego dostępnego punktu w czasie.

> [!NOTE]
> Jeśli centrum danych powróci do trybu online przed przełączeniem aplikacji do odzyskanej bazy danych, można anulować odzyskiwanie.

### <a name="perform-post-failover--recovery-tasks"></a>Wykonywanie zadań po przejściu do trybu failover lub po odzyskiwaniu

Po odzyskaniu za pomocą dowolnego mechanizmu odzyskiwania należy wykonać następujące zadania dodatkowe, zanim będzie możliwe ponowne rozpoczęcie pracy przez użytkowników i aplikacje:

- Przekieruj klientów i aplikacje klienckie na nowy serwer i przywróconą bazę danych
- Upewnij się, że istnieją odpowiednie reguły zapory IP na poziomie serwera, aby użytkownicy mogli łączyć się z [zapory na poziomie bazy danych](sql-database-firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules) lub używać ich w celu włączenia odpowiednich reguł.
- Zapewnij użycie odpowiednich danych logowania i uprawnień na poziomie głównej bazy danych (lub użyj [użytkowników, którzy się w niej znajdują](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable))
- W razie potrzeby skonfiguruj inspekcję
- W razie potrzeby skonfiguruj alerty

> [!NOTE]
> Jeśli używasz grupy trybu failover i połączyć się z bazami danych za pomocą lstener odczytu i zapisu, przekierowanie po pracy awaryjnej nastąpi automatycznie i w sposób przezroczysty do aplikacji.

## <a name="upgrade-an-application-with-minimal-downtime"></a>Uaktualnianie aplikacji przy minimalnych przestojach

Czasami aplikacja musi zostać przetraktować w trybie offline z powodu planowanej konserwacji, takiej jak uaktualnienie aplikacji. [Zarządzanie uaktualnieniami aplikacji](sql-database-manage-application-rolling-upgrade.md) opisuje sposób używania aktywnej replikacji geograficznej w celu umożliwienia stopniowych uaktualnień aplikacji w chmurze w celu zminimalizowania przestojów podczas uaktualnień i zapewnienia ścieżki odzyskiwania, jeśli coś pójdzie nie tak.

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z zagadnieniami związanymi z projektowaniem aplikacji dla autonomicznych baz danych i pul elastycznych, zobacz [Projektowanie aplikacji do odzyskiwania po awarii w chmurze](sql-database-designing-cloud-solutions-for-disaster-recovery.md) i [strategii odzyskiwania po awarii puli elastycznej.](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)
