---
title: Wprowadzenie do interfejsu API języka Gremlin usługi Azure Cosmos DB | Microsoft Docs
description: Dowiedz się, jak można używać usługi Azure Cosmos DB do przechowywania dużych grafów, wykonywania na nich zapytań i przechodzenia ich z krótkim czasem oczekiwania za pomocą języka zapytań dotyczących grafów w środowisku Gremlin witryny Apache TinkerPop.
services: cosmos-db
author: LuisBosquez
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-graph
ms.devlang: na
ms.topic: overview
ms.date: 01/05/2017
ms.author: lbosq
ms.openlocfilehash: a0eec8aec315eefcbcc859828fa68ea0ccee6190
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/05/2018
ms.locfileid: "43695354"
---
# <a name="introduction-to-azure-cosmos-db-gremlin-api"></a>Wprowadzenie do usługi Azure Cosmos DB: interfejs API języka Gremlin

[Azure Cosmos DB](introduction.md) to rozproszona globalnie wielomodelowa usługa bazy danych firmy Microsoft do aplikacji o krytycznym znaczeniu. Usługa Azure Cosmos DB zapewnia następujące funkcje, z których wszystkie bazują na [wiodących w branży umowach SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/):

* [Gotowa do użytku dystrybucja globalna](distribute-data-globally.md)
* [Elastyczne skalowanie przepływności i magazynu](partition-data.md) na całym świecie
* Opóźnienie rzędu kilku milisekund wyrażone przy użyciu jednej cyfry na poziomie 99. percentyla
* [Pięć dokładnie zdefiniowanych poziomów spójności](consistency-levels.md)
* Gwarantowana wysoka dostępność 

Usługa Azure Cosmos DB [automatycznie indeksuje dane](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) bez konieczności zarządzania schematami i indeksami. To usługa wielomodelowa, obsługująca modele danych dokumentów, par klucz-wartość, grafów i kolumn.

Interfejs API języka Gremlin usługi Azure Cosmos DB udostępnia:

- Modelowanie grafów.
- Interfejsy API przejścia.
- Gotową do użytku dystrybucję globalną.
- Elastyczne skalowanie pamięci masowej i przepływności z opóźnieniami odczytu poniżej 10 ms oraz poniżej 15 ms na poziomie 99. percentyla.
- Automatyczne indeksowanie z błyskawiczną dostępnością zapytań.
- Poziomy z możliwością dostosowania spójności.
- Kompleksowe umowy SLA gwarantujące dostępność na poziomie co najmniej 99,99% dla wszystkich kont w obrębie jednego regionu i wszystkich kont w wielu regionach w przypadku rozluźnionej spójności, a także dostępność do odczytu na poziomie co najmniej 99,999% dla wszystkich kont bazy danych w wielu regionach.

