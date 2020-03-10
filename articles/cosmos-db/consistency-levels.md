---
title: Poziomy spójności w usłudze Azure Cosmos DB
description: Usługa Azure Cosmos DB ma pięć poziomów spójności, aby ułatwić równoważenie ostatecznej spójnością, dostępnością i opóźnieniem wad i zalet.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: b5d9df7a0afa9b4270f0eff643e083e5bccfceb8
ms.sourcegitcommit: e6bce4b30486cb19a6b415e8b8442dd688ad4f92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2020
ms.locfileid: "78933709"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Poziomy spójności w usłudze Azure Cosmos DB

Rozproszonych baz danych, które opierają się na replikację, wysoką dostępność, małe opóźnienia lub obu, należy podstawowych zależnościami między spójności odczytu, a dostępność, opóźnienia i przepływności. Najbardziej komercyjnie dostępne bazy danych umożliwiają deweloperom wybór między dwoma ekstremalnymi modelami spójności: *silną* spójnością i spójnością *ostateczną* . Model linearizability lub silna spójność jest standardem dla programowania danych. Jednak dodaje cenę o wyższym opóźnieniu (w stanie stałym) i ograniczoną dostępność (podczas niepowodzeń). Z drugiej strony spójność ostateczna zapewnia wyższą dostępność i lepszą wydajność, ale utrudnia Programowanie aplikacji. 

Wyjaśnienie pojęcia spójności danych jako liczne opcje zamiast dwoma skrajnymi poziomami zbliża się do usługi Azure Cosmos DB. Ścisła spójność i ostateczna spójność znajdują się na końcu spektrum, ale istnieje wiele opcji spójności w spektrum. Deweloperzy mogą korzystać z tych opcji, aby wybierać precyzyjne i szczegółowe kompromisy w odniesieniu do wysokiej dostępności i wydajności. 

Usługa Azure Cosmos DB deweloperzy mogą wybierać pięć dokładnie zdefiniowanych modeli spójności na spektrum spójności. Z najsilniejszych do bardziej swobodnych modeli zalicza się *silne*, *ograniczone nieaktualność*, *sesję*, *spójny prefiks*i spójność *ostateczną* . Modele są dobrze zdefiniowane i intuicyjne i mogą być używane w określonych rzeczywistych scenariuszach. Każdy model zapewnia [wady dostępności i wydajności](consistency-levels-tradeoffs.md) i jest obsługiwany przez umowy SLA. Na poniższej ilustracji przedstawiono różne poziomy spójności jako spektrum.

![Spójność o szerokim zakresie funkcji](./media/consistency-levels/five-consistency-levels.png)

Poziomy spójności to region-niezależny od i są gwarantowane dla wszystkich operacji niezależnie od regionu, w którym są obsługiwane odczyty i zapisy, liczbę regionów skojarzonych z kontem usługi Azure Cosmos lub czy Twoje konto jest skonfigurowane z użyciem jednego lub wiele regionów zapisu.

## <a name="scope-of-the-read-consistency"></a>Zakres spójności odczytu

Kondycja spójności odczytu mają zastosowanie do jednej operacji odczytu, o określonym zakresie w ramach zakresu klucza partycji lub partycji logicznej. Operacja odczytu mogą być wystawiane przez klienta zdalnego lub procedury składowanej.

## <a name="configure-the-default-consistency-level"></a>Konfigurowanie domyślnego poziomu spójności

