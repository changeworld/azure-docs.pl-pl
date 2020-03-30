---
title: Wybierz odpowiedni poziom spójności dla aplikacji Usługi Azure Cosmos DB
description: Wybieranie odpowiedniego poziomu spójności dla aplikacji w usłudze Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.openlocfilehash: cea157e272a2bf464141e592b8e742f314a83233
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75441918"
---
# <a name="choose-the-right-consistency-level"></a>Wybieranie właściwego poziomu spójności 

Rozproszone bazy danych korzystające z replikacji dla wysokiej dostępności, małych opóźnień lub obu, dokonać podstawowego kompromisu między spójności odczytu a dostępności, opóźnienia i przepływności. Większość dostępnych na rynku rozproszonych baz danych prosi deweloperów o wybór między dwoma modelami skrajnej spójności: *silną* spójnością i *spójnością ostateczną.* Usługa Azure Cosmos DB umożliwia deweloperom wybór spośród pięciu dobrze zdefiniowanych modeli spójności: *silny,* *ograniczony nieaktualność,* *sesja,* *spójny prefiks* i *ewentualne.* Każdy z tych modeli spójności jest dobrze zdefiniowany, intuicyjny i może być używany w określonych scenariuszach rzeczywistych. Każdy z pięciu modeli spójności zapewnia precyzyjną [dostępność i wydajność kompromisów](consistency-levels-tradeoffs.md) i są wspierane przez kompleksowe umowy SLA. Następujące proste zagadnienia pomogą Ci dokonać właściwego wyboru w wielu typowych scenariuszach.

## <a name="sql-api-and-table-api"></a>Interfejs API i interfejsu API tabeli SQL

Należy wziąć pod uwagę następujące punkty, jeśli aplikacja jest zbudowana przy użyciu interfejsu API SQL lub interfejsu API tabeli:

- W wielu rzeczywistych scenariuszach spójność sesji jest optymalna i jest to zalecana opcja. Aby uzyskać więcej informacji, zobacz [Jak zarządzać tokenem sesji dla aplikacji](how-to-manage-consistency.md#utilize-session-tokens).

- Jeśli aplikacja wymaga silnej spójności, zaleca się użycie ograniczonego poziomu spójności nieaktualność.

- Jeśli potrzebujesz bardziej rygorystyczne gwarancje spójności niż te przewidziane przez spójność sesji i jednocyfrowe opóźnienie milisekund dla zapisów, zaleca się użycie ograniczonego poziomu spójności nieaktualność.  

- Jeśli aplikacja wymaga spójności ostatecznej, zaleca się użycie spójnego poziomu spójności prefiksu.

- Jeśli potrzebujesz mniej ścisłych gwarancji spójności niż te przewidziane przez spójność sesji, zaleca się użycie spójnego poziomu spójności prefiksu.

- Jeśli potrzebujesz najwyższej dostępności i najniższego opóźnienia, użyj poziomu spójności ostatecznej.

- Jeśli potrzebujesz jeszcze większej trwałości danych bez utraty wydajności, możesz utworzyć niestandardowy poziom spójności w warstwie aplikacji. Aby uzyskać więcej informacji, zobacz [Jak zaimplementować niestandardową synchronizację w aplikacjach](how-to-custom-synchronization.md).

## <a name="cassandra-mongodb-and-gremlin-apis"></a>Interfejsy API Cassandra, MongoDB i Gremlin

- Aby uzyskać szczegółowe informacje na temat mapowania między "Poziomem spójności odczytu" oferowanym w poziomach spójności Apache Cassandra i Cosmos DB, zobacz [Poziomy spójności i interfejsy API bazy danych usługi Cosmos](consistency-levels-across-apis.md#cassandra-mapping).

- Aby uzyskać szczegółowe informacje na temat mapowania między "Read Concern" poziomów spójności MongoDB i Azure Cosmos DB, zobacz [poziomy spójności i interfejsy API usługi Cosmos DB.](consistency-levels-across-apis.md#mongo-mapping)

## <a name="consistency-guarantees-in-practice"></a>Gwarancje spójności w praktyce

W praktyce często można uzyskać silniejsze gwarancje spójności. Gwarancje spójności dla operacji odczytu odpowiadają świeżości i kolejności stanu bazy danych, który żądasz. Spójność odczytu jest powiązana z kolejnością i propagacją operacji zapisu/aktualizacji.  

* Gdy poziom spójności jest ustawiona na **ograniczoną nieaktualność,** usługa Cosmos DB gwarantuje, że klienci zawsze odczytują wartość poprzedniego zapisu, z opóźnieniem ograniczonym przez okno nieaktualność.

* Gdy poziom spójności jest ustawiona na **silna,** okno nieaktualność jest równoważne zero, a klienci są gwarantowane do odczytu ostatniej zatwierdzonej wartości operacji zapisu.

* W przypadku pozostałych trzech poziomów spójności okno nieaktualność jest w dużej mierze zależne od obciążenia. Na przykład jeśli w bazie danych nie ma żadnych operacji zapisu, operacja odczytu z **ewentualnymi**, **sesjami**lub spójnymi poziomami spójności **prefiksów** może przynieść takie same wyniki jak operacja odczytu z silnym poziomem spójności.

Jeśli twoje konto usługi Azure Cosmos jest skonfigurowany z poziomem spójności innych niż silne spójności, można dowiedzieć się prawdopodobieństwo, że klienci mogą uzyskać silne i spójne odczyty dla obciążeń, patrząc na *probabilisically ograniczone staleness* (PBS) metryki. Ta metryka jest widoczna w witrynie Azure portal, aby dowiedzieć się więcej, zobacz [Monitor probabilistycznie ograniczone staleść (PBS) metryki.](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)

Probabilistic ograniczone nieaktualność pokazuje, jak ostateczne jest ostateczna spójność. Ta metryka zapewnia wgląd w to, jak często można uzyskać większą spójność niż poziom spójności, który został obecnie skonfigurowany na koncie usługi Azure Cosmos. Innymi słowy można zobaczyć prawdopodobieństwo (mierzone w milisekundach) uzyskania silnie spójne odczyty dla kombinacji regionów zapisu i odczytu.

## <a name="next-steps"></a>Następne kroki

Przeczytaj więcej o poziomach spójności w następujących artykułach:

* [Mapowanie poziomu spójności między interfejsami API bazy danych usługi Cosmos](consistency-levels-across-apis.md)
* [Kompromisy w zakresie dostępności i wydajności dla różnych poziomów spójności](consistency-levels-tradeoffs.md)
* [Jak zarządzać tokenem sesji dla aplikacji](how-to-manage-consistency.md#utilize-session-tokens)
* [Monitorowanie metryki PBS (Probabilistically Bounded Staleness)](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)
