---
title: Poziomy spójności w usłudze Azure Cosmos DB
description: Usługa Azure Cosmos DB ma pięć poziomów spójności, aby ułatwić równoważenie ostatecznej spójnością, dostępnością i opóźnieniem wad i zalet.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/16/2019
ms.openlocfilehash: bb1fda48119785f5173790246f8069d3e11b6dae
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297741"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Poziomy spójności w usłudze Azure Cosmos DB

Rozproszonych baz danych, które opierają się na replikację, wysoką dostępność, małe opóźnienia lub obu, należy podstawowych zależnościami między spójności odczytu, a dostępność, opóźnienia i przepływności. Najbardziej komercyjnie dostępne bazy danych umożliwiają deweloperom wybór między dwoma ekstremalnymi modelami spójności  : silną spójnością i spójnością *ostateczną* . Model linearizability lub silna spójność jest standardem dla programowania danych. Jednak dodaje cenę o wyższym opóźnieniu (w stanie stałym) i ograniczoną dostępność (podczas niepowodzeń). Z drugiej strony spójność ostateczna zapewnia wyższą dostępność i lepszą wydajność, ale utrudnia Programowanie aplikacji. 

Wyjaśnienie pojęcia spójności danych jako liczne opcje zamiast dwoma skrajnymi poziomami zbliża się do usługi Azure Cosmos DB. Ścisła spójność i ostateczna spójność znajdują się na końcu spektrum, ale istnieje wiele opcji spójności w spektrum. Deweloperzy mogą korzystać z tych opcji, aby wybierać precyzyjne i szczegółowe kompromisy w odniesieniu do wysokiej dostępności i wydajności. 

Usługa Azure Cosmos DB deweloperzy mogą wybierać pięć dokładnie zdefiniowanych modeli spójności na spektrum spójności. Z najsilniejszych do bardziej swobodnych modeli zalicza się *silne*, *ograniczone*nieaktualność, *sesję*, *spójny prefiks*i spójność *ostateczną* . Modele są dobrze zdefiniowane i intuicyjne i mogą być używane w określonych rzeczywistych scenariuszach. Każdy model zapewnia [wady dostępności i wydajności](consistency-levels-tradeoffs.md) i jest obsługiwany przez umowy SLA. Na poniższej ilustracji przedstawiono różne poziomy spójności jako spektrum.

![Spójność o szerokim zakresie funkcji](./media/consistency-levels/five-consistency-levels.png)

Poziomy spójności to region-niezależny od i są gwarantowane dla wszystkich operacji niezależnie od regionu, w którym są obsługiwane odczyty i zapisy, liczbę regionów skojarzonych z kontem usługi Azure Cosmos lub czy Twoje konto jest skonfigurowane z użyciem jednego lub wiele regionów zapisu.

## <a name="scope-of-the-read-consistency"></a>Zakres spójności odczytu

Kondycja spójności odczytu mają zastosowanie do jednej operacji odczytu, o określonym zakresie w ramach zakresu klucza partycji lub partycji logicznej. Operacja odczytu mogą być wystawiane przez klienta zdalnego lub procedury składowanej.

## <a name="configure-the-default-consistency-level"></a>Konfigurowanie domyślnego poziomu spójności

