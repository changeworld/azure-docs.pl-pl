---
title: Wysoka dostępność — usługa Azure SQL Database | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o tych funkcji i możliwości wysokiej dostępności usługi Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlrab, sashan
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: fd6e383c2631a47daa7bf469c5bec59959453252
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2019
ms.locfileid: "59616853"
---
# <a name="high-availability-and-azure-sql-database"></a>Wysoka dostępność i Azure SQL Database

Usługa Azure SQL Database to platforma jako usługa, która gwarantuje, że baza danych znajduje się i uruchomione działanie przez 99,99% czasu, nie martwiąc się o konserwację i przestoje wysoką dostępność bazy danych. Jest w pełni zarządzany proces aparatu bazy danych programu SQL Server hostowanych w chmurze platformy Azure, która zapewnia, że bazy danych programu SQL Server jest zawsze uaktualnione poprawkami bez wywierania wpływu na obciążenia. Gdy wystąpienie jest zastosowana poprawka lub w trybie Failover, przestój zwykle nie jest widoczne w przypadku możesz [stosować logikę ponawiania próby](sql-database-develop-overview.md#resiliency) w swojej aplikacji. Jeśli czas wymagany do ukończenia pracy w trybie failover jest dłuższy niż 60 sekund, należy otworzyć zgłoszenie do pomocy technicznej. Usługa Azure SQL Database można szybko odzyskać nawet w najbardziej krytycznych okoliczności, zapewniając, że Twoje dane są zawsze dostępne.

Platforma Azure w pełni zarządza każdym usługi Azure SQL Database i gwarantuje bez utraty danych i wysoki odsetek dostępność danych. Azure automatycznie obsługuje stosowanie poprawek, kopii zapasowych, replikacji, wykrywanie awarii, bazowego sprzętu potencjalnych, awarii oprogramowania lub sieci, wdrażanie poprawek, pracy w trybie Failover, uaktualnienia bazy danych i innych zadań konserwacyjnych. Inżynierów programu SQL Server wdrożono najbardziej znanych sposobów postępowania, zapewniając, że wszystkie operacje konserwacji odbywa się w czasie krótszym niż 0,01% czas życia Twojej bazy danych. Ta architektura została zaprojektowana, aby upewnić się, że zatwierdzone dane nigdy nie zostaną utracone i że operacji konserwacji są wykonywane bez wywierania wpływu na obciążenia. Nie istnieją żadne okna konserwacyjne ani przestoje, wymagające należy zatrzymać obciążenia, gdy baza danych jest uaktualniony lub utrzymane. Wbudowana wysoka dostępność w usłudze Azure SQL Database gwarantuje, że ta baza danych nigdy nie będą pojedynczym punktem awarii w architekturze oprogramowania.

Usługa Azure SQL Database jest oparty na architekturę aparatu bazy danych programu SQL Server, która jest uwzględniany w środowisku chmury w celu zapewnienia dostępności 99,99%, nawet w przypadku wystąpienia awarii infrastruktury. Istnieją dwa modele architektury wysokiej dostępności, które są używane w usłudze Azure SQL Database (obydwaj zapewniające dostępność na poziomie 99,99%):

- Model warstwy usługi Standard/ogólnego przeznaczenia, oparty na oddzielenie zasobów obliczeniowych i magazynowych. Ten model architektury opiera się na wysoką dostępność i niezawodność warstwy magazynowania, ale może mieć niektóre potencjalne obniżenie wydajności podczas czynności konserwacyjnych.
- Model warstwy Premium/business krytycznych usług, który jest oparty na klastrze procesy aparatu bazy danych. Ten model architektury opiera się na fakt, że jest zawsze kworum węzłów aparatu bazy danych dostępności i ma negatywny wpływ na wydajność minimalne obciążenie nawet w trakcie czynności konserwacyjnych.

Platforma Azure uaktualnia i poprawek podstawowego systemu operacyjnego, sterowników i aparatu bazy danych programu SQL Server sposób niewidoczny dla użytkownika za pomocą minimalny czas przestoju dla użytkowników końcowych. Usługa Azure SQL Database działa na najnowsza stabilna wersja aparatu bazy danych programu SQL Server i system operacyjny Windows, a większość użytkowników będzie nie należy zauważyć, że uaktualnienia są wykonywane stale.

## <a name="basic-standard-and-general-purpose-service-tier-availability"></a>Podstawowa, standardowa i ogólnego przeznaczenia warstwy dostępność usług

Standardowa dostępności odnosi się do umowy SLA dostępność przez 99,99%, która jest stosowana w następujących warstwach usługi podstawowa, standardowa i ogólnego przeznaczenia. Wysoka dostępność w tym modelu architektury jest osiągana przez oddzielenie warstwy obliczeniowej i magazynowej i replikację danych w warstwie magazynu.

Na poniższej ilustracji przedstawiono cztery węzły w standardowych architektury modelu z rozdzielonych warstwy obliczeń i magazynu.

![Oddzielenie zasobów obliczeniowych i magazynu](media/sql-database-managed-instance/general-purpose-service-tier.png)

W modelu standardowego dostępności istnieją dwie warstwy:

- Warstwy obliczeniowej bezstanowe uruchomioną `sqlserver.exe` przetwarzania i zawiera tylko błędy przejściowe i buforowanych danych (na przykład — pamięci podręcznej planu, puli buforów, pula magazynu kolumn). To bezstanowe węzła programu SQL Server jest obsługiwany przez usługi Azure Service Fabric inicjuje proces, który kontroluje kondycji węzła i wykonuje trybu failover w inne miejsce, jeśli to konieczne.
- Warstwy danych stanowych z plikami bazy danych (.mdf/.ldf), które są przechowywane w usłudze Azure Blob storage. Usługa Azure Blob storage gwarantuje, że będzie bez utraty danych, dla dowolnego rekordu, który znajduje się w dowolnym pliku bazy danych. Usługa Azure Blob storage ma wbudowane dostępność/nadmiarowości danych gwarantuje, że każdy rekord w pliku dziennika lub strony w pliku danych zostaną zachowane nawet, jeśli wystąpiła awaria procesu programu SQL Server.

Zawsze, gdy aparat bazy danych lub system operacyjny zostanie uaktualniony, część podstawowej infrastruktury nie powiedzie się lub jeśli jakiś problem krytyczny zostanie wykryte w procesie programu Sql Server, usługi Azure Service Fabric zostanie przesunięty bezstanowe procesu programu SQL Server do innego węzła obliczeniowego o bezstanowa. Istnieje zestaw węzłów zapasowych, który oczekuje na uruchomienie nowej usługi obliczeniowe w przypadku pracy awaryjnej, aby zminimalizować czas pracy awaryjnej. Nie mają wpływu na dane w usłudze Azure Blob storage, a pliki danych/dziennika są dołączone do nowo utworzonym procesu programu SQL Server. Tego procesu gwarantuje dostępność przez 99,99%, ale może mieć wpływ na niektóre wydajności na duże obciążenie, które działa ze względu na czas przejścia i fakt nowego węzła programu SQL Server, który rozpoczyna się od zimnych pamięci podręcznej.

## <a name="premium-and-business-critical-service-tier-availability"></a>Dostępność warstwy Premium i krytyczne dla działania firmy usługi

Dostępność — wersja Premium jest włączona w warstwach usług Premium i krytyczne dla działania firmy z usługi Azure SQL Database i jest ona przeznaczona dla dużych obciążeń, które nie tolerują żadnego wpływu na wydajność, z powodu operacji konserwacji.

W modelu premium bazy danych Azure SQL database integruje zasobów obliczeniowych i magazynowych w pojedynczym węźle. Wysoka dostępność w tym modelu architektury jest osiągana przez funkcję replikacji obliczeniowej (proces aparatu bazy danych programu SQL Server) i pamięci masowej (SSD podłączonych lokalnie) wdrożonych w klastrze 4-węzłowy, przy użyciu technologii podobne do programu SQL Server [zawsze dostępność Grupy](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server).

![Klastra węzłów aparatu bazy danych](media/sql-database-managed-instance/business-critical-service-tier.png)

Zarówno SQL bazy danych, proces aparatu i podstawowych plików mdf/ldf są umieszczane w tym samym węźle za pomocą podłączonych lokalnie magazynu SSD zapewnianie małych opóźnień do obciążenia. Wysoka dostępność jest implementowany przy użyciu technologii podobne do programu SQL Server [zawsze włączonych grup dostępności](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). Każda baza danych działa w klastrze z węzłów bazy danych przy użyciu jednej podstawowej bazy danych, który jest dostępny dla obciążenia klientów i trzy procesy dodatkowej zawierającego kopie danych. Węzeł podstawowy stale wypychanie zmian do węzłów pomocniczych w celu zapewnienia, że dane są dostępne w replikach pomocniczych, jeśli węzeł podstawowy ulegnie awarii jakiegokolwiek powodu. Tryb failover jest obsługiwany przez usługi Azure Service Fabric — jedna replika pomocnicza staje się węzeł podstawowy i nową replikę pomocniczą jest utworzone w celu zapewnienia wystarczającej liczby węzłów w klastrze. Obciążenie jest automatycznie przekierowywane do nowego węzła podstawowego.

Ponadto takie aplikacje zawierają wbudowany w klaster krytyczne dla działania firmy [odczytu skalowalnego w poziomie](sql-database-read-scale-out.md) funkcją, która udostępnia bezpłatnie z jest opłata w wysokości wbudowanego węzła tylko do odczytu, który może służyć do uruchamiania tylko do odczytu zapytań (na przykład raporty), które nie powinny mieć wpływ na wydajność podstawowego obciążenia.

## <a name="zone-redundant-configuration"></a>Nadmiarowe konfiguracji strefy

Domyślnie repliki zestawu kworum w przypadku konfiguracji z magazynu lokalnego są tworzone w tym samym centrum danych. Wraz z wprowadzeniem [strefy dostępności platformy Azure](../availability-zones/az-overview.md), masz możliwość umieszczenia różnych replik w zestawach kworum różnych strefach dostępności w tym samym regionie. Aby wyeliminować pojedynczy punkt awarii, pierścień kontroli również występuje w wielu strefach jako trzy pierścienie bramy (GW). Routing do pierścienia daną bramę jest kontrolowana przez [usługi Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) (ATM). Ponieważ nadmiarowy konfiguracji strefy nie powoduje utworzenia nadmiarowości dodatkowa baza danych, użycia stref dostępności w warstwach Premium lub krytyczne dla działania firmy jest dostępna bez dodatkowych kosztów. Wybierając nadmiarowych strefy baz danych, możesz wprowadzić bazach danych Premium lub krytyczne dla działania firmy odporne na błędy dużo większego zbioru awarii, w tym awarii krytycznego centrum danych bez wprowadzania żadnych zmian w logice aplikacji. Możesz również przeprowadzić konwersję wszystkie istniejące bazy danych Premium lub krytyczne dla działania firmy lub pule nadmiarowe konfiguracji strefy.

Strefy nadmiarowe kworum zestawie ma replik w różnych centrach danych z niektórych odległość między nimi, opóźnienie sieci zwiększone może zwiększyć czas zatwierdzenia i dlatego ma wpływ na wydajność niektórych obciążeń OLTP. Zawsze możesz wrócić do konfiguracji pojedynczej strefy, wyłączając ustawienie nadmiarowości strefy. Ten proces jest rozmiar operacji na danych i jest podobna do aktualizacji warstwy usług regularnych. Po zakończeniu procesu bazy danych lub puli jest migrowane z nadmiarowych pierścień strefy do pierścienia jedną strefę lub na odwrót.

> [!IMPORTANT]
> Strefa nadmiarowych baz danych i pule elastyczne są obecnie obsługiwane tylko w warstwie Premium. Domyślnie kopie zapasowe i inspekcji rekordy są przechowywane w magazynach RA-GRS i dlatego nie można automatycznie dostępne w przypadku awarii całej strefy. 

Poniższy diagram przedstawia nadmiarowe strefy wersję architektura wysokiej dostępności:

![Wysoka dostępność architektury strefowo nadmiarowe](./media/sql-database-high-availability/high-availability-architecture-zone-redundant.png)

## <a name="accelerated-database-recovery-adr"></a>Odzyskiwanie bazy danych jej jako przyspieszonej (ADR)

[Przyspieszone odzyskiwanie bazy danych (ADR)](sql-database-accelerated-database-recovery.md) nową funkcję do aparatu bazy danych SQL, która znacznie zwiększa dostępność bazy danych, szczególnie obecności długo działa transakcji, przeprojektowanie proces odzyskiwania aparatu bazy danych SQL. Reguły ADR jest obecnie dostępna dla pojedynczych baz danych, pul elastycznych i Azure SQL Data Warehouse.

## <a name="conclusion"></a>Podsumowanie

Usługa Azure SQL Database jest ściśle zintegrowana z platformą Azure i zależy od wysoce usługi Service Fabric wykrywania awarii i odzyskiwania w usłudze Azure Blob storage w celu ochrony danych i strefy dostępności wyższych odporności na uszkodzenia. W tym samym czasie bazy danych Azure SQL w pełni korzysta z technologii zawsze włączonej grupy dostępności z programu SQL Server gotowym produkcie podczas replikacji i trybu failover. Kombinacja tych technologii umożliwia aplikacjom w pełni korzystać z zalet modelu mieszane pamięci masowej i obsługuje najbardziej wymagające umowy SLA.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [strefy dostępności platformy Azure](../availability-zones/az-overview.md)
- Dowiedz się więcej o [usługi Service Fabric](../service-fabric/service-fabric-overview.md)
- Dowiedz się więcej o [usługa Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)
- Aby uzyskać więcej opcji wysokiej dostępności i odzyskiwania po awarii, zobacz [ciągłość prowadzenia działalności biznesowej](sql-database-business-continuity.md)
