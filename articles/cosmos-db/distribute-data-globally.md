---
title: Dystrybucja danych globalnie z bazy danych Azure rozwiązania Cosmos | Dokumentacja firmy Microsoft
description: Więcej informacji na temat skali planety — replikacja geograficzna, trybu failover i odzyskiwanie danych za pomocą globalnej bazy danych z bazy danych z rozwiązania Cosmos platformy Azure, usługa globalnie rozproszone, mutli modelu bazy danych.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: sngun
ms.openlocfilehash: 4f548e180ca315013d5ca91118041cac2e622520
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34611453"
---
# <a name="how-to-distribute-data-globally-with-azure-cosmos-db"></a>Sposób rozpowszechniania danych globalnie z bazy danych Azure rozwiązania Cosmos
Azure to powszechny — ma wpływu globalnych w regionach geograficznych 50 + i jest stale rozszerzanie. Z jego obecność globalnych jeden zróżnicowanych funkcji, które platforma Azure oferuje deweloperom jego jest możliwość tworzenia, wdrażania i łatwo Zarządzaj aplikacjami globalnie rozproszone. 

[Azure Cosmos DB](../cosmos-db/introduction.md) to rozproszona globalnie wielomodelowa usługa bazy danych firmy Microsoft do aplikacji o krytycznym znaczeniu. Azure DB rozwiązania Cosmos zapewnia gotowe dystrybucję globalnych, [elastyczne skalowanie przepływność i magazyn](../cosmos-db/partition-data.md) na całym świecie, jednocyfrowej milisekundy opóźnienia w 99-ty percentyl [pięć dobrze zdefiniowany spójności modele](consistency-levels.md), zagwarantować wysokiej dostępności, wszystkie kopie przez [SLA kompleksowe branży](https://azure.microsoft.com/support/legal/sla/cosmos-db/). Azure DB rozwiązania Cosmos [automatycznie indeksuje wszystkie dane](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) bez konieczności postępowania w przypadku zarządzania schematu lub indeksu. Jest wiele modeli usługi i obsługuje dokumentu, klucz wartość, wykres i modeli danych rodziny kolumn. Jako natywnie urodzony w usłudze w chmurze bazy danych Azure rozwiązania Cosmos jest dokładnie odtwarzane z wielu dzierżawców i globalnych dystrybucji od podstaw się.


![Azure DB rozwiązania Cosmos kolekcji podzielone na partycje i rozproszone na trzech regionów](./media/distribute-data-globally/global-apps.png)

**Jeden kontener bazy danych Azure rozwiązania Cosmos podzielona na partycje i rozpowszechniane w różnych regionach platformy Azure**

Jak ma dowiedzieliśmy się podczas tworzenia bazy danych Azure rozwiązania Cosmos, Dodawanie globalnego dystrybucji nie może być zbagatelizowane. Nie można go "skręcania w" nad system bazy danych "w jednej lokacji". Możliwości oferowane przez globalnie rozproszoną bazę danych span poza z tradycyjnego geograficzne po awarii odzyskiwania (geograficznie DR) oferowane przez "pojedynczej lokacji" baz danych. Baz danych w jednej lokacji oferty DR geograficznie możliwości są podzbiorem strict globalnie rozproszone baz danych. 

Z rozkładem globalne gotowe Azure rozwiązania Cosmos DB, programiści nie muszą do tworzenia własnych szkieletów replikacji przez wykorzystanie jednej wzorzec Lambda (na przykład [DynamoDB usług AWS replikacji](https://github.com/awslabs/dynamodb-cross-region-library/blob/master/README.md)) za pośrednictwem dziennika bazy danych lub przez wykonywanie "zapisy double" w różnych regionach. Jak *nie* zaleca tych metod, ponieważ nie jest możliwe zapewnić poprawność takiego podejścia i podaj SLA dźwięku. 

W tym artykule udostępniamy omówienie funkcji globalnych dystrybucji Azure rozwiązania Cosmos DB. Opisano również Azure rozwiązania Cosmos DB unikatowy podejście do zapewnienia kompleksowej umów SLA. 

## <a id="EnableGlobalDistribution"></a>Włączanie gotowe dystrybucji globalne
Azure DB rozwiązania Cosmos udostępnia następujące funkcje do tworzenia aplikacji rozproszonych globalnie. Te funkcje są dostępne za pośrednictwem opartej na dostawcy zasobów Azure DB rozwiązania Cosmos [interfejsów API REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/) oraz portalu Azure.

Obejrzyj następujące wideo, aby zobaczyć funkcji gotowe dystrybucji globalne w usłudze Azure DB rozwiązania Cosmos w akcji.

> [!VIDEO https://www.youtube.com/embed/1D06yjTVxt8]
>

### <a id="RegionalPresence"></a>Wszechobecne regionalne 
Azure stale rośnie jego obecność geograficzne przełączając [nowych regionów](https://azure.microsoft.com/regions/) online. Azure DB rozwiązania Cosmos jest sklasyfikowany jako *podstawowych usługi* na platformie Azure i jest dostępna we wszystkich regionach platformy Azure nowe domyślnie. Dzięki temu można skojarzyć z konta bazy danych Azure DB rozwiązania Cosmos region geograficzny, jak Azure zostanie otwarty nowy region dla firm.

### <a id="UnlimitedRegionsPerAccount"></a>Kojarzenie nieograniczoną liczbę regionów z konta bazy danych Azure DB rozwiązania Cosmos
Azure DB rozwiązania Cosmos można skojarzyć dowolną liczbę regiony platformy Azure przy użyciu konta bazy danych Azure DB rozwiązania Cosmos. Poza ograniczenia grodzenia (na przykład Chin, Niemcy) nie istnieją ograniczenia liczby regionów, które mogą być powiązane z Twoim kontem bazy danych Azure DB rozwiązania Cosmos. Na poniższej ilustracji przedstawiono konta bazy danych skonfigurowane zakresu w 25 regionach platformy Azure.  

![Konto bazy danych DB rozwiązania Cosmos Azure spanning 25 regiony platformy Azure](./media/distribute-data-globally/spanning-regions.png)

**Dzierżawy Azure rozwiązania Cosmos DB bazy danych konta rozszerzania regiony platformy Azure 25**


### <a id="PolicyBasedGeoFencing"></a>Oparte na zasadach grodzenia
Azure DB rozwiązania Cosmos jest przeznaczona do obsługi opartych na zasadach grodzenia. Grodzenia jest istotnym elementem do zapewnienia ograniczenia dotyczące zarządzania i zgodności danych i spowodować, że skojarzenie określonego regionu z Twoim kontem. Przykłady grodzenia obejmują (ale nie są ograniczone do) zakresu globalnego dystrybucji do regionów, w chmurze suwerennych (na przykład Chin i Niemczech) lub w obrębie granicy podatkowych dla instytucji rządowych (na przykład Australia). Zasady są kontrolowane przy użyciu metadanych subskrypcji platformy Azure.

### <a id="DynamicallyAddRegions"></a>Dynamicznie dodawać i usuwać regionów
Azure DB rozwiązania Cosmos umożliwia (skojarzenia) Dodaj lub Usuń (skojarzenie) regionów, z Twojego konta bazy danych w dowolnym momencie (zobacz [powyższej ilustracji](#UnlimitedRegionsPerAccount)). Z równoległą replikację danych na partycji bazy danych Azure rozwiązania Cosmos zapewnia, że po dodaniu nowego regionu pobiera on jest dostępnych dla operacji w ciągu 30 minut dowolnego miejsca na świecie (przy założeniu danych jest 100 tabel lub mniej). 

### <a id="FailoverPriorities"></a>Priorytetów trybu failover
Aby kontrolować dokładna kolejność regionalnej pracy w trybie Failover w przypadku awarii, bazy danych rozwiązania Cosmos Azure umożliwia kojarzenie *priorytet* z różnych regionów skojarzone z bazy danych konta (zobacz rysunek poniżej). Azure DB rozwiązania Cosmos gwarantuje, że sekwencja automatycznej pracy awaryjnej występuje w podanej kolejności priorytet. Aby uzyskać więcej informacji na temat regionalnej pracy w trybie Failover, zobacz [automatyczne regionalnej pracy w trybie Failover dla ciągłość prowadzenia działalności biznesowej w usłudze Azure DB rozwiązania Cosmos](regional-failover.md).


![Konfigurowanie priorytetów trybu failover z bazy danych Azure rozwiązania Cosmos](./media/distribute-data-globally/failover-priorities.png)

**Dzierżawy Azure DB rozwiązania Cosmos można skonfigurować kolejność priorytetów trybu failover (po prawej) dla regionów skojarzone z kontem bazy danych**

### <a id="ConsistencyLevels"></a>Wiele, modeli dobrze zdefiniowany spójności globalnie rozproszone baz danych
Obsługuje bazę danych systemu Azure rozwiązania Cosmos [wielu modeli dobrze zdefiniowany intuicyjne i praktyczne spójności](consistency-levels.md) przez umowy SLA. Można wybrać model określonych spójności (z listy dostępnych opcji) w zależności od obciążenia/scenariuszy. 

### <a id="TunableConsistency"></a>Dostosowywalne spójności globalnie zreplikowanych baz danych
Azure DB rozwiązania Cosmos umożliwia programowo zastąpienia i zwalnia domyślny wybór spójności na podstawie danego żądania w czasie wykonywania. 

### <a id="DynamicallyConfigurableReadWriteRegions"></a>Dynamicznie konfigurować odczytu i zapisu regionów
Azure DB rozwiązania Cosmos umożliwia konfigurowanie regionów (skojarzonego z bazą danych) "do odczytu", "write" lub "odczytu/zapisu" regionów. 

### <a id="ElasticallyScaleThroughput"></a>Elastycznie skalować przepływność w regionach platformy Azure
Kontener bazy danych Azure rozwiązania Cosmos można elastycznie skalować przez inicjowania obsługi administracyjnej przepływności programowo. Przepływność jest stosowany do wszystkich regionów, które kontenera Azure DB rozwiązania Cosmos jest dystrybuowane w.

### <a id="GeoLocalReadsAndWrites"></a>Lokalnych Geo odczyty i zapisy
Najważniejszą korzyścią globalnie rozproszoną bazę danych jest oferowane małe opóźnienia dostępu do danych w dowolnym miejscu na świecie. Azure DB rozwiązania Cosmos zapewnia małe opóźnienia odczytuje i zapisuje w 99-ty percentyl na całym świecie. Gwarantuje, że wszystkie operacje odczytu są obsługiwane z najbliżej regionu (local). Aby obsłużyć żądanie odczytu, kworum lokalnego regionu, w którym zostało wystawione odczytu jest używany. To samo dotyczy zapisów. Zapis zostaje potwierdzony tylko wtedy, gdy większość replik trwale popełnienie zapisu lokalnie, ale bez jest uzyskiwany w replikach zdalnego potwierdzić zapisy. Aby ją inaczej, protokół replikacji bazy danych Azure rozwiązania Cosmos działa przy założeniu, że kworum odczytu i zapisu zawsze znajdują się lokalnie do regionu, w którym żądanie zostało wydane.

### <a id="ManualFailover"></a>Ręcznego przełączania trybu failover
Azure DB rozwiązania Cosmos pozwala na zainicjowanie pracy awaryjnej konta bazy danych, aby sprawdzić poprawność *kompleksowe* właściwości dostępności dla całej aplikacji (poza bazą danych). Ponieważ dotrą zarówno bezpieczeństwa, jak i właściwości liveness wyboru wykrywania i wiodące błąd bazy danych Azure rozwiązania Cosmos gwarantuje *utracie zero* operacji zainicjował dzierżawy ręcznego przełączania trybu failover.

### <a id="AutomaticFailover"></a>Automatycznej pracy awaryjnej
Azure DB rozwiązania Cosmos obsługuje automatycznej pracy awaryjnej w trakcie co najmniej jeden regionalnej awarii. Podczas regionalnej pracy awaryjnej bazy danych Azure rozwiązania Cosmos przechowuje opóźnienie odczytu, dostępność przestojów, spójność i przepływności umów SLA. Azure DB rozwiązania Cosmos zapewnia górna granica na czas trwania na zakończenie operacji automatycznej pracy awaryjnej. To okno o utracie danych podczas regionalnej awarii.

### <a id="GranularFailover"></a>Przeznaczona dla innego trybu failover szczegółowości
Obecnie funkcji automatycznej i ręcznej pracy awaryjnej są widoczne na poziom szczegółowości konta bazy danych. Uwaga: wewnętrznie rozwiązania Cosmos bazy danych Azure oferuje proste *automatyczne* pracy awaryjnej dokładniej bazy danych, kontenera lub nawet partycji (kontener będącej właścicielem, jeśli zakres kluczy). 

### <a id="MultiHomingAPIs"></a>Wiele homing w Azure rozwiązania Cosmos bazy danych
Azure DB rozwiązania Cosmos pozwala korzystać z bazą danych przy użyciu *logicznej* (niezależny od regionu) lub *fizycznych* punktów końcowych (określonego regionu). Użycie logiczne punkty końcowe gwarantuje, że aplikacja może być przezroczysty wieloadresowego podczas pracy awaryjnej. Drugie, fizycznych punktu końcowego, zapewnia precyzyjną kontrolę do aplikacji w celu przekierowania odczyty i zapisuje określonych regionach.

Informacje dotyczące sposobu konfigurowania preferencji odczytu można znaleźć [interfejsu API SQL](../cosmos-db/tutorial-global-distribution-sql-api.md), [Gremlin API](../cosmos-db/tutorial-global-distribution-graph.md), [API tabeli](../cosmos-db/tutorial-global-distribution-table.md), i [API bazy danych MongoDB](../cosmos-db/tutorial-global-distribution-mongodb.md) w te artykuły.

### <a id="TransparentSchemaMigration"></a>Migracja schematu i indeksu przejrzyste i spójności bazy danych 
Azure DB rozwiązania Cosmos jest w pełni [niezależny od schematu](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf). Unikatowa konstrukcja aparatu bazy danych umożliwia DB rozwiązania Cosmos Azure, aby automatycznie i synchronicznie indeks wszystkie dane na wprowadzanie, bez konieczności żadnego schematu lub indeksów pomocniczych od użytkownika. Dzięki temu można szybko przejść aplikacji rozproszonych globalnie bez obaw o migracji schematu i indeks bazy danych lub koordynowanie wdrożenia aplikacji fazy wielu zmian schematu. Azure DB rozwiązania Cosmos gwarantuje, że wszystkie zmiany do indeksowania jawnie wprowadzonych przez Ciebie zasad powoduje spadek wydajności i dostępności.  

### <a id="ComprehensiveSLAs"></a>Kompleksowe umów SLA (poza wysokiej dostępności)
Jako usługa globalnie rozproszoną bazę danych, bazy danych rozwiązania Cosmos Azure oferuje dobrze zdefiniowany i wszechstronne umowy SLA dla **dostępności**, **opóźnienia**, **przepływności**i **spójności** dla bazy danych, systemem w skali globalnej, niezależnie od liczby regionów skojarzonego z bazą danych.  

## <a id="LatencyGuarantees"></a>Gwarancje opóźnienia
Najważniejszą korzyścią usług globalnie rozproszoną bazę danych, takich jak bazy danych Azure rozwiązania Cosmos jest oferowanie małe opóźnienia dostępu do danych w dowolnym miejscu na świecie. Azure DB rozwiązania Cosmos oferuje gwarantowane małe opóźnienia w 99-ty percentyl dla różnych operacji bazy danych. Protokół replikacji bazy danych Azure rozwiązania Cosmos używającego gwarantuje, że operacje bazy danych (odczyty i zapisy) są zawsze wykonywane w regionie lokalnego do klienta. Opóźnienie umowy SLA platformy Azure DB rozwiązania Cosmos zapewnia gwarancje na 99-ty percentyl dla odczytów, zapisów (synchronicznie) indeksowane i zapytania o różnych rozmiarach żądań i odpowiedzi. Gwarancje opóźnienia zapisów obejmują zatwierdzeń kworum Większość trwałe w ramach lokalnego regionu.

### <a id="LatencyAndConsistency"></a>Czas oczekiwania w relacji z spójności 
Globalnie rozproszone usługi zapewniające wysoki poziom spójności w Instalatorze globalnie rozproszone wymaga synchroniczny replikacji zapisami lub przeprowadzić synchronicznie odczyty między regionu. Szybkość jasny i niezawodność sieci rozległej mówią, że wysoki poziom spójności spowoduje zmniejszenie dostępności operacji bazy danych i większych opóźnień. W związku z tym oferują zagwarantować małe opóźnienia 99-ty percentyl i dostępności 99,99% dla wszystkich kont w pojedynczym regionie i wszystkich kont w przypadku z swobodna spójności i 99,999% dostępności na wszystkich kontach w przypadku bazy danych, usługi należy stosować asynchroniczną replikację. Wymaga to w Włącz usługę musi oferują [modele spójności dobrze zdefiniowany, swobodna](consistency-levels.md) — mniejsze niż silne (oferowanie niskich opóźnień i dostępności gwarancje) i najlepiej mocniejszy niż element spójność "ostateczna" (z intuicyjne model programowania).

Azure DB rozwiązania Cosmos zapewnia operacja odczytu nie jest wymagane do kontaktowania się z replik w różnych regionach dostarczać gwarancję poziomu spójności określone. Podobnie, gwarantuje, że operacja zapisu nie pobrać blokowane podczas replikacji danych we wszystkich regionach oznacza to, zapisuje asynchronicznie replikacja w regionach). Dla konta bazy danych w przypadku obu silne oraz wiele poziomów spójności swobodna są dostępne. 

### <a id="LatencyAndAvailability"></a>Czas oczekiwania w relacji o dostępności 
Opóźnienia i dostępności są dwie strony tego samego monety. Omówieniu, czas oczekiwania operacji w stanie stabilności i dostępności obecności partycje sieci i błędów. Z punktu widzenia aplikacji powolne uruchomioną operację bazy danych jest nierozróżnialne z bazy danych, która jest niedostępna. 

Aby odróżnić duże opóźnienie od niedostępności, bazy danych Azure rozwiązania Cosmos zapewnia bezwzględną górna granica opóźnienia różne operacje bazy danych. Jeśli operacja bazy danych trwa dłużej niż górna granica do ukończenia, bazy danych Azure rozwiązania Cosmos zwraca błąd upływu limitu czasu. SLA dostępności bazy danych Azure rozwiązania Cosmos gwarantuje, że limity czasu są uwzględniane dostępności umowy dotyczącej poziomu usług. 

### <a id="LatencyAndThroughput"></a>Czas oczekiwania w relacji o przepływności
Azure DB rozwiązania Cosmos nie powoduje możesz wybrać opóźnienia i przepływności. Honoruje umowy SLA dla obu opóźnienia w 99-ty percentyl, a zapewnia przepływność, które zostały udostępnione. 

## <a id="ConsistencyGuarantees"></a>Gwarancje spójności
Gdy [modelu wysoki poziom spójności](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) jest standardem złota z programowania danych znajduje się na problemy z ich opanowaniem ceny większego opóźnienia (w stanie stabilności) i zmniejszona dostępność (w wypadku awarii). 

Azure DB rozwiązania Cosmos zapewnia model programowania dobrze zdefiniowany możesz przeglądanie informacji o spójności replikowanych danych. Aby umożliwiają łatwe tworzenie aplikacji rozproszonych globalnie możliwości podłączonej do wielu sieci, mają być niezależny od regionu i niezależne od regionu, z którym są odczyty i zapisy modeli spójności udostępnianych przez bazy danych Azure rozwiązania Cosmos obsługiwane. 

Azure DB rozwiązania Cosmos spójności umowy dotyczącej poziomu usług gwarantuje, że 100% żądań odczytu spełniającą gwarancji spójności dla modelu spójności określonego przez użytkownika (albo na poziomie żądania lub konto bazy danych). Żądanie odczytu jest uważana za osiągnięcia spójności umowy SLA, jeśli spełnione są wszystkie skojarzone z poziomu spójności gwarancje spójności. Poniższa tabela umożliwia przechwytywanie gwarancje spójności odpowiadające modeli określonego spójności oferowane przez bazy danych Azure rozwiązania Cosmos.

<table>
    <tr>
        <td><strong>Model spójności</strong></td>
        <td><strong>Właściwości spójności</strong></td>
        <td><strong>Umowa SLA</strong></td>
    </tr>
        <tr>
        <td>Silna</td>
        <td>Linearizable</td>
        <td>100%</td>
    </tr>
    <tr>
        <td rowspan="3">Spójność powiązanej nieaktualności</td>
        <td>Przeczytaj monotoniczna (w obrębie regionu)</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>Prefiks spójne</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>Nieaktualności powiązany &lt; K, T</td>
        <td>100%</td>
    </tr>
<tr>
        <td rowspan="3">Sesja</td>
        <td>Przeczytaj własne zapisu</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>Monotoniczna odczytu</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>Prefiks spójne</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>Prefiks spójne</td>
        <td>Prefiks spójne</td>
        <td>100%</td>
    </tr>
</table>

**Gwarancje spójności skojarzone z modelem danego spójności w usłudze Azure DB rozwiązania Cosmos**


### <a id="ConsistencyAndAvailability"></a>Relacji w spójności z dostępności
[Wynik niemożności](http://www.glassbeam.com/sites/all/themes/glassbeam/images/blog/10.1.1.67.6951.pdf) z [Newtona zakończenia](https://people.eecs.berkeley.edu/~brewer/cs262b-2004/PODC-keynote.pdf) potwierdza, że jest w rzeczywistości możliwości systemu pozostają dostępne i oferowanie linearizable spójności w wypadku awarii. Usługa bazy danych należy wybrać CP lub region, w którym CP systemów zrezygnujesz z dostępności na rzecz linearizable spójności, gdy zrezygnujesz z systemów region [linearizable spójności](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) na rzecz dostępności. Azure DB rozwiązania Cosmos nigdy nie narusza modelu żądanego spójności formalnie ułatwia systemu CP. W praktyce spójności nie jest jednak wszystkie lub żadne oferty; istnieje wiele modeli dobrze zdefiniowany spójności wzdłuż spektrum spójności między linearizable i ewentualnej spójności. W usłudze Azure DB rozwiązania Cosmos identyfikuje kilka modele swobodna spójności, które są stosowane, do rzeczywistych scenariuszach i intuicyjne do użycia. Azure DB rozwiązania Cosmos przechodzi skutków ubocznych spójności dostępności, oferując [wielu rozluźnić spójności jeszcze dobrze zdefiniowanego, modele](consistency-levels.md) i o dostępności 99,99% dla wszystkich pojedynczy region konta bazy danych i 99,999% odczytu i zapisu dostępność dla wszystkich kont w przypadku bazy danych. 

### <a id="ConsistencyAndAvailability"></a>Relacja w spójności z opóźnieniem
Bardziej zaawansowane odmianą Newtona zakończenia jest wywoływana [PACELC](http://cs-www.cs.yale.edu/homes/dna/papers/abadi-pacelc.pdf), która uwzględnia również wpływ na opóźnienie i spójności w stanie stabilności. Stany go, że w stanie stabilności, system bazy danych należy wybrać opcję spójności i czas oczekiwania. Z wielu modeli swobodna spójności (obsługiwana przez asynchroniczną replikację i lokalne odczytu i zapisu kworum) bazy danych Azure rozwiązania Cosmos zapewnia wszystkie odczyty i zapisy są lokalne do odczytu i zapisu odpowiednio regionów. Dzięki temu Azure DB rozwiązania Cosmos oferowanie gwarancje małe opóźnienia w obrębie regionu dla modeli danej spójności.  

### <a id="ConsistencyAndThroughput"></a>Relacji w spójności z przepływnością
Ponieważ wybór zależy od implementacji modelu określonego spójności [typu kworum](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf), przepływności również w zależności od wyboru modelu spójności. Na przykład w usłudze Azure DB rozwiązania Cosmos, RU dla odczytów silnie spójne jest około *podwójne* który ostatecznie spójności odczytów. W takim przypadku należy udostępnić double RUs do osiągnięcia tej samej przepływności.


![Relacja między spójności i przepustowość](./media/distribute-data-globally/consistency-and-throughput.png)

**Relacja odczytu pojemności dla modelu określonego spójności w usłudze Azure DB rozwiązania Cosmos**

## <a id="ThroughputGuarantees"></a>Gwarancje przepustowości 
Azure DB rozwiązania Cosmos pozwala na skali przepływności (a także, magazynu), elastycznie przez dowolną liczbę regionów, zależnie od potrzeb i żądanie. 

![Azure DB rozwiązania Cosmos rozproszonych i na partycje w kolekcjach](../cosmos-db/media/introduction/azure-cosmos-db-global-distribution.png)

**Jeden kontener bazy danych Azure rozwiązania Cosmos poziomie podzielonym na partycje (w trzech zasobów partycji w obrębie regionu) i globalnie rozproszone w trzech regionach platformy Azure**

Kontener bazy danych Azure rozwiązania Cosmos pobiera dystrybuowane w dwóch wymiarów i w obrębie regionu i (ii) w regionach. Oto kroki tej procedury: 

* **Lokalnej dystrybucji**: W pojedynczym regionie kontenera Azure DB rozwiązania Cosmos jest poziomo skalowana w poziomie na podstawie *partycje zasobów*. Każda partycja zasobów zarządza zestawu kluczy i silnie spójne i wysokiej dostępności fizycznie reprezentowanego przez cztery repliki skrót *zestawu replik* i stan replikacji maszyny wśród tych replik. Azure DB rozwiązania Cosmos jest systemem pełni postanowieniom zasobów, których partycję zasobu odpowiada dostarczać swój udział przepływności budżet przydzielony zasobów systemowych. Skalowanie kontenera Azure DB rozwiązania Cosmos jest niewidoczny dla użytkowników. Azure DB rozwiązania Cosmos zarządza partycje zasobów i dzieli scala je w razie potrzeby przechowywania danych i zmienić wymagania dotyczące przepływności. 
* **Globalne dystrybucji**: Jeśli jest bazą danych w przypadku każdej partycji zasobów jest następnie dystrybuowana do tych regionów. Partycje zasobów będący właścicielem tego samego zestawu kluczy w różnych regionach formularza *zestawem partycji* (zobacz [powyższej ilustracji](#ThroughputGuarantees)).  Partycje zasobów w ramach zestawu partycji są koordynowane w różnych regionach skojarzonego z bazą danych przy użyciu replikacji maszyny stanu. W zależności od poziomu spójności skonfigurowana partycje zasobów w ramach zestawu partycji są skonfigurowane dynamicznie za pomocą różnych topologiach (na przykład gwiazdka, łańcucha, drzewa itp.). 

Na podstawie zarządzania szybkiego partycji, równoważenia obciążenia i zarządzanie ograniczeniami zasobów bazy danych Azure rozwiązania Cosmos umożliwia elastycznie skalowalnego przepływności w różnych regionach platformy Azure skojarzone z bazy danych Azure rozwiązania Cosmos kontener lub bazy danych. Zmiana udostępnionej przepływności jest operacja czasu wykonywania w usłudze Azure DB rozwiązania Cosmos. Podobnie jak inne operacje bazy danych, bazy danych Azure rozwiązania Cosmos gwarantuje bezwzględną górna granica na czas oczekiwania na żądanie zmiany udostępnionej przepływności. Na przykład na poniższej ilustracji przedstawiono kontenera klienta z elastycznie udostępnionej przepływności (z zakresu od 1M - 10M żądań na sekundę w dwóch regionach) na podstawie zapotrzebowania.

![Azure DB rozwiązania Cosmos elastycznie elastycznie przepływności](./media/distribute-data-globally/elastic-throughput.png)

**Kontener klienta z elastycznie udostępnionej przepływności (różnią się między 1M - 10M żądania/s)**

### <a id="ThroughputAndConsistency"></a>Przepływność w relacji z spójności 
Jest to ten sam zgodnie z opisem w [relacji w spójności z przepływnością](#ConsistencyAndThroughput).

### <a id="ThroughputAndAvailability"></a>Przepływność w relacji o dostępności
Azure DB rozwiązania Cosmos w dalszym ciągu obsługi jego wysokiej dostępności podczas wprowadzania zmian do udostępnionej przepływności. Azure DB rozwiązania Cosmos niewidocznie zarządza partycje zasobów (i wykonuje podziału, scalania i klonowania operacji) i zapewnia, że operacje zmniejsza wydajność i dostępność, gdy aplikacja elastycznie zwiększa lub zmniejsza przepływności. 

## <a id="AvailabilityGuarantees"></a>Gwarancje dostępności
Azure DB rozwiązania Cosmos oferuje dostępności 99,99% umowy SLA dla wszystkich kont bazy danych w pojedynczym regionie i wszystkich kont w przypadku swobodna spójności i 99,999% dostępności na wszystkich kontach w przypadku bazy danych. Zgodnie z wcześniejszym opisem gwarancje dostępności Azure rozwiązania Cosmos DB obejmują bezwzględną górna granica opóźnienie dla każdej operacji płaszczyzna danych i kontroli. Gwarancje dostępności nie zmienia się wiele regionów lub położenia geograficznego między regionami. Gwarancje dostępności są stosowane względem zarówno ręcznego jak i automatycznego przechodzenia w tryb failover. Azure DB rozwiązania Cosmos oferuje przezroczysty wielu interfejsów API, które Sprawdź, czy aplikacja może działać względem punktów końcowych logicznych i niewidocznie może kierować żądań do nowego regionu podczas pracy awaryjnej. Aplikacja nie potrzeba ponownego wdrożenia w przypadku regionalnej pracy awaryjnej i dostępność SLA są zachowywane przez cały czas.

### <a id="AvailabilityAndConsistency"></a>Relacja dostępności w spójności, opóźnienia i przepływności
Relacja dostępności w spójności, opóźnienia i przepływność jest opisane w sekcjach [relacji w spójności z dostępności](#ConsistencyAndAvailability), [relacji czas oczekiwania na dostępność](#LatencyAndAvailability) i [Przepływności w relacji z dostępnością](#ThroughputAndAvailability). 

## <a id="CustomerFacingSLAMetrics"></a>Metryki umowy SLA skierowane do klienta
Azure DB rozwiązania Cosmos niewidocznie przedstawia metryki przepływności, opóźnienia, spójność i dostępności. Te metryki są dostępne programowo i za pośrednictwem portalu Azure (zobacz rysunek poniżej). Można również Konfigurowanie alertów dla różnych progów przy użyciu usługi Azure Application Insights.
 

![Azure DB rozwiązania Cosmos widoczne klienta metryki umowy dotyczącej poziomu usług](./media/distribute-data-globally/customer-slas.png)

**Metryki spójności, czas oczekiwania, przepływności i dostępności są niewidocznie dostępne dla każdego dzierżawcy**

## <a id="Next Steps"></a>Następne kroki
* Aby zaimplementować globalnej replikacji na Twoim koncie Azure DB rozwiązania Cosmos przy użyciu portalu Azure, zobacz [sposób wykonywania replikacji globalna baza danych bazy danych rozwiązania Cosmos Azure przy użyciu portalu Azure](tutorial-global-distribution-sql-api.md).
* Informacje na temat sposobu wdrażania wielu wzorców architektury z bazy danych rozwiązania Cosmos Azure, zobacz [architektury wielu głównej bazy danych z bazy danych Azure rozwiązania Cosmos](multi-region-writers.md).
* Aby dowiedzieć się więcej na temat sposobu automatycznej i ręcznej pracy awaryjnej pracy w usłudze Azure DB rozwiązania Cosmos, zobacz [regionalnej pracy w trybie Failover w usłudze Azure DB rozwiązania Cosmos](regional-failover.md).

## <a id="References"></a>Odwołania
1. Marek Brewera. [Kierunku niezawodne systemów rozproszonych](https://people.eecs.berkeley.edu/~brewer/cs262b-2004/PODC-keynote.pdf)
2. Marek Brewera. [Zakończenie dwunastu lat później — jak zostały zmienione reguły](http://informatik.unibas.ch/fileadmin/Lectures/HS2012/CS341/workshops/reportsAndSlides/PresentationKevinUrban.pdf)
3. Gilbert, Lynch. - [Brewera&#39;przypuszczeń s oraz możliwości spójny, dostępnych usług sieci Web na uszkodzenia partycji](http://www.glassbeam.com/sites/all/themes/glassbeam/images/blog/10.1.1.67.6951.pdf)
4. Daniel Abadi. [Wady i zalety spójności w Modern rozproszonych projektu systemów bazy danych](http://cs-www.cs.yale.edu/homes/dna/papers/abadi-pacelc.pdf)
5. Pole Kleppmann. [Zatrzymaj wywoływanie bazy danych CP lub region](https://martin.kleppmann.com/2015/05/11/please-stop-calling-databases-cp-or-ap.html)
6. Peterowi Bailis i wsp. [Spójność powiązanej nieaktualności prawdopodobieństwa (PBS) do praktycznych częściowe kworum](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
7. Naor i wełny. [Obciążenia, wydajności i dostępności w systemach kworum](http://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf)
8. Herlihy i następującą. [Lineralizability: Warunek poprawności równoczesnych obiektów](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf)
9. [Umowa SLA DB Azure rozwiązania Cosmos](https://azure.microsoft.com/support/legal/sla/cosmos-db/)
