---
title: StringToObject w języku zapytań Azure Cosmos DB
description: Dowiedz się więcej o funkcji StringToObject systemu SQL w Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: f09c27458a630386664f3f6579cfeee0721d8be9
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349217"
---
# <a name="stringtoobject-azure-cosmos-db"></a>StringToObject (Azure Cosmos DB)
 Zwraca wyrażenie przetłumaczone na obiekt. Jeśli wyrażenia nie można przetłumaczyć, funkcja zwraca wartość undefined.  
  
## <a name="syntax"></a>Składnia
  
```sql
StringToObject(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Jest wyrażeniem ciągu, które ma być analizowane jako wyrażenie obiektu JSON. Należy zauważyć, że zagnieżdżone wartości ciągu muszą być zapisywane z podwójnymi cudzysłowami, aby były prawidłowe. Aby uzyskać szczegółowe informacje o formacie JSON, zobacz [JSON.org](https://json.org/)  
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wyrażenie obiektu lub niezdefiniowane.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład pokazuje, jak `StringToObject` zachowuje się w różnych typach. 
  
 Poniżej przedstawiono przykłady z prawidłowymi danymi wejściowymi.

```sql
SELECT 
    StringToObject("{}") AS obj1, 
    StringToObject('{"A":[1,2,3]}') AS obj2,
    StringToObject('{"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]}') AS obj3, 
    StringToObject("{\"C\":[{\"c1\":[5,6,7]},{\"c2\":8},{\"c3\":9}]}") AS obj4
``` 

W tym miejscu znajduje się zestaw wyników.

```json
[{"obj1": {}, 
  "obj2": {"A": [1,2,3]}, 
  "obj3": {"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]},
  "obj4": {"C":[{"c1":[5,6,7]},{"c2":8},{"c3":9}]}}]
```

 Poniżej przedstawiono przykłady z nieprawidłowymi danymi wejściowymi.
Mimo że są one prawidłowe w ramach zapytania, nie będą analizowane pod kątem prawidłowych obiektów. Ciągi w ciągu obiektu muszą mieć wartość ucieczki "{\\" a @ no__t-1 ": \\" str @ no__t-3 "}" lub otaczające cudzysłowy muszą być pojedynczymi "{" a ":" str "}".

Pojedyncze cudzysłowy otaczające nazwy właściwości nie są prawidłowymi formatami JSON.

```sql
SELECT 
    StringToObject("{'a':[1,2,3]}")
```

W tym miejscu znajduje się zestaw wyników.

```json
[{}]
```  

Nazwy właściwości bez otaczających cudzysłowów nie są prawidłowymi formatami JSON.

```sql
SELECT 
    StringToObject("{a:[1,2,3]}")
```

W tym miejscu znajduje się zestaw wyników.

```json
[{}]
``` 

Poniżej przedstawiono przykłady z nieprawidłowymi danymi wejściowymi.

 Przesłane wyrażenie zostanie przeanalizowane jako obiekt JSON; te dane wejściowe nie są oceniane do typu Object i w rezultacie zwracają niezdefiniowane.

```sql
SELECT 
    StringToObject("}"),
    StringToObject("{"),
    StringToObject("1"),
    StringToObject(NaN), 
    StringToObject(false), 
    StringToObject(undefined)
``` 
 
 W tym miejscu znajduje się zestaw wyników.

```json
[{}]
```

## <a name="next-steps"></a>Następne kroki

- [Azure Cosmos DB funkcje ciągów](sql-query-string-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do Azure Cosmos DB](introduction.md)
