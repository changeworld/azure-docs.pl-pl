---
title: Praca awaryjna grupy i aktywna replikacja geograficzna — baza danych SQL Azure | Dokumentacja firmy Microsoft
description: Użyj trybu failover automatycznie grup z aktywna replikacja geograficzna i Włącz autoomatic trybu failover w przypadku awarii.
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: article
ms.date: 04/04/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: 39c61fd3b2f03c99d74fded22927f319cc7f4cdd
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="overview-failover-groups-and-active-geo-replication"></a>Omówienie: Grup trybu Failover i aktywna replikacja geograficzna
Aktywna replikacja geograficzna można skonfigurować maksymalnie cztery czytelny dodatkowej bazy danych w centrach danych tego samego lub innego (regiony). Pomocniczych baz danych dostępnych do wykonywania zapytań i pracy awaryjnej w przypadku awarii centrum danych lub brakiem możliwości nawiązania połączenia podstawowej bazy danych. Tryb failover musi być inicjowana ręcznie przez użytkownika aplikacji. Po przejściu w tryb failover nową podstawową ma punkt końcowy innego połączenia. 

> [!NOTE]
> Aktywna replikacja geograficzna jest dostępna dla wszystkich baz danych w warstwach usług z wszystkich we wszystkich regionach.
>  

Azure grup pracy awaryjnej automatycznie bazy danych SQL (w — wersja zapoznawcza) jest funkcją bazy danych SQL przeznaczona do zarządzania automatycznie relacji — replikacja geograficzna, łączności i pracy awaryjnej na dużą skalę. Klienci uzyskują możliwość automatycznie wznowione po poważnej awarii regionalnych lub inne nieplanowanego zdarzenia, które pełnej lub częściowej utratę dostępności usługi SQL Database w regionie podstawowym wielu powiązanych baz danych w regionie pomocniczym. Ponadto ich używać do odczytu pomocniczej bazy danych odciążania obciążeń tylko do odczytu.  Ponieważ grupy pracy awaryjnej automatycznie wymagają wielu baz danych, musi być skonfigurowany na serwerze podstawowym. Podstawowe i dodatkowe serwery muszą być w tej samej subskrypcji. Grupy pracy awaryjnej automatycznie obsługują replikacji wszystkich baz danych w grupie, aby tylko jeden serwer pomocniczy w innym regionie. Aktywna replikacja geograficzna, bez grupy pracy awaryjnej automatycznie umożliwia do czterech replik pomocniczych w dowolnym regionie.

Jeśli używasz aktywna replikacja geograficzna i dla każdego przyczyny z podstawowej bazy danych kończy się niepowodzeniem lub musi zostać przełączony w tryb offline można zainicjować trybu failover do dowolnego z dodatkowej bazy danych. Podczas pracy awaryjnej jest aktywowany do jednej pomocniczej bazy danych, wszystkie inne pomocnicze bazy danych są automatycznie łączeni ze nową podstawową. Jeśli używasz trybu failover automatycznie grupy (w podglądzie) do zarządzania odzyskiwanie bazy danych i wszelkie awarii, który ma wpływ na jednego lub kilku baz danych w wynikach grupy w automatycznej pracy awaryjnej. Zasady automatycznej pracy w trybie failover, która najlepiej odpowiada Twoim potrzebom aplikacji można skonfigurować, lub można zrezygnować i korzystać z aktywacji ręcznego. Ponadto pracy awaryjnej automatycznie grupy (w podglądzie) podaj odczytu i zapisu, a punkty końcowe odbiornika tylko do odczytu, które pozostają bez zmian podczas pracy w trybie Failover. Czy używać aktywacji ręczne lub automatyczne trybu failover, pracy awaryjnej zmienia wszystkie pomocnicze bazy danych w grupie do podstawowych. Po zakończeniu pracy awaryjnej bazy danych rekord DNS zostanie automatycznie zaktualizowany do przekierowania do regionu nowe punkty końcowe.

Możesz zarządzać replikacji i trybu failover poszczególne bazy danych lub zestaw baz danych na serwerze lub w puli elastycznej za pomocą aktywna replikacja geograficzna. Możesz zrobić tego za pomocą 

