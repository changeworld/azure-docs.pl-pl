---
title: UPPER w języku zapytań usługi Azure Cosmos DB
description: Dowiedz się więcej o funkcji systemu SQL UPPER w usłudze Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5129b4fffafb6918f655263cac2f5564635acf36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303974"
---
# <a name="upper-azure-cosmos-db"></a>UPPER (Usługa Azure Cosmos DB)
 Zwraca wyrażenie ciągu po przekonwertowaniu danych znakowych małych liter na wielkie litery.  

Funkcja systemu UPPER nie korzysta z indeksu. Jeśli planujesz zrobić częste porównania bez uwzględniania przypadków, funkcja systemu UPPER może zużywać znaczną ilość RU. W takim przypadku zamiast używać funkcji systemu UPPER do normalizacji danych za każdym razem do porównań, można znormalizować obudową po wstawieniu. Następnie kwerenda, taka jak SELECT * FROM c WHERE UPPER(c.name) = 'BOB' po prostu staje się SELECT * FROM c GDZIE c.name = 'BOB'.

## <a name="syntax"></a>Składnia
  
```sql
UPPER(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Jest wyrażeniem ciągu.  
  
## <a name="return-types"></a>Typy zwrotów
  
  Zwraca wyrażenie ciągu.  
  
## <a name="examples"></a>Przykłady
  
  W poniższym przykładzie `UPPER` pokazano, jak używać w kwerendzie  
  
```sql
SELECT UPPER("Abc") AS upper  
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{"upper": "ABC"}]  
```

## <a name="remarks"></a>Uwagi

Ta funkcja systemu nie będzie korzystać z indeksu.

## <a name="next-steps"></a>Następne kroki

- [Funkcje ciągów usługi Azure Cosmos DB](sql-query-string-functions.md)
- [Funkcje systemowe Usługi Azure Cosmos DB](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
