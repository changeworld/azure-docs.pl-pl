---
title: ZAWIERA w Azure Cosmos DB języku zapytań
description: Dowiedz się, jak Funkcja systemowa SQL w Azure Cosmos DB zwraca wartość Boolean wskazującą, czy pierwsze wyrażenie ciągu zawiera sekundę
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 59c0e2628bfde24200bd386adc858ba778790231
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871537"
---
# <a name="contains-azure-cosmos-db"></a>ZAWIERA (Azure Cosmos DB)
 Zwraca wartość logiczną wskazującą, czy pierwsze wyrażenie ciągu zawiera drugie.  
  
## <a name="syntax"></a>Składnia
  
```sql
CONTAINS(<str_expr1>, <str_expr2>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr1*  
   Jest wyrażeniem ciągu do przeszukania.  
  
*str_expr2*  
   Jest wyrażeniem ciągu do znalezienia.  
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wyrażenie logiczne.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład sprawdza, czy "ABC" zawiera "AB" i czy "ABC" zawiera "d".  
  
```sql
SELECT CONTAINS("abc", "ab") AS c1, CONTAINS("abc", "d") AS c2 
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```json
[{"c1": true, "c2": false}]  
```  

## <a name="next-steps"></a>Następne kroki

- [Azure Cosmos DB funkcje ciągów](sql-query-string-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do Azure Cosmos DB](introduction.md)
