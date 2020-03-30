---
title: IS_ARRAY w języku zapytań usługi Azure Cosmos DB
description: Dowiedz się więcej o IS_ARRAY funkcji systemu SQL w usłudze Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: f5867850db6eb3d6552bc129cca3708ef7747072
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303889"
---
# <a name="is_array-azure-cosmos-db"></a>IS_ARRAY (Usługa Azure Cosmos DB)
 Zwraca wartość logiczną wskazującą, czy typem określonego wyrażenia jest tablica.  
  
## <a name="syntax"></a>Składnia
  
```sql
IS_ARRAY(<expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*Expr*  
   Czy dowolne wyrażenie.  
  
## <a name="return-types"></a>Typy zwrotów
  
  Zwraca wyrażenie logiczne.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład sprawdza obiekty JSON Boolean, liczba, ciąg, null, obiekt, tablica i niezdefiniowane typy za pomocą `IS_ARRAY` funkcji.  
  
```sql
SELECT   
 IS_ARRAY(true) AS isArray1,   
 IS_ARRAY(1) AS isArray2,  
 IS_ARRAY("value") AS isArray3,  
 IS_ARRAY(null) AS isArray4,  
 IS_ARRAY({prop: "value"}) AS isArray5,   
 IS_ARRAY([1, 2, 3]) AS isArray6,  
 IS_ARRAY({prop: "value"}.prop2) AS isArray7  
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{"isArray1":false,"isArray2":false,"isArray3":false,"isArray4":false,"isArray5":false,"isArray6":true,"isArray7":false}]
```  

## <a name="remarks"></a>Uwagi

Ta funkcja systemu będzie korzystać z [indeksu zakresu](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Następne kroki

- [Funkcje sprawdzania typów Usługi Azure Cosmos DB](sql-query-type-checking-functions.md)
- [Funkcje systemowe Usługi Azure Cosmos DB](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
