---
title: Poziomy spójności w usłudze Azure Cosmos DB | Dokumentacja firmy Microsoft
description: Usługa Azure Cosmos DB ma pięć poziomów spójności, aby ułatwić równoważenie ostatecznej spójnością, dostępnością i opóźnieniem wad i zalet.
keywords: eventual consistency, azure cosmos db, azure, Microsoft azure
services: cosmos-db
author: aliuy
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: andrl
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6ace11cf3704ddbd503c0202d45874670476198e
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2018
ms.locfileid: "51624831"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Poziomy spójności w usłudze Azure Cosmos DB

Rozproszonych baz danych, opierając się na replikację, wysoką dostępność, małych opóźnień, czy oba rodzaje, wprowadzić podstawowe zależnościami między spójności odczytu, a dostępność, opóźnienia i przepływności. Większość komercyjnego rozproszonych baz danych, poproś deweloperów dokonać wyboru między dwoma modelami spójności extreme: wysoki poziom spójności i spójności ostatecznej. Gdy [atomowych](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) lub modelu silnej spójności jest standardy programowania danych, dodaje wymaga początkowo dużej ilości cena czas oczekiwania (w stanie stabilnym) i ograniczoną dostępnością (podczas awarii). Z drugiej strony spójność ostateczną zapewnia większą dostępność i lepszą wydajność, ale jest trudny do aplikacji.

Wyjaśnienie pojęcia spójności danych jako liczne opcje zamiast dwoma skrajnymi poziomami zbliża się do usługi cosmos DB. Mimo że wysoki poziom spójności i spójności ostatecznej po obu stronach spektrum, istnieją spójności wyborze technologii odpowiedniej wzdłuż spektrum. Te opcje spójności umożliwiają deweloperom opcje dokładne i szczegółową skutków ubocznych dotyczących wysokiej dostępności lub wydajności. Usługa cosmos DB umożliwiły deweloperom do wyboru spośród pięciu dobrze zdefiniowanych modeli spójności (najsilniejszej do najsłabszej) — spektrum spójności **silne**, **powiązana nieaktualność**, **sesji** , **spójny prefiks**, i **ostatecznej**. Każda z tych modeli spójności jest dobrze zdefiniowany i intuicyjny i może służyć do określonych scenariuszy w rzeczywistych warunkach. Każda z pięcioma modelami spójności oferuje [wpływ na dostępność i wydajność](consistency-levels-tradeoffs.md) i jest wspierana przez kompleksowe umowy SLA. Na poniższej ilustracji przedstawiono poziomów spójności różnych jako liczne:

![Spójność o szerokim zakresie funkcji](./media/consistency-levels/five-consistency-levels.png)

Poziomy spójności są niezależne od regionu. Poziom spójności konta usługi Cosmos DB gwarantuje dla wszystkich operacji niezależnie od określonego regionu, w którym odczyty i zapisy są one obsługiwane, przez liczbę regionów skojarzonych z Twojego konta usługi Cosmos lub tego, czy Twoje konto jest skonfigurowane za pomocą jednego odczytu lub wiele regionów zapisu.

## <a name="scope-of-the-read-consistency"></a>Zakres spójność odczytu

Spójność odczytu dotyczy jednego zakresu w zakresie klucz partycji (czyli partycji logicznej) Operacja odczytu. Operacja odczytu mogą być wystawiane przez klienta zdalnego lub procedury składowanej.

## <a name="configuring-the-default-consistency-level"></a>Konfigurowanie domyślny poziom spójności

