---
title: TAN w Azure Cosmos DB języku zapytań
description: Dowiedz się więcej na temat funkcji systemowej TAN w Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9d7187ba116067445e835769fc33aa70677ef80b
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78301985"
---
# <a name="tan-azure-cosmos-db"></a>TAN (Azure Cosmos DB)
 Zwraca tangens kąta określonego w radianach, określone wyrażenie.  
  
## <a name="syntax"></a>Składnia
  
```sql
TAN (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   To wyrażenie liczbowe.  
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wartość wyrażenia liczbowego.  
  
## <a name="examples"></a>Przykłady
  
  W poniższym przykładzie oblicza tangens liczby PI () / 2.  
  
```sql
SELECT TAN(PI()/2) AS tan 
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{"tan": 16331239353195370 }]  
```  

## <a name="remarks"></a>Uwagi

Ta funkcja systemowa nie będzie używać indeksu.

## <a name="next-steps"></a>Następne kroki

- [Funkcje matematyczne Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do Azure Cosmos DB](introduction.md)
