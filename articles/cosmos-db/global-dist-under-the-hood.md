---
title: Dystrybucja globalna z Azure Cosmos DBą pod okapem
description: Ten artykuł zawiera szczegółowe informacje techniczne dotyczące dystrybucji globalnej usługi Azure Cosmos DB
author: dharmas-cosmos
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: dharmas
ms.reviewer: sngun
ms.openlocfilehash: ce943fbed0774667100f6de4c60f91c0b02de6c3
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69615350"
---
# <a name="global-data-distribution-with-azure-cosmos-db---under-the-hood"></a>Globalna dystrybucja danych z Azure Cosmos DBą pod okapem

Azure Cosmos DB to fundamentowa usługa na platformie Azure, więc jest wdrażana we wszystkich regionach świadczenia usługi Azure na całym świecie, w tym w publicznych, suwerennych departamentach obrony i instytucji rządowych. W ramach centrum danych, firma Microsoft wdrażanie i zarządzanie Azure Cosmos DB na ogromną sygnaturą czasową maszyny, każdy z dedykowanych dla magazynu lokalnego. W centrum danych usługi Azure Cosmos DB jest wdrażana w wielu klastrach, każdy potencjalnie uruchamianie wielu generacji sprzętu. Maszyny w klastrze są zwykle rozproszone w domenach błędów 10-20 w celu zapewnienia wysokiej dostępności w regionie. Na poniższej ilustracji przedstawiono topologię systemu dystrybucji globalnej usługi Cosmos DB:

![Topologia systemu](./media/global-dist-under-the-hood/distributed-system-topology.png)

**Globalna dystrybucja w Azure Cosmos DB to gotowe:** W dowolnym momencie za pomocą kilku kliknięć lub programowo przy użyciu jednego wywołania interfejsu API można dodawać lub usuwać regiony geograficzne skojarzone z bazą danych Cosmos. Baza danych Cosmos, z kolei, składa się z zestawu kontenerów Cosmos. W usłudze Cosmos DB kontenery służyć jako jednostki logiczne dystrybucji i skalowalności. Kolekcje, tabele i wykresy, które możesz utworzyć to (wewnętrznie) po prostu Cosmos kontenery. Kontenery są całkowicie schemat-niezależny od i zapewniają zakres zapytania. Dane w kontenerze Cosmos są automatycznie indeksowane po pozyskiwania. Automatyczne indeksowanie umożliwia użytkownikom wykonywanie zapytań dotyczących danych bez problemów z zarządzaniem schematem lub indeksem, szczególnie w przypadku konfiguracji rozproszonej globalnie.  

- W danym regionie dane znajdujące się w kontenerze są dystrybuowane przy użyciu klucza partycji, który jest dostarczany i jest w sposób niewidoczny dla użytkownika zarządzany przez bazowe partycje fizyczne (*Dystrybucja lokalna*).  

- Każda partycja fizyczna jest również replikowana w regionach geograficznych (*dystrybucja globalna*). 

Gdy aplikacja używająca Cosmos DB elastycznie skaluje przepływność na kontenerze Cosmos lub zużywa więcej przestrzeni dyskowej, Cosmos DB nieprzezroczystie obsługuje operacje zarządzania partycjami (dzielenie, klonowanie, usuwanie) we wszystkich regionach. Niezależnie od skali, dystrybucji czy awarii usługa Cosmos DB stale zapewnia pojedynczy obraz systemu danych w kontenerach, które są globalnie rozproszone w dowolnej liczbie regionów.  

Jak pokazano na poniższej ilustracji, dane w kontenerze są dystrybuowane w dwóch wymiarach — w regionie i w różnych regionach, na całym świecie:  

![Partycje fizyczne](./media/global-dist-under-the-hood/distribution-of-resource-partitions.png)

Partycja fizyczna jest implementowana przez grupę replik nazywaną zestawem *replik*. Każdy komputer obsługuje setki replik odpowiadających różnym partycjom fizycznym w ramach ustalonego zestawu procesów, jak pokazano na powyższym obrazie. Repliki, odpowiadające na partycje fizyczne dynamicznie są umieszczane i zrównoważonym między komputerami w ramach klastrów i centrów danych w obrębie regionu.  

