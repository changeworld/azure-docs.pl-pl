---
title: Funkcje agregujące w usłudze Azure Cosmos DB
description: Dowiedz się więcej o składni funkcji agregującej SQL dla usługi Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: a6937e9e811ea8e44eda6f2bcb5d2c7d78db4934
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342941"
---
# <a name="aggregate-functions-in-azure-cosmos-db"></a>Funkcje agregujące w usłudze Azure Cosmos DB

Funkcje agregujące wykonywanie obliczeń na zestaw wartości w klauzuli SELECT i zwracać pojedynczą wartość. Na przykład, następujące zapytanie zwraca liczbę elementów w obrębie `Families` kontenera:

## <a name="examples"></a>Przykłady

```sql
    SELECT COUNT(1)
    FROM Families f
```

Wyniki są:

```json
    [{
        "$1": 2
    }]
```

Może również zwracać wartość skalarna agregacji za pomocą słowa kluczowego wartość. Na przykład poniższe zapytanie zwraca liczbę wartości jako pojedynczą liczbę:

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

Wyniki są:

```json
    [ 2 ]
```

Można także połączyć agregacji za pomocą filtrów. Na przykład, następujące zapytanie zwraca liczbę elementów, stan adresu `WA`.

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

Wyniki są:

```json
    [ 1 ]
```

## <a name="types-of-aggregate-functions"></a>Typy funkcji agregującej

Interfejs API SQL obsługuje następujące funkcje agregujące. Suma i średnia działają na wartości liczbowe i COUNT, MIN i MAX pracować na liczby, ciągi, wartości logicznych i wartości null.

| Funkcja | Opis |
|-------|-------------|
| COUNT | Zwraca liczbę elementów w wyrażeniu. |
| SUM   | Zwraca sumę wszystkich wartości w wyrażeniu. |
| MIN   | Zwraca minimalną wartość w wyrażeniu. |
| MAX   | Zwraca maksymalną wartość w wyrażeniu. |
| AVG   | Zwraca średnią wartości w wyrażeniu. |

Możesz także agregować wynikami iterację tablicy.

> [!NOTE]
> W Eksploratorze danych w witrynie Azure portal zapytania agregacji mogą zostać zagregowane wyniki częściowe za pośrednictwem strony tylko jedno zapytanie. Zestaw SDK tworzy pojedynczej wartości zbiorczej na wszystkich stronach. Do wykonywania zapytań agregacji, przy użyciu kodu, potrzebny jest zestaw SDK platformy .NET 1.12.0, zestaw SDK platformy .NET Core 1.1.0 lub zestawu Java SDK 1.9.5 lub nowszej.

## <a name="next-steps"></a>Kolejne kroki

- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
- [Funkcje systemowe](sql-query-system-functions.md)
- [Funkcje zdefiniowane przez użytkownika](sql-query-udfs.md)