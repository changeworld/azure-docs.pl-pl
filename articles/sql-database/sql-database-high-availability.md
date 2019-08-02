---
title: Wysoka dostępność — usługa Azure SQL Database | Microsoft Docs
description: Dowiedz się więcej o możliwościach i funkcjach wysokiej dostępności usługi Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: sashan
ms.reviewer: carlrab, sashan
ms.date: 06/10/2019
ms.openlocfilehash: 226b0c1cb11fc872cb7759e0d0e49275b9c2d9bf
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568152"
---
# <a name="high-availability-and-azure-sql-database"></a>Wysoka dostępność i Azure SQL Database

Celem architektury wysokiej dostępności w Azure SQL Database jest zagwarantowanie, że baza danych działa i działa 99,99% czasu, bez zakłócania wpływu operacji konserwacji i przestojów. Platforma Azure automatycznie obsługuje krytyczne zadania obsługi, takie jak stosowanie poprawek, kopii zapasowych, uaktualnień systemu Windows i programu SQL, a także niezaplanowanych zdarzeń, takich jak sprzęt, oprogramowanie lub awarie sieci.  Gdy bazowe wystąpienie programu SQL Server jest poprawione lub działa w trybie failover, przestoje nie jest zauważalne, jeśli w aplikacji jest stosowana [logika ponawiania](sql-database-develop-overview.md#resiliency) . Azure SQL Database można szybko odzyskać nawet w najbardziej krytycznych okolicznościach, dzięki czemu dane są zawsze dostępne.

Rozwiązanie wysokiej dostępności zostało zaprojektowane z myślą o zapewnieniu, że przekazane dane nigdy nie zostaną utracone z powodu niepowodzeń, że operacje konserwacji nie wpłyną na obciążenie, a baza danych nie będzie single point of failure w architekturze oprogramowania. Nie ma okien obsługi ani przestojów, które powinny wymagać zatrzymania obciążenia, gdy baza danych jest uaktualniana lub utrzymywana. 

Istnieją dwa modele architektoniczne wysokiej dostępności, które są używane w Azure SQL Database:

- Standardowy model dostępności oparty na rozdzieleniu zasobów obliczeniowych i magazynu.  Zależy to od wysokiej dostępności i niezawodności zdalnej warstwy magazynowania. Ta architektura ukierunkowana na aplikacje biznesowe zorientowane na budżet, które mogą tolerować spadek wydajności podczas aktywności konserwacyjnej.
- Model dostępności Premium oparty na klastrze procesów aparatu bazy danych. Opiera się to na faktach, że zawsze jest kworum dostępnych węzłów aparatu bazy danych. Ta architektura ukierunkowana na aplikacje o znaczeniu strategicznym o wysokiej wydajności operacji we/wy, wysoka liczba transakcji i gwarantuje minimalny wpływ na wydajność podczas aktywności konserwacyjnej.

Azure SQL Database jest uruchamiany w najnowszej stabilnej wersji aparatu bazy danych SQL Server i systemu operacyjnego Windows, a większość użytkowników nie zauważy, że uaktualnienia są wykonywane w sposób ciągły.

## <a name="basic-standard-and-general-purpose-service-tier-availability"></a>Dostępność warstwy usługi Basic, standard i Ogólnego przeznaczenia

Te warstwy usług wykorzystują standardową architekturę dostępności. Poniższy rysunek przedstawia cztery różne węzły z oddzielnymi warstwami obliczeniowymi i magazynowymi.

![Rozdzielenie zasobów obliczeniowych i magazynu](media/sql-database-high-availability/general-purpose-service-tier.png)

Standardowy model dostępności obejmuje dwie warstwy:

- Warstwa obliczeń bezstanowych, która `sqlserver.exe` uruchamia proces i zawiera tylko dane przejściowe i buforowane na podłączonym SSD, takie jak tempdb, model bazy danych, pamięć podręczna planu, pula buforów i pula magazynu kolumn. Ten bezstanowy węzeł jest obsługiwany przez usługę Azure `sqlserver.exe`Service Fabric, która inicjuje, steruje kondycją węzła i przeprowadza przejście w tryb failover do innego węzła w razie potrzeby.
- Warstwa danych stanowych z plikami bazy danych (. mdf/. ldf), które są przechowywane w usłudze Azure Blob Storage. Usługa Azure Blob Storage ma wbudowaną funkcję dostępności i nadmiarowości danych. Gwarantuje, że każdy rekord w pliku dziennika lub stronie w pliku danych zostanie zachowany nawet w przypadku awarii procesu SQL Server.

Za każdym razem, gdy aparat bazy danych lub system operacyjny zostanie uaktualniony lub zostanie wykryta awaria, usługa Azure Service Fabric przeniesie bezstanowy proces SQL Server do innego bezstanowego węzła obliczeniowego z wystarczającą ilością wolnego miejsca. Przeniesienie nie ma wpływ na dane w usłudze Azure Blob Storage, a pliki danych/dziennika są dołączone do nowo zainicjowanego procesu SQL Server. Ten proces gwarantuje dostępność na 99,99%, ale duże obciążenie może napotkać spadek wydajności podczas przejścia, ponieważ nowe wystąpienie SQL Server rozpoczyna się od zimnej pamięci podręcznej.

## <a name="premium-and-business-critical-service-tier-availability"></a>Dostępność warstwy usług premium i Krytyczne dla działania firmy

Warstwy usług premium i Krytyczne dla działania firmy korzystają z modelu dostępności Premium, który integruje zasoby obliczeniowe (proces SQL Server Database Engine) i magazyn (lokalnie dołączony dysk SSD) w jednym węźle. Wysoka dostępność jest osiągana przez replikowanie zarówno zasobów obliczeniowych, jak i magazynu do dodatkowych węzłów tworzących klaster z trzema czterema węzłami. 

![Klaster węzłów aparatu bazy danych](media/sql-database-high-availability/business-critical-service-tier.png)

Bazowe pliki bazy danych (. mdf/. ldf) są umieszczane w podłączonym magazynie SSD w celu zapewnienia bardzo małych opóźnień we/wy dla obciążenia. Wysoka dostępność jest implementowana przy użyciu technologii podobnego do SQL Server [zawsze włączonymi grupami dostępności](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). Klaster zawiera pojedynczą replikę podstawową (proces SQL Server), która jest dostępna do obsługi obciążeń klientów odczytu i zapisu, a także do trzech replik pomocniczych (obliczeniowych i magazynowych) zawierających kopie danych. Węzeł podstawowy ciągle przekazuje zmiany do węzłów pomocniczych w kolejności i gwarantuje, że dane są synchronizowane z co najmniej jedną repliką pomocniczą przed zatwierdzeniem każdej transakcji. Ten proces gwarantuje, że jeśli węzeł podstawowy ulegnie awarii z jakiegokolwiek powodu, zawsze jest w pełni zsynchronizowany węzeł w celu przełączenia w tryb failover. Przełączenie w tryb failover jest inicjowane przez Service Fabric platformy Azure. Gdy replika pomocnicza zostanie nowym węzłem podstawowym, zostanie utworzona inna replika pomocnicza, aby upewnić się, że klaster ma wystarczającą liczbę węzłów (zestaw kworum). Po zakończeniu pracy w trybie failover połączenia SQL są automatycznie przekierowywane do nowego węzła podstawowego.

Dodatkowo model dostępności Premium umożliwia przekierowywanie połączeń SQL tylko do odczytu do jednej z replik pomocniczych. Ta funkcja jest nazywana [skalowaniem do odczytu](sql-database-read-scale-out.md). Zapewnia 100% dodatkowej pojemności obliczeniowej bez dodatkowej opłaty za magazyn operacji tylko do odczytu, na przykład obciążenia analityczne, z repliki podstawowej.

## <a name="zone-redundant-configuration"></a>Konfiguracja nadmiarowa stref

Domyślnie klaster węzłów dla modelu dostępności Premium jest tworzony w tym samym centrum danych. Wprowadzając [strefy dostępności platformy Azure](../availability-zones/az-overview.md), SQL Database mogą umieścić różne repliki w klastrze z różnymi strefami dostępności w tym samym regionie. Aby wyeliminować single point of failure, pierścień kontrolny jest również duplikowany w wielu strefach jako trzy pierścienie bramy (GW). Routing do określonego pierścienia bramy jest kontrolowany przez [usługę Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) (ATM). Ponieważ konfiguracja nadmiarowa strefy w warstwach usług premium lub Krytyczne dla działania firmy nie powoduje utworzenia dodatkowej nadmiarowości bazy danych, możesz ją włączyć bez dodatkowych kosztów. Wybierając strefowo nadmiarową konfigurację, można sprawić, aby bazy danych Premium lub Krytyczne dla działania firmy odporne na znacznie większy zestaw błędów, w tym katastrofalne przerwy w działaniu, bez wprowadzania żadnych zmian w logice aplikacji. Istnieje również możliwość przekonwertowania wszelkich istniejących baz danych lub pul w warstwie Premium lub Krytyczne dla działania firmy na strefę nadmiarową.

Ze względu na to, że nadmiarowe bazy danych strefy mają repliki w różnych centrach, z odległości między nimi, zwiększone opóźnienie sieci może wydłużyć czas zatwierdzania i w ten sposób mieć wpływ na wydajność niektórych obciążeń OLTP. Zawsze możesz wrócić do konfiguracji pojedynczej strefy, wyłączając ustawienie nadmiarowości strefy. Ten proces jest operacją online podobną do zwykłego uaktualnienia warstwy usług. Na końcu procesu baza danych lub Pula jest migrowana ze strefy nadmiarowego pierścień do pojedynczego pierścienia strefy lub odwrotnie.

> [!IMPORTANT]
> Nadmiarowe bazy danych stref i pule elastyczne są obecnie obsługiwane tylko w warstwach usług premium i Krytyczne dla działania firmy w wybranych regionach. W przypadku korzystania z warstwy Krytyczne dla działania firmy konfiguracja nadmiarowa strefy jest dostępna tylko po wybraniu sprzętu obliczeniowego 5 rdzeń. Aby uzyskać aktualne informacje o regionach, które obsługują nadmiarowe bazy danych strefy, zobacz temat [Obsługa usług według regionów](../availability-zones/az-overview.md#services-support-by-region).  

Strefa o wysokiej dostępności nadmiarowa jest zilustrowana na poniższym diagramie:

![Strefa architektury wysokiej dostępności nadmiarowa](./media/sql-database-high-availability/zone-redundant-business-critical-service-tier.png)

## <a name="accelerated-database-recovery-adr"></a>Szybsze odzyskiwanie bazy danych (ADR)

[Szybsze odzyskiwanie bazy danych (ADR)](sql-database-accelerated-database-recovery.md) to nowa funkcja aparatu bazy danych SQL, która znacznie zwiększa dostępność bazy danych, szczególnie w przypadku długotrwałych transakcji. Reguły ADR są obecnie dostępne dla pojedynczych baz danych, pul elastycznych i Azure SQL Data Warehouse.

## <a name="conclusion"></a>Wniosek

Azure SQL Database funkcje wbudowanego rozwiązania wysokiej dostępności, które jest głęboko zintegrowane z platformą Azure. Jest ona zależna od Service Fabric do wykrywania awarii i odzyskiwania, w usłudze Azure Blob Storage na potrzeby ochrony danych, a na Strefy dostępności w celu uzyskania większej odporności na uszkodzenia. Ponadto usługa Azure SQL Database korzysta z technologii Always On Availability Group w SQL Server na potrzeby replikacji i przełączania do trybu failover. Połączenie tych technologii pozwala aplikacjom w pełni wykorzystać zalety modelu magazynu mieszanego i obsługiwać najbardziej wymaganą umowy SLA.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [strefy dostępności platformy Azure](../availability-zones/az-overview.md)
- Dowiedz się więcej o [Service Fabric](../service-fabric/service-fabric-overview.md)
- Dowiedz się więcej o [usłudze Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)
- Aby uzyskać więcej opcji dotyczących wysokiej dostępności i odzyskiwania po awarii, zobacz ciągłość działania [firmy](sql-database-business-continuity.md)
