---
title: Modelowanie danych wykresu dla interfejsu API Gremlin usługi Azure Cosmos DB
description: Dowiedz się, jak modelować bazę danych wykresów przy użyciu interfejsu API Gremlin usługi Azure Cosmos DB. W tym artykule opisano, kiedy używać bazy danych wykresu i najlepszych rozwiązań do modelowania jednostek i relacji.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: lbosq
ms.openlocfilehash: dc9a5616aa2bb1f7e09045b9cfe4f4d7e9c69be2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898326"
---
# <a name="graph-data-modeling-for-azure-cosmos-db-gremlin-api"></a>Modelowanie danych wykresu dla interfejsu API Gremlin usługi Azure Cosmos DB

Poniższy dokument ma na celu dostarczenie zaleceń dotyczących modelowania danych wykresu. Ten krok ma kluczowe znaczenie dla zapewnienia skalowalności i wydajności systemu baz danych wykresu w miarę rozwoju danych. Wydajny model danych jest szczególnie ważny w przypadku wykresów na dużą skalę.

## <a name="requirements"></a>Wymagania

Proces opisany w niniejszym przewodniku opiera się na następujących założeniach:
 * **Jednostki** w przestrzeni problemowej są identyfikowane. Te jednostki są przeznaczone do zużywania _niepodzielnie_ dla każdego żądania. Innymi słowy system bazy danych nie jest przeznaczony do pobierania danych pojedynczej jednostki w wielu żądaniach kwerend.
 * Istnieje zrozumienie **wymagań odczytu i zapisu** dla systemu bazy danych. Te wymagania będą kierować optymalizacje potrzebne dla modelu danych wykresu.
 * Zasady [standardu wykresu właściwości Apache Tinkerpop](https://tinkerpop.apache.org/docs/current/reference/#graph-computing) są dobrze rozumiane.

## <a name="when-do-i-need-a-graph-database"></a>Kiedy potrzebuję bazy danych wykresów?

Rozwiązanie bazy danych wykresu można optymalnie zastosować, jeśli jednostki i relacje w domenie danych mają dowolną z następujących cech: 

* Jednostki są **silnie połączone** za pomocą relacji opisowych. Korzyści w tym scenariuszu jest fakt, że relacje są zachowywane w magazynie.
* Istnieją **relacje cykliczne** lub **jednostki, do których istnieją odwołania.** Ten wzorzec jest często wyzwaniem podczas korzystania z relacyjnych lub dokumentów baz danych.
* Istnieją **dynamicznie ewoluujące relacje między jednostkami.** Ten wzorzec ma szczególne zastosowanie do danych hierarchicznych lub strukturywach drzewa o wielu poziomach.
* Istnieją relacje wiele do wielu między **jednostkami.**
* Istnieją **wymagania dotyczące zapisu i odczytu zarówno dla jednostek, jak i relacji**. 

Jeśli powyższe kryteria są spełnione, jest prawdopodobne, że podejście bazy danych wykresu zapewni korzyści dla **złożoności zapytania,** **skalowalności modelu danych**i **wydajności kwerendy.**

Następnym krokiem jest ustalenie, czy wykres będzie używany do celów analitycznych lub transakcyjnych. Jeśli wykres jest przeznaczony do użycia w przypadku dużych obciążeń obliczeniowych i przetwarzania danych, warto zapoznać się ze [łącznikiem Platformy Spark usługi Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/spark-connector) i wykorzystaniem [biblioteki GraphX.](https://spark.apache.org/graphx/) 

## <a name="how-to-use-graph-objects"></a>Jak używać obiektów wykresu

[Standard wykresu właściwości Apache Tinkerpop](https://tinkerpop.apache.org/docs/current/reference/#graph-computing) definiuje dwa typy obiektów **Wierzchołki** i **krawędzie**. 

Poniżej przedstawiono najlepsze rozwiązania dotyczące właściwości w obiektach wykresu:

| Obiekt | Właściwość | Typ | Uwagi |
| --- | --- | --- |  --- |
| Wierzchołek | ID | Ciąg | Unikatowo wymuszane na partycji. Jeśli wartość nie zostanie podana po wstawieniu, zostanie zapisany automatycznie wygenerowany identyfikator GUID. |
| Wierzchołek | label | Ciąg | Ta właściwość jest używana do definiowania typu jednostki, która reprezentuje wierzchołek. Jeśli wartość nie zostanie dostarczona, zostanie użyta wartość domyślna "wierzchołek". |
| Wierzchołek | properties | Ciąg, logiczny, numeryczny | Lista oddzielnych właściwości przechowywanych jako pary klucz-wartość w każdym wierzchołku. |
| Wierzchołek | klucz partycji | Ciąg, logiczny, numeryczny | Ta właściwość definiuje, gdzie będą przechowywane wierzchołek i jego krawędzie wychodzące. Dowiedz się więcej o [partycjonowaniu wykresów](graph-partitioning.md). |
| Brzeg | ID | Ciąg | Unikatowo wymuszane na partycji. Domyślnie generowany automatycznie. Krawędzie zwykle nie muszą być jednoznacznie pobierane przez identyfikator. |
| Brzeg | label | Ciąg | Ta właściwość jest używana do definiowania typu relacji, które mają dwa wierzchołki. |
| Brzeg | properties | Ciąg, logiczny, numeryczny | Lista oddzielnych właściwości przechowywanych jako pary klucz-wartość w każdej krawędzi. |

> [!NOTE]
> Krawędzie nie wymagają wartości klucza partycji, ponieważ jego wartość jest automatycznie przypisywana na podstawie ich wierzchołka źródłowego. Dowiedz się więcej w artykule [partycjonowania wykresu.](graph-partitioning.md)

## <a name="entity-and-relationship-modeling-guidelines"></a>Wytyczne dotyczące modelowania jednostek i relacji

Poniżej przedstawiono zestaw wskazówek dotyczących modelowania danych dla bazy danych wykresu interfejsu API usługi Azure Cosmos DB Gremlin. Te wytyczne zakładają, że istnieje istniejąca definicja domeny danych i kwerendy dla niego.

> [!NOTE]
> Kroki opisane poniżej przedstawiono jako zalecenia. Ostateczny model powinien zostać oceniony i przetestowany przed jego uwzględnieniem jako gotowy do produkcji. Ponadto poniższe zalecenia są specyficzne dla implementacji interfejsu API gremlin usługi Azure Cosmos DB. 

### <a name="modeling-vertices-and-properties"></a>Wierzchołki i właściwości modelowania 

Pierwszym krokiem dla modelu danych wykresu jest mapowanie każdego zidentyfikowanego elementu na **obiekt wierzchołka**. Od jednego do jednego mapowanie wszystkich jednostek do wierzchołków powinien być krokiem początkowym i może ulec zmianie.

Jedną z typowych pułapek jest mapowanie właściwości pojedynczej jednostki jako oddzielnych wierzchołków. Rozważmy poniższy przykład, gdzie ta sama jednostka jest reprezentowana na dwa różne sposoby:

* **Właściwości oparte na wierzchołku**: W tym podejściu jednostka używa trzech oddzielnych wierzchołków i dwóch krawędzi do opisania jego właściwości. Takie podejście może zmniejszyć nadmiarowość, ale zwiększa złożoność modelu. Zwiększenie złożoności modelu może spowodować dodatkowe opóźnienie, złożoność zapytania i koszt obliczeń. Ten model może również stanowić wyzwania w partycjonowaniu.

![Model jednostki z wierzchołkami właściwości.](./media/graph-modeling/graph-modeling-1.png)

* **Wierzchołki osadzone właściwości:** To podejście korzysta z listy pary klucz-wartość do reprezentowania wszystkich właściwości jednostki wewnątrz wierzchołka. Takie podejście zapewnia zmniejszoną złożoność modelu, co doprowadzi do prostszych zapytań i bardziej oszczędnych procesów przechodzenia.

![Model jednostki z wierzchołkami właściwości.](./media/graph-modeling/graph-modeling-2.png)

> [!NOTE]
> Powyższe przykłady pokazują uproszczony model wykresu, aby pokazać tylko porównanie między dwoma sposobami podziału właściwości jednostki.

Wzorzec **wierzchołków osadzonych właściwości** zazwyczaj zapewnia bardziej wydajne i skalowalne podejście. Domyślne podejście do nowego modelu danych wykresu powinny ciążą na tym wzorcu.

Istnieją jednak scenariusze, w których odwoływanie się do właściwości może zapewnić korzyści. Na przykład: jeśli właściwość, do którego istnieje odwołanie, jest często aktualizowana. Za pomocą oddzielnego wierzchołka do reprezentowania właściwości, która jest stale zmieniana zminimalizuje ilość operacji zapisu, które wymagałoby aktualizacji.

### <a name="relationship-modeling-with-edge-directions"></a>Modelowanie relacji z kierunkami brzegowymi

Po modelowanie wierzchołków krawędzie mogą być dodawane w celu oznaczenia relacji między nimi. Pierwszym aspektem, który należy ocenić, jest **kierunek relacji.** 

Obiekty krawędzi mają domyślny kierunek, po którym `out()` następuje przechodzenie podczas korzystania z funkcji lub. `outE()` Użycie tego naturalnego kierunku powoduje wydajną operację, ponieważ wszystkie wierzchołki są przechowywane z ich wychodzącymi krawędziami. 

Jednak przechodzenie w przeciwnym kierunku krawędzi, `in()` przy użyciu funkcji, zawsze spowoduje kwerendy między partycjami. Dowiedz się więcej o [partycjonowaniu wykresów](graph-partitioning.md). Jeśli istnieje potrzeba ciągłego przechodzenia `in()` przez funkcję, zaleca się dodawanie krawędzi w obu kierunkach.

Kierunek krawędzi można określić `.to()` za `.from()` pomocą lub `.addE()` predykatów do kroku Gremlin. Lub za pomocą [biblioteki zbiorczej executor dla Gremlin API](bulk-executor-graph-dotnet.md).

> [!NOTE]
> Obiekty krawędziowe mają kierunek domyślnie.

### <a name="relationship-labeling"></a>Etykietowanie relacji

Używanie opisowych etykiet relacji może poprawić wydajność operacji rozpoznawania krawędzi. Ten wzorzec można zastosować w następujący sposób:
* Użyj terminów nierodzycznych, aby oznaczyć relację.
* Skojarzyć etykietę wierzchołka źródłowego z etykietą wierzchołka docelowego z nazwą relacji.

![Przykłady etykietowania relacji.](./media/graph-modeling/graph-modeling-3.png)

Im bardziej szczegółowa etykieta, że trawers będzie używany do filtrowania krawędzi, tym lepiej. Ta decyzja może mieć znaczący wpływ na koszt kwerendy, jak również. Koszt kwerendy można ocenić w dowolnym [momencie przy użyciu kroku executionProfile](graph-execution-profile.md).


## <a name="next-steps"></a>Następne kroki: 
* Sprawdź listę obsługiwanych [kroków Gremlin](gremlin-support.md).
* Dowiedz się więcej o [partycjonowaniu bazy danych wykresów](graph-partitioning.md) w celu radzenia sobie z wykresami na dużą skalę.
* Oceń swoje zapytania Gremlin za pomocą [kroku Profil wykonania](graph-execution-profile.md).
