---
title: Klauzula WHERE w Azure Cosmos DB
description: Informacje o klauzuli WHERE języka SQL dla Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 362024868de269ed64a440a25e8c19c5b68bef80
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003477"
---
# <a name="where-clause"></a>Klauzula WHERE

Opcjonalna klauzula WHERE (`WHERE <filter_condition>`) określa warunek (y), które muszą spełniać źródłowe elementy JSON dla zapytania, aby uwzględnić je w wynikach. Element JSON musi oszacować określone warunki, aby `true` można je było uwzględnić w wyniku. Warstwa indeksu używa klauzuli WHERE do określenia najmniejszego podzestawu elementów źródłowych, które mogą być częścią wyniku.
  
## <a name="syntax"></a>Składnia
  
```sql  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
## <a name="arguments"></a>Argumenty

- `<filter_condition>`  
  
   Określa warunek do spełnienia dokumentów, które mają zostać zwrócone.  
  
- `<scalar_expression>`  
  
   Wyrażenie reprezentujące wartość ma zostać obliczony. Aby uzyskać szczegółowe informacje, zobacz [wyrażenia skalarne](sql-query-scalar-expressions.md) .  
  

## <a name="remarks"></a>Uwagi
  
  W kolejności dokumentu, które mają zostać zwrócone wyrażenie określone jako filtr warunek musi zwrócić wartość true. Tylko wartość logiczną PRAWDA będzie spełniać warunek jakakolwiek inna wartość: Niezdefiniowany, null, wartość false, liczby, tablicy lub obiektu nie spełnia warunku. 

## <a name="examples"></a>Przykłady

Następujące zapytanie żąda elementów, które zawierają `id` właściwość, której wartość to. `AndersenFamily` Wyklucza wszelkie elementy, które nie mają `id` właściwości lub których wartość nie jest zgodna. `AndersenFamily`

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
|String     |  \|\| (łączenie) |

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

Można również użyć odwołań do właściwości w zapytaniach. Na przykład `SELECT * FROM Families f WHERE f.isRegistered` zwraca element JSON zawierający Właściwość `isRegistered` o wartości równej `true`. Każda inna wartość, taka jak `false` `Undefined`, `null` `<number>` `<string>` `<array>`,,,, lub, wyklucza element z wyniku. `<object>` 

## <a name="next-steps"></a>Następne kroki

- [Rozpoczęcie pracy](sql-query-getting-started.md)
- [Przykłady dla platformy .NET w usłudze Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [FROM — klauzula](sql-query-from.md)