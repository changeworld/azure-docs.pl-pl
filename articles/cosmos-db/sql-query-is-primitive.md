---
title: IS_PRIMITIVE w języku zapytań Azure Cosmos DB
description: Dowiedz się więcej na temat funkcji systemu SQL IS_PRIMITIVE w Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 04c8e41f1a431b329f2093851e4430e69ab6aee3
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303787"
---
# <a name="is_primitive-azure-cosmos-db"></a>IS_PRIMITIVE (Azure Cosmos DB)
 Zwraca wartość logiczną wskazującą, czy podstawowy typu z określonego wyrażenia (string, Boolean, liczbowych lub wartość null).  
  
## <a name="syntax"></a>Składnia
  
```sql
IS_PRIMITIVE(<expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*wyrażenie*  
   To dowolne wyrażenie.  
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wyrażenie logiczne.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład sprawdza obiekty typu Boolean JSON, Number, String, null, Object, Array i undefined przy użyciu funkcji `IS_PRIMITIVE`.  
  
```sql
SELECT   
           IS_PRIMITIVE(true) AS isPrim1,   
           IS_PRIMITIVE(1) AS isPrim2,  
           IS_PRIMITIVE("value") AS isPrim3,   
           IS_PRIMITIVE(null) AS isPrim4,  
           IS_PRIMITIVE({prop: "value"}) AS isPrim5,   
           IS_PRIMITIVE([1, 2, 3]) AS isPrim6,  
           IS_PRIMITIVE({prop: "value"}.prop2) AS isPrim7  
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{"isPrim1": true, "isPrim2": true, "isPrim3": true, "isPrim4": true, "isPrim5": false, "isPrim6": false, "isPrim7": false}]  
```  

## <a name="remarks"></a>Uwagi

Ta funkcja systemowa będzie korzystać z [indeksu zakresu](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Następne kroki

- [Funkcje sprawdzania typu Azure Cosmos DB](sql-query-type-checking-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do Azure Cosmos DB](introduction.md)
