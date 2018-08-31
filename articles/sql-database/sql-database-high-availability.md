---
title: Wysoka dostępność — usługa Azure SQL Database | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o tych funkcji i możliwości wysokiej dostępności usługi Azure SQL Database
services: sql-database
author: jovanpop-msft
manager: craigg
ms.service: sql-database
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: jovanpop
ms.reviewer: carlrab, sashan
ms.openlocfilehash: f056bfa33e2a43af60357d6bf50a0b9cd8d7f254
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2018
ms.locfileid: "43286301"
---
# <a name="high-availability-and-azure-sql-database"></a>Wysoka dostępność i Azure SQL Database

Usługa Azure SQL Database to platforma jako usługa, która gwarantuje, że baza danych znajduje się i uruchomione działanie przez 99,99% czasu, nie martwiąc się o konserwację i przestoje wysoką dostępność bazy danych. Jest w pełni zarządzany proces aparatu bazy danych programu SQL Server hostowanych w chmurze platformy Azure, która zapewnia, że bazy danych programu SQL Server jest zawsze uaktualnione poprawkami bez wywierania wpływu na obciążenia. Gdy wystąpienie jest zastosowana poprawka lub w trybie Failover, przestój zwykle nie jest operacja Jeśli możesz [stosować logikę ponawiania próby](sql-database-develop-overview.md#resiliency) w swojej aplikacji. Jeśli czas wymagany do ukończenia pracy w trybie failover jest dłuższy niż 60 sekund, należy otworzyć zgłoszenie do pomocy technicznej. Usługa Azure SQL Database można szybko odzyskać nawet w najbardziej krytycznych okoliczności, zapewniając, że Twoje dane są zawsze dostępne.

Platforma Azure w pełni zarządza każdym usługi Azure SQL Database i gwarantuje bez utraty danych i wysoki odsetek dostępność danych. Azure automatycznie obsługuje stosowanie poprawek, kopii zapasowych, replikacji, wykrywanie awarii, bazowego sprzętu potencjalnych, awarii oprogramowania lub sieci, wdrażanie poprawek, pracy w trybie Failover, uaktualnienia bazy danych i innych zadań konserwacyjnych. Inżynierów programu SQL Server wdrożono najbardziej znanych sposobów postępowania, zapewniając, że wszystkie operacje konserwacji odbywa się w czasie krótszym niż 0,01% czas życia Twojej bazy danych. Ta architektura została zaprojektowana, aby upewnić się, że zatwierdzone dane nigdy nie zostaną utracone i że operacji konserwacji są wykonywane bez wywierania wpływu na obciążenia. Nie istnieją żadne okna konserwacyjne ani przestoje, wymagające należy zatrzymać obciążenia, gdy baza danych jest uaktualniony lub utrzymane. Wbudowana wysoka dostępność w usłudze Azure SQL Database gwarantuje, że ta baza danych nigdy nie będą pojedynczym punktem awarii w architekturze oprogramowania.

Usługa Azure SQL Database jest oparty na architekturę aparatu bazy danych programu SQL Server, która jest uwzględniany w środowisku chmury w celu zapewnienia dostępności 99,99%, nawet w przypadku wystąpienia awarii infrastruktury. Istnieją dwa modele architektury wysokiej dostępności, które są używane w usłudze Azure SQL Database (obydwaj zapewniające dostępność na poziomie 99,99%):
- Model Standard/ogólnego przeznaczenia, który jest oparty na oddzielenie zasobów obliczeniowych i magazynowych. Ten model architektury opiera się na wysoką dostępność i niezawodność warstwy magazynowania, ale może mieć niektóre potencjalne obniżenie wydajności podczas czynności konserwacyjnych.
- Premium/krytycznych modelu biznesowego opartego na klastrze procesy aparatu bazy danych. Ten model architektury opiera się na fakt, że jest zawsze kworum węzłów aparatu bazy danych dostępności i ma negatywny wpływ na wydajność minimalne obciążenie nawet w trakcie czynności konserwacyjnych.

Platforma Azure uaktualnia i poprawek podstawowego systemu operacyjnego, sterowników i aparatu bazy danych programu SQL Server sposób niewidoczny dla użytkownika za pomocą minimalny czas przestoju dla użytkowników końcowych. Usługa Azure SQL Database działa na najnowsza stabilna wersja aparatu bazy danych programu SQL Server i system operacyjny Windows, a większość użytkowników będzie nie należy zauważyć, że uaktualnienia są wykonywane stale.

## <a name="standardgeneral-purpose-availability"></a>Dostępność Standard/ogólnego przeznaczenia

Standardowa dostępności odnosi się do umowy SLA dostępność przez 99,99%, która jest stosowana w warstwach Basic/Standard/ogólnego przeznaczenia. Wysoka dostępność w tym modelu architektury jest osiągana przez oddzielenie warstwy obliczeniowej i magazynowej i replikację danych w warstwie magazynu.

Na poniższej ilustracji przedstawiono cztery węzły w standardowych architektury modelu z rozdzielonych warstwy obliczeń i magazynu.

![Oddzielenie zasobów obliczeniowych i magazynu](media/sql-database-managed-instance/general-purpose-service-tier.png)

W modelu standardowego dostępności istnieją dwie warstwy:

- Warstwy obliczeniowej bezstanowych, który jest uruchomiony proces sqlserver.exe i zawiera tylko błędy przejściowe i buforowanych danych (na przykład — pamięci podręcznej planu, puli buforów, pula magazynu kolumn). To bezstanowe węzła programu SQL Server jest obsługiwany przez usługi Azure Service Fabric inicjuje proces, który kontroluje kondycji węzła i wykonuje trybu failover w inne miejsce, jeśli to konieczne.
- Warstwy danych stanowych z plikami bazy danych (.mdf/.ldf), które są przechowywane w usłudze Azure Premium Storage. Usługa Azure Storage gwarantuje, że będzie bez utraty danych, dla dowolnego rekordu, który znajduje się w dowolnym pliku bazy danych. Usługa Azure Storage ma wbudowane dostępność/nadmiarowości danych gwarantuje, że każdy rekord w pliku dziennika lub strony w pliku danych zostaną zachowane nawet, jeśli wystąpiła awaria procesu programu SQL Server.

Zawsze, gdy aparat bazy danych lub system operacyjny zostanie uaktualniony, część podstawowej infrastruktury nie powiedzie się lub jeśli jakiś problem krytyczny zostanie wykryte w procesie programu Sql Server, usługi Azure Service Fabric zostanie przesunięty bezstanowe procesu programu SQL Server do innego węzła obliczeniowego o bezstanowa. Istnieje zestaw węzłów zapasowych, który oczekuje na uruchomienie nowej usługi obliczeniowe w przypadku pracy awaryjnej, aby zminimalizować czas pracy awaryjnej. W warstwie usługi Azure Storage to nie miało wpływu na dane i pliki danych/dziennika są dołączone do nowo utworzonym procesu programu SQL Server. Czas oczekiwanego trybu failover może być mierzony w sekundach. Tego procesu gwarantuje dostępność przez 99,99%, ale może mieć wpływ na niektóre wydajności na duże obciążenie, które działa ze względu na czas przejścia i fakt nowego węzła programu SQL Server, który rozpoczyna się od zimnych pamięci podręcznej.

## <a name="premiumbusiness-critical-availability"></a>Dostępność w warstwie Premium/krytyczne

Dostępność — wersja Premium jest włączona w warstwie Premium usługi Azure SQL Database i jest ona przeznaczona dla dużych obciążeń, które nie tolerują żadnego wpływu na wydajność, z powodu operacji konserwacji.

W modelu premium bazy danych Azure SQL database integruje zasobów obliczeniowych i magazynowych w pojedynczym węźle. Wysoka dostępność w tym modelu architektury jest osiągana przez funkcję replikacji obliczeniowej (proces aparatu bazy danych programu SQL Server) i pamięci masowej (SSD podłączonych lokalnie), które zostały wdrożone w 4-węzłowy [zawsze włączonych grup dostępności](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) klastra.

![Klastra węzłów aparatu bazy danych](media/sql-database-managed-instance/business-critical-service-tier.png)

Proces aparatu bazy danych programu SQL Server i podstawowych plików mdf/ldf są umieszczane w tym samym węźle, za pomocą podłączonych lokalnie magazynu SSD zapewnianie małych opóźnień do obciążenia. Wysoka dostępność jest implementowany przy użyciu standardu [zawsze włączonych grup dostępności](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). Każda baza danych działa w klastrze z węzłów bazy danych przy użyciu jednej podstawowej bazy danych, który jest dostępny dla obciążenia klientów i trzy procesy dodatkowej zawierającego kopie danych. Węzeł podstawowy stale wypychanie zmian do węzłów pomocniczych w celu zapewnienia, że dane są dostępne w replikach pomocniczych, jeśli węzeł podstawowy ulegnie awarii jakiegokolwiek powodu. Tryb failover odbywa się przez aparat bazy danych programu SQL Server — jedna replika pomocnicza staje się węzeł podstawowy i nową replikę pomocniczą jest utworzone w celu zapewnienia wystarczającej liczby węzłów w klastrze. Obciążenie jest automatycznie przekierowywane do nowego węzła podstawowego. Czas pracy awaryjnej jest mierzony w milisekundach i nowego podstawowego wystąpienia jest natychmiast gotowy do nadal obsługiwać żądań.

Ponadto klaster krytyczne dla działania firmy udostępnia wbudowanego węzła tylko do odczytu, który może służyć do uruchamiania tylko do odczytu zapytań (na przykład raporty), które nie wpływają na wydajność obciążenia głównej. 

## <a name="zone-redundant-configuration-preview"></a>Nadmiarowe konfiguracji strefy (wersja zapoznawcza)

Domyślnie repliki zestawu kworum w przypadku konfiguracji z magazynu lokalnego są tworzone w tym samym centrum danych. Wraz z wprowadzeniem [strefy dostępności platformy Azure](../availability-zones/az-overview.md), masz możliwość umieszczenia różnych replik w zestawach kworum różnych strefach dostępności w tym samym regionie. Aby wyeliminować pojedynczy punkt awarii, pierścień kontroli również występuje w wielu strefach jako trzy pierścienie bramy (GW). Routing do pierścienia daną bramę jest kontrolowana przez [usługi Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) (ATM). Ponieważ nadmiarowy konfiguracji strefy nie powoduje utworzenia nadmiarowość dodatkowa baza danych, użyj stref dostępności w warstwie Premium lub krytyczne dla działania firmy (wersja zapoznawcza) warstwy usług jest dostępna bez dodatkowych kosztów. Wybierając nadmiarowych strefy baz danych, możesz wprowadzić swoje Premium lub krytyczne dla działania firmy (wersja zapoznawcza) baz danych, odporne na znacznie większy zbiór awarii, w tym awarii krytycznego centrum danych bez wprowadzania żadnych zmian w logice aplikacji. Konfiguracja nadmiarowe strefy, można przekonwertować wszystkie istniejące bazy danych Premium lub krytyczne dla działania firmy lub pule (wersja zapoznawcza).

Strefy nadmiarowe kworum zestawie ma replik w różnych centrach danych z niektórych odległość między nimi, opóźnienie sieci zwiększone może zwiększyć czas zatwierdzenia i dlatego ma wpływ na wydajność niektórych obciążeń OLTP. Zawsze możesz wrócić do konfiguracji pojedynczej strefy, wyłączając ustawienie nadmiarowości strefy. Ten proces jest rozmiar operacji na danych i jest podobna do aktualizacji poziomu (SLO) regularne usługi. Po zakończeniu procesu bazy danych lub puli jest migrowane z nadmiarowych pierścień strefy do pierścienia jedną strefę lub na odwrót.

> [!IMPORTANT]
> Strefa nadmiarowych baz danych i pule elastyczne są obecnie obsługiwane tylko w warstwie Premium. W publicznej wersji zapoznawczej, kopie zapasowe i inspekcji rekordy są przechowywane w magazynach RA-GRS i dlatego nie można automatycznie dostępne w przypadku awarii całej strefy. 

Poniższy diagram przedstawia nadmiarowe strefy wersję architektura wysokiej dostępności:
 
![Wysoka dostępność architektury strefowo nadmiarowe](./media/sql-database-high-availability/high-availability-architecture-zone-redundant.png)

## <a name="read-scale-out"></a>Odczyt skalowalnego w poziomie
Zgodnie z opisem, Premium i krytyczne dla działania firmy (wersja zapoznawcza) usługi warstwy korzystanie z zestawów kworum i zawsze włączonej technologii wysokiej dostępności, zarówno w jednej strefie i konfiguracje nadmiarowe stref. Jedną z zalet funkcji AlwaysOn jest, że repliki są zawsze transakcyjnie spójne. Ponieważ repliki mają ten sam poziom wydajności jako podstawowy, aplikacji mogą korzystać z tej dodatkowej pojemności do obsługi obciążeń tylko do odczytu nie wymagają ponoszenia dodatkowych kosztów (odczytu skalowalnego w poziomie). Dzięki temu zapytania tylko do odczytu zostanie odizolowana od głównej obciążenia odczytu i zapisu i nie ma wpływu na jego wydajność. Przeczytaj funkcja skalowania w poziomie jest przeznaczona dla aplikacji, które obejmują logicznie oddzielone obciążeń tylko do odczytu, takich jak analiza i w związku z tym może wykorzystać atak za tej dodatkowej pojemności bez konieczności nawiązywania połączenia podstawowego. 

Funkcja odczytu skalowalnego w poziomie za pomocą określonej bazy danych, musisz jawnie aktywować go podczas tworzenia bazy danych lub później, zmieniając jego konfigurację przy użyciu programu PowerShell, wywołując [polecenia Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) lub [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) poleceń cmdlet lub za pomocą interfejsu REST API usługi Azure Resource Manager [baz danych — Utwórz lub zaktualizuj](/rest/api/sql/databases/createorupdate) metody.

Po włączeniu odczytu skalowalnego w poziomie dla bazy danych aplikacji łączących się tej bazy danych z nastąpi przekierowanie do repliki odczytu i zapisu lub tylko do odczytu replik tej bazy danych zgodnie z opisem w `ApplicationIntent` właściwości skonfigurowane w aplikacji Parametry połączenia. Instrukcje dotyczące `ApplicationIntent` właściwości, zobacz [Określanie przeznaczenia aplikacji](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent). 

Jeśli odczyt skalowalnego w poziomie jest wyłączony lub ustaw właściwość ReadScale w warstwie usługi z nieobsługiwanego, wszystkie połączenia są kierowane do repliki odczytu i zapisu, niezależnie od `ApplicationIntent` właściwości.  

## <a name="conclusion"></a>Podsumowanie
Usługa Azure SQL Database jest ściśle zintegrowana z platformą Azure i zależy od wysoce usługi Service Fabric wykrywania awarii i odzyskiwania w obiektach blob magazynu Azure do ochrony danych i strefy dostępności wyższych odporności na uszkodzenia. W tym samym czasie bazy danych Azure SQL w pełni korzysta z technologii zawsze włączonej grupy dostępności z programu SQL Server gotowym produkcie podczas replikacji i trybu failover. Kombinacja tych technologii umożliwia aplikacjom w pełni korzystać z zalet modelu mieszane pamięci masowej i obsługuje najbardziej wymagające umowy SLA. 

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [strefy dostępności platformy Azure](../availability-zones/az-overview.md)
- Dowiedz się więcej o [usługi Service Fabric](../service-fabric/service-fabric-overview.md)
- Dowiedz się więcej o [usługa Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) 
