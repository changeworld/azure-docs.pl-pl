---
title: Klauzula limitu przesunięcia w Azure Cosmos DB
description: Dowiedz się, jak używać klauzuli LIMIT przesunięcia do pomijania i wykonywania niektórych wartości podczas wykonywania zapytań w Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 3d23676885323e370cee1e9cc9e98c7128faf2e0
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2020
ms.locfileid: "76771570"
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
  
  W klauzuli `OFFSET LIMIT` są wymagane zarówno licznik `OFFSET`, jak i liczba `LIMIT`. Jeśli zostanie użyta opcjonalna klauzula `ORDER BY`, zestaw wyników zostanie wytworzony przez przeskoczenie na uporządkowane wartości. W przeciwnym razie zapytanie zwróci ustaloną kolejność wartości.

  Opłata za obiekt RU zapytania z `OFFSET LIMIT` zwiększy się w miarę zwiększania się liczby przesunięć. W przypadku zapytań mających wiele stron wyników zwykle zalecamy użycie tokenów kontynuacji. Tokeny kontynuacji są "zakładkami" dla miejsca, w którym zapytanie może zostać później wznowione. Jeśli używasz `OFFSET LIMIT`, nie ma "zakładki". Jeśli chcesz zwrócić następną stronę zapytania, trzeba zacząć od początku.
  
  Należy używać `OFFSET LIMIT` dla przypadków, gdy chcesz całkowicie pominąć dokumenty i zapisać zasoby klienta. Na przykład należy użyć `OFFSET LIMIT`, jeśli chcesz przejść do wyniku zapytania 1000th i nie ma potrzeby wyświetlania wyników od 1 do 999. W zapleczu `OFFSET LIMIT` nadal ładuje każdy dokument, w tym te, które zostały pominięte. Zalety wydajności są oszczędnością w zasobach klientów, unikając przetwarzania dokumentów, które nie są zbędne.

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
- [Klauzula SELECT](sql-query-select.md)
- [Klauzula ORDER BY](sql-query-order-by.md)
