---
title: Język zapytań dodatku Azure Cosmos DB
description: Dowiedz się więcej o MOŻLIWOŚCIach systemu SQL w Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 683c53c369f136ad4b917b93e9a92a71072d05e0
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349632"
---
# <a name="power-azure-cosmos-db"></a>MOC (Azure Cosmos DB)
 Zwraca wartość z określonego wyrażenia do określonej potęgi.  
  
## <a name="syntax"></a>Składnia
  
```sql
POWER (<numeric_expr1>, <numeric_expr2>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr1*  
   To wyrażenie liczbowe.  
  
*numeric_expr2*  
   Jest potęgą, do której należy podnieść *numeric_expr1*.  
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wartość wyrażenia liczbowego.  
  
## <a name="examples"></a>Przykłady
  
  W poniższym przykładzie pokazano, zwiększenie liczby do potęgi 3 (moduł liczba).  
  
```sql
SELECT POWER(2, 3) AS pow1, POWER(2.5, 3) AS pow2  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```json
[{pow1: 8, pow2: 15.625}]  
```  

## <a name="next-steps"></a>Następne kroki

- [Funkcje matematyczne Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do Azure Cosmos DB](introduction.md)
