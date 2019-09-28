---
title: PI w Azure Cosmos DB języku zapytań
description: Dowiedz się więcej o funkcji systemowej PI w Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 27832008e8922e339a648985192a58b111555bc9
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349655"
---
# <a name="pi-azure-cosmos-db"></a>PI (Azure Cosmos DB)
 Zwraca stałą wartość liczby PI.  
  
## <a name="syntax"></a>Składnia
  
```sql
PI ()  
```  
   
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wartość wyrażenia liczbowego.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład zwraca wartość `PI`.  
  
```sql
SELECT PI() AS pi 
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```json
[{"pi": 3.1415926535897931}]  
```  

## <a name="next-steps"></a>Następne kroki

- [Funkcje matematyczne Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do Azure Cosmos DB](introduction.md)
