---
title: IS_BOOL w języku zapytań usługi Azure Cosmos DB
description: Dowiedz się więcej o IS_BOOL funkcji systemu SQL w usłudze Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b7f1cfb09121309e246b314d57a5e4e475bd0983
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303872"
---
# <a name="is_bool-azure-cosmos-db"></a>IS_BOOL (Usługa Azure Cosmos DB)
 Zwraca wartość logiczną wskazującą, czy typem określonego wyrażenia jest wartość logiczna.  
  
## <a name="syntax"></a>Składnia
  
```sql
IS_BOOL(<expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*Expr*  
   Czy dowolne wyrażenie.  
  
## <a name="return-types"></a>Typy zwrotów
  
  Zwraca wyrażenie logiczne.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład sprawdza obiekty JSON Boolean, liczba, ciąg, null, obiekt, tablica i niezdefiniowane typy za pomocą `IS_BOOL` funkcji.  
  
```sql
SELECT   
    IS_BOOL(true) AS isBool1,   
    IS_BOOL(1) AS isBool2,  
    IS_BOOL("value") AS isBool3,   
    IS_BOOL(null) AS isBool4,  
    IS_BOOL({prop: "value"}) AS isBool5,   
    IS_BOOL([1, 2, 3]) AS isBool6,  
    IS_BOOL({prop: "value"}.prop2) AS isBool7  
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{"isBool1":true,"isBool2":false,"isBool3":false,"isBool4":false,"isBool5":false,"isBool6":false,"isBool7":false}]
```  

## <a name="remarks"></a>Uwagi

Ta funkcja systemu będzie korzystać z [indeksu zakresu](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Następne kroki

- [Funkcje sprawdzania typów Usługi Azure Cosmos DB](sql-query-type-checking-functions.md)
- [Funkcje systemowe Usługi Azure Cosmos DB](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
