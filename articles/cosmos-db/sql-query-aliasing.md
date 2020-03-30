---
title: Aliasing w usłudze Azure Cosmos DB
description: Dowiedz się, jak używać aliasingu w kwerendach SQL usługi Azure Cosmos DB do rozróżniania dwóch właściwości o tej samej nazwie
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 74849eec4c5808a584894321269c49c41f0b8a5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873475"
---
# <a name="aliasing-in-azure-cosmos-db"></a>Aliasing w usłudze Azure Cosmos DB

W kwerendach można jawnie aliasować wartości. Jeśli kwerenda ma dwie właściwości o tej samej nazwie, użyj aliasingu, aby zmienić nazwę jednej lub obu właściwości, tak aby były one niejednoznaczne w przewidywanym wyniku.

## <a name="examples"></a>Przykłady

Słowo kluczowe AS używane do aliasowania jest opcjonalne, jak pokazano `NameInfo`w poniższym przykładzie podczas rzutowania drugiej wartości jako:

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
- [Klauzula OD](sql-query-from.md)
