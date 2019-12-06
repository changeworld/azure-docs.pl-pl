---
title: CONCAT w Azure Cosmos DB języku zapytań
description: Dowiedz się, w jaki sposób funkcja CONCAT systemu SQL w Azure Cosmos DB zwraca ciąg, który jest wynikiem łączenia dwóch lub więcej wartości ciągu
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: adb8564d4addaaa3f4a383b32941549aed3d53bd
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871554"
---
# <a name="concat-azure-cosmos-db"></a>CONCAT (Azure Cosmos DB)
 Zwraca ciąg, który jest wynikiem połączenia co najmniej dwóch wartości ciągu.  
  
## <a name="syntax"></a>Składnia
  
```sql
CONCAT(<str_expr1>, <str_expr2> [, <str_exprN>])  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Jest wyrażeniem ciągu do łączenia z innymi wartościami. Funkcja `CONCAT` wymaga co najmniej dwóch argumentów *str_expr* .  
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wyrażenie ciągu.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład zwraca połączony ciąg z określonymi wartościami.  
  
```sql
SELECT CONCAT("abc", "def") AS concat  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```json
[{"concat": "abcdef"}]  
```  
  

## <a name="next-steps"></a>Następne kroki

- [Azure Cosmos DB funkcje ciągów](sql-query-string-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do Azure Cosmos DB](introduction.md)