Można skonfigurować **domyślny poziom spójności** na Twoim koncie usługi Cosmos DB, w dowolnym momencie. Domyślny poziom spójności skonfigurowane na Twoje konto ma zastosowanie do wszystkich baz danych Cosmos (lub kontenery) w ramach tego konta. Wszystkie operacje odczytu i zapytania względem kontenera lub bazy danych użyje poziomu spójności określonego domyślnie. Aby dowiedzieć się więcej, zobacz temat jak [skonfigurować domyślny poziom spójności](how-to-manage-consistency.md#configure-the-default-consistency-level) artykułu.

## <a name="guarantees-associated-with-consistency-levels"></a>Gwarancje skojarzone z poziomów spójności

Kompleksowe umowy SLA, dostarczone przez usługę Azure Cosmos DB gwarancji, że 100% żądań odczytu spełnia gwarancję spójności dla dowolnego poziomu spójności, że została wybrana. Żądania odczytu jest uważane za spełniające spójności umowy SLA, jeśli spełnione są wszystkie gwarancje spójności skojarzone z poziomu spójności. Dokładne definicje poziomów spójności pięć w usłudze Cosmos DB przy użyciu [języka TLA + specyfikacji](http://lamport.azurewebsites.net/tla/tla.html) znajdują się w [azure-cosmos-tla](https://github.com/Azure/azure-cosmos-tla) repozytorium GitHub. Semantyka poziomów spójności pięć zostały opisane poniżej:

- **Poziom spójności "strong" =**: zapewnia wysoki poziom spójności [atomowych](https://aphyr.com/posts/313-strong-consistency-models) gwarantuje z odczyty gwarancję zwracania zatwierdzone najbardziej aktualną wersję elementu. Klient nigdy nie zobaczą zapisu niezatwierdzone lub jego część. Użytkownicy są zawsze gwarantowane odczyt najnowsza wersja zatwierdzone/zapis.

- **Poziom spójności = "powiązana nieaktualność"**: operacje odczytu mają gwarancję respektować gwarancja spójnego prefiksu. Odczyty może być opóźniona zapisy, przez co najwyżej wersje K (to znaczy "aktualizacji" elementu) lub t "Interwał czasu. Podczas wybierania powiązana nieaktualność, "nieaktualność", można skonfigurować na dwa sposoby: 

  * Liczba wersji (KB) elementu lub
  * Przedział czasu (t) za pomocą którego odczytami może być opóźniona zapisy. 

  Powiązana nieaktualność oferty całkowitej globalnej kolejności z wyjątkiem w ramach "okno nieaktualność." Monotoniczny gwarancje odczytu istnieją w obrębie regionu zarówno wewnątrz i na zewnątrz "nieaktualność okna." Wysoki poziom spójności ma tą samą semantyką jako te oferowane przez powiązana nieaktualność i za pomocą "nieaktualność okno" równy zero. Powiązana nieaktualność jest również nazywany **opóźnione czasu atomowych**. Gdy klient wykonuje operacje odczytu w regionie, który akceptuje zapisy, gwarancji spójności powiązana nieaktualność są identyczne z tymi za pomocą silnej spójności.

- **Poziom spójności "sesja" =**: operacje odczytu mają gwarancję respektować spójny prefiks, monotoniczne odczyty, zapisy monotoniczny, gwarantuje odczytu swoich zapisów, write poniżej — operacje odczytu. Spójność sesji jest ograniczony do sesji klienta.

- **Poziom spójności = "spójny prefiks"**: aktualizacji zwracanych zawierają pewne prefiksy ze wszystkich aktualizacji, bez przerw. Spójny prefiks gwarancji, że odczyty nigdy nie zobaczy zapisów poza kolejnością.

- **Poziom spójności = "ostateczna"**: nie ma żadnej gwarancji szeregowania dla odczytów. W przypadku braku dalszy zapis replik ostatecznie zbiegają się.

## <a name="consistency-levels-explained-through-baseball"></a>Poziomy spójności baseballu

Umożliwia pobranie scenariusza gier mecz, na przykład, imagine sekwencji zapisów reprezentujący wynik baseballu, za pomocą oceny linii inning przez inning zgodnie z opisem w [replikowane spójności danych przy użyciu mecz](https://www.microsoft.com/en-us/research/wp-content/uploads/2011/10/ConsistencyAndBaseballReport.pdf) dokument. To hipotetyczny baseballu jest obecnie w trakcie siódmego inning (proverbial siódma — inning stretch), a domowej zespołu jest zastosowanie 2 – 5.

| | **1** | **2** | **3** | **4** | **5** | **6** | **7** | **8** | **9** | **Przebiegi** |
| - | - | - | - | - | - | - | - | - | - | - |
| **Goście** | 0 | 0 | 1 | 0 | 1 | 0 | 0 |  |  | 2 |
| **Strona główna** | 1 | 0 | 1 | 1 | 0 | 2 |  |  |  | 5 |

Kontenera usługi Cosmos DB przechowuje gości i sumy wykonywania domowej zespołu. Gra w trakcie, różnych odczytu gwarancje może spowodować klientom odczytywanie różne wyniki. W poniższej tabeli wymieniono kompletny zestaw wyników, które mogą być zwracane przez odczytanie odwiedzających i macierzystego wyniki z każdym gwarancje spójności pięć. Osoby odwiedzające wynik jest wymienione jako pierwsze, a różne możliwe wartości zwracane są oddzielone przecinkami.

| **Poziom spójności** | **Wyniki** |
| - | - |
| **Silne** | 2 – 5 |
| **Powiązana nieaktualność** | wyniki są w większości inning jeden nieaktualny"2-3, 2 – 4, 2 – 5 |
| **Sesji** | <ul><li>dla modułu zapisującego"2-5</li><li> dla każdego z wyjątkiem moduł zapisujący: 0-0, 0-1, 0-2, 0 – 3, 0 4, 0-5, 1-0, 1-1, 1 – 2, 1 – 3, 1 – 4, 1 – 5, 2-0, 2-1, 2-2, 2 i 3, 2 – 4, 2 – 5</li><li>Po przeczytaniu 1-3: 1-3, 1 – 4, 1 – 5, 2 i 3, 2 – 4, 2 – 5</li> |
| **Spójny prefiks** | 0-0, 0 – 1, 1-1, 1 – 2, 1 – 3, 2 i 3, 2 – 4, 2 – 5 |
| **Ostateczna** | 0-0, 0-1, 0-2, 0 – 3, 0 4, 0-5, 1-0, 1-1, 1 – 2, 1 – 3, 1 – 4, 1 – 5, 2-0, 2-1, 2-2, 2 i 3, 2 – 4, 2 – 5 |

## <a name="additional-reading"></a>Materiały uzupełniające

Aby dowiedzieć się więcej na temat pojęć spójności, przeczytaj następujące artykuły:

- [Ogólne TLA + specyfikacje dotyczące poziomów spójności pięć oferowanych przez usługę Azure Cosmos DB](https://github.com/Azure/azure-cosmos-tla)
- [Replikowane dane spójności baseballu (wideo) przez Doug Terry](https://www.youtube.com/watch?v=gluIh8zd26I)
- [Replikowane dane spójności baseballu (dokument oficjalny) przez Doug Terry](https://www.microsoft.com/en-us/research/publication/replicated-data-consistency-explained-through-baseball/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F157411%2Fconsistencyandbaseballreport.pdf)
- [Sesja gwarancje słabo spójności replikowanych danych](https://dl.acm.org/citation.cfm?id=383631)
- [Wady i zalety spójności w nowoczesny wygląd systemy bazy danych dystrybucji: limit to tylko część wątku](https://www.computer.org/web/csdl/index/-/csdl/mags/co/2012/02/mco2012020037-abs.html)
- [Powiązana nieaktualność probabilistyczny (PBS) dla praktyczne kworum częściowe](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
- [Ostatecznie spójny — poprawiony](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html)

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat poziomów spójności w usłudze Cosmos DB, przeczytaj następujące artykuły:

* [Wybieranie poziomu spójności odpowiednie dla twojej aplikacji](consistency-levels-choosing.md)
* [Poziomy spójności między Cosmos DB z interfejsów API](consistency-levels-across-apis.md)
* [Dostępność i wydajność kompromisy dla różnych poziomów spójności](consistency-levels-tradeoffs.md)
* [Jak skonfigurować domyślny poziom spójności](how-to-manage-consistency.md#configure-the-default-consistency-level)
* [Jak zastąpić domyślny poziom spójności](how-to-manage-consistency.md#override-the-default-consistency-level)

