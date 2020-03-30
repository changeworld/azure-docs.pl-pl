---
title: Słowa kluczowe SQL dla usługi Azure Cosmos DB
description: Dowiedz się więcej o słowach kluczowych SQL dla usługi Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: mjbrown
ms.openlocfilehash: f2da2695ec20eac9dd2636104d3314427e60d541
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79498537"
---
# <a name="keywords-in-azure-cosmos-db"></a>Słowa kluczowe w usłudze Azure Cosmos DB

W tym artykule opisano słowa kluczowe, które mogą być używane w kwerendach SQL usługi Azure Cosmos DB.

## <a name="between"></a>BETWEEN

Za pomocą `BETWEEN` słowa kluczowego można wyrazić zapytania względem zakresów wartości ciągów lub liczbowych. Na przykład następująca kwerenda zwraca wszystkie elementy, w których ocena pierwszego dziecka jest 1-5, włącznie.

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

Można również użyć `BETWEEN` słowa `SELECT` kluczowego w klauzuli, jak w poniższym przykładzie.

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

W interfejsie API SQL, w przeciwieństwie do ANSI SQL, można wyrazić zapytania zakresowe względem właściwości typów mieszanych. Na przykład `grade` może być `5` liczba jak w niektórych elementów i ciąg jak `grade4` w innych. W takich przypadkach, podobnie jak w języku JavaScript, `Undefined`porównanie między dwoma różnymi typami powoduje , więc element jest pomijany.

> [!TIP]
> Aby przyspieszyć czas wykonywania kwerendy, należy utworzyć zasadę indeksowania, która używa typu `BETWEEN` indeksu zakresu względem wszystkich właściwości liczbowych lub ścieżek, które filtruje klauzula.

## <a name="distinct"></a>DISTINCT

Słowo `DISTINCT` kluczowe eliminuje duplikaty w projekcji kwerendy.

W tym przykładzie kwerenda projektuje wartości dla każdego nazwiska:

```sql
SELECT DISTINCT VALUE f.lastName
FROM Families f
```

Wyniki są następujące:

```json
[
    "Andersen"
]
```

Można również wyświetlać unikatowe obiekty. W takim przypadku pole lastName nie istnieje w jednym z dwóch dokumentów, więc kwerenda zwraca pusty obiekt.

```sql
SELECT DISTINCT f.lastName
FROM Families f
```

Wyniki są następujące:

```json
[
    {
        "lastName": "Andersen"
    },
    {}
]
```

DISTINCT może być również stosowany w projekcji w podkwerendy:

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

Ta kwerenda projektuje tablicę, która zawiera danename każdego dziecka z duplikatami usunięte. Ta tablica jest aliasowana jako ChildNames i rzutowana w kwerendzie zewnętrznej.

Wyniki są następujące:

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

Zapytania z agregowaną funkcją systemową `DISTINCT` i podkwerendą z nie są obsługiwane. Na przykład następująca kwerenda nie jest obsługiwana:

```sql
SELECT COUNT(1) FROM (SELECT DISTINCT f.lastName FROM f)
```

## <a name="in"></a>IN

Użyj słowa kluczowego IN, aby sprawdzić, czy określona wartość jest zgodna z dowolną wartością na liście. Na przykład następująca kwerenda zwraca `id` wszystkie `WakefieldFamily` `AndersenFamily`elementy rodziny, w których jest lub .

```sql
    SELECT *
    FROM Families
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

Poniższy przykład zwraca wszystkie elementy, w których stan jest dowolną z określonych wartości:

```sql
    SELECT *
    FROM Families
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

Interfejs API SQL zapewnia obsługę [iteracji za pośrednictwem tablic JSON,](sql-query-object-array.md#Iteration)z nową konstrukcją dodaną za pomocą słowa kluczowego in source from.

Jeśli dołączysz klucz partycji do filtru, `IN` zapytanie zostanie automatycznie filtrowane tylko do odpowiednich partycji.

## <a name="top"></a>TOP

Słowo kluczowe TOP `N` zwraca pierwszą liczbę wyników kwerendy w niezdefiniowanej kolejności. Najlepszym rozwiązaniem jest użycie funkcji `ORDER BY` TOP z klauzulą, aby ograniczyć wyniki do pierwszej `N` liczby uporządkowanych wartości. Połączenie tych dwóch klauzul jest jedynym sposobem, aby przewidywalnie wskazać, które wiersze TOP wpływa.

Można użyć TOP ze stałą wartością, jak w poniższym przykładzie lub z wartością zmienną przy użyciu kwerend sparametryzowanych.

```sql
    SELECT TOP 1 *
    FROM Families f
```

Wyniki są następujące:

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

## <a name="next-steps"></a>Następne kroki

- [Wprowadzenie](sql-query-getting-started.md)
- [Łączy](sql-query-join.md)
- [Podkwerend](sql-query-subquery.md)