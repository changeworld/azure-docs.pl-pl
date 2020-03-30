---
title: FLOOR w języku zapytań usługi Azure Cosmos DB
description: Dowiedz się więcej o funkcji systemu FLOOR SQL w usłudze Azure Cosmos DB, aby zwrócić największą wartość całkowitą mniejszą lub równą określonej wyrażeniu liczbowym
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 04dfa6a028cf7c44bf99c665b396d51d8a0f3cef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303192"
---
# <a name="floor-azure-cosmos-db"></a>FLOOR (Azure Cosmos DB)
 Zwraca największą wartość całkowitą równą określonemu wyrażeniu liczbowemu lub mniejszą.  
  
## <a name="syntax"></a>Składnia
  
```sql
FLOOR (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   Jest wyrażeniem liczbowym.  
  
## <a name="return-types"></a>Typy zwrotów
  
  Zwraca wyrażenie liczbowe.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład pokazuje dodatnie wartości liczbowe, `FLOOR` ujemne i zerowe z funkcją.  
  
```sql
SELECT FLOOR(123.45) AS fl1, FLOOR(-123.45) AS fl2, FLOOR(0.0) AS fl3  
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{fl1: 123, fl2: -124, fl3: 0}]  
```

## <a name="remarks"></a>Uwagi

Ta funkcja systemu będzie korzystać z [indeksu zakresu](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Następne kroki

- [Funkcje matematyczne Usługi Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funkcje systemowe Usługi Azure Cosmos DB](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
