---
title: Poziomy spójności w usłudze Azure Cosmos DB
description: Usługa Azure Cosmos DB ma pięć poziomów spójności, aby zrównoważyć ostateczną spójność, dostępność i opóźnienia kompromisów.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: e5966f142ece32f148c56edb5b0ef5dfd88603aa
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380076"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Poziomy spójności w usłudze Azure Cosmos DB

Rozproszone bazy danych, które opierają się na replikacji dla wysokiej dostępności, małe opóźnienia lub obu, dokonać podstawowych kompromis między spójności odczytu a dostępności, opóźnienia i przepływności. Większość dostępnych na rynku rozproszonych baz danych prosi deweloperów o wybór między dwoma modelami skrajnej spójności: *silną* spójnością i *spójnością ostateczną.* Linearizability modelu silnej spójności jest złoty standard programowalności danych. Ale dodaje cenę wyższego opóźnienia zapisu (w stanie stacjonarnym) i zmniejszonej dostępności (podczas awarii). Z drugiej strony spójność ostateczna oferuje większą dostępność i lepszą wydajność, ale utrudnia programowania aplikacji.

Usługa Azure Cosmos DB podejścia spójności danych jako spektrum wyborów zamiast dwóch skrajności. Deweloperzy mogą korzystać z tych opcji, aby dokonać precyzyjnych wyborów i szczegółowe kompromisy w odniesieniu do wysokiej dostępności i wydajności.

Dzięki usłudze Azure Cosmos DB deweloperzy mogą wybierać spośród pięciu dobrze zdefiniowanych poziomów spójności w spektrum spójności. Poziomy te obejmują *silny,* *ograniczony nieaktualność,* *sesję,* *spójny prefiks*i *spójność ostateczną.* Poziomy są dobrze zdefiniowane i intuicyjne i mogą być używane w określonych scenariuszach rzeczywistych. Każdy poziom zapewnia [dostępność i wydajność kompromisy](consistency-levels-tradeoffs.md) i są wspierane przez umowy SLA. Na poniższej ilustracji przedstawiono różne poziomy spójności jako widmo.

![Spójność jako widmo](./media/consistency-levels/five-consistency-levels.png)

Poziomy spójności są niezależne od regionu i są gwarantowane dla wszystkich operacji, niezależnie od regionu, z którego są obsługiwane odczyty i zapisy, liczba regionów skojarzonych z kontem usługi Azure Cosmos lub czy twoje konto jest skonfigurowane z jednym lub wieloma regionami zapisu.

## <a name="scope-of-the-read-consistency"></a>Zakres spójności odczytu

Spójność odczytu ma zastosowanie do pojedynczej operacji odczytu o zakresie w ramach partycji logicznej. Operacja odczytu może być wystawiona przez klienta zdalnego lub procedury składowanej.

## <a name="configure-the-default-consistency-level"></a>Konfigurowanie domyślnego poziomu spójności

