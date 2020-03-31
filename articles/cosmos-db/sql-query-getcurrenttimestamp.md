---
title: Plasowanie czasu w języku kwerend usługi Azure Cosmos DB
description: Dowiedz się więcej o funkcji systemu SQL GetCurrentTimestamp w usłudze Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b816c63da56025fe6e9cbaece2cde5dcd01585d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71351015"
---
# <a name="getcurrenttimestamp-azure-cosmos-db"></a>GetCurrentTimestamp (usługa Azure Cosmos DB)
 Zwraca liczbę milisekund, które upłynęły od 00:00:00 czwartek, 1 stycznia 1970. 
  
## <a name="syntax"></a>Składnia
  
```sql
GetCurrentTimestamp ()  
```  
  
## <a name="return-types"></a>Typy zwrotów
  
  Zwraca wartość liczbową, bieżącą liczbę milisekund, które upłynęły od epoki Uniksa, czyli liczby milisekund, które upłynęły od 00:00:00 czwartek, 1 stycznia 1970.

## <a name="remarks"></a>Uwagi

  GetCurrentTimestamp() jest funkcją niedeterministyczną.
  
  Wynik zwrócony jest UTC (Koordynowany czas uniwersalny).

## <a name="examples"></a>Przykłady
  
  W poniższym przykładzie pokazano, jak uzyskać bieżący znacznik czasu przy użyciu funkcji wbudowanej GetCurrentTimestamp().
  
```sql
SELECT GetCurrentTimestamp() AS currentUtcTimestamp
```  
  
 Oto przykładowy zestaw wyników.
  
```json
[{
  "currentUtcTimestamp": 1556916469065
}]  
```

## <a name="next-steps"></a>Następne kroki

- [Funkcje daty i godziny usługi Azure Cosmos DB](sql-query-date-time-functions.md)
- [Funkcje systemowe Usługi Azure Cosmos DB](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
