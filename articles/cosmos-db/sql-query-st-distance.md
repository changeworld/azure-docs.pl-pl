---
title: ST_DISTANCE w języku zapytań Azure Cosmos DB
description: Dowiedz się więcej na temat funkcji systemu SQL ST_DISTANCE w Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 4908d5f9f6eccaaaf71308b868d712f0eb96cb52
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303158"
---
# <a name="st_distance-azure-cosmos-db"></a>ST_DISTANCE (Azure Cosmos DB)
 Zwraca odległość między dwoma wyrażeniami GeoJSON typu Point, Polygon lub LineString.  
  
## <a name="syntax"></a>Składnia
  
```sql
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*spatial_expr*  
   Jest dowolne prawidłowe wyrażenie obiektu GeoJSON punkt, wielokąta lub LineString.  
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wartość wyrażenia liczbowego zawierający odległość. To wymaganie jest wyrażone w metrach dla systemu odniesienia domyślne.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład pokazuje, jak zwrócić wszystkie dokumenty rodziny, które znajdują się w zakresie 30 km od określonej lokalizacji za pomocą wbudowanej funkcji `ST_DISTANCE`. .  
  
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

Ta funkcja systemowa będzie korzystać z [indeksu geoprzestrzennego](index-policy.md#spatial-indexes).

## <a name="next-steps"></a>Następne kroki

- [Funkcje przestrzenne Azure Cosmos DB](sql-query-spatial-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do Azure Cosmos DB](introduction.md)
