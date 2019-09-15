---
title: Praca z tablicami i obiektami w Azure Cosmos DB
description: Informacje na temat składni SQL tworzenia obiektów i tablic dla Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: tisande
ms.openlocfilehash: 17a0e4ddf5acd267a4cfbb68c218fe9409a91d57
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003936"
---
# <a name="working-with-arrays-and-objects-in-azure-cosmos-db"></a>Praca z tablicami i obiektami w Azure Cosmos DB

Kluczową funkcją interfejsu API SQL Azure Cosmos DB jest tworzenie tablic i obiektów.

## <a name="arrays"></a>Tablice

Można skonstruować tablice, jak pokazano w następującym przykładzie:

```sql
    SELECT [f.address.city, f.address.state] AS CityState
    FROM Families f
```

Wyniki są następujące:

```json
    [
      {
        "CityState": [
          "Seattle",
          "WA"
        ]
      },
      {
        "CityState": [
          "NY", 
          "NY"
        ]
      }
    ]
```

Możesz również użyć [wyrażenia Array](sql-query-subquery.md#array-expression) , aby utworzyć tablicę z wyników [podzapytania](sql-query-subquery.md) . To zapytanie pobiera wszystkie różne nazwy elementów podrzędnych w tablicy.

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

## <a id="Iteration"></a>Iteracja

Interfejs API SQL zapewnia obsługę iteracji w tablicach JSON, a nowa konstrukcja dodana za pośrednictwem [słowa kluczowego in](sql-query-keywords.md#in) w źródle from. W poniższym przykładzie:

```sql
    SELECT *
    FROM Families.children
```

Wyniki są następujące:

```json
    [
      [
        {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy"}]
        }
      ], 
      [
        {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        }, 
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]
```

Następne zapytanie wykonuje iterację `children` `Families` w kontenerze. Tablica wyjściowa różni się od powyższego zapytania. Ten przykład dzieli `children`i spłaszcza wyniki do pojedynczej tablicy:  

```sql
    SELECT *
    FROM c IN Families.children
```

Wyniki są następujące:

```json
    [
      {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy" }]
      },
      {
          "familyName": "Merriam",
          "givenName": "Jesse",
          "gender": "female",
          "grade": 1
      },
      {
          "familyName": "Miller",
          "givenName": "Lisa",
          "gender": "female",
          "grade": 8
      }
    ]
```

Można filtrować więcej według poszczególnych wpisów tablicy, jak pokazano w następującym przykładzie:

```sql
    SELECT c.givenName
    FROM c IN Families.children
    WHERE c.grade = 8
```

Wyniki są następujące:

```json
    [{
      "givenName": "Lisa"
    }]
```

Można również agregować wynik iteracji tablicy. Na przykład następujące zapytanie liczy liczbę elementów podrzędnych między wszystkimi rodzinami:

```sql
    SELECT COUNT(child)
    FROM child IN Families.children
```

Wyniki są następujące:

```json
    [
      {
        "$1": 3
      }
    ]
```

## <a name="next-steps"></a>Następne kroki

- [Rozpoczęcie pracy](sql-query-getting-started.md)
- [Przykłady dla platformy .NET w usłudze Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Łącze](sql-query-join.md)