---
title: Log10 — w języku zapytań Azure Cosmos DB
description: Dowiedz się więcej na temat funkcji systemowej log10 — SQL w Azure Cosmos DB, aby zwrócić logarytm dziesiętny określonego wyrażenia liczbowego
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5950a2a40a78716a76e49fcdaeb3129948b6a0e1
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871061"
---
# <a name="log10-azure-cosmos-db"></a>LOG10 — (Azure Cosmos DB)
 Zwraca logarytm base 10 określonego wyrażenia liczbowego.  
  
## <a name="syntax"></a>Składnia
  
```sql
LOG10 (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expression*  
   To wyrażenie liczbowe.  
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wartość wyrażenia liczbowego.  
  
## <a name="remarks"></a>Uwagi
  
  LOG10 i możliwości funkcji odwrotnie są powiązane ze sobą. Na przykład 10 ^ LOG10(n) = n.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład deklaruje zmienną i zwraca wartość LOG10 określonej zmiennej (100).  
  
```sql
SELECT LOG10(100) AS log10 
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```json
[{log10: 2}]  
```  

## <a name="next-steps"></a>Następne kroki

- [Funkcje matematyczne Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do Azure Cosmos DB](introduction.md)
