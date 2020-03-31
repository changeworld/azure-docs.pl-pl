---
title: ARRAY_LENGTH w języku zapytań usługi Azure Cosmos DB
description: Dowiedz się, jak funkcja systemu SQL o długości tablicy w usłudze Azure Cosmos DB zwraca liczbę elementów określonego wyrażenia tablicy
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 3596ce4bc702d5e54225d8c90db2f9563feab670
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303991"
---
# <a name="array_length-azure-cosmos-db"></a>ARRAY_LENGTH (Usługa Azure Cosmos DB)
 Zwraca liczbę elementów określonego wyrażenia tablicy.  
  
## <a name="syntax"></a>Składnia
  
```sql
ARRAY_LENGTH(<arr_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*arr_expr*  
   Jest wyrażeniem tablicy.  
  
## <a name="return-types"></a>Typy zwrotów
  
  Zwraca wyrażenie liczbowe.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład, jak uzyskać długość `ARRAY_LENGTH`tablicy przy użyciu .  
  
```sql
SELECT ARRAY_LENGTH(["apples", "strawberries", "bananas"]) AS len  
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{"len": 3}]  
```  
  
## <a name="remarks"></a>Uwagi

Ta funkcja systemu nie będzie korzystać z indeksu.

## <a name="next-steps"></a>Następne kroki

- [Funkcje macierzy Usługi Azure Cosmos DB](sql-query-array-functions.md)
- [Funkcje systemowe Usługi Azure Cosmos DB](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
