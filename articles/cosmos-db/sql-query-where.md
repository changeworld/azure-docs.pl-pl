---
title: Klauzula WHERE w usłudze Azure Cosmos DB
description: Dowiedz się więcej o klauzuli SQL WHERE dla usługi Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: tisande
ms.openlocfilehash: 483a0533eafc81ef8698d260a753062ae074f6d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898773"
---
# <a name="where-clause-in-azure-cosmos-db"></a>Klauzula WHERE w usłudze Azure Cosmos DB

Opcjonalna klauzula`WHERE <filter_condition>`WHERE ( ) określa warunki, które źródłowe elementy JSON muszą spełniać, aby kwerenda zawierała je w wynikach. Element JSON musi ocenić `true` określone warunki, które mają być brane pod uwagę dla wyniku. Warstwa indeksu używa klauzuli WHERE do określenia najmniejszego podzbioru elementów źródłowych, które mogą być częścią wyniku.
  
## <a name="syntax"></a>Składnia
  
```sql  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
## <a name="arguments"></a>Argumenty

- `<filter_condition>`  
  
   Określa warunek, który ma być spełniony dla dokumentów, które mają być zwracane.  
  
- `<scalar_expression>`  
  
   Wyrażenie reprezentujące wartość, która ma być obliczona. Zobacz [scalar wyrażeń,](sql-query-scalar-expressions.md) aby uzyskać szczegółowe informacje.  
  
## <a name="remarks"></a>Uwagi
  
  Aby dokument ma zostać zwrócony, wyrażenie określone jako warunek filtru musi zostać ocenione jako true. Tylko wartość `true` logiczna spełnia warunek, każda inna wartość: undefined, null, false, Number, Array lub Object nie spełni warunku.

  Jeśli dołączysz klucz partycji do `WHERE` klauzuli jako część filtru równości, kwerenda zostanie automatycznie filtrowana tylko do odpowiednich partycji.

## <a name="examples"></a>Przykłady

Następująca kwerenda żąda `id` elementów, `AndersenFamily`które zawierają właściwość, której wartością jest . Wyklucza dowolny element, który nie `id` ma właściwości lub którego `AndersenFamily`wartość nie jest zgodna .

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

W poprzednim przykładzie pokazano proste zapytanie dotyczące równości. Interfejs API SQL obsługuje również różne [wyrażenia skalarne.](sql-query-scalar-expressions.md) Najczęściej używane są wyrażenia binarne i jednoargumentowe. Odwołania do właściwości z obiektu JSON źródła są również prawidłowymi wyrażeniami.

Można użyć następujących obsługiwanych operatorów binarnych:  

|**Typ operatora**  | **Wartości** |
|---------|---------|
|Arytmetyczny | +,-,*,/,% |
|Bitowy    | \|, &, ^, <<, >>, >>> (wypełnienie zerami, przesunięcie w prawo) |
|Logiczny    | AND, OR, NOT      |
|Porównanie | =, !=, &lt;, &gt;, &lt;=, &gt;=, <> |
|Ciąg     |  \|\| (łączenie) |

Następujące kwerendy używają operatorów binarnych:

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

Można również użyć operatorów ujednolimię +,-, ~, i NOT w kwerendach, jak pokazano w poniższych przykładach:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5
```

Można również użyć odwołań do właściwości w kwerendach. Na przykład `SELECT * FROM Families f WHERE f.isRegistered` zwraca element JSON zawierający `isRegistered` właściwość `true`o wartości równej . Każda inna wartość, `false` `null`taka `Undefined` `<number>`jak `<string>` `<object>`, `<array>`, , , , lub , wyklucza element z wyniku.

## <a name="next-steps"></a>Następne kroki

- [Wprowadzenie](sql-query-getting-started.md)
- [Słowo kluczowe IN](sql-query-keywords.md#in)
- [Klauzula OD](sql-query-from.md)