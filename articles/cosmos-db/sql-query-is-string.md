---
title: IS_STRING w języku zapytań usługi Azure Cosmos DB
description: Dowiedz się więcej o IS_STRING funkcji systemu SQL w usłudze Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a717f343b0f46522a3ce2bb56c32e3f15998d777
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303770"
---
# <a name="is_string-azure-cosmos-db"></a>IS_STRING (Usługa Azure Cosmos DB)
 Zwraca wartość logiczną wskazującą, czy typem określonego wyrażenia jest ciąg.  
  
## <a name="syntax"></a>Składnia
  
```sql
IS_STRING(<expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*Expr*  
   Czy dowolne wyrażenie.  
  
## <a name="return-types"></a>Typy zwrotów
  
  Zwraca wyrażenie logiczne.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład sprawdza obiekty JSON Boolean, liczba, ciąg, null, obiekt, tablica i niezdefiniowane typy za pomocą `IS_STRING` funkcji.  
  
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
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{"isStr1":false,"isStr2":false,"isStr3":true,"isStr4":false,"isStr5":false,"isStr6":false,"isStr7":false}] 
```  

## <a name="remarks"></a>Uwagi

Ta funkcja systemu będzie korzystać z [indeksu zakresu](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Następne kroki

- [Funkcje sprawdzania typów Usługi Azure Cosmos DB](sql-query-type-checking-functions.md)
- [Funkcje systemowe Usługi Azure Cosmos DB](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
