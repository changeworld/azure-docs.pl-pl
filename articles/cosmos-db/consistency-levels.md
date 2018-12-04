---
title: Poziomy spójności w usłudze Azure Cosmos DB
description: Usługa Azure Cosmos DB ma pięć poziomów spójności, aby ułatwić równoważenie ostatecznej spójnością, dostępnością i opóźnieniem wad i zalet.
keywords: eventual consistency, azure cosmos db, azure, Microsoft azure
services: cosmos-db
author: aliuy
ms.author: andrl
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/27/2018
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 075f79707dda4683c288163ec7bfdbf9807e153f
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52834880"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Poziomy spójności w usłudze Azure Cosmos DB

Rozproszonych baz danych, które opierają się na replikację, wysoką dostępność, małe opóźnienia lub obu, należy podstawowych zależnościami między spójności odczytu, a dostępność, opóźnienia i przepływności. Większość komercyjnego rozproszonych baz danych, poproś deweloperów dokonać wyboru między dwoma modelami spójności extreme: wysoki poziom spójności i spójności ostatecznej.  [Atomowych](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) lub standardy programowania danych modelu silnej spójności. Ale dodaje wymaga początkowo dużej ilości cena czas oczekiwania (w stanie stabilnym) i ograniczoną dostępnością (podczas awarii). Z drugiej strony spójność ostateczną zapewnia większą dostępność i lepszą wydajność, ale jest trudny do aplikacji. 

Wyjaśnienie pojęcia spójności danych jako liczne opcje zamiast dwoma skrajnymi poziomami zbliża się do usługi Azure Cosmos DB. Wysoki poziom spójności i spójności ostatecznej znajdują się na końcach, ale istnieje wiele opcji spójności wzdłuż spektrum. Deweloperzy mogą używać tych opcji, opcji dokładne i szczegółową skutków ubocznych dotyczących wysokiej dostępności lub wydajności. 

Usługa Azure Cosmos DB deweloperzy mogą wybierać pięć dokładnie zdefiniowanych modeli spójności na spektrum spójności. Od najsilniejszej do najsłabszej, modele są silne, powiązana nieaktualność, sesja, spójny prefiks i "eventual". Modele są dobrze zdefiniowany i intuicyjne. Służy do określonych scenariuszy w rzeczywistych warunkach. Każdy model zawiera [wpływ na dostępność i wydajność](consistency-levels-tradeoffs.md) i jest wspierana przez kompleksowe umowy SLA. Na poniższej ilustracji przedstawiono poziomów spójności różnych jako spektrum.

![Spójność o szerokim zakresie funkcji](./media/consistency-levels/five-consistency-levels.png)

Poziomy spójności są niezależne od regionu. Poziom spójności konta usługi Azure Cosmos jest gwarantowane, dla wszystkich operacji niezależnie od określonego regionu, w którym odczyty i zapisy są one obsługiwane, przez liczbę regionów skojarzonych z Twoim kontem usługi Azure Cosmos odczytu, czy Twoje konto jest skonfigurowane przy użyciu jednego lub wielu regionach zapisu.

## <a name="scope-of-the-read-consistency"></a>Zakres spójności odczytu

Kondycja spójności odczytu mają zastosowanie do jednej operacji odczytu, o określonym zakresie w ramach zakresu klucza partycji lub partycji logicznej. Operacja odczytu mogą być wystawiane przez klienta zdalnego lub procedury składowanej.

## <a name="configure-the-default-consistency-level"></a>Konfigurowanie domyślnego poziomu spójności

