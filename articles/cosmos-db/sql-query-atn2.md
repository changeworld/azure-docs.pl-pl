---
title: ATN2 w języku zapytań Azure Cosmos DB
description: Dowiedz się więcej o funkcji ATN2 systemu SQL w Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 43bf2f6e27d093b72560b87349150268e0f58a60
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350195"
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
