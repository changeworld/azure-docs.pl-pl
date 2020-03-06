---
title: Podciąg w Azure Cosmos DB języku zapytań
description: Dowiedz się więcej o podciągu funkcji systemu SQL w Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d4462fc407093b23510bddfae4d9f55d68f8c0fa
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303702"
---
# <a name="substring-azure-cosmos-db"></a>Podciąg (Azure Cosmos DB)
 Zwraca część wyrażenia ciągu, zaczynając od pozycji liczony od zera określony znak i kontynuuje do określonej długości lub do końca ciągu.  
  
## <a name="syntax"></a>Składnia
  
```sql
SUBSTRING(<str_expr>, <num_expr1>, <num_expr2>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Jest wyrażeniem ciągu.
  
*num_expr1*  
   Jest wyrażeniem liczbowym służącym do określenia znaku początkowego. Wartość 0 jest pierwszym znakiem *str_expr*.
  
*num_expr2*  
   Jest wyrażeniem liczbowym określającym maksymalną liczbę znaków *str_expr* do zwrócenia. Wartość 0 lub mniej skutkuje ciągiem pustym.

## <a name="return-types"></a>Typy zwracane
  
  Zwraca wyrażenie ciągu.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład zwraca podciąg "abc", począwszy od stawki 1 i długości 1 znak.  
  
```sql
SELECT SUBSTRING("abc", 1, 1) AS substring  
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{"substring": "b"}]  
```

## <a name="remarks"></a>Uwagi

Ta funkcja systemowa będzie korzystać z [indeksu zakresu](index-policy.md#includeexclude-strategy) , jeśli pozycja początkowa jest `0`.

## <a name="next-steps"></a>Następne kroki

- [Azure Cosmos DB funkcje ciągów](sql-query-string-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do Azure Cosmos DB](introduction.md)
