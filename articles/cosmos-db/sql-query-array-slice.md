---
title: ARRAY_SLICE w języku zapytań Azure Cosmos DB
description: Dowiedz się, w jaki sposób Funkcja systemowa SQL wycinka tablicy w Azure Cosmos DB zwraca część wyrażenia tablicy
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 3c65886eabfa87f729e18fb854aca6662d1d6fac
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871792"
---
# <a name="array_slice-azure-cosmos-db"></a>ARRAY_SLICE (Azure Cosmos DB)
 Zwraca część wyrażenia tablicy.
  
## <a name="syntax"></a>Składnia
  
```sql
ARRAY_SLICE (<arr_expr>, <num_expr> [, <num_expr>])  
```  
  
## <a name="arguments"></a>Argumenty
  
*arr_expr*  
   To dowolne wyrażenie tablicowe.  
  
*num_expr*  
   Liczony od zera indeksu liczbowego od którego należy rozpocząć tablicy. Ujemne wartości mogą być używane, aby określić indeks początkowy względem ostatniego elementu odwołań do tablic tj -1 do ostatniego elementu w tablicy.  

*num_expr* Opcjonalne wyrażenie liczbowe, które ustawia maksymalną liczbę elementów w tablicy wyników.    

## <a name="return-types"></a>Typy zwracane
  
  Zwraca wyrażenie tablicy.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład pokazuje, jak uzyskać różne wycinków tablicy przy użyciu `ARRAY_SLICE`.  
  
```sql
SELECT
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1) AS s1,  
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1) AS s2,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 1) AS s3,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 2) AS s4,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 0) AS s5,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1000) AS s6,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, -100) AS s7      
  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```json
[{  
           "s1": ["strawberries", "bananas"],   
           "s2": ["strawberries"],
           "s3": ["strawberries"],  
           "s4": ["strawberries", "bananas"], 
           "s5": [],
           "s6": ["strawberries", "bananas"],
           "s7": [] 
}]  
```  

## <a name="next-steps"></a>Następne kroki

- [Funkcje tablicowe Azure Cosmos DB](sql-query-array-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do Azure Cosmos DB](introduction.md)