Replikę należy jednoznacznie do dzierżawy usługi Azure Cosmos DB. Każda replika znajduje się wystąpienie usługi Cosmos DB [aparatu bazy danych](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf), która zarządza zasobami, a także indeksy. Aparat bazy danych Cosmos działa w systemie typów opartych na protokole Atom-Record-Sequence (ARS). Aparat jest niezależny od do koncepcji schematu, przez odmycie granicy między strukturą i wartościami wystąpień rekordów. Usługa cosmos DB realizuje agnosticism pełnego schematu przez automatyczne indeksowanie wszystkich elementów, od pozyskiwania w sposób efektywny, które umożliwia użytkownikom do wykonywania zapytań ich danych rozproszonych globalnie, bez konieczności zarządzania schematami lub indeksami.

Aparat bazy danych Cosmos składa się z składników, takich jak implementacja kilku elementów podstawowych koordynacji, środowisko uruchomieniowe języka, procesor zapytań oraz Podsystemy magazynowania i indeksowania odpowiedzialne za magazyn transakcyjny i indeksowanie danych, piwo. Zapewnienie trwałości i wysokiej dostępności, aparatu bazy danych będzie nadal występować, jego danych i indeksu na dyskach SSD i replikuje ją między wystąpieniami aparatu bazy danych w ramach repliki — określiła odpowiednio. Większe dzierżawy odnoszą się do większej skali przepływności i magazynu oraz mają większe lub więcej replik. Każdy składnik system jest w pełni asynchroniczne — nigdy nie blokuje żadnych wątków, a każdy wątek działa krótkotrwałe bez ponoszenia żadnych przełączników niepotrzebne wątku. Ograniczanie szybkości i ciśnienia wstecznego są przyłączone do instalacji w całym stosie z formantu czasowej na wszystkie ścieżki we/wy. Aparat bazy danych Cosmos został zaprojektowany w celu wykorzystania precyzyjnej współbieżności i zapewnienia wysokiej przepływności podczas pracy w Frugal ilości zasobów systemowych.

Globalna dystrybucja Cosmos DB opiera się na dwóch abstrakcyjnych kluczach — *zestawach replik* i *zestawach partycji*. Zestawu replik to moduły blok Lego koordynacji i zestaw partycji jest dynamiczne nakładkę z jedną lub więcej partycji fizycznych geograficznie rozproszonych. Aby zrozumieć, jak globalnej dystrybucji działa, należy zrozumieć te dwa elementy abstrakcji klucza. 

## <a name="replica-sets"></a>Zestawów replik

Partycja fizyczna jest przeznaczona do użycia przez samodzielną i dynamiczną obciążenie grupą replik w wielu domenach błędów, nazywanych zestawem replik. Ten zestaw zbiorczo implementuje protokół zreplikowanego komputera stanu, aby dane z partycji fizycznej były wysoce dostępne, trwałe i spójne. Członkostwo w zestawie replik *N* jest dynamiczne — utrzymuje wahania między *NMin* i *nmaks.* na podstawie błędów, operacji administracyjnych oraz czasu niepowodzeń replik w celu ponownego wygenerowania/odzyskania. Na podstawie członkostwa zmian, replikacja protokołu również Rekonfiguruj rozmiar odczytu i zapisu kworum. Aby równomiernie dystrybuować przepływność, która jest przypisana do danej partycji fizycznej, wykorzystujemy dwa pomysły: 

- Po pierwsze koszt przetwarzania żądań zapisu na liderze jest wyższy niż koszt zastosowania aktualizacji na stronie nadzorującej. Odpowiednio lidera jest w budżecie więcej zasobów systemowych niż obserwatorów. 

