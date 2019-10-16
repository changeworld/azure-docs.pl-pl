---
title: Klauzula WHERE w Azure Cosmos DB
description: Informacje o klauzuli WHERE języka SQL dla Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: cd5643d8be06afcd43c5bfe38d6f5e9caa6f906e
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72326639"
---
# <a name="where-clause-in-azure-cosmos-db"></a>Klauzula WHERE w Azure Cosmos DB

Opcjonalna klauzula WHERE (`WHERE <filter_condition>`) określa warunki, że źródłowe elementy JSON muszą być spełnione dla zapytania, aby uwzględnić je w wynikach. Element JSON musi oszacować określone warunki, aby `true` do uwzględnienia w wyniku. Warstwa indeksu używa klauzuli WHERE do określenia najmniejszego podzestawu elementów źródłowych, które mogą być częścią wyniku.
  
## <a name="syntax"></a>Składnia
  
```sql  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
## <a name="arguments"></a>Argumenty

- `<filter_condition>`  
  
   Określa warunek, który ma zostać spełniony dla dokumentów, które mają zostać zwrócone.  
  
- `<scalar_expression>`  
  
   Wyrażenie reprezentujące wartość, która ma zostać obliczona. Aby uzyskać szczegółowe informacje, zobacz [wyrażenia skalarne](sql-query-scalar-expressions.md) .  
  

## <a name="remarks"></a>Uwagi
  
  Aby dokument był zwracany wyrażenie określone jako warunek filtru musi mieć wartość true. Tylko wartość logiczna true będzie spełniać warunek, żadna inna wartość: undefined, null, false, Number, array lub Object nie spełnia warunku. 

## <a name="examples"></a>Przykłady

Następujące zapytanie żąda elementów, które zawierają Właściwość `id`, której wartość jest `AndersenFamily`. Wyklucza każdy element, który nie ma właściwości `id` lub którego wartość nie jest zgodna z `AndersenFamily`.

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Wyniki są następujące:

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

### <a name="scalar-expressions-in-the-where-clause"></a>Wyrażenia skalarne w klauzuli WHERE

W poprzednim przykładzie pokazano proste zapytanie dotyczące równości. Interfejs API SQL obsługuje również różne [wyrażenia skalarne](sql-query-scalar-expressions.md). Najczęściej używane są wyrażenia binarne i jednoargumentowe. Odwołania do właściwości z obiektu JSON źródła są również prawidłowymi wyrażeniami.

Można użyć następujących obsługiwanych operatorów binarnych:  

|**Typ operatora**  | **Wartości** |
|---------|---------|
|Arytmetyczny | +,-,*,/,% |
|Bitowy    | \|, &, ^, <<, >>, >>> (wypełnienie zerami, przesunięcie w prawo) |
|Logiczny    | AND, OR, NOT      |
|Porównanie | =, !=, &lt;, &gt;, &lt;=, &gt;=, <> |
|Ciąg     |  \|\| (łączenie) |

Następujące zapytania używają operatorów binarnych:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade % 2 = 1     -- matching grades == 5, 1

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade ^ 4 = 1    -- matching grades == 5

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade >= 5    -- matching grades == 5
```

Można również użyć operatorów jednoargumentowych +,-, ~, a nie w zapytaniach, jak pokazano w następujących przykładach:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5
```

Można również użyć odwołań do właściwości w zapytaniach. Na przykład `SELECT * FROM Families f WHERE f.isRegistered` zwraca element JSON zawierający Właściwość `isRegistered` o wartości równej `true`. Każda inna wartość, taka jak `false`, `null`, `Undefined`, `<number>`, `<string>`, `<object>` lub `<array>`, wyklucza element z wyniku. 

## <a name="next-steps"></a>Następne kroki

- [Rozpoczęcie pracy](sql-query-getting-started.md)
- [Przykłady dla platformy .NET w usłudze Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Klauzula FROM](sql-query-from.md)