---
title: ARRAY_CONTAINS w języku zapytań Azure Cosmos DB
description: Dowiedz się, w jaki sposób tablica zawiera funkcję systemową SQL w Azure Cosmos DB zwraca wartość Boolean wskazującą, czy tablica zawiera określoną wartość
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 115667c93bbb832926a7939cf250a02b6fef8baa
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873492"
---
# <a name="array_contains-azure-cosmos-db"></a>ARRAY_CONTAINS (Azure Cosmos DB)
Zwraca wartość logiczną wskazującą, czy tablica zawiera określoną wartość. Można wyszukać częściowe lub pełne dopasowanie obiektu za pomocą wyrażenia logicznego w poleceniu. 

## <a name="syntax"></a>Składnia
  
```sql
ARRAY_CONTAINS (<arr_expr>, <expr> [, bool_expr])  
```  
  
## <a name="arguments"></a>Argumenty
  
*arr_expr*  
   Jest wyrażeniem tablicy, które ma być przeszukiwane.  
  
*expr*  
   Jest wyrażeniem, które ma zostać znalezione.  

*bool_expr*  
   Jest wyrażeniem logicznym. Jeśli wartość jest równa "true", a w przypadku określonej wartości wyszukiwania jest obiektem, polecenie sprawdza obecność częściowego dopasowania (obiekt wyszukiwania jest podzbiorem jednego z obiektów). Jeśli wartość jest równa "false", polecenie sprawdza pełne dopasowanie wszystkich obiektów w tablicy. Wartość domyślna, jeśli nie zostanie określona, jest równa false. 
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wartość typu Boolean.  
  
## <a name="examples"></a>Przykłady
  
  W poniższym przykładzie pokazano, jak sprawdzić członkostwo w tablicy przy użyciu `ARRAY_CONTAINS`.  
  
```sql
SELECT   
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "apples") AS b1,  
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "mangoes") AS b2  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```json
[{"b1": true, "b2": false}]  
```  

Poniższy przykład jak sprawdzić, czy częściowym JSON w tablicy przy użyciu ARRAY_CONTAINS.  
  
```sql
SELECT  
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}, true) AS b1, 
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}) AS b2,
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "mangoes"}, true) AS b3 
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```json
[{
  "b1": true,
  "b2": false,
  "b3": false
}] 
```  
  

## <a name="next-steps"></a>Następne kroki

- [Funkcje tablicowe Azure Cosmos DB](sql-query-array-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do Azure Cosmos DB](introduction.md)
