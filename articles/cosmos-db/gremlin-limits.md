---
title: Limity Azure Cosmos DB Gremlin
description: Dokumentacja referencyjna dotycząca ograniczeń środowiska uruchomieniowego aparatu grafu
author: olignat
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: olignat
ms.openlocfilehash: f1fe4cfac22a651f44338986d0a767fe3e1a360b
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70911087"
---
# <a name="azure-cosmos-db-gremlin-limits"></a>Azure Cosmos DB limity Gremlin
W tym artykule omówiono limity Azure Cosmos DB aparacie Gremlin i wyjaśniono, jak mogą one wpływać na przechodzenie klientów.

Cosmos DB Gremlin jest oparty na infrastrukturze Cosmos DB, dlatego wszystkie ograniczenia w [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/concepts-limits) nadal mają zastosowanie. 

## <a name="limits"></a>Limity

Po osiągnięciu limitu Gremlin przechodzenie zostało anulowane przy użyciu wartości **x-MS-status-Code** = 429 wskazującej na błąd ograniczania.

**Zasób**    | **Limit domyślny** | **Wyjaśnienie**
--- | --- | ---
*Pamięć na żądanie* | **2 GB** | Maksymalna ilość pamięci do użycia przez żądanie podczas przetwarzania. Żądania, które wymagają obliczenia dużych zestawów danych, będą zużywać dodatkową pamięć. Rozważ możliwość określania zakresu żądań do mniejszych zestawów danych, aby uniknąć przekroczenia tego limitu lub korzystania z rozwiązań OLAP.
*Długość skryptu* | **64 KB** | Maksymalna długość skrypt przechodzenia języka Gremlin na żądanie.
*Głębokość operatora* | **400** |  Całkowita liczba unikatowych kroków przechodzenia. Na przykład ```g.V().out()``` ma liczbę operatorów wynoszącą 2: V () i out (), ```g.V('label').repeat(out()).times(100)``` ma głębokość operatora 3: V (), Repeat () i out (), ponieważ ```.times(100)``` jest ```.repeat()``` parametrem operatora.
*Stopień równoległości* | **32** | Maksymalna liczba partycji magazynu, dla których wykonano zapytania w jednym żądaniu skierowanym do warstwy magazynu. Ten limit będzie miał wpływ na wykresy z setkami partycji.
*Limit powtarzania* | **32** | Maksymalna liczba iteracji, które może wykonywać operator ```.repeat()```. Każda iteracja ```.repeat()``` kroku w większości przypadków uruchamia przechodzenie w pierwszej kolejności, co oznacza, że każde przechodzenie jest ograniczone do maksymalnie 32 przeskoków między wierzchołkami.
*Limit czasu przechodzenia* | **30 sekund** | Przechodzenie zostanie anulowane, gdy przekroczy ten czas. Graf usługi Cosmos DB to baza danych OLTP z większością przejść ukończonych w ciągu milisekund. Aby uruchamiać zapytania OLAP na Cosmos DB grafie, należy użyć [Apache Spark](https://azure.microsoft.com/services/cosmos-db/) z [ramkami danych grafów](https://spark.apache.org/docs/latest/sql-programming-guide.html#datasets-and-dataframes) i [łącznikiem Cosmos DB Spark](https://github.com/Azure/azure-cosmosdb-spark).
*Limit predykatu* | **20** | Liczba kroków ```.has()``` lub ```.hasNot()``` stosowanych w pojedynczym wierzchołku lub pojedynczej krawędzi. Po osiągnięciu tego limitu w aplikacji jest wyświetlany błąd ```The SQL query exceeded the maximum number of joins. The allowed limit is 20```. Jest to tymczasowa niedogodności, ponieważ zespół pracuje nad zniesieniem tego ograniczenia. 
*Limit czasu bezczynności połączenia* | **5 godzin** | Czas, przez jaki serwer grafów będzie mieć otwarte połączenie protokołu WebSocket bez ruchu na nim. Pakiety Keep-Alive protokołu TCP lub żądania Keep-Alive protokołu HTTP nie zwiększają cykl życia połączeń poza tym limitem, jednak jeśli nie są one wysyłane, podstawowa infrastruktura platformy Azure może zamknąć połączenie jeszcze wcześniej. Aparat grafu Cosmos DB jest uznawany za bezczynny, jeśli nie ma na nim żadnych przechodzenia Gremlin.
*Token zasobu na godzinę* | **100** | Liczba unikatowych tokenów zasobów używanych przez klientów Gremlin do nawiązywania połączenia z kontem Gremlin w regionie. Gdy aplikacja przekroczy limit czasu unikatowych `"Exceeded allowed resource token limit of 100 that can be used concurrently"` tokenów, zostanie zwrócona przy następnym żądaniu uwierzytelniania.

## <a name="next-steps"></a>Następne kroki
* [Azure Cosmos DB nagłówki odpowiedzi Gremlin](gremlin-headers.md) 
* [Azure Cosmos DB tokeny zasobów z Gremlin](how-to-use-resource-tokens-gremlin.md)
