---
title: Wysoka dostępność w usłudze Azure Cosmos DB
description: W tym artykule opisano, jak usługa Azure Cosmos DB zapewnia wysoką dostępność
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 928c943e21e7d00b87ac1e506b98d47107ac4348
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508553"
---
# <a name="high-availability-with-azure-cosmos-db"></a>Wysoka dostępność dzięki usłudze Azure Cosmos DB

Usługa Azure Cosmos DB w sposób przezroczysty replikuje dane we wszystkich regionach platformy Azure skojarzony z Twoim kontem Cosmos. Usługa cosmos DB wykorzystuje wiele warstw nadmiarowości danych, jak pokazano na poniższej ilustracji:

![Podział na partycje fizyczne](./media/high-availability/cosmosdb-data-redundancy.png)

- Dane w kontenerach Cosmos [partycjonowanej w poziomie](partitioning-overview.md).

- W każdym regionie każdej partycji jest chroniony za pomocą wszystkie zapisy replikowane i trwałym zatwierdzeniu większością replik zestawu replik. Repliki są dystrybuowane na maksymalnie 10-20 domen błędów.

- Każda partycja we wszystkich regionach są replikowane. Każdy region zawiera wszystkie partycje dane kontenera Cosmos i mogą akceptować zapisów i obsługiwać operacje odczytu.  

Jeśli Twoje konta usługi Cosmos jest rozłożona na *N* regiony platformy Azure będzie istnieć co najmniej *N* x 4 kopii wszystkich danych. Oprócz zapewnienia dostępu do danych z małymi opóźnieniami i skalowanie przepływności odczytu/zapisu między regionami skojarzonych z Twoim kontem Cosmos, o większej liczbie regionów (wyższe *N*) dodatkowo zwiększa dostępność.  

## <a name="slas-for-availability"></a>Umowy SLA dla dostępności

Jako globalnie rozproszonej bazy danych Cosmos DB zapewnia kompleksowe umowy SLA, które obejmują przepustowość, opóźnienie w 99. percentylu, spójności i wysokiej dostępności. W poniższej tabeli przedstawiono gwarancje dotyczące wysokiej dostępności oferowanej przez Cosmos DB dla kont pojedynczych i wielu regionów. Wysoką dostępność zawsze należy skonfigurować konta Cosmos mieć wiele regionów zapisu.

|Typ operacji  | Pojedynczy region |Multiregionalne (zapisuje pojedynczy region)|Multiregionalne (zapisuje wielu regionów) |
|---------|---------|---------|-------|
|Operacje zapisu    | 99.99    |99.99   |99.999|
|Operacje odczytu     | 99.99    |99.999  |99.999|

> [!NOTE]
> W praktyce dostępność rzeczywiste zapisu powiązana nieaktualność, sesja, spójny prefiks i modeli spójności ostatecznej jest znacznie wyższa niż opublikowanych umowy SLA. Rzeczywiste dostępność do odczytu dla wszystkich poziomów spójności jest znacznie wyższa niż opublikowanych umowy SLA.

## <a name="high-availability-with-cosmos-db-in-the-event-of-regional-outages"></a>Wysoka dostępność za pomocą usługi Cosmos DB w przypadku awarii regionalnej

Regionalnej awarii nie są niczym niezwykłym, a usługi Azure Cosmos DB upewnia się, że baza danych jest zawsze o wysokiej dostępności. Poniższe szczegóły Przechwyć zachowanie usługi Cosmos DB podczas awarii, w zależności od konfiguracji konta usługi Cosmos:

- Za pomocą usługi Cosmos DB zanim operacji zapisu zostało potwierdzone, do klienta, danych jest trwałym zatwierdzeniu przez kworum replik w regionie, który akceptuje operacji zapisu.

- Skonfigurowano regiony odczytu dla wielu kont w wielu regionach będzie o wysokiej dostępności dla odczytów i zapisów. Wywoływania regionalnego trybu failover są natychmiastowe i nie wymaga dokonywania żadnych zmian w aplikacji.

