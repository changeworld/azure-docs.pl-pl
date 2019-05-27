---
title: Poziomy spójności w usłudze Azure Cosmos DB
description: Usługa Azure Cosmos DB ma pięć poziomów spójności, aby ułatwić równoważenie ostatecznej spójnością, dostępnością i opóźnieniem wad i zalet.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.openlocfilehash: dcd51756a9c5a5a24a082862bb911cc2d2605d61
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2019
ms.locfileid: "65954363"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Poziomy spójności w usłudze Azure Cosmos DB

Rozproszonych baz danych, które opierają się na replikację, wysoką dostępność, małe opóźnienia lub obu, należy podstawowych zależnościami między spójności odczytu, a dostępność, opóźnienia i przepływności. Większość komercyjnego rozproszonych baz danych, poproś deweloperów dokonać wyboru między dwoma modelami spójności extreme: *silne* spójności i *ostatecznej* spójności.  [Atomowych](https://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) lub standardy programowania danych modelu silnej spójności. Ale dodaje cenie czas oczekiwania (w stanie stabilnym) i ograniczoną dostępnością (podczas awarii). Z drugiej strony spójność ostateczną oferuje większą dostępność i lepszą wydajność, ale utrudniało to aplikacje programu. 

Wyjaśnienie pojęcia spójności danych jako liczne opcje zamiast dwoma skrajnymi poziomami zbliża się do usługi Azure Cosmos DB. Wysoki poziom spójności i spójności ostatecznej jest na końcu spektrum, ale istnieje wiele opcji spójności wzdłuż spektrum. Deweloperzy mogą używać tych opcji, opcji dokładne i szczegółową kompromisów w zakresie wysokiej dostępności i wydajności. 

Usługa Azure Cosmos DB deweloperzy mogą wybierać pięć dokładnie zdefiniowanych modeli spójności na spektrum spójności. Od najsilniejszej do bardziej złagodzone, modele obejmują *silne*, *powiązana nieaktualność*, *sesji*, *spójny prefiks*i *ostatecznej* spójności. Modele są dobrze zdefiniowany i intuicyjne i może służyć do określonych scenariuszy w rzeczywistych warunkach. Każdy model zawiera [wpływ na dostępność i wydajność](consistency-levels-tradeoffs.md) i jest uzupełniana przez umów SLA. Na poniższej ilustracji przedstawiono poziomów spójności różnych jako spektrum.

![Spójność o szerokim zakresie funkcji](./media/consistency-levels/five-consistency-levels.png)

Poziomy spójności są niezależne od regionu i gwarantują dla wszystkich operacji niezależnie od określonego regionu, w którym odczyty i zapisy są one obsługiwane, przez liczbę regionów skojarzonych z Twoim kontem usługi Azure Cosmos lub tego, czy Twoje konto jest skonfigurowane za pomocą jednego lub wielu regionach zapisu.

## <a name="scope-of-the-read-consistency"></a>Zakres spójności odczytu

Kondycja spójności odczytu mają zastosowanie do jednej operacji odczytu, o określonym zakresie w ramach zakresu klucza partycji lub partycji logicznej. Operacja odczytu mogą być wystawiane przez klienta zdalnego lub procedury składowanej.

## <a name="configure-the-default-consistency-level"></a>Konfigurowanie domyślnego poziomu spójności

W dowolnym momencie można skonfigurować domyślny poziom spójności na Twoim koncie usługi Azure Cosmos. Domyślny poziom spójności skonfigurowane na Twoim koncie ma zastosowanie do wszystkich baz danych Azure Cosmos i kontenerów w ramach tego konta. Wszystkie operacje odczytu i zapytania względem kontenera lub bazę danych domyślnie używają poziomu spójności określony. Aby dowiedzieć się więcej, zobacz temat jak [skonfigurować domyślny poziom spójności](how-to-manage-consistency.md#configure-the-default-consistency-level).

## <a name="guarantees-associated-with-consistency-levels"></a>Gwarancje skojarzone z poziomów spójności

Kompleksowe umowy SLA, udostępniane przez usługi Azure Cosmos DB gwarancji, że 100 procent żądań odczytu spełnia gwarancję spójności dla dowolnego poziomu spójności, że wybrano. Żądania odczytu spełnia spójności umowy SLA, jeśli spełnione są wszystkie gwarancje spójności skojarzone z poziomu spójności. Dokładne definicje poziomów spójności pięć w usłudze Azure Cosmos DB przy użyciu [języka TLA + specyfikacji](https://lamport.azurewebsites.net/tla/tla.html) znajdują się w [azure-cosmos-tla](https://github.com/Azure/azure-cosmos-tla) repozytorium GitHub. 

Semantyka poziomów spójności pięć są opisane poniżej:

- **Silne**: Zapewnia wysoki poziom spójności [atomowych](https://aphyr.com/posts/313-strong-consistency-models) gwarantuje. Operacje odczytu mają gwarancję do zwrócenia zatwierdzone najbardziej aktualną wersję elementu. Klient nigdy nie widzi zapisu niezatwierdzone lub jego część. Użytkownicy są zawsze gwarantowane odczyt najnowsza wersja zatwierdzone/zapis.

- **Powiązana nieaktualność**: Operacje odczytu mają gwarancję respektować gwarancja spójnego prefiksu. Odczyty mogą być opóźnione stosunku do zapisów o co najwyżej *"K"* wersji (czyli "aktualizacji") elementu lub przez *"T"* przedział czasu. Innymi słowy gdy wybierzesz powiązana nieaktualność, "nieaktualność" można skonfigurować na dwa sposoby: 

  * Liczba wersji (*K*) elementu
  * Przedział czasu (*T*), który odczyty mogą być opóźnione w stosunku do zapisów 

  Powiązana nieaktualność oferty całkowitej globalnej kolejności z wyjątkiem w ramach "okno nieaktualność." Istnieje monotoniczny gwarancje odczytu w regionie wewnątrz lub na zewnątrz okna nieaktualność. Wysoki poziom spójności ma tą samą semantyką jako jeden oferowany przez powiązana nieaktualność. Okno nieaktualność jest równa zero. Powiązana nieaktualność jest również określany jako atomowych opóźnione czasu. Gdy klient wykonuje operacje odczytu w regionie, który akceptuje zapisy, gwarancji spójności powiązana nieaktualność są identyczne z tych gwarancji, wysoki poziom spójności.

- **Sesja**: Operacje odczytu mają gwarancję respektować spójny prefiks (przy założeniu sesji jednego elementu "zapisującego"), monotoniczne odczyty, zapisy monotoniczny, gwarancje odczytu swoich zapisów i write poniżej — operacje odczytu. Spójność sesji jest ograniczony do sesji klienta.

- **Spójny prefiks**: Aktualizacje, które są zwracane zawierają pewne prefiksy ze wszystkich aktualizacji, bez przerw. Poziom spójności spójny prefiks gwarantuje, że odczyty nigdy nie zobaczy zapisów poza kolejnością.

- **Ostateczna**: Nie ma szeregowania gwarancji dla odczytów. W przypadku braku dalszy zapis replik ostatecznie zbiegają się.

## <a name="consistency-levels-explained-through-baseball"></a>Poziomy spójności baseballu

Jako przykład Weźmy mecz scenariusz gier. Wyobraź sobie sekwencji operacji zapisu, które reprezentują wynik z baseballu. Ocena linii inning przez inning jest opisana w [replikowane spójności danych przy użyciu mecz](https://www.microsoft.com/en-us/research/wp-content/uploads/2011/10/ConsistencyAndBaseballReport.pdf) papieru. Ta hipotetyczny baseballu jest obecnie w trakcie inning siódmego. Jest stretch inning — siódmego. Za z wynikiem 2 do 5 są gości, jak pokazano poniżej:

| | **1** | **2** | **3** | **4** | **5** | **6** | **7** | **8** | **9** | **Przebiegi** |
| - | - | - | - | - | - | - | - | - | - | - |
| **Goście** | 0 | 0 | 1 | 0 | 1 | 0 | 0 |  |  | 2 |
| **Strona główna** | 1 | 0 | 1 | 1 | 0 | 2 |  |  |  | 5 |

Kontener usługi Azure Cosmos przechowuje wykonywania łączne wartości dla gości i zespołów macierzystego. Gra w trakcie, różnych odczytu gwarancje może prowadzić do odczytywania różne wyniki klientom. W poniższej tabeli wymieniono kompletny zestaw wyników, które mogą być zwrócone przez odczytywanie odwiedzających i macierzystego wyniki z każdym gwarancje spójności pięć. Osoby odwiedzające wynik jest wymienione jako pierwsze. Różne możliwe wartości zwracane są oddzielone przecinkami.

| **Poziom spójności** | **Ocenia (odwiedzających, strona główna)** |
| - | - |
| **Silne** | 2 – 5 |
| **Powiązana nieaktualność** | Wyniki są co najwyżej jeden inning nieaktualna: 2-3, 2-4, 2-5 |
| **Sesji** | <ul><li>Dla modułu zapisującego: 2 – 5</li><li> Dla każdego z wyjątkiem składnika zapisywania programu: 0-0, 0-1, 0-2, 0 – 3, 0 4, 0-5, 1-0, 1-1, 1 – 2, 1 – 3, 1 – 4, 1 – 5, 2-0, 2-1, 2-2, 2 i 3, 2 – 4, 2 – 5</li><li>Po przeczytaniu 1-3: 1-3, 1-4, 1-5, 2-3, 2-4, 2-5</li> |
| **Spójny prefiks** | 0-0, 0 – 1, 1-1, 1 – 2, 1 – 3, 2 i 3, 2 – 4, 2 – 5 |
| **Ostateczna** | 0-0, 0-1, 0-2, 0 – 3, 0 4, 0-5, 1-0, 1-1, 1 – 2, 1 – 3, 1 – 4, 1 – 5, 2-0, 2-1, 2-2, 2 i 3, 2 – 4, 2 – 5 |

## <a name="additional-reading"></a>Materiały uzupełniające

Aby dowiedzieć się więcej na temat pojęć spójności, przeczytaj następujące artykuły:

- [Ogólne TLA + specyfikacje dotyczące poziomów spójności pięć oferowanych przez usługę Azure Cosmos DB](https://github.com/Azure/azure-cosmos-tla)
- [Replikowane dane spójności wyjaśniono za pośrednictwem mecz (wideo) przez Doug Terry](https://www.youtube.com/watch?v=gluIh8zd26I)
- [Replikowane dane spójności wyjaśniono za pośrednictwem mecz (dokument oficjalny) przez Doug Terry](https://www.microsoft.com/en-us/research/publication/replicated-data-consistency-explained-through-baseball/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F157411%2Fconsistencyandbaseballreport.pdf)
- [Sesja gwarancje słabo spójności replikowanych danych](https://dl.acm.org/citation.cfm?id=383631)
- [Wady i zalety spójności w nowoczesnym rozproszonej bazy danych, projektowanie systemów: LIMIT to tylko część wątku](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Powiązana nieaktualność probabilistyczny (PBS) dla praktyczne kworum częściowe](https://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
- [Ostatecznie spójny — poprawiony](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html)

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat poziomów spójności w usłudze Azure Cosmos DB, przeczytaj następujące artykuły:

* [Wybierz poziom spójności odpowiednie dla twojej aplikacji](consistency-levels-choosing.md)
* [Poziomy spójności w różnych interfejsów API usługi Azure Cosmos DB](consistency-levels-across-apis.md)
* [Dostępność i wydajność kompromisy dla różnych poziomów spójności](consistency-levels-tradeoffs.md)
* [Skonfiguruj domyślny poziom spójności](how-to-manage-consistency.md#configure-the-default-consistency-level)
* [Przesłonić domyślny poziom spójności](how-to-manage-consistency.md#override-the-default-consistency-level)

