---
title: ACOS w języku zapytań usługi Azure Cosmos DB
description: Dowiedz się, jak funkcja systemu SQL ACOS (arccosice) w usłudze Azure Cosmos DB zwraca kąt w radianach, których cosine jest określonym wyrażeniem liczbowym
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 733d6b009f03d61c37170cc506a3b2ec842d7c47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78300965"
---
# <a name="acos-azure-cosmos-db"></a>ACOS (Usługa Azure Cosmos DB)
 Zwraca kąt w radianach, którego cosinus jest określonym wyrażeniem liczbowym. Ta funkcja jest również nazywana arcus cosinus.  
  
## <a name="syntax"></a>Składnia
  
```sql
ACOS(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   Jest wyrażeniem liczbowym.  
  
## <a name="return-types"></a>Typy zwrotów
  
  Zwraca wyrażenie liczbowe.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład `ACOS` zwraca wartość -1.  
  
```sql
SELECT ACOS(-1) AS acos 
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{"acos": 3.1415926535897931}]  
```  

## <a name="remarks"></a>Uwagi

Ta funkcja systemu nie będzie korzystać z indeksu.

## <a name="next-steps"></a>Następne kroki

- [Funkcje matematyczne Usługi Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funkcje systemowe Usługi Azure Cosmos DB](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
