---
title: Klauzula GROUP BY w usłudze Azure Cosmos DB
description: Dowiedz się więcej o klauzuli GROUP BY dla usługi Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: tisande
ms.openlocfilehash: 8a3cbbafc066747b62f79934f2cd12301aa1ba17
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261605"
---
# <a name="group-by-clause-in-azure-cosmos-db"></a>Klauzula GROUP BY w usłudze Azure Cosmos DB

Klauzula GROUP BY dzieli wyniki kwerendy zgodnie z wartościami jednej lub więcej określonych właściwości.

> [!NOTE]
> Usługa Azure Cosmos DB obsługuje obecnie grupę według w pliku .NET SDK 3.3 i nowszy, a także javascript SDK 3.4 lub nowszy.
> Obsługa innych języków SDK nie jest obecnie dostępna, ale jest planowana.

## <a name="syntax"></a>Składnia

```sql  
<group_by_clause> ::= GROUP BY <scalar_expression_list>

<scalar_expression_list> ::=
          <scalar_expression>
        | <scalar_expression_list>, <scalar_expression>
```  

## <a name="arguments"></a>Argumenty

- `<scalar_expression_list>`

   Określa wyrażenia, które będą używane do dzielenia wyników kwerendy.

- `<scalar_expression>`
  
   Wszelkie wyrażenia skalarne są dozwolone z wyjątkiem podquerii skalarnych i agregacji skalarnych. Każde wyrażenie skalarne musi zawierać co najmniej jedno odwołanie do właściwości. Nie ma ograniczeń co do liczby pojedynczych wyrażeń ani kardynalności każdego wyrażenia.

## <a name="remarks"></a>Uwagi
  
  Gdy kwerenda używa klauzuli GROUP BY, klauzula SELECT może zawierać tylko podzbiór właściwości i funkcji systemowych zawartych w klauzuli GROUP BY. Jednym z wyjątków są [agregowane funkcje systemowe,](sql-query-aggregates.md)które mogą pojawić się w klauzuli SELECT bez uwzględniania ich w klauzuli GROUP BY. W klauzuli SELECT zawsze można również uwzględnić wartości literału.

  Klauzula GROUP BY musi być po select, FROM i WHERE klauzuli i przed offset limit klauzuli. Obecnie nie można użyć GROUP BY z klauzulą ORDER BY, ale jest to planowane.

  Klauzula GROUP BY nie zezwala na żadną z następujących czynności:
  
- Właściwości aliasowania lub funkcje systemu aliasowania (aliasowanie jest nadal dozwolone w klauzuli SELECT)
- Podkwerend
- Zagregowane funkcje systemowe (są one dozwolone tylko w klauzuli SELECT)

Zapytania z agregowaną funkcją systemową `GROUP BY` i podkwerendą z nie są obsługiwane. Na przykład następująca kwerenda nie jest obsługiwana:

```sql
SELECT COUNT(UniqueLastNames) FROM (SELECT AVG(f.age) FROM f GROUP BY f.lastName) AS UniqueLastNames
```

## <a name="examples"></a>Przykłady

W tych przykładach użyto zestawu danych żywieniowych dostępnych za pośrednictwem [usługi Azure Cosmos DB Query Playground](https://www.documentdb.com/sql/demo).

Na przykład oto zapytanie, które zwraca całkowitą liczbę elementów w każdej grupie żywności:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup
FROM Food f
GROUP BY f.foodGroup
```

Niektóre wyniki są (SŁOWO kluczowe TOP służy do ograniczania wyników):

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

Ta kwerenda ma dwa wyrażenia używane do dzielenia wyników:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup, f.version
FROM Food f
GROUP BY f.foodGroup, f.version
```

Niektóre wyniki są:

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

Ta kwerenda ma funkcję systemową w klauzuli GROUP BY:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, UPPER(f.foodGroup) AS upperFoodGroup
FROM Food f
GROUP BY UPPER(f.foodGroup)
```

Niektóre wyniki są:

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

Ta kwerenda używa zarówno słów kluczowych, jak i funkcji systemowych w wyrażeniu właściwości elementu:

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
- [Klauzula SELECT](sql-query-select.md)
- [Agregujących](sql-query-aggregates.md)