- **Multiregionalne konta z regionem zapisu pojedynczego (przestojach region zapisu):** Podczas awarii region zapisu tych kont będzie pozostawać stale dostępnymi dla odczytów. Jednak opłata za zapisywanie należy **"Włącz automatyczną pracę awaryjną"** na usługi Cosmos konta do trybu failover objęte wpływem regionu do innego regionu. Przełączenie w tryb failover miało miejsce w kolejności priorytet regionu, który został określony. Gdy objęte wpływem region jest wróci do trybu online, niezreplikowane danych w regionie zapisu dotyczy problem, w czasie awarii są udostępniane za pośrednictwem [konflikty kanału informacyjnego](how-to-manage-conflicts.md#read-from-conflict-feed). Aplikacje mogą odczytywać konflikty źródła danych, rozwiąż konflikty, w oparciu o logikę specyficzną dla aplikacji i zapisywać zaktualizowane dane z powrotem do kontenera Cosmos zgodnie z potrzebami. Po odzyskaniu region zapisu wcześniej objęte wpływem staje się automatycznie dostępne jako region odczytu. Można wywołać ręcznej pracy awaryjnej i skonfigurować objęte wpływem region jako regionu zapisu. Ponownie wykonać ręcznej pracy awaryjnej, korzystając z [wiersza polecenia platformy Azure lub w witrynie Azure portal](how-to-manage-database-account.md#manual-failover). Brak **bez utraty danych lub dostępności** przed, podczas lub po ręcznej pracy awaryjnej. Aplikacja jest nadal o wysokiej dostępności. 

- **Multiregionalne konta z regionem zapisu pojedynczego (regionem odczytu awarii):** Podczas awarii regionem odczytu te konta będzie pozostawać stale dostępnymi dla operacji odczytu i zapisu. Objęte wpływem region jest automatycznie rozłączany z regionu zapisu i zostanie oznaczona w trybie offline. [Cosmos DB z zestawów SDK](sql-api-sdk-dotnet.md) przekierowania odczyta wywołania do następnego dostępnego regionu na liście preferowany region. Jeśli żaden z regionów na liście preferowany region jest dostępny, wywołania automatycznie wrócić do bieżącego regionu zapisu. Żadne zmiany nie są wymagane w kodzie aplikacji do obsługi awarii w regionie odczytu. Po pewnym czasie gdy objęte wpływem region jest wróci do trybu online, wcześniej objęte wpływem odczytu z regionu zostanie automatycznie zsynchronizowana z bieżącego regionu zapisu i będzie można ponownie obsługiwać żądań odczytu. Dalsze operacje odczytu są przekierowywane do regionu odzyskane bez wprowadzania jakichkolwiek zmian w kodzie aplikacji. Podczas pracy awaryjnej i ponowne przyłączanie regionu zakończyły się niepowodzeniem przeczytaj spójności, które gwarancje w dalszym ciągu być uznawane przez usługi Cosmos DB.

- Kont w jednym regionie mogą zostać utracone po awarii regionalnej dostępności. Zawsze zaleca się konfigurowanie **co najmniej dwóch regionach** (najlepiej, co najmniej dwóch zapis regionów) przy użyciu konta Cosmos w celu zapewnienia wysokiej dostępności przez cały czas.

- Nawet w przypadku rzadko i niefortunne gdy region platformy Azure jest trwale nieodwracalny, istnieje bez utraty danych skonfigurowanie Twojego konta usługi Cosmos wielu regionów za pomocą domyślnego poziomu spójności *silne*. W przypadku region zapisu trwale nieodwracalny, dla kont Cosmos w wielu regionach skonfigurowano spójności powiązana nieaktualność potencjalnych okno utraty danych jest ograniczony do okna nieaktualność (*K* lub *T*); w sesji, poziom spójny prefiks i spójność ostateczna potencjalnych okno utraty danych jest ograniczony do maksymalnie pięć sekund. 

## <a name="availability-zone-support"></a>Obsługa strefy dostępności

Usługa Azure Cosmos DB to usługa globalnie dystrybuowanej, wielu wzorców bazy danych, która zapewnia wysoką dostępność i odporność podczas awarii regionalnego. Ponadto do wielu regionów odporności, teraz możesz włączyć **nadmiarowości strefy** podczas wybierania regionu do skojarzenia z bazą danych Azure Cosmos. 

Strefy dostępności, z obsługą usługi Azure Cosmos DB będzie upewnij się, że repliki są umieszczane w wielu strefach w danym regionie w celu zapewnienia wysokiej dostępności i odporności strefowej awarii. Nie wprowadzono żadnych zmian, opóźnienia i inne umowy SLA w tej konfiguracji. W przypadku awarii jednej strefie strefy nadmiarowość zapewnia trwałość pełnych danych w celu punktu odzyskiwania = 0 i dostępność dzięki RTO = 0. 

Nadmiarowość strefy to *dodatkowe możliwości* do [replikacji wielu wzorców](how-to-multi-master.md) funkcji. Samodzielnie nadmiarowości strefy nie może polegać umożliwiającego osiągnięcie odporności regionalne. Na przykład w przypadku regionalnej awarii lub dostępie z małymi opóźnieniami między regionami, zalecane jest zapewnienie wielu regionów zapisu oprócz nadmiarowości strefy. 

Podczas konfigurowania multiregionalne zapisu dla konta usługi Azure Cosmos, możesz zdecydować się na nadmiarowości strefy bez dodatkowych kosztów. W przeciwnym razie zobacz uwagi poniżej dotyczące cennika usługi obsługę nadmiarowości strefy. Nadmiarowości strefy na istniejący region konta usługi Azure Cosmos można włączyć, usuwając region i dodanie go z powrotem włączony nadmiarowości strefy.

Ta funkcja jest dostępna w następujących regionach platformy Azure:

* Południowe Zjednoczone Królestwo
* Azja Południowo-Wschodnia 
* East US
* Wschodnie stany USA 2 
* Środkowe stany USA

> [!NOTE] 
> Włączanie strefy dostępności dla jednego regionu konta usługi Azure Cosmos spowoduje opłaty, które są równoważne dodanie dodatkowych regionów do konta. Aby uzyskać szczegółowe informacje o cenach, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/cosmos-db/) i [koszt wielu regionów w usłudze Azure Cosmos DB](optimize-cost-regions.md) artykułów. 

