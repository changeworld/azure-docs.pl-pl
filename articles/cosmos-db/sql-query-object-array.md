---
title: Praca z macierzami i obiektami w usłudze Azure Cosmos DB
description: Poznaj składnię SQL, aby utworzyć tablice i obiekty w usłudze Azure Cosmos DB. W tym artykule podano również kilka przykładów do wykonywania operacji na obiektach tablicowych
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 5b2801b0a71f04803955e9d8bc18a97133019996
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246554"
---
# <a name="working-with-arrays-and-objects-in-azure-cosmos-db"></a>Praca z macierzami i obiektami w usłudze Azure Cosmos DB

Kluczową cechą interfejsu API SQL usługi Azure Cosmos DB jest tworzenie tablic i obiektów.

## <a name="arrays"></a>Tablice

Można konstruować tablice, jak pokazano w poniższym przykładzie:

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

Można również użyć [wyrażenie ARRAY](sql-query-subquery.md#array-expression) do konstruowania tablicy z wyników [podzapytania.](sql-query-subquery.md) Ta kwerenda pobiera wszystkie różne podane nazwy wiązek podrzędnych w tablicy.

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

## <a name="iteration"></a><a id="Iteration"></a>Iteracja

Interfejs API SQL zapewnia obsługę iteracji za pośrednictwem tablic JSON, z nową konstrukcją dodaną za pomocą [słowa kluczowego IN](sql-query-keywords.md#in) w źródle FROM. W poniższym przykładzie:

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

Następna kwerenda wykonuje iteracji w `children` kontenerze. `Families` Tablica wyjściowa różni się od poprzedniej kwerendy. W tym `children`przykładzie dzieli i spłaszcza wyniki w jednej tablicy:  

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

Można filtrować dalej przy każdym pojedynczym wpisie tablicy, jak pokazano w poniższym przykładzie:

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

Można również agregować w wyniku iteracji tablicy. Na przykład następująca kwerenda zlicza liczbę dzieci we wszystkich rodzinach:

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

- [Wprowadzenie](sql-query-getting-started.md)
- [Przykłady dla platformy .NET w usłudze Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Łączy](sql-query-join.md)