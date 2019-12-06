---
title: Operatory zapytań SQL dla Azure Cosmos DB
description: Dowiedz się więcej na temat operatorów SQL, takich jak równość, porównanie i operatory logiczne obsługiwane przez Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: f3efe4bee749f0d3132206ca68a33a60f0e16b81
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74870942"
---
# <a name="operators-in-azure-cosmos-db"></a>Operatory w Azure Cosmos DB

W tym artykule szczegółowo opisano różne operatory obsługiwane przez Azure Cosmos DB.

## <a name="equality-and-comparison-operators"></a>Operatory równości i porównania

W poniższej tabeli przedstawiono wynik porównań równości w interfejsie API SQL między dwoma dowolnymi typami JSON.

| **Operator** | **Niezdefiniowane** | **Null** | **Wartość logiczna** | **Liczba** | **Ciąg** | **Obiekt** | **Tablica** |
|---|---|---|---|---|---|---|---|
| **Niezdefiniowane** | Nie zdefiniowano | Nie zdefiniowano | Nie zdefiniowano | Nie zdefiniowano | Nie zdefiniowano | Nie zdefiniowano | Nie zdefiniowano |
| **Null** | Nie zdefiniowano | **OK** | Nie zdefiniowano | Nie zdefiniowano | Nie zdefiniowano | Nie zdefiniowano | Nie zdefiniowano |
| **Wartość logiczna** | Nie zdefiniowano | Nie zdefiniowano | **OK** | Nie zdefiniowano | Nie zdefiniowano | Nie zdefiniowano | Nie zdefiniowano |
| **Liczba** | Nie zdefiniowano | Nie zdefiniowano | Nie zdefiniowano | **OK** | Nie zdefiniowano | Nie zdefiniowano | Nie zdefiniowano |
| **Ciąg** | Nie zdefiniowano | Nie zdefiniowano | Nie zdefiniowano | Nie zdefiniowano | **OK** | Nie zdefiniowano | Nie zdefiniowano |
| **Obiekt** | Nie zdefiniowano | Nie zdefiniowano | Nie zdefiniowano | Nie zdefiniowano | Nie zdefiniowano | **OK** | Nie zdefiniowano |
| **Tablica** | Nie zdefiniowano | Nie zdefiniowano | Nie zdefiniowano | Nie zdefiniowano | Nie zdefiniowano | Nie zdefiniowano | **OK** |

W przypadku operatorów porównania, takich jak `>`, `>=`, `!=`, `<`i `<=`, porównanie między typami lub między dwoma obiektami lub tablicami produkuje `Undefined`.  

Jeśli wynik wyrażenia skalarnego jest `Undefined`, element nie jest uwzględniony w wyniku, ponieważ `Undefined` nie jest równe `true`.

## <a name="logical-and-or-and-not-operators"></a>Operatory logiczne (AND, OR i NOT)

Operatory logiczne działają na wartościach logicznych. W poniższych tabelach przedstawiono logiczne tabele prawdy dla następujących operatorów:

**Operator OR**

| LUB | Prawda | Fałsz | Nie zdefiniowano |
| --- | --- | --- | --- |
| Prawda |Prawda |Prawda |Prawda |
| Fałsz |Prawda |Fałsz |Nie zdefiniowano |
| Nie zdefiniowano |Prawda |Nie zdefiniowano |Nie zdefiniowano |

**Operator AND**

| AND | Prawda | Fałsz | Nie zdefiniowano |
| --- | --- | --- | --- |
| Prawda |Prawda |Fałsz |Nie zdefiniowano |
| Fałsz |Fałsz |Fałsz |Fałsz |
| Nie zdefiniowano |Nie zdefiniowano |Fałsz |Nie zdefiniowano |

**Operator NOT**

| NOT |  |
| --- | --- |
| Prawda |Fałsz |
| Fałsz |Prawda |
| Nie zdefiniowano |Nie zdefiniowano |


## <a name="-operator"></a>* — Operator

Operator specjalny * projektuje cały element jako. Jeśli go używasz, musi być to jedyne pole w projekcji. Zapytanie takie jak `SELECT * FROM Families f` jest prawidłowe, ale `SELECT VALUE * FROM Families f` i `SELECT *, f.id FROM Families f` są nieprawidłowe.

## <a name="-and--operators"></a>? i? operatory

Można użyć operatorów Trzyelementowy (?) i łączenia (??), aby kompilować wyrażenia warunkowe, jak w językach programowania, C# takich jak i JavaScript. 

Możesz użyć? operator do konstruowania nowych właściwości JSON na bieżąco. Na przykład następujące zapytanie klasyfikuje poziomy klasy do `elementary` lub `other`:

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

Możesz również zagnieżdżać wywołania do? operator, jak w następującej kwerendzie: 

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high") AS gradeLevel
    FROM Families.children[0] c
```

Tak jak w przypadku innych operatorów zapytań,? operator wyklucza elementy, jeśli brakuje przywoływanych właściwości lub porównywane typy są różne.

Użyć? operator, który skutecznie sprawdza obecność właściwości w elemencie podczas wykonywania zapytania dotyczącego danych z częściową strukturą lub typem mieszanym. Na przykład następujące zapytanie zwraca `lastName`, jeśli istnieje, lub `surname`, jeśli `lastName` nie istnieje.

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a name="next-steps"></a>Następne kroki

- [Przykłady dla platformy .NET w usłudze Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [słowa kluczowe](sql-query-keywords.md)
- [Klauzula SELECT](sql-query-select.md)