W dowolnym momencie można skonfigurować domyślny poziom spójności na Twoim koncie usługi Azure Cosmos. Domyślny poziom spójności skonfigurowany na Twoim koncie ma zastosowanie do wszystkich baz danych i kontenerów usługi Azure Cosmos w ramach tego konta. Wszystkie operacje odczytu i zapytania względem kontenera lub bazę danych domyślnie używają poziomu spójności określony. Aby dowiedzieć się więcej, zobacz jak [skonfigurować domyślny poziom spójności](how-to-manage-consistency.md#configure-the-default-consistency-level).

## <a name="guarantees-associated-with-consistency-levels"></a>Gwarancje skojarzone z poziomów spójności

Kompleksowe umowy SLA, udostępniane przez usługi Azure Cosmos DB gwarancji, że 100 procent żądań odczytu spełnia gwarancję spójności dla dowolnego poziomu spójności, że wybrano. Żądania odczytu spełnia spójności umowy SLA, jeśli spełnione są wszystkie gwarancje spójności skojarzone z poziomu spójności. Precyzyjne definicje pięciu poziomów spójności w Azure Cosmos DB przy użyciu języka specyfikacji TLA + są dostępne w repozytorium GitHub [Azure-Cosmos-tla](https://github.com/Azure/azure-cosmos-tla) .

Semantyka poziomów spójności pięć są opisane poniżej:

- **Silne**: silna spójność oferuje gwarancję linearizability. Linearizability odwołuje się do obsługi żądań współbieżnie. Operacje odczytu mają gwarancję do zwrócenia zatwierdzone najbardziej aktualną wersję elementu. Klient nigdy nie widzi zapisu niezatwierdzone lub jego część. Użytkownicy są zawsze gwarantowane odczyt najnowsza wersja zatwierdzone/zapis.

  Poniższa ilustracja ilustruje silną spójność z notatkami muzycznymi. Po zapisaniu danych w regionie "Wschodnie stany USA" podczas odczytywania danych z innych regionów otrzymujesz najnowszą wartość:

  ![wideo](media/consistency-levels/strong-consistency.gif)

- **Powiązana nieaktualność**: odczyty są gwarantowane do przestrzegania gwarancji o spójnym prefiksie. Odczyty mogą być opóźnione w stosunku do zapisu o największej liczbie *"K"* (czyli "aktualizacjach") elementu lub interwału czasu *"T"* . Innymi słowy, w przypadku wybrania ograniczonej nieodświeżoności, "nieaktualność" można skonfigurować na dwa sposoby: 

  * Liczba wersji (*K*) elementu
  * Przedział czasu (*T*), przez który odczyty mogą być opóźnione za zapisem 

  Powiązana nieaktualność oferty całkowitej globalnej kolejności z wyjątkiem w ramach "okno nieaktualność." Istnieje monotoniczny gwarancje odczytu w regionie wewnątrz lub na zewnątrz okna nieaktualność. Silna spójność ma taką samą semantykę jak ta, która jest oferowana przez ograniczony czas nieodświeżony. Okno nieaktualność jest równa zero. Powiązana nieaktualność jest również określany jako atomowych opóźnione czasu. Gdy klient wykonuje operacje odczytu w obrębie regionu akceptującego zapisy, gwarancje wynikające z podanej nieodświeżonej spójności są identyczne z tymi gwarancjami przez silną spójność.

  Ograniczone nieodświeżoności są często wybierane przez aplikacje rozproszone globalnie, które oczekują niskich opóźnień zapisu, ale wymagają całkowitej gwarancji zamówienia globalnego. Powiązana nieaktualność jest doskonałe dla aplikacji, które udostępniają współpracę i udostępnianie grup, taktowanie, publikowanie/subskrybowanie/kolejkowanie itp. Poniższa ilustracja ilustruje ograniczone niezgodność ze spójnością z notatkami muzycznymi. Po zapisaniu danych w regionie "Wschodnie stany USA" regiony "zachodnie stany USA" i "Australia Wschodnia" odczytają wartość zapisaną na podstawie skonfigurowanego maksymalnego czasu zwłoki lub maksymalnej liczby operacji:

  ![wideo](media/consistency-levels/bounded-staleness-consistency.gif)

- **Sesja**: w ramach jednej sesji klienta zagwarantowane jest przestrzeganie spójnego prefiksu (przy założeniu, że pojedyncza sesja "zapisująca"), monotoniczny odczytów, monotoniczny zapisów, odczytów i zapisów, a ponadto gwarantuje zapis do odczytu. Klienci poza sesją wykonującą operacje zapisu będą widzieli ostateczną spójność.

  Spójność sesji to szeroko używany poziom spójności dla jednego regionu, a także aplikacje rozproszone globalnie. Zapewnia ona opóźnienia zapisu, dostępność i przepływność odczytu porównywalne do tej samej spójności ostatecznej, ale również zapewnia gwarancje spójności, które odpowiadają potrzebom aplikacji napisanych w kontekście użytkownika. Poniższa ilustracja ilustruje spójność sesji z notatkami muzycznymi. Region "zachodnie stany USA" i regiony "Wschodnie stany USA" korzystają z tej samej sesji (sesji A), tak aby jednocześnie odczytywać dane w tym samym czasie. Region "Australia Wschodnia" używa "Session B", więc odbiera dane później, ale w kolejności zapisu.

  ![wideo](media/consistency-levels/session-consistency.gif)

- **Spójny prefiks**: zwrócone aktualizacje zawierają prefiks wszystkich aktualizacji bez przerw. Spójny poziom spójności prefiksu gwarantuje, że odczyt nie zobaczy niewidocznych kolejności zapisu.

  Jeśli operacje zapisu zostały wykonane w `A, B, C`zamówienia, klient zobaczy `A`, `A,B`lub `A,B,C`, ale nigdy nie ma kolejności, takiej jak `A,C` lub `B,A,C`. Spójny prefiks zapewnia opóźnienia zapisu, dostępność i przepływność odczytu porównywalne do tej spójności ostatecznej, ale również zapewnia gwarancję zamówienia, która odpowiada potrzebom scenariuszy, w których kolejność jest ważna. Poniższa ilustracja ilustruje spójność prefiksu spójności z notatkami muzycznymi. We wszystkich regionach odczyty nigdy nie są wyświetlane w kolejności zapisu:

  ![wideo](media/consistency-levels/consistent-prefix.gif)

- **Ostateczne**: nie ma gwarancji porządkowania dla operacji odczytu. W przypadku braku dalszy zapis replik ostatecznie zbiegają się.  
Spójność ostateczna to najsłaba forma spójności, ponieważ klient może odczytać wartości starsze niż te, które były wcześniej odczytywane. Spójność ostateczna jest idealnym miejscem, w którym aplikacja nie wymaga żadnych gwarancji związanych z porządkowaniem. Przykłady obejmują liczbę ponownych tweetów, polubień lub komentarzy niewielowątkowych. Na poniższej ilustracji przedstawiono spójność ostateczną z notatkami muzycznymi.

  ![wideo](media/consistency-levels/eventual-consistency.gif)

## <a name="additional-reading"></a>Materiały uzupełniające

Aby dowiedzieć się więcej na temat pojęć spójności, przeczytaj następujące artykuły:

- [TLA i specyfikacje wysokiego poziomu dla pięciu poziomów spójności oferowanych przez Azure Cosmos DB](https://github.com/Azure/azure-cosmos-tla)
- [Spójność zreplikowanego danych omówiona przez siatkówki (wideo) przez Doug Terry](https://www.youtube.com/watch?v=gluIh8zd26I)
- [Spójność zreplikowanego danych omówiona przez siatkówki (oficjalny dokument) przez Doug Terry](https://www.microsoft.com/en-us/research/publication/replicated-data-consistency-explained-through-baseball/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F157411%2Fconsistencyandbaseballreport.pdf)
- [Gwarancje sesji dla słabo spójnych replikowanych danych](https://dl.acm.org/citation.cfm?id=383631)
- [Wady spójności w projekcie nowoczesnej rozproszonej bazy danych: CAP jest tylko częścią artykułu](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Nieprobabilistycznea (PBS) w przypadku praktycznych częściowych kworum](https://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
- [Ostatecznie spójne — oglądany](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat poziomów spójności w usłudze Azure Cosmos DB, przeczytaj następujące artykuły:

* [Wybierz odpowiedni poziom spójności dla aplikacji](consistency-levels-choosing.md)
* [Poziomy spójności w interfejsach API Azure Cosmos DB](consistency-levels-across-apis.md)
* [Wady dostępności i wydajności dla różnych poziomów spójności](consistency-levels-tradeoffs.md)
* [Konfigurowanie domyślnego poziomu spójności](how-to-manage-consistency.md#configure-the-default-consistency-level)
* [Zastąp domyślny poziom spójności](how-to-manage-consistency.md#override-the-default-consistency-level)

