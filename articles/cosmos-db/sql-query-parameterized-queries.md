---
title: Sparametryzowane zapytania w usłudze Azure Cosmos DB
description: Dowiedz się, jak zapytania sparametryzowane SQL zapewniają niezawodną obsługę i ucieczkę od danych wejściowych użytkownika oraz zapobiegają przypadkowemu narażeniu danych za pomocą iniekcji SQL.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: tisande
ms.openlocfilehash: e15a8236723c1efd80f27f2d253e9bbc44af4b0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74870823"
---
# <a name="parameterized-queries-in-azure-cosmos-db"></a>Sparametryzowane zapytania w usłudze Azure Cosmos DB

Usługa Cosmos DB obsługuje zapytania z parametrami wyrażonymi przez znane @ notacji. Sparametryzowany SQL zapewnia niezawodną obsługę i ucieczkę od danych wejściowych użytkownika i zapobiega przypadkowemu narażeniu danych za pomocą iniekcji SQL.

## <a name="examples"></a>Przykłady

Na przykład można napisać `lastName` kwerendę, `address.state` która przyjmuje i jako parametry `lastName` `address.state` i wykonać go dla różnych wartości i na podstawie danych wejściowych użytkownika.

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

Następnie można wysłać to żądanie do usługi Cosmos DB jako sparametryzowane zapytanie JSON, takie jak następujące:

```sql
    {
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",
        "parameters": [
            {"name": "@lastName", "value": "Wakefield"},
            {"name": "@addressState", "value": "NY"},
        ]
    }
```

W poniższym przykładzie ustawia argument TOP z kwerendą sparametryzowaną: 

```sql
    {
        "query": "SELECT TOP @n * FROM Families",
        "parameters": [
            {"name": "@n", "value": 10},
        ]
    }
```

Wartości parametrów mogą być dowolną prawidłową JSON: ciągi, liczby, wartości logiczne, null, parzyste tablice lub zagnieżdżone JSON. Ponieważ usługa Cosmos DB jest bez schematu, parametry nie są sprawdzane względem dowolnego typu.


## <a name="next-steps"></a>Następne kroki

- [Przykłady dla platformy .NET w usłudze Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Modelowanie danych dokumentów](modeling-data.md)
