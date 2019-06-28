---
title: Klauzula WHERE w usłudze Azure Cosmos DB
description: Dowiedz się więcej o klauzuli WHERE języka SQL dla usługi Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 6a942e48ffea7785fe971cc2f8fa66e8569ed672
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342794"
---
# <a name="where-clause"></a>Klauzula WHERE

Opcjonalna klauzula WHERE (`WHERE <filter_condition>`) określa warunki, w formacie JSON źródła musi spełniać dla zapytania je uwzględnić w wynikach. Element JSON musi być określone warunki, aby `true` wziąć pod uwagę na wynik. Warstwa indeksu używa klauzuli WHERE, aby określić najmniejszy podzbiór elementów źródła, które mogą być częścią wynik.
  
## <a name="syntax"></a>Składnia
  
```sql  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
## <a name="arguments"></a>Argumenty

- `<filter_condition>`  
  
   Określa warunek do spełnienia dokumentów, które mają zostać zwrócone.  
  
- `<scalar_expression>`  
  
   Wyrażenie reprezentujące wartość ma zostać obliczony. Zobacz [wyrażenia skalarne](sql-query-scalar-expressions.md) Aby uzyskać szczegółowe informacje.  
  

## <a name="remarks"></a>Uwagi
  
  W kolejności dokumentu, które mają zostać zwrócone wyrażenie określone jako filtr warunek musi zwrócić wartość true. Tylko wartość logiczną PRAWDA będzie spełniać warunek jakakolwiek inna wartość: Niezdefiniowany, null, wartość false, liczby, tablicy lub obiektu nie spełnia warunku. 

## <a name="examples"></a>Przykłady

Poniższe zapytanie elementów żądań, które zawierają `id` właściwości, których wartość jest `AndersenFamily`. Wyklucza dowolny element, który nie ma `id` właściwości lub którego wartość nie jest zgodna `AndersenFamily`.

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Wyniki są:

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

Można użyć następujących obsługiwanych operatory binarne:  

|**Typ operatora**  | **Wartości** |
|---------|---------|
|Arytmetyczny | +,-,*,/,% |
|Bitowy    | \|, &, ^, <<, >>, >>> (wypełnienie zerami, przesunięcie w prawo) |
|Logiczny    | AND, OR, NOT      |
|Porównanie | =, !=, &lt;, &gt;, &lt;=, &gt;=, <> |
|String     |  \|\| (łączenie) |

Operatory dwuargumentowe należy użyć następujących zapytań:

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

Można również użyć operatorów jednoargumentowych +,-, ~, a nie w zapytaniach, jak pokazano w poniższych przykładach:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5
```

Umożliwia także odwołania do właściwości w zapytaniach. Na przykład `SELECT * FROM Families f WHERE f.isRegistered` zwraca element JSON zawierający właściwości `isRegistered` o wartości równej `true`. Wszelkie inne wartości, takich jak `false`, `null`, `Undefined`, `<number>`, `<string>`, `<object>`, lub `<array>`, wyklucza element z wynikiem. 

## <a name="next-steps"></a>Kolejne kroki

- [Rozpoczęcie pracy](sql-query-getting-started.md)
- [Przykłady dla platformy .NET w usłudze Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-dotnet)
- [FROM — klauzula](sql-query-from.md)