---
title: ST_ISVALIDDETAILED w języku zapytań usługi Azure Cosmos DB
description: Dowiedz się więcej o ST_ISVALIDDETAILED funkcji systemu SQL w usłudze Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9e640c223c2fef844b9b53e1f4afa3a5d398c8c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71349356"
---
# <a name="st_isvaliddetailed-azure-cosmos-db"></a>ST_ISVALIDDETAILED (Usługa Azure Cosmos DB)
 Zwraca wartość JSON zawierającą wartość logiczną, jeśli określone wyrażenie GeoJSON typu Point, Polygon lub LineString jest prawidłowe, a jeśli jest nieprawidłowe, dodatkowo zwraca przyczynę jako wartość ciągu.  
  
## <a name="syntax"></a>Składnia
  
```sql
ST_ISVALIDDETAILED(<spatial_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*spatial_expr*  
   Jest wyrażeniem punktu lub wielokąta GeoJSON.  
  
## <a name="return-types"></a>Typy zwrotów
  
  Zwraca wartość JSON zawierającą wartość logiczną, jeśli określone wyrażenie punktowe lub wielokątne GeoJSON jest prawidłowe, a jeśli jest nieprawidłowe, dodatkowo przyczyna jako wartość ciągu.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład jak sprawdzić ważność (ze szczegółami) za pomocą . `ST_ISVALIDDETAILED`  
  
```sql
SELECT ST_ISVALIDDETAILED({   
  "type": "Polygon",   
  "coordinates": [[ [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] ]]  
}) AS b 
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{  
  "b": {
    "valid": false,
    "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points."   
  }  
}]  
```  

## <a name="next-steps"></a>Następne kroki

- [Funkcje przestrzenne usługi Azure Cosmos DB](sql-query-spatial-functions.md)
- [Funkcje systemowe Usługi Azure Cosmos DB](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
