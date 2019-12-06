---
title: Język zapytań w Azure Cosmos DB
description: Dowiedz się więcej na temat funkcji systemowej SQL FLOOR w Azure Cosmos DB, aby zwrócić największą liczbę całkowitą mniejszą lub równą określonemu wyrażeniu liczbowym
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8faf223a4871f6f109f9d133716989b1bcfdee08
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871180"
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
  
 W tym miejscu znajduje się zestaw wyników.  
  
```json
[{fl1: 123, fl2: -124, fl3: 0}]  
```  

## <a name="next-steps"></a>Następne kroki

- [Funkcje matematyczne Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do Azure Cosmos DB](introduction.md)
