---
title: ARRAY_CONTAINS w języku zapytań usługi Azure Cosmos DB
description: Dowiedz się, jak funkcja systemu Macierzy zawiera sql w usłudze Azure Cosmos DB zwraca wartość logiczną wskazującą, czy tablica zawiera określoną wartość
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 47fe20888aa546e414b268b30c2e03580750a040
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303481"
---
# <a name="array_contains-azure-cosmos-db"></a>ARRAY_CONTAINS (Usługa Azure Cosmos DB)
Zwraca wartość logiczną wskazującą, czy tablica zawiera określoną wartość. Można sprawdzić, czy częściowe lub pełne dopasowanie obiektu za pomocą wyrażenia logicznego w poleceniu. 

## <a name="syntax"></a>Składnia
  
```sql
ARRAY_CONTAINS (<arr_expr>, <expr> [, bool_expr])  
```  
  
## <a name="arguments"></a>Argumenty
  
*arr_expr*  
   Jest wyrażeniem tablicy, które ma być przeszukiwane.  
  
*Expr*  
   Czy wyrażenie można znaleźć.  

*bool_expr*  
   Jest wyrażeniem logicznym. Jeśli ma wartość "true" i jeśli określona wartość wyszukiwania jest obiektem, polecenie sprawdza, czy dopasowanie częściowe (obiekt wyszukiwania jest podzbiorem jednego z obiektów). Jeśli ma wartość "false", polecenie sprawdza pełne dopasowanie wszystkich obiektów w tablicy. Wartość domyślna, jeśli nie określono jest false. 
  
## <a name="return-types"></a>Typy zwrotów
  
  Zwraca wartość logiczną.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład, jak sprawdzić członkostwo `ARRAY_CONTAINS`w tablicy przy użyciu .  
  
```sql
SELECT   
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "apples") AS b1,  
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "mangoes") AS b2  
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{"b1": true, "b2": false}]  
```  

Poniższy przykład, jak sprawdzić, czy częściowe dopasowanie JSON w tablicy przy użyciu ARRAY_CONTAINS.  
  
```sql
SELECT  
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}, true) AS b1, 
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}) AS b2,
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "mangoes"}, true) AS b3 
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{
  "b1": true,
  "b2": false,
  "b3": false
}]
```

## <a name="remarks"></a>Uwagi

Ta funkcja systemu będzie korzystać z [indeksu zakresu](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Następne kroki

- [Funkcje macierzy Usługi Azure Cosmos DB](sql-query-array-functions.md)
- [Funkcje systemowe Usługi Azure Cosmos DB](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
