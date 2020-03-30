---
title: CONCAT w języku zapytań usługi Azure Cosmos DB
description: Dowiedz się, jak funkcja systemu SQL CONCAT w usłudze Azure Cosmos DB zwraca ciąg, który jest wynikiem łączenia dwóch lub więcej wartości ciągu
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c8a0941376ed74d7f8cb819d78df43eb9f0b7bd1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302614"
---
# <a name="concat-azure-cosmos-db"></a>CONCAT (usługa Azure Cosmos DB)
 Zwraca ciąg, który jest wynikiem połączenia co najmniej dwóch wartości ciągu.  
  
## <a name="syntax"></a>Składnia
  
```sql
CONCAT(<str_expr1>, <str_expr2> [, <str_exprN>])  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Jest wyrażeniem ciągu do łączenia z innymi wartościami. Funkcja `CONCAT` wymaga co najmniej dwóch *str_expr* argumentów.  
  
## <a name="return-types"></a>Typy zwrotów
  
  Zwraca wyrażenie ciągu.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład zwraca konkadyjowany ciąg określonych wartości.  
  
```sql
SELECT CONCAT("abc", "def") AS concat  
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{"concat": "abcdef"}]  
```  
  
## <a name="remarks"></a>Uwagi

Ta funkcja systemu nie będzie korzystać z indeksu.

## <a name="next-steps"></a>Następne kroki

- [Funkcje ciągów usługi Azure Cosmos DB](sql-query-string-functions.md)
- [Funkcje systemowe Usługi Azure Cosmos DB](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
