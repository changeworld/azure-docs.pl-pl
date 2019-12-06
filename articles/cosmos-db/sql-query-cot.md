---
title: COT w języku zapytań Azure Cosmos DB
description: Dowiedz się więcej o tym, jak Funkcja systemowa "COT" w języku SQL w Azure Cosmos DB zwraca miarę trygonometryczną o określonym kącie, w radianach, w określonym wyrażeniu liczbowym
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: f049d1295eef3e6a45abeaafe8c22d376f90abe1
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871503"
---
# <a name="cot-azure-cosmos-db"></a>COT (Azure Cosmos DB)
 Zwraca trygonometryczny cotangens określonego kąta w radianach w określonym wyrażeniu liczbowym.  
  
## <a name="syntax"></a>Składnia
  
```sql
COT(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   To wyrażenie liczbowe.  
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wartość wyrażenia liczbowego.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład oblicza `COT` określonego kąta.  
  
```sql
SELECT COT(124.1332) AS cot  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```json
[{"cot": -0.040311998371148884}]  
```  
  

## <a name="next-steps"></a>Następne kroki

- [Funkcje matematyczne Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do Azure Cosmos DB](introduction.md)
