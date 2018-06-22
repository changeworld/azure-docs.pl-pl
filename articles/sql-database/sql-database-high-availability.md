---
title: Wysoka dostępność — usługa Azure SQL Database | Dokumentacja firmy Microsoft
description: Więcej informacji na temat możliwości wysokiej dostępności usługi baza danych SQL Azure i funkcje
services: sql-database
author: jovanpop-msft
manager: craigg
ms.service: sql-database
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: jovanpop
ms.reviewer: carlrab, sashan
ms.openlocfilehash: 4e1963e97a7458db8badb63e28dbc3d215ad88b2
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309634"
---
# <a name="high-availability-and-azure-sql-database"></a>Baza danych SQL wysokiej dostępności i platformy Azure

Baza danych SQL Azure jest wysokiej dostępności bazy danych platforma jako usługa, która gwarantuje, że baza danych działa i uruchomione 99,99% czasu, nie martwiąc się o konserwacji i awariami. To jest procesem aparatu bazy danych programu SQL Server pełni zarządzana hostowanych w chmurze Azure, która zapewnia, że bazy danych SQL Server jest zawsze uaktualnione poprawiono bez wpływu na obciążenia. Szybkie przywrócenie bazy danych SQL Azure nawet w sytuacjach najważniejszych zapewnienie, że dane są zawsze dostępne.

Platformy Azure w pełni zarządza co baza danych SQL Azure i gwarantuje bez utraty danych i procent wysoką dostępność danych. Azure automatycznie obsługuje stosowanie poprawek, kopie zapasowe, replikacji, wykrywanie błędów, używany sprzęt potencjalnych, awarii oprogramowania lub sieci, wdrażanie poprawki, pracy w trybie Failover, uaktualnienia bazy danych i inne zadania konserwacji. Inżynierów programu SQL Server wdrożono najpopularniejszych rozwiązania, zapewniając zakończenie wszystkich operacji konserwacji w czasie krótszym niż 0,01% czasu życia Twojej bazy danych. Taka architektura jest przeznaczona do upewnij się, że przekazane dane nigdy nie zostaną utracone i wykonanie operacji konserwacji bez wywierania wpływu na obciążenia. Nie ma żadnych okien obsługi lub awariami wymagające należy zatrzymać obciążenie bazy danych jest uaktualniony lub aktualizować. Wbudowaną wysoką dostępność w bazie danych SQL Azure gwarantuje, że tej bazy danych nigdy nie będą pojedynczego punktu awarii w architekturę oprogramowania.

Istnieją dwa modele wysokiej dostępności w Azure SQL:

- Model Standard/ogólnego przeznaczenia, który udostępnia 99,99% dostępności, ale niektóre potencjalne obniżenie wydajności podczas obsługi aktywności.
- Premium/biznesowe krytyczne modelu, który zawiera także dostępności 99,99% z minimalnym wpływu na obciążenie nawet podczas działania obsługi.

Azure uaktualniania i niewidocznie poprawek podstawowego systemu operacyjnego, sterowników i aparatu bazy danych programu SQL Server z minimalny czas przestoju dla użytkowników końcowych. Baza danych SQL Azure działa na najnowszą wersję aparatu bazy danych programu SQL Server i systemu operacyjnego Windows, a większość użytkowników może nie zostać zauważony stale wykonanie uaktualnień.

## <a name="standard-availability"></a>Standardowa dostępności

Standardowa dostępności odwołuje się do umowy SLA 99,99%, stosowany w celu Standard/Basic/ogólne warstw. Dostępność jest to osiągane przez rozdzielenie warstwy obliczeniowej i pamięci masowej. W modelu standardowe dostępności mamy dwie warstwy:

- Warstwy obliczeniowej bezstanowych, który jest uruchomiony proces sqlserver.exe i zawiera tylko przejściowych i pamięci podręcznej danych (na przykład — pamięci podręcznej planu, puli buforów, puli magazynu kolumn). Ta bezstanowych węzła programu SQL Server jest świadczona przez sieć szkieletowa usług Azure inicjuje proces, formanty kondycji węzła i wykonuje pracy awaryjnej w inne miejsce, jeśli to konieczne.
- Warstwa danych stanowych z plikami bazy danych (.mdf/.ldf), które są przechowywane w dysków magazynu Azure Premium. Usługa Azure Storage gwarantuje, że nie będzie istnieć bez utraty danych dowolnego rekordu, który znajduje się w dowolnym pliku bazy danych. Magazyn Azure ma wbudowane dostępności/nadmiarowość danych, który zapewnia, że każdy rekord w pliku dziennika lub strony w pliku danych zostanie zachowana nawet jeśli wystąpiła awaria procesu programu SQL Server.

