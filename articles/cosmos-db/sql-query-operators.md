---
title: Operatory zapytań SQL dla Azure Cosmos DB
description: Więcej informacji na temat operatorów SQL dla Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: 899355ad7331a3df8cd5d647a573dc15e3a0bb14
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003378"
---
# <a name="operators-in-azure-cosmos-db"></a>Operatory w Azure Cosmos DB

W tym artykule szczegółowo opisano różne operatory obsługiwane przez Azure Cosmos DB.

## <a name="equality-and-comparison-operators"></a>Operatory równości i porównania

W poniższej tabeli przedstawiono wynik porównań równości w interfejsie API SQL między dwoma dowolnymi typami JSON.

| **Operator** | **Niezdefiniowane** | **Null** | **Wartość logiczna** | **Liczba** | **Ciąg** | **Obiekt** | **Tablica** |
|---|---|---|---|---|---|---|---|
| **Niezdefiniowane** | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane |
| **Null** | Niezdefiniowane | **OK** | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane |
| **Boolean** | Niezdefiniowane | Niezdefiniowane | **OK** | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane |
| **Liczba** | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | **OK** | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane |
| **Ciąg** | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | **OK** | Niezdefiniowane | Niezdefiniowane |
| **Obiekt** | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | **OK** | Niezdefiniowane |
| **Tablica** | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | **OK** |

Dla operatorów porównania, takich `>`jak `>=`, `!=`, `<`,, `<=`i, porównywanie między typami lub między dwoma obiektami lub `Undefined`tablicami powstaje.  

Jeśli wynik wyrażenia skalarnego to `Undefined`, element nie jest uwzględniony w wyniku, ponieważ `Undefined` nie jest równe `true`.

## <a name="logical-and-or-and-not-operators"></a>Operatory logiczne (AND, OR i NOT)

Operatory logiczne działają na wartościach logicznych. W poniższych tabelach przedstawiono logiczne tabele prawdy dla następujących operatorów:

**Operator OR**

| LUB | Prawda | False | Niezdefiniowane |
| --- | --- | --- | --- |
| Prawda |Prawda |Prawda |Prawda |
| False |Prawda |False |Niezdefiniowane |
| Niezdefiniowane |Prawda |Niezdefiniowane |Niezdefiniowane |

**Operator AND**

| AND | Prawda | False | Niezdefiniowane |
| --- | --- | --- | --- |
| Prawda |Prawda |False |Niezdefiniowane |
| False |False |False |False |
| Niezdefiniowane |Niezdefiniowane |False |Niezdefiniowane |

**Operator NOT**

| NOT |  |
| --- | --- |
| Prawda |False |
| False |Prawda |
| Niezdefiniowane |Niezdefiniowane |


## <a name="-operator"></a>* — operator

Operator specjalny * projektuje cały element jako. Jeśli go używasz, musi być to jedyne pole w projekcji. Zapytanie `SELECT * FROM Families f` , które jest prawidłowe, ale `SELECT VALUE * FROM Families f` i `SELECT *, f.id FROM Families f` jest nieprawidłowe.

## <a name="-and--operators"></a>? i? zainteresowanych

Można użyć operatorów Trzyelementowy (?) i łączenia (??), aby kompilować wyrażenia warunkowe, jak w językach programowania, C# takich jak i JavaScript. 

Możesz użyć? operator do konstruowania nowych właściwości JSON na bieżąco. Na przykład następujące zapytanie klasyfikuje poziomy klasy do `elementary` lub: `other`

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

Użyć? operator, który skutecznie sprawdza obecność właściwości w elemencie podczas wykonywania zapytania dotyczącego danych z częściową strukturą lub typem mieszanym. Na przykład następujące zapytanie zwraca wartość `lastName` , jeśli jest obecny, lub `lastName` `surname` Jeśli nie jest obecne.

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a name="next-steps"></a>Następne kroki

- [Przykłady dla platformy .NET w usłudze Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [słowa kluczowe](sql-query-keywords.md)
- [Klauzula SELECT](sql-query-select.md)
