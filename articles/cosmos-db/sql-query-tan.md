---
title: TAN w Azure Cosmos DB języku zapytań
description: Dowiedz się więcej na temat funkcji systemowej TAN w Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 70636dbc00307ced8ad0cf84f41fe025c759997e
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349165"
---
# <a name="tan-azure-cosmos-db"></a>TAN (Azure Cosmos DB)
 Zwraca tangens kąta określonego w radianach, określone wyrażenie.  
  
## <a name="syntax"></a>Składnia
  
```sql
TAN (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   To wyrażenie liczbowe.  
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wartość wyrażenia liczbowego.  
  
## <a name="examples"></a>Przykłady
  
  W poniższym przykładzie oblicza tangens liczby PI () / 2.  
  
```sql
SELECT TAN(PI()/2) AS tan 
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```json
[{"tan": 16331239353195370 }]  
```  

## <a name="next-steps"></a>Następne kroki

- [Funkcje matematyczne Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do Azure Cosmos DB](introduction.md)
