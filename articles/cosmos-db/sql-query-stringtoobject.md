---
title: StringToObject w języku kwerendy usługi Azure Cosmos DB
description: Dowiedz się więcej o funkcji systemu SQL StringToObject w usłudze Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c3e61d1efe20910d84ef4ff583d74982b3ea9f3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78296385"
---
# <a name="stringtoobject-azure-cosmos-db"></a>StringToObject (usługa Azure Cosmos DB)
 Zwraca wyrażenie przetłumaczone na obiekt. Jeśli nie można przetłumaczyć wyrażenia, zwraca niezdefiniowane.  
  
## <a name="syntax"></a>Składnia
  
```sql
StringToObject(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Jest wyrażeniem ciągu, które ma być analizowane jako wyrażenie obiektu JSON. Należy zauważyć, że wartości zagnieżdżonych ciągów muszą być zapisywane z cudzysłowami podwójnymi, aby były prawidłowe. Aby uzyskać szczegółowe informacje na temat formatu JSON, zobacz [json.org](https://json.org/)  
  
## <a name="return-types"></a>Typy zwrotów
  
  Zwraca wyrażenie obiektu lub niezdefiniowane.  
  
## <a name="examples"></a>Przykłady
  
  W poniższym `StringToObject` przykładzie pokazano, jak zachowuje się w różnych typach. 
  
 Poniżej przedstawiono przykłady z prawidłowym wejściem.

```sql
SELECT 
    StringToObject("{}") AS obj1, 
    StringToObject('{"A":[1,2,3]}') AS obj2,
    StringToObject('{"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]}') AS obj3, 
    StringToObject("{\"C\":[{\"c1\":[5,6,7]},{\"c2\":8},{\"c3\":9}]}") AS obj4
``` 

Tutaj znajduje się zestaw wyników.

```json
[{"obj1": {}, 
  "obj2": {"A": [1,2,3]}, 
  "obj3": {"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]},
  "obj4": {"C":[{"c1":[5,6,7]},{"c2":8},{"c3":9}]}}]
```

 Poniżej przedstawiono przykłady z nieprawidłowym wprowadzaniem danych.
Mimo że są one prawidłowe w ramach kwerendy, nie będą analizować prawidłowe obiekty. Ciągi w ciągu obiektu muszą być albo\\zmienione\\"{ "a ":\\"str\\"}" lub otaczający cytat musi być pojedynczy '{"a": "str"}'.

Pojedyncze cudzysłowy otaczające nazwy właściwości są nieprawidłowe JSON.

```sql
SELECT 
    StringToObject("{'a':[1,2,3]}")
```

Tutaj znajduje się zestaw wyników.

```json
[{}]
```  

Nazwy właściwości bez otaczających cudzysłowów nie są prawidłowe JSON.

```sql
SELECT 
    StringToObject("{a:[1,2,3]}")
```

Tutaj znajduje się zestaw wyników.

```json
[{}]
``` 

Poniżej przedstawiono przykłady z nieprawidłowym wprowadzaniem danych.

 Wyrażenie przekazane zostanie przeanalizowane jako obiekt JSON; te dane wejściowe nie są do oceny typu obiektu i w ten sposób zwraca niezdefiniowane.

```sql
SELECT 
    StringToObject("}"),
    StringToObject("{"),
    StringToObject("1"),
    StringToObject(NaN), 
    StringToObject(false), 
    StringToObject(undefined)
``` 
 
 Tutaj znajduje się zestaw wyników.

```json
[{}]
```

## <a name="remarks"></a>Uwagi

Ta funkcja systemu nie będzie korzystać z indeksu.

## <a name="next-steps"></a>Następne kroki

- [Funkcje ciągów usługi Azure Cosmos DB](sql-query-string-functions.md)
- [Funkcje systemowe Usługi Azure Cosmos DB](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
