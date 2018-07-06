---
title: Globalna dystrybucja danych za pomocą usługi Azure Cosmos DB | Dokumentacja firmy Microsoft
description: Więcej informacji o skali globalnej replikacji geograficznej, trybu failover i danych odzyskiwanie z użyciem globalnej bazy danych z usługi Azure Cosmos DB, to usługa globalnie dystrybuowanej, odpowiednie model bazy danych.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: sngun
ms.openlocfilehash: dec981ad750a49646916dbef40a4cc632ab71da2
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2018
ms.locfileid: "37856644"
---
# <a name="how-to-distribute-data-globally-with-azure-cosmos-db"></a>Jak dystrybuować dane globalnie za pomocą usługi Azure Cosmos DB
Platforma Azure jest powszechnie stosowana — jej zasięgu globalnym w regionach geograficznych ponad 50 i stale powiększający się. Za pomocą jego obecność jedną zróżnicowane możliwości, które platforma Azure oferuje deweloperom jest możliwość tworzenie, wdrażanie i łatwe zarządzanie globalnie rozproszonych aplikacji. 

[Azure Cosmos DB](../cosmos-db/introduction.md) to rozproszona globalnie wielomodelowa usługa bazy danych firmy Microsoft do aplikacji o krytycznym znaczeniu. Usługa Azure Cosmos DB udostępnia gotowa do użycia funkcja dystrybucji globalnej, [elastyczne skalowanie przepływności i przestrzeni dyskowej](../cosmos-db/partition-data.md) na całym świecie, oznaczona jedną cyfrą milisekundowe opóźnienie w 99. percentylu, [pięć dokładnie zdefiniowanych modeli spójności](consistency-levels.md)oraz gwarantowaną wysoką dostępność, wspierane przez [wiodące w branży kompleksowe umowy SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/). Usługa Azure Cosmos DB [automatycznie indeksuje wszystkie dane](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) bez konieczności zarządzania schematami lub indeksami. Jest to usługa wielomodelowa i obsługuje dokumentów, pary klucz wartość, wykres i modeli danych rodzin kolumn. Jako natywnie urodzony w usłudze w chmurze Azure Cosmos DB dokładnie został zaprojektowany przy użyciu wielu dzierżawców i globalnej dystrybucji od podstaw się.


![Kontener usługi Azure Cosmos DB na partycje i rozproszonych w trzech regionach](./media/distribute-data-globally/global-apps.png)

**Jeden kontener usługi Azure Cosmos DB na partycje i rozproszone w wielu regionach platformy Azure**

Jak nauczyliśmy się podczas tworzenia usługi Azure Cosmos DB, dodawanie globalnej dystrybucji nie może być ekskluzywnym. Nie może być "skręcania jednokrotnego" na jego podstawie system bazy danych "w jednej lokacji". Możliwości oferowane przez globalnie rozproszonej bazy danych obejmują poza tradycyjnych awarii geograficznego odzyskiwania (Geo-DR) oferowane przez bazy danych "w jednej lokacji". Pojedyncza lokacja bazy danych oferuje możliwość Geo-DR to podzbiór globalnie rozproszonych baz danych. 

