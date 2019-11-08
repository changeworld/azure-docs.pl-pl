---
title: Klauzula GROUP BY w Azure Cosmos DB
description: Zapoznaj się z klauzulą GROUP BY dla Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: tisande
ms.openlocfilehash: e41e81457421bfe27e3c0313fc06e39e6df4cdce
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819105"
---
# <a name="group-by-clause-in-azure-cosmos-db"></a>Klauzula GROUP BY w Azure Cosmos DB

Klauzula GROUP BY dzieli wyniki zapytania zgodnie z wartościami jednej lub więcej określonych właściwości.

> [!NOTE]
> Azure Cosmos DB obecnie obsługuje grupowanie według zestawu .NET SDK 3,3 i nowszego oraz zestawu JavaScript SDK 3,4 lub nowszego.
> Obsługa innych zestawów SDK języka nie jest obecnie dostępna, ale jest planowana.

## <a name="syntax"></a>Składnia

```sql  
<group_by_clause> ::= GROUP BY <scalar_expression_list>

<scalar_expression_list> ::=
          <scalar_expression>
        | <scalar_expression_list>, <scalar_expression>
```  

## <a name="arguments"></a>Argumenty

- `<scalar_expression_list>`

   Określa wyrażenia, które będą używane do dzielenia wyników zapytania.

- `<scalar_expression>`
  
   Każde wyrażenie skalarne jest dozwolone z wyjątkiem skalarnych podzapytań i agregacji skalarnych. Każde wyrażenie skalarne musi zawierać co najmniej jedno odwołanie do właściwości. Nie ma żadnego limitu liczby pojedynczych wyrażeń ani kardynalności poszczególnych wyrażeń.

## <a name="remarks"></a>Uwagi
  
  Gdy zapytanie używa klauzuli GROUP BY, klauzula SELECT może zawierać tylko podzestaw właściwości i funkcji systemowych zawartych w klauzuli GROUP BY. Jedynym wyjątkiem są [zagregowane funkcje systemowe](sql-query-aggregates.md), które mogą występować w klauzuli SELECT bez uwzględniania ich w klauzuli Group by. Można zawsze uwzględnić wartości literału w klauzuli SELECT.

  Klauzula GROUP BY musi być późniejsza niż klauzula SELECT, FROM i WHERE oraz przed klauzulą LIMIT przesunięcia. Obecnie nie można używać klauzuli GROUP BY z klauzulą ORDER BY, ale jest to planowane.

  Klauzula GROUP BY nie zezwala na żadną z następujących wartości:
  
- Właściwości aliasowania lub funkcje systemowe aliasowania (aliasowanie jest nadal dozwolone w klauzuli SELECT)
- Podzapytania
- Agreguj funkcje systemowe (są dozwolone tylko w klauzuli SELECT)

## <a name="examples"></a>Przykłady

W poniższych przykładach użyto zestawu danych odżywiania dostępnego za pomocą [środowisko testowe zapytań Azure Cosmos DB](https://www.documentdb.com/sql/demo).

Na przykład Oto zapytanie, które zwraca łączną liczbę elementów w każdej z nich:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup
FROM Food f
GROUP BY f.foodGroup
```

Niektóre wyniki to (pierwsze słowo kluczowe jest używane do ograniczania wyników):

```json
[{
  "foodGroup": "Fast Foods",
  "foodGroupCount": 371
},
{
  "foodGroup": "Finfish and Shellfish Products",
  "foodGroupCount": 267
},
{
  "foodGroup": "Meals, Entrees, and Side Dishes",
  "foodGroupCount": 113
},
{
  "foodGroup": "Sausages and Luncheon Meats",
  "foodGroupCount": 244
}]
```

To zapytanie ma dwa wyrażenia używane do dzielenia wyników:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup, f.version
FROM Food f
GROUP BY f.foodGroup, f.version
```

Oto niektóre wyniki:

```json
[{
  "version": 1,
  "foodGroup": "Nut and Seed Products",
  "foodGroupCount": 133
},
{
  "version": 1,
  "foodGroup": "Finfish and Shellfish Products",
  "foodGroupCount": 267
},
{
  "version": 1,
  "foodGroup": "Fast Foods",
  "foodGroupCount": 371
},
{
  "version": 1,
  "foodGroup": "Sausages and Luncheon Meats",
  "foodGroupCount": 244
}]
```

To zapytanie zawiera funkcję systemową w klauzuli GROUP BY:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, UPPER(f.foodGroup) AS upperFoodGroup
FROM Food f
GROUP BY UPPER(f.foodGroup)
```

Oto niektóre wyniki:

```json
[{
  "foodGroupCount": 371,
  "upperFoodGroup": "FAST FOODS"
},
{
  "foodGroupCount": 267,
  "upperFoodGroup": "FINFISH AND SHELLFISH PRODUCTS"
},
{
  "foodGroupCount": 389,
  "upperFoodGroup": "LEGUMES AND LEGUME PRODUCTS"
},
{
  "foodGroupCount": 113,
  "upperFoodGroup": "MEALS, ENTREES, AND SIDE DISHES"
}]
```

To zapytanie używa zarówno słów kluczowych, jak i funkcji systemowych w wyrażeniu właściwości elementu:

```sql
SELECT COUNT(1) AS foodGroupCount, ARRAY_CONTAINS(f.tags, {name: 'orange'}) AS containsOrangeTag,  f.version BETWEEN 0 AND 2 AS correctVersion
FROM Food f
GROUP BY ARRAY_CONTAINS(f.tags, {name: 'orange'}), f.version BETWEEN 0 AND 2
```

Wyniki są następujące:

```json
[{
  "correctVersion": true,
  "containsOrangeTag": false,
  "foodGroupCount": 8608
},
{
  "correctVersion": true,
  "containsOrangeTag": true,
  "foodGroupCount": 10
}]
```

## <a name="next-steps"></a>Następne kroki

- [Wprowadzenie](sql-query-getting-started.md)
- [SELECT — klauzula](sql-query-select.md)
- [Funkcje agregujące](sql-query-aggregates.md)
