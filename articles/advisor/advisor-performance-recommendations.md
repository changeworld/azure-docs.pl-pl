---
title: Zwiększ wydajność aplikacji platformy Azure dzięki usłudze Azure Advisor
description: Użyj klasyfikatora, aby zoptymalizować wydajność wdrożeń platformy Azure.
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: 405ec395feeb33b8511b9b915151b2ed9503c371
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443052"
---
# <a name="improve-performance-of-azure-applications-with-azure-advisor"></a>Zwiększ wydajność aplikacji platformy Azure dzięki usłudze Azure Advisor

Zalecenia dotyczące wydajności usługi Azure Advisor pomagają zwiększyć szybkość i szybkość reakcji aplikacji o krytycznym znaczeniu dla firmy. Zalecenia dotyczące wydajności można uzyskać od klasyfikatora na karcie **Wydajność** pulpitu nawigacyjnego klasyfikatora.

## <a name="reduce-dns-time-to-live-on-your-traffic-manager-profile-to-fail-over-to-healthy-endpoints-faster"></a>Skrócenie czasu działania dns w profilu usługi Traffic Manager w celu szybszego przerój awaryjnego punktu końcowego w trybie fail over do zdrowych punktów końcowych

[Ustawienia czasu wygaśnięcia (TTL)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-performance-considerations) w profilu usługi Traffic Manager umożliwiają określenie, jak szybko przełączyć punkty końcowe, jeśli dany punkt końcowy przestanie odpowiadać na zapytania. Zmniejszenie wartości czasu wygaśnięcia oznacza, że klienci będą kierowane do funkcjonowania punktów końcowych szybciej.

