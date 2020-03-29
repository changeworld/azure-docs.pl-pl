---
title: Globalna dystrybucja z usługą Azure Cosmos DB- pod maską
description: Ten artykuł zawiera szczegółowe informacje techniczne dotyczące globalnej dystrybucji usługi Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: a46a69476a2ad6550bc7b3a533fd09565d461db3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74872132"
---
# <a name="global-data-distribution-with-azure-cosmos-db---under-the-hood"></a>Globalna dystrybucja danych za pomocą usługi Azure Cosmos DB — pod maską

Usługa Azure Cosmos DB jest podstawową usługą na platformie Azure, więc jest wdrażana we wszystkich regionach platformy Azure na całym świecie, w tym w chmurach publicznych, suwerennych, Departamentu Obrony (DoD) i rządowych. W centrum danych wdrażamy usługę Azure Cosmos DB i zarządzamy nią na ogromnych znakach maszyn, z których każdy ma dedykowaną pamięć lokalną. W centrum danych usługa Azure Cosmos DB jest wdrażana w wielu klastrach, z których każdy może uruchamiać wiele generacji sprzętu. Maszyny w klastrze są zazwyczaj rozłożone na 10-20 domen błędów dla wysokiej dostępności w regionie. Na poniższej ilustracji przedstawiono topologię globalnego systemu dystrybucji usługi Cosmos DB:

![Topologia systemu](./media/global-dist-under-the-hood/distributed-system-topology.png)

**Dystrybucja globalna w usłudze Azure Cosmos DB jest pod klucz:** W dowolnym momencie za pomocą kilku kliknięć lub programowo za pomocą jednego wywołania interfejsu API można dodać lub usunąć regiony geograficzne skojarzone z bazą danych usługi Cosmos. Baza danych usługi Cosmos z kolei składa się z zestawu kontenerów usługi Cosmos. W usłudze Cosmos DB kontenery służą jako logiczne jednostki dystrybucji i skalowalności. Kolekcje, tabele i wykresy, które tworzysz są (wewnętrznie) tylko kontenery Cosmos. Kontenery są całkowicie niezależna od schematu i zapewniają zakres kwerendy. Dane w kontenerze usługi Cosmos są automatycznie indeksowane po spożyciu. Automatyczne indeksowanie umożliwia użytkownikom wykonywanie zapytań o dane bez konieczności zarządzania schematem lub indeksem, szczególnie w konfiguracji rozproszonej globalnie.  

- W danym regionie dane w kontenerze są dystrybuowane przy użyciu klucza partycji, który jest podany i jest w sposób przejrzysty zarządzany przez podstawowe partycje fizyczne *(dystrybucja lokalna).*  

- Każda partycja fizyczna jest również replikowana w różnych regionach geograficznych *(dystrybucja globalna).* 

Gdy aplikacja korzystająca z usługi Cosmos DB elastycznie skaluje przepływność w kontenerze usługi Cosmos lub zużywa więcej miejsca, usługa Cosmos DB w sposób przejrzysty obsługuje operacje zarządzania partycjami (dzielenie, klonowanie, usuwanie) we wszystkich regionach. Niezależnie od skali, dystrybucji lub awarii usługi Cosmos DB nadal zapewnia pojedynczy obraz systemu danych w kontenerach, które są globalnie rozproszone w dowolnej liczbie regionów.  

Jak pokazano na poniższej ilustracji, dane w kontenerze są rozprowadzane według dwóch wymiarów — w obrębie regionu i między regionami na całym świecie:  

![partycje fizyczne](./media/global-dist-under-the-hood/distribution-of-resource-partitions.png)

Partycja fizyczna jest implementowana przez grupę replik, zwaną *zestawem replik*. Każda maszyna obsługuje setki replik, które odpowiadają różnym partycjom fizycznym w ramach stałego zestawu procesów, jak pokazano na powyższym obrazku. Repliki odpowiadające partycjom fizycznym są dynamicznie umieszczane i równoważą obciążenie na komputerach w klastrze i centrach danych w regionie.  

