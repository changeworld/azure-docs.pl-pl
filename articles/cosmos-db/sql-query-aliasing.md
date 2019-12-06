---
title: Tworzenie aliasów w Azure Cosmos DB
description: Dowiedz się, jak używać aliasów w Azure Cosmos DB zapytań SQL, aby odróżnić dwie właściwości o tej samej nazwie
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 74849eec4c5808a584894321269c49c41f0b8a5c
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873475"
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
