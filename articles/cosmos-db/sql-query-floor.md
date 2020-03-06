---
title: Język zapytań w Azure Cosmos DB
description: Dowiedz się więcej na temat funkcji systemowej SQL FLOOR w Azure Cosmos DB, aby zwrócić największą liczbę całkowitą mniejszą lub równą określonemu wyrażeniu liczbowym
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 04dfa6a028cf7c44bf99c665b396d51d8a0f3cef
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303192"
---
# <a name="floor-azure-cosmos-db"></a>PIĘTRo (Azure Cosmos DB)
 Zwraca największą wartość całkowitą równą określonemu wyrażeniu liczbowemu lub mniejszą.  
  
## <a name="syntax"></a>Składnia
  
```sql
FLOOR (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   To wyrażenie liczbowe.  
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wartość wyrażenia liczbowego.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład pokazuje dodatnie wartości liczbowe, ujemne i zerowe z funkcją `FLOOR`.  
  
```sql
SELECT FLOOR(123.45) AS fl1, FLOOR(-123.45) AS fl2, FLOOR(0.0) AS fl3  
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{fl1: 123, fl2: -124, fl3: 0}]  
```

## <a name="remarks"></a>Uwagi

Ta funkcja systemowa będzie korzystać z [indeksu zakresu](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Następne kroki

- [Funkcje matematyczne Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do Azure Cosmos DB](introduction.md)
