---
title: COS w Azure Cosmos DB języku zapytań
description: Dowiedz się, w jaki sposób Funkcja systemowa SQL cosinus (COS) w Azure Cosmos DB zwraca kąt w radianach, który jest określony dla danego kąta, przy użyciu radiany, w określonym wyrażeniu
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 05546ef531059e05a1426a288a48446d63d8e5df
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873407"
---
# <a name="cos-azure-cosmos-db"></a>COS (Azure Cosmos DB)
 Zwraca trygonometryczny cosinus określonego kąta w radianach w określonym wyrażeniu.  
  
## <a name="syntax"></a>Składnia
  
```sql
COS(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   To wyrażenie liczbowe.  
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wartość wyrażenia liczbowego.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład oblicza `COS` określonego kąta.  
  
```sql
SELECT COS(14.78) AS cos  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```json
[{"cos": -0.59946542619465426}]  
```  

## <a name="next-steps"></a>Następne kroki

- [Funkcje matematyczne Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do Azure Cosmos DB](introduction.md)
