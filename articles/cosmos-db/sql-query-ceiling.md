---
title: PUŁAP w Azure Cosmos DB języku zapytań
description: Dowiedz się, jak Funkcja systemowa SQL sufit w Azure Cosmos DB zwraca najmniejszą liczbę całkowitą większą lub równą określonemu wyrażeniu liczbowym.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 2da7820a6c9f1f90585b4deb605bb99c7580b0e5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75444802"
---
# <a name="ceiling-azure-cosmos-db"></a>GÓRNy limit (Azure Cosmos DB)
 Zwraca najmniejszą wartość całkowitą równą określonemu wyrażeniu liczbowemu lub większą.  
  
## <a name="syntax"></a>Składnia
  
```sql
CEILING (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   To wyrażenie liczbowe.  
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wartość wyrażenia liczbowego.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład pokazuje dodatnie wartości liczbowe, ujemne i zerowe z funkcją `CEILING`.  
  
```sql
SELECT CEILING(123.45) AS c1, CEILING(-123.45) AS c2, CEILING(0.0) AS c3  
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{c1: 124, c2: -123, c3: 0}]  
```  

## <a name="next-steps"></a>Następne kroki

- [Funkcje matematyczne Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do Azure Cosmos DB](introduction.md)
