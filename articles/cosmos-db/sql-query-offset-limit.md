---
title: Klauzula limitu przesunięcia w Azure Cosmos DB
description: Dowiedz się więcej na temat klauzuli LIMIT przesunięcia dla Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 7aae56783f83f13b50321c88d69f07d910e589dd
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72326874"
---
# <a name="offset-limit-clause-in-azure-cosmos-db"></a>Klauzula limitu przesunięcia w Azure Cosmos DB

Klauzula LIMIT przesunięcia jest opcjonalną klauzulą, aby pominąć, a następnie pobrać pewną liczbę wartości z zapytania. W klauzuli LIMIT przesunięcia są wymagane liczby przesunięcia i liczba LIMITów.

Gdy LIMIT przesunięcia jest używany w połączeniu z klauzulą ORDER BY, zestaw wyników jest tworzony przez wykonanie pominięcia i wykonania uporządkowanych wartości. Jeśli nie jest używana klauzula ORDER BY, spowoduje to deterministyczną kolejność wartości.

## <a name="syntax"></a>Składnia
  
```sql  
OFFSET <offset_amount> LIMIT <limit_amount>
```  
  
## <a name="arguments"></a>Argumenty

- `<offset_amount>`

   Określa liczbę całkowitą elementów, które mają zostać pominięte w wynikach zapytania.

- `<limit_amount>`
  
   Określa liczbę całkowitą elementów, które powinny zawierać wyniki zapytania

## <a name="remarks"></a>Uwagi
  
  W klauzuli LIMIT przesunięcia są wymagane zarówno licznik przesunięcia, jak i liczba LIMITów. Jeśli zostanie użyta opcjonalna klauzula `ORDER BY`, zestaw wyników zostanie utworzony przez przeskoczenie na uporządkowane wartości. W przeciwnym razie zapytanie zwróci ustaloną kolejność wartości. Obecnie ta klauzula jest obsługiwana w przypadku zapytań w ramach pojedynczej partycji. zapytania między partycjami jeszcze nie obsługują tego programu.

## <a name="examples"></a>Przykłady

Na przykład jest to zapytanie, które pomija pierwszą wartość i zwraca drugą wartość (w kolejności nazwy miasta rezydenta):

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
    OFFSET 1 LIMIT 1
```

Wyniki są następujące:

```json
    [
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

Oto zapytanie, które pomija pierwszą wartość i zwraca drugą wartość (bez porządkowania):

```sql
   SELECT f.id, f.address.city
    FROM Families f
    OFFSET 1 LIMIT 1
```

Wyniki są następujące:

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "Seattle"
      }
    ]
```

## <a name="next-steps"></a>Następne kroki

- [Rozpoczęcie pracy](sql-query-getting-started.md)
- [SELECT — klauzula](sql-query-select.md)
- [Klauzula ORDER BY](sql-query-order-by.md)
