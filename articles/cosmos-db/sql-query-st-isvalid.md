---
title: ST_ISVALID w języku zapytań usługi Azure Cosmos DB
description: Dowiedz się więcej o ST_ISVALID funkcji systemu SQL w usłudze Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8fbddbe82ae13585b8259a66dffaeef8024baf5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71349364"
---
# <a name="st_isvalid-azure-cosmos-db"></a>ST_ISVALID (Usługa Azure Cosmos DB)
 Zwraca wartość logiczną wskazującą, czy określone wyrażenie GeoJSON typu Point, Polygon lub LineString jest nieprawidłowe.  
  
## <a name="syntax"></a>Składnia
  
```sql
ST_ISVALID(<spatial_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*spatial_expr*  
   Jest wyrażeniem Punkt GeoJSON, Wielokąt lub Sznurek liniowy.  
  
## <a name="return-types"></a>Typy zwrotów
  
  Zwraca wyrażenie logiczne.  
  
## <a name="examples"></a>Przykłady
  
  W poniższym przykładzie pokazano, jak sprawdzić, czy punkt jest prawidłowy przy użyciu ST_VALID.  
  
  Na przykład ten punkt ma wartość szerokości geograficznej, która nie jest w prawidłowym zakresie wartości [-90, 90], więc kwerenda zwraca false.  
  
  W przypadku wielokątów specyfikacja GeoJSON wymaga, aby ostatnia podana para współrzędnych była taka sama jak pierwsza, aby utworzyć zamknięty kształt. Punkty w wielokątach muszą być określone w kolejności odwrotnej do ruchu wskazówek zegara. Wielokąt określony w kolejności zgodnie z ruchem wskazówek zegara reprezentuje odwrotność regionu w nim.  
  
```sql
SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] }) AS b 
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{ "b": false }]  
```  

## <a name="next-steps"></a>Następne kroki

- [Funkcje przestrzenne usługi Azure Cosmos DB](sql-query-spatial-functions.md)
- [Funkcje systemowe Usługi Azure Cosmos DB](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
