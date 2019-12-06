---
title: ENDSWITH w języku zapytań Azure Cosmos DB
description: Dowiedz się więcej o funkcji systemowej ENDSWITH SQL w Azure Cosmos DB, aby zwrócić wartość Boolean wskazującą, czy pierwsze wyrażenie ciągu kończące się drugim
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6b3e692877faab8a8d507a44068d4cdfdc73a916
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873356"
---
# <a name="endswith-azure-cosmos-db"></a>ENDSWITH (Azure Cosmos DB)
 Zwraca wartość logiczną wskazującą, czy pierwszy ciąg wyrażenia kończy się na drugi.  
  
## <a name="syntax"></a>Składnia
  
```sql
ENDSWITH(<str_expr1>, <str_expr2>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr1*  
   Jest wyrażeniem ciągu.  
  
*str_expr2*  
   Jest wyrażeniem ciągu, które ma zostać porównane z końcem *str_expr1*.  
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wyrażenie logiczne.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład zwraca "abc" kończy się ciągiem "b" i "bc".  
  
```sql
SELECT ENDSWITH("abc", "b") AS e1, ENDSWITH("abc", "bc") AS e2 
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```json
[{"e1": false, "e2": true}]  
```  

## <a name="next-steps"></a>Następne kroki

- [Azure Cosmos DB funkcje ciągów](sql-query-string-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do Azure Cosmos DB](introduction.md)
