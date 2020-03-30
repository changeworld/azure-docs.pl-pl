---
title: W lewo w języku kwerendy usługi Azure Cosmos DB
description: Dowiedz się więcej o funkcji systemu SQL left w usłudze Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 0eac35a91e4d5158335d6797d49a09f8f6f391e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303753"
---
# <a name="left-azure-cosmos-db"></a>PO LEWEJ (Usługa Azure Cosmos DB)
 Zwraca lewą część ciągu z określoną liczbą znaków.  
  
## <a name="syntax"></a>Składnia
  
```sql
LEFT(<str_expr>, <num_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Jest wyrażeniem ciągu, z wycięcie znaków.  
  
*num_expr*  
   Jest wyrażeniem liczbowym określającym liczbę znaków.  
  
## <a name="return-types"></a>Typy zwrotów
  
  Zwraca wyrażenie ciągu.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład zwraca lewą część "abc" dla różnych wartości długości.  
  
```sql
SELECT LEFT("abc", 1) AS l1, LEFT("abc", 2) AS l2 
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{"l1": "a", "l2": "ab"}]  
```  

## <a name="remarks"></a>Uwagi

Ta funkcja systemu będzie korzystać z [indeksu zakresu](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Następne kroki

- [Funkcje ciągów usługi Azure Cosmos DB](sql-query-string-functions.md)
- [Funkcje systemowe Usługi Azure Cosmos DB](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