W dowolnym momencie można skonfigurować domyślny poziom spójności na Twoim koncie usługi Azure Cosmos. Domyślny poziom spójności skonfigurowane na Twoje konto ma zastosowanie do wszystkich baz danych Azure Cosmos DB i kontenerów w ramach tego konta. Wszystkie operacje odczytu i zapytania względem kontenera lub bazę danych domyślnie używają poziomu spójności określony. Aby dowiedzieć się więcej, zobacz temat jak [skonfigurować domyślny poziom spójności](how-to-manage-consistency.md#configure-the-default-consistency-level).

## <a name="guarantees-associated-with-consistency-levels"></a>Gwarancje skojarzone z poziomów spójności

Kompleksowe umowy SLA, udostępniane przez usługi Azure Cosmos DB gwarancji, że 100 procent żądań odczytu spełnia gwarancję spójności dla dowolnego poziomu spójności, że wybrano. Żądania odczytu spełnia spójności umowy SLA, jeśli spełnione są wszystkie gwarancje spójności skojarzone z poziomu spójności. Dokładne definicje poziomów spójności pięć w usłudze Azure Cosmos DB przy użyciu [języka TLA + specyfikacji](http://lamport.azurewebsites.net/tla/tla.html) znajdują się w [azure-cosmos-tla](https://github.com/Azure/azure-cosmos-tla) repozytorium GitHub. 

Semantyka poziomów spójności pięć są opisane poniżej:

- **Silne**: zapewnia wysoki poziom spójności [atomowych](https://aphyr.com/posts/313-strong-consistency-models) gwarantuje. Operacje odczytu mają gwarancję do zwrócenia zatwierdzone najbardziej aktualną wersję elementu. Klient nigdy nie widzi zapisu niezatwierdzone lub jego część. Użytkownicy są zawsze gwarantowane odczyt najnowsza wersja zatwierdzone/zapis.

- **Powiązana nieaktualność**: operacje odczytu mają gwarancję respektować gwarancja spójnego prefiksu. Odczyty mogą być opóźnione stosunku do zapisów przez co najwyżej wersje "K" (to znaczy "aktualizacji") elementu lub przedziału czasu "t". Po wybraniu powiązana nieaktualność, "nieaktualność", można skonfigurować na dwa sposoby: 

  * Liczba wersji (KB) elementu
  * Za pomocą którego odczyty mogą być opóźnione w stosunku do zapisów przedział czasu (t) 

  Powiązana nieaktualność oferty całkowitej globalnej kolejności z wyjątkiem w ramach "okno nieaktualność." Istnieje monotoniczny gwarancje odczytu w regionie wewnątrz lub na zewnątrz okna nieaktualność. Wysoki poziom spójności ma tą samą semantyką jako te oferowane przez powiązana nieaktualność. Okno nieaktualność jest równa zero. Powiązana nieaktualność jest również określany jako atomowych opóźnione czasu. Gdy klient wykonuje operacje odczytu w regionie, który akceptuje zapisy, gwarancji spójności powiązana nieaktualność są identyczne te gwarancje silnej spójności.

- **Sesja**: operacje odczytu mają gwarancję respektować spójny prefiks (przy założeniu sesji jednego elementu "zapisującego"), monotoniczne odczyty, gwarantuje monotoniczny zapisu, odczytu swoich zapisów i write poniżej — operacje odczytu. Spójność sesji jest ograniczony do sesji klienta.

- **Spójny prefiks**: aktualizacje, które są zwracane zawierają pewne prefiksy ze wszystkich aktualizacji, bez przerw. Spójny prefiks gwarantuje, że odczyty nigdy nie zobaczy zapisów poza kolejnością.

- **Ostateczna**: nie ma żadnej gwarancji szeregowania dla odczytów. W przypadku braku dalszy zapis replik ostatecznie zbiegają się.

## <a name="consistency-levels-explained-through-baseball"></a>Poziomy spójności baseballu

Jako przykład Weźmy mecz scenariusz gier. Wyobraź sobie sekwencji operacji zapisu, które reprezentują wynik z baseballu. Ocena linii inning przez inning jest opisana w [replikowane spójności danych przy użyciu mecz](https://www.microsoft.com/en-us/research/wp-content/uploads/2011/10/ConsistencyAndBaseballReport.pdf) papieru. Ta hipotetyczny baseballu jest obecnie w trakcie inning siódmego. Jest stretch inning — siódmego. Gości znajdują się za z wynikiem 2 do 5.

| | **1** | **2** | **3** | **4** | **5** | **6** | **7** | **8** | **9** | **Przebiegi** |
| - | - | - | - | - | - | - | - | - | - | - |
| **Goście** | 0 | 0 | 1 | 0 | 1 | 0 | 0 |  |  | 2 |
| **Strona główna** | 1 | 0 | 1 | 1 | 0 | 2 |  |  |  | 5 |

Kontener usługi Azure Cosmos DB zawiera odwiedzających i domowej zespołu Uruchom sum. Gra w trakcie, różnych odczytu gwarancje może prowadzić do odczytywania różne wyniki klientom. W poniższej tabeli wymieniono kompletny zestaw wyników, które mogą być zwrócone przez odczytywanie odwiedzających i macierzystego wyniki z każdym gwarancje spójności pięć. Osoby odwiedzające wynik jest wymienione jako pierwsze. Różne możliwe wartości zwracane są oddzielone przecinkami.

| **Poziom spójności** | **Wyniki** |
| - | - |
| **Silne** | 2 – 5 |
| **Powiązana nieaktualność** | Wyniki są co najwyżej jeden inning nieaktualna: 2-3, 2 – 4, 2 – 5 |
| **Sesji** | <ul><li>Dla modułu zapisującego: 2 – 5</li><li> dla każdego z wyjątkiem moduł zapisujący: 0-0, 0-1, 0-2, 0 – 3, 0 4, 0-5, 1-0, 1-1, 1 – 2, 1 – 3, 1 – 4, 1 – 5, 2-0, 2-1, 2-2, 2 i 3, 2 – 4, 2 – 5</li><li>Po przeczytaniu 1-3: 1-3, 1 – 4, 1 – 5, 2 i 3, 2 – 4, 2 – 5</li> |
| **Spójny prefiks** | 0-0, 0 – 1, 1-1, 1 – 2, 1 – 3, 2 i 3, 2 – 4, 2 – 5 |
| **Ostateczna** | 0-0, 0-1, 0-2, 0 – 3, 0 4, 0-5, 1-0, 1-1, 1 – 2, 1 – 3, 1 – 4, 1 – 5, 2-0, 2-1, 2-2, 2 i 3, 2 – 4, 2 – 5 |

## <a name="additional-reading"></a>Materiały uzupełniające

Aby dowiedzieć się więcej na temat pojęć spójności, przeczytaj następujące artykuły:

- [Ogólne TLA + specyfikacje dotyczące poziomów spójności pięć oferowanych przez usługę Azure Cosmos DB](https://github.com/Azure/azure-cosmos-tla)
- [Replikowane dane spójności wyjaśniono za pośrednictwem mecz (wideo) przez Doug Terry](https://www.youtube.com/watch?v=gluIh8zd26I)
- [Replikowane dane spójności wyjaśniono za pośrednictwem mecz (dokument oficjalny) przez Doug Terry](https://www.microsoft.com/en-us/research/publication/replicated-data-consistency-explained-through-baseball/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F157411%2Fconsistencyandbaseballreport.pdf)
- [Sesja gwarancje słabo spójności replikowanych danych](https://dl.acm.org/citation.cfm?id=383631)
- [Wady i zalety spójności w nowoczesny wygląd systemy bazy danych dystrybucji: limit to tylko część wątku](https://www.computer.org/web/csdl/index/-/csdl/mags/co/2012/02/mco2012020037-abs.html)
- [Powiązana nieaktualność probabilistyczny (PBS) dla praktyczne kworum częściowe](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
- [Ostatecznie spójny — poprawiony](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html)

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat poziomów spójności w usłudze Azure Cosmos DB, przeczytaj następujące artykuły:

* [Wybierz poziom spójności odpowiednie dla twojej aplikacji](consistency-levels-choosing.md)
* [Poziomy spójności w różnych interfejsów API usługi Azure Cosmos DB](consistency-levels-across-apis.md)
* [Dostępność i wydajność kompromisy dla różnych poziomów spójności](consistency-levels-tradeoffs.md)
* [Skonfiguruj domyślny poziom spójności](how-to-manage-consistency.md#configure-the-default-consistency-level)
* [Przesłonić domyślny poziom spójności](how-to-manage-consistency.md#override-the-default-consistency-level)

