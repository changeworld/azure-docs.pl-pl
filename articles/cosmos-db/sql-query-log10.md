---
title: DZIENNIK 10 w języku kwerend usługi Azure Cosmos DB
description: Dowiedz się więcej o funkcji systemu SQL LOG10 w usłudze Azure Cosmos DB w celu zwrócenia logarytmu base-10 określonego wyrażenia liczbowego
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6f47200f6978d91f46c010640bb9c2bb26e9b7d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302495"
---
# <a name="log10-azure-cosmos-db"></a>LOG10 (usługa Azure Cosmos DB)
 Zwraca logarytm base-10 określonego wyrażenia liczbowego.  
  
## <a name="syntax"></a>Składnia
  
```sql
LOG10 (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*Numeric_expression*  
   Jest wyrażeniem liczbowym.  
  
## <a name="return-types"></a>Typy zwrotów
  
  Zwraca wyrażenie liczbowe.  
  
## <a name="remarks"></a>Uwagi
  
  Funkcje LOG10 i POWER są ze sobą odwrotnie powiązane. Na przykład 10 ^ LOG10(n) = n.  
  
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

Ta funkcja systemu nie będzie korzystać z indeksu.

## <a name="next-steps"></a>Następne kroki

- [Funkcje matematyczne Usługi Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funkcje systemowe Usługi Azure Cosmos DB](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
