---
title: Wykonywanie zapytań o dane geoprzestrzenne za pomocą usługi Azure Cosmos DB
description: Wykonywanie zapytań o dane przestrzenne za pomocą usługi Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: 08b12bd9d35aaa61c79d35a55068983cdc0f1b83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77566324"
---
# <a name="querying-geospatial-data-with-azure-cosmos-db"></a>Wykonywanie zapytań o dane geoprzestrzenne za pomocą usługi Azure Cosmos DB

W tym artykule opisano, jak kwerendy danych geoprzestrzennych w usłudze Azure Cosmos DB przy użyciu języka SQL i LINQ. Obecnie przechowywanie danych geoprzestrzennych i uzyskiwanie do nich dostępu jest obsługiwane tylko przez konta interfejsu API usługi Azure Cosmos DB SQL. Usługa Azure Cosmos DB obsługuje następujące funkcje wbudowanego konsorcjum Open Geospatial Consortium (OGC) do wykonywania zapytań geoprzestrzennych. Aby uzyskać więcej informacji na temat pełnego zestawu wbudowanych funkcji w języku SQL, zobacz [Funkcje systemu zapytań w usłudze Azure Cosmos DB](sql-query-system-functions.md).

## <a name="spatial-sql-built-in-functions"></a>Funkcje wbudowane SQL przestrzenne

Oto lista funkcji systemu geoprzestrzennego przydatnych do wykonywania zapytań w usłudze Azure Cosmos DB:

|**Użycia**|**Opis**|
|---|---|
| ST_DISTANCE (spatial_expr, spatial_expr) | Zwraca odległość między dwoma wyrażeniami GeoJSON typu Point, Polygon lub LineString.|
|ST_WITHIN (spatial_expr, spatial_expr) | Zwraca wyrażenie warunkowe wskazujące, czy pierwszy obiekt GeoJSON (Point, Polygon lub LineString) znajduje się w drugim obiekcie GeoJSON (Point, Polygon lub LineString).|
|ST_INTERSECTS (wyrażenie_przestrzenne, wyrażenie_przestrzenne)| Zwraca wyrażenie warunkowe wskazujące, czy dwa określone obiekty GeoJSON (Point, Polygon lub LineString) przecinają się.|
|ST_ISVALID| Zwraca wartość logiczną wskazującą, czy określone wyrażenie GeoJSON typu Point, Polygon lub LineString jest nieprawidłowe.|
| ST_ISVALIDDETAILED| Zwraca wartość JSON zawierającą wartość logiczną, jeśli określone wyrażenie Punkt, Wielokąt lub Sznurek jest prawidłowy. Jeśli jest nieprawidłowy, zwraca przyczynę jako wartość ciągu.|

Funkcje przestrzenne mogą być używane do wykonywania zapytań dotyczących odległości względem danych przestrzennych. Na przykład oto kwerenda, która zwraca wszystkie dokumenty rodzinne, które znajdują się `ST_DISTANCE` w odległości 30 km od określonej lokalizacji przy użyciu wbudowanej funkcji.

**Kwerendy**

```sql
    SELECT f.id
    FROM Families f
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000
```

**Results**

```json
    [{
      "id": "WakefieldFamily"
    }]
```

Jeśli uwzględnisz indeksowanie przestrzenne w zasadach indeksowania, "zapytania dotyczące odległości" będą obsługiwane skutecznie za pośrednictwem indeksu. Aby uzyskać więcej informacji na temat indeksowania przestrzennego, zobacz [indeksowanie geoprzestrzenne](sql-query-geospatial-index.md). Jeśli nie masz indeksu przestrzennego dla określonych ścieżek, kwerenda wykona skanowanie kontenera.

`ST_WITHIN`można użyć do sprawdzenia, czy punkt znajduje się w polygonie. Często wielokąty są używane do reprezentowania granic, takich jak kody pocztowe, granice stanu lub formacje naturalne. Ponownie, jeśli uwzględnisz indeksowanie przestrzenne w zasadach indeksowania, kwerendy "w obrębie" będą obsługiwane skutecznie za pośrednictwem indeksu.

Wielokąt argumenty `ST_WITHIN` w może zawierać tylko jeden pierścień, to znaczy, Wielokąty nie może zawierać otworów w nich.

**Kwerendy**

```sql
    SELECT *
    FROM Families f
    WHERE ST_WITHIN(f.location, {
        'type':'Polygon',
        'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
    })
```

**Results**

```json
    [{
      "id": "WakefieldFamily",
    }]
```

