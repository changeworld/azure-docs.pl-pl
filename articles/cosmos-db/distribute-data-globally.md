---
title: Dystrybucja danych globalnie z bazy danych Azure rozwiązania Cosmos | Dokumentacja firmy Microsoft
description: Więcej informacji na temat skali planety — replikacja geograficzna, trybu failover i odzyskiwanie danych za pomocą globalnej bazy danych z bazy danych z rozwiązania Cosmos platformy Azure, usługa globalnie rozproszone, mutli modelu bazy danych.
services: cosmos-db
documentationcenter: ''
author: arramac
manager: kfile
ms.assetid: ba5ad0cc-aa1f-4f40-aee9-3364af070725
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2018
ms.author: arramac
ms.openlocfilehash: 588eb4df9555f20fe4193e8ae34ad51c17921db2
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="how-to-distribute-data-globally-with-azure-cosmos-db"></a>Sposób rozpowszechniania danych globalnie z bazy danych Azure rozwiązania Cosmos
Azure to powszechny — ma wpływu globalnych w regionach geograficznych 30 + i jest stale rozszerzanie. Z jego obecność na całym świecie jeden zróżnicowanych funkcji, które platforma Azure oferuje deweloperom jego jest możliwość tworzenia, wdrażania i łatwo Zarządzaj aplikacjami globalnie rozproszone. 

