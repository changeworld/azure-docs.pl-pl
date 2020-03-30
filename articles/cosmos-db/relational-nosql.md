---
title: Opis różnic między usługą Azure Cosmos DB NoSQL a relacyjnych baz danych
description: W tym artykule wylicza się różnice między NoSQL i relacyjnych baz danych
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/16/2019
ms.reviewer: sngun
ms.openlocfilehash: 1cd80fee51565f2a2c1afa38ed883c10f51a5ee3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75896625"
---
# <a name="understanding-the-differences-between-nosql-and-relational-databases"></a>Zrozumienie różnic między NoSQL a relacyjnych baz danych

W tym artykule wyliczy niektóre z kluczowych zalet baz danych NoSQL zaostłużę baz danych. Omówimy również niektóre z wyzwań związanych ze współpracą z NoSQL. Aby uzyskać szczegółowe spojrzenie na różne magazyny danych, które istnieją, zapoznaj się z naszym artykułem na [temat wyboru odpowiedniego magazynu danych.](https://docs.microsoft.com/azure/architecture/guide/technology-choices/data-store-overview)

## <a name="high-throughput"></a>Wysoka przepływność

Jednym z najbardziej oczywistych wyzwań przy utrzymaniu relacyjnego systemu baz danych jest to, że większość relacyjnych silników stosuje blokady i zatrzaski w celu [wymuszenia ścisłej semantyki ACID.](https://en.wikipedia.org/wiki/ACID) Takie podejście ma korzyści pod względem zapewnienia spójnego stanu danych w bazie danych. Istnieją jednak duże kompromisy w odniesieniu do współbieżności, opóźnienia i dostępności. Ze względu na te podstawowe ograniczenia architektoniczne, duże woluminy transakcyjne może spowodować konieczność ręcznie niezależnego fragmentu danych. Implementowanie ręcznego dzielenia na fragmenty może być czasochłonnym i bolesnym ćwiczeniem.

W tych scenariuszach [rozproszone bazy danych](https://en.wikipedia.org/wiki/Distributed_database) mogą oferować bardziej skalowalne rozwiązanie. Jednak konserwacja może być nadal kosztowne i czasochłonne ćwiczenia. Administratorzy mogą być musieli wykonać dodatkową pracę, aby zapewnić, że rozproszony charakter systemu jest przejrzysty. Mogą one również mieć do uwzględnienia "rozłączony" charakter bazy danych.

[Usługa Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction) upraszcza te wyzwania, wdrażając je na całym świecie we wszystkich regionach platformy Azure. Zakresy partycji mogą być dynamicznie podzielone, aby bezproblemowo rozwijać bazę danych zgodnie z aplikacją, przy jednoczesnym zachowaniu wysokiej dostępności. Szczegółowe wielonajemność i ściśle kontrolowane, natywne zarządzanie zasobami w chmurze ułatwia [zadziwiające gwarancje opóźnienia](https://docs.microsoft.com/azure/cosmos-db/consistency-levels-tradeoffs#consistency-levels-and-latency) i przewidywalną wydajność. Partycjonowanie jest w pełni zarządzane, więc administratorzy nie muszą pisać kodu ani zarządzać partycjami.

Jeśli woluminy transakcyjne osiągają ekstremalne poziomy, takie jak wiele tysięcy transakcji na sekundę, należy wziąć pod uwagę rozproszoną bazę danych NoSQL. Należy wziąć pod uwagę usługi Azure Cosmos DB dla maksymalnej wydajności, łatwości konserwacji i zmniejszenia całkowitego kosztu posiadania.

![Zaplecze](./media/relational-or-nosql/backend-scaled.png)

## <a name="hierarchical-data"></a>Dane hierarchiczne

Istnieje znaczna liczba przypadków użycia, w których transakcje w bazie danych może zawierać wiele relacji nadrzędny podrzędny. Relacje te mogą znacznie rosnąć w czasie, i okazać się trudne do zarządzania. Formy [hierarchicznych baz danych](https://en.wikipedia.org/wiki/Hierarchical_database_model) pojawiły się w latach 80., ale nie były popularne z powodu nieefektywności przechowywania. Stracili również przyczepność, ponieważ [model relacyjny Teda Codda](https://en.wikipedia.org/wiki/Relational_model) stał się de facto standardem używanym przez praktycznie wszystkie główne systemy zarządzania bazami danych.

Jednak dziś popularność baz danych w stylu dokumentów znacznie wzrosła. Te bazy danych można uznać za ponowne wynalezienie hierarchicznego paradygmatu bazy danych, teraz nieskrębne przez obawy dotyczące kosztów przechowywania danych na dysku. W rezultacie utrzymanie wielu złożonych relacji jednostki nadrzędny podrzędność w relacyjnej bazy danych można teraz uznać za anty-wzorzec w porównaniu do nowoczesnych metod zorientowanych na dokument.

Pojawienie się [projektu obiektowego](https://en.wikipedia.org/wiki/Object-oriented_design)i [niedopasowanie impedancji,](https://en.wikipedia.org/wiki/Object-relational_impedance_mismatch) które pojawia się podczas łączenia go z modelami relacyjnymi, podkreśla również anty-wzorzec w relacyjnych bazach danych w niektórych przypadkach użycia. W rezultacie mogą pojawić się ukryte, ale często znaczne koszty utrzymania. Chociaż [podejścia ORM](https://en.wikipedia.org/wiki/Object-relational_mapping) ewoluowały, aby częściowo złagodzić ten temat, bazy danych zorientowane na dokumenty łączą się jednak znacznie lepiej z podejściami obiektowymi. Dzięki takiemu podejściu deweloperzy nie są zmuszeni do zaangażowania się w sterowniki ORM lub dostosowane do potrzeb [aparaty bazy danych OO.](https://en.wikipedia.org/wiki/Object_database) Jeśli dane zawierają wiele relacji nadrzędny-podrzędny i głębokie poziomy hierarchii, warto rozważyć użycie bazy danych dokumentów NoSQL, takiej jak [interfejs API SQL usługi Azure Cosmos DB.](https://docs.microsoft.com/azure/cosmos-db/introduction)

![Szczegółyzamówienia](./media/relational-or-nosql/order-orderdetails.jpg)

## <a name="complex-networks-and-relationships"></a>Złożone sieci i relacje

Jak na ironię, biorąc pod uwagę ich nazwę, relacyjne bazy danych stanowią mniej niż optymalne rozwiązanie do modelowania głębokich i złożonych relacji. Powodem tego jest to, że relacje między jednostkami faktycznie nie istnieją w relacyjnej bazie danych. Muszą być obliczane w czasie wykonywania, ze złożonymi relacjami wymagającymi sprzężeń kartezjańskich w celu umożliwienia mapowania przy użyciu zapytań. W rezultacie operacje stają się wykładniczo droższe pod względem obliczeń w miarę wzrostu relacji. W niektórych przypadkach relacyjnej bazy danych próbującej zarządzać takimi jednostkami stanie się bezużyteczna.

Różne formy "sieciowych" baz danych pojawiły się w czasie, gdy pojawiły się relacyjne bazy danych, ale podobnie jak w przypadku hierarchicznych baz danych, systemy te miały trudności z uzyskaniem popularności. Powolne przyjęcie było spowodowane brakiem przypadków użycia w tym czasie i nieefektywności przechowywania. Obecnie silniki baz danych wykresów można uznać za ponowne pojawienie się paradygmatu sieciowej bazy danych. Kluczową zaletą tych systemów jest to, że relacje są przechowywane jako "obywatele pierwszej klasy" w bazie danych. W ten sposób przechodzenie przez relacje można wykonywać w stałym czasie, zamiast zwiększać złożoność czasu z każdym nowym produktem sprzężenia lub krzyża.

Jeśli utrzymujesz złożoną sieć relacji w bazie danych, warto rozważyć bazę danych wykresu, taką jak [interfejs API Gremlin usługi Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/graph-introduction) do zarządzania tymi danymi.

![Graph](./media/relational-or-nosql/graph.png)

Usługa Azure Cosmos DB to usługa wielomodelowej bazy danych, która oferuje projekcję interfejsu API dla wszystkich głównych typów modeli NoSQL; Rodzina kolumn, dokument, wykres i wartość klucza. Warstwy interfejsu API dokumentu [Gremlin (wykres)](https://docs.microsoft.com/azure/cosmos-db/gremlin-support) i SQL (Core) są w pełni interoperacyjne. Ma to zalety przełączania między różnymi modelami na poziomie programowalności. Magazyny wykresów mogą być wyszukiwane zarówno pod względem złożonych traversals sieci, jak również transakcji modelowanych jako rekordy dokumentów w tym samym magazynie.

## <a name="fluid-schema"></a>Schemat płynu

Inną szczególną cechą relacyjnych baz danych jest, że schematy są wymagane do zdefiniowania w czasie projektowania. Ma to zalety pod względem rzetelności referencyjnej i zgodności danych. Jednak może być również restrykcyjne w miarę wzrostu aplikacji. Odpowiadanie na zmiany w schemacie w logicznie oddzielnych modelach współużytkujących tę samą tabelę lub definicję bazy danych może z czasem stać się złożone. Takie przypadki użycia często korzystają ze schematu przekazywane do aplikacji do zarządzania na podstawie rekordu. Wymaga to bazy danych, aby być "niezależny od schematu" i umożliwić rekordy do "samookreślenia" pod względem danych zawartych w nich.

Jeśli zarządzasz danymi, których struktury stale się zmieniają w wysokim tempie, szczególnie jeśli transakcje mogą pochodzić ze źródeł zewnętrznych, gdzie trudno jest wymusić zgodność w całej bazie danych, warto rozważyć podejście bardziej agnostyczne ze schematu przy użyciu zarządzanej usługi bazy danych NoSQL, takiej jak usługa Azure Cosmos DB.

## <a name="microservices"></a>Mikrousługi

Wzorzec [mikrousług](https://en.wikipedia.org/wiki/Microservices) znacznie wzrosła w ostatnich latach. Ten wzorzec ma swoje korzenie w [architekturze zorientowanej na usługi.](https://en.wikipedia.org/wiki/Service-oriented_architecture) De facto standardem transmisji danych w tych nowoczesnych architekturach mikrousług jest [JSON](https://en.wikipedia.org/wiki/JSON), który również jest nośnikiem pamięci masowej dla większości zorientowanych na dokumenty baz danych NoSQL. Dzięki temu magazyny nosql znacznie bardziej bezproblemowe dopasowanie zarówno trwałości i synchronizacji (przy użyciu [wzorców pozyskiwania zdarzeń)](https://en.wikipedia.org/wiki/Event-driven_architecture)w złożonych implementacji mikrousług. Bardziej tradycyjne relacyjne bazy danych mogą być znacznie bardziej złożone do utrzymania w tych architekturach. Wynika to z większej ilości transformacji wymaganej zarówno dla stanu, jak i synchronizacji między interfejsami API. Usługa Azure Cosmos DB w szczególności ma wiele funkcji, które sprawiają, że jeszcze bardziej bezproblemowe dopasowanie do architektury mikrousług opartych na JSON niż wiele baz danych NoSQL:

* wybór czystych typów danych JSON
* aparat JavaScript i [interfejs API zapytań](https://docs.microsoft.com/azure/cosmos-db/javascript-query-api) wbudowany w bazę danych.
* najnowocześniejszy [kanał zmian,](https://docs.microsoft.com/azure/cosmos-db/change-feed) który klienci mogą subskrybować, aby otrzymywać powiadomienia o modyfikacjach kontenera.

## <a name="some-challenges-with-nosql-databases"></a>Niektóre wyzwania związane z bazami danych NoSQL

Chociaż istnieją pewne wyraźne zalety podczas wdrażania baz danych NoSQL, istnieją również pewne wyzwania, które warto wziąć pod uwagę. Mogą one nie być obecne w tym samym stopniu podczas pracy z modelem relacyjnym:

* transakcji z wieloma relacjami wskazującymi na ten sam podmiot.
* transakcje wymagające silnej spójności w całym zestawie danych.

Patrząc na pierwsze wyzwanie, regułą w bazach danych NoSQL jest zazwyczaj denormalizacja, która jako artykułowana wcześniej, wytwarza bardziej wydajne odczyty w systemie rozproszonym. Istnieje jednak pewne wyzwania projektowe, które wchodzą w grę z tego podejścia. Weźmy przykład produktu związanego z jedną kategorią i wieloma tagami:

![Sprzężenia](./media/relational-or-nosql/many-joins.png)

Najlepszym rozwiązaniem w bazie danych dokumentów NoSQL byłoby denormalize nazwy kategorii i nazwy tagów bezpośrednio w "dokumencie produktu". Jednak w celu zachowania synchronizacji kategorii, tagów i produktów, opcje projektowe ułatwiające to zwiększyły złożoność konserwacji, ponieważ dane są powielane w wielu rekordach produktu, a nie są prostą aktualizacją w "jeden do wielu" i sprzężenia w celu pobrania danych. 

Kompromis polega na tym, że odczyty są bardziej wydajne w zdenormalizowanej rekordzie i stają się coraz bardziej wydajne wraz ze wzrostem liczby koncepcyjnych jednostek. Jednak podobnie jak wydajność odczytu wzrasta wraz ze wzrostem liczby połączonych jednostek w rekordzie denormalize, tak samo złożoność konserwacji utrzymywania jednostek w synchronizacji. Jednym ze sposobów złagodzenia tego kompromisu jest utworzenie [hybrydowego modelu danych.](https://docs.microsoft.com/azure/cosmos-db/modeling-data#hybrid-data-models)

Chociaż w bazach danych NoSQL dostępna jest większa elastyczność, aby radzić sobie z tymi kompromisami, zwiększona elastyczność może również powodować więcej decyzji projektowych. Zapoznaj się z naszym [artykułem, jak modelować i partycjonować dane w usłudze Azure Cosmos DB przy użyciu przykładu rzeczywistego](https://docs.microsoft.com/azure/cosmos-db/how-to-model-partition-example), który zawiera podejście do przechowywania [zdezormializowanych danych użytkownika w synchronizacji, gdzie](https://docs.microsoft.com/azure/cosmos-db/how-to-model-partition-example#denormalizing-usernames) użytkownicy nie tylko siedzą w różnych partycjach, ale w różnych kontenerach.

W odniesieniu do silnej spójności, rzadko zdarza się, że będzie to wymagane w całym zestawie danych. Jednak w przypadkach, gdy jest to konieczne, może to być wyzwaniem w rozproszonych bazach danych. Aby zapewnić wysoką spójność, dane muszą być synchronizowane we wszystkich replikach i regionach przed zezwoleniem klientom na ich odczyt. Może to zwiększyć opóźnienie odczytów.

Ponownie usługi Azure Cosmos DB oferuje większą elastyczność niż relacyjne bazy danych dla różnych kompromisów, które są istotne w tym miejscu, ale dla implementacji na małą skalę, takie podejście może dodać więcej zagadnień projektowych. Aby uzyskać więcej informacji na ten temat, zapoznaj się z naszym artykułem na temat [spójności, dostępności i kompromisów dotyczących wydajności.](https://docs.microsoft.com/azure/cosmos-db/consistency-levels-tradeoffs)

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak zarządzać kontem usługi Azure Cosmos i innymi pojęciami:

* [Jak zarządzać kontem usługi Azure Cosmos](how-to-manage-database-account.md)
* [Dystrybucja globalna](distribute-data-globally.md)
* [Poziomy spójności](consistency-levels.md)
* [Praca z kontenerami i elementami usługi Azure Cosmos](databases-containers-items.md)
* [Punkt końcowy usługi sieci wirtualnej dla konta usługi Azure Cosmos](vnet-service-endpoint.md)
* [Zapora IP dla twojego konta usługi Azure Cosmos](firewall-support.md)
* [Instrukcje dodawania i usuwania regionów platformy Azure do konta usługi Azure Cosmos](how-to-manage-database-account.md)
* [Łas 10 100 000 000 000 000 00](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
