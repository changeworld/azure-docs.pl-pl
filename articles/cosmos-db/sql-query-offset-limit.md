---
title: Klauzula OFFSET LIMIT w usłudze Azure Cosmos DB
description: Dowiedz się, jak pominąć i wziąć niektóre określone wartości podczas wykonywania zapytań w usłudze Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 3d23676885323e370cee1e9cc9e98c7128faf2e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76771570"
---
# <a name="offset-limit-clause-in-azure-cosmos-db"></a>Klauzula OFFSET LIMIT w usłudze Azure Cosmos DB

Klauzula OFFSET LIMIT jest opcjonalną klauzulą do pominięcia, a następnie przyjmuje pewną liczbę wartości z kwerendy. Licznik PRZESUNIĘCIA i LICZNIK LIMIT są wymagane w klauzuli OFFSET LIMIT.

Gdy OFFSET LIMIT jest używany w połączeniu z klauzulą ORDER BY, zestaw wyników jest produkowany przez wykonanie pomijania i przejęcie uporządkowanych wartości. Jeśli nie jest używana klauzula ORDER BY, spowoduje to deterministyczną kolejność wartości.

## <a name="syntax"></a>Składnia
  
```sql  
OFFSET <offset_amount> LIMIT <limit_amount>
```  
  
## <a name="arguments"></a>Argumenty

- `<offset_amount>`

   Określa liczbę całkowitą elementów, które wyniki kwerendy powinny pominąć.

- `<limit_amount>`
  
   Określa liczbę całkowitą elementów, które powinny zawierać wyniki kwerendy

## <a name="remarks"></a>Uwagi
  
  Zarówno `OFFSET` liczba, `LIMIT` jak i liczba `OFFSET LIMIT` są wymagane w klauzuli. Jeśli używana `ORDER BY` jest klauzula opcjonalna, zestaw wyników jest produkowany przez wykonanie pomijania zamówionych wartości. W przeciwnym razie kwerenda zwróci stałą kolejność wartości.

  Opłata RU kwerendy `OFFSET LIMIT` z wzrośnie wraz ze wzrostem liczby terminów jest przesunięcie. W przypadku kwerend, które mają wiele stron wyników, zazwyczaj zaleca się przy użyciu tokenów kontynuacji. Tokeny kontynuacji są "zakładką" dla miejsca, w którym kwerenda może później wznowić. Jeśli używasz `OFFSET LIMIT`, nie ma "zakładki". Jeśli chcesz zwrócić następną stronę kwerendy, należy rozpocząć od początku.
  
  Należy użyć `OFFSET LIMIT` w przypadkach, gdy chcesz całkowicie pominąć dokumenty i zapisać zasoby klienta. Na przykład należy `OFFSET LIMIT` użyć, jeśli chcesz przejść do wyniku kwerendy 1000-sze i nie trzeba wyświetlać wyniki od 1 do 999. W wewnętrznej bazy `OFFSET LIMIT` danych nadal ładuje każdy dokument, łącznie z tymi, które są pomijane. Zaletą wydajności jest oszczędność zasobów klienta, unikając przetwarzania dokumentów, które nie są potrzebne.

## <a name="examples"></a>Przykłady

Na przykład oto kwerenda, która pomija pierwszą wartość i zwraca drugą wartość (w kolejności nazwy miasta rezydenta):

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

Oto kwerenda, która pomija pierwszą wartość i zwraca drugą wartość (bez zamawiania):

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

- [Wprowadzenie](sql-query-getting-started.md)
- [Klauzula SELECT](sql-query-select.md)
- [KLAUZULA ORDER BY](sql-query-order-by.md)