[Azure Cosmos DB](../cosmos-db/introduction.md) to rozproszona globalnie wielomodelowa usługa bazy danych firmy Microsoft do aplikacji o krytycznym znaczeniu. Azure DB rozwiązania Cosmos zapewnia gotowe dystrybucję globalnych, [elastyczne skalowanie przepływność i magazyn](../cosmos-db/partition-data.md) na całym świecie, jednocyfrowej milisekundy opóźnienia w 99-ty percentyl [pięć dobrze zdefiniowane poziomy spójności](consistency-levels.md), zagwarantować wysokiej dostępności, wszystkie kopie przez [SLA branży](https://azure.microsoft.com/support/legal/sla/cosmos-db/). Usługa Azure Cosmos DB [automatycznie indeksuje dane](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) bez konieczności zarządzania schematami i indeksami. To usługa wielomodelowa, obsługująca modele dokumentowe, klucz-wartość, wykresy i kolumny. Jako usługa urodzony chmury Azure DB rozwiązania Cosmos jest dokładnie odtwarzane z wielu dzierżawców i globalnych dystrybucji od podstaw się.

**Jednej kolekcji bazy danych Azure rozwiązania Cosmos podzielona na partycje i rozpowszechniane w różnych regionach platformy Azure**

![Azure DB rozwiązania Cosmos kolekcji podzielone na partycje i rozproszone na trzech regionów](./media/distribute-data-globally/global-apps.png)

Jak ma dowiedzieliśmy się podczas tworzenia bazy danych Azure rozwiązania Cosmos, Dodawanie globalnego dystrybucji nie może być zbagatelizowane — nie może być "skręcania w" nad system bazy danych "w jednej lokacji". Możliwości oferowane przez globalnie rozproszoną bazę danych span poza z tradycyjnego geograficzne po awarii odzyskiwania (geograficznie DR) oferowane przez "pojedynczej lokacji" baz danych. Baz danych w jednej lokacji oferty DR geograficznie możliwości są podzbiorem strict globalnie rozproszone baz danych. 

Z rozkładem globalne gotowe Azure rozwiązania Cosmos DB, programiści nie muszą do tworzenia własnych szkieletów replikacji przez wykorzystanie jednej wzorzec Lambda (na przykład [DynamoDB usług AWS replikacji](https://github.com/awslabs/dynamodb-cross-region-library/blob/master/README.md)) za pośrednictwem dziennika bazy danych lub za pomocą ćwiczeń " Podwójna zapisuje"w różnych regionach. Nie zaleca się zastosowanie powyższych strategii ponieważ nie jest możliwe zapewnić poprawność takiego podejścia i podaj SLA dźwięku. 

W tym artykule udostępniamy omówienie funkcji globalnych dystrybucji Azure rozwiązania Cosmos DB. Opisano również Azure rozwiązania Cosmos DB unikatowy podejście do zapewnienia kompleksowej umów SLA. 

## <a id="EnableGlobalDistribution"></a>Włączanie gotowe dystrybucji globalne
Azure DB rozwiązania Cosmos udostępnia następujące funkcje do tworzenia aplikacji w skali planety. Te funkcje są dostępne za pośrednictwem opartej na dostawcy zasobów Azure DB rozwiązania Cosmos [interfejsów API REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/) oraz portalu Azure.

W poniższego klipu wideo Andrew Liu Menedżera programów DB rozwiązania Cosmos Azure przedstawiono funkcje gotowe dystrybucji globalnego.

> [!VIDEO https://www.youtube.com/embed/1D06yjTVxt8]
>

### <a id="RegionalPresence"></a>Wszechobecne regionalne 
Azure stale rośnie jego obecność geograficzne przełączając [nowych regionów](https://azure.microsoft.com/regions/) online. Azure DB rozwiązania Cosmos jest dostępna we wszystkich regionach platformy Azure nowe domyślnie. Dzięki temu można skojarzyć z konta bazy danych Azure DB rozwiązania Cosmos region geograficzny, jak Azure zostanie otwarty nowy region dla firm.

### <a id="UnlimitedRegionsPerAccount"></a>Kojarzenie nieograniczoną liczbę regionów z konta bazy danych Azure DB rozwiązania Cosmos
Azure DB rozwiązania Cosmos można skojarzyć dowolną liczbę regiony platformy Azure przy użyciu konta bazy danych Azure DB rozwiązania Cosmos. Poza ograniczenia grodzenia (na przykład Chin, Niemcy) nie istnieją ograniczenia liczby regionów, które mogą być powiązane z Twoim kontem bazy danych Azure DB rozwiązania Cosmos. Na poniższej ilustracji przedstawiono konta bazy danych skonfigurowane zakresu w 25 regionach platformy Azure.  

**Dzierżawy Azure rozwiązania Cosmos DB bazy danych konta rozszerzania regiony platformy Azure 25**

![Konto bazy danych DB rozwiązania Cosmos Azure spanning 25 regiony platformy Azure](./media/distribute-data-globally/spanning-regions.png)

### <a id="PolicyBasedGeoFencing"></a>Oparte na zasadach grodzenia
Azure DB rozwiązania Cosmos jest przeznaczona do mają możliwości grodzenia opartych na zasadach. Grodzenia jest istotnym elementem do zapewnienia ograniczenia dotyczące zarządzania i zgodności danych i spowodować, że skojarzenie określonego regionu z Twoim kontem. Przykłady grodzenia obejmują (ale nie są ograniczone do), zakresu globalnego dystrybucji do regionów, w chmurze suwerennych (na przykład Chin i Niemczech) lub w obrębie granicy podatkowych dla instytucji rządowych (na przykład Australia). Zasady są kontrolowane przy użyciu metadanych subskrypcji platformy Azure.

### <a id="DynamicallyAddRegions"></a>Dynamicznie dodawać i usuwać regionów
Azure DB rozwiązania Cosmos umożliwia (skojarzenia) Dodaj lub Usuń (skojarzenie) regionów konta bazy danych w dowolnym momencie (zobacz [powyższej ilustracji](#UnlimitedRegionsPerAccount)). Ze względu na replikację danych między partycjami równolegle, bazy danych rozwiązania Cosmos Azure zapewnia, że gdy nowy region do trybu online, Azure DB rozwiązania Cosmos jest dostępny w ciągu 30 minut wszędzie na świecie dla maksymalnie 100 tabel. 

### <a id="FailoverPriorities"></a>Priorytetów trybu failover
Do sterowania dokładna kolejność regionalnej pracy w trybie Failover, po wielu regionalnej awarii, bazy danych rozwiązania Cosmos Azure umożliwia skojarzenie priorytet do różnych regionach skojarzone z bazy danych konta (zobacz poniższy rysunek). Azure DB rozwiązania Cosmos gwarantuje, że sekwencja automatycznej pracy awaryjnej występuje w podanej kolejności priorytet. Aby uzyskać więcej informacji na temat regionalnej pracy w trybie Failover, zobacz [automatyczne regionalnej pracy w trybie Failover dla ciągłość prowadzenia działalności biznesowej w usłudze Azure DB rozwiązania Cosmos](regional-failover.md).

**Dzierżawy Azure DB rozwiązania Cosmos można skonfigurować kolejność priorytetów trybu failover (po prawej) dla regionów skojarzone z kontem bazy danych**

![Konfigurowanie priorytetów trybu failover z bazy danych Azure rozwiązania Cosmos](./media/distribute-data-globally/failover-priorities.png)

### <a id="ConsistencyLevels"></a>Wiele, modeli spójności dobrze zdefiniowany dla globalnie zreplikowanych baz danych
Udostępnia bazę danych systemu Azure rozwiązania Cosmos [wielu dobrze zdefiniowane poziomy spójności](consistency-levels.md) przez umowy SLA. Można wybrać model określonych spójności (z listy dostępnych opcji) w zależności od obciążenia/scenariuszy. 

### <a id="TunableConsistency"></a>Dostosowywalne spójności globalnie zreplikowanych baz danych
Azure DB rozwiązania Cosmos umożliwia programowo zastąpienia i zwalnia domyślny wybór spójności na podstawie danego żądania w czasie wykonywania. 

### <a id="DynamicallyConfigurableReadWriteRegions"></a>Dynamicznie konfigurować odczytu i zapisu regionów
Azure DB rozwiązania Cosmos umożliwia konfigurowanie regionów (skojarzonego z bazą danych) "do odczytu", "write" lub "odczytu/zapisu" regionów. 

### <a id="ElasticallyScaleThroughput"></a>Elastycznie skalować przepływność w regionach platformy Azure
Kolekcja bazy danych Azure rozwiązania Cosmos można elastycznie skalować przez przepływności inicjowania obsługi administracyjnej programowo. Przepływność jest stosowany do wszystkich regionów, które kolekcji jest dystrybuowane w.

### <a id="GeoLocalReadsAndWrites"></a>Lokalnych Geo odczyty i zapisy
Najważniejszą korzyścią globalnie rozproszoną bazę danych jest oferowanie małe opóźnienia dostępu do danych o miejscu na świecie. Azure DB rozwiązania Cosmos zapewnia małe opóźnienia gwarancji P99 dla różnych operacji bazy danych. Gwarantuje, że wszystkie operacje odczytu są kierowane do najbliższego lokalny region odczytu. Do obsługi żądań odczytu, kworum lokalnego regionu, w którym zostało wystawione odczytu jest używany; to samo dotyczy zapisywania. Zapis zostaje potwierdzony tylko wtedy, gdy większość replik trwale przeprowadziła zapisu lokalnie, ale bez jest uzyskiwany w replikach zdalnego potwierdzić zapisy. Umieść inaczej, protokół replikacji bazy danych Azure rozwiązania Cosmos działa z założeniem, że kworum odczytu i zapisu zawsze znajdują się lokalnie do odczytu i zapisu regionach, odpowiednio wygenerowania żądania.

### <a id="ManualFailover"></a>Ręcznie zainicjować regionalnej pracy awaryjnej
Azure DB rozwiązania Cosmos pozwala na zainicjowanie pracy awaryjnej konta bazy danych, aby sprawdzić poprawność *kompleksowe* właściwości dostępności dla całej aplikacji (poza bazą danych). Ponieważ ma gwarancji bezpieczeństwa i liveness właściwości wyborów wykrywania i wiodące awarii, bazy danych Azure rozwiązania Cosmos gwarantuje *utracie zero* operacji zainicjował dzierżawy ręcznego przełączania trybu failover.

### <a id="AutomaticFailover"></a>Automatycznej pracy awaryjnej
Azure DB rozwiązania Cosmos obsługuje automatycznej pracy awaryjnej w przypadku co najmniej jeden regionalnej awarii. Podczas regionalnej pracy awaryjnej bazy danych Azure rozwiązania Cosmos przechowuje opóźnienie odczytu, dostępność przestojów, spójność i przepływności umów SLA. Azure DB rozwiązania Cosmos zapewnia górna granica czasu na ukończenie operacji automatycznej pracy awaryjnej. To okno o utracie danych podczas regionalnej awarii.

### <a id="GranularFailover"></a>Przeznaczona dla innego trybu failover szczegółowości
Obecnie funkcji automatycznej i ręcznej pracy awaryjnej są widoczne na poziom szczegółowości konta bazy danych. Uwaga: wewnętrznie rozwiązania Cosmos bazy danych Azure oferuje proste *automatyczne* pracy awaryjnej dokładniej bazy danych, kolekcji lub nawet partycji (kolekcji będącej właścicielem, jeśli zakres kluczy). 

### <a id="MultiHomingAPIs"></a>Wielu interfejsów API w Azure rozwiązania Cosmos bazy danych
Azure DB rozwiązania Cosmos pozwala korzystać z bazą danych przy użyciu jednej logicznej (niezależny od regionu) lub fizycznych (określonego regionu) punktów końcowych. Użycie logiczne punkty końcowe gwarantuje, czy aplikacja może być przezroczysty wieloadresowego w przypadku trybu failover. Ostatnie fizycznego punktów końcowych, podaj szczegółową kontrolę do aplikacji, aby przekierować odczytuje i zapisuje określonych regionach.

Informacje dotyczące sposobu konfigurowania preferencji odczytu można znaleźć [interfejsu API SQL](../cosmos-db/tutorial-global-distribution-sql-api.md), [interfejsu API programu Graph](../cosmos-db/tutorial-global-distribution-graph.md), [API tabeli](../cosmos-db/tutorial-global-distribution-table.md), i [API bazy danych MongoDB](../cosmos-db/tutorial-global-distribution-mongodb.md) w ich odpowiednich połączonych artykułów.

### <a id="TransparentSchemaMigration"></a>Migracja schematu i indeksu przejrzyste i spójności bazy danych 
Azure DB rozwiązania Cosmos jest w pełni [niezależny od schematu](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf). Unikatowa konstrukcja jego aparatu bazy danych pozwala na automatyczne i synchronicznie indeks wszystkich danych, który wysyła strumień go bez żadnego schematu lub indeksów pomocniczych od użytkownika. Dzięki temu można szybko przejść aplikacji rozproszonych globalnie bez obaw o migracji schematu i indeks bazy danych lub koordynowanie wdrożenia aplikacji fazy wielu zmian schematu. Azure DB rozwiązania Cosmos gwarantuje, że wszystkie zmiany do indeksowania jawnie wprowadzonych przez Ciebie zasad nie powoduje do pogorszenia się wydajności i dostępności.  

### <a id="ComprehensiveSLAs"></a>Kompleksowe umów SLA (poza tylko wysokiej dostępności)
Jako usługa globalnie rozproszoną bazę danych, bazy danych rozwiązania Cosmos Azure oferuje dobrze zdefiniowany umowy SLA dla **utraty danych**, **dostępności**, **czas oczekiwania na P99**, **przepływności**  i **spójności** dla bazy danych jako całości, niezależnie od liczby regionów skojarzonego z bazą danych.  

## <a id="LatencyGuarantees"></a>Gwarancje opóźnienia
Najważniejszą korzyścią usług globalnie rozproszoną bazę danych, takich jak bazy danych Azure rozwiązania Cosmos jest oferowanie małe opóźnienia dostępu do danych w dowolnym miejscu na świecie. Azure DB rozwiązania Cosmos oferuje gwarantowane małe opóźnienia w P99 dla różnych operacji bazy danych. Protokół replikacji bazy danych Azure rozwiązania Cosmos używającego zapewnia, że operacje bazy danych (w idealnym przypadku odczytuje i zapisuje) są zawsze wykonywane w regionie lokalnego do klienta. Opóźnienie umowy SLA platformy Azure DB rozwiązania Cosmos obejmuje P99 odczytów, zapisów (synchronicznie) indeksowane i zapytania o różnych rozmiarach żądań i odpowiedzi. Gwarancje opóźnienia zapisów obejmują zatwierdzeń kworum Większość trwałe w lokalnym centrum danych.

### <a id="LatencyAndConsistency"></a>Czas oczekiwania w relacji z spójności 
Globalnie rozproszone usługi zapewniające wysoki poziom spójności w Instalatorze globalnie rozproszone, należy go replikowane synchronicznie zapisuje lub synchroniczne wykonać odczyty między region — szybkości jasny i niezawodność sieci rozległej dyktowania to strong spójność powoduje zmniejszenie dostępności operacji bazy danych i większych opóźnień. W związku z tym w celu dostarczenia małych opóźnień w P99 i dostępności 99,99% dla wszystkich kont w pojedynczym regionie i wszystkich kont w przypadku dzięki zastosowaniu swobodna spójności i 99,999% odczyt dostępności na wszystkich kontach w przypadku bazy danych, należy stosować usługę asynchroniczną replikację. Wymaga to w Włącz usługę musi oferują [choice(s) spójności dobrze zdefiniowany, swobodna](consistency-levels.md) — mniejsze niż silne (oferowanie niski gwarancje dostępności i opóźnienia), jak i w idealnym przypadku mocniejszy niż element spójność "ostateczna" (do oferuje intuicyjny model programowania).

Azure DB rozwiązania Cosmos zapewnia operacja odczytu nie jest wymagane do kontaktowania się z replik w różnych regionach dostarczać gwarancji poziomu spójności określone. Podobnie zapewnia, że operacja zapisu nie pobrać blokowane podczas replikacji danych we wszystkich regionach (tj. zapisy są asynchronicznie replikowane w regionach). Dla konta bazy danych w przypadku wielu poziomów spójności swobodna są dostępne. 

### <a id="LatencyAndAvailability"></a>Czas oczekiwania w relacji o dostępności 
Opóźnienia i dostępności są dwie strony tego samego monety. Omawianiu opóźnienia operacji w stanie stabilności i dostępności w wypadku awarii. Z punktu widzenia aplikacji powolne uruchomioną operację bazy danych jest nierozróżnialne z bazy danych, która jest niedostępna. 

Aby odróżnić duże opóźnienie od niedostępności, bazy danych Azure rozwiązania Cosmos umożliwia bezwzględną górna granica opóźnienia różne operacje bazy danych. Jeśli operacja bazy danych trwa dłużej niż górna granica do ukończenia, bazy danych Azure rozwiązania Cosmos zwraca błąd upływu limitu czasu. SLA dostępności bazy danych Azure rozwiązania Cosmos gwarantuje, że limity czasu są uwzględniane dostępności umowy dotyczącej poziomu usług. 

### <a id="LatencyAndThroughput"></a>Czas oczekiwania w relacji o przepływności
Azure DB rozwiązania Cosmos nie powoduje możesz wybrać opóźnienia i przepływności. Uwzględnia ona zdefiniowane umowy SLA dla obu opóźnienia P99 i dostarczyć przepływności, które zostały udostępnione. 

## <a id="ConsistencyGuarantees"></a>Gwarancje spójności
Gdy [modelu wysoki poziom spójności](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) jest standardem złota z programowaniem, znajduje się na problemy z ich opanowaniem ceny większego opóźnienia (w stanie stabilności) i zmniejszona dostępność (w wypadku awarii). 

Azure DB rozwiązania Cosmos oferuje dobrze zdefiniowany model programowania użytkownikowi przeglądanie informacji o spójności replikowanych danych. Aby włączyć tworzenie aplikacji wieloadresowych, modeli spójności udostępnianych przez bazy danych Azure rozwiązania Cosmos są przeznaczone do być niezależny od regionu i nie zależą od regionu, z którym odczyty i zapisy są obsługiwane. 

Spójności Azure DB rozwiązania Cosmos umowy dotyczącej poziomu usług gwarantuje, że 100% żądań odczytu spełniającą gwarancji spójności dla poziomu spójności żądanie użytkownika (domyślny poziom spójności na konto bazy danych) albo wartość zastąpiona na żądanie. Żądanie odczytu jest uważana za osiągnięcia SLA spójności, jeśli spełnione są wszystkie skojarzone z poziomu spójności gwarancje spójności. Poniższa tabela zawiera spójność gwarantuje, które odpowiadają poziomy spójności określonych oferowane przez bazy danych Azure rozwiązania Cosmos.

**Gwarancje spójności skojarzone z poziomu danego spójności w usłudze Azure DB rozwiązania Cosmos**

<table>
    <tr>
        <td><strong>Poziomu spójności</strong></td>
        <td><strong>Właściwości spójności</strong></td>
        <td><strong>Umowa SLA</strong></td>
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
        <td>Prefiks spójne</td>
        <td>Prefiks spójne</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>Silna</td>
        <td>Linearizable</td>
        <td>100%</td>
    </tr>
</table>

### <a id="ConsistencyAndAvailability"></a>Relacji w spójności z dostępności
[Wynik niemożności](http://www.glassbeam.com/sites/all/themes/glassbeam/images/blog/10.1.1.67.6951.pdf) z [Newtona zakończenia](https://people.eecs.berkeley.edu/~brewer/cs262b-2004/PODC-keynote.pdf) potwierdza, że jest to niemożliwe do pozostają dostępne i oferują linearizable spójności w wypadku awarii systemu. Usługa bazy danych należy wybrać region lub CP - systemów CP zrezygnujesz z dostępności na rzecz linearizable spójności podczas zrezygnujesz z systemów region [linearizable spójności](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) na rzecz dostępności. Azure DB rozwiązania Cosmos nigdy nie narusza poziomu spójności żądanego, dzięki czemu formalnie systemu CP. Jednak w praktyce spójności nie jest wszystkie lub żadne oferty — istnieją wielu modeli dobrze zdefiniowany spójności wzdłuż spektrum spójności między linearizable i ewentualnej spójności. W usłudze Azure DB rozwiązania Cosmos próbowaliśmy zostały pokazane kilka modeli swobodna spójności z zastosowania rzeczywistych i intuicyjne modelu programowania. Azure DB rozwiązania Cosmos przechodzi skutków ubocznych spójności dostępności, oferując [wielu rozluźnić jeszcze dobrze zdefiniowane poziomy spójności](consistency-levels.md) i o dostępności 99,99% dla wszystkich kont w pojedynczym regionie i wszystkich kont w przypadku z rozluźnić spójności i 99,999% odczytu dostępności na wszystkich kontach w przypadku bazy danych. 

### <a id="ConsistencyAndAvailability"></a>Relacja w spójności z opóźnieniem
Bardziej zaawansowane odmianą zakończenia został podanymi przez Abadi Danielowi Prof. i jest ona wywoływana [PACELC](http://cs-www.cs.yale.edu/homes/dna/papers/abadi-pacelc.pdf), która uwzględnia również wpływ na opóźnienie i spójności w stanie stabilności. Stany go, że w stanie stabilności system bazy danych należy wybrać opcję spójności i opóźnień. Z wielu modeli swobodna spójności (obsługiwana przez asynchroniczną replikację i lokalne odczytu, zapisu kworum) bazy danych Azure rozwiązania Cosmos zapewnia wszystkie odczyty i zapisy są lokalne do odczytu i zapisu odpowiednio regionów.  Dzięki temu Azure DB rozwiązania Cosmos oferowanie gwarancje małe opóźnienia w obrębie regionu poziomów spójności.  

### <a id="ConsistencyAndThroughput"></a>Relacji w spójności z przepływnością
Ponieważ wybór zależy od implementacji modelu określonego spójności [typu kworum](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf), przepływność również w zależności od wyboru spójności. Na przykład w usłudze Azure DB rozwiązania Cosmos, RU dla odczytów silnie spójne jest około dwa razy, że z ostatecznie spójne odczytuje. W takim przypadku należy udostępnić double RUs w kolekcji do osiągnięcia tej samej przepływności.
 
**Relacja odczytu pojemności dla poziomu spójności określonych w usłudze Azure DB rozwiązania Cosmos**

![Relacja między spójności i przepustowość](./media/distribute-data-globally/consistency-and-throughput.png)

## <a id="ThroughputGuarantees"></a>Gwarancje przepustowości 
Azure DB rozwiązania Cosmos pozwala na skali przepływności (a także, magazynu), elastycznie w różnych regionach, w zależności od zapotrzebowania. 

**Jednej kolekcji bazy danych Azure rozwiązania Cosmos podzielonym na partycje (w trzech odłamków), a następnie dystrybuowana do trzech regiony platformy Azure**

![Azure DB rozwiązania Cosmos rozproszonych i na partycje w kolekcjach](../cosmos-db/media/introduction/azure-cosmos-db-global-distribution.png)

Kolekcji usługi Azure DB rozwiązania Cosmos pobiera dystrybuowane za pomocą dwóch wymiarów — w obrębie regionu, a następnie w regionach. Oto kroki tej procedury: 

* W jednym regionie kolekcji usługi Azure DB rozwiązania Cosmos jest skalowana w poziomie pod względem zasobów partycji. Każda partycja zasobów zarządza zestawu kluczy i jest silnie spójne i wysokiej dostępności, ze względu na stan replikacji maszyny z zestawu replik. Azure DB rozwiązania Cosmos jest systemem zasobów postanowieniom pełni której partycję zasobu jest odpowiedzialny za dostarczanie swój udział przepływności budżet przydzielony zasobów systemowych. Skalowanie kolekcji usługi Azure DB rozwiązania Cosmos jest całkowicie niewidoczne — bazy danych Azure rozwiązania Cosmos zarządza partycje zasobów i dzieli i scala go zgodnie z potrzebami. 
* Każdej partycji zasobów jest dystrybuowane w różnych regionach. Partycje zasobów będący właścicielem tego samego zestawu kluczy w różnych regionach tworzą zestaw partycji (zobacz [powyższej ilustracji](#ThroughputGuarantees)).  Partycje zasobów w ramach zestawu partycji są koordynowane przy użyciu stan replikacji maszyny w różnych regionach. W zależności od poziomu spójności skonfigurowana partycje zasobów w ramach zestawu partycji są skonfigurowane dynamicznie za pomocą różnych topologiach (na przykład gwiazdka, łańcucha, drzewa itp.). 

Na podstawie zarządzania szybkiego partycji, równoważenia obciążenia i zarządzanie ograniczeniami zasobów bazy danych Azure rozwiązania Cosmos umożliwia elastycznie skalowalnego przepływności w różnych regionach platformy Azure w kolekcji usługi Azure DB rozwiązania Cosmos. Zmiana przepływności na kolekcję jest operacja czasu wykonywania w usłudze Azure DB rozwiązania Cosmos — tak jak z innymi operacjami bazy danych Azure DB rozwiązania Cosmos gwarantuje bezwzględną górna granica na czas oczekiwania na żądanie zmiany przepływności. Na przykład na poniższej ilustracji przedstawiono kolekcji klienta z elastycznie udostępnionej przepływności (z zakresu od 1M - 10M żądań na sekundę w dwóch regionach) na podstawie zapotrzebowania.
 
**Kolekcja klienta o elastycznie udostępnionej przepływności (1 mln 10M żądania/s)**

![Azure DB rozwiązania Cosmos elastycznie elastycznie przepływności](./media/distribute-data-globally/elastic-throughput.png)

### <a id="ThroughputAndConsistency"></a>Przepływność w relacji z spójności 
Taki sam jak [relacji w spójności z przepływnością](#ConsistencyAndThroughput).

### <a id="ThroughputAndAvailability"></a>Przepływność w relacji o dostępności
Azure DB rozwiązania Cosmos w dalszym ciągu Obsługa jego dostępność podczas wprowadzania zmian przepływności. Azure DB rozwiązania Cosmos niewidocznie zarządza partycji (na przykład podziału, scalenie, operacji klonowania) i zapewnia, że operacje zmniejsza wydajność i dostępność, gdy aplikacja elastycznie zwiększa lub zmniejsza przepływności. 

## <a id="AvailabilityGuarantees"></a>Gwarancje dostępności
Azure DB rozwiązania Cosmos oferuje dostępności 99,99% umowy SLA dla wszystkich kont w pojedynczym regionie i wszystkich kont w przypadku z swobodna spójności i 99,999% odczytać dostępności na wszystkich kontach w przypadku bazy danych. Zgodnie z wcześniejszym opisem gwarancje dostępności Azure rozwiązania Cosmos DB obejmują bezwzględną górna granica opóźnienie dla każdej operacji płaszczyzna danych i kontroli. Gwarancje dostępności są steadfast i nie zmienia się wiele regionów lub położenia geograficznego między regionami. Gwarancje dostępności zastosowanie zarówno ręcznego jak również, automatycznej pracy awaryjnej. Azure DB rozwiązania Cosmos oferuje przezroczysty wielu interfejsów API, które Sprawdź, czy aplikacja może działać względem punktów końcowych logicznych i niewidocznie może kierować żądań do nowego regionu w przypadku trybu failover. Umieść inaczej, aplikacja nie potrzeba ponownego wdrożenia na regionalnej pracy awaryjnej i umów SLA dostępności, które są obsługiwane.

### <a id="AvailabilityAndConsistency"></a>Relacja dostępności w spójności, opóźnienia i przepływności
Relacja dostępności w spójności, opóźnienia i przepływność jest opisana w [relacji w spójności z dostępności](#ConsistencyAndAvailability), [relacji czas oczekiwania na dostępność](#LatencyAndAvailability) i [Przepływności w relacji z dostępnością](#ThroughputAndAvailability). 

## <a id="GuaranteesAgainstDataLoss"></a>Gwarancje i zachowanie systemowe dla "utraty danych"
W usłudze Azure DB rozwiązania Cosmos każdej partycji (kolekcja) jest udostępniane dużej liczby replik, które są rozkładane między co najmniej 10-20 domen błędów. Zapisuje wszystkie są synchronicznie i trwałym zatwierdzeniu przez większość kworum replik przed uznanych do klienta. Replikacji asynchronicznej jest stosowana z koordynacji między partycjami rozprzestrzeniać się w różnych regionach. Azure DB rozwiązania Cosmos gwarantuje, że istnieje nie powoduje utraty danych ręcznej pracy awaryjnej zainicjował dzierżawy. Podczas automatycznej pracy awaryjnej bazy danych Azure rozwiązania Cosmos gwarantuje górna granica zakresu skonfigurowanych spójność powiązanej nieaktualności okno utraty danych w ramach jego umowy dotyczącej poziomu usług.

## <a id="CustomerFacingSLAMetrics"></a>Metryki umowy SLA skierowane do klienta
Azure DB rozwiązania Cosmos niewidocznie przedstawia metryki przepływności, opóźnienia, spójność i dostępności. Te metryki są dostępne programowo i za pośrednictwem portalu Azure (patrz niżej rysunku). Można również Konfigurowanie alertów dla różnych progów przy użyciu usługi Azure Application Insights.
 
**Metryki spójności, czas oczekiwania, przepływności i dostępności są niewidocznie dostępne dla każdego dzierżawcy**

![Azure DB rozwiązania Cosmos widoczne klienta metryki umowy dotyczącej poziomu usług](./media/distribute-data-globally/customer-slas.png)

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
