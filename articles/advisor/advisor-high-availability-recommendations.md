---
title: Poprawienie dostępności aplikacji za pomocą usługi Azure Advisor | Dokumentacja firmy Microsoft
description: Użyj usługi Azure Advisor, aby poprawić wysoką dostępność wdrożeń platformy Azure.
services: advisor
documentationcenter: NA
author: kasparks
ms.author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: bdba3f135f852312af1692f77643095d865f1d06
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/28/2019
ms.locfileid: "66254663"
---
# <a name="improve-availability-of-your-application-with-azure-advisor"></a>Poprawienie dostępności aplikacji za pomocą usługi Azure Advisor

Usługa Azure Advisor ułatwia upewnij się, a ciągłość aplikacje krytyczne dla prowadzonej działalności. Można uzyskać zaleceń dotyczących wysokiej dostępności przez usługę Advisor z **wysokiej dostępności** karty Pulpit nawigacyjny usługi Advisor.

## <a name="ensure-virtual-machine-fault-tolerance"></a>Upewnij się, odporność na uszkodzenia maszyny wirtualnej

Aby zapewnić nadmiarowość aplikacji, zalecamy grupowanie co najmniej dwóch maszyn wirtualnych w zestawie dostępności. Klasyfikator identyfikuje maszyny wirtualne, które nie są częścią zestawu dostępności i zaleca przenoszenia ich do zestawu dostępności. Ta konfiguracja gwarantuje, że podczas każdej planowanego lub nieplanowanego zdarzenia konserwacji, co najmniej jedna maszyna wirtualna jest dostępna i spełnia warunki umowy SLA maszyn wirtualnych platformy Azure. Można wybrać, aby utworzyć zestaw dostępności dla maszyny wirtualnej lub Dodaj maszynę wirtualną do istniejącego zestawu dostępności.

> [!NOTE]
> Jeśli zdecydujesz się utworzyć zestaw dostępności, należy dodać co najmniej jedną maszynę wirtualną do niego. Firma Microsoft zaleca tego należy co najmniej dwóch maszyn wirtualnych w dostępności zestawu grup aby upewnić się, że co najmniej jedna maszyna jest dostępny podczas awarii.

## <a name="ensure-availability-set-fault-tolerance"></a>Upewnij się, że zestaw dostępności, odporności na uszkodzenia

Aby zapewnić nadmiarowość aplikacji, zalecamy grupowanie co najmniej dwóch maszyn wirtualnych w zestawie dostępności. Advisor ustala zestawów dostępności, które zawierają pojedynczą maszynę wirtualną i zaleca dodanie co najmniej jednej maszyny wirtualnej do niego. Ta konfiguracja gwarantuje, że podczas każdej planowanego lub nieplanowanego zdarzenia konserwacji, co najmniej jedna maszyna wirtualna jest dostępna i spełnia warunki umowy SLA maszyn wirtualnych platformy Azure. Możesz wybrać do utworzenia maszyny wirtualnej, lub można dodać istniejącej maszyny wirtualnej do zestawu dostępności.  

## <a name="use-managed-disks-to-improve-data-reliability"></a>Użycie usługi Managed Disks w celu poprawy niezawodności dotyczącej danych

Maszyny wirtualne, które znajdują się w zestaw dostępności z dyskami współużytkującymi konta magazynu lub jednostki skali magazynu nie są odporne na błędy jednostki skali magazynu jednego podczas awarii. Klasyfikator zidentyfikuje te zestawy dostępności i zaleca się migrację do usługi Azure Managed Disks. Pozwoli to zagwarantować, że dyski różnych maszyn wirtualnych w zestawie dostępności są wystarczająco izolowane pod kątem uniknięcia pojedynczego punktu awarii. 

## <a name="ensure-application-gateway-fault-tolerance"></a>Upewnij się, odporność na uszkodzenia bramy aplikacji

