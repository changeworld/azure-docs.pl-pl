---
title: Wybieranie poziomu spójności odpowiednie dla twojej aplikacji, która korzysta z usługi Azure Cosmos DB
description: Wybieranie poziomu spójności odpowiednie dla twojej aplikacji w usłudze Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/24/2018
ms.reviewer: sngun
ms.openlocfilehash: f32434e5ac0cd35cf620c1589aeb441476622442
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60892203"
---
# <a name="choose-the-right-consistency-level"></a>Wybieranie właściwego poziomu spójności 

Rozproszonych baz danych, opierając się na replikację, wysoką dostępność, małych opóźnień, czy oba rodzaje, wprowadzić podstawowe zależnościami między spójności odczytu, a dostępność, opóźnienia i przepływności. Większość komercyjnego rozproszonych baz danych, poproś deweloperów dokonać wyboru między dwoma modelami spójności extreme: *silne* spójności i *ostatecznej* spójności. Usługa Azure Cosmos DB umożliwia deweloperom wybranie jednego z pięciu dobrze zdefiniowanych modeli spójności: *silne*, *powiązana nieaktualność*, *sesji*, *spójne prefiks* i *ostatecznej*. Każda z tych modeli spójności jest dobrze zdefiniowany i intuicyjny i może służyć do określonych scenariuszy w rzeczywistych warunkach. Każdy z pięcioma modelami spójności zapewnić dokładne [wpływ na dostępność i wydajność](consistency-levels-tradeoffs.md) i jest wspierana przez kompleksowe umowy SLA. Następujące kwestie proste ułatwi właściwym wyborem w wielu typowych scenariuszy.

## <a name="sql-api-and-table-api"></a>SQL, interfejsu API i interfejs API tabel

Jeśli Twoja aplikacja została skompilowana z interfejsu API SQL lub interfejsu API tabel, należy wziąć pod uwagę następujące kwestie:

- Dla wielu scenariuszy w rzeczywistych warunkach spójność sesji to optymalne i jest to zalecana opcja. Aby uzyskać więcej informacji znajduje się pozycja [instrukcje Zarządzanie tokenu sesji aplikacji](how-to-manage-consistency.md#utilize-session-tokens).

- Jeśli aplikacja wymaga silnej spójności, zaleca się, że używasz poziom spójności powiązana nieaktualność.

- Jeśli potrzebujesz bardziej rygorystyczne spójności gwarantuje niż te pod warunkiem spójność sesji i pojedynczych milisekund opóźnienia zapisu, zalecane jest, że używasz powiązana nieaktualność poziomu spójności.  

- Jeśli aplikacja wymaga spójności ostatecznej, zaleca się, że używasz poziomu spójności spójny prefiks.

- Gwarancje ścisłej spójności, mniej niż te dostarczone przez spójność sesji, należy zaleca się, że używasz poziomu spójności spójny prefiks.

- Jeśli potrzebujesz najwyższej dostępności i najniższym opóźnieniu, użyj poziomu spójności ostatecznej.

- Jeśli potrzebujesz jeszcze większą trwałość danych bez obniżania oczekiwanego poziomu wydajności, można utworzyć poziomu spójności niestandardowych w warstwie aplikacji. Aby uzyskać więcej informacji, zobacz [instrukcje Implementowanie niestandardowych synchronizacji w swoich aplikacjach](how-to-custom-synchronization.md).

## <a name="cassandra-mongodb-and-gremlin-apis"></a>Bazy danych Cassandra, MongoDB i interfejsów API rozwiązania Gremlin

- "Poziom spójności odczytu" oferowane poziomy spójności bazy danych Apache Cassandra i Cosmos DB można znaleźć szczegółowe informacje na temat mapowania między [poziomy spójności i Cosmos DB API](consistency-levels-across-apis.md#cassandra-mapping).

- Aby uzyskać szczegółowe informacje dotyczące mapowania między "Odczytu kwestią" poziomów spójności bazy danych MongoDB i usługi Azure Cosmos DB, zobacz [poziomy spójności i Cosmos DB API](consistency-levels-across-apis.md#mongo-mapping).

## <a name="consistency-guarantees-in-practice"></a>Gwarancje spójności w praktyce

W praktyce może być często uzyskać lepsze gwarancje spójności. Gwarancje spójności dla operacji odczytu odpowiadają aktualność i szeregowania stanu bazy danych, który w przypadku żądania. Spójność odczytu jest powiązany do porządkowania i propagację operacje zapisu/aktualizacji.  

* Gdy ma wartość poziomu spójności **powiązana nieaktualność**, Cosmos DB gwarantuje, czy klienci zawsze odczytać wartość poprzedniego zapisu z to opóźnienie ograniczany przez okno nieaktualność.

* Gdy ma wartość poziomu spójności **silne**okno nieaktualność jest odpowiednikiem zero i klienci są gwarantowane odczytać wartości najnowsza wersja zatwierdzić operacji zapisywania.

* Dla pozostałych poziomów spójności trzy okno nieaktualność jest dużym stopniu zależy od obciążenia. Na przykład, jeśli żadne operacje zapisu w bazie danych, operacji odczytu z **ostatecznej**, **sesji**, lub **spójny prefiks** poziomów spójności jest prawdopodobne te same wyniki operacji odczytu z poziomem silnej spójności.

Jeśli Twoje konto usługi Azure Cosmos jest skonfigurowany z poziomem spójności niż wysoki poziom spójności, można znaleźć prawdopodobieństwo, że klienci mogą otrzymać silnych i spójnych odczytów dla obciążeń, analizując *Probabilistically Powiązana nieaktualność* metryki (PBS). Ta metryka jest widoczna w witrynie Azure portal, aby dowiedzieć się więcej, zobacz [metryki Monitor Probabilistically powiązana nieaktualność książek Telefonicznych](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric).

Probabilistyczne powiązana nieaktualność pokazuje, jak ostateczną spójność ostateczną. Ta metryka zapewnia wgląd w jak często można uzyskać spójności silniejsza niż poziom spójności, który obecnie został skonfigurowany na Twoim koncie usługi Azure Cosmos. Innymi słowy można wyświetlić prawdopodobieństwo (mierzonego w milisekundach) uzyskiwanie zdecydowanie spójnych odczytów dla kombinacji zapisu i regionów odczytu.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat poziomów spójności w następujących artykułach:

* [Mapowanie na poziomie spójności między Cosmos DB z interfejsów API](consistency-levels-across-apis.md)
* [Dostępność i wydajność kompromisy dla różnych poziomów spójności](consistency-levels-tradeoffs.md)
* [Jak zarządzać tokenu sesji aplikacji](how-to-manage-consistency.md#utilize-session-tokens)
* [Monitoruj metryki Probabilistically powiązana nieaktualność książek Telefonicznych](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)