> [!NOTE]
> Podobnie jak niezgodne typy działają w zapytaniu usługi Azure Cosmos DB, jeśli wartość lokalizacji określona w którymkolwiek z argumentów jest nieprawidłowo sformułowana lub nieprawidłowa, następnie ocenia się **na niezdefiniowany** i oceniony dokument, który ma zostać pominięty z wyników kwerendy. Jeśli kwerenda zwraca żadnych `ST_ISVALIDDETAILED` wyników, uruchom do debugowania, dlaczego typ przestrzenny jest nieprawidłowy.
>
>

Usługa Azure Cosmos DB obsługuje również wykonywanie zapytań odwrotnych, czyli można indeksować wielokąty lub wiersze w usłudze Azure Cosmos DB, a następnie wysyłać zapytania do obszarów zawierających określony punkt. Wzorzec ten jest powszechnie stosowany w logistyce do identyfikacji, na przykład, gdy ciężarówka wjeżdża lub opuszcza wyznaczony obszar.

**Kwerendy**

```sql
    SELECT *
    FROM Areas a
    WHERE ST_WITHIN({'type': 'Point', 'coordinates':[31.9, -4.8]}, a.location)
```

**Results**

```json
    [{
      "id": "MyDesignatedLocation",
      "location": {
        "type":"Polygon",
        "coordinates": [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
      }
    }]
```

`ST_ISVALID`i `ST_ISVALIDDETAILED` może służyć do sprawdzania, czy obiekt przestrzenny jest prawidłowy. Na przykład następująca kwerenda sprawdza ważność punktu z wartością szerokości geograficznej poza zakresem (-132.8). `ST_ISVALID`zwraca tylko wartość logiczną `ST_ISVALIDDETAILED` i zwraca wartość logiczną i ciąg zawierający przyczynę, dla którego jest uważany za nieprawidłowy.

**Kwerendy**

```sql
    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })
```

**Results**

```json
    [{
      "$1": false
    }]
```

Funkcje te mogą być również używane do sprawdzania poprawności wielokątów. Na przykład w `ST_ISVALIDDETAILED` tym miejscu używamy do sprawdzania poprawności wielokąta, który nie jest zamknięty.

**Kwerendy**

```sql
    SELECT ST_ISVALIDDETAILED({ "type": "Polygon", "coordinates": [[ 
        [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] 
        ]]})
```

**Results**

```json
    [{
       "$1": { 
            "valid": false, 
            "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a Polygon must have the same start and end points." 
          }
    }]
```

## <a name="linq-querying-in-the-net-sdk"></a>Linq kwerendy w pliku .NET SDK

SQL .NET SDK również `Distance()` dostawców `Within()` metod skrótowych i do użytku w wyrażeniach LINQ. Dostawca LINQ SQL tłumaczy tę metodę wywołania równoważne wywołania funkcji wbudowanych SQL (odpowiednio ST_DISTANCE i ST_WITHIN).

Oto przykład zapytania LINQ, który znajduje wszystkie dokumenty w kontenerze usługi Azure Cosmos, którego `location` wartość znajduje się w promieniu 30 km od określonego punktu przy użyciu LINQ.

**Linq kwerendy dla odległość**

```csharp
    foreach (UserProfile user in container.GetItemLinqQueryable<UserProfile>(allowSynchronousQueryExecution: true)
        .Where(u => u.ProfileType == "Public" && u.Location.Distance(new Point(32.33, -4.66)) < 30000))
    {
        Console.WriteLine("\t" + user);
    }
```

Podobnie oto zapytanie o znalezienie wszystkich dokumentów, których `location` znajduje się w określonym polu/wielokąta.

**Linq kwerendy dla w obrębie**

```csharp
    Polygon rectangularArea = new Polygon(
        new[]
        {
            new LinearRing(new [] {
                new Position(31.8, -5),
                new Position(32, -5),
                new Position(32, -4.7),
                new Position(31.8, -4.7),
                new Position(31.8, -5)
            })
        });

    foreach (UserProfile user in container.GetItemLinqQueryable<UserProfile>(allowSynchronousQueryExecution: true)
         .Where(a => a.Location.Within(rectangularArea)))
    {
        Console.WriteLine("\t" + user);
    }
```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy dowiesz się, jak rozpocząć korzystanie z pomocy technicznej geoprzestrzennej w usłudze Azure Cosmos DB, możesz następnie:

* Dowiedz się więcej o [zapytaniu usługi Azure Cosmos DB](sql-query-getting-started.md)
* Dowiedz się więcej o [danych lokalizacji Geospatial i GeoJSON w usłudze Azure Cosmos DB](sql-query-geospatial-intro.md)
* Dowiedz się więcej o [indeksowaniu danych przestrzennych za pomocą usługi Azure Cosmos DB](sql-query-geospatial-index.md)
