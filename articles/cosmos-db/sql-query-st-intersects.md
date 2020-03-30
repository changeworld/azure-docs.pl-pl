---
title: ST_INTERSECTS w języku zapytań usługi Azure Cosmos DB
description: Dowiedz się więcej o ST_INTERSECTS funkcji systemu SQL w usłudze Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: f3c3878956b90ffb45556ed819046af9eb7618f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303141"
---
# <a name="st_intersects-azure-cosmos-db"></a>ST_INTERSECTS (Usługa Azure Cosmos DB)
 Zwraca wyrażenie logiczne wskazujące, czy obiekt GeoJSON (Point, Polygon lub LineString) określony w pierwszym argucie przecina geojson (punkt, wielokąt lub linestring) w drugim argumentie.  
  
## <a name="syntax"></a>Składnia
  
```sql
ST_INTERSECTS (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*spatial_expr*  
   Jest wyrażeniem obiektu GeoJSON Point, Polygon lub LineString.  
  
## <a name="return-types"></a>Typy zwrotów
  
  Zwraca wartość logiczną.  
  
## <a name="examples"></a>Przykłady
  
  W poniższym przykładzie pokazano, jak znaleźć wszystkie obszary, które przecinają się z danym wielokątem.  
  
```sql
SELECT a.id
FROM Areas a
WHERE ST_INTERSECTS(a.location, {  
    'type':'Polygon',
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{ "id": "IntersectingPolygon" }]  
```  

## <a name="remarks"></a>Uwagi

Ta funkcja systemu będzie korzystać z [indeksu geoprzestrzennego](index-policy.md#spatial-indexes).

## <a name="next-steps"></a>Następne kroki

- [Funkcje przestrzenne usługi Azure Cosmos DB](sql-query-spatial-functions.md)
- [Funkcje systemowe Usługi Azure Cosmos DB](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
