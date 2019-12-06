---
title: NIŻSZY w Azure Cosmos DB języku zapytań
description: Zapoznaj się z NIŻSZą funkcją systemu SQL w Azure Cosmos DB, aby zwrócić wyrażenie ciągu po przekonwertowaniu wielkich liter na małe litery
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8f7cd864d90b8ab17a180a00a7a6d4bb683da9be
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873305"
---
# <a name="lower-azure-cosmos-db"></a>NIŻSZY (Azure Cosmos DB)
 Zwraca wyrażenie ciągu po przekonwertowaniu danych znakowych wielkich liter na małe litery.  
  
## <a name="syntax"></a>Składnia
  
```sql
LOWER(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Jest wyrażeniem ciągu.  
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wyrażenie ciągu.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład pokazuje, jak używać `LOWER` w zapytaniu.  
  
```sql
SELECT LOWER("Abc") AS lower
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```json
[{"lower": "abc"}]  
  
```  

## <a name="next-steps"></a>Następne kroki

- [Azure Cosmos DB funkcje ciągów](sql-query-string-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do Azure Cosmos DB](introduction.md)
