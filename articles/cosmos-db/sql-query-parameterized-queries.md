---
title: Sparametryzowanych zapytań w usłudze Azure Cosmos DB
description: Dowiedz się więcej na temat zapytań SQL sparametryzowane
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: tisande
ms.openlocfilehash: 2bfc22346c1dd43d7d3c2937ffc286e48ae774d0
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342907"
---
# <a name="parameterized-queries-in-azure-cosmos-db"></a>Sparametryzowanych zapytań w usłudze Azure Cosmos DB

Usługa cosmos DB obsługuje zapytania z parametrami wyrażone w znanej @ notacji. SQL — sparametryzowane zapewnia niezawodne obsługi i anulowania zapewnianego element danych wejściowych użytkownika i uniemożliwia przypadkowe ujawnienie danych przez wstrzyknięcie kodu SQL.

## <a name="examples"></a>Przykłady

Na przykład można napisać zapytanie pobierające `lastName` i `address.state` jako parametry i wykonaj go dla różnych wartości `lastName` i `address.state` na podstawie danych wejściowych użytkownika.

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

Następnie możesz wysłać żądanie do usługi Cosmos DB jako sparametryzowanych zapytań JSON, jak pokazano poniżej:

```sql
    {
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",
        "parameters": [
            {"name": "@lastName", "value": "Wakefield"},
            {"name": "@addressState", "value": "NY"},
        ]
    }
```

W poniższym przykładzie ustawiono argument TOP za pomocą sparametryzowanych zapytań: 

```sql
    {
        "query": "SELECT TOP @n * FROM Families",
        "parameters": [
            {"name": "@n", "value": 10},
        ]
    }
```

Wartości parametru może być dowolnym prawidłowym kodem JSON: ciągi, liczby, wartości logicznych ma wartość null, nawet wtedy, tablic lub są zagnieżdżone JSON. Ponieważ usługi Cosmos DB korzysta ze schematów, parametry nie są weryfikowany pod kątem dowolnego typu.


## <a name="next-steps"></a>Kolejne kroki

- [Przykłady dla platformy .NET w usłudze Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Modelowanie danych dokumentów](modeling-data.md)
