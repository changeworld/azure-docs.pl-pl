---
title: Tworzenie aliasów w Azure Cosmos DB
description: Informacje o aliasach wartości w Azure Cosmos DB zapytaniach SQL
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: mjbrown
ms.openlocfilehash: 3b17cbc7710647b1e1875025a1db1849034ec1dc
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/15/2019
ms.locfileid: "71002082"
---
# <a name="aliasing-in-azure-cosmos-db"></a>Tworzenie aliasów w Azure Cosmos DB

Można jawnie aliasować wartości w zapytaniach. Jeśli zapytanie ma dwie właściwości o tej samej nazwie, użyj aliasu, aby zmienić nazwę jednej lub obu właściwości, tak aby były one niejednoznaczne w przewidywanym wyniku.

## <a name="examples"></a>Przykłady

Słowo kluczowe AS używane do aliasowania jest opcjonalne, jak pokazano w poniższym przykładzie podczas projekcji drugiej wartości jako `NameInfo`:

```sql
    SELECT 
           { "state": f.address.state, "city": f.address.city } AS AddressInfo,
           { "name": f.id } NameInfo
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Wyniki są następujące:

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

## <a name="next-steps"></a>Następne kroki

- [Przykłady dla platformy .NET w usłudze Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Klauzula SELECT](sql-query-select.md)
- [FROM — klauzula](sql-query-from.md)
