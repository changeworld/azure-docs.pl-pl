---
title: DŁUGOŚĆ w języku zapytań usługi Azure Cosmos DB
description: Dowiedz się więcej o funkcji systemu SQL DŁUGOŚĆ w usłudze Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: e020555b0c706b5577bd20ac9bd537604d43ba3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303719"
---
# <a name="length-azure-cosmos-db"></a>DŁUGOŚĆ (usługa Azure Cosmos DB)
 Zwraca liczbę znaków określonego wyrażenia ciągu.  
  
## <a name="syntax"></a>Składnia
  
```sql
LENGTH(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Jest wyrażeniem ciągu, które ma zostać ocenione.  
  
## <a name="return-types"></a>Typy zwrotów
  
  Zwraca wyrażenie liczbowe.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład zwraca długość ciągu.  
  
```sql
SELECT LENGTH("abc") AS len 
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{"len": 3}]  
```  

## <a name="remarks"></a>Uwagi

Ta funkcja systemu nie będzie korzystać z indeksu.

## <a name="next-steps"></a>Następne kroki

- [Funkcje ciągów usługi Azure Cosmos DB](sql-query-string-functions.md)
- [Funkcje systemowe Usługi Azure Cosmos DB](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
