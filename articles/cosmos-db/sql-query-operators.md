---
title: Operatory zapytań SQL dla usługi Azure Cosmos DB
description: Dowiedz się więcej o operatorach SQL, takich jak równość, porównanie i operatory logiczne obsługiwane przez usługę Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: tisande
ms.openlocfilehash: 8ef41edb687a5df39243880c897d12e83c008ec9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063570"
---
# <a name="operators-in-azure-cosmos-db"></a>Operatorzy w usłudze Azure Cosmos DB

W tym artykule opisano różne operatory obsługiwane przez usługę Azure Cosmos DB.

## <a name="equality-and-comparison-operators"></a>Operatory równości i porównania

W poniższej tabeli przedstawiono wynik porównań równości w interfejsie API SQL między dwoma dowolnymi typami JSON.

| **Operator** | **Niezdefiniowane** | **Null** | **Wartość logiczna** | **Numer** | **Ciąg** | **Obiektu** | **Tablicy** |
|---|---|---|---|---|---|---|---|
| **Niezdefiniowane** | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane |
| **Null** | Niezdefiniowane | **Ok** | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane |
| **Wartość logiczna** | Niezdefiniowane | Niezdefiniowane | **Ok** | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane |
| **Numer** | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | **Ok** | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane |
| **Ciąg** | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | **Ok** | Niezdefiniowane | Niezdefiniowane |
| **Obiektu** | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | **Ok** | Niezdefiniowane |
| **Tablicy** | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | **Ok** |

Dla operatorów `>`porównania, `<`takich `<=`jak , `>=` `!=`, , i , porównanie `Undefined`między typami lub między dwoma obiektami lub tablicami tworzy .  

Jeśli wynikiem wyrażenia skalarnego `Undefined`jest , element nie jest uwzględniony `Undefined` w wyniku, ponieważ nie jest równa `true`.

## <a name="logical-and-or-and-not-operators"></a>Operatory logiczne (AND, OR i NOT)

Operatory logiczne działają na wartościach logicznych. W poniższych tabelach przedstawiono logiczne tabele prawdy dla tych operatorów:

**Operator OR**

Zwraca, `true` gdy którykolwiek `true`z warunków jest .

|  | **True** | **False** | **Niezdefiniowane** |
| --- | --- | --- | --- |
| **True** |True |True |True |
| **False** |True |False |Niezdefiniowane |
| **Niezdefiniowane** |True |Niezdefiniowane |Niezdefiniowane |

**Operator AND**

Zwraca, `true` gdy oba `true`wyrażenia są .

|  | **True** | **False** | **Niezdefiniowane** |
| --- | --- | --- | --- |
| **True** |True |False |Niezdefiniowane |
| **False** |False |False |False |
| **Niezdefiniowane** |Niezdefiniowane |False |Niezdefiniowane |

**Operator NOT**

Odwraca wartość dowolnego wyrażenia logicznego.

|  | **Nie** |
| --- | --- |
| **True** |False |
| **False** |True |
| **Niezdefiniowane** |Niezdefiniowane |

**Kolejność wykonywania działań**

Operatory `OR`logiczne `NOT` i `AND`mają poziom pierwszeństwa pokazany poniżej:

| **Operator** | **Priorytet** |
| --- | --- |
| **Nie** |1 |
| **I** |2 |
| **Lub** |3 |

## <a name="-operator"></a>* — Operator

Specjalny operator * projektuje cały element, tak jak jest. Jeśli go używasz, musi być to jedyne pole w projekcji. Kwerenda `SELECT * FROM Families f` jak jest `SELECT VALUE * FROM Families f` prawidłowa, ale i `SELECT *, f.id FROM Families f` nie są prawidłowe.

## <a name="-and--operators"></a>? I?? Operatory

Operatory Ternary (?) i Coalesce (??) można używać do tworzenia wyrażeń warunkowych, tak jak w językach programowania, takich jak C# i JavaScript.

Można użyć ? operator do konstruowania nowych właściwości JSON na bieżąco. Na przykład następująca kwerenda klasyfikuje `other`poziomy nachylenia do: `elementary`

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

Można również zagnieżdżać połączenia do ? operator, jak w następującej kwerendzie: 

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high") AS gradeLevel
    FROM Families.children[0] c
```

Podobnie jak w przypadku innych operatorów zapytań, ? operator wyklucza elementy, jeśli brakuje właściwości, do których istnieje odwołanie lub porównywane typy są różne.

Użyj ?? operator, aby skutecznie sprawdzić właściwość w elemencie podczas wykonywania zapytań względem danych półstrukturacyjnych lub mieszanych. Na przykład następująca `lastName` kwerenda zwraca, jeśli jest obecny lub `surname` jeśli `lastName` nie jest obecny.

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a name="next-steps"></a>Następne kroki

- [Przykłady dla platformy .NET w usłudze Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Słowa kluczowe](sql-query-keywords.md)
- [Klauzula SELECT](sql-query-select.md)
