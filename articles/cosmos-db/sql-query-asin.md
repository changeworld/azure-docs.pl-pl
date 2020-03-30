---
title: ASIN w języku zapytań usługi Azure Cosmos DB
description: Dowiedz się, jak funkcja systemu SQL Arcsine (ASIN) w usłudze Azure Cosmos DB zwraca kąt w radianach, których sinus jest określonym wyrażeniem liczbowym
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8b70738a439b6c64a84a63adf63c83995530e92e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302699"
---
# <a name="asin-azure-cosmos-db"></a>ASIN (usługa Azure Cosmos DB)
 Zwraca kąt w radianach, którego sinus jest określonym wyrażeniem liczbowym. Jest to również nazywane arcsine.  
  
## <a name="syntax"></a>Składnia
  
```sql
ASIN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   Jest wyrażeniem liczbowym.  
  
## <a name="return-types"></a>Typy zwrotów
  
  Zwraca wyrażenie liczbowe.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład `ASIN` zwraca wartość -1.  
  
```sql
SELECT ASIN(-1) AS asin  
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{"asin": -1.5707963267948966}]  
```  

## <a name="remarks"></a>Uwagi

Ta funkcja systemu nie będzie korzystać z indeksu.

## <a name="next-steps"></a>Następne kroki

- [Funkcje matematyczne Usługi Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funkcje systemowe Usługi Azure Cosmos DB](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
