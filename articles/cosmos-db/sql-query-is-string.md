---
title: IS_STRING w języku zapytań Azure Cosmos DB
description: Dowiedz się więcej o funkcji IS_STRING systemu SQL w Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: e105394765ccd8a495a0b44b0c29e0c4c0b9f59a
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349786"
---
# <a name="is_string-azure-cosmos-db"></a>IS_STRING (Azure Cosmos DB)
 Zwraca wartość logiczną wskazującą, czy typ określonego wyrażenia ciągu.  
  
## <a name="syntax"></a>Składnia
  
```sql
IS_STRING(<expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*expr*  
   To dowolne wyrażenie.  
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wyrażenie logiczne.  
  
## <a name="examples"></a>Przykłady
  
  W poniższym przykładzie są sprawdzane obiekty typu Boolean, Number, String, null, Object, Array i undefined przy użyciu funkcji `IS_STRING`.  
  
```sql
SELECT   
       IS_STRING(true) AS isStr1,   
       IS_STRING(1) AS isStr2,  
       IS_STRING("value") AS isStr3,   
       IS_STRING(null) AS isStr4,  
       IS_STRING({prop: "value"}) AS isStr5,   
       IS_STRING([1, 2, 3]) AS isStr6,  
       IS_STRING({prop: "value"}.prop2) AS isStr7  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```json
[{"isStr1":false,"isStr2":false,"isStr3":true,"isStr4":false,"isStr5":false,"isStr6":false,"isStr7":false}] 
```  

## <a name="next-steps"></a>Następne kroki

- [Funkcje sprawdzania typu Azure Cosmos DB](sql-query-type-checking-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do Azure Cosmos DB](introduction.md)