W dowolnym momencie można skonfigurować domyślny poziom spójności na Twoim koncie usługi Azure Cosmos. Domyślny poziom spójności skonfigurowany na Twoim koncie ma zastosowanie do wszystkich baz danych i kontenerów usługi Azure Cosmos w ramach tego konta. Wszystkie operacje odczytu i zapytania względem kontenera lub bazę danych domyślnie używają poziomu spójności określony. Aby dowiedzieć się więcej, zobacz temat jak [skonfigurować domyślny poziom spójności](how-to-manage-consistency.md#configure-the-default-consistency-level).

## <a name="guarantees-associated-with-consistency-levels"></a>Gwarancje skojarzone z poziomów spójności

Kompleksowe umowy SLA, udostępniane przez usługi Azure Cosmos DB gwarancji, że 100 procent żądań odczytu spełnia gwarancję spójności dla dowolnego poziomu spójności, że wybrano. Żądania odczytu spełnia spójności umowy SLA, jeśli spełnione są wszystkie gwarancje spójności skojarzone z poziomu spójności. Precyzyjne definicje pięciu poziomów spójności w Azure Cosmos DB przy użyciu języka specyfikacji TLA + są dostępne w repozytorium GitHub [Azure-Cosmos-tla](https://github.com/Azure/azure-cosmos-tla) .

Semantyka poziomów spójności pięć są opisane poniżej:

- **Silne**: Silna spójność oferuje gwarancję linearizability. Linearizability odwołuje się do obsługi żądań współbieżnie. Operacje odczytu mają gwarancję do zwrócenia zatwierdzone najbardziej aktualną wersję elementu. Klient nigdy nie widzi zapisu niezatwierdzone lub jego część. Użytkownicy są zawsze gwarantowane odczyt najnowsza wersja zatwierdzone/zapis.

- **Powiązana**nieaktualność: Odczyty są gwarantowane do przestrzegania gwarancji o spójnym prefiksie. Odczyty mogą być opóźnione w stosunku do zapisu o największej liczbie *"K"* (np. "aktualizacje") elementu lub interwału czasu *"T"* . Innymi słowy, w przypadku wybrania ograniczonej nieodświeżoności, "nieaktualność" można skonfigurować na dwa sposoby: 

  * Liczba wersji (*K*) elementu
  * Przedział czasu (*T*), przez który odczyty mogą być opóźnione za zapisem 

  Powiązana nieaktualność oferty całkowitej globalnej kolejności z wyjątkiem w ramach "okno nieaktualność." Istnieje monotoniczny gwarancje odczytu w regionie wewnątrz lub na zewnątrz okna nieaktualność. Silna spójność ma taką samą semantykę jak ta, która jest oferowana przez ograniczony czas nieodświeżony. Okno nieaktualność jest równa zero. Powiązana nieaktualność jest również określany jako atomowych opóźnione czasu. Gdy klient wykonuje operacje odczytu w obrębie regionu akceptującego zapisy, gwarancje wynikające z podanej nieodświeżonej spójności są identyczne z tymi gwarancjami przez silną spójność.

- **Sesja**:  W ramach jednej sesji klienta zagwarantowane jest przestrzeganie spójnego prefiksu (przy założeniu pojedynczej sesji "składnika zapisywania"), monotoniczny odczytów, monotoniczny zapisów, odczytów i zapisów, a ponadto gwarantuje zapis do odczytu. Klienci poza sesją wykonującą operacje zapisu będą widzieli ostateczną spójność.

- **Spójny prefiks**: Zwrócone aktualizacje zawierają niektóre prefiksy wszystkich aktualizacji bez przerw. Spójny poziom spójności prefiksu gwarantuje, że odczyty nigdy nie są wyświetlane.

- **Ostateczne**: Nie ma gwarancji porządkowania dla operacji odczytu. W przypadku braku dalszy zapis replik ostatecznie zbiegają się.

## <a name="consistency-levels-explained-through-baseball"></a>Poziomy spójności baseballu

Jako przykład Weźmy mecz scenariusz gier. Wyobraź sobie sekwencji operacji zapisu, które reprezentują wynik z baseballu. Ocena linii inning przez inning jest opisana w [replikowane spójności danych przy użyciu mecz](https://www.microsoft.com/en-us/research/wp-content/uploads/2011/10/ConsistencyAndBaseballReport.pdf) papieru. Ta hipotetyczny baseballu jest obecnie w trakcie inning siódmego. Jest stretch inning — siódmego. Odwiedzający znajdują się za wynikami od 2 do 5, jak pokazano poniżej:

| | **1** | **2** | **3** | **4** | **5** | **6** | **7** | **8** | **9** | **Przebiegi** |
| - | - | - | - | - | - | - | - | - | - | - |
| **Goście** | 0 | 0 | 1 | 0 | 1 | 0 | 0 |  |  | 2 |
| **Strona główna** | 1 | 0 | 1 | 1 | 0 | 2 |  |  |  | 5 |

Kontener usługi Azure Cosmos przechowuje podsumowania przebiegów dla odwiedzających i zespołów domowych. Gra w trakcie, różnych odczytu gwarancje może prowadzić do odczytywania różne wyniki klientom. W poniższej tabeli wymieniono kompletny zestaw wyników, które mogą być zwrócone przez odczytywanie odwiedzających i macierzystego wyniki z każdym gwarancje spójności pięć. Osoby odwiedzające wynik jest wymienione jako pierwsze. Różne możliwe wartości zwracane są oddzielone przecinkami.

| **Poziom spójności** | **Wyniki (Goście, dom)** |
| - | - |
| **Silne** | 2 – 5 |
| **Powiązana nieaktualność** | Wyniki, które są co najwyżej jedną inningą: 2-3, 2-4, 2-5 |
| **Sesji** | <ul><li>Dla składnika zapisywania: 2 – 5</li><li> Dla wszystkich osób poza modułem zapisywania: 0-0, 0-1, 0-2, 0 – 3, 0 4, 0-5, 1-0, 1-1, 1 – 2, 1 – 3, 1 – 4, 1 – 5, 2-0, 2-1, 2-2, 2 i 3, 2 – 4, 2 – 5</li><li>Po przeczytaniu 1-3: 1-3, 1-4, 1-5, 2-3, 2-4, 2-5</li> |
| **Spójny prefiks** | 0-0, 0 – 1, 1-1, 1 – 2, 1 – 3, 2 i 3, 2 – 4, 2 – 5 |
| **Ostateczna** | 0-0, 0-1, 0-2, 0 – 3, 0 4, 0-5, 1-0, 1-1, 1 – 2, 1 – 3, 1 – 4, 1 – 5, 2-0, 2-1, 2-2, 2 i 3, 2 – 4, 2 – 5 |

## <a name="additional-reading"></a>Materiały uzupełniające

Aby dowiedzieć się więcej na temat pojęć spójności, przeczytaj następujące artykuły:

- [Ogólne TLA + specyfikacje dotyczące poziomów spójności pięć oferowanych przez usługę Azure Cosmos DB](https://github.com/Azure/azure-cosmos-tla)
- [Replikowane dane spójności wyjaśniono za pośrednictwem mecz (wideo) przez Doug Terry](https://www.youtube.com/watch?v=gluIh8zd26I)
- [Replikowane dane spójności wyjaśniono za pośrednictwem mecz (dokument oficjalny) przez Doug Terry](https://www.microsoft.com/en-us/research/publication/replicated-data-consistency-explained-through-baseball/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F157411%2Fconsistencyandbaseballreport.pdf)
- [Sesja gwarancje słabo spójności replikowanych danych](https://dl.acm.org/citation.cfm?id=383631)
- [Założenia dotyczące spójności w nowoczesnych systemach rozproszonej bazy danych: CAP jest tylko częścią wątku](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Powiązana nieaktualność probabilistyczny (PBS) dla praktyczne kworum częściowe](https://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
- [Ostatecznie spójny — poprawiony](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html)

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat poziomów spójności w usłudze Azure Cosmos DB, przeczytaj następujące artykuły:

* [Wybierz poziom spójności odpowiednie dla twojej aplikacji](consistency-levels-choosing.md)
* [Poziomy spójności w różnych interfejsów API usługi Azure Cosmos DB](consistency-levels-across-apis.md)
* [Dostępność i wydajność kompromisy dla różnych poziomów spójności](consistency-levels-tradeoffs.md)
* [Skonfiguruj domyślny poziom spójności](how-to-manage-consistency.md#configure-the-default-consistency-level)
* [Przesłonić domyślny poziom spójności](how-to-manage-consistency.md#override-the-default-consistency-level)

