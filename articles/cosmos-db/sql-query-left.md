---
title: Pozostało w Azure Cosmos DB języku zapytań
description: Dowiedz się więcej o funkcji systemu SQL pozostałej w Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 0eac35a91e4d5158335d6797d49a09f8f6f391e3
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303753"
---
# <a name="left-azure-cosmos-db"></a>Z lewej (Azure Cosmos DB)
 Zwraca lewą część ciągu z określoną liczbą znaków.  
  
## <a name="syntax"></a>Składnia
  
```sql
LEFT(<str_expr>, <num_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Jest wyrażeniem ciągu, z którego mają zostać wyodrębnione znaki.  
  
*num_expr*  
   Jest wyrażeniem liczbowym określającym liczbę znaków.  
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wyrażenie ciągu.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład zwraca lewej części "abc" dla różnych wartości długości.  
  
```sql
SELECT LEFT("abc", 1) AS l1, LEFT("abc", 2) AS l2 
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{"l1": "a", "l2": "ab"}]  
```  

## <a name="remarks"></a>Uwagi

Ta funkcja systemowa będzie korzystać z [indeksu zakresu](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Następne kroki

- [Azure Cosmos DB funkcje ciągów](sql-query-string-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do Azure Cosmos DB](introduction.md)
