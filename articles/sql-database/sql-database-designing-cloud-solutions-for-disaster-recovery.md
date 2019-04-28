---
title: Projektowania dostępnej globalnie usługi przy użyciu usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Więcej informacji na temat projektowania aplikacji dla usług o wysokiej dostępności przy użyciu usługi Azure SQL Database.
keywords: odzyskiwanie po awarii, rozwiązania odzyskiwania po awarii, kopia zapasowa danych aplikacji, replikacji geograficznej w chmurze planowania ciągłości biznesowej
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: carlrab
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 46232afcaf9504d4cfbd80160e2d7e7ea958d600
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61488188"
---
# <a name="designing-globally-available-services-using-azure-sql-database"></a>Projektowania dostępnej globalnie usługi przy użyciu usługi Azure SQL Database

Podczas kompilowania i wdrażania usług w chmurze z usługą Azure SQL Database, możesz użyć [aktywnej replikacji geograficznej](sql-database-active-geo-replication.md) lub [automatyczny tryb failover grupy](sql-database-auto-failover-group.md) aby zapewnić odporność na katastrofalnych awarii i awarii regionalnej. Tej samej funkcji umożliwia tworzenie aplikacji dystrybuowanych globalnie, zoptymalizowane pod kątem lokalnego dostępu do danych. W tym artykule omówiono typowych wzorców do zastosowań, łącznie z zalet i wad poszczególnych opcji.

> [!NOTE]
> Jeśli używasz wersji Premium lub krytyczne dla działania firmy baz danych i pul elastycznych, możesz zwiększyć ich odporne na błędy regionalnych przestojów przez konwertowania go na potrzeby konfiguracji wdrożenia nadmiarowe strefy. Zobacz [strefowo nadmiarowe bazy danych](sql-database-high-availability.md).  

## <a name="scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime"></a>Scenariusz 1: Za pomocą dwóch regionach platformy Azure w celu zachowania ciągłości przy minimalnych przestojach

W tym scenariuszu aplikacje mają następującą charakterystykę:

* Aplikacja jest aktywna w jednym regionie platformy Azure
* Wszystkie sesje bazy danych wymagają dostępu odczytu i zapisu (RW) z danymi
* Warstwy danych i sieci Web musi być zlokalizowana, aby zmniejszyć koszt opóźnienia i ruchu
* Zasadniczo przestój jest większe ryzyko biznesowe dla tych aplikacji niż utraty danych

W tym przypadku topologii wdrożenia aplikacji jest zoptymalizowane pod kątem obsługi awarii regionalnej, gdy wszystkie składniki aplikacji muszą ze sobą pracy awaryjnej. Poniższy diagram przedstawia tej topologii. W celu zapewnienia nadmiarowości geograficznej zasoby aplikacji są wdrażane w regionie, A i B. Jednak zasoby w regionie B nie są używane, aż Region, A nie powiedzie się. Grupy trybu failover skonfigurowano między dwoma regionami, aby zarządzać łączność z bazą danych, replikacji i trybu failover. Usługa sieci web w obu regionach jest skonfigurowany do dostępu do bazy danych za pośrednictwem odbiornika odczytu i zapisu  **&lt;nazwę grupy trybu failover&gt;. database.windows.net** (1). Usługa Traffic manager jest skonfigurowany do użycia [metody routingu opartego na priorytecie](../traffic-manager/traffic-manager-configure-priority-routing-method.md) (2).  

> [!NOTE]
> [Usługi Azure traffic manager](../traffic-manager/traffic-manager-overview.md) jest używany w tym artykule tylko w celach ilustracyjnych. Możesz użyć żadne rozwiązanie równoważenia obciążenia, który obsługuje metody routingu opartego na priorytecie.

Na poniższym diagramie przedstawiono tę konfigurację przed awarii:

![Scenariusz 1. Konfiguracja przed awarii.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario1-a.png)

