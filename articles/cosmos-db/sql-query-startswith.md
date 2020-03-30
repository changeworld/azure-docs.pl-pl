---
title: STARTSWITH w języku zapytań usługi Azure Cosmos DB
description: Dowiedz się więcej o funkcji systemu SQL STARTSWITH w usłudze Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9ed49c067946186f8b79f67bad0a460113eacb73
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78295712"
---
# <a name="startswith-azure-cosmos-db"></a>STARTSWITH (Azure Cosmos DB)
 Zwraca wartość logiczną wskazującą, czy pierwsze wyrażenie ciągu rozpoczyna się od drugiego.  
  
## <a name="syntax"></a>Składnia
  
```sql
STARTSWITH(<str_expr1>, <str_expr2>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr1*  
   Jest wyrażeniem ciągu.
  
*str_expr2*  
   Jest wyrażeniem ciągu, które należy porównać z początkiem *str_expr1*.

## <a name="return-types"></a>Typy zwrotów
  
  Zwraca wyrażenie logiczne.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład sprawdza, czy ciąg "abc" zaczyna się od "b" i "a".  
  
```sql
SELECT STARTSWITH("abc", "b") AS s1, STARTSWITH("abc", "a") AS s2  
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{"s1": false, "s2": true}]  
```  

## <a name="remarks"></a>Uwagi

Ta funkcja systemu będzie korzystać z [indeksu zakresu](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Następne kroki

- [Funkcje ciągów usługi Azure Cosmos DB](sql-query-string-functions.md)
- [Funkcje systemowe Usługi Azure Cosmos DB](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