Replika jednoznacznie należy do dzierżawy usługi Azure Cosmos DB. Każda replika obsługuje wystąpienie aparatu [bazy danych](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)usługi Cosmos DB, który zarządza zasobami, a także skojarzonymi indeksami. Aparat bazy danych Cosmos działa na atom-record-sequence (ARS) na podstawie systemu typu. Aparat jest niezależny od koncepcji schematu, zacierając granicę między strukturą i wartościami instancji rekordów. Usługa Cosmos DB osiąga pełny niezależny od schematu, automatycznie indeksując wszystko po spożyciu w efektywny sposób, co pozwala użytkownikom na wykonywanie zapytań o ich globalnie rozproszone dane bez konieczności zajmowania się zarządzaniem schematem lub indeksem.

Aparat bazy danych Cosmos składa się ze składników, w tym implementacji kilku elementów pierwotnych koordynacji, środowiska wykonawcze języka, procesora zapytań oraz podsystemów magazynu i indeksowania odpowiedzialnych za przechowywanie transakcyjne i indeksowanie danych, Odpowiednio. Aby zapewnić trwałość i wysoką dostępność, aparat bazy danych utrzymuje swoje dane i indeks na ssd i replikuje go między wystąpieniami aparatu bazy danych w ramach replik zestawu(-ów) odpowiednio. Większe dzierżawy odpowiadają wyższej skali przepływności i magazynu i mają większe lub więcej replik lub obu. Każdy składnik systemu jest w pełni asynchroniczne - żaden wątek nigdy nie blokuje, a każdy wątek wykonuje krótkotrwałą pracę bez ponoszenia niepotrzebnych przełączników gwintu. Ograniczenie szybkości i ciśnienie wsteczne są wędzone w całym stosie, od kontroli wstępu do wszystkich ścieżek we/wy. Aparat bazy danych usługi Cosmos jest przeznaczony do wykorzystania szczegółowe współbieżności i zapewnić wysoką przepustowość podczas pracy w oszczędnych ilości zasobów systemowych.

Globalna dystrybucja usługi Cosmos DB opiera się na dwóch kluczowych abstrakcjach – *zestawach replik* i *zestawach partycji.* Zestaw replik to modułowy blok Lego do koordynacji, a zestaw partycji jest dynamiczną nakładką jednej lub więcej geograficznie rozproszonych partycji fizycznych. Aby zrozumieć, jak działa dystrybucja globalna, musimy zrozumieć te dwie kluczowe abstrakcje. 

## <a name="replica-sets"></a>Zestawy replik

Partycja fizyczna jest materializowana jako samodzielnie zarządzana i dynamicznie równoważąca obciążenie grupa replik rozmieszczonych w wielu domenach błędów, nazywanych zestawem replik. Ten zestaw wspólnie implementuje protokół maszyny z replikowanego stanu, aby dane w partycji fizycznej były wysoce dostępne, trwałe i spójne. Członkostwo zestawu replik *N* jest dynamiczne — utrzymuje wahania między *NMin* i *NMax* na podstawie awarii, operacji administracyjnych i czasu dla replik nie powiodło się do ponownego wygenerowanie/odzyskanie. Na podstawie zmian członkostwa protokół replikacji również ponownie konfiguruje rozmiar kworum odczytu i zapisu. Aby równomiernie rozpowszechniać przepływność przypisaną do danej partycji fizycznej, stosujemy dwa pomysły: 

- Po pierwsze koszt przetwarzania żądań zapisu na linii odniesienia jest wyższa niż koszt zastosowania aktualizacji na zwolennika. Odpowiednio, lider jest budżetowane więcej zasobów systemowych niż zwolenników. 

