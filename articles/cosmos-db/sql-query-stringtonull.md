---
title: StringToNull w języku zapytań Azure Cosmos DB
description: Dowiedz się więcej o funkcji StringToNull systemu SQL w Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c7ea3ca82cadb1351d8581f3d652ce060d67969b
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349250"
---
# <a name="stringtonull-azure-cosmos-db"></a>StringToNull (Azure Cosmos DB)
 Zwraca wyrażenie przetłumaczone na wartość null. Jeśli wyrażenia nie można przetłumaczyć, funkcja zwraca wartość undefined.  
  
## <a name="syntax"></a>Składnia
  
```sql
StringToNull(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Jest wyrażeniem ciągu, które ma być analizowane jako wyrażenie o wartości null.
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wyrażenie o wartości null lub niezdefiniowane.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład pokazuje, jak `StringToNull` zachowuje się w różnych typach. 

Poniżej przedstawiono przykłady z prawidłowymi danymi wejściowymi.

 Odstęp jest dozwolony tylko przed lub po "null".

```sql
SELECT 
    StringToNull("null") AS n1, 
    StringToNull("  null ") AS n2,
    IS_NULL(StringToNull("null   ")) AS n3
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```json
[{"n1": null, "n2": null, "n3": true}]
```  

Poniżej przedstawiono przykłady z nieprawidłowymi danymi wejściowymi.

Wartość null uwzględnia wielkość liter i musi być zapisana ze wszystkimi małymi literami, np. "null".

```sql
SELECT    
    StringToNull("NULL"),
    StringToNull("Null")
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```json
[{}]
```  

Wyrażenie przesłane zostanie przeanalizowane jako wyrażenie o wartości null; te dane wejściowe nie są oceniane do typu null i w rezultacie zwracają niezdefiniowane.

```sql
SELECT    
    StringToNull("true"), 
    StringToNull(false), 
    StringToNull(undefined),
    StringToNull(NaN) 
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```json
[{}]
```  

## <a name="next-steps"></a>Następne kroki

- [Azure Cosmos DB funkcje ciągów](sql-query-string-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do Azure Cosmos DB](introduction.md)
