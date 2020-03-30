---
title: ENDSWITH w języku zapytań usługi Azure Cosmos DB
description: Dowiedz się więcej o funkcji systemu ENDSWITH SQL w usłudze Azure Cosmos DB, aby zwrócić wartość logiczną wskazującą, czy pierwsze wyrażenie ciągu kończy się na drugim
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 37c5a8b3c44c5ac46b837e4d851d22f85aeaf39c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299452"
---
# <a name="endswith-azure-cosmos-db"></a>ENDSWITH (Azure Cosmos DB)
 Zwraca wartość logiczną wskazującą, czy pierwsze wyrażenie ciągu kończy się na drugim.  
  
## <a name="syntax"></a>Składnia
  
```sql
ENDSWITH(<str_expr1>, <str_expr2>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr1*  
   Jest wyrażeniem ciągu.  
  
*str_expr2*  
   Jest wyrażeniem ciągu, które należy porównać z końcem *str_expr1*.  
  
## <a name="return-types"></a>Typy zwrotów
  
  Zwraca wyrażenie logiczne.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład zwraca "abc" kończy się na "b" i "bc".  
  
```sql
SELECT ENDSWITH("abc", "b") AS e1, ENDSWITH("abc", "bc") AS e2 
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{"e1": false, "e2": true}]  
```  

## <a name="remarks"></a>Uwagi

Ta funkcja systemu nie będzie korzystać z indeksu.

## <a name="next-steps"></a>Następne kroki

- [Funkcje ciągów usługi Azure Cosmos DB](sql-query-string-functions.md)
- [Funkcje systemowe Usługi Azure Cosmos DB](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
