---
title: SIN w Azure Cosmos DB języku zapytań
description: Dowiedz się więcej o funkcji SIN systemu SQL w Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 25e7cf66fdd55a0b641c35443e38b0a67cbe365d
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303107"
---
# <a name="sin-azure-cosmos-db"></a>SIN (Azure Cosmos DB)
 Zwraca trygonometryczny sinus określonego kąta w radianach w określonym wyrażeniu.  
  
## <a name="syntax"></a>Składnia
  
```sql
SIN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   To wyrażenie liczbowe.  
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wartość wyrażenia liczbowego.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład oblicza `SIN` określonego kąta.  
  
```sql
SELECT SIN(45.175643) AS sin  
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{"sin": 0.929607286611012}]  
```  

## <a name="remarks"></a>Uwagi

Ta funkcja systemowa nie będzie używać indeksu.

## <a name="next-steps"></a>Następne kroki

- [Funkcje matematyczne Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do Azure Cosmos DB](introduction.md)
