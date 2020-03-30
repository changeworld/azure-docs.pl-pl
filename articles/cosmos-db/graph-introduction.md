---
title: Wprowadzenie do interfejsu API języka Gremlin w usłudze Azure Cosmos DB
description: Dowiedz się, jak można używać usługi Azure Cosmos DB do przechowywania dużych grafów, wykonywania na nich zapytań i przechodzenia ich z krótkim czasem oczekiwania za pomocą języka zapytań dotyczących grafów w środowisku Gremlin witryny Apache TinkerPop.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 07/18/2019
ms.author: lbosq
ms.openlocfilehash: 9151b54d7fa0b64a465aa8384cb4bfdb8e72c482
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "75500009"
---
# <a name="introduction-to-azure-cosmos-db-gremlin-api"></a>Wprowadzenie do usługi Azure Cosmos DB: interfejs API języka Gremlin

[Usługa Azure Cosmos DB](introduction.md) to globalnie rozproszona, wielomodelowa usługa bazy danych firmy Microsoft dla aplikacji o znaczeniu krytycznym. Jest wielomodelową bazą danych i obsługuje modele danych dokumentu, klucza, wykresu i rodziny kolumn. Interfejs API gremlin usługi Azure Cosmos DB jest używany do przechowywania danych wykresu i pracy z nimi w pełni zarządzanej usłudze bazy danych przeznaczonej do dowolnej skali.  

![Architektura grafów w usłudze Azure Cosmos DB](./media/graph-introduction/cosmosdb-graph-architecture.png)