- [Portalu Azure](sql-database-geo-replication-portal.md)
- [Programu PowerShell: Pojedyncza baza danych](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
- [Środowiska PowerShell: Pula elastyczna](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
- [Programu PowerShell: Grupy pracy awaryjnej](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md)
- [Transact-SQL: Pojedyncza baza danych lub puli elastycznej](/sql/t-sql/statements/alter-database-azure-sql-database)
- [Interfejsu API REST: Pojedyncza baza danych](/rest/api/sql/replicationlinks/failover)
- [Interfejs API REST: Trybu Failover grupy](/rest/api/sql/failovergroups/failover). 
 
Po przejściu w tryb failover upewnij się, że wymagania dotyczące uwierzytelniania dla serwera i bazy danych są skonfigurowane na serwerze podstawowym nowego. Aby uzyskać więcej informacji, zobacz [zabezpieczeń bazy danych SQL po awarii](sql-database-geo-replication-security-config.md). Dotyczy to zarówno do aktywnych grup — replikacja geograficzna i pracy awaryjnej automatycznego (w — wersja zapoznawcza).

Aktywna replikacja geograficzna wykorzystuje [zawsze na](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) technologii programu SQL Server, aby asynchronicznie zreplikowanie zatwierdzonych transakcji w głównej bazie danych do korzystania z dodatkowej bazy danych do odczytu izolacji migawki zatwierdzone (RCSI). Automatycznie pracy w trybie failover grupy zapewniają semantyki grupy na górze aktywna replikacja geograficzna, ale jest używany ten sam mechanizm replikacji asynchronicznej. Znajduje się na dowolnym etapie, dodatkowej bazy danych może być nieco za podstawowej bazy danych, danych pomocniczych jest gwarantowana nigdy nie mają częściowe transakcji. Nadmiarowość między region umożliwia aplikacjom Szybkie odzyskiwanie z trwałą utratę całe centrum danych lub części spowodowane klęski żywiołowej, krytycznego błędów ludzkich lub złośliwych działań Centrum danych. Cel punktu odzyskiwania danych można znaleźć w folderze [omówienie ciągłości działalności biznesowej](sql-database-business-continuity.md).

Na poniższej ilustracji przedstawiono przykład aktywna replikacja geograficzna skonfigurowany z użyciem podstawowego w regionie północno-środkowe Stany i dodatkowych w regionie południowo-środkowe Stany.

![Replikacja geograficzna relacji](./media/sql-database-active-geo-replication/geo-replication-relationship.png)

Ponieważ pomocniczej bazy danych można odczytać, ich może służyć do odciążenia obciążeń tylko do odczytu, takich jak zadania raportowania. Jeśli używasz aktywna replikacja geograficzna, umożliwia tworzenie pomocniczej bazy danych w tym samym regionie z serwera podstawowego, ale nie zwiększa odporność aplikacji do poważnej awarii. Jeśli używasz trybu failover automatycznie grupy (w podglądzie) z dodatkowej bazy danych zawsze jest tworzony w innym regionie.

Oprócz po awarii aktywna replikacja geograficzna odzyskiwania mogą być używane w następujących scenariuszach:

* **Baza danych migracji**: aktywna replikacja geograficzna umożliwia migrację bazy danych z jednego serwera na inny online minimalna przestojów.
* **Uaktualnianie aplikacji**: można utworzyć dodatkowe pomocniczy jako kopia tyłu podczas uaktualniania aplikacji.

Aby zapewnić ciągłość prawdziwe, dodawanie nadmiarowość bazy danych między centrami danych jest tylko część rozwiązania. Odzyskiwanie aplikacji (usługa) end-to-end po poważnej awarii wymaga odzyskiwania wszystkich składników, które stanowią usługi i usług zależnych. Przykładami takich składników oprogramowania klienta (na przykład przeglądarki z niestandardowych skryptów JavaScript), sieci Web, magazynu i DNS. Jest krytyczny, że wszystkie składniki są odporność na awarie tego samego i stają się dostępne w celu czasu odzyskiwania (RTO) aplikacji. W związku z tym należy zidentyfikować wszystkich zależnych usług i zrozumieć gwarancje i możliwości, które zapewniają. Następnie należy wykonać odpowiednie kroki w celu zapewnienia, że funkcji usługi podczas pracy awaryjnej usług, od których zależy. Aby uzyskać więcej informacji na temat projektowania rozwiązań do odzyskiwania po awarii, zobacz [projektowania rozwiązań chmury za pomocą odzyskiwania po awarii aktywna replikacja geograficzna](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## <a name="active-geo-replication-capabilities"></a>Aktywna replikacja geograficzna możliwości
Aktywna replikacja geograficzna udostępnia następujące podstawowe możliwości:
* **Automatyczne asynchroniczną replikację**: pomocniczej bazy danych można utworzyć tylko przez dodanie do istniejącej bazy danych. Pomocniczy mogą być tworzone w dowolnym serwerze bazy danych SQL Azure. Po utworzeniu dodatkowej bazy danych jest wypełniana dane skopiowane z podstawowej bazy danych. Ten proces jest nazywany wstępnego wypełniania. Po utworzeniu pomocniczej bazy danych i rozpoczęta, aktualizacje do podstawowej bazy danych są asynchronicznie replikowane do dodatkowej bazy danych automatycznie. Replikacja asynchronicznego oznacza, że transakcje są zostało zatwierdzone na poziomie podstawowej bazy danych, zanim one są replikowane do dodatkowej bazy danych. 
* **Do odczytu bazy danych w dodatkowej**: aplikacja może uzyskiwać dostęp do pomocniczej bazy danych dla operacji tylko do odczytu za pomocą podmiotów zabezpieczeń tego samego lub innego używane do uzyskiwania dostępu do podstawowej bazy danych. Pomocniczej bazy danych działa w trybie izolacji migawki, aby upewnić się, że replikacja aktualizacji z serwera podstawowego (dziennika powtarzania) nie jest opóźnione przez zapytania są wykonywane na serwerze pomocniczym.

   > [!NOTE]
   > Powtarzania dziennika jest opóźnione w pomocniczej bazie danych, jeśli są dostępne aktualizacje schematu na serwerze podstawowym. Drugie wymaga blokady schematu w pomocniczej bazie danych. 
   > 

* **Wiele elementów dodatkowych do odczytu**: dwa lub więcej pomocniczych baz danych zwiększenia nadmiarowości i poziom ochrony dla podstawowej bazy danych i aplikacji. Jeśli istnieje wiele pomocniczych baz danych, aplikacji pozostaje chroniony nawet w przypadku awarii jednego z dodatkowej bazy danych. Jeśli istnieje tylko jeden z dodatkowej bazy danych, a jej nie powiedzie się, aplikacji jest narażony na większe ryzyko, dopóki nie zostanie utworzony nowy pomocniczej bazy danych.

   > [!NOTE]
   > Jeśli używasz aktywna replikacja geograficzna do tworzenia aplikacji rozproszonych globalnie i trzeba zapewnić dostęp tylko do odczytu do danych w więcej niż czterech regionów, można utworzyć dodatkowej pomocniczej (proces znany jako łańcucha). W ten sposób można osiągnąć nieograniczoną skali replikacji bazy danych. Ponadto łańcucha zmniejsza obciążenie związane z replikacją z podstawowej bazy danych. Rozwiązanie jest opóźnienie replikacji zwiększona, w bazach danych dodatkowej większość typu liść. 
   >

* **Obsługa elastycznej puli baz danych**: każdej repliki można osobno uczestniczą w puli elastycznej lub nie można w każdej puli elastycznej w ogóle. Wybór puli dla każdej repliki jest oddzielony i nie zależą od konfiguracji innej repliki (czy podstawowej lub pomocniczej). Każda pula elastyczna jest zawarty w pojedynczym regionie, w związku z tym wiele replik w tej samej topologii nigdy nie mogą współużytkować puli elastycznej.
* **Poziom wydajności można skonfigurować bazy danych w dodatkowej**: podstawowych i pomocniczych baz danych muszą mieć tej samej warstwie usługi. Można utworzyć pomocniczą bazę danych z mniejszą wydajnością (Dtu) niż podstawowy. Ta opcja nie jest zalecana dla aplikacji z działania zapisu bazy danych wysokiej ponieważ opóźnienie replikacji zwiększona zwiększa ryzyko utraty danych znacznej po przejściu w tryb failover. Ponadto po pracy awaryjnej wydajności aplikacji jest w pełni funkcjonalne dopiero po uaktualnieniu nową podstawową na wyższy poziom wydajności. Wykres procent we/wy dziennika w portalu Azure zapewnia dobry sposób, aby oszacować poziom wydajności minimalnej dodatkowej, która jest wymagana do obsługi obciążenia replikacji. Na przykład, jeśli P6 jest podstawową bazą danych (1000 DTU) i procent we/wy dziennika to 50% lokacji dodatkowej musi mieć co najmniej P4 (500 DTU). Można również pobierać dane we/wy dziennika przy użyciu [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) lub [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) bazy danych widoków.  Aby uzyskać więcej informacji na temat poziomów wydajności bazy danych SQL, zobacz [co to są warstwach usług bazy danych SQL](sql-database-service-tiers.md). 
* **Kontrolowane przez użytkownika trybu failover i powrotu po awarii**: pomocniczej bazy danych można jawnie przełącza się na podstawową rolą w dowolnym momencie przez użytkownika lub aplikacji. Podczas rzeczywista awaria opcji "nieplanowane" należy, która wspiera natychmiast pomocniczego się serwerem podstawowym. Kiedy nie powiodło się podstawowym odzyskuje i jest ponownie dostępny, system automatycznie oznacza odzyskane podstawowej jako dodatkowej i przełączyć go, instalując nową podstawową. Ze względu na specyfikę asynchroniczne replikacji niewielką ilość danych mogą zostać utracone podczas niezaplanowanych operacji Failover Jeśli podstawowy ulegnie awarii, przed rozpoczęciem replikacji najnowszych zmian na serwerze pomocniczym. W przypadku awarii podstawowego przy użyciu wielu pomocnicze bazy danych za pośrednictwem systemu automatycznie ponownie konfiguruje relacji replikacji i łączy pozostałe serwery do nowo utworzonego podstawowych bez interwencji użytkownika. Po awarii, który spowodował przejście w tryb failover jest niewielkie, może być pożądane, aby powrócić do aplikacji w regionie podstawowym. W tym celu polecenia pracy awaryjnej powinna być wywoływana z opcją "planowane". 
* **Synchronizacja poświadczeń i jego reguły zapory**: Firma Microsoft zaleca używanie [bazy danych reguły zapory](sql-database-firewall-configure.md) replikacją geograficzną baz danych, więc te reguły mogą być replikowane z bazy danych, aby zapewnić wszystkie pomocnicze bazy danych tym samym reguły zapory jako podstawowy. Takie podejście eliminuje potrzebę stosowania klientów ręcznie konfigurowania i konserwacji reguły zapory na serwerach hostujących zarówno podstawowych i pomocniczych baz danych. Podobnie za pomocą [zawarte bazy danych użytkowników](sql-database-manage-logins.md) danych dostęp zapewnia podstawowe i pomocnicze bazy danych zawsze mieć taki sam poświadczenia użytkownika, dlatego podczas pracy w trybie failover nie przerwom z powodu niezgodności z logowania i hasła. Z dodatkiem [usługi Azure Active Directory](../active-directory/active-directory-whatis.md), klientów można zarządzać dostępem użytkowników do podstawowych i pomocniczych baz danych i wyeliminowanie konieczności zarządzania całkowicie poświadczeń w bazach danych.

## <a name="auto-failover-group-capabilities"></a>Funkcje grupy pracy awaryjnej automatycznie

Funkcja grup pracy awaryjnej automatycznie zapewnia zaawansowane Abstrakcja aktywna replikacja geograficzna dzięki obsłudze replikacji poziomu grupy i automatycznej pracy awaryjnej. Ponadto eliminuje konieczność zmiany parametrów połączenia SQL po pracy awaryjnej zapewniając punkty końcowe dodatkowe odbiornika. 

* **Praca awaryjna grupy**: można utworzyć jedną lub wiele grup trybu failover między dwoma serwerami w różnych regionach (podstawowych i pomocniczych serwerów). Każda grupa może zawierać co najmniej jednej bazy danych, które są odzyskiwane jako jednostki, w przypadku wszystkich lub niektórych głównej bazy danych jest niedostępny z powodu awarii w regionie podstawowym.  
* **Podstawowy serwer**: serwer obsługuje głównej bazy danych w grupie trybu failover.
* **Serwer pomocniczy**: serwer obsługuje pomocniczej bazy danych w grupie trybu failover. Serwer pomocniczy nie może być w tym samym regionie, jako serwer podstawowy.
* **Dodawanie baz danych do trybu failover grupy**: szereg baz danych w serwerze lub w puli elastycznej można umieścić w tej samej grupie trybu failover. Jeśli autonomiczna baza danych należy dodać do grupy, automatycznie tworzy pomocniczej bazy danych przy użyciu tej samej wersji i poziom wydajności. W przypadku podstawowej bazy danych w puli elastycznej, pomocniczy jest tworzony automatycznie w puli elastycznej o takiej samej nazwie. Po dodaniu bazy danych, która ma już pomocniczej bazy danych serwera pomocniczego, czy replikacja geograficzna jest dziedziczona przez grupę.

   > [!NOTE]
   > Dodając bazy danych, która ma już pomocniczej bazy danych na serwerze, który nie jest częścią grupy trybu failover w nowym serwerem pomocniczym jest tworzony w serwerze pomocniczym. 
   >

* **Odbiornik odczytu i zapisu grupy pracy awaryjnej**: Rekord DNS CNAME jest sformatowany jako  **&lt;nazwę grupy pracy awaryjnej&gt;. database.windows.net** wskazuje bieżący adres URL serwera podstawowego. Umożliwia aplikacji SQL odczytu i zapisu przezroczysty ponownie nawiązać połączenie z podstawowej bazy danych zmiany podstawowego po pracy awaryjnej. 
* **Odbiornik grupy trybu failover tylko do odczytu**: Rekord DNS CNAME jest sformatowany jako  **&lt;nazwę grupy pracy awaryjnej&gt;. secondary.database.windows.net** wskazującego adres URL serwera pomocniczego. Umożliwia aplikacji SQL tylko do odczytu do jawnego połączenia do pomocniczej bazy danych przy użyciu określonej reguły równoważenia obciążenia. Opcjonalnie można określić, czy ruch tylko do odczytu do można automatycznie przekierowywane do serwera podstawowego, gdy serwer pomocniczy jest niedostępny.
* **Zasady automatycznej pracy awaryjnej**: Domyślnie grupa pracy awaryjnej jest skonfigurowana przy użyciu zasad automatycznej pracy awaryjnej. System wyzwala trybu failover, jak zostaje wykryta awaria. Jeśli chcesz kontrolować przepływu pracy awaryjnej z aplikacji, można wyłączyć automatycznej pracy awaryjnej. 
* **Ręcznego przełączania trybu failover**: Inicjuj tryb failover ręcznie w dowolnym momencie, niezależnie od konfiguracji automatycznej pracy awaryjnej. Jeśli nie skonfigurowano zasad automatycznej pracy awaryjnej, ręcznego przełączania trybu failover jest potrzebny do odzyskania bazy danych w grupie trybu failover. Możesz zainicjować wymuszonym lub przyjazną trybu failover (z pełnej synchronizacji danych). Drugie można przenosić na aktywnym serwerze w regionie podstawowym. Po zakończeniu pracy awaryjnej rekordy DNS są automatycznie aktualizowane do zapewnienia łączności z właściwym serwerem.
* **Okres prolongaty utraty danych**: ponieważ podstawowych i pomocniczych baz danych są synchronizowane przy użyciu replikacji asynchronicznej, przejście w tryb failover może spowodować utratę danych. Można dostosować zasady automatycznej pracy awaryjnej, aby odzwierciedlić aplikacji tolerancję utraty danych. Konfigurując **GracePeriodWithDataLossHours**, można kontrolować, jak długo system czeka przed przejściem w tryb failover, który prawdopodobnie wynik utraty danych. 

   > [!NOTE]
   > Jeśli system wykryje, że bazy danych w grupie są nadal w trybie online (na przykład awaria tylko wpływ na płaszczyźnie kontroli usługi), natychmiast aktywuje tryb failover z pełnej synchronizacji danych (przyjazna trybu failover) niezależnie od wartości ustawionej przez **GracePeriodWithDataLossHours**. Takie zachowanie gwarantuje, że istnieje nie powoduje utraty danych podczas odzyskiwania. Okres prolongaty obowiązuje tylko wtedy, gdy jest to przyjazna trybu failover nie jest możliwe. Jeśli skuteczność została osłabiona awarii, przed upływem okresu prolongaty, przejście w tryb failover nie został aktywowany.
   >

* **Wiele grup pracy awaryjnej**: można skonfigurować wiele grup pracy awaryjnej dla tej samej pary serwerów do kontrolowania skali przechodzenia w tryb failover. Każda grupa nie powiedzie się za pośrednictwem niezależnie. Jeśli aplikacja wielodostępne używa pule elastyczne, umożliwia tej możliwości mieszać podstawowych i pomocniczych baz danych w każdej puli. Dzięki temu można zmniejszyć wpływ awarii połowę dzierżawcy.

## <a name="best-practices-of-building-highly-available-service"></a>Najlepsze rozwiązania w zakresie tworzenia usługi wysokiej dostępności

Do tworzenia wysokiej dostępności usługi, która używa bazy danych Azure SQL, należy przestrzegać następujących wytycznych:

- **Użyj grupy trybu failover**: można utworzyć jedną lub wiele grup trybu failover między dwoma serwerami w różnych regionach (podstawowych i pomocniczych serwerów). Każda grupa może zawierać co najmniej jednej bazy danych, które są odzyskiwane jako jednostki, w przypadku wszystkich lub niektórych głównej bazy danych jest niedostępny z powodu awarii w regionie podstawowym. Grupy pracy awaryjnej tworzy geograficznie pomocnicze bazy danych w tym samym celu usługi jako podstawowy. Jeśli dodasz istniejącej relacji replikacji geograficznej w grupie trybu failover, upewnij się, że dodatkowej geograficzna jest skonfigurowany z tej samej cel poziomu usługi jako podstawowy.
- **Użyj odbiornika odczytu i zapisu dla obciążenia OLTP**: podczas wykonywania operacji OLTP, użyj  **&lt;nazwę grupy pracy awaryjnej&gt;. database.windows.net** jako serwer są połączenia i adres URL automatyczne przekierowanie do serwera podstawowego. Ten adres URL nie zmienia się po pracy awaryjnej. Należy pamiętać, że tryb failover obejmuje aktualizowania rekordu DNS tak połączenia klienckie są przekierowywane do nowego podstawowego dopiero po klienta pamięci podręcznej DNS są odświeżane.
- **Użyj tylko do odczytu odbiornika dla obciążenia tylko do odczytu**: jeśli obciążenie logicznie odizolowane tylko do odczytu, które są odporne na niektórych nieaktualności danych, można użyć dodatkowej bazy danych w aplikacji. Sesje, tylko do odczytu, użyj  **&lt;nazwę grupy pracy awaryjnej&gt;. secondary.database.windows.net** jako serwer adresu URL i połączenie automatycznie zostaje skierowany do lokacji dodatkowej. Zalecane jest również, wskazanej w parametrach połączenia do odczytu próba przy użyciu **ApplicationIntent = ReadOnly**. 
- **Przygotowanie do pogorszenia się wydajności**: decyzja pracy awaryjnej programu SQL jest niezależna od pozostałej części lub innych usług używanych aplikacji. Aplikacja może mieć wartość "mixed" z niektórymi składnikami w jeden region, a niektóre w innym. Aby uniknąć degradacji, upewnij się, wdrożenie nadmiarowego aplikacji w regionie odzyskiwania po awarii i postępuj zgodnie z wytycznymi, w tym artykule. Uwaga aplikacji w regionie odzyskiwania po awarii nie trzeba użyć ciągu innego połączenia.  
- **Przygotowanie do utraty danych**: wykrycie awarii SQL automatyczne wyzwolenie pracy awaryjnej odczytu i zapisu, jeśli zerowej utraty danych do naszej informacji. W przeciwnym razie oczekiwania przez czas określony przez **GracePeriodWithDataLossHours**. Jeśli określono **GracePeriodWithDataLossHours**, można przygotować do utraty danych. Ogólnie rzecz biorąc podczas awarii, Azure objawach dostępności. Jeśli niedopuszczalna utraty danych, upewnij się ustawić **GracePeriodWithDataLossHours** wystarczająco dużą liczbę, takich jak 24 godziny. 

> [!IMPORTANT]
> Pule elastyczne 800 lub mniej Dtu i więcej niż 250 baz danych przy użyciu — replikacja geograficzna, mogą wystąpić problemy z tym już planowanej pracy w trybie Failover i pogorszenie wydajności.  Te problemy są częściej dla obciążeń intensywnie korzystających z zapisu, gdy punkty końcowe — replikacja geograficzna powszechnie są rozdzielone według lokalizacji geograficznej lub w przypadku, gdy wiele punktów końcowych pomocnicze są używane dla każdej bazy danych.  Zwiększa się wraz z upływem czasu opóźnienia replikacji geograficznej wskazano objawy te problemy.  To opóźnienie może monitorować za pomocą [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).  Jeśli te problemy występują, środki zaradcze to zwiększenie liczby jednostek Dtu puli lub zmniejszenie liczby replikacją geograficzną baz danych w tej samej puli.

## <a name="upgrading-or-downgrading-a-primary-database"></a>Uaktualnianie lub zmiany na starszą wersję podstawowej bazy danych
Można uaktualnić lub starszą wersję podstawowej bazy danych do poziomu wydajności różnych (w ramach tej samej warstwie usługi) bez odłączania wszystkie pomocnicze bazy danych. Podczas uaktualniania, zalecamy najpierw uaktualnić dodatkowej bazy danych, a następnie uaktualnić podstawowy. Podczas zmiany na starszą wersję, odwrócić kolejność: najpierw starszą wersję serwera podstawowego i starszą wersję pomocniczą. Po uaktualnieniu lub starszą wersję bazy danych do warstwy z innej usługi, to zalecenie jest wymuszana. 

> [!NOTE]
> Jeśli utworzono dodatkowej bazy danych jako część konfiguracji trybu failover grupy nie zaleca się obniżyć dodatkowej bazy danych. To upewnij się, że warstwę danych ma wystarczającej wydajności, aby przetworzyć regularne obciążenie, po aktywowaniu trybu failover. 
>

## <a name="preventing-the-loss-of-critical-data"></a>Zapobieganie utracie kluczowych danych
Ze względu na duże opóźnienia sieci rozległej ciągła kopia używa mechanizmu replikacji asynchronicznej. Asynchroniczną replikację powoduje utratę danych nieuniknione Jeśli wystąpi błąd. Jednak niektóre aplikacje mogą wymagać bez utraty danych. Aby chronić te aktualizacje krytyczne, Deweloper aplikacji może wywołać [operacja sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) procedury systemu natychmiast po zatwierdzania transakcji. Wywoływanie **operacja sp_wait_for_database_copy_sync** blokuje wątek wywołujący do momentu ostatniej zatwierdzić transakcji została przesłana do dodatkowej bazy danych. Jednak nie oczekiwania dla przekazywanych transakcji, które mają być odtwarzany i zatwierdzona na serwerze pomocniczym. **Operacja sp_wait_for_database_copy_sync** łącze określonych ciągła kopia jest zakresem. Każdy użytkownik z uprawnieniami połączenia do podstawowej bazy danych można wywołać tej procedury.

> [!NOTE]
> **Operacja sp_wait_for_database_copy_sync** zapobiega utracie danych po pracy awaryjnej, ale nie gwarantuje pełną synchronizację, aby uzyskać dostęp do odczytu. Opóźnienia spowodowane przez **operacja sp_wait_for_database_copy_sync** wywołania procedury może być znaczny i zależy od rozmiaru dziennika transakcji w momencie wywołania. 
> 

## <a name="programmatically-managing-failover-groups-and-active-geo-replication"></a>Programowe zarządzanie grupami trybu failover i aktywna replikacja geograficzna
Zgodnie z opisem wcześniej, pracy awaryjnej automatycznie grupy (w — wersja zapoznawcza) i aktywne — replikacja geograficzna może być także zarządzane programowo przy użyciu programu Azure PowerShell i interfejsu API REST. W poniższych tabelach opisano zestaw dostępnych poleceń.

**Interfejs API Menedżera zasobów Azure i zabezpieczenia oparte na rolach**: aktywna replikacja geograficzna zawiera zestaw interfejsów API usługi Azure Resource Manager do zarządzania, w tym [interfejsu API REST bazy danych SQL Azure](https://docs.microsoft.com/rest/api/sql/) i [Azure Polecenia cmdlet programu PowerShell](https://docs.microsoft.com/powershell/azure/overview). Te interfejsy API wymaga korzystania z grup zasobów i obsługuje zabezpieczeń opartych na rolach (RBAC). Aby uzyskać więcej informacji dotyczących sposobu wdrażania ról dostępu, zobacz [kontroli dostępu](../role-based-access-control/overview.md).

## <a name="manage-sql-database-failover-using-transact-sql"></a>Zarządzanie trybu failover bazy danych SQL przy użyciu języka Transact-SQL

| Polecenie | Opis |
| --- | --- |
| [ALTER DATABASE (baza danych Azure SQL)](/sql/t-sql/statements/alter-database-azure-sql-database) |Aby utworzyć pomocniczą bazę danych do istniejącej bazy danych i rozpoczyna replikację danych, użyj argumentu dodawania serwera POMOCNICZEGO w |
| [ALTER DATABASE (baza danych Azure SQL)](/sql/t-sql/statements/alter-database-azure-sql-database) |Przełączyć pomocniczej bazy danych jako głównej zainicjować trybu failover za pomocą trybu FAILOVER lub FORCE_FAILOVER_ALLOW_DATA_LOSS |
| [ALTER DATABASE (baza danych Azure SQL)](/sql/t-sql/statements/alter-database-azure-sql-database) |Użyj Usuń POMOCNICZEGO serwera na zakończenie replikacji danych między bazą danych SQL i dodatkowej określonej bazy danych. |
| [sys.geo_replication_links (Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-geo-replication-links-azure-sql-database) |Zwraca informacje o wszystkich istniejących łączy replikacji dla każdej bazy danych na serwerze logicznym bazy danych SQL Azure. |
| [sys.dm_geo_replication_link_status (Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) |Pobiera czas ostatniej replikacji, ostatni opóźnienie replikacji i inne informacje na temat łącza replikacji określonej bazy danych SQL. |
| [sys.dm_operation_status (Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) |Pokazuje stan wszystkich operacji bazy danych, w tym stan łącza replikacji. |
| [sp_wait_for_database_copy_sync (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) |powoduje, że aplikacja poczekać, aż wszystkie zatwierdzone transakcje są replikowane i potwierdzone przez aktywnej pomocniczej bazy danych. |
|  | |

## <a name="manage-sql-database-failover-using-powershell"></a>Zarządzanie trybu failover bazy danych SQL przy użyciu programu PowerShell

| Polecenie cmdlet | Opis |
| --- | --- |
| [Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase) |Pobiera co najmniej jedną bazę danych. |
| [New-AzureRmSqlDatabaseSecondary](/powershell/module/azurerm.sql/new-azurermsqldatabasesecondary) |Tworzy pomocniczą bazę danych dla istniejącej bazy danych i rozpoczyna replikację danych. |
| [Set-AzureRmSqlDatabaseSecondary](/powershell/module/azurerm.sql/set-azurermsqldatabasesecondary) |Przełącza pomocniczą bazę danych jako główną w celu zainicjowania trybu failover. |
| [Remove-AzureRmSqlDatabaseSecondary](/powershell/module/azurerm.sql/remove-azurermsqldatabasesecondary) |Przerywa replikację danych między bazą danych SQL Database i wybraną pomocniczą bazą danych. |
| [Get-AzureRmSqlDatabaseReplicationLink](/powershell/module/azurerm.sql/get-azurermsqldatabasereplicationlink) |Pobiera linki replikacji geograficznej między bazą danych Azure SQL Database i grupą zasobów lub programem SQL Server. |
| [New-AzureRmSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/set-azurermsqldatabasefailovergroup) |   To polecenie tworzy grupę trybu failover i rejestruje go na serwerach głównych i dodatkowych|
| [Remove-AzureRmSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/remove-azurermsqldatabasefailovergroup) | Usuwa grupę trybu failover z serwera i wszystkich baz danych w dodatkowej uwzględnione grupy |
| [Get-AzureRmSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/get-azurermsqldatabasefailovergroup) | Pobiera grupy konfiguracji trybu failover |
| [Set-AzureRmSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/set-azurermsqldatabasefailovergroup) |   Modyfikuje konfigurację grupy pracy awaryjnej |
| [Switch-AzureRMSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/switch-azurermsqldatabasefailovergroup) | Wyzwalacze pracy w trybie failover grupy trybu failover na serwer pomocniczy |
|  | |

> [!IMPORTANT]
> Przykładowe skrypty dla [Konfigurowanie i pracy awaryjnej pojedynczej bazy danych przy użyciu aktywna replikacja geograficzna](scripts/sql-database-setup-geodr-and-failover-database-powershell.md), [Konfigurowanie i pracy awaryjnej puli bazy danych przy użyciu aktywna replikacja geograficzna](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md), i [ Konfigurowanie i pracy awaryjnej trybu failover grupy dla pojedynczej bazy danych (wersja zapoznawcza)](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md).
>

## <a name="manage-sql-database-failover-using-the-rest-api"></a>Zarządzanie trybu failover bazy danych SQL przy użyciu interfejsu API REST
| Interfejs API | Opis |
| --- | --- |
| [Tworzenie lub aktualizacja bazy danych (createMode = przywracanie)](/rest/api/sql/Databases/CreateOrUpdate) |Tworzy, aktualizuje lub przywraca podstawowej lub pomocniczej bazy danych. |
| [GET, Utwórz lub zaktualizuj stan bazy danych](/rest/api/sql/Databases/CreateOrUpdate) |Zwraca informacje o stanie podczas operacji tworzenia. |
| [Ustaw dodatkowej bazy danych jako podstawowy (planowany tryb Failover)](/rest/api/sql/replicationlinks/failover) |Ustawia repliki bazy danych jest kluczem podstawowym przez awarii z bieżącej bazy danych repliki podstawowej. |
| [Ustaw dodatkowej bazy danych jako podstawowy (nieplanowanego trybu Failover)](/rest/api/sql/replicationlinks/failoverallowdataloss) |Ustawia repliki bazy danych jest kluczem podstawowym przez awarii z bieżącej bazy danych repliki podstawowej. Ta operacja może spowodować utratę danych. |
| [Pobierz łącza replikacji](/rest/api/sql/replicationlinks/get) |Pobiera łącze replikacji określonych określonej bazy danych SQL w partnerstwie — replikacja geograficzna. Pobiera on informacje widoczne w widoku wykazu sys.geo_replication_links. |
| [Łącza replikacji — lista przez bazę danych](/rest/api/sql/replicationlinks/listbydatabase) | Pobiera wszystkie linki replikacji określonej bazy danych SQL w partnerstwie — replikacja geograficzna. Pobiera on informacje widoczne w widoku wykazu sys.geo_replication_links. |
| [Usuń łącza replikacji](/rest/api/sql/databases/delete) | Usuwa łącze replikacji bazy danych. Nie można wykonać w trybie failover. |
| [Utwórz lub zaktualizuj grupy pracy awaryjnej](/rest/api/sql/failovergroups/createorupdate) | Tworzy lub aktualizuje grupę trybu failover |
| [Usuń grupę trybu Failover](/rest/api/sql/failovergroups/delete) | Usuwa grupę trybu failover z serwera |
| [Tryb failover (zaplanowana)](/rest/api/sql/failovergroups/failover) | Nie powiedzie się za pośrednictwem z bieżącego serwera podstawowego do tego serwera. |
| [Zezwalaj na wymuszenie trybu Failover utrata danych](/rest/api/sql/failovergroups/forcefailoverallowdataloss) |ails za pośrednictwem z bieżącego serwera podstawowego do tego serwera. Ta operacja może spowodować utratę danych. |
| [Grupy pracy awaryjnej Get](/rest/api/sql/failovergroups/get) | Pobiera grupy pracy awaryjnej. |
| [Wyświetlanie listy grup trybu Failover przez serwer](/rest/api/sql/failovergroups/listbyserver) | Wyświetla listę grup trybu failover na serwerze. |
| [Aktualizacja trybu Failover grupy](/rest/api/sql/failovergroups/update) | Aktualizuje grupy pracy awaryjnej. |
|  | |

## <a name="next-steps"></a>Kolejne kroki
* Aby uzyskać przykładowe skrypty Zobacz:
   - [Konfigurowanie pojedynczej bazy danych i wprowadzanie jej w tryb failover przy użyciu funkcji aktywnej replikacji geograficznej](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
   - [Konfigurowanie bazy danych w puli i wprowadzanie jej w tryb failover przy użyciu funkcji aktywnej replikacji geograficznej](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
   - [Konfigurowanie grupy trybu failover i wprowadzanie jej w tryb failover dla jednej bazy danych (wersja zapoznawcza)](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md)
* Omówienie ciągłości działalności biznesowej i scenariuszy, zobacz [omówienie ciągłości działalności biznesowej](sql-database-business-continuity.md)
* Aby dowiedzieć się więcej na temat usługi Azure SQL bazy danych automatycznego tworzenia kopii zapasowych, zobacz [bazy danych SQL automatycznego tworzenia kopii zapasowych](sql-database-automated-backups.md).
* Aby dowiedzieć się więcej o używaniu kopie zapasowe automatycznego odzyskiwania, zobacz [przywrócić bazę danych z kopii zapasowych inicjowane przez usługę](sql-database-recovery-using-backups.md).
* Aby poznać wymagania dotyczące uwierzytelniania dla nowym serwerem podstawowym i bazy danych, zobacz temat [zabezpieczeń bazy danych SQL po awarii](sql-database-geo-replication-security-config.md).

