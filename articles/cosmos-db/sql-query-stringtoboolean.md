---
title: StringToBoolean w języku zapytań Azure Cosmos DB
description: Dowiedz się więcej o funkcji StringToBoolean systemu SQL w Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8719857dca16585a045f8174dbac8df455f38f38
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349256"
---
# <a name="stringtoboolean-azure-cosmos-db"></a>StringToBoolean (Azure Cosmos DB)
 Zwraca wyrażenie tłumaczone na wartość logiczną. Jeśli wyrażenia nie można przetłumaczyć, funkcja zwraca wartość undefined.  
  
## <a name="syntax"></a>Składnia
  
```sql
StringToBoolean(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Jest wyrażeniem ciągu, które ma być analizowane jako wyrażenie logiczne.  
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wyrażenie logiczne lub niezdefiniowane.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład pokazuje, jak `StringToBoolean` zachowuje się w różnych typach. 
 
 Poniżej przedstawiono przykłady z prawidłowymi danymi wejściowymi.

Biały znak jest dozwolony tylko przed lub po "prawda"/"fałsz".

```sql
SELECT 
    StringToBoolean("true") AS b1, 
    StringToBoolean("    false") AS b2,
    StringToBoolean("false    ") AS b3
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```json
[{"b1": true, "b2": false, "b3": false}]
```  

Poniżej przedstawiono przykłady z nieprawidłowymi danymi wejściowymi.

 Wartości logiczne uwzględniają wielkość liter i muszą być zapisywane ze wszystkimi małymi literami, np. "true" i "false".

```sql
SELECT 
    StringToBoolean("TRUE"),
    StringToBoolean("False")
```  

W tym miejscu znajduje się zestaw wyników.  
  
```json
[{}]
``` 

Wyrażenie przesłane zostanie przeanalizowane jako wyrażenie logiczne; te dane wejściowe nie są obliczane do typu Boolean i w ten sposób zwracają niezdefiniowane.

```sql
SELECT 
    StringToBoolean("null"),
    StringToBoolean(undefined),
    StringToBoolean(NaN), 
    StringToBoolean(false), 
    StringToBoolean(true)
```  

W tym miejscu znajduje się zestaw wyników.  
  
```json
[{}]
```  

## <a name="next-steps"></a>Następne kroki

- [Azure Cosmos DB funkcje ciągów](sql-query-string-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do Azure Cosmos DB](introduction.md)
