---
title: POWER w języku zapytań usługi Azure Cosmos DB
description: Dowiedz się więcej o funkcji systemu SQL POWER w usłudze Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 683c53c369f136ad4b917b93e9a92a71072d05e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71349632"
---
# <a name="power-azure-cosmos-db"></a>POWER (Azure Cosmos DB)
 Zwraca wartość określonego wyrażenia do określonej mocy.  
  
## <a name="syntax"></a>Składnia
  
```sql
POWER (<numeric_expr1>, <numeric_expr2>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr1*  
   Jest wyrażeniem liczbowym.  
  
*numeric_expr2*  
   Czy moc, do której można podnieść *numeric_expr1*.  
  
## <a name="return-types"></a>Typy zwrotów
  
  Zwraca wyrażenie liczbowe.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład pokazuje podniesienie liczby do potęgi 3 (moduł liczby).  
  
```sql
SELECT POWER(2, 3) AS pow1, POWER(2.5, 3) AS pow2  
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{pow1: 8, pow2: 15.625}]  
```  

## <a name="next-steps"></a>Następne kroki

- [Funkcje matematyczne Usługi Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funkcje systemowe Usługi Azure Cosmos DB](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