Dzięki gotowej do użycia dystrybucję globalną usługi Azure Cosmos DB, deweloperzy nie muszą tworzyć własne szkieletu replikacji przez wprowadzenie albo wzorzec Lambda (na przykład [replikacji usługi DynamoDB AWS](https://github.com/awslabs/dynamodb-cross-region-library/blob/master/README.md)) za pośrednictwem dziennika bazy danych lub przez w wielu regionach, wykonywania operacji "double zapisu". Robimy *nie* zaleca się takiego podejścia, ponieważ nie jest możliwe sprawdzić ich poprawność takiego podejścia i podaj dźwięku umowy SLA. 

W tym artykule zawiera omówienie funkcji dystrybucję globalną usługi Azure Cosmos DB. Ponadto opisano podejście unikatowy usługi Azure Cosmos DB, by zapewnić kompleksowe umowy SLA. 

## <a id="EnableGlobalDistribution"></a>Włączanie kompleksowa dystrybucja globalna
Usługa Azure Cosmos DB zapewnia następujące możliwości, aby umożliwić łatwe tworzenie aplikacji dystrybuowanych globalnie. Te możliwości są dostępne za pośrednictwem zasobów usługi Azure Cosmos DB, opartą na dostawcy [interfejsów API REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/) oraz witryny Azure portal.

Obejrzyj poniższy klip wideo, aby wyświetlić funkcję gotowej do użycia dystrybucję globalną w usłudze Azure Cosmos DB w działaniu.

> [!VIDEO https://www.youtube.com/embed/1D06yjTVxt8]
>

### <a id="RegionalPresence"></a>Wszechobecne regionalne 
Azure stale rośnie swojej obecności geograficznych, przenosząc [nowe regiony](https://azure.microsoft.com/regions/) online. Usługa Azure Cosmos DB jest klasyfikowana jako *usługi podstawowej* na platformie Azure i jest dostępny w wszystkich nowych regionów systemu Azure, domyślnie. Dzięki temu można skojarzyć z Twojego konta bazy danych Azure Cosmos DB region geograficzny, tak szybko, jak platforma Azure otworzy nowy region dla firm.

### <a id="UnlimitedRegionsPerAccount"></a>Kojarzenie nieograniczoną liczbę regiony z Twojego konta bazy danych Azure Cosmos DB
Usługa Azure Cosmos DB umożliwia kojarzenie dowolnej liczbie regionów platformy Azure z Twojego konta bazy danych Azure Cosmos DB. Poza ograniczenia grodzenia (na przykład, Chiny, Niemcy) nie istnieją ograniczenia liczby regionów, które mogą być powiązane z Twoim kontem bazy danych Azure Cosmos DB. Na poniższej ilustracji przedstawiono konto bazy danych skonfigurowane na 25 regionach platformy Azure.  

![Azure Cosmos DB konto bazy danych obejmujące 25 regionów platformy Azure](./media/distribute-data-globally/spanning-regions.png)

**Dzierżawy usługi Azure Cosmos DB, bazy danych konta rozszerzania 25 regionach świadczenia usługi Azure**


### <a id="PolicyBasedGeoFencing"></a>Grodzenia na podstawie zasad
Usługa Azure Cosmos DB jest przeznaczony do obsługi grodzenia na podstawie zasad. Grodzenia jest ważnym elementem zapewnienie nadzoru i zgodności ograniczenia danych i może uniemożliwić skojarzenie określonego regionu z Twoim kontem. Przykłady grodzenia obejmują (ale nie są ograniczone do) zakresu dystrybucję globalną w regionach w obrębie należących do suwerennej chmury (na przykład, Chinach i Niemczech) lub w granicach opodatkowania dla instytucji rządowych (na przykład, Australia). Zasady są kontrolowane za pomocą metadanych subskrypcji platformy Azure.

### <a id="DynamicallyAddRegions"></a>Dynamiczne dodawanie i usuwanie regionów
Usługa Azure Cosmos DB umożliwia (skojarzenia) Dodawanie lub usuwanie (usuwanie skojarzenia) regiony z Twojego konta bazy danych w dowolnym momencie (zobacz [poprzednim rysunku](#UnlimitedRegionsPerAccount)). Z równoległą replikację danych między partycjami, usługi Azure Cosmos DB gwarantuje, że po dodaniu nowego regionu, jest dostępna na potrzeby operacji w ciągu 30 minut dowolnym miejscu na świecie (zakładając, że dane to 100 TB lub mniej). 

### <a id="FailoverPriorities"></a>Priorytety trybu failover
Aby kontrolować dokładną sekwencję wybranych wywoływania regionalnego trybu failover w przypadku awarii, Azure Cosmos DB pozwala kojarzyć *priorytet* z różnych regionów skojarzonych z bazy danych konta (zobacz rysunek poniżej). Usługa Azure Cosmos DB zapewniają, że sekwencji automatycznej pracy awaryjnej odbywa się w podanej kolejności priorytetu. Aby uzyskać więcej informacji na temat wywoływania regionalnego trybu failover, zobacz [automatycznego wywoływania regionalnego trybu failover w celu zachowania ciągłości w usłudze Azure Cosmos DB](regional-failover.md).


![Konfigurowanie priorytetów trybu failover przy użyciu usługi Azure Cosmos DB](./media/distribute-data-globally/failover-priorities.png)

**Dzierżawy usługi Azure Cosmos DB można skonfigurować kolejność priorytetów trybu failover (w okienku po prawej stronie) dla regionów skojarzonych z kontem bazy danych**

### <a id="ConsistencyLevels"></a>Wiele dokładnie zdefiniowanych modeli spójności dla globalnie rozproszonych baz danych
Usługa Azure Cosmos DB obsługuje [wiele modeli spójności dobrze zdefiniowanych, intuicyjny i praktyczne](consistency-levels.md) wspieranej przez umowy SLA. Można wybrać model spójności określonych (z listy dostępnych opcji) w zależności od obciążenia/scenariusze. 

### <a id="TunableConsistency"></a>Dostosowania spójności dla globalnie replikowanego baz danych
Usługa Azure Cosmos DB umożliwia programowe zastąpienia i zwalnia domyślnego wyboru spójności na podstawie danego żądania w czasie wykonywania. 

### <a id="DynamicallyConfigurableReadWriteRegions"></a>Dynamiczną konfigurację odczytu i zapisu regionów
Usługa Azure Cosmos DB umożliwia skonfigurowanie regionach (skojarzone z bazy danych) dla "Odczyt", "write" lub regiony "odczytu/zapisu". 

### <a id="ElasticallyScaleThroughput"></a>Elastyczne skalowanie przepływności między regionami platformy Azure
Kontener usługi Azure Cosmos DB można elastycznie skalować, inicjowania obsługi administracyjnej przepływności programowo. Przepływność jest stosowany do wszystkich regionów, które kontenera usługi Azure Cosmos DB jest dystrybuowane w.

### <a id="GeoLocalReadsAndWrites"></a>Geo-local odczyty i zapisy
Zaletą globalnie rozproszona baza danych jest oferuje małe opóźnienia dostępu do danych w dowolnym miejscu na świecie. Usługa Azure Cosmos DB oferuje odczytów z małymi opóźnieniami i zapisuje w 99. percentylu na całym świecie. Zapewnia, że wszystkie operacje odczytu są obsługiwane z najbliższego regionu (lokalny). Aby obsługiwać żądania odczytu, kworum lokalnego do regionu, w którym zostało wystawione odczytu jest używany. Dotyczy to operacje zapisu. Zapis zostało potwierdzone, tylko wtedy, gdy większość replik trwale potwierdził zapisu lokalnie, ale bez są bramkowane na zdalnej repliki, aby potwierdzić zapisy. Aby przełączyć inaczej, protokołu replikacji usługi Azure Cosmos DB działa przy założeniu, że Kwora odczytu i zapisu są zawsze lokalnych do regionu, w którym żądanie zostało wydane.

### <a id="ManualFailover"></a>Ręczna praca awaryjna
Usługa Azure Cosmos DB umożliwia wyzwolić tryb failover konta bazy danych, aby sprawdzić poprawność *typu end to end* właściwości dostępności dla całej aplikacji (poza bazą danych). Ponieważ gwarantują bezpieczeństwo i właściwości żywotności wyborów wykrywania i lider awarii usługi Azure Cosmos DB gwarantuje *utracie zero* operacji zainicjowane dzierżawy ręcznej pracy awaryjnej.

### <a id="AutomaticFailover"></a>Automatyczny tryb failover
Usługa Azure Cosmos DB obsługuje automatycznej pracy awaryjnej w co najmniej jeden regionalnych przestojów. Podczas regionalnej pracy awaryjnej usługi Azure Cosmos DB obsługuje opóźnienie odczytu, czasu dostępności, spójności i przepływności umowy SLA. Usługa Azure Cosmos DB udostępnia górną granicę dla czasu trwania na zakończenie operacji automatycznej pracy awaryjnej. Jest to okno utraty danych podczas awarii regionalnej.

### <a id="GranularFailover"></a>Przeznaczony do pracy awaryjnej w różnych stopniach szczegółowości
Obecnie możliwości automatycznego i ręcznego trybu failover są widoczne na poziomie szczegółowości konta bazy danych. Uwaga: wewnętrznie usługi Azure Cosmos DB jest przeznaczona do zaoferowania *automatyczne* trybu failover dokładniej bazę danych, kontenera lub nawet partycji (kontener będącej właścicielem zakres kluczy). 

### <a id="MultiHomingAPIs"></a>Obsługa wielu regionów w usłudze Azure Cosmos DB
Usługa Azure Cosmos DB umożliwia korzystanie z bazy danych za pomocą *logiczne* (niezależnie od regionu) lub *fizycznych* punktów końcowych (specyficzne dla regionu). Za pomocą punktów końcowych logicznej zapewnia aplikacji można przezroczyste wieloadresowy w przypadku przejścia w tryb failover. To ostatnie fizycznym punktem końcowym oferuje szczegółową kontrolę nad aplikację w celu przekierowania operacji odczytu i zapisu do określonych regionów.

Można znaleźć informacje dotyczące sposobu konfigurowania preferencji odczytu [interfejsu API SQL](../cosmos-db/tutorial-global-distribution-sql-api.md), [interfejsu API tabel](../cosmos-db/tutorial-global-distribution-table.md), i [interfejsu API usługi MongoDB](../cosmos-db/tutorial-global-distribution-mongodb.md) w tych artykułach.

### <a id="TransparentSchemaMigration"></a>Migracja schematami i indeksami przejrzyste i spójne bazy danych 
Usługa Azure Cosmos DB jest w pełni [niezależnej od schematu](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf). Unikatowe projektowania aparatu bazy danych umożliwia usłudze Azure Cosmos DB automatycznie i synchronicznie Indeksuj wszystkie dane od pozyskiwania, bez wymagania żadnych schematów ani indeksów pomocniczych od użytkownika. Dzięki temu można szybko iterować globalnie rozproszonych aplikacji bez konieczności martwienia się o migracji schematami i indeksami bazy danych ani koordynowanie aplikacji wielofazowych wprowadzanie zmian schematu. Usługa Azure Cosmos DB gwarantuje, że wszelkie zmiany, aby jawnie wprowadzone przez Ciebie zasady indeksowania nie powodują spadek wydajności lub dostępności.  

### <a id="ComprehensiveSLAs"></a>Kompleksowe umowy SLA (poza wysokiej dostępności)
Jako usługa globalnie rozproszonej bazy danych Azure Cosmos DB oferuje dobrze zdefiniowany i kompleksowe umowy SLA dla **dostępności**, **opóźnienie**, **przepływności**i **spójności** dla bazy danych, uruchomione w skali globalnej, bez względu na liczbę regionów skojarzonych z bazą danych.  

## <a id="LatencyGuarantees"></a>Gwarancje dot. opóźnienia
Zaletą usługi globalnie rozproszonej bazy danych, takich jak usługi Azure Cosmos DB jest oferują niskie opóźnienia dostępu do danych w dowolnym miejscu na świecie. Usługa Azure Cosmos DB oferuje gwarantowane małe opóźnienia w 99. percentylu dla różnych operacji bazy danych. Protokołu replikacji, która używa usługi Azure Cosmos DB gwarantuje operacji bazy danych (odczyty i zapisy) są zawsze wykonywane w regionie lokalnym do klienta. Opóźnienie umowa SLA usługi Azure Cosmos DB zawiera gwarancje w 99. percentylu odczyty, zapisy (synchroniczne) indeksowane i zapytania dla różnych rozmiarów żądań i odpowiedzi. Gwarancje opóźnienia zapisów obejmują trwałe głosów kworum zatwierdzeń w regionie lokalnym.

### <a id="LatencyAndConsistency"></a>Czas oczekiwania w relacji z spójności 
Globalnie dystrybuowanej usługi do zaoferowania wysoki poziom spójności w konfiguracji globalnej dystrybucji musi ona synchroniczna replikacja zapisu lub synchronicznie wykonywać operacje odczytu między regionami. Szybkość jasny i niezawodność sieci rozległej decyduje o tym, czy wysoki poziom spójności spowoduje większych opóźnień i powoduje zmniejszoną dostępność operacji bazy danych. W związku z tym aby zapewnić gwarantowane małe wartości opóźnienia w 99. percentylu i dostępność przez 99,99% dostępności dla wszystkich kont w obrębie jednego regionu i wszystkich kont w wielu regionach za pomocą rozluźnionej spójności i dostępność przez 99,999% dla wszystkich kont bazy danych w wielu regionach, usługi należy stosować replikacji asynchronicznej. To umożliwiłoby wymaga, że usługa również muszą oferować [modeli spójności dobrze zdefiniowanych, swobodna](consistency-levels.md) — słabszą niż silne (w celu oferują niskie opóźnienia i dostępności gwarancje) i najlepiej mocniejszych niż spójności "ostateczna" (przy użyciu intuicyjnego modelu programowania).

Usługa Azure Cosmos DB zapewnia operacje odczytu nie jest wymagane do kontaktowania się z replik w wielu regionach w celu dostarczania gwarancji spójności określonego poziomu. Podobnie, zapewnia, że operacja zapisu nie zablokowania podczas replikacji danych we wszystkich regionach oznacza to, że zapisy są asynchronicznie replikowane między regionami). Multiregionalne konta baz danych zarówno silne, a także wiele poziomów rozluźnionej spójności są dostępne. 

### <a id="LatencyAndAvailability"></a>Czas oczekiwania w relacji o dostępności 
Opóźnienia i dostępności są partnerami monet ten sam. Mowa o czas oczekiwania operacji w stanie stabilności i dostępność obecności partycje sieci i błędy. Z punktu widzenia aplikacji uruchomionej wolne działanie bazy danych nie można odróżnić od bazy danych, który jest niedostępny. 

Aby rozróżnić duże opóźnienie z brakiem dostępu, Azure Cosmos DB zapewnia bezwzględne górną granicę opóźnienia różnych operacji bazy danych. Jeśli operacja bazy danych trwa dłużej niż górna granica, aby zakończyć, usługi Azure Cosmos DB zwraca błąd upływu limitu czasu. Umowa SLA dotycząca dostępności dla usługi Azure Cosmos DB gwarantuje, że limity czasu są przeliczane względem umowa SLA dotycząca dostępności. 

### <a id="LatencyAndThroughput"></a>Czas oczekiwania w relacji o przepływności
Usługa Azure Cosmos DB nie każą wybierać między opóźnienia i przepływności. Ona honoruje umowa SLA dla obu opóźnienie w 99. percentylu i zapewnia przepływność, którą aprowizowaniu. 

## <a id="ConsistencyGuarantees"></a>Gwarancje spójności
Gdy [modelu silnej spójności](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) to gold standard z programowania danych jest dostępna w cenie wymaga początkowo dużej ilości czas oczekiwania (w stanie stabilnym) i zmniejszyć dostępność (także w przypadku awarii). 

Usługa Azure Cosmos DB oferuje dobrze zdefiniowany model programowania dla Ciebie, przeglądanie informacji o wyjaśnienie pojęcia spójności replikowanych danych. Aby umożliwić łatwe tworzenie aplikacji dystrybuowanych globalnie za pomocą międzyregionalnych, modeli spójności udostępnianych przez usługę Azure Cosmos DB są przeznaczone do niezależna od regionu i niezależny z regionu znajdującego się miałyby gdy odczyty i zapisy są obsługiwane. 

Umowa SLA spójności danych usługi Azure Cosmos DB gwarantuje, że 100% żądań odczytu spełnia gwarancję spójności dla modelu spójności, określonego przez użytkownika (albo na poziomie żądania lub konto bazy danych). Żądania odczytu jest uważany za spełniono spójności umowy SLA, jeśli spełnione są wszystkie gwarancje spójności skojarzone z poziomu spójności. Poniższa tabela przedstawia gwarancje spójności, które odnoszą się do modeli spójności określonych oferowane przez usługę Azure Cosmos DB.

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
        <td rowspan="3">Powiązana nieaktualność</td>
        <td>Przeczytaj monotoniczny (w obrębie regionu)</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>Spójny prefiks</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>Powiązana nieaktualność &lt; K, T</td>
        <td>100%</td>
    </tr>
<tr>
        <td rowspan="3">Sesja</td>
        <td>Odczyt własne/zapis</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>Odsetek powodzeń odczytów monotonicznych</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>Spójny prefiks</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>Spójny prefiks</td>
        <td>Spójny prefiks</td>
        <td>100%</td>
    </tr>
</table>

**Gwarancje spójności skojarzony z modelu danego spójności w usłudze Azure Cosmos DB**


### <a id="ConsistencyAndAvailability"></a>Spójność w relacji o dostępności
[Wynik niemożności](http://www.glassbeam.com/sites/all/themes/glassbeam/images/blog/10.1.1.67.6951.pdf) z [kolejnego elementu teorii CAP](https://people.eecs.berkeley.edu/~brewer/cs262b-2004/PODC-keynote.pdf) potwierdza, że jest w rzeczywistości uniemożliwia systemu do odzyskiwania pozostaną dostępne i oferuje linearizable spójności w przypadku awarii. Usługa bazy danych należy wybrać CP lub AP, gdzie systemów CP zrezygnujesz dostępności na rzecz linearizable spójności podczas zrezygnujesz z systemów AP [linearizable spójności](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) na rzecz dostępności. Usługa Azure Cosmos DB nigdy nie narusza modelu spójności żądanego formalnie sprawia, że CP system. Jednak w praktyce spójności nie jest wszystko lub nic oferty; istnieje wiele dokładnie zdefiniowanych modeli spójności wzdłuż spektrum spójności między linearizable i spójność ostateczna. W usłudze Azure Cosmos DB identyfikuje różne modele rozluźnionej spójności, mają zastosowanie do rzeczywistych scenariuszy, które są intuicyjne do użycia. Usługa Azure Cosmos DB przechodzi kompromisów spójności i dostępności, oferując [wielu złagodzone jeszcze dokładnie zdefiniowanych modeli spójności](consistency-levels.md) i 99,99% dostępności dla wszystkich kont bazy danych w regionie i odczytu przez 99,999% czasu i zapis dostępność dla wszystkich kont bazy danych w wielu regionach. 

### <a id="ConsistencyAndAvailability"></a>Relacja firmy spójności z opóźnieniem
Nazywa się bardziej złożone odmianą kolejnego elementu teorii CAP [PACELC](http://cs-www.cs.yale.edu/homes/dna/papers/abadi-pacelc.pdf), który uwzględnia również wpływ na opóźnienia i spójności w stanie stabilności. Stwierdza, że w stanie stabilnym, system bazy danych należy wybrać opcję spójności oraz opóźnienia. Za pomocą wielu modeli rozluźnionej spójności (wspierana przez replikację asynchroniczną i lokalne odczytu i zapisu kworum) usługi Azure Cosmos DB zapewnia wszystkich operacji odczytu i zapisu są lokalne do odczytu i zapisu odpowiednio regionów. Dzięki temu oferują gwarancja małych opóźnień w obrębie regionu dla modeli spójności danej usługi Azure Cosmos DB.  

### <a id="ConsistencyAndThroughput"></a>Spójność w relacji o przepływności
Ponieważ wybór zależy od implementacji modelu spójności określonych [typ kworum](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf), przepływności różni się też w oparciu o wybór modelu spójności. Na przykład w usłudze Azure Cosmos DB, RU opłata zdecydowanie spójnych odczytów około jest *double* z ostatecznie spójnych odczytów. W takim przypadku należy aprowizować double (RUS) w celu uzyskania tego samego przepływności.


![Relacja między spójnością i przepływności](./media/distribute-data-globally/consistency-and-throughput.png)

**Relacja odczytu pojemności dla modelu spójności określonych w usłudze Azure Cosmos DB**

## <a id="ThroughputGuarantees"></a>Gwarancje przepustowości 
Usługa Azure Cosmos DB pozwala na skalowanie przepływności (a także, magazynu), elastyczne w dowolnej liczbie regionów, w zależności od potrzeb lub żądanie. 

![Usługa Azure Cosmos DB rozproszonych i na partycje kontenerów](../cosmos-db/media/introduction/azure-cosmos-db-global-distribution.png)

**Jeden kontener usługi Azure Cosmos DB w poziomie podzielonym na partycje (w trzech partycji zasobów w regionie) i globalnie dystrybuowane w trzech regionach platformy Azure**

Kontener usługi Azure Cosmos DB pobiera dystrybuowane w dwóch wymiarach (i) w danym regionie oraz (ii) w wielu regionach. Oto kroki tej procedury: 

* **Dystrybucja lokalna**: w ramach jednego regionu, kontener usługi Azure Cosmos DB jest poziomo skalowana w poziomie pod względem *partycji zasobów*. Każda partycja zasobów zarządza zestawu kluczy i są silnie spójne i o wysokiej dostępności są fizycznie reprezentowany przez cztery repliki jest określana skrótem *zestawu replik* i stan replikacji maszyny wśród tych replik. Usługa Azure Cosmos DB jest w pełni zarządzane zasobów systemu, której partycja zasobu to odpowiedzialni za dostarczanie swój udział w odpowiedniej przepustowości dla budżetu zasobów systemowych, przydzielone do niego. Skalowanie kontenera usługi Azure Cosmos DB jest niewidoczne dla użytkowników. Usługa Azure Cosmos DB zarządza partycjami zasobów i dzieli oraz scala je stosownie do potrzeb jako magazyn i zmieniających się wymagań dotyczących przepływności. 
* **Dystrybucja globalna**: Jeśli bazy danych w wielu regionach, każdej partycji zasobów jest dystrybuowane w tych regionach. Partycje zasobu będącego właścicielem tego samego zestawu kluczy w różnych regionach formularza *zestawem partycji* (zobacz [poprzednim rysunku](#ThroughputGuarantees)).  Partycji zasobów w zestawie partycji są koordynowane przy użyciu replikacji w stanie maszyn w wielu regionach skojarzonych z bazą danych. W zależności od poziomu spójności skonfigurowane partycje zasobów w zestawie partycji są skonfigurowane, dynamicznie przy użyciu różnych topologii (na przykład star, łańcucha, drzewo itp.). 

Na podstawie zarządzania o wysokiej dynamice partycji, równoważenia obciążenia i nadzór nad ograniczeniami usługi Azure Cosmos DB umożliwia elastyczne skalowanie przepływności w wielu regionach platformy Azure skojarzone z kontenera usługi Azure Cosmos DB lub bazy danych. Zmiana aprowizowana przepływność jest operacją środowiska uruchomieniowego w usłudze Azure Cosmos DB. Podobnie jak inne operacje bazy danych, usługi Azure Cosmos DB gwarantuje bezwzględne górną granicę na czas oczekiwania na żądanie można zmienić aprowizowanej przepływności. Na przykład na poniższej ilustracji przedstawiono kontenera klienta przy użyciu elastycznego aprowizowanej przepływności (z zakresu od 1M - 10 mln żądań na sekundę w dwóch regionach) na podstawie zapotrzebowania.

![Usługa Azure Cosmos DB elastycznie aprowizowana przepływność](./media/distribute-data-globally/elastic-throughput.png)

**Kontener klienta za pomocą elastycznie przepływnością (różnią się między 1M - 10 mln żądań na sekundę)**

### <a id="ThroughputAndConsistency"></a>Przepływność w relacji z spójności 
Jest taka sama, jak opisano w [spójności w relacji z przepływnością](#ConsistencyAndThroughput).

### <a id="ThroughputAndAvailability"></a>Przepływność w relacji o dostępności
Usługa Azure Cosmos DB w dalszym ciągu utrzymać jej wysoką dostępność, gdy zmiany zostaną wprowadzone do aprowizowanej przepływności. Usługa Azure Cosmos DB niewidocznie zarządza partycjami zasobów (i wykonuje podziału, scalania i klonowania operacji) i zapewnia, że operacje zmniejsza wydajności lub dostępności, podczas gdy aplikacja elastycznie zwiększa lub zmniejsza przepustowość. 

## <a id="AvailabilityGuarantees"></a>Gwarancje dostępności
Usługa Azure Cosmos DB oferuje 99,99% dostępności umowy SLA dla wszystkich kont bazy danych w jednym regionie i wszystkich kont w wielu regionach, zapewniając rozluźnionej spójności, a także dostępność przez 99,999% dla wszystkich kont bazy danych w wielu regionach. Zgodnie z wcześniejszym opisem gwarantuje dostępność usługi Azure Cosmos DB to bezwzględny górną granicę opóźnień dla każdej operacji płaszczyzny danych i kontrolki. Nie zmieniaj gwarancje dostępności z liczbą regionów i geograficznej odległości między regionami. Gwarancje dostępności są stosowane względem zarówno ręcznego jak i automatycznego przejścia w tryb failover. Usługa Azure Cosmos DB zawiera przezroczyste międzyregionalnych interfejsów API, który upewnij się, że aplikacja może działać względem punktów końcowych logiczny sposób niewidoczny dla użytkownika może kierować żądania do nowego regionu w przypadku przejścia w tryb failover. Aplikacja nie musi być ponownie wdrażana w przypadku regionalnej pracy awaryjnej i dostępność umowy SLA są zachowywane przez cały czas.

### <a id="AvailabilityAndConsistency"></a>Dostępność w relacji z spójności, opóźnienia i przepływności
Dostępność w relacji z spójności, opóźnienia i przepływności jest opisane w sekcjach [spójności w relacji o dostępności](#ConsistencyAndAvailability), [opóźnienia w relacji o dostępności](#LatencyAndAvailability) i [Przepływność w relacji z dostępnością](#ThroughputAndAvailability). 

## <a id="CustomerFacingSLAMetrics"></a>Metryki umowy SLA przeznaczonych dla klientów
Usługa Azure Cosmos DB w sposób niewidoczny dla użytkownika udostępnia metryk przepływności, opóźnienia, spójności i dostępności. Te metryki są dostępne programowo i za pośrednictwem witryny Azure portal (zobacz rysunek poniżej). Można też skonfigurować alerty dotyczące różnych progów przy użyciu usługi Azure Application Insights.
 

![Usługa Azure Cosmos DB, widoczne dla klientów metryki umowy SLA](./media/distribute-data-globally/customer-slas.png)

**Metryki spójności, opóźnienia, przepływności i dostępności są przezroczyste dostępne dla każdego dzierżawcy**

## <a id="Next Steps"></a>Następne kroki
* Aby zaimplementować globalnej replikacji na Twoim koncie usługi Azure Cosmos DB przy użyciu witryny Azure portal, zobacz [jak replikacja globalnej bazy danych Azure Cosmos DB przy użyciu witryny Azure portal wykonać](tutorial-global-distribution-sql-api.md).
* Aby dowiedzieć się więcej o sposobie wdrażania wielu wzorców architektury za pomocą usługi Azure Cosmos DB, zobacz [architektury wielu wzorców bazy danych za pomocą usługi Azure Cosmos DB](multi-region-writers.md).
* Aby dowiedzieć się więcej na temat sposobu automatycznego i ręcznego trybu failover działa w usłudze Azure Cosmos DB, zobacz [wywoływania regionalnego trybu failover w usłudze Azure Cosmos DB](regional-failover.md).

## <a id="References"></a>Odwołania
1. Eric Brewera. [Kierunku niezawodnych systemów rozproszonych](https://people.eecs.berkeley.edu/~brewer/cs262b-2004/PODC-keynote.pdf)
2. Eric Brewera. [LIMIT dwunastu latach później — jak reguły uległy zmianie.](http://informatik.unibas.ch/fileadmin/Lectures/HS2012/CS341/workshops/reportsAndSlides/PresentationKevinUrban.pdf)
3. Gilbert, Lynch. - [Brewera&#39;przypuszczeń s i wykonalności spójne, która jest dostępna, usług internetowych odpornego na błędy partycji](http://www.glassbeam.com/sites/all/themes/glassbeam/images/blog/10.1.1.67.6951.pdf)
4. Daniel Abadi. [Wady i zalety spójności w nowoczesnym rozproszonej bazy danych, projektowanie systemów](http://cs-www.cs.yale.edu/homes/dna/papers/abadi-pacelc.pdf)
5. Martin Kleppmann. [Zatrzymaj wywoływania bazy danych CP lub AP](https://martin.kleppmann.com/2015/05/11/please-stop-calling-databases-cp-or-ap.html)
6. Peter Bailis et al. [Powiązana nieaktualność probabilistyczny (PBS) dla praktyczne kworum częściowe](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
7. Naor i wełny. [Obciążenia, wydajności i dostępności w systemach kworum](http://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf)
8. Herlihy i skrzydła. [Lineralizability: Poprawność warunek dla obiektów współbieżnych](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf)
9. [Azure Cosmos DB w umowie SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/)