- Po drugie, w miarę możliwości kworum odczytu dla danego poziomu spójności składa się wyłącznie z repliki monitora. Unikamy kontaktowania się z liderem w celu serwowania odczytów, chyba że jest to wymagane. Stosujemy szereg pomysłów z badań przeprowadzonych na temat relacji [obciążenia i pojemności](https://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) w systemach opartych na kworum dla pięciu modeli [spójności,](consistency-levels.md) które obsługuje usługa Cosmos DB.  

## <a name="partition-sets"></a>Zestawy partycji

Grupa partycji fizycznych, po jednej z każdej skonfigurowanej w regionach bazy danych usługi Cosmos, składa się do zarządzania tym samym zestawem kluczy replikowanych we wszystkich skonfigurowanych regionach. Ta wyższa koordynacja prymitywu jest nazywany *partycji zestaw* - geograficznie rozproszone dynamiczne nakładki partycji fizycznych zarządzania danego zestawu kluczy. Podczas gdy dana partycja fizyczna (zestaw replik) jest o zakresie w klastrze, zestaw partycji może obejmować klastry, centra danych i regiony geograficzne, jak pokazano na poniższej ilustracji:  

![Zestawy partycji](./media/global-dist-under-the-hood/dynamic-overlay-of-resource-partitions.png)

Zestaw partycji można potraktować jako geograficznie rozproszony "superredual-set", który składa się z wielu zestawów replik posiadających ten sam zestaw kluczy. Podobnie jak w przypadku zestawu replik, członkostwo zestawu partycji jest również dynamiczne — zmienia się w zależności od niejawnych operacji zarządzania partycjami fizycznymi w celu dodania/usunięcia nowych partycji do/z danego zestawu partycji (na przykład podczas skalowania w poziomie przepływności w kontenerze, dodawania/usuwania regionu do bazy danych usługi Cosmos lub gdy wystąpią błędy). Ze względu na to, że każda z partycji (zestawu partycji) zarządza członkostwem w zestawie partycji we własnym zestawie replik, członkostwo jest w pełni zdecentralizowane i wysoce dostępne. Podczas ponownej konfiguracji zestawu partycji jest również ustanawiana topologia nakładki między partycjami fizycznymi. Topologia jest wybierana dynamicznie na podstawie poziomu spójności, odległości geograficznej i dostępnej przepustowości sieci między źródłem a docelowymi partycjami fizycznymi.  

Usługa umożliwia skonfigurowanie baz danych usługi Cosmos za pomocą jednego regionu zapisu lub wielu regionów zapisu i w zależności od wyboru zestawy partycji są skonfigurowane do akceptowania zapisów w dokładnie jednym lub wszystkich regionach. System wykorzystuje dwupoziomowy, zagnieżdżony protokół konsensusu – jeden poziom działa w replikach zestawu replik partycji fizycznej akceptującej zapisy, a drugi działa na poziomie zestawu partycji, aby zapewnić pełne gwarancje zamawiania dla wszystkich zapisy popełnione w ramach zestawu partycji. Ten wielowarstwowy, zagnieżdżony konsensus ma kluczowe znaczenie dla wdrożenia naszych rygorystycznych łas SLA dla wysokiej dostępności, a także implementacji modeli spójności, które usługa Cosmos DB oferuje swoim klientom.  

## <a name="conflict-resolution"></a>Rozwiązywanie konfliktów

Nasz projekt propagacji aktualizacji, rozwiązywania konfliktów i śledzenia przyczynowości jest inspirowany [wcześniejszymi pracami nad algorytmami epidemii](https://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) i systemem [Bayou.](https://zoo.cs.yale.edu/classes/cs422/2013/bib/terry95managing.pdf) Podczas gdy jądra pomysłów przetrwały i zapewniają wygodną ramę odniesienia do komunikowania projektu systemu Cosmos DB, przeszły one również znaczną transformację, ponieważ zastosowaliśmy je do systemu Cosmos DB. Było to potrzebne, ponieważ poprzednie systemy nie zostały zaprojektowane ani z zarządzaniem zasobami, ani ze skalą, w jakiej cosmos DB musi działać, ani w celu zapewnienia możliwości (na przykład ograniczonej spójności nieaktualności) oraz rygorystycznych i kompleksowe elasy, które usługa Cosmos DB dostarcza swoim klientom.  

Przypomnijmy, że zestaw partycji jest rozproszony w wielu regionach i następuje cosmos DBs (multi-master) protokół replikacji do replikacji danych między partycjami fizycznymi składającymi się na danego zestawu partycji. Każda partycja fizyczna (zestawu partycji) akceptuje zapisy i służy odczyty zazwyczaj do klientów, którzy są lokalne w tym regionie. Zapisy akceptowane przez partycję fizyczną w regionie są trwale zatwierdzone i udostępniane w partycji fizycznej, zanim zostaną potwierdzone klientowi. Są to zapisy wstępne i są propagowane do innych partycji fizycznych w ramach zestawu partycji przy użyciu kanału anty-entropii. Klienci mogą żądać wstępnego lub zatwierdzonego zapisu, przekazując nagłówek żądania. Propagacja anty-entropii (w tym częstotliwość propagacji) jest dynamiczna, oparta na topologii zestawu partycji, regionalnej bliskości partycji fizycznych i skonfigurowanym poziomie spójności. W ramach zestawu partycji usługa Cosmos DB jest zgodna z podstawowym schematem zatwierdzania z dynamicznie wyselekcjonowaną partycją arbitra. Wybór arbitra jest dynamiczny i stanowi integralną część ponownej konfiguracji zestawu partycji na podstawie topologii nakładki. Zatwierdzone zapisy (w tym aktualizacje wielowierszowe/wsadowe) są gwarantowane do zamówienia. 

Używamy zakodowanych zegarów wektorowych (zawierających identyfikator regionu i zegary logiczne odpowiadające każdemu poziomowi konsensusu odpowiednio w zestawie replik i zestaw partycji) do śledzenia przyczynowości i wektorów wersji do wykrywania i rozwiązywania konfliktów aktualizacji. Topologia i algorytm wyboru elementu równorzędnego mają na celu zapewnienie stałego i minimalnego przechowywania oraz minimalnego obciążenia sieciowego wektorów wersji. Algorytm gwarantuje ścisłą właściwość konwergencji.  

W przypadku baz danych cosmosu skonfigurowanych z wieloma regionami zapisu system oferuje szereg elastycznych zasad automatycznego rozwiązywania konfliktów dla deweloperów do wyboru, w tym: 

- **Last-Write-Wins (LWW)**, który domyślnie używa właściwości sygnatury czasowej zdefiniowanej przez system (która jest oparta na protokole zegara synchronizacji czasu). Usługa Cosmos DB umożliwia również określenie dowolnej innej niestandardowej właściwości numerycznej, która ma być używana do rozwiązywania konfliktów.  
- **Zdefiniowane w aplikacji (niestandardowe) zasady rozwiązywania konfliktów** (wyrażone za pomocą procedur scalania), który jest przeznaczony do semantyki zdefiniowanej przez aplikację uzgadniania konfliktów. Procedury te są wywoływane po wykryciu konfliktów zapisu i zapisu pod auspicjami transakcji bazy danych po stronie serwera. System zapewnia dokładnie raz gwarancję wykonania procedury scalania w ramach protokołu zobowiązania. Dostępnych jest [kilka przykładów rozwiązywania konfliktów.](how-to-manage-conflicts.md)  

## <a name="consistency-models"></a>Modele spójności

Niezależnie od tego, czy baza danych usługi Cosmos jest konfigurowana za pomocą jednego czy wielu regionów zapisu, można wybrać jedną z pięciu dobrze zdefiniowanych modeli spójności. W przypadku wielu regionów zapisu oto kilka istotnych aspektów poziomów spójności:  

Spójność przestarzałości ograniczona gwarantuje, że wszystkie odczyty będą w ramach prefiksów *K* lub *T* sekund od najnowszego zapisu w dowolnym z regionów. Ponadto odczyty z ograniczoną spójnością nieaktualność są gwarantowane jako monotoniczne i spójne gwarancje prefiksu. Protokół anty-entropii działa w sposób ograniczony szybkością i zapewnia, że prefiksy nie gromadzą się, a ciśnienie wsteczne na zapisach nie musi być stosowane. Spójność sesji gwarantuje monotoniczne odczytywanie, monotoniczne zapisy, odczytywanie własnych zapisów, pisanie zgodnie z odczytem i spójne gwarancje prefiksów na całym świecie. W przypadku baz danych skonfigurowanych z silną spójnością korzyści (małe opóźnienie zapisu, wysoka dostępność zapisu) wielu regionów zapisu nie mają zastosowania z powodu replikacji synchroniczowej w różnych regionach.

Semantyka pięciu modeli spójności w usłudze Cosmos DB jest [opisana tutaj](consistency-levels.md)i matematycznie opisana przy użyciu wysokiej klasy specyfikacji TLA+ [tutaj.](https://github.com/Azure/azure-cosmos-tla)

## <a name="next-steps"></a>Następne kroki

Następnie dowiedz się, jak skonfigurować dystrybucję globalną przy użyciu następujących artykułów:

* [Dodawanie/usuwanie regionów z poziomu konta bazy danych](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Jak skonfigurować klientów do multi-homing](how-to-manage-database-account.md#configure-multiple-write-regions)
* [Jak utworzyć niestandardowe zasady rozwiązywania konfliktów](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)
