---
title: ST_WITHIN w języku zapytań Azure Cosmos DB
description: Dowiedz się więcej na temat funkcji systemu SQL ST_WITHIN w Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 07a339d82f5e4bea1ea0412a5d5b19522611b54a
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78296120"
---
# <a name="st_within-azure-cosmos-db"></a>ST_WITHIN (Azure Cosmos DB)
 Zwraca wartość wskazującą, czy obiekt GeoJSON (punkt, wielokąta lub LineString) określona w pierwszym argumencie znajduje się w GeoJSON (punkt wielokąta i LineString) w drugim argumencie wyrażenia logicznego.  
  
## <a name="syntax"></a>Składnia
  
```sql
ST_WITHIN (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*spatial_expr*  
   Jest wyrażeniem typu GEOJSON, wielokąta lub LineString.  
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wartość typu Boolean.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład pokazuje, jak znaleźć wszystkie dokumenty rodziny w wielokąta przy użyciu `ST_WITHIN`.  
  
```sql
SELECT f.id
FROM Families f
WHERE ST_WITHIN(f.location, {  
    'type':'Polygon',
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{ "id": "WakefieldFamily" }]  
```  

## <a name="remarks"></a>Uwagi

Ta funkcja systemowa będzie korzystać z [indeksu geoprzestrzennego](index-policy.md#spatial-indexes).

## <a name="next-steps"></a>Następne kroki

- [Funkcje przestrzenne Azure Cosmos DB](sql-query-spatial-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do Azure Cosmos DB](introduction.md)
