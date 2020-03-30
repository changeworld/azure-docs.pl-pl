---
title: ST_DISTANCE w języku zapytań usługi Azure Cosmos DB
description: Dowiedz się więcej o ST_DISTANCE funkcji systemu SQL w usłudze Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 02844569137a46ea030b2189191b84a9db24ed22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537299"
---
# <a name="st_distance-azure-cosmos-db"></a>ST_DISTANCE (Usługa Azure Cosmos DB)
 Zwraca odległość między dwoma wyrażeniami Punkt GeoJSON, Wielokąt, Wielopolygon lub Sznurek liniowy. Aby dowiedzieć się więcej, zobacz [geoprzestrzenne i GeoJSON dane dotyczące lokalizacji](sql-query-geospatial-intro.md) artykułu.
  
## <a name="syntax"></a>Składnia
  
```sql
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*spatial_expr*  
   Czy jest to prawidłowe wyrażenie obiektu GeoJSON Point, Polygon lub LineString.  
  
## <a name="return-types"></a>Typy zwrotów
  
  Zwraca wyrażenie liczbowe zawierające odległość. Jest to wyrażone w licznikach dla domyślnego systemu referencyjnego.  
  
## <a name="examples"></a>Przykłady
  
  W poniższym przykładzie pokazano, jak zwrócić wszystkie dokumenty rodzinne, które `ST_DISTANCE` znajdują się w odległości 30 km od określonej lokalizacji przy użyciu wbudowanej funkcji. .  
  
```sql
SELECT f.id
FROM Families f
WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000  
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{  
  "id": "WakefieldFamily"  
}]  
```

## <a name="remarks"></a>Uwagi

Ta funkcja systemu będzie korzystać z [indeksu geoprzestrzennego](index-policy.md#spatial-indexes).

## <a name="next-steps"></a>Następne kroki

- [Funkcje przestrzenne usługi Azure Cosmos DB](sql-query-spatial-functions.md)
- [Funkcje systemowe Usługi Azure Cosmos DB](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