Poniższa tabela zawiera podsumowanie możliwości wysokiej dostępności w różnych konfiguracjach konta: 

|KLUCZOWY WSKAŹNIK WYDAJNOŚCI  |Jednym regionie bez stref dostępności (Non-AZ)  |Jeden region z strefy dostępności (AZ)  |Multiregionalne zapisuje ze strefami dostępności (AZ, 2 regiony) — większość zalecane ustawienie |
|---------|---------|---------|---------|
|Zapis umowa SLA dotycząca dostępności     |   99,99%      |    99,99%     |  99.999%  |
|Umowa SLA gwarantująca dostępność do odczytu   |   99,99%      |   99,99%      |  99.999%       |
|Cena  |  Stawka rozliczeniowa w obrębie jednego regionu |  Stawka rozliczeniowa w jednym regionie w strefie dostępności |  Stawka rozliczeniowa w wielu regionach       |
|Błędy strefy — utrata danych   |  Utrata danych  |   Bez utraty danych |   Bez utraty danych  |
|Błędy strefy — dostępność |  Utrata dostępności  | Bez utraty dostępności  |  Bez utraty dostępności  |
|Opóźnienie odczytu    |  Obejmujące wiele regionów    |   Obejmujące wiele regionów   |    Małe  |
|Opóźnienie zapisu    |   Obejmujące wiele regionów   |  Obejmujące wiele regionów    |   Małe   |
|Awaria regionalna — utrata danych    |   Utrata danych      |  Utrata danych       |   Utrata danych <br/><br/> Jeśli przy użyciu powiązana nieaktualność spójności z węzłem głównym multi i więcej niż jeden region, utrata danych jest ograniczona do powiązana nieaktualność skonfigurowane na Twoim koncie. <br/><br/> Można uniknąć utraty danych podczas awarii regionalnej, konfigurując wysoki poziom spójności z wieloma regionami. Ta opcja jest powiązana z skutków ubocznych, które mają wpływ na dostępność i wydajność.      |
|Awaria regionalna — dostępność  |  Utrata dostępności       |  Utrata dostępności       |  Bez utraty dostępności  |
|Przepływność    |  Jedn. X aprowizowana przepływność      |  Jedn. X aprowizowana przepływność       |  2 x aprowizowanej przepływności jednostek RU/s <br/><br/> Ten tryb konfiguracji wymaga dwukrotność przepływność w porównaniu do jednego regionu, ze strefami dostępności występują, ponieważ istnieją dwa regiony.   |

> [!NOTE] 
> Aby włączyć obsługę strefy dostępności, konto usługi Azure Cosmos DB musi mieć wielu — główny/wielu-region zapisu włączone. 

