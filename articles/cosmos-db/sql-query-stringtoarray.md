---
title: StringToArray w języku kwerendy usługi Azure Cosmos DB
description: Dowiedz się więcej o funkcji systemu SQL StringToArray w usłudze Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 18acbd94fa3d717fc20b9e1020b9bf7c6db7744d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302920"
---
# <a name="stringtoarray-azure-cosmos-db"></a>StringToArray (usługa Azure Cosmos DB)
 Zwraca wyrażenie przetłumaczone na tablicę. Jeśli nie można przetłumaczyć wyrażenia, zwraca niezdefiniowane.  
  
## <a name="syntax"></a>Składnia
  
```sql  
StringToArray(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Jest wyrażeniem ciągu, które ma być analizowane jako wyrażenie tablicy JSON. 
  
## <a name="return-types"></a>Typy zwrotów
  
  Zwraca wyrażenie tablicy lub niezdefiniowane. 
  
## <a name="remarks"></a>Uwagi
  Wartości zagnieżdżonych ciągów muszą być zapisywane z cudzysłowami podwójnymi, aby były prawidłowe JSON. Aby uzyskać szczegółowe informacje na temat formatu JSON, zobacz [json.org](https://json.org/)
  
## <a name="examples"></a>Przykłady
  
  W poniższym `StringToArray` przykładzie pokazano, jak zachowuje się w różnych typach. 
  
 Poniżej przedstawiono przykłady z prawidłowym wejściem.

```sql
SELECT 
    StringToArray('[]') AS a1, 
    StringToArray("[1,2,3]") AS a2,
    StringToArray("[\"str\",2,3]") AS a3,
    StringToArray('[["5","6","7"],["8"],["9"]]') AS a4,
    StringToArray('[1,2,3, "[4,5,6]",[7,8]]') AS a5
```

Tutaj znajduje się zestaw wyników.

```json
[{"a1": [], "a2": [1,2,3], "a3": ["str",2,3], "a4": [["5","6","7"],["8"],["9"]], "a5": [1,2,3,"[4,5,6]",[7,8]]}]
```

Poniżej przedstawiono przykład nieprawidłowego wprowadzania danych. 
   
 Pojedyncze cudzysłowy w tablicy nie są prawidłowe JSON.
Mimo że są one prawidłowe w ramach kwerendy, nie będą analizować prawidłowe tablice. Ciągi w ciągu tablicy muszą być\\\\zmienione "[ " "]" lub otaczający cytat musi być pojedynczy '[""]'.

```sql
SELECT
    StringToArray("['5','6','7']")
```

Tutaj znajduje się zestaw wyników.

```json
[{}]
```

Poniżej przedstawiono przykłady nieprawidłowych danych wejściowych.
   
 Przekazane wyrażenie będzie analizowane jako tablica JSON; następujące nie ocenić, aby wpisać tablicę i w ten sposób zwracać niezdefiniowane.
   
```sql
SELECT
    StringToArray("["),
    StringToArray("1"),
    StringToArray(NaN),
    StringToArray(false),
    StringToArray(undefined)
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
