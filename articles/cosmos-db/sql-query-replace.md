---
title: ZAMIEŃ w języku kwerend usługi Azure Cosmos DB
description: Dowiedz się więcej o funkcji systemu SQL ZAMIEŃ w usłudze Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 758ac13530752df481d27e7e253f025f5c8d6430
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302206"
---
# <a name="replace-azure-cosmos-db"></a>ZAMIEŃ (Usługa Azure Cosmos DB)
 Zamienia wszystkie wystąpienia określonej wartości ciągu na inną wartość ciągu.  
  
## <a name="syntax"></a>Składnia
  
```sql
REPLACE(<str_expr1>, <str_expr2>, <str_expr3>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr1*  
   Jest wyrażeniem ciągu, które ma być przeszukiwane.  
  
*str_expr2*  
   Czy wyrażenie ciągu można znaleźć.  
  
*str_expr3*  
   Jest wyrażeniem ciągu zastępującym wystąpienia *str_expr2* w *str_expr1*.  
  
## <a name="return-types"></a>Typy zwrotów
  
  Zwraca wyrażenie ciągu.  
  
## <a name="examples"></a>Przykłady
  
  W poniższym przykładzie `REPLACE` pokazano, jak używać w kwerendzie.  
  
```sql
SELECT REPLACE("This is a Test", "Test", "desk") AS replace
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{"replace": "This is a desk"}]  
```  

## <a name="remarks"></a>Uwagi

Ta funkcja systemu nie będzie korzystać z indeksu.

## <a name="next-steps"></a>Następne kroki

- [Funkcje ciągów usługi Azure Cosmos DB](sql-query-string-functions.md)
- [Funkcje systemowe Usługi Azure Cosmos DB](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
