---
title: Dane wykresu modelowania dla interfejsu API usługi Azure Cosmos DB języka Gremlin
description: Dowiedz się, jak model bazy danych grafów przy użyciu interfejsu API języka Gremlin usługi Cosmos DB.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 06/24/2019
ms.author: lbosq
ms.openlocfilehash: c6ae23efa90874bbefc2aff35f8798aa6c0da791
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503420"
---
# <a name="graph-data-modeling-for-azure-cosmos-db-gremlin-api"></a>Dane wykresu modelowania dla interfejsu API usługi Azure Cosmos DB języka Gremlin

Ten dokument jest zaprojektowany w celu zapewnienia modelowania zalecenia dotyczące danych wykresu. Ten krok jest ważne w celu zapewnienia skalowalności i wydajności systemu bazy danych programu graph w miarę rozwoju danych. Model danych wydajność jest szczególnie ważna w przypadku dużych wykresów.

## <a name="requirements"></a>Wymagania

Proces opisany w tym przewodniku opiera się na następujące założenia:
 * **Jednostek** są identyfikowane w obszarze problemu. Te jednostki są przeznaczone do użycia _niepodzielne_ dla każdego żądania. Innymi słowy system bazy danych nie jest przeznaczona do pobierania danych z pojedynczej jednostki w wielu żądań zapytań.
 * Brak zrozumienia **Odczyt i zapis wymagania** systemu bazy danych. Te wymagania przeprowadzi optymalizacje służące do modelu danych programu graph.
 * Zasady [przez witrynę Apache Tinkerpop właściwości wykresu standardowego](http://tinkerpop.apache.org/docs/current/reference/#graph-computing) są dobrze zrozumiałe.

## <a name="when-do-i-need-a-graph-database"></a>Jeśli potrzebujesz grafowej bazy danych?

Rozwiązanie bazy danych programu graph można zastosować optymalnie, gdy jednostki i relacje w domenie danych nie ma żadnej z następujących właściwości: 

* Jednostki są **wysoce połączone** relacjami opisowe. W przypadku tego scenariusza jest fakt, że relacje są utrwalane w magazynie.
* Istnieją **relacje cykliczne** lub **własnym odwołanie do jednostki**. Ten wzorzec jest często żądanie przy użyciu relacyjnych lub bazy danych dokumentów.
* Istnieją **dynamicznie ewoluują relacje** między jednostkami. Ten wzorzec jest szczególnie dotyczy hierarchicznej lub strukturze drzewa danych za pomocą wiele poziomów.
* Istnieją **relacji wiele do wielu** między jednostkami.
* Istnieją **zapisu i odczytu wymagania dotyczące jednostek i relacji**. 

Jeśli kryteriów powyżej zostanie spełniony, istnieje duże prawdopodobieństwo, że podejście do baz danych programu graph zapewnia dodatkowych korzyści w przypadku **zapytania złożoności**, **skalowalności modelu danych**, i **wydajnośćzapytań**.

Następnym krokiem jest ustalenie, jeśli wykres ma zostać użyte do celów analizy lub transakcyjnych. Jeśli wykres jest przeznaczony do użycia w przypadku obciążeń przetwarzania danych i duże obliczenia, byłoby warte Eksplorowanie [łącznika usługi Cosmos DB Spark](https://docs.microsoft.com/azure/cosmos-db/spark-connector) i umożliwia korzystanie z [biblioteki GraphX](https://spark.apache.org/graphx/). 

## <a name="how-to-use-graph-objects"></a>Jak używać programu Graph

[Przez witrynę Apache Tinkerpop właściwości wykresu standardowego](http://tinkerpop.apache.org/docs/current/reference/#graph-computing) definiuje dwa typy obiektów **wierzchołki** i **krawędzie**. 

Poniżej przedstawiono najlepsze rozwiązania dotyczące właściwości w obiektach wykresu:

| Object | Właściwość | Typ | Uwagi |
| --- | --- | --- |  --- |
| Vertex | id | String | Jednoznacznie wymuszane dla każdej partycji. Jeśli wartość nie jest podany podczas wstawiania i generowanych automatycznie będzie przechowywany identyfikator GUID. |
| Vertex | label | String | Ta właściwość jest używana do definiowania typu jednostki, która reprezentuje wierzchołek. Jeśli wartość nie jest podany, wartość domyślna "wierzchołka" będzie używany. |
| Vertex | properties | String, Boolean, numeryczne | Lista osobne właściwości przechowywane jako pary klucz wartość w każdego wierzchołka. |
| Vertex | Klucz partycji | String, Boolean, numeryczne | Ta właściwość określa, gdzie będą przechowywane wierzchołka i jego krawędzi wychodzących. Przeczytaj więcej na temat [partycjonowanie grafu](graph-partitioning.md). |
| Brzeg | id | String | Jednoznacznie wymuszane dla każdej partycji. Wygenerowany automatycznie domyślnie. Krawędzie zwykle nie ma konieczności jednoznacznie można pobrać za pomocą tego identyfikatora. |
| Brzeg | label | String | Ta właściwość jest używana do definiowania typu relacji, który ma dwa wierzchołki. |
| Brzeg | properties | String, Boolean, numeryczne | Lista osobne właściwości przechowywane jako pary klucz wartość w każdej przeglądarce edge. |

> [!NOTE]
> Krawędzie nie wymaga wartość klucza partycji, ponieważ jej wartość jest automatycznie przypisywany oparte na ich wierzchołka źródłowego. Dowiedz się więcej w [partycjonowanie grafu](graph-partitioning.md) artykułu.

## <a name="entity-and-relationship-modeling-guidelines"></a>Jednostki i wskazówki dotyczące modelowania relacji

Poniżej przedstawiono zestaw wskazówek dotyczących danych podejście modelowania dla bazy danych programu graph usługi Azure Cosmos DB — interfejs API Gremlin. Te wytyczne przyjęto założenie, ma istniejącą definicję domeny danych i zapytania dla niego.

> [!NOTE]
> Wykonując poniższe kroki są uporządkowane jako zalecenia. Końcowego modelu powinien być oceniane i testowane przed jego brany pod uwagę jako gotowe do produkcji. Ponadto poniższe zalecenia są specyficzne dla implementacji interfejsu API języka Gremlin usługi Azure Cosmos DB. 

### <a name="modeling-vertices-and-properties"></a>Modelowanie wierzchołków i właściwości 

Pierwszym krokiem dzięki wykresowemu modelowi danych jest aby mapować każdy zidentyfikowany jednostki do **obiektu wierzchołka**. Mapowanie jeden-do-jednego wszystkich jednostek do wierzchołków powinna być kroku początkowego i ulegną zmianie.

Jednej wspólnej niedogodności ma mapowania właściwości pojedynczej jednostki jako osobne wierzchołków. Rozważmy przykład poniżej, gdzie tej samej jednostki jest reprezentowany na dwa sposoby:

* **Na podstawie wierzchołka właściwości**: W tym podejściu jednostki używa trzech oddzielnych wierzchołki i krawędzie dwa do opisu jego właściwości. Chociaż to podejście może zmniejszyć nadmiarowość, zwiększa złożoność modelu. Wzrost złożoności modelu może spowodować opóźnienia dodano, złożoności zapytania i koszt obliczeń. Ten model także może powodować problemy w przypadku użycia partycjonowania.

![Model jednostki za pomocą wierzchołków dla właściwości.](./media/graph-modeling/graph-modeling-1.png)

* **Wierzchołki osadzonych właściwości**: Ta metoda korzysta z listy pary klucz wartość do reprezentowania wszystkich właściwości obiektu wewnątrz wierzchołka. To podejście oferuje bezpieczny modelu zmniejszenie złożoności, co będzie prowadzić do prostsze zapytania i więcej efektywny kosztowo sposób przejścia.

![Model jednostki za pomocą wierzchołków dla właściwości.](./media/graph-modeling/graph-modeling-2.png)

> [!NOTE]
> W powyższym przykładzie pokazano uproszczony dzięki wykresowemu modelowi tylko w celu wyświetlenia porównanie dwóch sposobów dzielenia właściwości jednostki.

**Osadzonych właściwości wierzchołki** wzorzec zwykle zapewnia więcej wydajne i skalowalne podejście. Podejście domyślnego do nowego modelu danych programu graph gravitate należy do tego wzorca.

Jednak istnieją scenariusze, w którym odwołuje się do właściwości może zapewnić korzyści. Na przykład: Jeśli właściwość odwołania jest często aktualizowany. Przy użyciu oddzielnych wierzchołka do reprezentowania właściwość, która stale zmiany będą minimalizację liczby operacji zapisu, które wymagają aktualizacji.

### <a name="relationship-modeling-with-edge-directions"></a>Relacja modelowania z kierunkami krawędzi

Po są modelowane wierzchołki, krawędzie można dodać do oznaczania relacji między nimi. Pierwszy aspektem, która powinna być oceniana jest **kierunek relacji**. 

Obiekty usługi Edge mają domyślny kierunek, w którym następuje przechodzenie, korzystając z `out()` lub `outE()` funkcji. Za pomocą tego naturalnych kierunek skutkuje wydajność operacji, ponieważ wszystkie wierzchołki są przechowywane przy użyciu ich krawędzi wychodzących. 

Jednak przechodzenie w odwrotnym kierunku krawędzi, za pomocą `in()` funkcji, będzie zawsze skutkuje zapytań między partycjami. Dowiedz się więcej o [partycjonowanie grafu](graph-partitioning.md). Jeśli zachodzi potrzeba stale przechodzenie przy użyciu `in()` funkcji, zaleca się dodawania krawędzie w obu kierunkach.

Należy określić kierunek krawędzi przy użyciu `.to()` lub `.from()` predykaty do `.addE()` kroku języka Gremlin. Lub za pomocą [Biblioteka BulkExecutor dla interfejsu API języka Gremlin](bulk-executor-graph-dotnet.md).

> [!NOTE]
> Domyślnie obiekty Edge mają kierunku.

### <a name="relationship-labeling"></a>Etykiety relacji

Za pomocą etykiety relacji opisowy można zwiększyć wydajność operacji rozpoznawania krawędzi. Ten wzorzec można zastosować w następujący sposób:
* Używaj terminów nieogólnego do etykiety relacji.
* Kojarzenie etykiety wierzchołka źródłowego do etykiety wierzchołka docelowego o nazwie relacji.

![Relacja etykietowania przykłady.](./media/graph-modeling/graph-modeling-3.png)

Bardziej szczegółowe etykietę, która traverser zostaną użyte do filtrowania krawędzie, tym lepiej. Ta decyzja może mieć znaczący wpływ na koszt zapytania również. Można oszacować koszt zapytania w dowolnym momencie [za pomocą kroku executionProfile](graph-execution-profile.md).


## <a name="next-steps"></a>Następne kroki: 
* Sprawdź listę obsługiwanych [kroków języka Gremlin](gremlin-support.md).
* Dowiedz się więcej o [programu graph, bazy danych partycjonowanie](graph-partitioning.md) radzenia sobie z dużych wykresów.
* Ocena zapytań języka Gremlin za pomocą [profil wykonania kroku](graph-execution-profile.md).
