---
title: Praca z tablicami i obiektami w usłudze Azure Cosmos DB
description: Więcej informacji na temat tablicy i obiektów tworzenia składni SQL usługi Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: tisande
ms.openlocfilehash: 338f3b51edf38d20a963992e121b7e2dbd0c6873
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342767"
---
# <a name="working-with-arrays-and-objects-in-azure-cosmos-db"></a>Praca z tablicami i obiektami w usłudze Azure Cosmos DB

Kluczową funkcją interfejsu API SQL usługi Azure Cosmos DB jest tworzenie tablicy i obiektów.

## <a name="arrays"></a>Tablice

Można utworzyć tablic, jak pokazano w poniższym przykładzie:

```sql
    SELECT [f.address.city, f.address.state] AS CityState
    FROM Families f
```

Wyniki są:

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

Można również użyć [wyrażenia tablicy](sql-query-subquery.md#array-expression) do utworzenia tablicy z [w podzapytaniu](sql-query-subquery.md) wyników. To zapytanie pobiera różne imiona wszystkich dzieci w tablicy.

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

## <a id="Iteration"></a>Iteracja

Interfejs API SQL zapewnia obsługę Iterowanie przez tablice JSON z nową konstrukcję dodane za pośrednictwem [IN — słowo kluczowe](sql-query-keywords.md#in) w źródle FROM. W poniższym przykładzie:

```sql
    SELECT *
    FROM Families.children
```

Wyniki są:

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

Następne zapytanie wykonuje iterację przez `children` w `Families` kontenera. Tablica dane wyjściowe różni się od poprzedniego zapytania. Ten przykład dzieli `children`i spłaszcza wyniki do jednej tablicy:  

```sql
    SELECT *
    FROM c IN Families.children
```

Wyniki są:

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

Można filtrować dalsze na każdy pojedynczy wpis tablicy, jak pokazano w poniższym przykładzie:

```sql
    SELECT c.givenName
    FROM c IN Families.children
    WHERE c.grade = 8
```

Wyniki są:

```json
    [{
      "givenName": "Lisa"
    }]
```

Możesz także agregować za pośrednictwem wyniku iterację tablicy. Na przykład następujące zapytanie zlicza liczbę elementów podrzędnych wśród wszystkich rodzin:

```sql
    SELECT COUNT(child)
    FROM child IN Families.children
```

Wyniki są:

```json
    [
      {
        "$1": 3
      }
    ]
```

## <a name="next-steps"></a>Kolejne kroki

- [Rozpoczęcie pracy](sql-query-getting-started.md)
- [Przykłady dla platformy .NET w usłudze Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Sprzężenia](sql-query-join.md)