Po awarii w regionie podstawowym usługa SQL Database wykryje, że podstawowa baza danych nie jest dostępny i wyzwala trybu failover do regionu pomocniczego, na podstawie parametrów zasad automatycznej pracy awaryjnej (1). W zależności od umowy SLA w aplikacji można skonfigurować okres prolongaty, który określa czas między wykrywanie awarii i pracy awaryjnej, sam. Jest możliwe, w tym usługi traffic manager inicjuje przełączenie w tryb failover punktu końcowego przed grupy trybu failover wyzwala trybu failover bazy danych. W takim przypadku aplikacja sieci web nie można natychmiast ponownie z bazą danych. Jednak ponowne łączenie będą automatycznie pomyślne zaraz po zakończeniu trybu failover bazy danych. Po nieudanych region przywrócona i wróci do trybu online, stary serwer podstawowy automatycznie połączy się ponownie jako nowym serwerem pomocniczym. Na poniższym diagramie przedstawiono konfigurację po pracy awaryjnej.

> [!NOTE]
> Wszystkie transakcje zatwierdzone po przełączeniu w tryb failover zostaną utracone podczas ponownego łączenia. Po zakończeniu pracy w trybie failover do aplikacji w regionie B będzie mógł ponownie połączyć i uruchom ponownie przetwarzanie żądania użytkownika. Zarówno aplikacji sieci web, jak i podstawowej bazy danych są obecnie dostępne w regionie B i pozostają w tej samej lokalizacji.

![Scenariusz 1. Konfiguracja po włączeniu trybu failover](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario1-b.png)

Jeśli wystąpi awaria w regionie B, proces replikacji pomiędzy podstawowej i pomocniczej bazy danych zostanie zawieszone, ale łącze między tymi dwoma pozostaje bez zmian (1). Ruchu zarządzane wykryje, że łączność z regionu B jest uszkodzona i czy oznacza punkt końcowy aplikacji sieci web 2 jako obniżony (2). Wydajność aplikacji nie ulega zmianie w tym przypadku, ale baza danych stanie się widoczne, i w związku z tym ugruntowanej utraty danych w przypadku regionu, A nie powiedzie się w przedziale czasu.

