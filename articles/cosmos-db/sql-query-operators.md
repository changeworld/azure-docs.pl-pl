---
title: Operatory zapytań SQL usługi Azure Cosmos DB
description: Więcej informacji na temat operatorów SQL na potrzeby usługi Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: eecc1522f8c260286c7dd7fc4c2e58d5d8caa8fb
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342960"
---
# <a name="operators-in-azure-cosmos-db"></a>Operatory w usłudze Azure Cosmos DB

Ten artykuł szczegółowo opisuje różne operatory obsługiwane przez usługę Azure Cosmos DB.

## <a name="equality-and-comparison-operators"></a>Porównania i operatorów porównania

W poniższej tabeli przedstawiono wynik porównań równości w interfejsie API SQL między dwoma dowolnymi typami JSON.

| **Operator** | **Niezdefiniowane** | **Null** | **Wartość logiczna** | **Liczba** | **Ciąg** | **Obiekt** | **Tablica** |
|---|---|---|---|---|---|---|---|
| **Niezdefiniowane** | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane |
| **Null** | Niezdefiniowane | **OK** | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane |
| **Wartość logiczna** | Niezdefiniowane | Niezdefiniowane | **OK** | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane |
| **Liczba** | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | **OK** | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane |
| **Ciąg** | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | **OK** | Niezdefiniowane | Niezdefiniowane |
| **Obiekt** | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | **OK** | Niezdefiniowane |
| **Tablica** | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | **OK** |

Dla operatorów porównania, takie jak `>`, `>=`, `!=`, `<`, i `<=`, porównanie różnych typów lub między dwoma obiektami lub tablic generuje `Undefined`.  

Jeśli jest wynikiem wyrażenia skalarne `Undefined`, element nie jest uwzględniony w wyniku, ponieważ `Undefined` nie równa się `true`.

## <a name="logical-and-or-and-not-operators"></a>Operatory logiczne (AND, OR i NOT)

Operatory logiczne działają na wartościach logicznych. W poniższych tabelach przedstawiono tabel prawdziwych danych logicznych dla tych operatorów:

**Operator OR**

| LUB | True | False | Niezdefiniowane |
| --- | --- | --- | --- |
| True |True |True |True |
| False |True |False |Niezdefiniowane |
| Niezdefiniowane |True |Niezdefiniowane |Niezdefiniowane |

**Operator AND**

| AND | True | False | Niezdefiniowane |
| --- | --- | --- | --- |
| True |True |False |Niezdefiniowane |
| False |False |False |False |
| Niezdefiniowane |Niezdefiniowane |False |Niezdefiniowane |

**Operator NOT**

| NOT |  |
| --- | --- |
| True |False |
| False |True |
| Niezdefiniowane |Niezdefiniowane |


## <a name="-operator"></a>* — operator

Operator specjalny * projektów całego elementu, ponieważ jest. Jeśli go używasz, musi być to jedyne pole w projekcji. Zapytanie, takich jak `SELECT * FROM Families f` jest prawidłowy, ale `SELECT VALUE * FROM Families f` i `SELECT *, f.id FROM Families f` są nieprawidłowe.

## <a name="-and--operators"></a>? i? Operatory

Możesz użyć Ternary (?) i operatory (?), aby zbudować wyrażenia warunkowego, tak jak w językach programowania, jak Coalesce C# i JavaScript. 

Można użyć? Operator do tworzenia nowych właściwości kodu JSON na bieżąco. Na przykład, następujące zapytanie klasyfikuje poziomy klasy korporacyjnej do `elementary` lub `other`:

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

Można także zagnieżdżać wywołania? operator, tak jak w następującej kwerendy: 

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high") AS gradeLevel
    FROM Families.children[0] c
```

Podobnie jak w przypadku innych operatorów zapytania? operator nie obejmuje elementy, czy brakuje odwołania właściwości różnią się typami, którą jest porównywany.

Użyj? operator, który ma być efektywnie Sprawdź właściwości w elemencie podczas wykonywania zapytań dotyczących danych z częściową strukturą lub typu mieszanego. Na przykład, następujące zapytanie zwraca `lastName` , jeśli jest dostępna lub `surname` Jeśli `lastName` nie będą dostępne.

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a name="next-steps"></a>Kolejne kroki

- [Przykłady dla platformy .NET w usłudze Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-dotnet)
- [słowa kluczowe](sql-query-keywords.md)
- [Klauzula SELECT](sql-query-select.md)
