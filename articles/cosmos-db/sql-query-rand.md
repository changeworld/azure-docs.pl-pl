---
title: Funkcja losowa w języku zapytań usługi Azure Cosmos DB
description: Dowiedz się więcej o funkcji systemu SQL RAND w usłudze Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: e44878b6d65725f08aeca4eb07088315ae2bb78a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302223"
---
# <a name="rand-azure-cosmos-db"></a>Funkcja losowa (usługa Azure Cosmos DB)
 Zwraca losowo wygenerowaną wartość liczbową z [0,1).
 
## <a name="syntax"></a>Składnia
  
```sql
RAND ()  
```  

## <a name="return-types"></a>Typy zwrotów

  Zwraca wyrażenie liczbowe.

## <a name="remarks"></a>Uwagi

  `RAND`jest funkcją niedeterministyczną. Powtarzające się wywołania `RAND` nie zwracają tych samych wyników.

## <a name="examples"></a>Przykłady
  
  Poniższy przykład zwraca losowo wygenerowaną wartość liczbową.
  
```sql
SELECT RAND() AS rand 
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{"rand": 0.87860053195618093}]  
``` 

## <a name="remarks"></a>Uwagi

Ta funkcja systemu nie będzie korzystać z indeksu.

## <a name="next-steps"></a>Następne kroki

- [Funkcje matematyczne Usługi Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funkcje systemowe Usługi Azure Cosmos DB](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
