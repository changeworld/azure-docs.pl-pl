---
title: ARRAY_LENGTH w języku zapytań Azure Cosmos DB
description: Dowiedz się, jak Funkcja systemowa SQL długości tablicy w Azure Cosmos DB zwraca liczbę elementów określonego wyrażenia tablicy
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 3596ce4bc702d5e54225d8c90db2f9563feab670
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303991"
---
# <a name="array_length-azure-cosmos-db"></a>ARRAY_LENGTH (Azure Cosmos DB)
 Zwraca liczbę elementów określonego wyrażenia tablicy.  
  
## <a name="syntax"></a>Składnia
  
```sql
ARRAY_LENGTH(<arr_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*arr_expr*  
   Jest wyrażeniem tablicowym.  
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wartość wyrażenia liczbowego.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład przedstawia, jak uzyskać długość tablicy przy użyciu `ARRAY_LENGTH`.  
  
```sql
SELECT ARRAY_LENGTH(["apples", "strawberries", "bananas"]) AS len  
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{"len": 3}]  
```  
  
## <a name="remarks"></a>Uwagi

Ta funkcja systemowa nie będzie używać indeksu.

## <a name="next-steps"></a>Następne kroki

- [Funkcje tablicowe Azure Cosmos DB](sql-query-array-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do Azure Cosmos DB](introduction.md)