Zawsze, gdy aparat bazy danych lub system operacyjny zostanie uaktualniony lub w przypadku wykrycia niektórych krytyczny problem w procesie programu Sql Server, sieć szkieletowa usług Azure zostanie Przenieś bezstanowych procesu programu SQL Server do innego węzła obliczeń bezstanowych. Nie wpływa na dane w warstwie usługi Azure Storage, a pliki danych/dziennika są dołączone do nowo utworzonym procesu programu SQL Server. Czas oczekiwanego trybu failover może być mierzony w sekundach. Ten proces gwarantuje dostępność 99,99%, ale może mieć pewne wpływu wydajności mocno obciążone korzystającymi z powodu czas przejścia i fakt nowego węzła programu SQL Server, który rozpoczyna się od zimnych pamięci podręcznej.

## <a name="premium-availability"></a>Dostępność — wersja Premium

Dostępność — wersja Premium jest włączona w warstwie Premium bazy danych SQL Azure i jest on przeznaczony dla dużych obciążeń, które nie tolerują wpływu wydajności z powodu trwającej obsługi operacji.

W modelu premium obliczeniowej i pamięci masowej w pojedynczym węźle integruje się bazy danych Azure SQL. Proces aparatu bazy danych programu SQL Server i bazowy plików mdf/ldf są umieszczane w tym samym węźle z lokalnie podłączonego magazynu SSD, zapewniając małe opóźnienia, aby obciążenie.

Wysoka dostępność jest zaimplementowany przy użyciu standardu [zawsze włączonych grup dostępności](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). Co baza danych działa w klastrze z węzłów bazy danych z jednej podstawowej bazy danych, który jest dostępny dla obciążeń klientów i kilka procesów dodatkowej zawierających kopie danych. Węzeł podstawowy stale wypycha zmiany do dodatkowej węzłów celu upewnij się, czy dane są dostępne w replikach pomocniczych, jeśli węzeł podstawowy ulegnie awarii różnych przyczyn. Tryb failover jest obsługiwany przez aparat bazy danych programu SQL Server — jedna replika pomocnicza staje się węzeł podstawowy i nową replikę pomocniczą jest utworzone w celu zapewnienia wystarczającej liczby węzłów w klastrze. Obciążenie jest automatycznie przekierowywane do nowego węzła podstawowego. Czas pracy awaryjnej jest mierzony w milisekundach i nowego podstawowego wystąpienia jest od razu zacząć nadal obsługiwać żądań.

## <a name="zone-redundant-configuration-preview"></a>Nadmiarowe konfiguracji strefy (wersja zapoznawcza)

Domyślnie replik zestawu kworum w przypadku konfiguracji z magazynu lokalnego zostaną utworzone w tym samym centrum danych. Wraz z wprowadzeniem [stref dostępności Azure](../availability-zones/az-overview.md), masz możliwość umieszczenia innej repliki w zestawach kworum dostępności różnych stref w tym samym regionie. Aby wyeliminować pojedynczy punkt awarii, pierścień kontroli również jest zduplikowany w wielu strefach jako trzy pierścienie bramy (GW). Routing do pierścień bramy jest kontrolowany przez [usługi Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) (ATM). Ponieważ konfiguracji nadmiarowe strefy nie tworzy nadmiarowość dodatkowej bazy danych, użyj stref dostępności w warstwie Premium lub warstwy usługi biznesowe krytyczne (wersja zapoznawcza) jest dostępna bez dodatkowych kosztów. Wybierając nadmiarowa strefy baza danych, użytkownik może określić użytkownika — wersja Premium lub biznesowe krytyczne (wersja zapoznawcza) w bazach danych odporność na znacznie większy zestaw awarii, w tym datacenter poważnej awarii, bez wprowadzania żadnych zmian logiki aplikacji. Możesz również przeprowadzić konwersję żadnych istniejących baz danych Premium lub biznesowe krytyczne lub pule (wersja zapoznawcza) konfiguracji nadmiarowe strefy.

Ponieważ strefy nadmiarowe kworum zestawie replik w różnych centrach danych, z niektórych odległość między nimi, opóźnienie sieci zwiększona może zwiększyć czas zatwierdzenia i w związku z tym wpływ na wydajność niektórych obciążeń OLTP. Możesz zawsze wrócić do konfiguracji strefy jednym wyłączenie ustawienia nadmiarowość strefy. Ten proces jest rozmiar operacji danych i jest podobny do aktualizacji (SLO), cel poziomu usługi regularne. Po zakończeniu procesu bazy danych lub puli migracji z nadmiarowych pierścień strefy pierścień jednej strefie lub odwrotnie.

