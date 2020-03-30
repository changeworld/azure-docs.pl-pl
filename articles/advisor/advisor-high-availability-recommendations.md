---
title: Zwiększ dostępność aplikacji za pomocą usługi Azure Advisor
description: Użyj usługi Azure Advisor, aby zwiększyć wysoką dostępność wdrożeń platformy Azure.
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: 997681ed62fa9985e3122ece22565dbae0e65b53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443110"
---
# <a name="improve-availability-of-your-application-with-azure-advisor"></a>Zwiększ dostępność aplikacji za pomocą usługi Azure Advisor

Usługa Azure Advisor pomaga zapewnić i poprawić ciągłość aplikacji o znaczeniu krytycznym dla firmy. Zaleceń dotyczących wysokiej dostępności można uzyskać na karcie **Wysoka dostępność** pulpitu nawigacyjnego klasyfikatora.

## <a name="ensure-virtual-machine-fault-tolerance"></a>Zapewnienie odporności na uszkodzenia maszyny wirtualnej

Aby zapewnić nadmiarowość aplikacji, zalecamy grupowanie co najmniej dwóch maszyn wirtualnych w zestawie dostępności. Advisor identyfikuje maszyny wirtualne, które nie są częścią zestawu dostępności i zaleca przeniesienie ich do zestawu dostępności. Ta konfiguracja zapewnia, że podczas zdarzenia planowanej lub nieplanowanej konserwacji dostępna jest co najmniej jedna maszyna wirtualna i spełniała umowy SLA maszyny wirtualnej platformy Azure. Można utworzyć zestaw dostępności dla maszyny wirtualnej lub dodać maszynę wirtualną do istniejącego zestawu dostępności.

> [!NOTE]
> Jeśli zdecydujesz się utworzyć zestaw dostępności, należy dodać do niego co najmniej jeszcze jedną maszynę wirtualną. Zaleca się pogrupowanie dwóch lub więcej maszyn wirtualnych w zestawie dostępności, aby upewnić się, że co najmniej jedna maszyna jest dostępna podczas awarii.

## <a name="ensure-availability-set-fault-tolerance"></a>Zapewnienie odporności na uszkodzenia ustawione na dostępność

Aby zapewnić nadmiarowość aplikacji, zalecamy grupowanie co najmniej dwóch maszyn wirtualnych w zestawie dostępności. Advisor identyfikuje zestawy dostępności, które zawierają jedną maszynę wirtualną i zaleca dodanie do niej co najmniej jednej maszyny wirtualnej.Ta konfiguracja zapewnia, że podczas zdarzenia planowanej lub nieplanowanej konserwacji dostępna jest co najmniej jedna maszyna wirtualna i spełniała umowy SLA maszyny wirtualnej platformy Azure.Można utworzyć maszynę wirtualną lub dodać istniejącą maszynę wirtualną do zestawu dostępności.  

## <a name="use-managed-disks-to-improve-data-reliability"></a>Korzystanie z funkcji Dyski zarządzane w celu zwiększania niezawodności danych

Maszyny wirtualne, które są w zestawie dostępności z dyskami, które współużytkują konta magazynu lub jednostki skalowania magazynu nie są odporne na awarie jednostek skalowania magazynu podczas awarii. Doradca zidentyfikuje te zestawy dostępności i zaleci migrację na dyski zarządzane platformy Azure. Zapewni to, że dyski różnych maszyn wirtualnych w zestawie dostępności są wystarczająco izolowane, aby uniknąć pojedynczego punktu awarii. 

## <a name="ensure-application-gateway-fault-tolerance"></a>Zapewnienie odporności na uszkodzenia bramy aplikacji

To zalecenie zapewnia ciągłość działania aplikacji o znaczeniu krytycznym, które są obsługiwane przez bramy aplikacji. Doradca identyfikuje wystąpienia bramy aplikacji, które nie są skonfigurowane pod kątem odporności na uszkodzenia, i sugeruje działania naprawcze, które można podjąć. Advisor identyfikuje bramy aplikacji średniego lub dużego pojedynczego wystąpienia i zaleca dodanie co najmniej jednego wystąpienia więcej. Identyfikuje również bramy małych aplikacji pojedynczych lub wielu wystąpień i zaleca migrację do średnich lub dużych jednostek SKU. Advisor zaleca te akcje, aby upewnić się, że wystąpienia bramy aplikacji są skonfigurowane tak, aby spełniały bieżące wymagania umowy SLA dla tych zasobów.

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>Ochrona danych maszyny wirtualnej przed przypadkowym usunięciem

