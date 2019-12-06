---
title: STOPnie w Azure Cosmos DB języku zapytań
description: Dowiedz się więcej o STOPNIach funkcji systemowej SQL w Azure Cosmos DB, aby zwrócić odpowiedni kąt w stopniach dla kąta określonego w radianach
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5f432f5ef57c8eccc0cf629f00d2231364785b9a
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871333"
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
