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
ms.author: sashan
ms.reviewer: carlrab, sashan
manager: craigg
ms.date: 04/17/2019
ms.openlocfilehash: ec9f5aa8163ea9bb838b1a95ab8ad49233a72643
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2019
ms.locfileid: "59698233"
---
# <a name="high-availability-and-azure-sql-database"></a>Wysoka dostępność i Azure SQL Database

Celem architektura wysokiej dostępności w usłudze Azure SQL Database jest gwarancji, że baza danych znajduje się i uruchomione działanie przez 99,99% czasu, nie martwiąc się o wpływ operacji konserwacji i awarie. Azure automatycznie obsługuje krytyczne zadania obsługi, takie jak stosowanie poprawek, tworzenie kopii zapasowych, uaktualnienia Windows i programu SQL, a także nieplanowanych zdarzeń, takich jak podstawowe błędy sprzętu, oprogramowania lub sieci.  Gdy podstawowe wystąpienie SQL jest zastosowana poprawka lub w trybie Failover, przestój jest niezauważalne Jeśli możesz [stosować logikę ponawiania próby](sql-database-develop-overview.md#resiliency) w swojej aplikacji. Usługa Azure SQL Database można szybko odzyskać nawet w najbardziej krytycznych okoliczności, zapewniając, że Twoje dane są zawsze dostępne.

Rozwiązanie o wysokiej dostępności jest zapewnienie, zatwierdzone dane nigdy nie jest utracone z powodu błędów operacji konserwacji nie wpływają na obciążenia i bazy danych nie będzie pojedynczym punktem awarii w architekturze oprogramowania. Nie istnieją żadne okna konserwacyjne ani przestoje, wymagające należy zatrzymać obciążenia, gdy baza danych jest uaktualniony lub utrzymane. 

Istnieją dwa modele architektury wysokiej dostępności, które są używane w usłudze Azure SQL Database:

- Model standardowy dostępności, który opiera się na oddzielenie zasobów obliczeniowych i magazynowych.  Opiera się na wysoką dostępność i niezawodność warstwy magazynu zdalnego. Ta architektura jest przeznaczona dla aplikacji biznesowych z ograniczonym budżetem, które mogą tolerować zmniejszenie wydajności podczas czynności konserwacyjnych.
- Model dostępności — wersja Premium, który jest oparty na klastrze procesy aparatu bazy danych. Opiera się na fakcie, że jest zawsze kworum węzłów aparatu bazy danych dostępności. Ta architektura jest przeznaczony dla krytycznych aplikacji z wysoką wydajnością we/wy, dużo transakcji i gwarancje niewielkimi wpływu na obciążenie podczas czynności konserwacyjnych.

Usługa Azure SQL Database działa na najnowsza stabilna wersja aparatu bazy danych programu SQL Server i system operacyjny Windows, a większość użytkowników będzie nie należy zauważyć, że aktualizacje są wykonywane stale.

## <a name="basic-standard-and-general-purpose-service-tier-availability"></a>Podstawowa, standardowa i ogólnego przeznaczenia warstwy dostępność usług

Te warstwy usługi wykorzystują architekturę standardowa dostępności. Na poniższej ilustracji przedstawiono cztery różne węzły z rozdzielonych warstwy obliczeń i magazynu.

![Oddzielenie zasobów obliczeniowych i magazynu](media/sql-database-high-availability/general-purpose-service-tier.png)

Model standardowy dostępności zawiera dwie warstwy:

- Warstwy obliczeniowej bezstanowe uruchomionym `sqlserver.exe` przetwarzania i zawiera tylko błędy przejściowe i pamięci podręcznej danych na dołączone dyski SSD, takie jak bazy danych TempDB, model bazy danych, pamięci podręcznej planu, Pula buforów i kolumny magazynu puli. Ten węzeł bezstanowe jest obsługiwany przez usługi Azure Service Fabric, która inicjuje `sqlserver.exe`kontroluje kondycji węzła oraz przeprowadza trybu failover do innego węzła, jeśli to konieczne.
- Warstwy danych stanowych z plikami bazy danych (.mdf/.ldf), które są przechowywane w usłudze Azure Blob storage. Usługa Azure blob storage ma danych wbudowane, dostępność i nadmiarowość funkcji. Gwarantuje on, że każdy rekord w pliku dziennika lub strony w pliku danych zostaną zachowane, nawet, jeśli wystąpiła awaria procesu programu SQL Server.

Zawsze, gdy aparat bazy danych lub system operacyjny zostanie uaktualniony lub zostanie wykryta awaria, bezstanowe procesu programu SQL Server do innego węzła obliczeniowego o bezstanowa zostanie przesunięty w usługi Azure Service Fabric z wystarczającą pojemnością bezpłatne. Dane w usłudze Azure Blob storage nie podlega przenoszenia i pliki danych/dziennika są dołączone do nowo utworzonym procesu programu SQL Server. Ten proces zapewnia gwarancję dostępności 99,99% czasu, ale duże obciążenie może spowodować zmniejszenie wydajności podczas przejścia, ponieważ w nowym wystąpieniu programu SQL Server, który rozpoczyna się od zimnych pamięci podręcznej.

## <a name="premium-and-business-critical-service-tier-availability"></a>Dostępność warstwy Premium i krytyczne dla działania firmy usługi

Premium i krytyczne dla działania firmy używają warstw usługi modelu dostępności — wersja Premium, który integruje się zasoby obliczeniowe (proces aparatu bazy danych programu SQL Server) i pamięci masowej (SSD podłączonych lokalnie) w jednym węźle. Wysoka dostępność jest osiągana przez replikowanie Obliczanie i magazynowanie do dodatkowych węzłów Tworzenie trzy do czterech - węzła klastra. 

![Klastra węzłów aparatu bazy danych](media/sql-database-high-availability/business-critical-service-tier.png)

Podstawowe pliki bazy danych (.mdf/.ldf) są umieszczane w dołączonego magazynu SSD w celu zapewnienia bardzo małych opóźnień operacji We/Wy do obciążenia. Wysoka dostępność jest implementowany przy użyciu technologii, które są podobne do programu SQL Server [zawsze włączonych grup dostępności](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). Klaster zawiera gdzie pojedyncza replika podstawowa (procesu programu SQL Server) dostępnej dla obciążeń klientów odczytu / zapisu i maksymalnie trzech replik pomocniczych (obliczanie i magazynowanie) zawierającego kopie danych. Węzeł podstawowy stale wypychanie zmian do dodatkowych węzłów w kolejności i gwarantuje, że dane są synchronizowane co najmniej jedna replika pomocnicza, przed wykonaniem każdej transakcji. Ten proces zapewnia, że jeśli z jakiegokolwiek powodu ulegnie awarii węzła podstawowego, zawsze jest w pełni zsynchronizowane węzła do trybu failover. Przełączenie w tryb failover jest inicjowane przez usługi Azure Service Fabric. Gdy replika pomocnicza staje się nowy węzeł podstawowy, tworzony jest inna replika pomocnicza upewnij się, że klaster ma wystarczającej liczby węzłów (zestaw kworum). Po zakończeniu trybu failover połączeń z serwerem SQL są automatycznie przekierowywane do nowego węzła podstawowego.

Dodatkowa korzyść model dostępności premium obejmuje możliwość przekierowywania połączeń z serwerem SQL tylko do odczytu do jednej z replik pomocniczych. Ta funkcja jest nazywana [odczytu skalowalnego w poziomie](sql-database-read-scale-out.md). Zapewnia ona 100% dodatkowe obliczenia pojemności bez dodatkowych opłat do kolejkowego operacji tylko do odczytu, takich jak obciążeń analitycznych, z repliki podstawowej.

## <a name="zone-redundant-configuration"></a>Nadmiarowe konfiguracji strefy

Domyślnie klastra węzłów dla modelu dostępności — wersja premium jest tworzony w tym samym centrum danych. Wraz z wprowadzeniem [strefy dostępności platformy Azure](../availability-zones/az-overview.md), umieścić różnych replik bazy danych SQL w klastrze w różnych strefach dostępności w tym samym regionie. Aby wyeliminować pojedynczy punkt awarii, pierścień kontroli również występuje w wielu strefach jako trzy pierścienie bramy (GW). Routing do pierścienia daną bramę jest kontrolowana przez [usługi Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) (ATM). Ponieważ nadmiarowy konfiguracji strefy w warstwach Premium lub krytyczne dla działania firmy nie powoduje utworzenia nadmiarowości dodatkowa baza danych, możesz je włączyć, bez dodatkowych kosztów. Wybierając nadmiarowe konfiguracji strefy, umożliwia bazach danych Premium lub krytyczne dla działania firmy odporne na błędy znacznie większy zbiór awarii, w tym awarii krytycznego centrum danych bez wprowadzania żadnych zmian do logiki aplikacji. Możesz również przeprowadzić konwersję wszystkie istniejące bazy danych Premium lub krytyczne dla działania firmy lub pule nadmiarowe konfiguracji strefy.

Ponieważ nadmiarowych baz danych strefy mają replik w różnych centrach danych z niektórych odległość między nimi, opóźnienie sieci zwiększone może zwiększyć czas zatwierdzenia i dlatego ma wpływ na wydajność niektórych obciążeń OLTP. Zawsze możesz wrócić do konfiguracji pojedynczej strefy, wyłączając ustawienie nadmiarowości strefy. Ten proces jest podobny do uaktualniania warstwy usługi regularnych operacji w trybie online. Po zakończeniu procesu bazy danych lub puli jest migrowane z nadmiarowych pierścień strefy do pierścienia jedną strefę lub na odwrót.

> [!IMPORTANT]
> Strefa nadmiarowych baz danych i pule elastyczne są obecnie obsługiwane tylko w warstwach Premium i krytyczne dla działania firmy. Domyślnie kopie zapasowe i inspekcji rekordy są przechowywane w magazynach RA-GRS i dlatego nie można automatycznie dostępne w przypadku awarii całej strefy. 

Poniższy diagram przedstawia nadmiarowe strefy wersję architektura wysokiej dostępności:

![Wysoka dostępność architektury strefowo nadmiarowe](./media/sql-database-high-availability/zone-redundant-business-critical-service-tier.png)

## <a name="accelerated-database-recovery-adr"></a>Odzyskiwanie bazy danych jej jako przyspieszonej (ADR)

[Przyspieszone odzyskiwanie bazy danych (ADR)](sql-database-accelerated-database-recovery.md) nową funkcję do aparatu bazy danych SQL, która znacznie zwiększa dostępność bazy danych, szczególnie obecności długo działa transakcji. Reguły ADR jest obecnie dostępna dla pojedynczych baz danych, pul elastycznych i Azure SQL Data Warehouse.

## <a name="conclusion"></a>Podsumowanie

Usługa Azure SQL Database oferuje rozwiązania wbudowaną wysoką dostępność, który jest ściśle zintegrowana z platformą Azure. Jest on zależny, w usłudze Service Fabric do wykrywania awarii i odzyskiwania, usługi Azure Blob storage w celu ochrony danych i strefy dostępności wyższych odporności na uszkodzenia. Ponadto bazy danych Azure SQL korzysta z technologii zawsze włączonej grupy dostępności programu SQL Server podczas replikacji i trybu failover. Kombinacja tych technologii umożliwia aplikacjom pełnym wykorzystaniu zalet mieszane magazyn modelu i obsługuje najbardziej wymagające umowy SLA.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [strefy dostępności platformy Azure](../availability-zones/az-overview.md)
- Dowiedz się więcej o [usługi Service Fabric](../service-fabric/service-fabric-overview.md)
- Dowiedz się więcej o [usługa Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)
- Aby uzyskać więcej opcji wysokiej dostępności i odzyskiwania po awarii, zobacz [ciągłość prowadzenia działalności biznesowej](sql-database-business-continuity.md)
