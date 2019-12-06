---
title: ASIN w języku zapytań Azure Cosmos DB
description: Dowiedz się więcej o tym, jak Funkcja systemowa SQL ASIN (sinus) w Azure Cosmos DB zwraca kąt w radianach, którego sinus jest określonym wyrażeniem liczbowym
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 3e790eb5ae5eb780637b199a1a65dec2dd02d1bc
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871742"
---
# <a name="asin-azure-cosmos-db"></a>ASIN (Azure Cosmos DB)
 Zwraca kąt w radianach, którego sinus jest określonym wyrażeniem liczbowym. Jest to również nazywane arcus sinus.  
  
## <a name="syntax"></a>Składnia
  
```sql
ASIN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   To wyrażenie liczbowe.  
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wartość wyrażenia liczbowego.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład zwraca `ASIN`-1.  
  
```sql
SELECT ASIN(-1) AS asin  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```json
[{"asin": -1.5707963267948966}]  
```  

## <a name="next-steps"></a>Następne kroki

- [Funkcje matematyczne Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do Azure Cosmos DB](introduction.md)
