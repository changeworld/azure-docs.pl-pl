---
title: ARRAY_CONCAT w języku zapytań Azure Cosmos DB
description: Dowiedz się więcej na temat sposobu, w jaki tablica concat funkcja SQL system w Azure Cosmos DB zwraca tablicę, która jest wynikiem łączenia dwóch lub więcej wartości tablicy
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 10370e16c95f4fc747dd3a66a56794da38562972
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871826"
---
# <a name="array_concat-azure-cosmos-db"></a>ARRAY_CONCAT (Azure Cosmos DB)
 Zwraca tablicę, która jest wynikiem połączenia co najmniej dwóch wartości tablicy.  
  
## <a name="syntax"></a>Składnia
  
```sql
ARRAY_CONCAT (<arr_expr1>, <arr_expr2> [, <arr_exprN>])  
```  
  
## <a name="arguments"></a>Argumenty
  
*arr_expr*  
   To wyrażenie tablicowe umożliwiające połączenie z innymi wartościami. Funkcja `ARRAY_CONCAT` wymaga co najmniej dwóch argumentów *arr_expr* .  
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wyrażenie tablicy.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład sposobu łączenia dwóch tablic.  
  
```sql
SELECT ARRAY_CONCAT(["apples", "strawberries"], ["bananas"]) AS arrayConcat 
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```json
[{"arrayConcat": ["apples", "strawberries", "bananas"]}]  
```  
  

## <a name="next-steps"></a>Następne kroki

- [Funkcje tablicowe Azure Cosmos DB](sql-query-array-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do Azure Cosmos DB](introduction.md)
