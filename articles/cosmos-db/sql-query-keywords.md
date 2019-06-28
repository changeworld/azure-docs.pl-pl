---
title: Słów kluczowych języka SQL dla usługi Azure Cosmos DB
description: Informacje na temat słów kluczowych języka SQL dla usługi Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: mjbrown
ms.openlocfilehash: c9024f120e0a55162a1f6dba0cd9cbda97f5eebc
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342679"
---
# <a name="keywords-in-azure-cosmos-db"></a>Słowa kluczowe w usłudze Azure Cosmos DB
Ten artykuł szczegółowo opisuje słów kluczowych, które mogą być używane w zapytaniach SQL usługi Azure Cosmos DB.

## <a name="between"></a>MIĘDZY

Jak ANSI SQL można użyć BETWEEN — słowo kluczowe wyrażenia zapytań dotyczących zakresów ciąg lub wartości liczbowych. Na przykład następujące zapytanie zwraca wszystkie elementy, w których klasa pierwszy element podrzędny jest 1-5, włącznie.

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

W odróżnieniu od w ANSI SQL umożliwia także klauzuli BETWEEN w klauzuli FROM, jak w poniższym przykładzie.

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

W interfejsie API SQL, w przeciwieństwie do ANSI SQL można wyrazić zakres zapytań dotyczących właściwości mieszane typy. Na przykład `grade` mogą być podobne do numeru `5` niektóre elementy i parametry, takie jak `grade4` w innych. W takich sytuacjach jak JavaScript, wynikiem porównania między dwoma różnymi typami `Undefined`, dzięki czemu element jest pomijany.

> [!TIP]
> Celu skrócenia czasu wykonywania zapytania należy utworzyć zasady indeksowania, korzystającej z typu indeks zakresu względem dowolnej właściwości liczbowych lub ścieżki, które filtruje klauzuli BETWEEN.

## <a name="distinct"></a>DISTINCT

Słowo kluczowe DISTINCT pozwala wyeliminować duplikaty w projekcji zapytań.

```sql
SELECT DISTINCT VALUE f.lastName
FROM Families f
```

W tym przykładzie zapytanie projektów wartości dla każdego nazwisko.

Wyniki są:

```json
[
    "Andersen"
]
```

Można również projektu następującą liczbę unikatowych obiektów. W tym przypadku pola Nazwisko nie istnieje w jednej z dwóch dokumentów, aby zapytanie zwraca pustego obiektu.

```sql
SELECT DISTINCT f.lastName
FROM Families f
```

Wyniki są:

```json
[
    {
        "lastName": "Andersen"
    },
    {}
]
```

Można także DISTINCT w projekcji w podzapytaniu:

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

To zapytanie projektów tablicę, która zawiera givenName każdego elementu podrzędnego, z której zostały usunięte duplikaty. Ta tablica ma alias ChildNames i pokazane zapytanie zewnętrzne.

Wyniki są:

```json
[
    {
        "id": "AndersenFamily",
        "ChildNames": []
    },
    {
        "id": "WakefieldFamily",
        "ChildNames": [
            "Jesse",
            "Lisa"
        ]
    }
]
```
## <a name="in"></a> INDIE

Użyj słowa kluczowego w celu sprawdzenia, czy określona wartość pasuje do dowolnej wartości na liście. Na przykład, następujące zapytanie zwraca wszystkie rodziny elementy gdzie `id` jest `WakefieldFamily` lub `AndersenFamily`.

```sql
    SELECT *
    FROM Families
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

Poniższy przykład zwraca wszystkie elementy, których stan to dowolnego z określonymi wartościami:

```sql
    SELECT *
    FROM Families
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

Interfejs API SQL udostępnia obsługę [Iterowanie przez tablice JSON](sql-query-object-array.md#Iteration), z nową konstrukcję dodane za pośrednictwem in — słowo kluczowe w źródle FROM. 

## <a name="top"></a>TOP

GÓRNY — słowo kluczowe zwraca pierwszy `N` liczba wyników zapytania w kolejności niezdefiniowane. Najlepszym rozwiązaniem, jak za pomocą GÓRNEJ klauzuli ORDER BY ograniczyć wyniki do pierwszego `N` liczba uporządkowane wartości. Połączenie tych dwóch klauzul jest jedynym sposobem, aby przewidywalnie wskazują wiersze, które wpływa na GÓRNYM.

Korzystać z GÓRNEGO, z wartością stałą, jak w poniższym przykładzie, lub z wartością zmiennej za pomocą sparametryzowanych zapytań.

```sql
    SELECT TOP 1 *
    FROM Families f
```

Wyniki są:

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

## <a name="next-steps"></a>Kolejne kroki

- [Rozpoczęcie pracy](sql-query-getting-started.md)
- [Sprzężenia](sql-query-join.md)
- [Zapytania podrzędne](sql-query-subquery.md)