Domyślny poziom spójności na koncie usługi Azure Cosmos można skonfigurować w dowolnym momencie. Domyślny poziom spójności skonfigurowany na koncie ma zastosowanie do wszystkich baz danych i kontenerów usługi Azure Cosmos w ramach tego konta. Wszystkie odczyty i kwerendy wystawione dla kontenera lub bazy danych domyślnie używają określonego poziomu spójności. Aby dowiedzieć się więcej, zobacz, jak [skonfigurować domyślny poziom spójności](how-to-manage-consistency.md#configure-the-default-consistency-level).

## <a name="guarantees-associated-with-consistency-levels"></a>Gwarancje związane z poziomami spójności

Kompleksowe umowy SLA dostarczone przez usługę Azure Cosmos DB gwarantują, że 100 procent żądań odczytu spełnia gwarancję spójności dla dowolnego wybranego poziomu spójności. Żądanie odczytu spełnia sla spójności, jeśli wszystkie gwarancje spójności skojarzone z poziomem spójności są spełnione. Dokładne definicje pięciu poziomów spójności w usłudze Azure Cosmos DB przy użyciu języka specyfikacji TLA+ są dostarczane w repozytorium [GitHub azure-cosmos-tla.](https://github.com/Azure/azure-cosmos-tla)

Semantyka pięciu poziomów spójności są opisane w tym miejscu:

- **Mocny:** Silna konsystencja gwarantuje linearizability. Linearizability odnosi się do obsługi żądań jednocześnie. Odczyty są gwarantowane do zwrócenia najnowszej zatwierdzonej wersji elementu. Klient nigdy nie widzi niezatwierdzonych lub częściowego zapisu. Użytkownicy są zawsze gwarantowane, aby odczytać najnowsze popełnione zapisu.

  Poniższa grafika ilustruje silną spójność z nutami muzycznymi. Po zapisaniu danych w regionie "Zachodnie stany USA 2" podczas odczytywania danych z innych regionów otrzymujesz najnowszą wartość:

  ![wideo](media/consistency-levels/strong-consistency.gif)

- **Ograniczona nieaktualność:** odczyty są gwarantowane do przestrzegania gwarancji spójnego prefiksu. Odczyty mogą pozostawać w tyle za zapisami przez co najwyżej *"K"* wersje (czyli "aktualizacje") elementu lub *przez "T"* przedział czasu. Innymi słowy, po wybraniu ograniczonego nieaktualności "nieaktualność" można skonfigurować na dwa sposoby:

- Liczba wersji (*K*) pozycji
- Przedział czasu (*T*), w którym odczyty mogą pozostawać w tyle za zapisami

Ograniczona nieaktualność oferuje całkowite zamówienie globalne poza "oknem nieaktualności". Gdy klient wykonuje operacje odczytu w regionie, który akceptuje zapisy, gwarancje dostarczone przez spójność przestarzałości są identyczne z tymi gwarancjami przez silną spójność.

Wewnątrz okna nieaktualność ograniczona staleness zapewnia następujące gwarancje spójności:

- Spójność dla klientów w tym samym regionie dla konta jedno-wzorcowego = Silna
- Spójność dla klientów w różnych regionach dla konta jednorodzinnego = spójny prefiks
- Spójność dla klientów zapisujących do jednego regionu dla konta wieloelmeczowego = Spójny prefiks
- Spójność dla klientów zapisu w różnych regionach dla konta wielowierszowego =

  Ograniczona nieaktualność jest często wybierana przez globalnie rozproszone aplikacje, które oczekują niskich opóźnień zapisu, ale wymagają całkowitej gwarancji zamówienia globalnego. Ograniczona nieaktualność jest świetna dla aplikacji zawierających współpracę grupową i udostępnianie, giełdowy, publikowanie subskrybowane / kolejkowanie itp. Poniższa grafika ilustruje spójność ograniczonego nieaktualności z nutami muzycznymi. Po zapisaniu danych w regionie "Zachodnie stany USA 2" regiony "Wschodnie stany USA 2" i "Australia Wschodnia" odczytywały wartość zapisaną na podstawie skonfigurowanego maksymalnego czasu opóźnienia lub maksymalnych operacji:

  ![wideo](media/consistency-levels/bounded-staleness-consistency.gif)

- **Sesja**: W ramach jednej sesji klienta odczyty są gwarantowane do przestrzegania spójnego prefiksu, odczyty monotoniczne, zapisy monotoniczne, odczyty i zapisy i zapis-follows-odczyty gwarancji. Zakłada to pojedynczą sesję "writer" lub udostępnianie tokenu sesji dla wielu autorów.

Klienci spoza sesji wykonywania zapisów zobaczą następujące gwarancje:

- Spójność dla klientów w tym samym regionie dla konta jednorodzinnego = Spójny prefiks
- Spójność dla klientów w różnych regionach dla konta jednorodzinnego = spójny prefiks
- Spójność dla klientów zapisujących do jednego regionu dla konta wieloelmeczowego = Spójny prefiks
- Spójność dla klientów zapisu do wielu regionów dla konta wielowierszowego = Ostateczny

  Spójność sesji jest powszechnie używanym poziomem spójności zarówno dla pojedynczego regionu, jak i aplikacji rozproszonych globalnie. Zapewnia opóźnienia zapisu, dostępność i przepływność odczytu porównywalne do spójności ostatecznej, ale również zapewnia gwarancje spójności, które odpowiadają potrzebom aplikacji napisanych do działania w kontekście użytkownika. Poniższa grafika ilustruje spójność sesji z nutami muzycznymi. "West US 2 writer" i "West US 2 reader" używają tej samej sesji (Sesja A), więc obaj czytają te same dane w tym samym czasie. Podczas gdy region "Australia Wschód" używa "Sesji B", więc odbiera dane później, ale w tej samej kolejności, w jakiej zapisuje.

  ![wideo](media/consistency-levels/session-consistency.gif)

- **Spójny prefiks:** Aktualizacje, które są zwracane zawierają pewne prefiks wszystkich aktualizacji, bez przerw. Spójny poziom spójności prefiksu gwarantuje, że odczyt nigdy nie zobaczy zapisów poza kolejnością.

Jeśli zapisy zostały wykonane w kolejności `A, B, C`, to klient będzie widział zapis `A`, `A,B` lub `A,B,C`, ale nigdy w innej kolejności, takiej jak `A,C` lub `B,A,C`. Spójny prefiks zapewnia opóźnienia zapisu, dostępność i przepływność odczytu porównywalne do spójności ostatecznej, ale także zapewnia gwarancje zamówienia, które odpowiadają potrzebom scenariuszy, w których kolejność jest ważna. 

Poniżej znajdują się gwarancje spójności dla spójnego prefiksu:

- Spójność dla klientów w tym samym regionie dla konta jednorodzinnego = Spójny prefiks
- Spójność dla klientów w różnych regionach dla konta jednorodzinnego = spójny prefiks
- Spójność dla klientów zapisujących do jednego regionu dla konta wieloelmeczowego = Spójny prefiks
- Spójność dla klientów zapisu do wielu regionów dla konta wielowierszowego = Ostateczny

Poniższa grafika ilustruje spójność prefiksu spójności z nutami muzycznymi. We wszystkich regionach odczyty nigdy nie widzą zapisów poza kolejnością:

  ![wideo](media/consistency-levels/consistent-prefix.gif)

- **Ostateczny:** Nie ma gwarancji zamawiania dla odczytów. W przypadku braku dalszych zapisów repliki ostatecznie zbiegają się.  
Spójność ostateczna jest najsłabszą formą spójności, ponieważ klient może odczytać wartości, które są starsze niż te, które czytał wcześniej. Spójność ostateczna jest idealnym rozwiązaniem, gdy aplikacja nie wymaga żadnych gwarancji zamawiania. Przykłady obejmują liczbę retweetów, polubień lub komentarzy niewątniczych. Poniższa grafika przedstawia ostateczną spójność z nutami muzycznymi.

  ![wideo](media/consistency-levels/eventual-consistency.gif)

## <a name="additional-reading"></a>Dodatkowa lektura

Aby dowiedzieć się więcej o pojęciach dotyczących spójności, przeczytaj następujące artykuły:

- [Specyfikacje TLA+ wysokiego poziomu dla pięciu poziomów spójności oferowanych przez usługę Azure Cosmos DB](https://github.com/Azure/azure-cosmos-tla)
- [Powtórzona spójność danych wyjaśniona przez Baseball (wideo) przez Douga Terry'ego](https://www.youtube.com/watch?v=gluIh8zd26I)
- [Powtórzona spójność danych wyjaśniona przez Baseball (oficjalny dokument) przez Douga Terry'ego](https://www.microsoft.com/research/publication/replicated-data-consistency-explained-through-baseball/)
- [Gwarancje sesji dla słabo spójnych replikowanych danych](https://dl.acm.org/citation.cfm?id=383631)
- [Kompromisy spójności w nowoczesnym projektowaniu systemów rozproszonych baz danych: WPR to tylko część historii](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Probabilistic Ograniczone Staleness (PBS) dla praktycznych częściowych kworów](https://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
- [Ostatecznie spójne - Revisited](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o poziomach spójności w usłudze Azure Cosmos DB, przeczytaj następujące artykuły:

- [Wybierz odpowiedni poziom spójności dla swojej aplikacji](consistency-levels-choosing.md)
- [Poziomy spójności w interfejsach API bazy danych usługi Azure Cosmos](consistency-levels-across-apis.md)
- [Kompromisy w zakresie dostępności i wydajności dla różnych poziomów spójności](consistency-levels-tradeoffs.md)
- [Konfigurowanie domyślnego poziomu spójności](how-to-manage-consistency.md#configure-the-default-consistency-level)
- [Zastępowanie domyślnego poziomu spójności](how-to-manage-consistency.md#override-the-default-consistency-level)
