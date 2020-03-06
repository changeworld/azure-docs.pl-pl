---
title: ACOS w języku zapytań Azure Cosmos DB
description: Dowiedz się, jak Funkcja systemowa SQL ACOS (arccosice) w Azure Cosmos DB zwraca kąt w radianach, którego cosinus jest określonym wyrażeniem liczbowym
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 733d6b009f03d61c37170cc506a3b2ec842d7c47
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78300965"
---
# <a name="acos-azure-cosmos-db"></a>ACOS (Azure Cosmos DB)
 Zwraca kąt w radianach, którego cosinus jest określonym wyrażeniem liczbowym. Ta funkcja jest również nazywana arcus cosinus.  
  
## <a name="syntax"></a>Składnia
  
```sql
ACOS(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   To wyrażenie liczbowe.  
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wartość wyrażenia liczbowego.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład zwraca `ACOS`-1.  
  
```sql
SELECT ACOS(-1) AS acos 
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{"acos": 3.1415926535897931}]  
```  

## <a name="remarks"></a>Uwagi

Ta funkcja systemowa nie będzie używać indeksu.

## <a name="next-steps"></a>Następne kroki

- [Funkcje matematyczne Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do Azure Cosmos DB](introduction.md)