Konfigurowanie kopii zapasowej maszyny wirtualnej zapewnia dostępność danych o znaczeniu krytycznym dla firmy i zapewnia ochronę przed przypadkowym usunięciem lub uszkodzeniem. Advisor identyfikuje maszyny wirtualne, w których kopia zapasowa nie jest włączona, i zaleca włączenie tworzenia kopii zapasowych. 

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>Upewnij się, że masz dostęp do ekspertów w chmurze platformy Azure, gdy jej potrzebujesz

Podczas uruchamiania obciążenia o krytycznym znaczeniu dla firmy, ważne jest, aby mieć dostęp do pomocy technicznej w razie potrzeby. Doradca identyfikuje potencjalne subskrypcje o znaczeniu krytycznym dla firmy, które nie mają pomocy technicznej zawartej w planie pomocy technicznej, i zaleca uaktualnienie do opcji obejmującej pomoc techniczną.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-issues-affect-you"></a>Tworzenie alertów usługi Azure Service Health, które mają być powiadamiane, gdy problemy z platformą Azure wpływają na Ciebie

Zalecamy skonfigurowanie alertów usługi Azure Service Health, które mają być powiadamiane, gdy problemy z usługą Platformy Azure mają wpływ na Ciebie. [Usługa Azure Service Health](https://azure.microsoft.com/features/service-health/) to bezpłatna usługa, która zapewnia spersonalizowane wskazówki i pomoc techniczną w przypadku wystąpienia problemu z usługą Azure. Doradca identyfikuje subskrypcje, które nie mają skonfigurowanych alertów i zaleca ich utworzenie.

## <a name="configure-traffic-manager-endpoints-for-resiliency"></a>Konfigurowanie punktów końcowych programu Traffic Manager pod kątem odporności

Profile usługi Traffic Manager z więcej niż jednym punktem końcowym doświadczają większej dostępności, jeśli dany punkt końcowy ulegnie awarii. Umieszczanie punktów końcowych w różnych regionach dodatkowo zwiększa niezawodność usług. Doradca identyfikuje profile usługi Traffic Manger, w których istnieje tylko jeden punkt końcowy i zaleca dodanie co najmniej jednego punktu końcowego w innym regionie.

Jeśli wszystkie punkty końcowe w profilu usługi Traffic Manager, który jest skonfigurowany do routingu zbliżeniowego znajdują się w tym samym regionie, użytkownicy z innych regionów mogą wystąpić opóźnienia połączenia. Dodawanie lub przenoszenie punktu końcowego do innego regionu poprawi ogólną wydajność i zapewni lepszą dostępność, jeśli wszystkie punkty końcowe w jednym regionie nie powiedzie się. Doradca identyfikuje profile usługi Traffic Manager skonfigurowane do routingu zbliżeniowego, w których wszystkie punkty końcowe znajdują się w tym samym regionie. Zaleca dodawanie lub przenoszenie punktu końcowego do innego regionu platformy Azure.

Jeśli profil usługi Traffic Manager jest skonfigurowany do routingu geograficznego, ruch jest kierowany do punktów końcowych na podstawie zdefiniowanych regionów. Jeśli region ulegnie awarii, nie ma wstępnie zdefiniowanej pracy awaryjnej. Posiadanie punktu końcowego, w którym grupowanie regionalne jest skonfigurowane na "All (World)" pozwoli uniknąć porzucania ruchu i poprawić dostępność usługi. Doradca identyfikuje profile usługi Traffic Manager skonfigurowane do routingu geograficznego, w których nie ma punktu końcowego skonfigurowanego tak, aby grupowanie regionalne było "Wszystkie (świat)". Zaleca się zmianę konfiguracji, aby punkt końcowy "All (World).

## <a name="use-soft-delete-on-your-azure-storage-account-to-save-and-recover-data-after-accidental-overwrite-or-deletion"></a>Użyj usuwania nietrwałego na koncie usługi Azure Storage, aby zapisywać i odzyskiwać dane po przypadkowym zastąpieniu lub usunięciu

Włącz [usuwanie nietrwałe](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) na koncie magazynu, aby usunięte obiekty blob przeszły do stanu nietrwałego usuniętego, a nie zostały trwale usunięte. Gdy dane są zastępowane, generowana jest migawka usuwania nietrwałego w celu zapisania stanu zastąpionych danych. Użycie usuwania nietrwałego umożliwia odzyskanie, jeśli występują przypadkowe usunięcia lub zastąpienie. Doradca identyfikuje konta usługi Azure Storage, które nie mają włączonego usuwania nietrwałego i sugeruje włączenie go.

## <a name="configure-your-vpn-gateway-to-active-active-for-connection-resiliency"></a>Konfigurowanie bramy sieci VPN do aktywności aktywnej pod kątem odporności połączenia

W konfiguracji aktywny-aktywny oba wystąpienia bramy sieci VPN ustanowią tunele sieci VPN S2S na lokalnym urządzeniu sieci VPN. Gdy zdarzenie planowanej konserwacji lub nieplanowane zdarzenie stanie się jednym wystąpieniem bramy, ruch zostanie automatycznie przełączony na inny aktywny tunel IPsec. Usługa Azure Advisor zidentyfikuje bramy sieci VPN, które nie są skonfigurowane jako aktywne i zasugeruje ich skonfigurowanie pod kątem wysokiej dostępności.

## <a name="use-production-vpn-gateways-to-run-your-production-workloads"></a>Używanie produkcyjnych bram sieci VPN do uruchamiania obciążeń produkcyjnych

Usługa Azure Advisor sprawdzi wszystkie bramy sieci VPN, które są podstawową jednostką SKU i zaleci użycie jednostki SKU produkcyjnej. Podstawowa jednostka SKU jest przeznaczona do celów programisty i testowania. Jednostki SKU produkcji oferują większą liczbę tuneli, obsługę protokołu BGP, opcje konfiguracji aktywnych aktywnych, niestandardowe zasady Ipsec/IKE oraz większą stabilność i dostępność.

## <a name="repair-invalid-log-alert-rules"></a>Naprawianie nieprawidłowych reguł alertów dziennika

Usługa Azure Advisor wykryje reguły alertów, które mają nieprawidłowe zapytania określone w sekcji warunków. Reguły alertów dziennika są tworzone w usłudze Azure Monitor i służą do uruchamiania zapytań analitycznych w określonych odstępach czasu. Wyniki zapytania określają, czy trzeba wyzwolić alert. Zapytania analityczne z upływem czasu mogą stać się nieprawidłowe z powodu zmian w zasobach, tabelach lub poleceniach, do których się odwołują. Advisor zaleci poprawienie kwerendy w regule alertów, aby uniemożliwić jej automatyczne wyłączenie i zapewnić monitorowanie zasobów na platformie Azure. [Dowiedz się więcej o rozwiązywaniu problemów z regułami alertów](https://aka.ms/aa_logalerts_queryrepair)

## <a name="configure-consistent-indexing-mode-on-your-cosmos-db-collection"></a>Konfigurowanie spójnego trybu indeksowania w kolekcji usługi Cosmos DB

Kontenery usługi Azure Cosmos DB skonfigurowane w trybie indeksowania z opóźnieniem mogą mieć wpływ na świeżość wyników zapytań. Doradca wykryje kontenery skonfigurowane w ten sposób i zaleci przełączenie do trybu spójnego. [Dowiedz się więcej o zasadach indeksowania w usłudze Cosmos DB](https://aka.ms/cosmosdb/how-to-manage-indexing-policy)

## <a name="configure-your-azure-cosmos-db-containers-with-a-partition-key"></a>Konfigurowanie kontenerów usługi Azure Cosmos DB przy użyciu klucza partycji

Usługa Azure Advisor zidentyfikuje kolekcje nieobjęte partycjami usługi Azure Cosmos DB, które zbliżają się do ich aprowizowanego przydziału magazynu. Zaleci migrację tych kolekcji do nowych kolekcji z definicją klucza partycji, dzięki czemu mogą być automatycznie skalowane w poziomie przez usługę. [Dowiedz się więcej o wyborze klucza partycji](https://aka.ms/cosmosdb/choose-partitionkey)

## <a name="upgrade-your-azure-cosmos-db-net-sdk-to-the-latest-version-from-nuget"></a>Uaktualnianie zestawu .NET SDK dla usługi Azure Cosmos DB do najnowszej wersji z narzędzia Nuget

Usługa Azure Advisor zidentyfikuje konta usługi Azure Cosmos DB, które używają starych wersji narzędzia .NET SDK, i zaleci uaktualnienie do najnowszej wersji firmy Nuget w celu uzyskania najnowszych poprawek, ulepszeń wydajności i nowych funkcji. [Dowiedz się więcej o SDK usługi Cosmos DB .NET](https://aka.ms/cosmosdb/sql-api-sdk-dotnet)

## <a name="upgrade-your-azure-cosmos-db-java-sdk-to-the-latest-version-from-maven"></a>Uaktualnianie zestawu Java SDK dla usługi Azure Cosmos DB do najnowszej wersji z programu Maven

Usługa Azure Advisor zidentyfikuje konta usługi Azure Cosmos DB, które używają starych wersji zestawu Java SDK i zaleci uaktualnienie do najnowszej wersji firmy Maven w celu uzyskania najnowszych poprawek, ulepszeń wydajności i nowych funkcji. [Dowiedz się więcej o SDK Java DB usługi Cosmos](https://aka.ms/cosmosdb/sql-api-sdk-dotnet)

## <a name="upgrade-your-azure-cosmos-db-spark-connector-to-the-latest-version-from-maven"></a>Uaktualnianie łącznika platformy Spark dla usługi Azure Cosmos DB do najnowszej wersji z programu Maven

Usługa Azure Advisor zidentyfikuje konta usługi Azure Cosmos DB, które używają starych wersji łącznika platformy Spark usługi Cosmos DB i zaleci uaktualnienie do najnowszej wersji firmy Maven w celu uzyskania najnowszych poprawek, ulepszeń wydajności i nowych funkcji. [Dowiedz się więcej o łączniku Platformy Spark usługi Cosmos DB](https://aka.ms/cosmosdb/spark-connector)

## <a name="enable-virtual-machine-replication"></a>Włącz replikację maszyn wirtualnych
Maszyny wirtualne, które nie mają włączonej replikacji w innym regionie, nie są odporne na awarie regionalne. Replikowanie maszyn wirtualnych zmniejsza wszelkie negatywne skutki biznesowe w czasie awarii regionu platformy Azure. Klasyfikator wykryje maszyny wirtualne, które nie mają włączonej replikacji i zaleca włączenie replikacji, dzięki czemu w przypadku awarii można szybko wywołać maszyny wirtualne w zdalnym regionie platformy Azure. [Dowiedz się więcej o replikacji maszyn wirtualnych](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart)

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>Jak uzyskać dostęp do zaleceń dotyczących wysokiej dostępności w programie Advisor

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com), a następnie otwórz [program Advisor](https://aka.ms/azureadvisordashboard).

2.  Na pulpicie nawigacyjnym klasyfikatora kliknij kartę **Wysoka dostępność.**

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat zaleceń advisor, zobacz:
* [Wprowadzenie do usługi Azure Advisor](advisor-overview.md)
* [Wprowadzenie do usługi Advisor](advisor-get-started.md)
* [Rekomendacje dotyczące kosztów doradcy](advisor-cost-recommendations.md)
* [Zalecenia dotyczące wydajności doradcy](advisor-performance-recommendations.md)
* [Zalecenia dotyczące zabezpieczeń doradcy](advisor-security-recommendations.md)
* [Zalecenia dotyczące doskonałości operacyjnej doradcy](advisor-operational-excellence-recommendations.md)
