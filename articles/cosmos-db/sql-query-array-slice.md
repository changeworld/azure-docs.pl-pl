---
title: ARRAY_SLICE w języku zapytań usługi Azure Cosmos DB
description: Dowiedz się, jak funkcja systemu SQL plasterka tablicy w usłudze Azure Cosmos DB zwraca część wyrażenia tablicy
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a98cb17d22f41776ff788d12ced6aa988ad0b10e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303328"
---
# <a name="array_slice-azure-cosmos-db"></a>ARRAY_SLICE (Usługa Azure Cosmos DB)
 Zwraca część wyrażenia tablicy.
  
## <a name="syntax"></a>Składnia
  
```sql
ARRAY_SLICE (<arr_expr>, <num_expr> [, <num_expr>])  
```  
  
## <a name="arguments"></a>Argumenty
  
*arr_expr*  
   Jest dowolne wyrażenie tablicy.  
  
*num_expr*  
   Indeks liczbowy oparty na wartości zerowej, od którego ma się rozpocząć tablicę. Wartości ujemne mogą służyć do określenia indeksu początkowego względem ostatniego elementu tablicy, czyli -1 odwołuje się do ostatniego elementu w tablicy.  

*num_expr* Opcjonalne wyrażenie liczbowe, które ustawia maksymalną liczbę elementów w tablicy wynikowej.    

## <a name="return-types"></a>Typy zwrotów
  
  Zwraca wyrażenie tablicy.  
  
## <a name="examples"></a>Przykłady
  
  W poniższym przykładzie pokazano, jak uzyskać `ARRAY_SLICE`różne wycinki tablicy za pomocą .  
  
```sql
SELECT
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1) AS s1,  
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1) AS s2,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 1) AS s3,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 2) AS s4,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 0) AS s5,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1000) AS s6,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, -100) AS s7      
  
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{  
           "s1": ["strawberries", "bananas"],   
           "s2": ["strawberries"],
           "s3": ["strawberries"],  
           "s4": ["strawberries", "bananas"], 
           "s5": [],
           "s6": ["strawberries", "bananas"],
           "s7": [] 
}]  
```  

## <a name="remarks"></a>Uwagi

Ta funkcja systemu nie będzie korzystać z indeksu.

## <a name="next-steps"></a>Następne kroki

- [Funkcje macierzy Usługi Azure Cosmos DB](sql-query-array-functions.md)
- [Funkcje systemowe Usługi Azure Cosmos DB](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