Aby wysłać zapytanie do usługi Azure Cosmos DB, możesz użyć języka przechodzenia grafu [Apache TinkerPop](http://tinkerpop.apache.org) lub [Gremlin](http://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps).

Ten artykuł zawiera omówienie interfejsu API języka Gremlin usługi Azure Cosmos DB i przedstawia jak można go użyć do przechowywania dużych grafów zawierających miliardy wierzchołków i krawędzi. Można wykonać zapytania dla grafów z opóźnieniem rzędu kilku milisekund i łatwo rozwinąć strukturę i schemat graf.

## <a name="graph-database"></a>Baza danych grafu
W świecie rzeczywistym dane w sposób naturalny łączą się ze sobą. Tradycyjne modelowanie danych skupia się na obiektach. W przypadku wielu aplikacji również występuje potrzeba naturalnego modelowania lub modelowania zarówno obiektów, jak i relacji.

[Graf](http://mathworld.wolfram.com/Graph.html) jest strukturą składającą się z [wierzchołków](http://mathworld.wolfram.com/GraphVertex.html) i [krawędzi](http://mathworld.wolfram.com/GraphEdge.html). Zarówno wierzchołki, jak i krawędzie mogą mieć dowolną liczbę właściwości. Wierzchołki określają odrębne obiekty, takie jak osoby, miejsca lub zdarzenia. Krawędzi określają relacje między wierzchołkami. Na przykład dana osoba może znać inną osobę, brać udział w wydarzeniu lub była niedawno w danej lokalizacji. Właściwości zawierają informacje na temat wierzchołków i krawędzi. Przykładowe właściwości obejmują wierzchołek, który ma nazwę i wiek oraz krawędź, która ma sygnaturę czasową i/lub wagę. Bardziej oficjalnie model ten jest określany jak [graf właściwości](http://tinkerpop.apache.org/docs/current/reference/#intro). Usługa Azure Cosmos DB obsługuje model grafu właściwości.

Na przykład poniższy przykładowy graf przedstawia relacje między ludźmi, urządzeniami przenośnymi, zainteresowaniami oraz systemami operacyjnymi:

![Przykładowa baza danych przedstawiająca osoby, urządzenia i zainteresowania](./media/graph-introduction/sample-graph.png)

Grafy pomagają zrozumieć najróżniejsze zestawy danych dotyczących nauki, technologii i biznesu. Bazy danych programu Graph umożliwiają modelowanie i przechowywanie grafów w naturalny i skuteczny sposób, dzięki czemu są przydatne w wielu scenariuszach. Bazy danych programu Graph są zwykle bazami danych NoSQL, ponieważ te przypadki użycia często wymagają również elastyczności schematów i szybkiej iteracji.

Grafy oferują nowatorską i zaawansowaną technikę modelowania danych. Jednak ten fakt sam w sobie nie jest wystarczającym powodem używania bazy danych grafów. Dla wielu przypadków użycia i wzorców, które obejmują przejścia grafów, grafy przewyższają tradycyjne bazy danych SQL i NoSQL w zakresie rzędów wielkości. Ta różnica w wydajności staje się jeszcze większa w przypadku przechodzenia poza więcej niż jedną relację, np. gdy chodzi o znajomego znajomego.

Do szybkich przejść, które oferują bazy danych grafów, można dołączyć algorytmy grafów, np. wyszukiwanie pierwszej głębi, wyszukiwanie pierwszej szerokości oraz algorytm Dijkstry w celu rozwiązania różnego typu problemów, np. dotyczących sieci społecznościowych, zarządzania zawartością, danych geoprzestrzennych oraz rekomendacji.

## <a name="planet-scale-graphs-with-azure-cosmos-db"></a>Grafy w skali globalnej przy użyciu usługi Azure Cosmos DB
Usługa Azure Cosmos DB jest w pełni zarządzaną bazą danych grafów, która oferuje globalną dystrybucję, elastyczne skalowanie magazynu i przepływności, automatyczne indeksowanie i wykonywanie zapytań, dostosowywalne poziomy spójności oraz obsługę standardu TinkerPop.

![Architektura grafów w usłudze Azure Cosmos DB](./media/graph-introduction/cosmosdb-graph-architecture.png)

W porównaniu z innymi bazami danych grafów na rynku usługa Azure Cosmos DB oferuje następujące zróżnicowane funkcje:

* Elastycznie skalowalna przepływność i magazyn

 Skalowanie grafów w świecie rzeczywistym wymaga skalowania przekraczającego pojemność pojedynczego serwera. Za pomocą usługi Azure Cosmos DB można płynnie skalować grafy na wielu serwerach. Można również niezależnie skalować przepływność grafy w oparciu o wzorce dostępu. Usługa Azure Cosmos DB obsługuje bazy danych grafów, które można skalować do niemal nieograniczonego rozmiaru magazynu i aprowizowanej przepływności.

* Replikacja w wielu regionach

 Usługa Azure Cosmos DB w sposób niewidoczny dla użytkownika replikuje dane grafu na wszystkie regiony skojarzone z kontem. Replikacja umożliwia tworzenie aplikacji, które wymagają globalnego dostępu do danych. Doszło do kompromisu w zakresie spójności, dostępności, wydajności i odpowiednich gwarancji. Usługa Cosmos DB oferuje niewidoczne dla użytkownika regionalne przejście w tryb failover z wykorzystaniem interfejsów API podłączonych do wielu sieci. Na całym świecie można elastycznie skalować przepływność i magazyn.

* Szybkie zapytania i przejścia ze znaną składnią języka Gremlin

 Można przechowywać heterogeniczne wierzchołki i krawędzie oraz wykonywać zapytania dla tych dokumentów przy użyciu znanej składni języka Gremlin. Usługa Azure Cosmos DB wykorzystuje wysoce współbieżną, nieblokującą, opartą na strukturze dziennika technologię indeksowania do automatycznego indeksowania całej zawartości. Ta funkcja umożliwia wykonywanie zaawansowanych zapytań i przejść w czasie rzeczywistym bez konieczności określania wskazówek schematu, indeksów pomocniczych czy widoków. Więcej informacji znajduje się w temacie [Query graphs by using Gremlin](gremlin-support.md) (Wykonywanie zapytań dla grafów przy użyciu środowiska Gremlin).

* Pełne zarządzanie

 Usługa Azure Cosmos DB eliminuje konieczność zarządzania zasobami maszyn i baz danych. Ponieważ jest to w pełni zarządzana usługa platformy Microsoft Azure, nie trzeba zarządzać maszynami wirtualnymi, wdrażać ani konfigurować oprogramowania, zarządzać skalowaniem ani obsługiwać złożonych aktualizacji warstwy danych. Dla każdego grafu jest automatycznie tworzona kopia zapasowa w celu ochrony przed regionalnymi awariami. Możesz łatwo dodać konto usługi Azure Cosmos DB i aprowizować pojemność odpowiednio do potrzeb, co pozwala skupić się na aplikacji, zamiast zajmować się obsługą bazy danych i zarządzaniem nią.

* Automatyczne indeksowanie

 Domyślnie usługa Azure Cosmos DB automatycznie indeksuje wszystkie właściwości w węzłach oraz krawędziach grafu i nie oczekuje ani nie wymaga żadnego schematu ani tworzenia indeksów pomocniczych.

* Zgodność z witryną Apache TinkerPop

 Usługa Azure Cosmos DB natywnie obsługuje standard Apache TinkerPop typu open-source i można ją zintegrować z innymi systemami grafów z obsługą witryny TinkerPop. Można więc łatwo przeprowadzić migrację z innej bazy danych grafów, np. Titan lub Neo4j, lub użyć usługi Azure Cosmos DB ze strukturami analizy grafów, takimi jak Apache Spark GraphX.

* Poziomy z możliwością dostosowania spójności

 Można wybrać spośród pięciu dobrze zdefiniowanych poziomów spójności w celu osiągnięcia optymalnego kompromisu między wydajnością a spójnością. Dla zapytań i operacji odczytu usługa Azure Cosmos DB oferuje pięć różnych poziomów spójności: „silna”, „powiązana nieaktualność”, „sesja”, „spójny prefiks” i „ostateczna”. Te szczegółowe, dokładnie zdefiniowane poziomy spójności umożliwiają ustalanie optymalnych kompromisów między spójnością, dostępnością i opóźnieniem. Więcej informacji znajduje się w temacie [Tunable data consistency levels in Azure Cosmos DB](consistency-levels.md) (Dostosowywalne poziomy spójności danych w usłudze Azure Cosmos DB).

Usługa Azure Cosmos DB może również korzystać z wielu modeli, np. dokumentów lub grafów, w tych samych kontenerach/bazach danych. Kontener dokumentów służy do przechowywania danych grafów równolegle z dokumentami. Aby wykonać zapytania dla tych samych danych w formie grafu, można użyć zarówno zapytań SQL w formacie JSON, jak i zapytań Gremlin.

## <a name="get-started"></a>Rozpoczęcie pracy
Do tworzenia kont usługi Azure Cosmos DB można użyć interfejsu wiersza polecenia (CLI) platformy Azure, programu Azure PowerShell lub witryny Azure Portal z obsługą interfejsu API języka Gremlin. Po utworzeniu kont witryna Azure Portal zapewnia punkt końcowy usługi, np. `https://<youraccount>.gremlin.cosmosdb.azure.com`, który dostarcza fronton WebSocket dla środowiska Gremlin. Można skonfigurować narzędzia zgodne z witryną TinkerPop, np. [Konsolę Gremlin](http://tinkerpop.apache.org/docs/current/reference/#gremlin-console), aby podłączyć się do tego punktu końcowego i tworzyć aplikacje w środowisku Java, Node.js lub w dowolnym sterowniku klienta Gremlin.

W poniższej tabeli przedstawiono popularne sterowniki Gremlin, których można użyć do usługi Azure Cosmos DB:

| Do pobrania | Dokumentacja | Wprowadzenie |
| --- | --- | --- |
| [.NET](http://tinkerpop.apache.org/docs/3.3.1/reference/#gremlin-DotNet) | [Gremlin.NET w witrynie GitHub](https://github.com/apache/tinkerpop/tree/master/gremlin-dotnet) | [Tworzenie grafu przy użyciu platformy .NET](create-graph-dotnet.md) |
| [Java](https://mvnrepository.com/artifact/com.tinkerpop.gremlin/gremlin-java) | [Gremlin JavaDoc](http://tinkerpop.apache.org/javadocs/current/full/) | [Tworzenie grafu przy użyciu środowiska Java](create-graph-java.md) |
| [Node.js](https://www.npmjs.com/package/gremlin) | [Gremlin-JavaScript w witrynie GitHub](https://github.com/jbmusso/gremlin-javascript) | [Tworzenie grafu przy użyciu platformy Node.js](create-graph-nodejs.md) |
| [Python](http://tinkerpop.apache.org/docs/3.3.1/reference/#gremlin-python) | [Gremlin-Python w witrynie GitHub](https://github.com/apache/tinkerpop/tree/master/gremlin-python) | [Tworzenie grafu przy użyciu środowiska Python](create-graph-python.md) |
| [PHP](https://packagist.org/packages/brightzone/gremlin-php) | [Gremlin-PHP w witrynie GitHub](https://github.com/PommeVerte/gremlin-php) | [Tworzenie grafu przy użyciu środowiska PHP](create-graph-php.md) |
| [Konsola Gremlin](https://tinkerpop.apache.org/downloads.html) | [Dokumentacja dotycząca witryny TinkerPop](http://tinkerpop.apache.org/docs/current/reference/#gremlin-console) |  [Tworzenie grafu przy użyciu Konsoli Gremlin](create-graph-gremlin-console.md) |

## <a name="scenarios-for-graph-support-of-azure-cosmos-db"></a>Scenariusze obsługi grafów w usłudze Azure Cosmos DB
Poniżej przedstawiono kilka scenariuszy, w których można użyć obsługi grafów w usłudze Azure Cosmos DB:

* Sieci społecznościowe

 Łącząc dane dotyczące klientów i ich interakcji z innymi osobami, można zaprojektować spersonalizowaną obsługę, przewidzieć zachowanie klientów lub umożliwić nawiązanie kontaktu z innymi osobami o podobnych zainteresowaniach. Usługa Azure Cosmos DB może służyć do zarządzania sieciami społecznościowymi oraz śledzenia preferencji i danych klientów.

* Aparaty rekomendacji

 Ten scenariusz jest powszechnie stosowany w branży sprzedaży detalicznej. Przez łączenie informacji na temat produktów, użytkowników oraz interakcji użytkownika, np. zakupów, przeglądania witryn internetowych lub oceny produktów, można tworzyć niestandardowe rekomendacje. Małe opóźnienia, elastyczne skalowanie i natywna obsługa grafów w usłudze Azure Cosmos DB nadają się idealnie do modelowania tych interakcji.

* Dane geoprzestrzenne

 Wiele aplikacji stosowanych w telekomunikacji, logistyce i planowaniu podróży musi znaleźć lokalizację będącą przedmiotem zainteresowania na danym obszarze lub zlokalizować najkrótszą/optymalną trasę między dwoma lokalizacjami. Usługa Azure Cosmos DB stanowi naturalne rozwiązanie tych problemów.

* Internet rzeczy

 Gdy sieć i połączenia między urządzeniami IoT zostają przedstawione w formie grafu, można lepiej rozumieć stan urządzeń i zasobów. Można także dowiedzieć się, jak zmiany w jednej części sieci mogą teoretycznie wpłynąć na inną część.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat obsługi grafów w usłudze Azure Cosmos DB, zobacz:

* Rozpocznij pracę od [samouczka dotyczącego grafów usługi Azure Cosmos DB](create-graph-dotnet.md).
* Uzyskaj informacje na temat [wykonywania zapytań dla grafów w usłudze Azure Cosmos DB przy użyciu środowiska Gremlin](gremlin-support.md).
