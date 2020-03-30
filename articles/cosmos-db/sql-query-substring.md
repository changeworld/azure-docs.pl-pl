---
title: PODCIĄGNIE w języku kwerend usługi Azure Cosmos DB
description: Dowiedz się więcej o funkcji systemu SQL SUBSTRING w usłudze Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d4462fc407093b23510bddfae4d9f55d68f8c0fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303702"
---
# <a name="substring-azure-cosmos-db"></a>PODCIĄGNIE (Usługa Azure Cosmos DB)
 Zwraca część wyrażenia ciągu, zaczynając od określonego znaku zerowego i kontynuuje do określonej długości lub na końcu ciągu.  
  
## <a name="syntax"></a>Składnia
  
```sql
SUBSTRING(<str_expr>, <num_expr1>, <num_expr2>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Jest wyrażeniem ciągu.
  
*num_expr1*  
   Jest wyrażeniem liczbowym oznaczanym znakiem początkowym. Wartość 0 jest pierwszym znakiem *str_expr*.
  
*num_expr2*  
   Jest wyrażeniem liczbowym oznaczającego maksymalną liczbę znaków *str_expr* do zwrotu. Wartość 0 lub mniej powoduje pusty ciąg.

## <a name="return-types"></a>Typy zwrotów
  
  Zwraca wyrażenie ciągu.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład zwraca podciąg "abc" począwszy od 1 i dla długości 1 znak.  
  
```sql
SELECT SUBSTRING("abc", 1, 1) AS substring  
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{"substring": "b"}]  
```

## <a name="remarks"></a>Uwagi

Ta funkcja systemu będzie korzystać z [indeksu zakresu,](index-policy.md#includeexclude-strategy) jeśli pozycja początkowa jest `0`.

## <a name="next-steps"></a>Następne kroki

- [Funkcje ciągów usługi Azure Cosmos DB](sql-query-string-functions.md)
- [Funkcje systemowe Usługi Azure Cosmos DB](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