Usługa Azure Advisor identyfikuje profile usługi Traffic Manager z dłuższym tl skonfigurowany i zaleca konfigurowanie czasu wygaśnięcia do 20 sekund lub 60 sekund w zależności od tego, czy profil jest skonfigurowany do [szybkiego pracy awaryjnej](https://azure.microsoft.com/roadmap/fast-failover-and-tcp-probing-in-azure-traffic-manager/).

## <a name="improve-database-performance-with-sql-db-advisor"></a>Zwiększanie wydajności bazy danych przy użyciu funkcji SQL DB Advisor

Advisor zapewnia spójny, skonsolidowany widok zaleceń dla wszystkich zasobów platformy Azure. Integruje się z klasyfikatorem bazy danych SQL, aby przedstawić zalecenia dotyczące poprawy wydajności bazy danych sql azure.Doradca bazy danych SQL ocenia wydajność baz danych sql azure analizując historię użycia. Następnie oferuje zalecenia, które najlepiej nadają się do uruchamiania typowego obciążenia bazy danych.

> [!NOTE]
> Aby uzyskać zalecenia, baza danych musi mieć około tygodnia użycia, a w ciągu tego tygodnia musi być pewne spójne działania. Doradca bazy danych SQL można zoptymalizować łatwiej dla spójnych wzorców zapytań niż dla losowych serii aktywności.

Aby uzyskać więcej informacji na temat klasyfikatora bazy danych [SQL,](https://azure.microsoft.com/documentation/articles/sql-database-advisor/)zobacz Doradca bazy danych SQL .

## <a name="improve-app-service-performance-and-reliability"></a>Zwiększanie wydajności i niezawodności usługi app service

Usługa Azure Advisor integruje zalecenia dotyczące najlepszych rozwiązań w celu poprawy środowiska usługi App Services i odnajdowania odpowiednich funkcji platformy. Przykłady zaleceń dotyczących usług app services to:
* Wykrywanie wystąpień, w których zasoby pamięci lub procesora CPU są wyczerpane przez środowiska wykonawcze aplikacji z opcjami ograniczania ryzyka.
* Wykrywanie wystąpień, w których kolokowanie zasobów, takich jak aplikacje sieci web i bazy danych, może zwiększyć wydajność i obniżyć koszty.

Aby uzyskać więcej informacji na temat zaleceń dotyczących usług aplikacji, zobacz [Najważniejsze wskazówki dotyczące usługi Azure App Service](https://azure.microsoft.com/documentation/articles/app-service-best-practices/).

## <a name="use-managed-disks-to-prevent-disk-io-throttling"></a>Użyj dysków zarządzanych, aby zapobiec ograniczaniu we/wy dysku

Doradca zidentyfikuje maszyny wirtualne, które należą do konta magazynu, który osiąga swój cel skalowalności. Ten warunek sprawia, że te maszyny wirtualne podatne na ograniczanie we/wy. Advisor zaleci, aby używać dysków zarządzanych, aby zapobiec pogorszeniu wydajności.

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks-by-using-premium-storage"></a>Zwiększ wydajność i niezawodność dysków maszyn wirtualnych za pomocą magazynu w wersji Premium

Doradca identyfikuje maszyny wirtualne z dyskami standardowymi, które mają dużą liczbę transakcji na koncie magazynu i zaleca uaktualnienie do dysków premium. 

Usługa Azure Premium Storage zapewnia wysoką wydajność i małe opóźnienia obsługi dysku dla maszyn wirtualnych, które uruchamiają obciążeń intensywnie korzystających z we/wy. Dyski maszyn wirtualnych korzystające z kont magazynu w wersji premium przechowują dane na dyskach PÓŁPRZEWODNIKOWYCH (SSD). Aby uzyskać najlepszą wydajność dla aplikacji, zaleca się migrowanie dysków maszyn wirtualnych wymagających wysokiej usługi We/Wy do magazynu w stanie Premium.

## <a name="remove-data-skew-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>Usuwanie pochylenia danych w tabeli magazynu danych SQL w celu zwiększenia wydajności kwerend

Pochylenie danych może spowodować niepotrzebne przenoszenie danych lub wąskie gardła zasobów podczas uruchamiania obciążenia. Doradca wykryje pochylenie danych dystrybucji większe niż 15% i zaleca, aby ponownie redystrybuować dane i ponownie zaznaczać klucze dystrybucji tabeli. Aby dowiedzieć się więcej o identyfikowaniu i usuwaniu pochylenia, zobacz [rozwiązywanie problemów z pochyleniami](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice).

## <a name="create-or-update-outdated-table-statistics-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>Tworzenie lub aktualizowanie nieaktualnych statystyk tabeli w tabeli magazynu danych SQL w celu zwiększenia wydajności kwerend

Doradca identyfikuje tabele, które nie mają [aktualnych statystyk tabeli](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics) i zaleca tworzenie lub aktualizowanie statystyk tabeli. Optymalizator zapytań magazynu danych SQL używa aktualnych statystyk do oszacowania kardynalności lub liczby wierszy w wyniku kwerendy, który umożliwia optymalizatorowi kwerendy utworzenie planu kwerend wysokiej jakości dla najszybszej wydajności.

## <a name="scale-up-to-optimize-cache-utilization-on-your-sql-data-warehouse-tables-to-increase-query-performance"></a>Skalowanie w górę w celu optymalizacji wykorzystania pamięci podręcznej w tabelach usługi SQL Data Warehouse w celu zwiększenia wydajności kwerend

Usługa Azure Advisor wykrywa, czy usługa SQL Data Warehouse ma wysoką wartość buforową i niską wartość procentową trafień. Ten warunek wskazuje eksmisję wysokiej pamięci podręcznej, co może mieć wpływ na wydajność magazynu danych SQL. Doradca sugeruje skalowanie w górę magazynu danych SQL, aby upewnić się, że przydzielić wystarczającą pojemność pamięci podręcznej dla obciążenia.

## <a name="convert-sql-data-warehouse-tables-to-replicated-tables-to-increase-query-performance"></a>Konwertowanie tabel magazynu danych SQL na tabele replikowane w celu zwiększenia wydajności kwerend

Advisor identyfikuje tabele, które nie są replikowane tabele, ale będzie korzystać z konwersji i sugeruje, że konwertujesz te tabele. Zalecenia są oparte na rozmiarze tabeli replikowanej, liczbie kolumn, typie dystrybucji tabeli i liczbie partycji tabeli usługi SQL Data Warehouse. Dodatkowe heurystyki mogą być przewidziane w zaleceniu dla kontekstu. Aby dowiedzieć się więcej o określaniu tego zalecenia, zobacz [Zalecenia dotyczące magazynu danych SQL](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-concept-recommendations#replicate-tables). 

## <a name="migrate-your-storage-account-to-azure-resource-manager-to-get-all-of-the-latest-azure-features"></a>Migrowanie konta magazynu do usługi Azure Resource Manager w celu uzyskania wszystkich najnowszych funkcji platformy Azure

Migruj model wdrażania konta magazynu do usługi Azure Resource Manager (Resource Manager), aby korzystać z wdrożeń szablonów, dodatkowych opcji zabezpieczeń i możliwości uaktualnienia do konta GPv2 w celu wykorzystania najnowszych funkcji usługi Azure Storage. Doradca zidentyfikuje wszystkie autonomiczne konta magazynu, które używają klasycznego modelu wdrażania i zaleca migrację do modelu wdrażania Menedżera zasobów.

> [!NOTE]
> Klasyczne alerty w usłudze Azure Monitor zostały wycofane w sierpniu 2019 r. Zaleca się uaktualnienie klasycznego konta magazynu w celu zachowania funkcji alertów za pomocą Menedżera zasobów w celu zachowania funkcji alertów na nowej platformie. Aby uzyskać więcej informacji, zobacz [Klasyczne alerty emerytury](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement#retirement-of-classic-monitoring-and-alerting-platform).

## <a name="design-your-storage-accounts-to-prevent-hitting-the-maximum-subscription-limit"></a>Zaprojektuj konta magazynu, aby zapobiec osiągnięciu maksymalnego limitu subskrypcji

Region platformy Azure może obsługiwać maksymalnie 250 kont magazynu na subskrypcję. Po osiągnięciu limitu nie będzie można utworzyć więcej kont magazynu w tej kombinacji regionu/subskrypcji. Doradca sprawdzi subskrypcje i zalecenia dotyczące powierzchni, aby zaprojektować mniejszą liczbę kont magazynu dla wszystkich, które są bliskie osiągnięcia maksymalnego limitu.

## <a name="optimize-the-performance-of-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers"></a>Optymalizuj wydajność serwerów Azure MySQL, Azure PostgreSQL i Azure MariaDB 

### <a name="fix-the-cpu-pressure-of-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers-with-cpu-bottlenecks"></a>Napraw ciśnienie procesora serwerów Azure MySQL, Azure PostgreSQL i Azure MariaDB z wąskimi gardłami procesora
Bardzo wysokie wykorzystanie procesora CPU przez dłuższy czas może spowodować powolną wydajność zapytań dla obciążenia. Zwiększenie rozmiaru procesora CPU pomoże w optymalizacji środowiska wykonawczego zapytań bazy danych i poprawić ogólną wydajność. Usługa Azure Advisor zidentyfikuje serwery o wysokim wykorzystaniu procesora CPU, które prawdopodobnie uruchamiają obciążenia z ograniczeniami procesora CPU i zaleci skalowanie obliczeń.

### <a name="reduce-memory-constraints-on-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers-or-move-to-a-memory-optimized-sku"></a>Zmniejsz ograniczenia pamięci na serwerach Azure MySQL, Azure PostgreSQL i Azure MariaDB lub przejdź do jednostki SKU zoptymalizowanej pod kątem pamięci
Niski współczynnik trafień pamięci podręcznej może spowodować wolniejsze wykonanie kwerendy i zwiększenie we/wy. Może to być spowodowane złym planem kwerend lub uruchomieniem obciążenia intensywnie korzystającego z pamięci. Naprawianie planu kwerend lub [zwiększenie pamięci](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers) usługi Azure Database dla serwera bazy danych PostgreSQL, serwera bazy danych Azure MySQL lub serwera Azure MariaDB pomoże zoptymalizować wykonywanie obciążenia bazy danych. Usługa Azure Advisor identyfikuje serwery, których dotyczy problem z powodu tego wysokiego współczynnika zmian puli buforów i zaleca albo naprawienie planu kwerend, przeniesienie do wyższej jednostki SKU z większą pamięcią, albo zwiększenie rozmiaru magazynu, aby uzyskać więcej usług We/Wy.

### <a name="use-a-azure-mysql-or-azure-postgresql-read-replica-to-scale-out-reads-for-read-intensive-workloads"></a>Użyj repliki odczytu Usługi Azure MySQL lub Azure PostgreSQL, aby skalować odczyty w poziomie dla intensywnych obciążeń odczytu
Usługa Azure Advisor wykorzystuje heurystykę opartą na obciążeniach, taką jak stosunek odczytów do zapisów na serwerze w ciągu ostatnich siedmiu dni, aby zidentyfikować obciążenia intensywnie korzystające z odczytu. Twoja baza danych platformy Azure dla zasobu PostgreSQL lub bazy danych platformy Azure dla zasobu MySQL o bardzo wysokim współczynniku odczytu/zapisu może spowodować rywalizację procesora CPU i/lub pamięci, co prowadzi do powolnej wydajności zapytań. Dodanie [repliki](https://docs.microsoft.com/azure/postgresql/howto-read-replicas-portal) pomoże w skalowaniu odczytów w poziomie do serwera replik, uniemożliwiając ograniczenie procesora CPU i/lub pamięci na serwerze podstawowym. Doradca będzie identyfikować serwery o tak wysokich obciążeniach intensywnie odczytu i zaleca dodanie [repliki](https://docs.microsoft.com/azure/postgresql/concepts-read-replicas) odczytu w celu odciążenia niektórych obciążeń odczytu.


### <a name="scale-your-azure-mysql-azure-postgresql-or-azure-mariadb-server-to-a-higher-sku-to-prevent-connection-constraints"></a>Skalowanie serwera Azure MySQL, Azure PostgreSQL lub Azure MariaDB do wyższej jednostki SKU, aby zapobiec ograniczeniom połączeń
Każde nowe połączenie z serwerem bazy danych zajmuje trochę pamięci. Wydajność serwera bazy danych spada, jeśli połączenia z serwerem nie popełniają się z powodu [górnego limitu](https://docs.microsoft.com/azure/postgresql/concepts-limits) w pamięci. Usługa Azure Advisor zidentyfikuje serwery z wieloma awariami połączeń i zaleci uaktualnienie limitów połączeń serwera, aby zapewnić więcej pamięci na serwerze, skalując zasoby obliczeniowe lub używając jednostek SKU zoptymalizowanych pod kątem pamięci, które mają więcej obliczeń na rdzeń.

## <a name="scale-your-cache-to-a-different-size-or-sku-to-improve-cache-and-application-performance"></a>Skalowanie pamięci podręcznej do innego rozmiaru lub jednostki SKU w celu zwiększenia wydajności pamięci podręcznej i aplikacji

Wystąpienia pamięci podręcznej działają najlepiej, gdy nie działają pod wysokim ciśnieniem pamięci, wysokim obciążeniem serwera lub dużą przepustowością sieci, co może spowodować, że przestaną odpowiadać, unikają utraty danych lub staną się niedostępne. Doradca zidentyfikuje wystąpienia pamięci podręcznej w tych warunkach i zaleci stosowanie najlepszych rozwiązań w celu zmniejszenia ciśnienia pamięci, obciążenia serwera lub przepustowości sieci lub skalowania do innego rozmiaru lub jednostki SKU o większej pojemności.

## <a name="add-regions-with-traffic-to-your-azure-cosmos-db-account"></a>Dodawanie regionów z ruchem do konta usługi Azure Cosmos DB

Doradca wykryje konta usługi Azure Cosmos DB, które mają ruch z regionu, który nie jest obecnie skonfigurowany i zaleca dodanie tego regionu. Poprawi to opóźnienie dla żądań pochodzących z tego regionu i zapewni dostępność w przypadku awarii regionu. [Dowiedz się więcej o globalnej dystrybucji danych w usłudze Azure Cosmos DB](https://aka.ms/cosmos/globaldistribution)

## <a name="configure-your-azure-cosmos-db-indexing-policy-with-customer-included-or-excluded-paths"></a>Konfigurowanie zasad indeksowania usługi Azure Cosmos DB przy uwzględnieniu ścieżek uwzględnionych lub wykluczonych przez klienta

Usługa Azure Advisor zidentyfikuje kontenery usługi Cosmos DB, które używają domyślnej zasady indeksowania, ale mogą korzystać z niestandardowych zasad indeksowania opartych na wzorcu obciążenia. Domyślna reguła indeksowania indeksuje wszystkie właściwości, ale przy użyciu niestandardowych zasad indeksowania z jawnie uwzględnione lub wykluczone ścieżki używane w filtrach zapytań można zmniejszyć rUs i magazynu używanego do indeksowania. [Dowiedz się więcej o modyfikowaniu zasad indeksu](https://aka.ms/cosmosdb/modify-index-policy)

## <a name="configure-your-azure-cosmos-db-query-page-size-maxitemcount-to--1"></a>Konfigurowanie rozmiaru strony zapytania usługi Azure Cosmos DB (MaxItemCount) na wartość -1 

Usługa Azure Advisor zidentyfikuje kontenery usługi Azure Cosmos DB, które używają rozmiaru strony kwerendy 100 i zaleca użycie rozmiaru strony -1 w celu szybszego skanowania. [Dowiedz się więcej o maksymalnej liczbie przedmiotów](https://aka.ms/cosmosdb/sql-api-query-metrics-max-item-count)

## <a name="how-to-access-performance-recommendations-in-advisor"></a>Jak uzyskać dostęp do rekomendacji wydajności w programie Advisor

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com), a następnie otwórz [program Advisor](https://aka.ms/azureadvisordashboard).

2.  Na pulpicie nawigacyjnym klasyfikatora kliknij kartę **Wydajność.**

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o zaleceniach doradcy, zobacz:

* [Wprowadzenie do doradcy](advisor-overview.md)
* [Wprowadzenie do usługi Advisor](advisor-get-started.md)
* [Rekomendacje dotyczące kosztów doradcy](advisor-cost-recommendations.md)
* [Zalecenia dotyczące wysokiej dostępności advisor](advisor-high-availability-recommendations.md)
* [Zalecenia dotyczące zabezpieczeń doradcy](advisor-security-recommendations.md)
* [Zalecenia dotyczące doskonałości operacyjnej doradcy](advisor-operational-excellence-recommendations.md)
