---
title: Wysoka dostępność
description: Dowiedz się więcej o możliwościach i funkcjach wysokiej dostępności usługi Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: sashan
ms.author: sashan
ms.reviewer: carlrab, sashan
ms.date: 10/14/2019
ms.openlocfilehash: b560cee23855d1c0e8a7b3c2cb9d82c184a1ebf6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269655"
---
# <a name="high-availability-and-azure-sql-database"></a>Wysoka dostępność i baza danych SQL platformy Azure

Celem architektury wysokiej dostępności w usłudze Azure SQL Database jest zagwarantowanie, że baza danych jest uruchomiona co najmniej 99,99% czasu (Aby uzyskać więcej informacji dotyczących konkretnej umowy SLA dla różnych warstw, zapoznaj się z [SLA dla usługi Azure SQL Database](https://azure.microsoft.com/support/legal/sla/sql-database/)), nie martwiąc się o wpływ operacji konserwacji i awarii. Platforma Azure automatycznie obsługuje krytyczne zadania obsługi, takie jak instalowanie poprawek, tworzenie kopii zapasowych, uaktualnienia systemu Windows i SQL, a także nieplanowane zdarzenia, takie jak podstawowe awarie sprzętu, oprogramowania lub sieci.  Gdy podstawowe wystąpienie SQL jest załatane lub kończy się niepowodzeniem, przestoje nie jest zauważalne, jeśli stosuje się [logiki ponawiania](sql-database-develop-overview.md#resiliency) w aplikacji. Usługa Azure SQL Database może szybko odzyskać nawet w najbardziej krytycznych okolicznościach, zapewniając, że dane są zawsze dostępne.

Rozwiązanie o wysokiej dostępności ma na celu zapewnienie, że zatwierdzone dane nigdy nie zostaną utracone z powodu awarii, że operacje konserwacji nie wpływają na obciążenie i że baza danych nie będzie pojedynczym punktem awarii w architekturze oprogramowania. Nie ma żadnych okien konserwacji lub przestojów, które powinny wymagać zatrzymania obciążenia, gdy baza danych jest uaktualniany lub utrzymywany. 

Istnieją dwa modele architektury o wysokiej dostępności, które są używane w usłudze Azure SQL Database:

- Standardowy model dostępności oparty na oddzieleniu danych obliczeniowych i magazynu.  Opiera się na wysokiej dostępności i niezawodności warstwy magazynu zdalnego. Ta architektura jest przeznaczona dla aplikacji biznesowych zorientowanych na budżet, które mogą tolerować pewne obniżenie wydajności podczas działań konserwacyjnych.
- Model dostępności w wersji premium, który jest oparty na klastrze procesów aparatu bazy danych. Opiera się na tym, że zawsze istnieje kworum dostępnych węzłów aparatu bazy danych. Ta architektura jest przeznaczona dla aplikacji o znaczeniu krytycznym o wysokiej wydajności we/wy, wysokiej szybkości transakcji i gwarantuje minimalny wpływ na wydajność obciążenia podczas czynności konserwacyjnych.

Usługa Azure SQL Database działa w najnowszej stabilnej wersji aparatu bazy danych programu SQL Server i systemu operacyjnego Windows, a większość użytkowników nie zauważy, że uaktualnienia są wykonywane w sposób ciągły.

## <a name="basic-standard-and-general-purpose-service-tier-availability"></a>Dostępność warstwy usług podstawowych, standardowych i ogólnego przeznaczenia

Te warstwy usług wykorzystują standardową architekturę dostępności. Na poniższej ilustracji przedstawiono cztery różne węzły z oddzielonymi warstwami obliczeniowymi i magazynowymi.

![Oddzielenie danych obliczeniowych i pamięci masowej](media/sql-database-high-availability/general-purpose-service-tier.png)

Standardowy model dostępności obejmuje dwie warstwy:

- Bezstanowa warstwa obliczeniowa, która uruchamia `sqlservr.exe` proces i zawiera tylko dane przejściowe i buforowane, takie jak TempDB, model baz danych na dołączonym SSD i plan pamięci podręcznej, puli buforów i puli magazynu kolumn w pamięci. Ten węzeł bezstanowy jest obsługiwany przez `sqlservr.exe`usługę Azure Service Fabric, która inicjuje , kontroluje kondycję węzła i wykonuje przebłaświadki do innego węzła, jeśli to konieczne.
- Warstwa danych stanowych z plikami bazy danych (.mdf/.ldf), które są przechowywane w magazynie obiektów Blob platformy Azure. Magazyn obiektów blob platformy Azure ma wbudowaną funkcję dostępności danych i nadmiarowości. Gwarantuje, że każdy rekord w pliku dziennika lub stronie w pliku danych zostaną zachowane, nawet jeśli proces PROGRAMU SQL Server ulegnie awarii.

Za każdym razem, gdy aparat bazy danych lub system operacyjny zostanie uaktualniony lub zostanie wykryty błąd, usługa Azure Service Fabric przeniesie bezstanowy proces programu SQL Server do innego bezstanowego węzła obliczeniowego o wystarczającej pojemności. Przenoszenie nie ma wpływu na dane w magazynie obiektów Blob platformy Azure, a pliki danych/dziennika są dołączane do nowo zainicjowany proces programu SQL Server. Ten proces gwarantuje dostępność 99,99%, ale duże obciążenie może wystąpić pewne pogorszenie wydajności podczas przejścia, ponieważ nowe wystąpienie programu SQL Server rozpoczyna się od zimnej pamięci podręcznej.

## <a name="premium-and-business-critical-service-tier-availability"></a>Dostępność warstwy usług Premium i Business Critical

Warstwy usług Premium i Business Critical wykorzystują model dostępności premium, który integruje zasoby obliczeniowe (proces aparatu baz danych programu SQL Server) i magazyn (lokalnie dołączony dysk SSD) w jednym węźle. Wysoka dostępność jest osiągana przez replikowanie zarówno danych obliczeniowych, jak i magazynu do dodatkowych węzłów tworzących klaster z trzema lub czterema węzłami. 

![Klaster węzłów aparatu bazy danych](media/sql-database-high-availability/business-critical-service-tier.png)

Podstawowe pliki bazy danych (mdf/.ldf) są umieszczane w dołączonej pamięci masowej SSD w celu zapewnienia bardzo małych opóźnień we/wy obciążenia. Wysoka dostępność jest implementowana przy użyciu technologii podobnej do grup dostępności programu SQL Server [Always On Availability.](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) Klaster zawiera pojedynczą replikę podstawową (proces PROGRAMU SQL Server), która jest dostępna dla obciążeń klientów do odczytu i zapisu, oraz maksymalnie trzy repliki pomocnicze (obliczeniowe i magazynowe) zawierające kopie danych. Węzeł podstawowy stale wypycha zmiany do węzłów pomocniczych w kolejności i zapewnia, że dane są synchronizowane z co najmniej jedną repliką pomocniczą przed zatwierdzeniem każdej transakcji. Ten proces gwarantuje, że jeśli węzeł podstawowy ulegnie awarii z jakiegokolwiek powodu, zawsze jest w pełni zsynchronizowany węzeł do pracy awaryjnej. Przewijając awaryjnie jest inicjowane przez sieci szkieletowej usługi Azure. Gdy replika pomocnicza stanie się nowym węzłem podstawowym, tworzona jest inna replika pomocnicza, aby upewnić się, że klaster ma wystarczającą liczbę węzłów (zestaw kworum). Po zakończeniu pracy awaryjnej połączenia SQL są automatycznie przekierowywane do nowego węzła podstawowego.

Dodatkową korzyścią jest model dostępności w ramach premii, który umożliwia przekierowanie połączeń SQL tylko do odczytu do jednej z replik pomocniczych. Ta funkcja nosi nazwę [Odczyt skalowa w poziomie](sql-database-read-scale-out.md). Zapewnia 100% dodatkową pojemność obliczeniową bez dodatkowych opłat dla operacji tylko do odczytu od obciążenia, takich jak obciążenia analityczne, z repliki podstawowej.

## <a name="hyperscale-service-tier-availability"></a>Dostępność warstwy usług w hiperskali

Architektura warstwy usług hiperskali jest opisana w [architekturze funkcji rozproszonych](https://docs.microsoft.com/azure/sql-database/sql-database-service-tier-hyperscale#distributed-functions-architecture). 

![Architektura funkcjonalna na dużą skalę](./media/sql-database-hyperscale/hyperscale-architecture.png)

Model dostępności w hiperskali obejmuje cztery warstwy:

- Bezstanowa warstwa obliczeniowa, która uruchamia `sqlservr.exe` procesy i zawiera tylko dane przejściowe i buforowane, takie jak nieobjęwa pamięci podręcznej RBPEX, TempDB, model bazy danych itp. Ta warstwa bezstanowa zawiera podstawową replikę obliczeniową i opcjonalnie szereg pomocniczych replik obliczeniowych, które mogą służyć jako obiekty docelowe pracy awaryjnej.
- Warstwa magazynu bezstanowego utworzona przez serwery stron. Ta warstwa jest aparatem `sqlservr.exe` magazynu rozproszonego dla procesów uruchomionych na replikach obliczeniowych. Każdy serwer strony zawiera tylko dane przejściowe i buforowane, takie jak pokrycie pamięci podręcznej RBPEX na dołączonym dyskie SSD i stron danych buforowanych w pamięci. Każdy serwer strony ma sparowany serwer stron w konfiguracji aktywny-aktywny, aby zapewnić równoważenie obciążenia, nadmiarowość i wysoką dostępność.
- Warstwa magazynu dziennika transakcji stanowej utworzona przez węzeł obliczeniowy z uruchomionym procesem usługi Dziennik, strefą docelową dziennika transakcji i długoterminowym magazynem dziennika transakcji. Strefa docelowa i magazyn długoterminowy używają usługi Azure Storage, która zapewnia dostępność i [nadmiarowość](https://docs.microsoft.com/azure/storage/common/storage-redundancy) dla dziennika transakcji, zapewniając trwałość danych dla zatwierdzonych transakcji.
- Stanowa warstwa magazynu danych z plikami bazy danych (.mdf/.ndf), które są przechowywane w usłudze Azure Storage i są aktualizowane przez serwery stron. Ta warstwa korzysta z funkcji dostępności danych i [nadmiarowości](https://docs.microsoft.com/azure/storage/common/storage-redundancy) usługi Azure Storage. Gwarantuje, że każda strona w pliku danych zostaną zachowane, nawet jeśli procesy w innych warstwach architektury hiperskali ulegną awarii lub jeśli węzły obliczeniowe nie powiodą się.

Węzły obliczeniowe we wszystkich warstwach hiperskali są uruchamiane w sieci szkieletowej usługi Azure, która kontroluje kondycję każdego węzła i w razie potrzeby wykonuje tryb failover do dostępnych węzłów w dobrej kondycji.

Aby uzyskać więcej informacji na temat wysokiej dostępności w hiperskali, zobacz [Wysoka dostępność bazy danych w hiperskali](https://docs.microsoft.com/azure/sql-database/sql-database-service-tier-hyperscale#database-high-availability-in-hyperscale).

## <a name="zone-redundant-configuration"></a>Konfiguracja nadmiarowa strefy

Domyślnie klaster węzłów dla modelu dostępności w wersji premium jest tworzony w tym samym centrum danych. Wraz z wprowadzeniem [stref dostępności platformy Azure](../availability-zones/az-overview.md)baza danych SQL może umieszczać różne repliki bazy danych o znaczeniu biznesowym w różnych strefach dostępności w tym samym regionie. Aby wyeliminować pojedynczy punkt awarii, pierścień sterujący jest również duplikowany w wielu strefach jako trzy pierścienie bramy (GW). Routing do określonego pierścienia bramy jest kontrolowany przez [usługę Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) (ATM). Ponieważ konfiguracja nadmiarowa strefy w warstwach usług Premium lub Business Critical nie tworzy dodatkowej nadmiarowości bazy danych, można ją włączyć bez dodatkowych kosztów. Wybierając konfigurację nadmiarową strefy, można sprawić, że bazy danych premium lub krytyczne dla firmy będą odporne na znacznie większy zestaw awarii, w tym katastrofalne awarie centrum danych, bez żadnych zmian w logice aplikacji. Można również przekonwertować wszystkie istniejące bazy danych lub pul premium lub krytyczne dla firmy na konfigurację nadmiarową strefy.

Ponieważ strefy nadmiarowe bazy danych mają repliki w różnych centrach danych z pewną odległość między nimi, zwiększone opóźnienie sieci może zwiększyć czas zatwierdzania, a tym samym wpływ na wydajność niektórych obciążeń OLTP. Zawsze można powrócić do konfiguracji jednostrefowej, wyłączając ustawienie nadmiarowości strefy. Ten proces jest operacją online podobną do normalnego uaktualniania warstwy usług. Na końcu procesu bazy danych lub puli jest migrowana z pierścienia nadmiarowego strefy do pierścienia pojedynczej strefy lub odwrotnie.

> [!IMPORTANT]
> Nadmiarowe bazy danych stref i pule elastyczne są obecnie obsługiwane tylko w warstwach usługi Premium i Business Critical w wybranych regionach. W przypadku korzystania z warstwy Krytyczne dla firmy konfiguracja nadmiarowa strefy jest dostępna tylko wtedy, gdy wybrano sprzęt obliczeniowy Gen5. Aby uzyskać aktualne informacje o regionach obsługujących nadmiarowe bazy danych stref, zobacz [Obsługa usług według regionów](../availability-zones/az-overview.md#services-support-by-region).  
> Ta funkcja nie jest dostępna w wystąpieniu zarządzanym.

Nadmiarowa wersja architektury o wysokiej dostępności jest zilustrowana na poniższym diagramie:

![strefa architektury wysokiej dostępności nadmiarowa](./media/sql-database-high-availability/zone-redundant-business-critical-service-tier.png)

## <a name="accelerated-database-recovery-adr"></a>Przyspieszone odzyskiwanie bazy danych (ADR)

[Przyspieszone odzyskiwanie bazy danych (ADR)](sql-database-accelerated-database-recovery.md) to nowa funkcja aparatu bazy danych SQL, która znacznie poprawia dostępność bazy danych, szczególnie w obecności długotrwałych transakcji. ADR jest obecnie dostępny dla pojedynczych baz danych, pul elastycznych i usługi Azure SQL Data Warehouse.

## <a name="testing-application-fault-resiliency"></a>Testowanie odporności na uszkodzenia aplikacji

Wysoka dostępność jest podstawową częścią platformy Azure SQL Database, która działa w sposób przejrzysty dla aplikacji bazy danych. Jednak zdajemy sobie sprawę, że można przetestować, jak automatyczne operacje pracy awaryjnej zainicjowane podczas planowanych lub nieplanowanych zdarzeń wpłynie na aplikację przed wdrożeniem go w procesach produkcyjnych. Można wywołać specjalny interfejs API, aby ponownie uruchomić bazę danych lub pulę elastyczną, co z kolei wyzwoli przebóstwienie awaryjne. W przypadku strefy nadmiarowej bazy danych lub puli elastycznej wywołanie interfejsu API spowodowałoby przekierowanie połączeń klienta do nowej podstawowej w strefie dostępności różni się od strefy dostępności starego podstawowego. Tak więc oprócz testowania wpływu pracy awaryjnej na istniejące sesje bazy danych, można również sprawdzić, czy zmienia wydajność end-to-end ze względu na zmiany opóźnienia sieci. Ponieważ operacja ponownego uruchamiania jest natrętne i duża liczba z nich może podkreślić platformy, tylko jedno wywołanie trybu failover jest dozwolone co 30 minut dla każdej bazy danych lub puli elastycznej. 

Przewija się w przypadku pracy awaryjnej przy użyciu interfejsu API REST lub programu PowerShell. Aby uzyskać interfejs API REST, zobacz [od pracy awaryjnej bazy danych](https://docs.microsoft.com/rest/api/sql/databases(failover)/failover) i pracy [awaryjnej puli elastycznej](https://docs.microsoft.com/rest/api/sql/elasticpools(failover)/failover). W przypadku programu PowerShell zobacz [Invoke-AzSqlDatabaseFailover](https://docs.microsoft.com/powershell/module/az.sql/invoke-azsqldatabasefailover) i [Invoke-AzSqlElasticPoolFailover](https://docs.microsoft.com/powershell/module/az.sql/invoke-azsqlelasticpoolfailover). Wywołania interfejsu API REST mogą być również wykonane z interfejsu wiersza polecenia platformy Azure przy użyciu polecenia [az rest.](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-rest)

> [!IMPORTANT]
> Polecenie Failover nie jest obecnie dostępne w warstwie usługi Hiperskali i w przypadku wystąpienia zarządzanego.

## <a name="conclusion"></a>Podsumowanie

Usługa Azure SQL Database oferuje wbudowane rozwiązanie o wysokiej dostępności, które jest głęboko zintegrowane z platformą Azure. Jest zależna od sieci szkieletowej usług do wykrywania awarii i odzyskiwania, w magazynie obiektów Blob platformy Azure w celu ochrony danych i od stref dostępności w celu zwiększenia odporności na uszkodzenia. Ponadto baza danych SQL platformy Azure wykorzystuje technologię grupy zawsze na dostępność z programu SQL Server do replikacji i pracy awaryjnej. Połączenie tych technologii umożliwia aplikacjom pełne wykorzystanie zalet modelu pamięci mieszanej i obsługę najbardziej wymagających łat.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [strefach dostępności platformy Azure](../availability-zones/az-overview.md)
- Dowiedz się więcej o [sieci szkieletowej usług](../service-fabric/service-fabric-overview.md)
- Dowiedz się więcej o [usłudze Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)
- Aby uzyskać więcej opcji wysokiej dostępności i odzyskiwania po awarii, zobacz [Ciągłość działania](sql-database-business-continuity.md)