To zalecenie zapewnia ciągłość działania aplikacji o kluczowym znaczeniu, które są obsługiwane przez bramy application Gateway. Klasyfikator identyfikuje wystąpienia bramy aplikacji, które nie są skonfigurowane dla odporności na uszkodzenia i sugerują one akcji korygowania, które należy wykonać. Klasyfikator identyfikuje średnich i dużych aplikacja o pojedynczym wystąpieniu bramy i zaleca się dodanie co najmniej jedno wystąpienie więcej. Ponadto identyfikuje instance jednego lub wielu małych bramach aplikacji i zaleca się migrację do średnich i dużych jednostek SKU. Klasyfikator zaleca tych akcji, aby upewnić się, że Twoje wystąpienia bramy aplikacji są skonfigurowane do spełnić bieżące wymagania umowy SLA dla tych zasobów.

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>Chronić dane maszyny wirtualnej przed przypadkowym usunięciem

Konfigurowanie kopii zapasowej maszyny wirtualnej zapewnia dostępność danych krytyczne dla prowadzonej działalności i zapewnia ochronę przed przypadkowym uszkodzeniem lub usunięciem. Klasyfikator identyfikuje maszyny wirtualne, których kopia zapasowa nie jest włączona i zaleca się włączenie kopii zapasowej. 

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>Upewnij się, że masz dostęp do ekspertów ds. chmury platformy Azure, gdy jej potrzebujesz

Podczas uruchamiania obciążenia krytyczne dla prowadzonej działalności, należy mieć dostęp do pomocy technicznej potrzebnych. Klasyfikator identyfikuje potencjalne krytyczne dla prowadzonej działalności subskrypcje, które nie mają pomocy technicznej uwzględnione w ich plan pomocy technicznej i zaleca się uaktualnienie do opcja, która obejmuje pomoc techniczną.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-issues-affect-you"></a>Tworzenie alertów usługi Azure Service Health, aby otrzymywać powiadomienia, gdy napotkasz problemy z platformy Azure

