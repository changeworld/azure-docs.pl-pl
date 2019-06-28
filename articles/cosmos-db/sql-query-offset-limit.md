---
title: Klauzula ograniczenia PRZESUNIĘCIA w usłudze Azure Cosmos DB
description: Dowiedz się więcej o klauzuli przesunięcie LIMIT usługi Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 60ac28c80e9f7cc72f4d6005c12cb5f68671341e
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342522"
---
# <a name="offset-limit-clause"></a>Klauzula ograniczenia PRZESUNIĘCIA

Klauzula przesunięcie LIMIT jest opcjonalna klauzula do pominięcia, a następnie wykonać pewnej liczby wartości z zapytania. Liczba przesunięcie i licznik limitu są wymagane w klauzuli ograniczenia PRZESUNIĘCIA.

Stosowania limitu przesunięcie w połączeniu z klauzulą ORDER BY zestaw wyników jest generowany, wykonując Pomiń i podejmij uporządkowane wartości. Jeśli jest używany nie klauzuli ORDER BY, spowodują deterministyczne kolejność wartości.

## <a name="syntax"></a>Składnia
  
```sql  
OFFSET <offset_amount> LIMIT <limit_amount>
```  
  
## <a name="arguments"></a>Argumenty

- `<offset_amount>`

   Określa liczbę całkowitą liczbę elementów, które należy pominąć wyników zapytania.

- `<limit_amount>`
  
   Określa liczbę całkowitą elementy zawierające wyniki zapytania

## <a name="remarks"></a>Uwagi
  
  Liczba przesunięcie i licznik limitu są wymagane w klauzuli ograniczenia PRZESUNIĘCIA. Jeśli jest to opcjonalne `ORDER BY` klauzula jest używana, zestaw wyników jest generowany, wykonując pomijania za pośrednictwem uporządkowane wartości. W przeciwnym razie zapytanie zwróci wartości ustalonej kolejności. Obecnie ta klauzula jest obsługiwana w przypadku kwerend w tylko jednej partycji, zapytań między partycjami jeszcze go nie obsługują.

## <a name="examples"></a>Przykłady

Na przykład w tym miejscu jest zapytanie, które zawierają pierwszej wartości i zwraca wartość drugiego (według nazwy miasta rezydentnego):

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
    OFFSET 1 LIMIT 1
```

Wyniki są:

```json
    [
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

Oto zapytanie, które zawierają pierwszej wartości i zwraca wartość drugiego (bez kolejności):

```sql
   SELECT f.id, f.address.city
    FROM Families f
    OFFSET 1 LIMIT 1
```

Wyniki są:

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "Seattle"
      }
    ]
```

## <a name="next-steps"></a>Kolejne kroki

- [Rozpoczęcie pracy](sql-query-getting-started.md)
- [Klauzula SELECT](sql-query-select.md)
- [Klauzula ORDER BY](sql-query-order-by.md)
