---
title: Wybieranie poziomu spójności odpowiednie dla aplikacji | Dokumentacja firmy Microsoft
description: Wybieranie poziomu spójności odpowiednie dla twojej aplikacji w usłudze Azure Cosmos DB.
keywords: spójność, wydajność, usługi azure cosmos db, azure, Microsoft azure
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: mjbrown
ms.openlocfilehash: 4b438320ac75918d10880a4bcdccadca4eebec32
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2018
ms.locfileid: "50244185"
---
# <a name="choose-the-right-consistency-level-for-your-application"></a>Wybierz poziom spójności odpowiednie dla twojej aplikacji

Rozproszonych baz danych, opierając się na replikację, wysoką dostępność, małych opóźnień, czy oba rodzaje, wprowadzić podstawowe zależnościami między spójności odczytu, a dostępność, opóźnienia i przepływności. Większość komercyjnego rozproszonych baz danych, poproś deweloperów dokonać wyboru między dwoma modelami spójności extreme: wysoki poziom spójności i spójności ostatecznej. Usługa Azure Cosmos DB umożliwia deweloperom pięć dokładnie zdefiniowanych modeli spójności do wyboru: silne, powiązana nieaktualność, sesja, spójny prefiks i "eventual". Każda z tych modeli spójności jest dobrze zdefiniowany i intuicyjny i może służyć do określonych scenariuszy w rzeczywistych warunkach. Każda z pięcioma modelami spójności zapewnia wyczyść wpływ na dostępność i wydajność i jest wspierana przez kompleksowe umowy SLA. Następujące kwestie proste ułatwi właściwym wyborem w wielu typowych scenariuszy.

## <a name="sql-api-or-table-api"></a>Interfejs API SQL lub interfejsu API tabel

- Dla wielu scenariuszy w rzeczywistych warunkach spójność sesji to optymalne i jest to zalecana opcja. Aby uzyskać więcej informacji, zobacz [instrukcje Zarządzanie tokenu sesji aplikacji](how-to-manage-consistency.md#utilize-session-tokens).
- Jeśli aplikacja wymaga silnej spójności, zaleca się, że używasz poziom spójności powiązana nieaktualność.
- Jeśli potrzebujesz bardziej rygorystyczne gwarancje spójności niż te dostarczone przez spójność sesji i opóźnienia pojedynczych milisekund do zapisu, zaleca się, że używasz poziom spójności powiązana nieaktualność.  
- Jeśli aplikacja wymaga spójności ostatecznej, zaleca się, że używasz spójny prefiks poziomu spójności.
- Gwarancje ścisłej spójności, mniej niż dostarczanych przez spójność sesji, należy zaleca się, że używasz spójny prefiks poziomu spójności.
- Jeśli potrzebujesz najwyższej dostępności i najniższym opóźnieniu, użyj poziomu spójności ostatecznej.

## <a name="cassandra-mongodb-or-gremlin-api"></a>Interfejs API rozwiązania Cassandra, MongoDB lub języka Gremlin

- Aby uzyskać szczegółowe informacje dotyczące mapowania między "Poziom spójności odczytu" poziomów spójności bazy danych Apache Cassandra i Cosmos DB, zobacz [poziomy spójności i Cosmos DB API](consistency-levels-across-apis.md#cassandra-mapping).
- Aby uzyskać szczegółowe informacje dotyczące mapowania między "Odczytu kwestią" poziomów spójności bazy danych MongoDB i usługi Azure Cosmos DB, zobacz [poziomy spójności i Cosmos DB API](consistency-levels-across-apis.md#mongo-mapping).

## <a name="you-may-get-stronger-consistency-guarantees-in-practice"></a>Lepsze gwarancje spójności może wystąpić w praktyce

Gwarancje spójności dla operacji odczytu odpowiadają aktualność i kolejność stan żądanej bazy danych. Spójność odczytu jest powiązany do porządkowania i propagację operacje zapisu (aktualizacja).  

Gdy ma wartość poziomu spójności **powiązana nieaktualność**, Cosmos DB gwarantuje, czy klienci zawsze odczytać wartość poprzedniego zapisu z to opóźnienie ograniczany przez okno nieaktualność.

Gdy ma wartość poziomu spójności **silne**okno nieaktualność jest odpowiednikiem zero i gwarantują klientów można odczytać najnowszą zatwierdzone wartości zapisu.

Dla pozostałych poziomów spójności trzy okno nieaktualność jest dużym stopniu zależy od obciążenia. Na przykład, jeśli nie zapisy są wykonywane w bazie danych, operacji odczytu z **ostatecznej**, **sesji**, lub **spójny prefiks** poziomów spójności jest prawdopodobne te same wyniki operacji odczytu z poziomem silnej spójności.

Jeśli konto usługi Cosmos DB jest skonfigurowany przy użyciu dowolnego poziomu spójności, innych niż wysoki poziom spójności, można znaleźć prawdopodobieństwo, że na komputerach klienckich, Pobierz zdecydowanie spójnych odczytów (linearizable) dla Twojego workload(s), analizując Probabilistyczne ograniczone Metryka nieaktualność (PBS) udostępniane w witrynie Azure portal [opublikujemy w zakresie używania metryki PBS](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric). Probabilistyczne powiązana nieaktualność pokazuje, jak ostateczną spójność ostateczną. Ta metryka zapewnia wgląd w częstotliwości otrzymywania spójności silniejsza niż poziom spójności, który został skonfigurowany na koncie usługi Cosmos DB. Innymi słowy możesz zobaczyć prawdopodobieństwo (opisanej w milisekundach), wprowadzenie zdecydowanie spójnych odczytów dla kombinacji zapisu i odczytu regionów.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat poziomów spójności w następujących artykułach:

* [Mapowanie na poziomie spójności między Cosmos DB z interfejsów API](consistency-levels-across-apis.md)
* [Dostępność i wydajność kompromisy dla różnych poziomów spójności](consistency-levels-tradeoffs.md)
* [Jak zarządzać tokenu sesji aplikacji](how-to-manage-consistency.md#utilize-session-tokens)
* [Jak monitorować metryki Probabilistically powiązana nieaktualność książek Telefonicznych](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)