Firma Microsoft zaleca skonfigurowanie alerty dotyczące kondycji usługi platformy Azure, aby otrzymywać powiadomienia, gdy napotkasz problemy z usługą Azure. [Usługa Azure Service Health](https://azure.microsoft.com/features/service-health/) to bezpłatna usługa, która zapewnia spersonalizowane wskazówki i pomoc techniczna, gdy mają wpływ problemu usługi platformy Azure. Advisor ustala subskrypcje, które nie mają skonfigurowano alertów i zaleca się tworzenie katalogu.

## <a name="configure-traffic-manager-endpoints-for-resiliency"></a>Skonfiguruj punkty końcowe usługi Traffic Manager w celu zapewnienia odporności

Profile usługi Traffic Manager z więcej niż jednym punktem końcowym środowiska wyższą dostępność, jeśli dowolnego danego punktu końcowego nie powiedzie się. Wprowadzenie do punktów końcowych w różnych regionach dalsze zwiększa niezawodność usług. Advisor identyfikuje profile Menedżer ruchu, gdy istnieje tylko jeden punkt końcowy i zaleca dodanie co najmniej jeden punkt końcowy więcej w innym regionie.

W przypadku wszystkich punktów końcowych w profilu usługi Traffic Manager, który jest skonfigurowany dla routingu odległości między elementami w tym samym regionie, użytkownicy z innych regionów, mogą wystąpić opóźnienia w połączeniu. Dodanie lub usunięcie punktu końcowego w innym regionie spowoduje zwiększenia ogólnej wydajności i zapewnienie wyższej dostępności, jeśli wszystkie punkty końcowe w jednym regionie nie powiedzie się. Klasyfikator identyfikuje profile usługi Traffic Manager skonfigurowany dla odległości routingu, gdy wszystkie punkty końcowe są w tym samym regionie. Zaleca się dodanie lub usunięcie punktu końcowego w innym regionie platformy Azure.

Jeśli profil usługi Traffic Manager jest skonfigurowany dla geograficznego routingu, ruch jest kierowany do punktów końcowych na podstawie określonych regionów. Region nie powiedzie się, czy wstępnie zdefiniowane trybu failover. O punkt końcowy, w której grupowanie regionalne jest skonfigurowana pod kątem "Wszystkie (World)" uniknąć ruchu sieciowego pomijanego i zwiększyć dostępność usług. Klasyfikator identyfikuje profile usługi Traffic Manager skonfigurowane dla geograficznego routingu w przypadku, gdy istnieje żaden punkt końcowy skonfigurowaną grupowanie regionalne jako "Wszystkie (World)". Zaleca się zmiany konfiguracji, aby punkt końcowy "wszystkie (świat).

## <a name="use-soft-delete-on-your-azure-storage-account-to-save-and-recover-data-after-accidental-overwrite-or-deletion"></a>Użyj usuwania nietrwałego na swoim koncie magazynu platformy Azure w celu zapisania i odzyskiwanie danych po przypadkowym zastępowania lub usuwania

Włącz [usuwania nietrwałego](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) na swoim koncie magazynu, aby usunąć obiekty BLOB przejście do stanu usunięcia nietrwałego zamiast trwale usunięte. Gdy dane są zastępowane, nietrwałe Usunięto migawkę jest generowany ma być zapisany stan zastąpione danych. Korzystanie z miękkiego usuwania umożliwia odzyskiwanie w przypadku przypadkowym lub zastępuje. Advisor ustala kont usługi Azure Storage, które nie mają włączone usuwanie nietrwałe i sugeruje, że można ją włączyć.

## <a name="configure-your-vpn-gateway-to-active-active-for-connection-resiliency"></a>Konfigurowanie bramy sieci VPN w taki sposób, aby aktywne aktywne dla połączeń

W konfiguracji aktywne aktywne oba wystąpienia bramy sieci VPN ustanowią tunele S2S sieci VPN do urządzenia sieci VPN w środowisku lokalnym. Sytuacji zdarzenie planowanej konserwacji lub nieplanowanego zdarzenia dotyczącego jednego wystąpienia bramy ruchu umożliwić przełączenie do innego aktywnego tunelu IPsec automatycznie. Usługa Azure Advisor zidentyfikuje bram sieci VPN, które nie są skonfigurowane jako aktywny aktywny i sugeruje, że można je skonfigurować wysoką dostępność.

## <a name="use-production-vpn-gateways-to-run-your-production-workloads"></a>Uruchamianie obciążeń produkcyjnych za pomocą bram sieci VPN w środowisku produkcyjnym

Usługa Azure Advisor sprawdzi dla bram sieci VPN, które są podstawowej jednostki SKU i zaleca się używanie produkcji jednostki SKU. Podstawowa jednostka SKU jest przeznaczona dla celów projektowania i testowania. Jednostki SKU w środowisku produkcyjnym oferują większej liczby tuneli, obsługi protokołu BGP, opcje konfiguracji aktywne aktywne, niestandardowe zasad Ipsec/IKE i wyższe stabilność i dostępność.

## <a name="repair-invalid-log-alert-rules"></a>Napraw reguł alertów dzienników nieprawidłowy

Usługa Azure Advisor wykryje reguł alertów, które mają nieprawidłowe zapytania określony w sekcji warunków. Reguł alertów dzienników są tworzone w usłudze Azure Monitor i są używane do uruchamiania zapytań analitycznych w określonych odstępach czasu. Wyniki zapytania określają, czy alert ma być wyzwalane. Zapytania analityczne może stać się nadgodzinach nieprawidłowa z powodu zmian w odwołania do zasobów, tabel lub poleceń. Klasyfikator oferuje rekomendacje dotyczące poprawisz zapytania w regule alertu, aby uniemożliwić wyłączone automatycznego pobierania i zapewnić pokrycie monitorowania zasobów na platformie Azure. [Dowiedz się więcej na temat rozwiązywania problemów reguły alertów](https://aka.ms/aa_logalerts_queryrepair)

## <a name="configure-consistent-indexing-mode-on-your-cosmos-db-collection"></a>Konfigurowanie spójnego trybu indeksowania w kolekcji usługi Cosmos DB

Kontenery usługi Azure Cosmos DB skonfigurowaną z opóźnieniem tryb indeksowania może mieć wpływ na aktualność wyników zapytania. Advisor wykryje, że kontenery są skonfigurowane w ten sposób i zaleca się przełączenie do trybu spójne. [Dowiedz się więcej na temat zasady w usłudze Cosmos DB indeksowania](https://aka.ms/cosmosdb/how-to-manage-indexing-policy)

## <a name="configure-your-azure-cosmos-db-containers-with-a-partition-key"></a>Konfigurowanie kontenerów usługi Azure Cosmos DB przy użyciu klucza partycji

Usługa Azure Advisor będzie identyfikować niepartycjonowana kolekcji usługi Azure Cosmos DB, które są zbliża się limit przydziału aprowizowanego magazynu. Oferuje rekomendacje, migracja tych kolekcji do nowej kolekcji z definicją klucza partycji, tak, aby ich automatycznie można skalować w poziomie przez usługę. [Dowiedz się więcej o wybór klucza partycji](https://aka.ms/cosmosdb/choose-partitionkey)

## <a name="upgrade-your-azure-cosmos-db-net-sdk-to-the-latest-version-from-nuget"></a>Uaktualnienie do najnowszej wersji Nuget zestawu SDK .NET usługi Azure Cosmos DB

Usługa Azure Advisor będzie identyfikować kont usługi Azure Cosmos DB, które korzystają z starsze wersje zestawu SDK platformy .NET i zaleca się uaktualnienie do najnowszej wersji z pakietów Nuget dla najnowszych poprawek, ulepszenia wydajności i nowe możliwości funkcji. [Dowiedz się więcej o Cosmos DB .NET SDK](https://aka.ms/cosmosdb/sql-api-sdk-dotnet)

## <a name="upgrade-your-azure-cosmos-db-java-sdk-to-the-latest-version-from-maven"></a>Uaktualnij do najnowszej wersji zestawu SDK Java usługi Azure Cosmos DB, z narzędzia Maven

Usługa Azure Advisor będzie identyfikować kont usługi Azure Cosmos DB, które używają starej wersji zestawu SDK języka Java i zaleca się uaktualnienie do najnowszej wersji z narzędzia Maven najnowszych poprawek, ulepszenia wydajności i nowe możliwości funkcji. [Dowiedz się więcej o Cosmos DB Java SDK](https://aka.ms/cosmosdb/sql-api-sdk-dotnet)

## <a name="upgrade-your-azure-cosmos-db-spark-connector-to-the-latest-version-from-maven"></a>Łącznik platformy Spark usługi Azure Cosmos DB uaktualnienie do najnowszej wersji narzędzia Maven

Usługa Azure Advisor będzie identyfikować kont usługi Azure Cosmos DB, które są używane przez łącznik usługi Cosmos DB Spark stare wersje i zaleca się uaktualnienie do najnowszej wersji z narzędzia Maven najnowszych poprawek, ulepszenia wydajności i nowe możliwości funkcji. [Dowiedz się więcej na temat łącznika usługi Cosmos DB Spark](https://aka.ms/cosmosdb/spark-connector)

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>Jak uzyskać dostęp do zaleceń dotyczących wysokiej dostępności w programie Advisor

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com), a następnie otwórz [Advisor](https://aka.ms/azureadvisordashboard).

2.  Na pulpicie nawigacyjnym usługi Advisor kliknij **wysokiej dostępności** kartę.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat zalecenia usługi Advisor zobacz:
* [Wprowadzenie do usługi Azure Advisor](advisor-overview.md)
* [Wprowadzenie do usługi Advisor](advisor-get-started.md)
* [Rekomendacji dotyczących kosztu usługi Advisor](advisor-cost-recommendations.md)
* [Zalecenia dotyczące wydajności usługi Advisor](advisor-performance-recommendations.md)
* [Zalecenia dotyczące zabezpieczeń usługi Advisor](advisor-security-recommendations.md)

