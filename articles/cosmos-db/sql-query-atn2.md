---
title: ATN2 w języku zapytań Azure Cosmos DB
description: Dowiedz się, w jaki sposób Funkcja systemowa SQL ATN2 w Azure Cosmos DB zwraca wartość główną tangensa łuku dla y/x, wyrażoną w radianach
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 11321ef9d7b81af279b04e0e435b19c645cf3bcf
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871656"
---
# <a name="atn2-azure-cosmos-db"></a>ATN2 (Azure Cosmos DB)
 Zwraca wartość główną wartości arcus tangens y / x, wyrażony w radianach.  
  
## <a name="syntax"></a>Składnia
  
```sql
ATN2(<numeric_expr>, <numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   To wyrażenie liczbowe.  
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wartość wyrażenia liczbowego.  
  
## <a name="examples"></a>Przykłady
  
  W poniższym przykładzie obliczany ATN2 dla określonego x i y składników.  
  
```sql
SELECT ATN2(35.175643, 129.44) AS atn2  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```json
[{"atn2": 1.3054517947300646}]  
```  

## <a name="next-steps"></a>Następne kroki

- [Funkcje matematyczne Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do Azure Cosmos DB](introduction.md)
