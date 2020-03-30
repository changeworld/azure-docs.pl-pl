---
title: REPLIKACJA w języku kwerend usługi Azure Cosmos DB
description: Dowiedz się więcej o replikacji funkcji systemu SQL w usłudze Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 19fcde522c5cb0355e53a5616145f27fada7dad9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302189"
---
# <a name="replicate-azure-cosmos-db"></a>REPLIKA (Usługa Azure Cosmos DB)
 Powtarza wartość ciągu określoną liczbę razy.
  
## <a name="syntax"></a>Składnia
  
```sql
REPLICATE(<str_expr>, <num_expr>)
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Jest wyrażeniem ciągu.
  
*num_expr*  
   Jest wyrażeniem liczbowym. Jeśli *num_expr* jest ujemna lub nie skończona, wynik jest niezdefiniowany.
  
## <a name="return-types"></a>Typy zwrotów
  
  Zwraca wyrażenie ciągu.
  
## <a name="remarks"></a>Uwagi
  Maksymalna długość wyniku wynosi 10 000 znaków, czyli (długość(*str_expr*) * *num_expr*) <= 10 000.

## <a name="examples"></a>Przykłady
  
  W poniższym przykładzie `REPLICATE` pokazano, jak używać w kwerendzie.
  
```sql
SELECT REPLICATE("a", 3) AS replicate
```  
  
 Tutaj znajduje się zestaw wyników.
  
```json
[{"replicate": "aaa"}]
```  

## <a name="remarks"></a>Uwagi

Ta funkcja systemu nie będzie korzystać z indeksu.

## <a name="next-steps"></a>Następne kroki

- [Funkcje ciągów usługi Azure Cosmos DB](sql-query-string-functions.md)
- [Funkcje systemowe Usługi Azure Cosmos DB](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
