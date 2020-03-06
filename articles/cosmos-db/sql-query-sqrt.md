---
title: Język SQRT w Azure Cosmos DB języku zapytań
description: Informacje o funkcji systemowej SQRT języka SQL w Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 3d5452e5ba650b487acff03f622cdbb170275dce
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303464"
---
# <a name="sqrt-azure-cosmos-db"></a>SQRT (Azure Cosmos DB)
 Zwraca pierwiastek kwadratowy z określoną wartość liczbową.  
  
## <a name="syntax"></a>Składnia
  
```sql
SQRT(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   To wyrażenie liczbowe.  
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wartość wyrażenia liczbowego.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład zwraca pierwiastki kwadratowe liczb od 1 do 3.  
  
```sql
SELECT SQRT(1) AS s1, SQRT(2.0) AS s2, SQRT(3) AS s3  
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{s1: 1, s2: 1.4142135623730952, s3: 1.7320508075688772}]  
```  

## <a name="remarks"></a>Uwagi

Ta funkcja systemowa nie będzie używać indeksu.

## <a name="next-steps"></a>Następne kroki

- [Funkcje matematyczne Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do Azure Cosmos DB](introduction.md)