Podczas dodawania regionów do nowego lub istniejącego konta usługi Azure Cosmos można włączyć nadmiarowości strefy. Obecnie można włączyć tylko nadmiarowości strefy za pomocą usługi Azure portal, szablonów programu PowerShell i usługi Azure Resource Manager. Aby włączyć nadmiarowości strefy na Twoim koncie usługi Azure Cosmos, należy ustawić `isZoneRedundant` flaga `true` dla określonej lokalizacji. Możesz ustawić tę flagę w ramach właściwości lokalizacji. Na przykład poniższy fragment kodu programu powershell umożliwia nadmiarowości strefy dla regionu "Azja południowo-wschodnia":

```powershell
$locations = @( 
    @{ "locationName"="Southeast Asia"; "failoverPriority"=0; "isZoneRedundant"= "true" }, 
    @{ "locationName"="East US"; "failoverPriority"=1 } 
) 
```

Strefy dostępności można włączyć za pomocą witryny Azure portal podczas tworzenia konta usługi Azure Cosmos. Podczas tworzenia konta usługi, upewnij się umożliwić **nadmiarowość geograficzna**, **zapisuje multiregionalne**i wybierz region, w której strefy dostępności są obsługiwane: 

![Włącz strefy dostępności przy użyciu witryny Azure portal](./media/high-availability/enable-availability-zones-using-portal.png) 

## <a name="building-highly-available-applications"></a>Tworzenie aplikacji o wysokiej dostępności

- Aby upewnić się wysoką zapisu, a także dostępność do odczytu, skonfiguruj konto usługi Cosmos, na co najmniej dwóch regionach za pomocą zapisu wielu regionów. Ta konfiguracja zapewnia najwyższą dostępność, opóźnienie najniższy, a najlepsze skalowalność dla obu odczytuje i zapisuje je to jest poparte umowy SLA. Aby dowiedzieć się więcej, zobacz temat jak [Skonfiguruj konto usługi Cosmos z wieloma regionami zapisu](tutorial-global-distribution-sql-api.md).

- Dla konta usługi Cosmos wielu regionów, które są skonfigurowane z regionem zapisu pojedynczego [włączyć automatyczny tryb failover przy użyciu wiersza polecenia platformy Azure lub w witrynie Azure portal](how-to-manage-database-account.md#automatic-failover). Po włączeniu automatycznej pracy awaryjnej, zawsze, gdy występuje regionalnej awarii, Cosmos DB zostanie automatycznie trybu failover Twoje konto.  

- Nawet w przypadku Twojego konta usługi Cosmos o wysokiej dostępności, aplikacja może nie być poprawnie zaprojektowana pozostaje o wysokiej dostępności. Aby przetestować end-to-end wysokiej dostępności aplikacji, okresowo wywoływać [ręcznej pracy awaryjnej przy użyciu wiersza polecenia platformy Azure lub w witrynie Azure portal](how-to-manage-database-account.md#manual-failover), jako część testowania aplikacji lub odzyskiwania po awarii (DR) awarii.

- W środowisku globalnie rozproszona baza danych ma bezpośrednią relację między trwałości danych i na spójność obecności awarii całego regionu. Podczas opracowywania planem ciągłości biznesowej, należy zrozumieć maksymalnego dopuszczalnego czasu oczekiwania na pełne odzyskanie aplikacji po wystąpieniu zdarzenia powodującego zakłócenia. Czas wymagany do przeprowadzenia pełnego odzyskania aplikacji jest znany jako cel czasu odzyskiwania (RTO). Należy również zrozumieć maksymalny okres najnowszych aktualizacji danych, aplikacja może tolerować utraty podczas odzyskiwania po wystąpieniu zdarzenia powodującego zakłócenia. Okres aktualizacji, którego utrata może być tolerowana, jest określany jako cel punktu odzyskiwania (RPO, recovery point objective). Aby wyświetlić cel punktu odzyskiwania i cel czasu odzyskiwania dla usługi Azure Cosmos DB, zobacz [spójności poziomy i danych trwałości](consistency-levels-tradeoffs.md#rto)

## <a name="next-steps"></a>Kolejne kroki

Następnie można przeczytać następujące artykuły:

* [Dostępność i wydajność kompromisy dla różnych poziomów spójności](consistency-levels-tradeoffs.md)
* [Globalnie skalowanie aprowizowana przepływność](scaling-throughput.md)
* [Dystrybucja globalna - kulisy](global-dist-under-the-hood.md)
* [Poziomy spójności w usłudze Azure Cosmos DB](consistency-levels.md)
* [Jak skonfigurować konto usługi Cosmos z wieloma regionami zapisu](how-to-multi-master.md)
