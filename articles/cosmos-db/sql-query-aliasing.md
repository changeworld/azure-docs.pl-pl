---
title: Tworzenie aliasów w usłudze Azure Cosmos DB
description: Dowiedz się więcej o wartości aliasów zapytań SQL usługi Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: mjbrown
ms.openlocfilehash: e532fb7180af8a21de6ae9a2e4d798abd9e93e7b
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342887"
---
# <a name="aliasing-in-azure-cosmos-db"></a>Tworzenie aliasów w usłudze Azure Cosmos DB

Można jawnie alias wartości w zapytaniach. Jeśli zapytanie ma dwie właściwości o takiej samej nazwie, należy użyć aliasów, można zmienić nazwy jedną lub obie właściwości, dzięki czemu są one rozróżniane w przewidywany wynik.

## <a name="examples"></a>Przykłady

AS — słowo kluczowe używane do aliasów jest opcjonalne, jak pokazano w poniższym przykładzie, podczas projekcji drugiej wartości jako `NameInfo`:

```sql
    SELECT 
           { "state": f.address.state, "city": f.address.city } AS AddressInfo,
           { "name": f.id } NameInfo
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Wyniki są:

```json
    [{
      "AddressInfo": {
        "state": "WA",
        "city": "Seattle"
      },
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]
```

## <a name="next-steps"></a>Kolejne kroki

- [Przykłady dla platformy .NET w usłudze Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Klauzula SELECT](sql-query-select.md)
- [FROM — klauzula](sql-query-from.md)
