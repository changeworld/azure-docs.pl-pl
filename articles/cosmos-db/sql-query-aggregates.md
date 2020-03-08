---
title: Funkcje agregujące w Azure Cosmos DB
description: Dowiedz się więcej na temat składni funkcji agregującej SQL, typów funkcji agregujących obsługiwanych przez Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: tisande
ms.openlocfilehash: df9700dd51c8915ff28c34cf0a29c2f5e48baa44
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2020
ms.locfileid: "78897824"
---
# <a name="aggregate-functions-in-azure-cosmos-db"></a>Funkcje agregujące w Azure Cosmos DB

Funkcje agregujące wykonują obliczenia na zestawie wartości w klauzuli SELECT i zwracają pojedynczą wartość. Na przykład następujące zapytanie zwraca liczbę elementów w kontenerze `Families`:

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

Możesz również zwrócić tylko wartość skalarną agregacji za pomocą słowa kluczowego VALUE. Na przykład poniższe zapytanie zwraca liczbę wartości jako pojedynczą liczbę:

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

Wyniki są następujące:

```json
    [ 2 ]
```

Można również łączyć agregacje z filtrami. Na przykład następujące zapytanie zwraca liczbę elementów ze stanem adresu `WA`.

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

Interfejs API SQL obsługuje następujące funkcje agregujące. Suma i średni czas działania na wartościach liczbowych oraz liczba, minimum i maksimum pracy dla liczb, ciągów, wartości logicznych i wartości null.

| Funkcja | Opis |
|-------|-------------|
| COUNT | Zwraca liczbę elementów w wyrażeniu. |
| SUM   | Zwraca sumę wszystkich wartości w wyrażeniu. |
| MIN   | Zwraca minimalną wartość w wyrażeniu. |
| MAX   | Zwraca maksymalną wartość w wyrażeniu. |
| AVG   | Zwraca średnią wartości w wyrażeniu. |

Można również agregować wyniki iteracji tablicy.

> [!NOTE]
> W Eksplorator danych Azure Portal zapytania agregacji mogą agregować częściowe wyniki tylko przez jedną stronę zapytania. Zestaw SDK tworzy pojedynczą wartość skumulowaną na wszystkich stronach. Aby wykonywać zapytania agregacji przy użyciu kodu, należy użyć zestawu .NET SDK 1.12.0, zestaw .NET Core SDK 1.1.0 lub zestawu Java SDK 1.9.5 lub nowszego.

## <a name="next-steps"></a>Następne kroki

- [Wprowadzenie do Azure Cosmos DB](introduction.md)
- [Funkcje systemowe](sql-query-system-functions.md)
- [Funkcje zdefiniowane przez użytkownika](sql-query-udfs.md)