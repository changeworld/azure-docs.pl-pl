---
title: RTRIM w języku zapytań usługi Azure Cosmos DB
description: Dowiedz się więcej o funkcji systemu SQL RTRIM w usłudze Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b740d14315f6d9ba2f1788c56d6b1fcd8945c83e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302087"
---
# <a name="rtrim-azure-cosmos-db"></a>RTRIM (Usługa Azure Cosmos DB)
 Zwraca wyrażenie ciągu po usunięciu końcowych pustych miejsc.  
  
## <a name="syntax"></a>Składnia
  
```sql
RTRIM(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Jest dowolnym prawidłowym wyrażeniem ciągu.  
  
## <a name="return-types"></a>Typy zwrotów
  
  Zwraca wyrażenie ciągu.  
  
## <a name="examples"></a>Przykłady
  
  W poniższym przykładzie `RTRIM` pokazano, jak używać wewnątrz kwerendy.  
  
```sql
SELECT RTRIM("  abc") AS r1, RTRIM("abc") AS r2, RTRIM("abc   ") AS r3  
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{"r1": "   abc", "r2": "abc", "r3": "abc"}]  
```  

## <a name="remarks"></a>Uwagi

Ta funkcja systemu nie będzie korzystać z indeksu.

## <a name="next-steps"></a>Następne kroki

- [Funkcje ciągów usługi Azure Cosmos DB](sql-query-string-functions.md)
- [Funkcje systemowe Usługi Azure Cosmos DB](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
