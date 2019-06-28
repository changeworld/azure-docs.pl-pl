---
title: Klauzula ORDER BY w usłudze Azure Cosmos DB
description: Dowiedz się więcej o SQL ORDER BY — klauzula dla usługi Azure Cosmos DB. Użyj programu SQL jako języka zapytań usługi Azure Cosmos DB w formacie JSON.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: d0a1ed33d5848c3ed8d5f83af8b320d77fe0dc65
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342678"
---
# <a name="order-by-clause"></a>Klauzula ORDER BY

Opcjonalna klauzula ORDER BY określa kolejność sortowania wyników zwróconych przez zapytanie.

## <a name="syntax"></a>Składnia
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= {<scalar_expression> [ASC | DESC]} [ ,...n ]  
```  

## <a name="arguments"></a>Argumenty
  
- `<sort_specification>`  
  
   Określa właściwość lub wyrażenie do sortowania zestawu wyników zapytania. Kolumna sortowania może być określona jako alias nazwy lub właściwości.  
  
   Można określić wiele właściwości. Nazwy właściwości muszą być unikatowe. Sekwencja właściwości sortowania w klauzuli ORDER BY określa organizacji zestawu posortowanego wyników. Oznacza to zestaw wyników jest posortowana według pierwszą właściwością, a następnie tego uporządkowana lista jest posortowana według właściwości drugiej i tak dalej.  
  
   Nazwy właściwości, do których odwołuje się w klauzuli ORDER BY muszą odpowiadać właściwością na liście wyboru lub do właściwości zdefiniowane w kolekcji określonej w klauzuli FROM bez żadnych niejednoznaczności.  
  
- `<sort_expression>`  
  
   Określa właściwości lub wyrażenia do sortowania zestawu wyników zapytania.  
  
- `<scalar_expression>`  
  
   Zobacz [wyrażenia skalarne](sql-query-scalar-expressions.md) sekcji, aby uzyskać szczegółowe informacje.  
  
- `ASC | DESC`  
  
   Określa, że wartości w określonej kolumnie powinny być sortowane w kolejności rosnącej lub malejącej. ASC sortuje od najniższej do najwyższej wartości. DESC sortuje od najwyższej do najniższej wartości. ASC jest domyślny porządek sortowania. Wartości null są traktowane jako najniższe możliwe wartości.  
  
## <a name="remarks"></a>Uwagi  
  
   W klauzuli ORDER BY wymaga, że zasady indeksowania obejmują indeksu dla pól posortowana. W czasie wykonywania zapytań usługi Azure Cosmos DB obsługuje sortowanie nazwę właściwości, a nie dla właściwości obliczane. Usługa Azure Cosmos DB obsługuje wiele właściwości ORDER BY. Aby można było uruchomić zapytania z wieloma właściwościami klauzuli ORDER BY, należy zdefiniować [indeksu złożonego](index-policy.md#composite-indexes) w polach posortowana.

## <a name="examples"></a>Przykłady

Na przykład w tym miejscu jest zapytanie, które pobiera rodzin rosnąco nazwę miejscowości rezydentnego:

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
```

Wyniki są:

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "NY"
      },
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

Następujące zapytanie pobiera rodziny `id`s w kolejności ich Data utworzenia elementu. Element `creationDate` jest liczbą oznaczającą *czasie uniksowym*, lub czas, który upłynął od 1 stycznia 1970 r. w ciągu kilku sekund.

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.creationDate DESC
```

Wyniki są:

```json
    [
      {
        "id": "WakefieldFamily",
        "creationDate": 1431620462
      },
      {
        "id": "AndersenFamily",
        "creationDate": 1431620472
      }
    ]
```

Ponadto może zamówić łączność obejmującą wiele właściwości. Zapytanie, które porządkuje przez wiele właściwości wymaga [indeksu złożonego](index-policy.md#composite-indexes). Należy wziąć pod uwagę następujące zapytanie:

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.address.city ASC, f.creationDate DESC
```

To zapytanie pobiera rodziny `id` rosnąco nazwę miejscowości. Jeśli wiele elementów ma taką samą nazwę miasta, zapytanie będzie kolejność według `creationDate` w kolejności malejącej.

## <a name="next-steps"></a>Kolejne kroki

- [Rozpoczęcie pracy](sql-query-getting-started.md)
- [Klauzula SELECT](sql-query-select.md)
- [Klauzula ograniczenia PRZESUNIĘCIA](sql-query-offset-limit.md)
