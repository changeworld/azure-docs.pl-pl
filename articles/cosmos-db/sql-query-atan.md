---
title: ATAN w języku zapytań Azure Cosmos DB
description: Dowiedz się więcej o tym, jak Funkcja systemowa SQL ATAN (tangens) w Azure Cosmos DB zwraca kąt w radianach, którego tangens to określone wyrażenie liczbowe
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8fd398e343a3e617ceb9f49ca8f31ee776a7f073
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873458"
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
  
 W tym miejscu znajduje się zestaw wyników.  
  
```json
[{"atan": -1.5485826962062663}]  
```  
  

## <a name="next-steps"></a>Następne kroki

- [Funkcje matematyczne Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do Azure Cosmos DB](introduction.md)