> [!IMPORTANT]
> Strefy nadmiarowe baz danych i elastyczne pule są obecnie obsługiwane tylko w warstwie usług Premium. W publicznej wersji zapoznawczej, kopie zapasowe i inspekcji rekordy są przechowywane w magazynie RA-GRS i dlatego nie można automatycznie dostępne w przypadku awarii całej strefy. 

W poniższym diagramie przedstawiono wersja nadmiarowe strefy architektury wysokiej dostępności:
 
![Wysoka dostępność architektura obszar strefowo nadmiarowy](./media/sql-database-high-availability/high-availability-architecture-zone-redundant.png)

## <a name="read-scale-out"></a>Przeczytaj skalowalnego w poziomie
Zgodnie z opisem, krytycznych Business (wersja zapoznawcza) i Premium usługi warstw korzystaj z zestawów kworum i zawsze na technologii wysokiej dostępności, zarówno w jednej strefie i nadmiarowego konfiguracji strefy. Jedną z zalet AlwasyON jest repliki są zawsze w stanie spójna transakcyjnie. Ponieważ replik mają ten sam poziom wydajności jako podstawowy, aplikację można korzystać z dodatkowych wydajność obsługi obciążenia tylko do odczytu bez dodatkowych kosztów (odczytu skalowalnych w poziomie). W ten sposób zapytania tylko do odczytu zostanie odizolowana od głównego obciążenia zapisu i odczytu i nie będzie miało wpływ na wydajność. Odczytu, które funkcja skalowania w poziomie jest przeznaczona dla aplikacji, które obejmują logicznie rozdzielane obciążeń tylko do odczytu, takich jak analizy, a w związku z tym można wykorzystać to dodatkowej pojemności bez nawiązywania połączenia z serwerem podstawowym. 

Do korzystania z funkcji odczytu skalowalnego w poziomie z określoną bazę danych, musisz jawnie aktywować go podczas tworzenia bazy danych lub później, zmieniając konfigurację przy użyciu programu PowerShell, wywołując [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) lub [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) poleceń cmdlet lub przy użyciu interfejsu API usługi Azure Resource Manager REST [baz danych — Tworzenie lub aktualizowanie](/rest/api/sql/databases/createorupdate) metody.

Po odczytu skalowalnego w poziomie jest włączona dla bazy danych, aplikacji nawiązywania połączenia z bazą danych zostanie skierowany do repliki do odczytu / zapisu lub tylko do odczytu repliki tej bazy danych zgodnie z `ApplicationIntent` właściwości skonfigurowane w aplikacji Parametry połączenia. Aby uzyskać informacje dotyczące `ApplicationIntent` właściwości, zobacz [określenia przeznaczenia aplikacji](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent). 

Jeśli odczytu skalowalnego w poziomie jest wyłączona lub ustaw właściwość ReadScale w warstwie usług nieobsługiwany, wszystkie połączenia są kierowane do repliki do odczytu / zapisu, niezależnie od `ApplicationIntent` właściwości.  

> [!NOTE]
> Istnieje możliwość aktywowania odczytu skalowalnego w poziomie na Standard lub zwykłej bazy danych, mimo że nie spowoduje routingu tylko do odczytu przeznaczone sesji do oddzielnych repliki. Jest to realizowane do obsługi istniejących aplikacji, które skalować w górę i w dół między warstwami cel ogólne/Standard i Premium/biznesowe krytyczne.  

Funkcja skalowania odczytu obsługuje poziomu spójności sesji. Jeśli ponownie nawiąże połączenie sesji tylko do odczytu, po spowodować błąd połączenia przez niedostępności repliki, mogą zostać przekierowane do innego serwera repliki. Gdy jest to mało prawdopodobne, może spowodować przetwarzania zestawu danych, która jest przestarzała. Podobnie jeśli aplikacja zapisuje dane przy użyciu sesji odczytu i zapisu i natychmiast odczytuje go za pomocą sesji tylko do odczytu, jest to możliwe, że nowe dane nie jest od razu widoczne.

## <a name="conclusion"></a>Podsumowanie
Baza danych SQL Azure jest ściśle zintegrowana z platformą Azure i wysoce zależy od usługi sieć szkieletowa wykrywania awarii i odzyskiwania na obiektach blob magazynu Azure do ochrony danych i stref dostępności wyższych odporności na uszkodzenia. W tym samym czasie bazy danych Azure SQL w pełni wykorzystuje technologię zawsze włączonej grupy dostępności programu SQL Server pole produkt replikacji i trybu failover. Kombinacja tych technologii umożliwia aplikacjom w pełni wykorzystać zalety modelu magazynu mieszane i obsługuje najbardziej wymagających umów SLA. 

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [stref dostępności Azure](../availability-zones/az-overview.md)
- Dowiedz się więcej o [usługi sieci szkieletowej](../service-fabric/service-fabric-overview.md)
- Dowiedz się więcej o [usługi Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) 
