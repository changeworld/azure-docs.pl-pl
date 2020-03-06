---
title: Log10 — w języku zapytań Azure Cosmos DB
description: Dowiedz się więcej na temat funkcji systemowej log10 — SQL w Azure Cosmos DB, aby zwrócić logarytm dziesiętny określonego wyrażenia liczbowego
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6f47200f6978d91f46c010640bb9c2bb26e9b7d5
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302495"
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
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{log10: 2}]  
```  

## <a name="remarks"></a>Uwagi

Ta funkcja systemowa nie będzie używać indeksu.

## <a name="next-steps"></a>Następne kroki

- [Funkcje matematyczne Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do Azure Cosmos DB](introduction.md)
