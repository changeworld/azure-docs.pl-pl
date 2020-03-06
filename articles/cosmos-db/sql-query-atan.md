---
title: ATAN w języku zapytań Azure Cosmos DB
description: Dowiedz się więcej o tym, jak Funkcja systemowa SQL ATAN (tangens) w Azure Cosmos DB zwraca kąt w radianach, którego tangens to określone wyrażenie liczbowe
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 899c94a939be7825dca82522eab235bde9252896
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302682"
---
# <a name="atan-azure-cosmos-db"></a>ATAN (Azure Cosmos DB)
 Zwraca kąt w radianach, którego tangens jest określonym wyrażeniem liczbowym. Ta funkcja jest również nazywana arcus tangens.  
  
## <a name="syntax"></a>Składnia
  
```sql
ATAN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   To wyrażenie liczbowe.  
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wartość wyrażenia liczbowego.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład zwraca `ATAN` określonej wartości.  
  
```sql
SELECT ATAN(-45.01) AS atan  
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{"atan": -1.5485826962062663}]  
```  
  
## <a name="remarks"></a>Uwagi

Ta funkcja systemowa nie będzie używać indeksu.

## <a name="next-steps"></a>Następne kroki

- [Funkcje matematyczne Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do Azure Cosmos DB](introduction.md)
