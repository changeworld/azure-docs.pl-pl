---
title: ARRAY_LENGTH w języku zapytań Azure Cosmos DB
description: Dowiedz się, jak Funkcja systemowa SQL długości tablicy w Azure Cosmos DB zwraca liczbę elementów określonego wyrażenia tablicy
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9a8bf33befdd842a2979151fef3d54679ee03de1
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871775"
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
  
 W tym miejscu znajduje się zestaw wyników.  
  
```json
[{"len": 3}]  
```  
  

## <a name="next-steps"></a>Następne kroki

- [Funkcje tablicowe Azure Cosmos DB](sql-query-array-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do Azure Cosmos DB](introduction.md)
