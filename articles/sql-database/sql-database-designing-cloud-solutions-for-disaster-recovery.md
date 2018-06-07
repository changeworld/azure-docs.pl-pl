---
title: Projektowanie usługi wysokiej dostępności przy użyciu usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Więcej informacji na temat projektowania aplikacji dla usługi wysokiej dostępności przy użyciu bazy danych SQL Azure.
keywords: w chmurze odzyskiwania po awarii, rozwiązania w zakresie odzyskiwania po awarii, kopia zapasowa danych aplikacji, replikacja geograficzna, planowanie ciągłości biznesowej
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: conceptual
ms.date: 04/04/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: ebe6d2b4d3210ad7c02ec2d26a311645660aeab8
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34647072"
---
# <a name="designing-highly-available-services-using-azure-sql-database"></a>Projektowanie usługi wysokiej dostępności przy użyciu bazy danych SQL Azure

Podczas tworzenia i wdrażania usług wysokiej dostępności w bazie danych SQL Azure, użyj [trybu failover grupy i aktywna replikacja geograficzna](sql-database-geo-replication-overview.md) zapewnienie odporności na awarie regionalnych i poważnej awarii. Umożliwia również szybkie odzyskiwanie do dodatkowej baz danych. Ten artykuł skupia się na typowe wzorce aplikacji i omówiono korzyści i kompromisy każdej z nich. Informacje aktywna replikacja geograficzna z pule elastyczne, zobacz [strategii odzyskiwania danych w puli elastycznej](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

> [!NOTE]
> Jeśli używasz Premium lub baz danych biznesowych krytyczne (wersja zapoznawcza) i pule elastyczne, umożliwia elastyczne regionalnej awarii konwertując je do konfiguracji wdrożenia nadmiarowe strefy (obecnie w wersji zapoznawczej). Zobacz [Strefowo nadmiarowy baz danych](sql-database-high-availability.md).  

## <a name="scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime"></a>Scenariusz 1: Za pomocą dwóch regionach platformy Azure dla ciągłość prowadzenia działalności biznesowej z minimalnym czasem przestojów
W tym scenariuszu aplikacje mają następującą charakterystykę: 
*   Aplikacja jest aktywna w jednym regionie Azure
*   Wszystkie sesje bazy danych wymagają odczytu i zapisu (RW) do danych
*   Warstwa sieci Web i warstwą danych musi być zlokalizowana na zmniejszenie kosztów opóźnienia i ruchu 
*   Zasadniczo przestoju jest większe ryzyko biznesowych dla tych aplikacji niż utrata danych

W takim przypadku topologii wdrożenia aplikacji jest zoptymalizowany do obsługi regionalnej awarii, jeśli wszystkie składniki aplikacji muszą ze sobą trybu failover. Na poniższym diagramie przedstawiono tej topologii. W celu zapewnienia nadmiarowości geograficznej zasoby aplikacji są wdrażane do regionu A i B. Jednak zasoby w regionie B nie są używane, dopóki Region, A nie powiedzie się. Grupy pracy awaryjnej jest skonfigurowana między dwóch regionach, aby zarządzać łączność z bazą danych, replikacji i trybu failover. Usługa sieci web w obu regionach jest skonfigurowana do dostępu do bazy danych za pośrednictwem odbiornika odczytu i zapisu  **&lt;nazwę grupy pracy awaryjnej&gt;. database.windows.net** (1). Menedżer ruchu jest skonfigurowany do użycia [metody routingu priorytet](../traffic-manager/traffic-manager-configure-priority-routing-method.md) (2).  

> [!NOTE]
> [Menedżer ruchu Azure](../traffic-manager/traffic-manager-overview.md) jest używany w tym artykule wyłącznie dla celów ilustracyjnych. Można użyć dowolnego równoważenia obciążenia rozwiązania, które obsługuje priorytet metody routingu.    
>

Na poniższym diagramie przedstawiono tę konfigurację przed awarii:

![Scenariusz 1. Konfiguracja przed awarii.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario1-a.png)

Po awarii w regionie podstawowym usługi SQL Database wykrywa, że podstawowa baza danych nie jest dostępny i wyzwala trybu failover w regionie pomocniczym, na podstawie parametrów zasady automatycznej pracy awaryjnej (1). W zależności od Twojego umowy SLA dla aplikacji można skonfigurować okres prolongaty, kontrolujące czas między wykrywania awarii i trybu failover, do samej siebie. Istnieje możliwość, aby Menedżer ruchu inicjuje trybu failover punktu końcowego przed grupy pracy awaryjnej wyzwala trybu failover bazy danych. W takim przypadku aplikacja sieci web nie może od razu ponownie z bazą danych. Jednak ponowne łączenie będą automatycznie pomyślne zaraz po zakończeniu trybu failover bazy danych. W przypadku przywrócić i w trybie online nie powiodło się region stary serwer podstawowy automatycznie połączy się ponownie jako nowym serwerem pomocniczym. Poniższy diagram przedstawia konfigurację po pracy awaryjnej.
 
> [!NOTE]
> Wszystkie transakcje zatwierdzone po przejściu w tryb failover zostaną utracone podczas ponownego łączenia. Po zakończeniu pracy awaryjnej aplikacji w regionie B jest w stanie ponownie połączyć i uruchom ponownie przetwarzanie żądania użytkownika. Zarówno aplikacji sieci web, jak i podstawowej bazy danych są dostępne w regionie B i pozostają wspólnie. n>

![Scenariusz 1. Konfiguracja po trybu failover](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario1-b.png)

Jeśli wystąpi awaria w regionie B, proces replikacji między serwerem podstawowym i pomocniczym bazy danych pobiera wstrzymane, ale łącze między tymi dwoma pozostanie bez zmian (1). Ruch zarządzane wykryje, że łączność Region B jest uszkodzona i oznacza punkt końcowy aplikacji sieci web 2 jako obniżony (2). Wydajność aplikacji nie ma wpływu na w takim przypadku, ale bazy danych staje się widoczne, i w związku z tym w większe ryzyko utraty danych w przypadku regionu A zakończy się niepowodzeniem w przedziale czasu.

> [!NOTE]
> Do odzyskiwania po awarii zaleca się konfiguracji z wdrożeniem aplikacji jest ograniczona do dwóch regionach. Jest to spowodowane większość Azure lokalizacji geograficznych mają tylko dwóch regionach. Ta konfiguracja nie chroni przed jednoczesnych poważnej awarii obu regionów aplikacji. W przypadku mało prawdopodobnego takiej awarii, można odzyskać bazy danych za pomocą trzeci region [operacji przywracania geograficznie](sql-database-disaster-recovery.md#recover-using-geo-restore).
>

 Po awarii jest niewielkie, dodatkowej bazy danych jest automatycznie synchronizuje z serwera podstawowego. Podczas synchronizacji może mieć wpływ na wydajność serwera podstawowego. Wpływ określonych zależy od ilości danych nowego podstawowego od czasu pracy awaryjnej. Na poniższym diagramie przedstawiono awaria w regionie pomocniczym:

![Scenariusz 1. Konfiguracja po awarii w regionie pomocniczym.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario1-c.png)

Klucz **zalety** tego wzorca projektu są:

* Ta sama aplikacja sieci web jest wdrażana na obu regionów, bez żadnej konfiguracji określonego regionu i nie wymaga dodatkową logikę do zarządzania trybu failover. 
* Wydajność aplikacji nie ma wpływu na pracy awaryjnej jako aplikacji sieci web i bazy danych są zawsze wspólnie.

Głównym **zależnościami** jest, że zasoby aplikacji w regionie B są niedostatecznego wykorzystania w większości przypadków.

## <a name="scenario-2-azure-regions-for-business-continuity-with-maximum-data-preservation"></a>Scenariusz 2: Regiony platformy Azure dla ciągłość prowadzenia działalności biznesowej, zachowywania danych maksymalna
Ta opcja jest najbardziej odpowiednie dla aplikacji o następującej charakterystyce:

* Utraty danych jest biznesowych wysokiego ryzyka. Trybu failover bazy danych można tylko w ostateczności jeśli awarii jest spowodowany przez błąd krytyczny.
* Aplikacja obsługuje tylko do odczytu i zapisu i odczytu tryby operacji i może działać w trybie"tylko do odczytu" przez pewien czas.

W tym wzorcu aplikacji przełącza na tryb tylko do odczytu po rozpoczęciu połączenia odczytu i zapisu występują błędy przekroczenia limitu czasu. Aplikacja sieci Web jest wdrażana na obu regionów i obejmują połączenia z punktem końcowym odbiornika odczytu i zapisu oraz innego połączenia z punktem końcowym odbiornika tylko do odczytu (1). Należy użyć profilu Menedżera ruchu [routingu priorytet](../traffic-manager/traffic-manager-configure-priority-routing-method.md). [Punkt końcowy monitorowania](../traffic-manager/traffic-manager-monitoring.md) powinno być włączone dla punktu końcowego aplikacji w każdym regionie (2).

Na poniższym diagramie przedstawiono tę konfigurację przed awarii:

![Scenariusz 2. Konfiguracja przed awarii.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario2-a.png)

Menedżer ruchu wykrycie awarii łączności do regionu A automatycznie przełączenie ruchu użytkowników do wystąpienia aplikacji w regionie B. Ten wzorzec ważne jest ustawienie okresu prolongaty utraty danych wystarczająco dużą wartość, na przykład 24 godziny. Gwarantuje, że dane ponosi strat, jeśli w tym czasie skuteczność została osłabiona awarii. Po aktywowaniu aplikacji sieci Web w regionie B operacje odczytu i zapisu kończyć się niepowodzeniem. W tym momencie należy przełączyć do trybu tylko do odczytu (1). W tym trybie żądania automatycznie są kierowane do pomocniczej bazy danych. Jeśli awarii jest spowodowany przez poważnej awarii, najprawdopodobniej go nie może być skorygowane w trakcie okresu prolongaty. Gdy wygaśnie wyzwalaczy grupy pracy awaryjnej pracy awaryjnej. Po udostępnieniu odbiornika odczytu i zapisu i połączenia jej zatrzymania niepowodzeniem (2). Na poniższym diagramie przedstawiono dwóch etapów procesu odzyskiwania.

> [!NOTE]
> Jeśli w trakcie okresu prolongaty skuteczność została osłabiona awaria w regionie podstawowym, Menedżera ruchu wykrywa przywrócenia łączności w regionie podstawowym i zmienia ruchu użytkowników do wystąpienia aplikacji w regionie A. To wystąpienie aplikacji wznawia i działa w trybie odczytu i zapisu w regionie A, jak pokazano na poprzedni diagram za pomocą podstawowej bazy danych.
>

![Scenariusz 2. Etapy odzyskiwania po awarii.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario2-b.png)

Jeśli wystąpi awaria w regionie B, Menedżera ruchu wykrywa błąd punktu końcowego sieci web-app-2 w regionie B i znaczniki ograniczone w (1). Tymczasem grupy pracy awaryjnej zmienia tylko do odczytu odbiornika do regionu A (2). Ta przerwa nie ma wpływu na środowisko użytkownika końcowego, ale podstawowej bazy danych jest dostępna podczas awarii. Na poniższym diagramie przedstawiono awaria w regionie pomocniczym:

![Scenariusz 2. Awaria w regionie pomocniczym.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario2-c.png)

Po awarii jest niewielkie, dodatkowej bazy danych jest natychmiast synchronizowane z serwera podstawowego i przełączeniu odbiornika tylko do odczytu w pomocniczej bazie danych w regionie B. Podczas synchronizacji wydajności serwera podstawowego może dotyczyć nieco w zależności od ilości danych, które muszą być zsynchronizowane.

Ten wzorzec projektowy ma kilka **zalety**:

* Pozwala ona na uniknięcie utraty danych podczas tymczasowych przestojów.
* Czas przestoju tylko zależy szybkość Menedżera ruchu wykrywa awarii łączności, które można skonfigurować.

**Zależnościami** jest, że aplikacja musi mieć możliwość pracy w trybie tylko do odczytu.

## <a name="scenario-3-application-relocation-to-a-different-geography-without-data-loss-and-near-zero-downtime"></a>Scenariusz 3: Aplikacji przeniesienie do innej lokalizacji geograficznej, bez utraty danych i w pobliżu zerowym przestoju 
W tym scenariuszu aplikacja ma następującą charakterystykę: 
* Użytkownicy końcowi dostępu do aplikacji w różnych lokalizacjach geograficznych
* Aplikacja zawiera tylko do odczytu obciążeń, które nie są zależne od pełną synchronizację z najnowszymi aktualizacjami
* Dostęp do zapisu danych powinny być obsługiwane w tej samej lokalizacji geograficznej dla większości użytkowników 
* Opóźnienie odczytu ma kluczowe znaczenie dla środowiska użytkownika końcowego 


Aby spełnić te wymagania, należy zagwarantować, że urządzenie użytkownika **zawsze** łączy do aplikacji wdrożonych w tej samej lokalizacji geograficznej dla operacji tylko do odczytu, takich jak przeglądania danych analytics itp. Podczas gdy operacje OLTP są przetwarzane w tej samej lokalizacji geograficznej **większość czasu**. Na przykład w czasie dnia OLTP operacje są przetwarzane w tej samej lokalizacji geograficznej, ale wyłączone godzinach można było przetworzyć w innej lokalizacji geograficznej. Jeśli działanie użytkownika końcowego odbywa się głównie podczas godzin pracy, można zagwarantować optymalną wydajność dla większości użytkowników większość czasu. Na poniższym diagramie przedstawiono tej topologii. 
 
Zasoby aplikacji powinny zostać wdrożone w każdej lokalizacji geograficznej, gdzie masz żądanie znaczne obciążenie. Na przykład jeśli aplikacja jest aktywnie używany na terenie Stanów Zjednoczonych, Unii Europejskiej i południowo Azja Wschodnia aplikacji powinny zostać wdrożone na wszystkich tych lokalizacji geograficznych. Podstawowej bazy danych należy dynamicznie przełącza się z jednej lokalizacji geograficznej do następnego pod koniec godziny pracy. Ta metoda jest wywoływana "wykonaj sun". Obciążenia OLTP zawsze łączy się z bazą danych przy użyciu odbiornika odczytu i zapisu  **&lt;nazwę grupy pracy awaryjnej&gt;. database.windows.net** (1). Łączy obciążenia tylko do odczytu w lokalnej bazie danych bezpośrednio za pomocą serwera bazy danych punktu końcowego  **&lt;nazwa serwera&gt;. database.windows.net** (2). Menedżer ruchu jest skonfigurowany z [metody routingu wydajności](../traffic-manager/traffic-manager-configure-performance-routing-method.md). Gwarantuje, że użytkownik końcowy urządzenie jest połączone z usługą sieci web w najbliżej regionu. Menedżer ruchu powinny zostać skonfigurowane z punktu końcowego monitorowania włączone dla każdego punktu końcowego usługi sieci web (3).

> [!NOTE]
> Konfiguracji trybu failover grupy określa, który jest używany w trybie failover. Ponieważ nową podstawową znajduje się w innej lokalizacji geograficznej wyniki pracy awaryjnej dłużej opóźnienia zarówno OLTP, jak i obciążeń tylko do odczytu do momentu wpływ na region jest znowu w trybie online.
>

![Scenariusz 3. Konfiguracja z podstawowym w wschodnie stany USA.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario3-a.png)

Na koniec dnia (na przykład o godzinie 23: 00 czasu lokalnego) aktywnej bazy danych powinny zostać przełączone do regionu dalej (Europa Północna). To zadanie można całkowicie zautomatyzować przy użyciu [Azure usługą planowania](../scheduler/scheduler-intro.md).  Zadanie obejmuje następujące kroki:
* Przełącz serwera podstawowego do trybu failover grupy do Europa Północna, przy użyciu przyjaznej pracy awaryjnej (1)
* Usuń grupę trybu failover między wschodnie stany USA i Europa Północna
* Utwórz nową grupę trybu failover o takiej samej nazwie, ale między Europa Północna, Europa i Azja Wschodnia (2). 
* Dodaj podstawowy w Europie północnej i dodatkowych w Azja Wschodnia, do tej grupy pracy awaryjnej (3).


Na poniższym diagramie przedstawiono nowej konfiguracji po planowanego trybu failover:

![Scenariusz 3. Przechodzenie serwera podstawowego do Europa Północna, Europa.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario3-b.png)

Jeśli awaria wystąpi na przykład w Europa Północna, bazy danych automatyczne przejście w tryb failover jest inicjowane przez grupę trybu failover, co powoduje efektywne podczas przenoszenia aplikacji na następny region planowana (1).  W takim przypadku wschodnie stany USA jest tylko pozostałe regionu pomocniczego do momentu Europa Północna, Europa trybu online. Pozostałych dwóch regionach obsługiwać klientów we wszystkich trzech lokalizacji geograficznych przełączając ról. Azure harmonogramu musi być dostosowana. Ponieważ pozostałe regiony ruchu dodatkowych użytkowników z Europy, wydajność aplikacji jest wpływ na nie tylko przez dodatkowe opóźnienia, ale również przez większą liczbę połączeń użytkowników końcowych. Po awarii jest skorygowane w Europa Północna, pomocniczej bazie danych jest natychmiast synchronizowane z bieżącej podstawowej. Na poniższym diagramie przedstawiono awarii w Europa Północna, Europa:

![Scenariusz 3. Awaria w Europie północnej.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario3-c.png)

> [!NOTE]
> Można ograniczyć czas, gdy środowisko użytkownika końcowego w Europie jest ograniczone przez długi czas oczekiwania. Robić, które powinny aktywne wdrożenie kopii aplikacji i Utwórz pomocnicze bazy danych w innym regionie lokalnego (Europa) jako zastąpienie wystąpienia aplikacji w trybie offline w Europa Północna. Po drugie polecenie jest znowu w trybie online można zdecydować, czy aby nadal korzystać z Europa Zachodnia, lub usunąć kopię aplikacji i wrócić do przy użyciu Europa Północna
>

Klucz **korzyści** w tym projekcie są:
* Obciążenia aplikacji tylko do odczytu uzyskuje dostęp do danych w regionie szaf przez cały czas. 
* Obciążenia aplikacji odczytu i zapisu uzyskuje dostęp do danych w najbliżej regionu w okresie najwyższy działania w każdej lokalizacji geograficznej
* Ponieważ aplikacja jest wdrażana w wielu regionach, go pozostają aktualne po utracie regionów bez żadnego przestoju znaczące. 

Brak niektórych, ale **wady i zalety**:
* Regionalnej awarii powoduje geograficzne wpływ przez dłuższy czas oczekiwania. Obciążeń odczytu zapisu i tylko do odczytu jest obsługiwana przez tę aplikację w innej lokalizacji geograficznej. 
* Obciążeń tylko do odczytu musi połączyć się inny punkt końcowy w każdym regionie. 


## <a name="business-continuity-planning-choose-an-application-design-for-cloud-disaster-recovery"></a>Planowanie ciągłości biznesowej: Wybierz projekt aplikacji do chmury odzyskiwania po awarii
Strategię odzyskiwania po awarii określonej chmury można łączyć lub rozszerzyć te wzorce projektowe, aby najlepiej odpowiadać potrzebom aplikacji.  Jak wspomniano wcześniej, strategii wybrane opiera się na umowie SLA chcesz zaoferować klientom i topologii wdrożenia aplikacji. W celu ułatwienia decyzji w poniższej tabeli porównano opcje, w zależności od celu punktu odzyskiwania (RPO) i odzyskiwania szacowany czas (Wstaw).

| Wzorce | RPO | ERT |
|:--- |:--- |:--- |
| Aktywny / pasywny wdrożenia dla odzyskiwania po awarii z dostępem do tej samej lokalizacji bazy danych |Dostęp do odczytu zapisu < 5 s |Czas wykrywania awarii + czas wygaśnięcia DNS |
| Aktywny aktywny wdrożenia aplikacji Równoważenie obciążenia sieciowego |Dostęp do odczytu zapisu < 5 s |Czas wykrywania awarii + czas wygaśnięcia DNS |
| Aktywny / pasywny wdrożenia do przechowywania danych |Dostęp tylko do odczytu < 5 s | Dostęp tylko do odczytu = 0 |
||Dostęp do odczytu zapisu = zero | Dostęp do odczytu zapisu = czas wykrycia błędu + okres prolongaty przy utracie danych |
|||

## <a name="next-steps"></a>Kolejne kroki
* Omówienie ciągłości działalności biznesowej i scenariuszy, zobacz [omówienie ciągłości działalności biznesowej](sql-database-business-continuity.md)
* Aby dowiedzieć się więcej o grupach — replikacja geograficzna i trybu failover, zobacz [aktywna replikacja geograficzna](sql-database-geo-replication-overview.md)  
* Informacje aktywna replikacja geograficzna z pule elastyczne, zobacz [strategii odzyskiwania danych w puli elastycznej](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