- Po drugie w miarę możliwości odczytu kworum dla poziomu spójności danego składa się wyłącznie z replikami poniżej. Unikaj firma Microsoft, kontaktując się z liderem do obsługi operacji odczytu, chyba że wymagane. Firma Microsoft stosuje kilka pomysłów z badań wykonywanych w związku z obciążeniem [i pojemnością](https://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) w systemach opartych na kworum dla [pięciu modeli spójności](consistency-levels.md) obsługiwanych przez Cosmos DB.  

## <a name="partition-sets"></a>Zestawy partycji

Grupa partycji fizycznych, jedna ze wszystkich skonfigurowanych za pomocą regionów bazy danych Cosmos, składa się z tego samego zestawu kluczy replikowanych we wszystkich skonfigurowanych regionach. Ta wyższa wartość pierwotna koordynacji jest nazywana rozłożoną w sposób dynamiczny rozłożeniem partycji fizycznych, która zarządza danym zestawem kluczy. Chociaż dana partycja fizyczna (zestaw replik) jest objęta zakresem klastra, zestaw partycji może obejmować klastry, centra danych i regiony geograficzne, jak pokazano na poniższej ilustracji:  

![Zestawy partycji](./media/global-dist-under-the-hood/dynamic-overlay-of-resource-partitions.png)

Zestaw partycji można traktować jako geograficznie "super repliki zestaw", który składa się z wielu zestawów replik będącej właścicielem tego samego zestawu kluczy. Podobnie jak w przypadku zestawu replik, członkostwo w zestawie partycji jest również dynamiczne — zmienia się w zależności od niejawnych operacji zarządzania partycjami fizycznymi w celu dodania/usunięcia nowych partycji do/z danego zestawu partycji (na przykład w przypadku skalowania przepływności na zewnątrz kontener, Dodaj/Usuń region do bazy danych Cosmos lub w przypadku wystąpienia błędów). Ze względu na to, że każda partycja (zestawu partycji) zarządza członkostwem w zestawie partycji w ramach własnego zestawu replik, członkostwo jest w pełni zdecentralizowane i wysoce dostępne. Podczas ponownej konfiguracji zestawu partycji również zostanie nawiązane topologii nakładki między partycje fizyczne. Topologia jest dynamicznie wybierana na podstawie poziomu spójności, odległości geograficznej i dostępnej przepustowości sieci między źródłową i docelową partycją fizyczną.  

Usługa pozwala na skonfigurowanie baz danych Cosmos z regionu zapisu w jednym lub wielu regionach zapisu, a w zależności od wyboru, zestawach partycji są skonfigurowane do akceptowania zapisów w dokładnie jednego lub wszystkich regionach. System korzysta z dwupoziomowego, zagnieżdżonego protokołu consensusu — jeden poziom działa w ramach replik zestawu replik partycji fizycznej akceptujących zapisy, a drugi działa na poziomie zestawu partycji, aby zapewnić kompletne gwarancje porządkowania dla wszystkich zatwierdzone zapisy w zestawie partycji. Ten consensus wielowarstwowych, zagnieżdżone jest krytyczne dla realizacji umowy SLA rygorystyczne w celu zapewnienia wysokiej dostępności, a także wdrożenia modeli spójności, które Cosmos DB oferuje klientom.  

## <a name="conflict-resolution"></a>Rozwiązywanie konfliktów

Nasze projektowanie pod kątem propagacji aktualizacji, rozwiązywanie konfliktów i przyczynowości śledzenia są INSPIROWANE od wcześniejszego pracy na [epidemii algorytmy](https://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) i [Bayou](https://zoo.cs.yale.edu/classes/cs422/2013/bib/terry95managing.pdf) systemu. Jądra pomysły przetrwały i zapewniają wygodny układem odniesienia do komunikowania się projekt systemu usługi Cosmos DB, również przeszły znaczące przekształcenia, jak firma Microsoft stosowane do systemu usługi Cosmos DB. Jest to konieczne, ponieważ w poprzednich systemach nie zaprojektowano żadnych zasobów związanych z zarządzaniem zasobami ani ze skalą, w której Cosmos DB ma działać, ani w celu zapewnienia możliwości (na przykład związanej ze spójnością nieodświeżoną) oraz rygorystycznych i kompleksowe umowy SLA, które Cosmos DB dostarcza klientom.  

Pamiętaj, że zestaw partycji jest rozpowszechniana w wielu regionach i korzysta z protokołu replikacji bazy danych Cosmos (Multi-master) do replikacji danych między partycjami fizycznymi wchodzących w skład danego zestawu partycji. Każda partycja fizyczna (zestawu partycji) akceptuje operacje zapisu i umożliwia zazwyczaj odczyty na klientach lokalnych dla danego regionu. Zapisy akceptowane przez partycję fizyczną w regionie są trwale zatwierdzone i są wysoce dostępne na partycji fizycznej przed ich potwierdzeniem do klienta. Te są wstępne operacje zapisu i są propagowane do innych fizycznych partycji w obrębie zestawu partycji przy użyciu kanału zapobieganie entropii. Klienci mogą żądać zapisów wstępnie zaakceptowane lub zatwierdzone przez przekazanie nagłówek żądania. Propagacja zapobieganie entropii (w tym częstotliwość propagacji) jest dynamiczny, na podstawie topologii zestawu partycji i regionalnym bliskość partycje fizyczne i spójności, poziom skonfigurowany. W ramach zestawu partycji Cosmos DB jest zgodny schemat podstawowy zatwierdzenia z partycją dynamicznie wybrane kryterium. Wybór kryterium jest dynamiczne i jest integralną częścią ponownej konfiguracji zestawu partycji na podstawie topologii nakładki. Zatwierdzone operacje zapisu (w tym wielu-row/partii aktualizacje) jest gwarantowana do zamówienia. 

Stosujemy zegary zakodowany wektora (zawierający identyfikator regionu i zegary logiczne odpowiadający każdy poziom zgody na zestawu replik i zestawu partycji odpowiednio) przyczynowości, śledzenia i wersji, mogą wystąpić konflikty aktualizacji wektorów do wykrywania i rozwiązywania. Topologii oraz algorytm Wybór elementu równorzędnego jest zapewnienie stałych i minimalne magazynu i sieci minimalnej nakładów pracy związanych z wektorów wersji. Algorytm gwarantuje właściwość zbieżności strict.  

W przypadku baz danych Cosmos skonfigurowany z wieloma regionami zapisu systemu udostępnia szereg elastyczny automatycznych konflikt zasad rozpoznawania dla deweloperów do wyboru, w tym: 

- **Ostatni zapis-WINS (LWW)** , który domyślnie używa zdefiniowanej przez system właściwości sygnatury czasowej (która jest oparta na protokole zegara czasu synchronizacji). Usługa cosmos DB umożliwia również określenie innych niestandardowych właściwości wartości liczbowych służący do rozwiązywania konfliktów.  
- **Zasady rozwiązywania konfliktów zdefiniowane przez aplikację (niestandardowe)** (wyrażone za pośrednictwem procedur scalania), który jest przeznaczony do uzgadniania przez aplikacje semantyki konfliktów. Te procedury Pobierz wywoływane po wykryciu konflikty zapisów pod nadzorem transakcji bazy danych po stronie serwera. System zapewnia dokładnie gwarancję wykonania procedury scalania w ramach protokołu zobowiązania. Istnieje [kilka przykładów rozwiązywania konfliktów](how-to-manage-conflicts.md) , które mogą być odtwarzane za pomocą programu.  

## <a name="consistency-models"></a>Modeli spójności

Niezależnie od tego, czy baza danych Cosmos jest konfigurowana z jednym czy wieloma regionami zapisu, można wybierać spośród pięciu dobrze zdefiniowanych modeli spójności. W przypadku wielu regionów zapisu poniżej przedstawiono kilka istotnych aspektów poziomów spójności:  

Ograniczona nieaktualność gwarantuje, że wszystkie operacje odczytu będą znajdować się w obrębie prefiksów *K* lub *T* sekund od ostatniego zapisu w dowolnym regionie. Ponadto należy zapewnić monotoniczny i z zachowaniem spójnych prefiksów. Protokół zapobieganie entropii działa w sposób ograniczony szybkość i gwarantuje, że prefiksy nie są gromadzone i wsteczne zapisu nie ma zastosowanie. Zapewnianie spójności sesji gwarantuje monotoniczny odczyt, monotoniczny zapis, odczytanie własnych zapisów, zapis następujący: Odczyt i spójne gwarancje dotyczące prefiksów, na całym świecie. W przypadku baz danych skonfigurowanych pod kątem silnej spójności korzyści wynikające z replikacji synchronicznej w różnych regionach nie mają zastosowania do małych i dużych ilości zapisów w wielu regionach zapisu.

Semantyka pięciu modeli spójności w Cosmos DB jest opisana [tutaj](consistency-levels.md)i matematycznie opisana przy użyciu wysokiego poziomu tla i specyfikacji. [](https://github.com/Azure/azure-cosmos-tla)

## <a name="next-steps"></a>Następne kroki

Następnie Dowiedz się, jak skonfigurować dystrybucję globalną, korzystając z następującymi artykułami:

* [Dodawanie/usuwanie regionów z Twojego konta bazy danych](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Jak skonfigurować klientów dla wielu](how-to-manage-database-account.md#configure-multiple-write-regions)
* [Jak utworzyć niestandardowe zasady rozwiązywania konfliktów](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)
