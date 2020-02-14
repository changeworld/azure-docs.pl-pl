---
title: Klauzula ORDER BY w Azure Cosmos DB
description: Dowiedz się więcej o klauzuli ORDER BY języka SQL dla Azure Cosmos DB. Użyj programu SQL jako Azure Cosmos DB języka zapytań JSON.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: tisande
ms.openlocfilehash: b88184be39a41ec42f8fb304a7511073f645f1cb
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77188730"
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
  
   Aby uzyskać szczegółowe informacje, zobacz sekcję [wyrażenia skalarne](sql-query-scalar-expressions.md) .  
  
- `ASC | DESC`  
  
   Określa, że wartości w określonej kolumnie powinny być sortowane w kolejności rosnącej lub malejącej. ASC sortuje od najniższej do najwyższej wartości. DESC sortuje od najwyższej do najniższej wartości. ASC jest domyślny porządek sortowania. Wartości null są traktowane jako najniższe możliwe wartości.  
  
## <a name="remarks"></a>Uwagi  
  
   Klauzula `ORDER BY` wymaga, aby zasady indeksowania zawierały indeks dla sortowanych pól. Środowisko uruchomieniowe zapytań Azure Cosmos DB obsługuje sortowanie względem nazwy właściwości, a nie do właściwości obliczanych. Azure Cosmos DB obsługuje wiele właściwości `ORDER BY`. Aby uruchomić zapytanie z wieloma właściwościami ORDER BY, należy zdefiniować [indeks złożony](index-policy.md#composite-indexes) dla sortowanych pól.

> [!Note]
> Jeśli sortowane właściwości mogą być niezdefiniowane w niektórych dokumentach i chcesz je pobrać w kwerendzie ORDER BY, musisz jawnie dołączyć tę ścieżkę w indeksie. Domyślne zasady indeksowania nie będą zezwalać na pobieranie dokumentów, w których właściwość Sort jest niezdefiniowana. [Przejrzyj przykładowe zapytania dotyczące dokumentów zawierających brakujące pola](#documents-with-missing-fields).

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

Ponadto możesz zamówić według wielu właściwości. Zapytanie, które porządkuje według wielu właściwości, wymaga [indeksu złożonego](index-policy.md#composite-indexes). Rozważ następujące zapytanie:

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.address.city ASC, f.creationDate DESC
```

To zapytanie pobiera rodzinę `id` w kolejności rosnącej według nazwy miasta. Jeśli wiele elementów ma taką samą nazwę miasta, zapytanie zostanie uporządkowane według `creationDate` w kolejności malejącej.

## <a name="documents-with-missing-fields"></a>Dokumenty z brakującymi polami

Zapytania o `ORDER BY`, które są uruchamiane w odniesieniu do kontenerów z domyślną zasadą indeksowania, nie będą zwracać dokumentów, w których właściwość Sort jest niezdefiniowana. Jeśli chcesz uwzględnić dokumenty, w których właściwość Sort jest niezdefiniowana, należy jawnie dołączyć tę właściwość do zasad indeksowania.

Na przykład poniżej znajduje się kontener z zasadami indeksowania, które nie zawierają jawnie żadnych ścieżek oprócz `"/*"`:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

W przypadku uruchomienia zapytania zawierającego `lastName` w klauzuli `Order By` wyniki będą zawierać tylko dokumenty, które mają zdefiniowaną Właściwość `lastName`. Nie zdefiniowano jawnie dołączonej ścieżki dla `lastName`, więc wszystkie dokumenty bez `lastName` nie będą wyświetlane w wynikach zapytania.

Oto zapytanie, które sortuje według `lastName` w dwóch dokumentach, z których jeden nie ma zdefiniowanego `lastName`:

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName
```

Wyniki obejmują tylko dokument, który ma zdefiniowane `lastName`:

```json
    [
        {
            "id": "AndersenFamily",
            "lastName": "Andersen"
        }
    ]
```

W przypadku aktualizowania zasad indeksowania kontenera w celu jawnego uwzględnienia ścieżki do `lastName`zostaną uwzględnione dokumenty z niezdefiniowaną właściwością sortowania w wynikach zapytania. Należy jawnie zdefiniować ścieżkę, która ma prowadzić do tej wartości skalarnej (a nie poza nią). Należy użyć znaku `?` w definicji ścieżki w ramach zasad indeksowania, aby upewnić się, że jawnie indeksuje Właściwość `lastName` i nie ma dodatkowych ścieżek zagnieżdżonych poza nią.

Oto przykładowe zasady indeksowania, które umożliwiają dokumentom z niezdefiniowanym `lastName` wyświetlane w wynikach zapytania:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/lastName/?"
        },
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

Jeśli ponownie uruchomisz to samo zapytanie, dokumenty, których brakuje `lastName` są wyświetlane jako pierwsze w wynikach zapytania:

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName
```

Wyniki są następujące:

```json
[
    {
        "id": "WakefieldFamily"
    },
    {
        "id": "AndersenFamily",
        "lastName": "Andersen"
    }
]
```

W przypadku zmodyfikowania porządku sortowania do `DESC`dokumenty, których brakuje, `lastName` wyświetlane jako ostatnie w wynikach zapytania:

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName DESC
```

Wyniki są następujące:

```json
[
    {
        "id": "AndersenFamily",
        "lastName": "Andersen"
    },
    {
        "id": "WakefieldFamily"
    }
]
```

## <a name="next-steps"></a>Następne kroki

- [Rozpoczęcie pracy](sql-query-getting-started.md)
- [Zasady indeksowania w usłudze Azure Cosmos DB](index-policy.md)
- [Klauzula limitu przesunięcia](sql-query-offset-limit.md)
