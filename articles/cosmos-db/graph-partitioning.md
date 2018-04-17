---
title: Interfejs API Graph partycjonowania | Dokumentacja firmy Microsoft
description: Dowiedz się używania partycjonowanej Graph w usłudze Azure DB rozwiązania Cosmos.
services: cosmos-db
author: luisbosquez
documentationcenter: ''
manager: kfile
ms.assetid: ''
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/28/2018
ms.author: lbosq
ms.openlocfilehash: db41efeee467d2cda89f62e0a18cf89cec2d9e63
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="using-a-partitioned-graph-in-azure-cosmos-db"></a>Przy użyciu wykresu podzielonym na partycje w usłudze Azure DB rozwiązania Cosmos

Jednym z kluczowych funkcji usługi interfejsu API programu Graph w usłudze Azure DB rozwiązania Cosmos jest zdolności do obsługi dużych wykresów za pomocą skalowania w poziomie. Ten proces odbywa się za pośrednictwem [partycjonowania możliwości w usłudze Azure DB rozwiązania Cosmos](partition-data.md#how-does-partitioning-work), którego należy użyć kolekcji, nazywana także kontenerów, które mogą być skalowane niezależnie pod względem pamięci masowej i przepływność. Azure DB rozwiązania Cosmos obsługuje następujące typy kontenerów we wszystkich interfejsów API:

- **Stałe kolekcji**: wykres mogą być przechowywane w tych kolekcjach bazy danych o rozmiarze maksymalnie 10 000 jednostek żądań na sekundę przydzielonego do 10 GB. Aby utworzyć kolekcję stałej nie należy określić właściwość klucza partycji w danych.

- **Nieograniczone kolekcji**: kolekcjach może automatycznie skalować do przechowywania wykres przekroczenie limitu 10 GB, za pomocą poziomych partycji. Każda partycja zapisze 10 GB, a dane są rozmieszczane automatycznie równomiernie na podstawie **klucza określonej partycji**, który będzie wymagany parametr po użyciu nieograniczone kolekcji. Tego typu kontener może przechowywać nieograniczoną wielkość i umożliwiają maksymalnie 100 000 jednostek żądań na sekundę lub więcej [za pośrednictwem pracowników pomocy technicznej](https://aka.ms/cosmosdbfeedback?subject=Cosmos%20DB%20More%20Throughput%20Request).

W tym dokumencie omówione charakterystykę na jak wykres baz danych są podzielone na partycje wraz z jego wpływ na zarówno wierzchołków (lub węzły) i krawędzi.

## <a name="requirements-for-partitioned-graph"></a>Wymagania dotyczące wykresu podzielonym na partycje

Poniżej przedstawiono szczegółowe informacje, które muszą rozumieć, podczas tworzenia kontenera wykres podzielonym na partycje:
- **Konfigurowanie partycjonowania będzie konieczne** Jeśli kolekcji powinien być więcej niż 10 GB i/lub jeśli przydzieleniu ponad 10 000 jednostek żądań na sekundę (RU/s) będzie wymagane.
- **Zarówno wierzchołki i krawędzi są przechowywane jako dokumenty JSON** w wewnętrznej bazy danych Azure rozwiązania Cosmos interfejsu API programu Graph kontenera.
- **Wierzchołki wymagają klucza partycji**. Ten klucz określi, w których partycji wierzchołka będą przechowywane przy użyciu algorytmu wyznaczania wartości skrótu. Nazwa tego klucza partycji jest jednowyrazowej ciągu bez spacji i znaków specjalnych, i jest on zdefiniowany podczas tworzenia nowej kolekcji w formacie `/partitioning-key-name` w portalu.
- **Krawędzi zostanie zapisany z ich wierzchołków źródła**. Innymi słowy dla każdego wierzchołka kluczem partycji określi, gdzie będą przechowywane wraz z jego krawędzi wychodzących. Pozwala to uniknąć między partycji zapytania, używając `out()` Kardynalność w zapytaniach wykresu.
- **Wykres zapytania należy określić klucz partycji**. Do w pełni korzystać poziome partycje w usłudze Azure DB rozwiązania Cosmos, należy określić klucz partycji podczas jednego wierzchołka jest zaznaczone, jeśli to możliwe. Zapytania dotyczące wybranie jednego lub wielu wierzchołki na wykresie podzielonym na partycje są następujące:

    - Wybieranie wierzchołków przez identyfikator, następnie **przy użyciu `.has()` krok, aby określić właściwości klucza partycji**: 
    
        ```
        g.V('vertex_id').has('partitionKey', 'partitionKey_value')
        ```
    
    - Wybieranie wierzchołków przez **określenie krotka tym wartość klucza partycji i identyfikator**: 
    
        ```
        g.V(['partitionKey_value', 'vertex_id'])
        ```
        
    - Określanie **tablicy krotek wartości kluczy partycji i identyfikatorów**:
    
        ```
        g.V(['partitionKey_value0', 'verted_id0'], ['partitionKey_value1', 'vertex_id1'], ...)
        ```
        
    - Wybieranie zestawu wierzchołki i **określenie listy wartości klucza partycji**: 
    
        ```
        g.V('vertex_id0', 'vertex_id1', 'vertex_id2', …).has('partitionKey', within('partitionKey_value0', 'partitionKey_value01', 'partitionKey_value02', …)
        ```

## <a name="best-practices-when-using-a-partitioned-graph"></a>Najlepsze rozwiązania w sytuacji, gdy przy użyciu wykresu podzielonym na partycje

Poniżej przedstawiono wskazówki, które powinny być zachowana w celu zapewnienia najbardziej efektywny wydajność i skalowalność w przypadku używanie wykresów podzielonym na partycje w kolekcjach nieograniczone:
- **Zawsze podać wartość klucza partycji podczas wykonywania zapytania wierzchołek**. Uzyskiwanie wierzchołek w znanych partycji jest najbardziej wydajny sposób pod względem wydajności.
- **Podczas wykonywania zapytania krawędzi, jeśli to możliwe, użyj kierunku wychodzącego**. Jak wspomniano powyżej, krawędzi są przechowywane ich wierzchołków źródła w kierunku wychodzącego. Oznacza to, że ryzyko ponowne sortowanie cross-partycji zapytania są zminimalizowane po danych i zapytania są zaprojektowane z tego wzorca pamiętać.
- **Wybierz klucz partycji, która zostanie równomiernie rozpowszechniają danych partycji**. Ta decyzja zależy silnie rozwiązania w modelu danych. Przeczytaj więcej na temat tworzenia kluczy partycji odpowiednie w [Partitining i skali w usłudze Azure DB rozwiązania Cosmos](partition-data.md).
- **Optymalizacja zapytania, aby uzyskać dane w granicach partycji, jeśli to możliwe**. Optymalnej strategii partycjonowania będzie wyrównany do wzorców wykonywanie zapytania. Zapytania, które uzyskują dane z jednej partycji zapewniają najlepszą wydajność.

## <a name="next-steps"></a>Kolejne kroki
W tym artykule podano omówienie pojęć i najlepsze rozwiązania dotyczące partycjonowania z interfejsu API Graph usługi Azure rozwiązania Cosmos bazy danych. 

* Dowiedz się więcej o [partycji i skali w usłudze Azure DB rozwiązania Cosmos](partition-data.md).
* Dowiedz się więcej o [obsługę Gremlin w interfejsu API programu Graph](gremlin-support.md).
* Dowiedz się więcej o [wprowadzenie do interfejsu Graph API](graph-introduction.md).
