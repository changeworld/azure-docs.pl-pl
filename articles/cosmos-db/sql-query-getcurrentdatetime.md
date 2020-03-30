---
title: GetCurrentDateTime w języku zapytań usługi Azure Cosmos DB
description: Dowiedz się więcej o funkcji systemu SQL GetCurrentDateTime w usłudze Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d50b08ab85c7e299c465c3eb6f34e867d6634006
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303906"
---
# <a name="getcurrentdatetime-azure-cosmos-db"></a>GetCurrentDateTime (usługa Azure Cosmos DB)
 Zwraca bieżącą datę i godzinę utc (koordynowany czas uniwersalny) jako ciąg ISO 8601.
  
## <a name="syntax"></a>Składnia
  
```sql
GetCurrentDateTime ()
```
  
## <a name="return-types"></a>Typy zwrotów
  
  Zwraca bieżącą wartość ciągu daty i godziny UTC ISO 8601 w formacie, `YYYY-MM-DDThh:mm:ss.fffffffZ` w którym:
  
  |||
  |-|-|
  |YYYY|czterocyfrowy rok|
  |MM|dwucyfrowy miesiąc (01 = styczeń itp.)|
  |DD|dwucyfrowy dzień miesiąca (od 01 do 31)|
  |T|oznaczającego początek elementów czasu|
  |hh|dwucyfrowa godzina (od 00 do 23)|
  |mm|dwucyfrowe minuty (od 00 do 59)|
  |ss|sekundy dwucyfrowe (od 00 do 59)|
  |.fffff|siedmiocyfrowe ułamki sekund|
  |Z|Utc (skoordynowany czas uniwersalny) - projektor||
  
  Aby uzyskać więcej informacji na temat formatu ISO 8601, zobacz [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

## <a name="remarks"></a>Uwagi

  GetCurrentDateTime() jest funkcją niedeterministyczną. 
  
  Wynik zwrócony jest UTC.

  Precyzja to 7 cyfr, z dokładnością 100 nanosekund.

## <a name="examples"></a>Przykłady
  
  W poniższym przykładzie pokazano, jak uzyskać bieżącą datę daty UTC przy użyciu funkcji wbudowanej GetCurrentDateTime().
  
```sql
SELECT GetCurrentDateTime() AS currentUtcDateTime
```  
  
 Oto przykładowy zestaw wyników.
  
```json
[{
  "currentUtcDateTime": "2019-05-03T20:36:17.1234567Z"
}]  
```  

## <a name="next-steps"></a>Następne kroki

- [Funkcje daty i godziny usługi Azure Cosmos DB](sql-query-date-time-functions.md)
- [Funkcje systemowe Usługi Azure Cosmos DB](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
