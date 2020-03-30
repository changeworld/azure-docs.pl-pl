---
title: ARRAY_CONCAT w języku zapytań usługi Azure Cosmos DB
description: Dowiedz się, jak funkcja systemu SQL Array Concat w usłudze Azure Cosmos DB zwraca tablicę, która jest wynikiem łączenia dwóch lub więcej wartości tablic
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8f2b37181e5d743809bb1f60be4056cb4442a8d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78295882"
---
# <a name="array_concat-azure-cosmos-db"></a>ARRAY_CONCAT (Usługa Azure Cosmos DB)
 Zwraca tablicę, która jest wynikiem połączenia co najmniej dwóch wartości tablicy.  
  
## <a name="syntax"></a>Składnia
  
```sql
ARRAY_CONCAT (<arr_expr1>, <arr_expr2> [, <arr_exprN>])  
```  
  
## <a name="arguments"></a>Argumenty
  
*arr_expr*  
   Jest wyrażeniem tablicy do łączenia z innymi wartościami. Funkcja `ARRAY_CONCAT` wymaga co najmniej dwóch *arr_expr* argumentów.  
  
## <a name="return-types"></a>Typy zwrotów
  
  Zwraca wyrażenie tablicy.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład, jak łączyć dwie tablice.  
  
```sql
SELECT ARRAY_CONCAT(["apples", "strawberries"], ["bananas"]) AS arrayConcat 
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{"arrayConcat": ["apples", "strawberries", "bananas"]}]  
```  
  
## <a name="remarks"></a>Uwagi

Ta funkcja systemu nie będzie korzystać z indeksu.

## <a name="next-steps"></a>Następne kroki

- [Funkcje macierzy Usługi Azure Cosmos DB](sql-query-array-functions.md)
- [Funkcje systemowe Usługi Azure Cosmos DB](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