Ten artykuł zawiera omówienie interfejsu API języka Gremlin usługi Azure Cosmos DB i przedstawia jak można go użyć do przechowywania dużych grafów zawierających miliardy wierzchołków i krawędzi. Można zbadać wykresy z opóźnieniem milisekundy i łatwo rozwijać strukturę wykresu. Interfejs API gremlin usługi Azure Cosmos DB jest oparty na standardzie bazy danych wykresu [Apache TinkerPop](https://tinkerpop.apache.org) i używa języka zapytań Gremlin. 

Interfejs API Gremlin usługi Azure Cosmos DB łączy w sobie możliwości algorytmów bazy danych wykresów z wysoce skalowalną, zarządzaną infrastrukturą, aby zapewnić unikatowe, elastyczne rozwiązanie najczęstszych problemów z danymi związanych z brakiem elastyczności i podejściami relacyjnych. 

## <a name="features-of-azure-cosmos-db-graph-database"></a>Funkcje bazy danych grafów usługi Azure Cosmos DB
 
Usługa Azure Cosmos DB jest w pełni zarządzaną bazą danych grafów, która oferuje globalną dystrybucję, elastyczne skalowanie magazynu i przepływności, automatyczne indeksowanie i wykonywanie zapytań, dostosowywalne poziomy spójności oraz obsługę standardu TinkerPop. 

Poniżej przedstawiono zróżnicowane funkcje, które oferuje interfejs API gremlin usługi Azure Cosmos DB:

* **Elastycznie skalowalna przepływność i magazyn**

  Skalowanie grafów w świecie rzeczywistym wymaga skalowania przekraczającego pojemność pojedynczego serwera. Usługa Azure Cosmos DB obsługuje skalowalne poziomo bazy danych wykresów, które mogą mieć praktycznie nieograniczony rozmiar pod względem magazynu i aprowizowanej przepływności. Wraz ze wzrostem skali bazy danych wykresu dane będą automatycznie dystrybuowane za pomocą [partycjonowania wykresu.](https://docs.microsoft.com/azure/cosmos-db/graph-partitioning)

* **Replikacja w wielu regionach**

  Usługa Azure Cosmos DB może automatycznie replikować dane wykresu do dowolnego regionu platformy Azure na całym świecie. Replikacja globalna upraszcza tworzenie aplikacji, które wymagają globalnego dostępu do danych. Oprócz minimalizacji opóźnień odczytu i zapisu w dowolnym miejscu na świecie usługa Azure Cosmos DB zapewnia automatyczny regionalny mechanizm pracy awaryjnej, który może zapewnić ciągłość aplikacji w rzadkich przypadkach przerwania usługi w regionie. 

* **Szybkie zapytania i przechodzenie z najczęściej przyjmowanym standardem zapytań grafowych**

  Przechowuj heterogeniczne wierzchołki i krawędzie i wysyłaj do nich zapytania za pomocą znanej składni Gremlin. Gremlin jest imperatywem, funkcjonalnym językiem zapytań, który zapewnia bogaty interfejs do implementowania typowych algorytmów wykresu. 
  
  Usługa Azure Cosmos DB umożliwia rozbudowanych zapytań w czasie rzeczywistym i przechodzenie bez konieczności określania wskazówek dotyczących schematu, indeksów pomocniczych lub widoków. Więcej informacji znajduje się w temacie [Query graphs by using Gremlin](gremlin-support.md) (Wykonywanie zapytań dla grafów przy użyciu środowiska Gremlin).

* **W pełni zarządzana baza danych wykresów**

  Usługa Azure Cosmos DB eliminuje konieczność zarządzania zasobami maszyn i baz danych. Większość istniejących platform baz danych wykresu są powiązane z ograniczeniami ich infrastruktury i często wymagają wysokiego stopnia konserwacji, aby zapewnić jego działanie. 
  
  Jako w pełni zarządzana usługa usługa Usługi Cosmos DB eliminuje konieczność zarządzania maszynami wirtualnymi, aktualizowania oprogramowania wykonawczego, zarządzania dzieleniem na fragmenty lub replikacją lub radzenia sobie ze złożonymi uaktualnieniami warstwy danych. Dla każdego grafu jest automatycznie tworzona kopia zapasowa w celu ochrony przed regionalnymi awariami. Gwarancje te umożliwiają deweloperom skupienie się na dostarczaniu wartości aplikacji zamiast obsługi i zarządzania bazami danych wykresów. 

* **Automatyczne indeksowanie**

  Domyślnie usługa Azure Cosmos DB automatycznie indeksuje wszystkie właściwości w węzłach oraz krawędziach grafu i nie oczekuje ani nie wymaga żadnego schematu ani tworzenia indeksów pomocniczych. Dowiedz się więcej o [indeksowaniu w usłudze Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/index-overview). 

* **Zgodność z witryną Apache TinkerPop**

  Usługa Azure Cosmos DB obsługuje [standard Apache TinkerPop typu open source.](https://tinkerpop.apache.org/) Standard Tinkerpop ma obszerny ekosystem aplikacji i bibliotek, które można łatwo zintegrować z interfejsem API Gremlin usługi Azure Cosmos DB. 

* **Poziomy z możliwością dostosowania spójności**

  Usługa Azure Cosmos DB zapewnia pięć dobrze zdefiniowanych poziomów spójności, aby osiągnąć odpowiedni kompromis między spójnością a wydajnością aplikacji. Dla zapytań i operacji odczytu usługa Azure Cosmos DB oferuje pięć różnych poziomów spójności: „silna”, „powiązana nieaktualność”, „sesja”, „spójny prefiks” i „ostateczna”. Te szczegółowe, dokładnie zdefiniowane poziomy spójności umożliwiają ustalanie optymalnych kompromisów między spójnością, dostępnością i opóźnieniem. Więcej informacji znajduje się w temacie [Tunable data consistency levels in Azure Cosmos DB](consistency-levels.md) (Dostosowywalne poziomy spójności danych w usłudze Azure Cosmos DB).

## <a name="scenarios-that-can-use-gremlin-api"></a>Scenariusze, w których można używać interfejsu API języka Gremlin
Oto kilka scenariuszy, w których pomoc techniczna dla wykresów usługi Azure Cosmos DB może być przydatna:

* **Sieci społecznościowe/klient 365**

  Łącząc dane dotyczące klientów i ich interakcji z innymi osobami, można zaprojektować spersonalizowaną obsługę, przewidzieć zachowanie klientów lub umożliwić nawiązanie kontaktu z innymi osobami o podobnych zainteresowaniach. Usługa Azure Cosmos DB może służyć do zarządzania sieciami społecznościowymi oraz śledzenia preferencji i danych klientów.

* **Aparaty rekomendacji**

  Ten scenariusz jest powszechnie stosowany w branży sprzedaży detalicznej. Przez łączenie informacji na temat produktów, użytkowników oraz interakcji użytkownika, np. zakupów, przeglądania witryn internetowych lub oceny produktów, można tworzyć niestandardowe rekomendacje. Obsługa małych opóźnień, skali elastycznej i wykresu macierzystego usługi Azure Cosmos DB jest idealna dla tych scenariuszy.

* **Dane geoprzestrzenne**

  Wiele aplikacji stosowanych w telekomunikacji, logistyce i planowaniu podróży musi znaleźć lokalizację będącą przedmiotem zainteresowania na danym obszarze lub zlokalizować najkrótszą/optymalną trasę między dwoma lokalizacjami. Usługa Azure Cosmos DB stanowi naturalne rozwiązanie tych problemów.

* **Internet rzeczy**

  Gdy sieć i połączenia między urządzeniami IoT zostają przedstawione w formie grafu, można lepiej rozumieć stan urządzeń i zasobów. Można także dowiedzieć się, jak zmiany w jednej części sieci mogą teoretycznie wpłynąć na inną część.

## <a name="introduction-to-graph-databases"></a>Wprowadzenie do baz danych wykresów
W świecie rzeczywistym dane w sposób naturalny łączą się ze sobą. Tradycyjne modelowanie danych koncentruje się na definiowaniu jednostek oddzielnie i obliczaniu ich relacji w czasie wykonywania. Chociaż ten model ma swoje zalety, wysoce połączone dane mogą być trudne do zarządzania pod jego ograniczeniami.  

Podejście bazy danych wykresu opiera się na utrwalanie relacji w warstwie magazynu zamiast tego, co prowadzi do bardzo wydajnych operacji pobierania wykresu. Interfejs API Gremlin usługi Azure Cosmos DB obsługuje [model wykresu właściwości.](https://tinkerpop.apache.org/docs/current/reference/#intro)

### <a name="property-graph-objects"></a>Obiekty wykresu właściwości

Wykres właściwości to struktura składająca się z [wierzchołków](http://mathworld.wolfram.com/GraphVertex.html) i [krawędzi.](http://mathworld.wolfram.com/GraphEdge.html) [graph](http://mathworld.wolfram.com/Graph.html) Oba obiekty mogą mieć dowolną liczbę par klucz-wartość jako właściwości. 

* **Wierzchołki** — wierzchołki oznaczają odrębne jednostki, takie jak osoba, miejsce lub zdarzenie.

* **Krawędzie** — krawędzie określają relacje między wierzchołkami. Na przykład dana osoba może znać inną osobę, brać udział w wydarzeniu lub była niedawno w danej lokalizacji. 

* **Właściwości** — właściwości zawierają informacje na temat wierzchołków i krawędzi. Może istnieć dowolna liczba właściwości w wierzchołkach lub krawędziach i mogą być używane do opisywania i filtrowania obiektów w kwerendzie. Przykładowe właściwości obejmują wierzchołek, który ma nazwę i wiek lub krawędź, która może mieć sygnaturę czasową i/lub wagę. 

Baz danych wykresu są często zawarte w nosql lub nierelacyjnej bazy danych kategorii, ponieważ nie ma zależności od schematu lub ograniczonego modelu danych. Ten brak schematu umożliwia modelowanie i przechowywanie połączonych struktur w sposób naturalny i wydajny. 

### <a name="gremlin-by-example"></a>Język Gremlin w przykładach
Przykładowy graf pomoże dowiedzieć się, jak można wyrazić zapytania w języku Gremlin. Na poniższej ilustracji przedstawiono w formie grafu aplikację biznesową, która zarządza danymi o użytkownikach, zainteresowaniach i urządzeniach.  

![Przykładowa baza danych przedstawiająca osoby, urządzenia i zainteresowania](./media/gremlin-support/sample-graph.png) 

Ten wykres ma następujące typy *wierzchołków* (o nazwie "label" w Gremlin):

- **Ludzie**: Wykres ma trzy osoby, Robin, Thomas i Ben
- **Zainteresowania**: Ich interesy, w tym przykładzie, gra w piłkę nożną
- **Urządzenia**: Urządzenia, z których korzystają użytkownicy
- **Systemy operacyjne**: Systemy operacyjne, na których urządzenia działają

Reprezentujemy relacje między tymi jednostkami za pomocą następujących *typów/etykiet krawędzi:*

- **Wie**: Na przykład "Thomas zna Robina"
- **Zainteresowany**: Do reprezentowania interesów ludzi na naszym wykresie, na przykład, "Ben jest zainteresowany piłką nożną"
- **RunsOS**: Laptop uruchamia system operacyjny Windows
- **Zastosowania:** Aby opowiedzieć, z którego urządzenia korzysta dana osoba. Na przykład Robin używa telefonu firmy Motorola o numerze seryjnym 77

Możemy uruchomić pewne operacje zgodnie z tym grafem przy użyciu [Konsoli Gremlin](https://tinkerpop.apache.org/docs/3.3.2/reference/#gremlin-console). Można również wykonywać te operacje przy użyciu sterowników Gremlin na wybranej platformie (Java, Node.js, Python lub .NET).  Zanim zajmiemy się tym, co jest obsługiwane w usłudze Azure Cosmos DB, przyjrzymy się kilku przykładom, aby zapoznać się ze składnią.

Najpierw zajmijmy się CRUD. Następująca instrukcja języka Gremlin wstawia do grafu wierzchołek „Thomas”:

```java
:> g.addV('person').property('id', 'thomas.1').property('firstName', 'Thomas').property('lastName', 'Andersen').property('age', 44)
```

Następnie poniższa instrukcja języka Gremlin wstawia krawędź „knows” między wierzchołkami „Thomas” i „Robin”.

```java
:> g.V('thomas.1').addE('knows').to(g.V('robin.1'))
```

Poniższe zapytanie zwraca wierzchołki „person” w porządku malejącym według ich imion:
```java
:> g.V().hasLabel('person').order().by('firstName', decr)
```

Wyjątkowość grafów docenia się, gdy trzeba odpowiedzieć na takie pytania jak: „Jakich systemów operacyjnych używają znajomi Thomasa?” Możesz uruchomić ten gremlin przechodzenia, aby uzyskać te informacje z wykresu:

```java
:> g.V('thomas.1').out('knows').out('uses').out('runsos').group().by('name').by(count())
```

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat obsługi grafów w usłudze Azure Cosmos DB, zobacz:

* Rozpocznij pracę od [samouczka dotyczącego grafów usługi Azure Cosmos DB](create-graph-dotnet.md).
* Uzyskaj informacje na temat [wykonywania zapytań dla grafów w usłudze Azure Cosmos DB przy użyciu środowiska Gremlin](gremlin-support.md).
