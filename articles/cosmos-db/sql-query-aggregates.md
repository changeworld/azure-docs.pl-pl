---
title: Agreguj funkcje w usłudze Azure Cosmos DB
description: Dowiedz się więcej o składni funkcji agregacji SQL, typach funkcji agregujących obsługiwanych przez usługę Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: tisande
ms.openlocfilehash: 24acd1e9c13320244ff4c27abd13abeda6f70b2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79464465"
---
# <a name="aggregate-functions-in-azure-cosmos-db"></a>Agreguj funkcje w usłudze Azure Cosmos DB

Funkcje agregowane wykonują obliczenia na `SELECT` zestawie wartości w klauzuli i zwracają pojedynczą wartość. Na przykład następująca kwerenda zwraca liczbę `Families` elementów w kontenerze:

## <a name="examples"></a>Przykłady

```sql
    SELECT COUNT(1)
    FROM Families f
```

Wyniki są następujące:

```json
    [{
        "$1": 2
    }]
```

Można również zwrócić tylko wartość skalarną agregacji przy użyciu słowa kluczowego WARTOŚĆ. Na przykład poniższe zapytanie zwraca liczbę wartości jako pojedynczą liczbę:

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

Wyniki są następujące:

```json
    [ 2 ]
```

Można również łączyć agregacje z filtrami. Na przykład następująca kwerenda zwraca liczbę elementów `WA`o stanie adresu .

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

Wyniki są następujące:

```json
    [ 1 ]
```

## <a name="types-of-aggregate-functions"></a>Typy funkcji agregujących

Interfejs API SQL obsługuje następujące funkcje agregacji. `SUM`i `AVG` działać na wartościach `COUNT` `MIN`liczbowych `MAX` i , i pracować na liczbach, ciągach, wartościach logicznych i wartościach null.

| Funkcja | Opis |
|-------|-------------|
| COUNT | Zwraca liczbę elementów w wyrażeniu. |
| SUM   | Zwraca sumę wszystkich wartości w wyrażeniu. |
| MIN   | Zwraca minimalną wartość w wyrażeniu. |
| MAX   | Zwraca maksymalną wartość w wyrażeniu. |
| AVG   | Zwraca średnią wartości w wyrażeniu. |

Można również agregować zaokiwki w wynikach iteracji tablicy.

> [!NOTE]
> W Eksploratorze danych witryny Azure portal kwerendy agregacji mogą agregować częściowe wyniki tylko na jednej stronie kwerendy. SDK tworzy jedną skumulowaną wartość na wszystkich stronach. Aby wykonywać kwerendy agregacji przy użyciu kodu, należy .NET SDK 1.12.0, .NET Core SDK 1.1.0 lub Java SDK 1.9.5 lub powyżej.

## <a name="remarks"></a>Uwagi

Te zagregowane funkcje systemowe będą korzystać z [indeksu zakresu](index-policy.md#includeexclude-strategy). Jeśli oczekujesz, `COUNT`że `SUM` `MIN`wykonasz , , `MAX`, lub `AVG` na właściwości, należy [uwzględnić odpowiednią ścieżkę w zasadach indeksowania](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Następne kroki

- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
- [Funkcje systemowe](sql-query-system-functions.md)
- [Funkcje zdefiniowane przez użytkownika](sql-query-udfs.md)