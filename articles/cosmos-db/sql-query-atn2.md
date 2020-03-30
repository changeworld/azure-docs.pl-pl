---
title: AtN2 w języku zapytań usługi Azure Cosmos DB
description: Dowiedz się, jak funkcja systemu SQL ATN2 w usłudze Azure Cosmos DB zwraca główną wartość stycznej łuku y/x, wyrażoną w radianach
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 696e14e75998ead04c99fab2b84fc4c742d5f54a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302665"
---
# <a name="atn2-azure-cosmos-db"></a>ATN2 (usługa Azure Cosmos DB)
 Zwraca główną wartość stycznej łuku y/x, wyrażoną w radianach.  
  
## <a name="syntax"></a>Składnia
  
```sql
ATN2(<numeric_expr>, <numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   Jest wyrażeniem liczbowym.  
  
## <a name="return-types"></a>Typy zwrotów
  
  Zwraca wyrażenie liczbowe.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład oblicza ATN2 dla określonych komponentów x i y.  
  
```sql
SELECT ATN2(35.175643, 129.44) AS atn2  
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{"atn2": 1.3054517947300646}]  
```  

## <a name="remarks"></a>Uwagi

Ta funkcja systemu nie będzie korzystać z indeksu.

## <a name="next-steps"></a>Następne kroki

- [Funkcje matematyczne Usługi Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funkcje systemowe Usługi Azure Cosmos DB](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
