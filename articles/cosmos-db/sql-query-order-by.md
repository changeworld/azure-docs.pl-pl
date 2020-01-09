---
title: Klauzula ORDER BY w Azure Cosmos DB
description: Dowiedz się więcej o klauzuli ORDER BY języka SQL dla Azure Cosmos DB. Użyj programu SQL jako Azure Cosmos DB języka zapytań JSON.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 5cae2bdd7d1f2f26e626c81ea95d2cee3cc8ae13
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75444789"
---
# <a name="order-by-clause-in-azure-cosmos-db"></a>Klauzula ORDER BY w Azure Cosmos DB

Opcjonalna klauzula ORDER BY określa kolejność sortowania dla wyników zwracanych przez zapytanie.

## <a name="syntax"></a>Składnia
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= {<scalar_expression> [ASC | DESC]} [ ,...n ]  
```  

## <a name="arguments"></a>Argumenty
  
- `<sort_specification>`  
  
   Określa właściwość lub wyrażenie do sortowania zestawu wyników zapytania. Kolumnę sortowania można określić jako nazwę lub alias właściwości.  
  
   Można określić wiele właściwości. Nazwy właściwości muszą być unikatowe. Sekwencja właściwości sortowania w klauzuli ORDER BY definiuje organizację zestawu wyników sortowania. Oznacza to zestaw wyników jest posortowana według pierwszą właściwością, a następnie tego uporządkowana lista jest posortowana według właściwości drugiej i tak dalej.  
  
   Nazwy właściwości, do których odwołuje się klauzula ORDER BY, muszą odpowiadać właściwości na liście wyboru lub do właściwości zdefiniowanej w kolekcji określonej w klauzuli FROM bez żadnych niejasności.  
  
- `<sort_expression>`  
  
   Określa jedną lub więcej właściwości lub wyrażeń, dla których ma zostać posortowany zestaw wyników zapytania.  
  
- `<scalar_expression>`  
  
   Zobacz [wyrażenia skalarne](sql-query-scalar-expressions.md) sekcji, aby uzyskać szczegółowe informacje.  
  
- `ASC | DESC`  
  
   Określa, że wartości w określonej kolumnie powinny być sortowane w kolejności rosnącej lub malejącej. ASC sortuje od najniższej do najwyższej wartości. DESC sortuje od najwyższej do najniższej wartości. ASC jest domyślny porządek sortowania. Wartości null są traktowane jako najniższe możliwe wartości.  
  
## <a name="remarks"></a>Uwagi  
  
   Klauzula ORDER BY wymaga, aby zasady indeksowania zawierały indeks dla sortowanych pól. Środowisko uruchomieniowe zapytań Azure Cosmos DB obsługuje sortowanie względem nazwy właściwości, a nie do właściwości obliczanych. Azure Cosmos DB obsługuje wiele właściwości ORDER BY. Aby uruchomić zapytanie z wieloma właściwościami ORDER BY, należy zdefiniować [indeks złożony](index-policy.md#composite-indexes) dla sortowanych pól.
   
> [!Note] 
> W przypadku korzystania z zestawu .NET SDK 3.4.0 lub nowszego, jeśli sortowane właściwości dla niektórych dokumentów mogą być niezdefiniowane, należy jawnie utworzyć indeks dla tych właściwości. Domyślne zasady indeksowania nie będą zezwalać na pobieranie dokumentów, w których nie jest zdefiniowana właściwość Sort.

## <a name="examples"></a>Przykłady

Załóżmy na przykład, że jest to zapytanie, które pobiera rodziny w kolejności rosnącej nazwy miejscowości rezydentnej:

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
```

Wyniki są następujące:

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

Poniższe zapytanie pobiera rodzinę `id`s w kolejności ich tworzenia elementów. Element `creationDate` to liczba reprezentująca *czas epoki*lub czas, który upłynął od 1 stycznia 1970 w sekundach.

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.creationDate DESC
```

Wyniki są następujące:

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

Ponadto możesz zamówić według wielu właściwości. Zapytanie, które porządkuje według wielu właściwości, wymaga [indeksu złożonego](index-policy.md#composite-indexes). Rozpatrzmy następujące zapytanie:

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.address.city ASC, f.creationDate DESC
```

To zapytanie pobiera rodzinę `id` w kolejności rosnącej według nazwy miasta. Jeśli wiele elementów ma taką samą nazwę miasta, zapytanie zostanie uporządkowane według `creationDate` w kolejności malejącej.

## <a name="next-steps"></a>Następne kroki

- [Rozpoczęcie pracy](sql-query-getting-started.md)
- [Klauzula SELECT](sql-query-select.md)
- [Klauzula limitu przesunięcia](sql-query-offset-limit.md)