> [!NOTE]
> Odzyskiwania po awarii firma Microsoft zaleca konfiguracji z wdrożenia aplikacji jest ograniczona do dwóch regionach. Jest tak, ponieważ większość lokalizacjach geograficznych platformy Azure ma tylko dwa regiony. Ta konfiguracja nie chroni aplikacji przed jednoczesnych poważnej awarii w obu regionach. W razie mało prawdopodobnej awarii, można odzyskać bazy danych za pomocą trzeciego regionu [operacji przywracania geograficznego](sql-database-disaster-recovery.md#recover-using-geo-restore).
>

 Po zminimalizowaniu wpływu awarii pomocniczej bazy danych automatycznie synchronizuje się z podstawowym. Podczas synchronizacji może mieć wpływ na wydajność podstawowego. Szczególne znaczenie zależy od ilości danych nowego podstawowego uzyskanych od trybu failover. Na poniższym diagramie przedstawiono awaria w regionie pomocniczym:

![Scenariusz 1. Konfiguracja po awarii w regionie pomocniczym.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario1-c.png)

Klucz **zalety** tym wzorcu projektowym są:

* Ta sama aplikacja sieci web jest wdrażana dla obu regionów, bez żadnej konfiguracji określonego regionu i nie wymagają dodatkowej logiki, aby zarządzać trybem failover.
* Wydajność aplikacji nie ma wpływu trybu failover jako aplikacja sieci web i bazy danych są zawsze tej samej lokalizacji.

Głównym **kosztem** jest, że zasoby aplikacji w regionie B będą wykorzystane niedostatecznie przez większość czasu.

## <a name="scenario-2-azure-regions-for-business-continuity-with-maximum-data-preservation"></a>Scenariusz 2: Regiony platformy Azure dla ciągłości działania za pomocą zachowanie maksymalnej danych

Ta opcja jest najbardziej odpowiednie dla aplikacji o następującej charakterystyce:

* Utraty danych jest ryzyko biznesowe wysoki. Tryb failover bazy danych należy używać tylko w ostateczności Jeżeli przestój jest spowodowany przez poważnej awarii.
* Aplikacja obsługuje tryb tylko do odczytu i odczytu / zapisu operacji i może działać w trybie"tylko do odczytu" w okresie czasu.

W tym wzorcu aplikacja przełącza się do trybu tylko do odczytu podczas połączenia odczytu i zapisu zaczniesz błędy przekroczenia limitu czasu. Aplikacja sieci Web jest wdrożony w obu regionach i Dodaj połączenie punkt końcowy odbiornika do odczytu i zapisu oraz inne połączenie na punkt końcowy odbiornika tylko do odczytu (1). Należy użyć profilu usługi Traffic manager [routingu priorytet](../traffic-manager/traffic-manager-configure-priority-routing-method.md). [Monitorowanie punktu końcowego](../traffic-manager/traffic-manager-monitoring.md) powinna być włączona dla punktu końcowego aplikacji w każdym regionie (2).

Na poniższym diagramie przedstawiono tę konfigurację przed awarii:

![Scenariusz 2. Konfiguracja przed awarii.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario2-a.png)

Gdy usługi traffic manager wykryje błąd łączności do regionu A, przełącza się ruchu użytkowników do wystąpienia aplikacji w regionie B. W ramach tego wzorca ważne jest ustawienie z utratą danych musi upłynąć okres prolongaty wystarczająco dużą wartość, na przykład 24 godziny. Zapewnia, że dane ponosi strat, jeżeli przestój jest zmniejszany w tym czasie. Po aktywowaniu aplikacji sieci Web w regionie B operacje odczytu i zapisu może kończyć się niepowodzeniem. W tym momencie należy przełączyć do trybu tylko do odczytu (1). W tym trybie żądania są automatycznie kierowane do pomocniczej bazy danych. Jeśli awaria jest spowodowany przez poważnej awarii, prawdopodobnie jej nie da się ograniczyć okres prolongaty. Kiedy wygasa wyzwalaczy grupy trybu failover przełączenie w tryb failover. Po udostępnieniu odbiornika odczytu i zapisu, która połączenia do niego zatrzymać kończy się niepowodzeniem (2). Na poniższym diagramie przedstawiono dwa etapy procesu odzyskiwania.

> [!NOTE]
> Jeśli awaria w regionie głównym jest zmniejszany w trakcie okresu prolongaty, usługi traffic manager wykryje przywrócenia łączności region podstawowy i przełącza ruchu użytkowników do wystąpienia aplikacji w regionie A. To wystąpienie aplikacji wznawia i działa w trybie odczytu i zapisu przy użyciu podstawowej bazy danych w regionie A, jak pokazano na poprzednim diagramie.

![Scenariusz 2. Etapy odzyskiwania po awarii.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario2-b.png)

Jeśli wystąpi awaria w regionie B, traffic manager wykryje błąd punktu końcowego sieci web-app-2, w regionie B i znaczniki go negatywny wpływ na dostępność (1). W międzyczasie grupy trybu failover przełącza odbiornika tylko do odczytu do regionu A (2). Tej niedostępności nie ma wpływu na środowisko użytkownika końcowego, ale podstawowa baza danych jest dostępna podczas awarii. Na poniższym diagramie przedstawiono awaria w regionie pomocniczym:

![Scenariusz 2. Awaria w regionie pomocniczym.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario2-c.png)

Po zminimalizowaniu wpływu awarii pomocniczej bazy danych jest od razu synchronizowane z podstawowej i przełączeniu odbiornika tylko do odczytu w pomocniczej bazie danych w regionie B. Podczas synchronizacji z podstawowych może mieć nieco wpływ na wydajność w zależności od ilości danych, które muszą być zsynchronizowane.

Ten wzorzec projektowy ma kilka **zalety**:

* Takie rozwiązanie pomaga uniknąć utraty danych podczas tymczasowe awarii.
* Czas przestoju zależy od tylko jak szybko traffic manager wykryje błąd łączności, które można konfigurować.

**Kosztem** jest, że aplikacja musi umożliwiać działają w trybie tylko do odczytu.

## <a name="scenario-3-application-relocation-to-a-different-geography-without-data-loss-and-near-zero-downtime"></a>Scenariusz 3: Przeniesienie aplikacji do innej lokalizacji geograficznej, bez utraty danych i niemal przestojów

W tym scenariuszu aplikacja ma następujące cechy:

* Użytkownicy końcowi dostępu do aplikacji z różnych lokalizacji geograficznych
* Aplikacja zawiera tylko do odczytu obciążeń, które nie są zależne od pełną synchronizację z najnowszymi aktualizacjami
* Dostęp do zapisu danych powinna być obsługiwana w tej samej lokalizacji geograficznej dla większości użytkowników
* Opóźnienie odczytu jest krytyczny dla środowiska użytkownika końcowego

Aby spełnić te wymagania, należy zagwarantować, że urządzenie użytkownika **zawsze** łączy do aplikacji wdrożonych w tej samej lokalizacji geograficznej dla operacji tylko do odczytu, takich jak dane przeglądania, analiz itd. Natomiast operacji OLTP są przetwarzane w tej samej lokalizacji geograficznej **większość czasu**. Na przykład w czasie dnia operacji OLTP są przetwarzane w tej samej lokalizacji geograficznej, ale godzinach wyłączone można było przetworzyć w innej lokalizacji geograficznej. Jeśli działania użytkowników końcowych najczęściej występuje podczas godzin pracy, można zagwarantować optymalną wydajność w przypadku większości użytkowników większość czasu. Na poniższym diagramie przedstawiono tej topologii.

Zasoby aplikacji powinny być wdrażane w każdej lokalizacji geograficznej, w którym znajduje się żądanie znaczne wykorzystanie. Na przykład jeśli aplikacja jest aktywnie używana w Stanach Zjednoczonych, Unii Europejskiej i Azja południowo-wschodnia aplikacji powinny zostać wdrożone do wszystkich tych obszarów geograficznych. Podstawowej bazy danych powinny być dynamicznie przełączane z jednej lokalizacji geograficznej do następnego pod koniec godziny pracy. Ta metoda jest wywoływana, "follow słońce". Obciążenia OLTP zawsze nawiązuje połączenie z bazą danych przy użyciu odbiornika odczytu i zapisu  **&lt;nazwę grupy trybu failover&gt;. database.windows.net** (1). Obciążenie tylko do odczytu nawiązanie połączenia z lokalnej bazy danych bezpośrednio przy użyciu punktu końcowego serwera bazy danych  **&lt;nazwy serwera&gt;. database.windows.net** (2). Skonfigurowano usługi Traffic manager [metody routingu opartego na wydajności](../traffic-manager/traffic-manager-configure-performance-routing-method.md). Zapewnia, że urządzenie użytkownika końcowego jest połączony z usługą sieci web w regionie najbliższym. Usługa Traffic manager, powinny zostać skonfigurowane przy użyciu monitorowanie punktu końcowego jest włączone dla każdego punktu końcowego usługi sieci web (3).

> [!NOTE]
> Konfiguracja grupy trybu failover definiuje region, który jest używany dla trybu failover. Ponieważ nową podstawową znajduje się w innej lokalizacji geograficznej, przełączenie w tryb failover w wyniku dłuższe opóźnienie OLTP i obciążeń tylko do odczytu do momentu objęte wpływem region jest wróci do trybu online.

![Scenariusz 3. Konfiguracja z podstawowego we wschodnim regionie USA.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario3-a.png)

Na koniec dnia (na przykład o godzinie 23: 00 czasu lokalnego) aktywnych baz danych powinien być przełączane do następnego regionu (Europa Północna). To zadanie można całkowicie zautomatyzować przy użyciu [platformy Azure, usługą planowania](../scheduler/scheduler-intro.md).  Zadanie obejmuje następujące czynności:

* Przełącz serwer podstawowy w grupie trybu failover do Europa Północna, przy użyciu przyjaznej trybu failover (1)
* Usuwanie grupy trybu failover między wschodnie stany USA i Europa Północna
* Utwórz nową grupę trybu failover o takiej samej nazwie, ale między Europa Północna i Azja Wschodnia (2).
* Dodaj podstawową w regionie Europa Północna i dodatkowych w Azji Wschodniej do tej grupy trybu failover (3).

Na poniższym diagramie przedstawiono nową konfigurację po planowanego trybu failover:

![Scenariusz 3. Przejście podstawowej do Europa Północna.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario3-b.png)

Jeśli awaria występuje na przykład w regionie Europa Północna, automatycznych kopiach przełączenie w tryb failover jest inicjowane przez grupy trybu failover, co skutkuje skutecznie przenoszenie aplikacji do następnego regionu w przód od harmonogramu (1).  W takim przypadku wschodnie stany USA jest tylko pozostałe regionu pomocniczego do momentu powrotu do trybu online Europa Północna. Pozostałe dwa regiony obsługiwać klientów we wszystkich trzech różnych obszarach geograficznych, przełączając ról. Usługa Azure scheduler musi zostać odpowiednio dostosowane. Ponieważ w pozostałych regionach uzyskać ruchu dodatkowych użytkowników z Europy, aplikacja jest wpływ na wydajność nie tylko przez dodatkowe opóźnienie, ale także przez większą liczbę połączeń użytkowników końcowych. Po awarii jest zmniejszany w regionie Europa Północna, pomocniczej bazy danych jest od razu synchronizowane z bieżącego podstawowego. Na poniższym diagramie przedstawiono awaria w regionie Europa Północna:

![Scenariusz 3. Awaria w regionie Europa Północna.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario3-c.png)

> [!NOTE]
> Można zmniejszyć czas, gdy środowisko użytkownika końcowego w Europie ma obniżoną wydajność przez długi czas oczekiwania. W tym, powinny aktywnie wdrażania kopii aplikacji i tworzenie pomocnicze bazy danych w innym regionie lokalnym (Europa Zachodnia) jako zamiennika wystąpienia aplikacji w trybie offline w regionie Europa Północna. Po drugie wróci do trybu online można zdecydować, czy nadal korzystać z Europa Zachodnia lub usunąć kopię ją tam i przejdź z powrotem do przy użyciu Europa Północna.

Klucz **korzyści** w tym projekcie są:

* Obciążenie aplikacji tylko do odczytu uzyskuje dostęp do danych w regionie półek przez cały czas.
* Obciążenia odczytu i zapisu aplikacji uzyskuje dostęp do danych w regionie najbliższym w okresie najwyższy działań w każdej lokalizacji geograficznej
* Ponieważ aplikacja jest wdrażana w wielu regionach, go nie są unieważniane utraty jednego z regionów bez żadnych znaczących przestojów.

Jednak istnieją pewne **wady i zalety**:

* Powoduje awarii regionalnej w lokalizacji geograficznej, aby mieć wpływ na dłuższy czas oczekiwania. Obciążenia odczytu i zapisu i tylko do odczytu jest obsługiwany przez tę aplikację w innej lokalizacji geograficznej.
* Obciążenia tylko do odczytu, musisz połączyć do innego punktu końcowego w każdym regionie.

## <a name="business-continuity-planning-choose-an-application-design-for-cloud-disaster-recovery"></a>Planowania ciągłości biznesowej: Wybierz projekt aplikacji do odzyskiwania po awarii w chmurze

Strategię odzyskiwania po awarii w chmurze można połączyć lub rozszerzać te wzorce projektowe, aby najlepiej zaspokoić potrzeb aplikacji.  Jak wspomniano wcześniej, strategii, którą wybierzesz opiera się na umowie SLA mają być oferowane do klientów i topologii wdrażania aplikacji. Pomoc na decyzję, w poniższej tabeli porównano opcje, w zależności od celu punktu odzyskiwania (RPO) i szacowany czas odzyskiwania (ERT).

| Wzorce | Cel punktu odzyskiwania | ERT |
|:--- |:--- |:--- |
| Aktywny / pasywny wdrożenia odzyskiwania po awarii przy użyciu dostępu do tej samej lokalizacji bazy danych |Dostęp do odczytu i zapisu < 5 s |Czas wykrycia awarii i czasu wygaśnięcia DNS |
| Aktywne aktywne wdrożenie równoważenia obciążenia aplikacji |Dostęp do odczytu i zapisu < 5 s |Czas wykrycia awarii i czasu wygaśnięcia DNS |
| Aktywny / pasywny wdrożenie ochrony danych |Dostęp tylko do odczytu < 5 s | Dostęp tylko do odczytu = 0 |
||Dostęp do odczytu i zapisu = 0 | Dostęp do odczytu i zapisu = czas wykrycia awarii i okres prolongaty z utratą danych |
|||

## <a name="next-steps"></a>Kolejne kroki

* Omówienie ciągłości działania i scenariuszach można znaleźć [omówienie ciągłości działania](sql-database-business-continuity.md)
* Aby dowiedzieć się więcej na temat aktywnej replikacji geograficznej, zobacz [aktywnej replikacji geograficznej](sql-database-active-geo-replication.md).
* Aby dowiedzieć się więcej o grupach automatyczny tryb failover, zobacz [automatyczny tryb failover grupy](sql-database-auto-failover-group.md).
* Aby uzyskać informacji na temat aktywnej replikacji geograficznej z pulami elastycznymi, zobacz [Strategie odzyskiwania po awarii dla puli elastycznej](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
