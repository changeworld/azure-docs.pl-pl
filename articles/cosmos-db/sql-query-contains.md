---
title: Zawiera w języku kwerend usługi Azure Cosmos DB
description: Dowiedz się, jak funkcja systemu SQL contains w usłudze Azure Cosmos DB zwraca wartość logiczną wskazującą, czy pierwsze wyrażenie ciągu zawiera drugie wyrażenie ciągu
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c0c25b63fb6a7bf42bd2ec5b9503cac2cce7583f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302597"
---
# <a name="contains-azure-cosmos-db"></a>ZAWIERA (Usługa Azure Cosmos DB)
 Zwraca wartość logiczną wskazującą, czy pierwsze wyrażenie ciągu zawiera drugie.  
  
## <a name="syntax"></a>Składnia
  
```sql
CONTAINS(<str_expr1>, <str_expr2>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr1*  
   Jest wyrażeniem ciągu, które ma być przeszukiwane.  
  
*str_expr2*  
   Jest wyrażeniem ciągu do znalezienia.  
  
## <a name="return-types"></a>Typy zwrotów
  
  Zwraca wyrażenie logiczne.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład sprawdza, czy "abc" zawiera "ab" i jeśli "abc" zawiera "d".  
  
```sql
SELECT CONTAINS("abc", "ab") AS c1, CONTAINS("abc", "d") AS c2 
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{"c1": true, "c2": false}]  
```  

## <a name="remarks"></a>Uwagi

Ta funkcja systemu nie będzie korzystać z indeksu.

## <a name="next-steps"></a>Następne kroki

- [Funkcje ciągów usługi Azure Cosmos DB](sql-query-string-functions.md)
- [Funkcje systemowe Usługi Azure Cosmos DB](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
