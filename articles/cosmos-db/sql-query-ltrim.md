---
title: LTRIM w języku zapytań usługi Azure Cosmos DB
description: Dowiedz się więcej o funkcji systemu SQL LTRIM w usłudze Azure Cosmos DB, aby zwrócić wyrażenie ciągu po usunięciu początkowych pustych miejsc
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 08c069de70684a8562e86963ddb2e84ee889e7cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302257"
---
# <a name="ltrim-azure-cosmos-db"></a>LTRIM (usługa Azure Cosmos DB)
 Zwraca wyrażenie ciągu po usunięciu spacji wiodących.  
  
## <a name="syntax"></a>Składnia
  
```sql
LTRIM(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Jest wyrażeniem ciągu.  
  
## <a name="return-types"></a>Typy zwrotów
  
  Zwraca wyrażenie ciągu.  
  
## <a name="examples"></a>Przykłady
  
  W poniższym przykładzie `LTRIM` pokazano, jak używać wewnątrz kwerendy.  
  
```sql
SELECT LTRIM("  abc") AS l1, LTRIM("abc") AS l2, LTRIM("abc   ") AS l3 
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{"l1": "abc", "l2": "abc", "l3": "abc   "}]  
```  

## <a name="remarks"></a>Uwagi

Ta funkcja systemu nie będzie korzystać z indeksu.

## <a name="next-steps"></a>Następne kroki

- [Funkcje ciągów usługi Azure Cosmos DB](sql-query-string-functions.md)
- [Funkcje systemowe Usługi Azure Cosmos DB](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
