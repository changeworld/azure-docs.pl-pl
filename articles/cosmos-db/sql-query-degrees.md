---
title: STOPnie w Azure Cosmos DB języku zapytań
description: Dowiedz się więcej o STOPNIach funkcji systemu SQL w Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d8c2109c2c11f137b1966741a95d1d0c02895016
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351206"
---
# <a name="degrees-azure-cosmos-db"></a>STOPnie (Azure Cosmos DB)
 Zwraca kąt w stopniach odpowiadający kątowi określonemu w radianach.  
  
## <a name="syntax"></a>Składnia
  
```sql
DEGREES (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   To wyrażenie liczbowe.  
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wartość wyrażenia liczbowego.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład zwraca liczbę stopni w pod kątem PI/2 radianów.  
  
```sql
SELECT DEGREES(PI()/2) AS degrees  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```json
[{"degrees": 90}]  
```  

## <a name="next-steps"></a>Następne kroki

- [Funkcje matematyczne Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do Azure Cosmos DB](introduction.md)
