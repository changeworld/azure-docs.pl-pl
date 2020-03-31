---
title: NIŻSZE w języku zapytań usługi Azure Cosmos DB
description: Dowiedz się więcej o funkcji systemu DOLNEJ CZĘŚCI SQL w usłudze Azure Cosmos DB w celu zwrócenia wyrażenia ciągu po przekonwertowaniu danych znaków wielkich liter na małe litery
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 80dba57d4fe05630eb5ae4f8fc96bd0aa214c6d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302274"
---
# <a name="lower-azure-cosmos-db"></a>LOWER (Azure Cosmos DB)
 Zwraca wyrażenie ciągu po przekonwertowaniu danych znakowych wielkich liter na małe litery.  

Funkcja systemu LOWER nie wykorzystuje indeksu. Jeśli planujesz zrobić częste porównania bez uwzględniania przypadków, funkcja systemu LOWER może zużywać znaczną ilość RU. W takim przypadku zamiast używać funkcji systemu LOWER do normalizacji danych za każdym razem do porównań, można znormalizować obudowę po wstawieniu. Następnie kwerenda, taka jak SELECT * FROM c WHERE LOWER(c.name) = 'bob' po prostu staje się SELECT * FROM c GDZIE c.name = 'bob'.

## <a name="syntax"></a>Składnia
  
```sql
LOWER(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Jest wyrażeniem ciągu.  
  
## <a name="return-types"></a>Typy zwrotów
  
  Zwraca wyrażenie ciągu.  
  
## <a name="examples"></a>Przykłady
  
  W poniższym przykładzie `LOWER` pokazano, jak używać w kwerendzie.  
  
```sql
SELECT LOWER("Abc") AS lower
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{"lower": "abc"}]  
  
```  

## <a name="remarks"></a>Uwagi

Ta funkcja systemu nie będzie korzystać z indeksu.

## <a name="next-steps"></a>Następne kroki

- [Funkcje ciągów usługi Azure Cosmos DB](sql-query-string-functions.md)
- [Funkcje systemowe Usługi Azure Cosmos DB](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
