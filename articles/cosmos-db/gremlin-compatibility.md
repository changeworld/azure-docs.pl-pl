---
title: Azure Cosmos DB zgodność Gremlin z funkcjami TinkerPop
description: Informacje o problemach ze zgodnością aparatu grafu dokumentacji
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: sngun
ms.openlocfilehash: 0ed5824859b8463919a809861993f9f98a4f9251
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72327034"
---
# <a name="azure-cosmos-db-gremlin-compatibility"></a>Azure Cosmos DB zgodność Gremlin
Aparat grafu Azure Cosmos DB jest ściśle opisany w specyfikacji kroków przechodzenia do programu [Apache TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) , ale istnieją różnice.

## <a name="behavior-differences"></a>Różnice w zachowaniu

* Aparat grafu Azure Cosmos DB uruchamia przechodzenie w ***pierwszej kolejności*** , podczas gdy TinkerPop Gremlin jest głębokością. Takie zachowanie zapewnia lepszą wydajność w skalowalnym systemie, takim jak Cosmos DB. 

## <a name="unsupported-features"></a>Nieobsługiwane funkcje

* ***[Kod bajtowy języka Gremlin](http://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)*** to specyfikacja przechodzenia przez graf niezależna od języka programowania. Program Cosmos DB Graph jeszcze nie obsługuje tego programu. Użyj ```GremlinClient.SubmitAsync()``` i przekaż przechodzenie jako ciąg tekstowy.

* Kardynalność zestawu ***```property(set, 'xyz', 1)```*** nie jest obecnie obsługiwana. Zamiast nich należy używać słów kluczowych ```property(list, 'xyz', 1)```.

* ***```match()```*** umożliwia wykonywanie zapytań na wykresach przy użyciu deklaratywnego dopasowania do wzorca. Ta funkcja jest niedostępna.

* ***Obiekty jako właściwości*** dla wierzchołków lub krawędzi nie są obsługiwane. Właściwości mogą być tylko typami pierwotnymi lub tablicami.

* ***Sortowanie według właściwości tablicy*** ```.order().by(<array property>)``` nie jest obsługiwane. Sortowanie jest obsługiwane tylko według typów pierwotnych.

* ***Niepierwotne typy JSON*** nie są obsługiwane. Użyj ```string```, ```number```lub ```true```/typy ```false```. wartości ```null``` nie są obsługiwane. 

* Serializator ***GraphSONv3*** nie jest obecnie dostępny.

* ***Transakcje*** nie są obsługiwane z powodu rozproszonego charakteru systemu.  Skonfiguruj odpowiedni model spójności na koncie Gremlin na "odczytywanie własnych zapisów" i Użyj optymistycznej współbieżności, aby rozwiązać konflikty zapisów.

## <a name="next-steps"></a>Następne kroki
* Odwiedź stronę [Cosmos DB User Voice](https://feedback.azure.com/forums/263030-azure-cosmos-db) , aby udostępnić Opinie i ułatwić zespołowi skupienie się na funkcjach, które są dla Ciebie ważne.
