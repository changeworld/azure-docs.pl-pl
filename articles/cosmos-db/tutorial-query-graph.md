---
title: Jak wykonywać zapytania względem danych grafu w usłudze Azure Cosmos DB? | Microsoft Docs
description: Dowiedz się, jak wykonywać zapytania względem danych grafu w usłudze Azure Cosmos DB
services: cosmos-db
documentationcenter: ''
author: luisbosquez
manager: kfile
editor: ''
tags: ''
ms.assetid: 8bde5c80-581c-4f70-acb4-9578873c92fa
ms.service: cosmos-db
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 01/02/2018
ms.author: lbosq
ms.custom: mvc
ms.openlocfilehash: 449821d6121f8fec40b151ae06f687586133c3d1
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2018
---
# <a name="tutorial-query-azure-cosmos-db-graph-api-by-using-gremlin"></a>Samouczek: Wykonywanie zapytań w interfejsie API programu Graph w usłudze Azure Cosmos DB przy użyciu języka Gremlin

[Interfejs API programu Graph](graph-introduction.md) w usłudze Azure Cosmos DB obsługuje zapytania w języku [Gremlin](https://github.com/tinkerpop/gremlin/wiki). W tym artykule udostępniono przykładowe dokumenty i zapytania ułatwiające rozpoczęcie pracy. Szczegółowa dokumentacja dotycząca języka Gremlin została podana w artykule [Obsługa języka Gremlin](gremlin-support.md).

W tym artykule opisano następujące zadania: 

> [!div class="checklist"]
> * Wykonywanie zapytań na danych przy użyciu języka Gremlin

## <a name="prerequisites"></a>Wymagania wstępne

Aby te zapytania działały, musisz mieć konto usługi Azure Cosmos DB i mieć dane grafu w kontenerze. Nie spełniasz tych warunków? Ukończ [5-minutowy przewodnik Szybki start](create-graph-dotnet.md) lub [samouczek dewelopera](tutorial-query-graph.md), aby utworzyć konto i wypełnić bazę danych. Poniższe zapytania możesz uruchomić przy użyciu [biblioteki programu Graph na platformie .NET w usłudze Azure Cosmos DB](graph-sdk-dotnet.md), [konsoli Gremlin](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console) lub ulubionego sterownika Gremlin.

## <a name="count-vertices-in-the-graph"></a>Liczba wierzchołków grafu

Poniższy fragment kodu pokazuje sposób obliczania liczby wierzchołków grafu:

```
g.V().count()
```

## <a name="filters"></a>Filtry

Filtry można wykonywać przy użyciu kroków `has` i `hasLabel`, a także łączyć je przy użyciu operatorów `and`, `or` i `not` w celu utworzenia bardziej złożonych filtrów. Usługa Azure Cosmos DB zapewnia niezależne od schematów indeksowanie wszystkich zależności w wierzchołkach i stopniach na potrzeby szybkich zapytań:

```
g.V().hasLabel('person').has('age', gt(40))
```

## <a name="projection"></a>Projekcja

Pewne właściwości wyników zapytania można poddawać projekcji za pomocą kroku `values`:

```
g.V().hasLabel('person').values('firstName')
```

## <a name="find-related-edges-and-vertices"></a>Znajdowanie powiązanych krawędzi i wierzchołków

Jak dotąd przedstawiono tylko operatory zapytań, które działają w dowolnej bazie danych. Grafy są szybkie i wydajne dla operacji przechodzenia, gdy trzeba przejść do powiązanych krawędzi i wierzchołków. Znajdźmy wszystkich znajomych Thomasa. Możemy to zrobić, używając kroku `outE` języka Gremlin w celu znalezienia wszystkich krawędzi wychodzących od Thomasa, a następnie przechodząc do wierzchołków z tych krawędzi przy użyciu kroku `inV` języka Gremlin:

```cs
g.V('thomas').outE('knows').inV().hasLabel('person')
```

Następne zapytanie wykonuje dwa przeskoki w celu znalezienia wszystkich „znajomych znajomych” Thomasa, wywołując `outE` i `inV` dwa razy. 

```cs
g.V('thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')
```

Możesz tworzyć bardziej złożone zapytania i implementować zaawansowaną logikę przechodzenia grafu za pomocą języka Gremlin, w tym mieszanie wyrażeń filtrowania, wykonywanie zapętlenia przy użyciu kroku `loop` i implementowanie nawigacji warunkowej przy użyciu kroku `choose`. Dowiedz się więcej o tym, co można zrobić dzięki [obsłudze języka Gremlin](gremlin-support.md).

## <a name="next-steps"></a>Następne kroki

W tym samouczku wykonano następujące czynności:

> [!div class="checklist"]
> * Przedstawiono sposób wykonywania zapytań przy użyciu programu Graph 

Możesz teraz przejść do następnego samouczka, aby dowiedzieć się, jak dystrybuować swoje dane globalnie.

> [!div class="nextstepaction"]
> [Globalna dystrybucja danych](tutorial-global-distribution-graph.md)
