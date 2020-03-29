---
title: Limity usługi Azure Cosmos DB Gremlin
description: Dokumentacja referencyjna dotycząca ograniczeń czasu wykonywania silnika Graph
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 10/04/2019
ms.author: lbosq
ms.openlocfilehash: 76ad787990c355d29613c05ca9fce31885a2eccc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72029851"
---
# <a name="azure-cosmos-db-gremlin-limits"></a>Limity środowiska Gremlin w usłudze Azure Cosmos DB
W tym artykule o mówi o ograniczeniach aparatu Azure Cosmos DB Gremlin i wyjaśniono, jak mogą one wpływać na przechodzenie klientów.

Cosmos DB Gremlin jest zbudowany na infrastrukturze Cosmos DB. W związku z tym nadal obowiązują wszystkie limity wyjaśnione w [limitach usługi usługi Usługi Azure Cosmos DB.](https://docs.microsoft.com/azure/cosmos-db/concepts-limits) 

## <a name="limits"></a>Limity

Po osiągnięciu limitu Gremlin przechodzenie jest anulowane z **x-ms-status-code** 429 wskazujący błąd ograniczania przepustowości. Aby uzyskać więcej [informacji, zobacz Nagłówki odpowiedzi serwera Gremlin.](gremlin-limits.md)

**Zasobów**    | **Limit domyślny** | **Wyjaśnienie**
--- | --- | ---
*Długość skryptu* | **64 KB** | Maksymalna długość skrypt przechodzenia języka Gremlin na żądanie.
*Głębokość operatora* | **400** |  Całkowita liczba unikatowych kroków przechodzenia. Na przykład ```g.V().out()``` ma liczbę operatorów 2: V() ```g.V('label').repeat(out()).times(100)``` i out(), ma głębokość operatora 3: V(), repeat(), i out(), ponieważ ```.times(100)``` jest parametrem do ```.repeat()``` operatora.
*Stopień równoległości* | **32** | Maksymalna liczba partycji magazynu, dla których wykonano zapytania w jednym żądaniu skierowanym do warstwy magazynu. Ten limit będzie miał wpływ na wykresy z setkami partycji.
*Powtórz limit* | **32** | Maksymalna liczba iteracji, które może wykonywać operator ```.repeat()```. Każda iteracja ```.repeat()``` kroku w większości przypadków uruchamia przechodzenie pierwszego szerokości, co oznacza, że każdy przechodzenie jest ograniczona do co najwyżej 32 przeskoków między wierzchołkami.
*Limit czasu przechodzenia* | **30 sekund** | Przechodzenie zostanie anulowane, gdy przekroczy ten czas. Graf usługi Cosmos DB to baza danych OLTP z większością przejść ukończonych w ciągu milisekund. Aby uruchomić kwerendy OLAP na grafie bazy danych usługi Cosmos, użyj [aplikacji Apache Spark](https://azure.microsoft.com/services/cosmos-db/) z [ramkami danych wykresu](https://spark.apache.org/docs/latest/sql-programming-guide.html#datasets-and-dataframes) i [łącznika platformy Spark usługi Cosmos DB](https://github.com/Azure/azure-cosmosdb-spark).
*Limit czasu bezczynnego połączenia* | **1 godzina** | Czas, przez który usługa Gremlin będzie utrzymywać bezczynne połączenia websocket otwarte. Pakiety TCP keep-alive lub żądania http keep-alive nie wydłużają okresu obowiązywania połączenia poza ten limit. Aparat programu Cosmos DB Graph uważa połączenia websocket za bezczynne, jeśli nie ma żadnych aktywnych żądań Gremlin uruchomiony na nim.
*Tokeny zasobów na godzinę* | **100** | Liczba unikatowych tokenów zasobów używanych przez klientów języka Gremlin do nawiązywania połączenia z kontem Gremlin w regionie. Gdy aplikacja przekracza co godzinę unikatowy limit tokenu, `"Exceeded allowed resource token limit of 100 that can be used concurrently"` zostaną zwrócone przy następnym żądaniu uwierzytelniania.

## <a name="next-steps"></a>Następne kroki
* [Nagłówki odpowiedzi usługi Azure Cosmos DB Gremlin](gremlin-headers.md) 
* [Tokeny zasobów usługi Azure Cosmos DB z gremlinem](how-to-use-resource-tokens-gremlin.md)
