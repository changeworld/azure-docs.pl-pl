---
title: Wykonywanie zapytania o dane geograficzne za pomocą Azure Cosmos DB
description: Wykonywanie zapytania o dane przestrzenne za pomocą Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: 08b12bd9d35aaa61c79d35a55068983cdc0f1b83
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/23/2020
ms.locfileid: "77566324"
---
# <a name="querying-geospatial-data-with-azure-cosmos-db"></a>Wykonywanie zapytania o dane geograficzne za pomocą Azure Cosmos DB

W tym artykule opisano sposób wykonywania zapytań dotyczących danych geoprzestrzennych w Azure Cosmos DB przy użyciu języka SQL i LINQ. Obecnie przechowywanie i dostęp do danych geoprzestrzennych jest obsługiwane tylko przez Azure Cosmos DB kont interfejsu API SQL. Usługa Azure Cosmos DB obsługuje następujące funkcje wbudowane Otwórz dane geograficzne Consortium (OGC) do wykonywania zapytań na danych geoprzestrzennych. Aby uzyskać więcej informacji na temat pełnego zestawu wbudowanych funkcji w języku SQL, zobacz temat [funkcje systemowe zapytań w Azure Cosmos DB](sql-query-system-functions.md).

## <a name="spatial-sql-built-in-functions"></a>Wbudowane funkcje przestrzenne programu SQL

Poniżej znajduje się lista funkcji systemu geoprzestrzennych, które są przydatne do wykonywania zapytań w Azure Cosmos DB:

|**Użycie**|**Opis**|
|---|---|
| ST_DISTANCE (spatial_expr, spatial_expr) | Zwraca odległość między dwoma wyrażeniami GeoJSON typu Point, Polygon lub LineString.|
|ST_WITHIN (spatial_expr, spatial_expr) | Zwraca wyrażenie warunkowe wskazujące, czy pierwszy obiekt GeoJSON (Point, Polygon lub LineString) znajduje się w drugim obiekcie GeoJSON (Point, Polygon lub LineString).|
|ST_INTERSECTS (wyrażenie_przestrzenne, wyrażenie_przestrzenne)| Zwraca wyrażenie warunkowe wskazujące, czy dwa określone obiekty GeoJSON (Point, Polygon lub LineString) przecinają się.|
|ST_ISVALID| Zwraca wartość logiczną wskazującą, czy określone wyrażenie GeoJSON typu Point, Polygon lub LineString jest nieprawidłowe.|
| ST_ISVALIDDETAILED| Zwraca wartość JSON, która zawiera wartość logiczną, jeśli określone wyrażenie GEOJSON, wielokąt lub LineString jest prawidłowe. Jeśli jest nieprawidłowa, zwraca przyczynę jako wartość ciągu.|

Funkcje przestrzenne mogą być używane do wykonywania zapytań dotyczących odległości względem danych przestrzennych. Na przykład Oto zapytanie, które zwraca wszystkie dokumenty rodziny, które znajdują się w przedziale 30 km od określonej lokalizacji przy użyciu wbudowanej funkcji `ST_DISTANCE`.

**Zapytanie**

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

Jeśli dołączysz indeksowania przestrzennego w zasady indeksowania, następnie "odległość zapytania" będzie ich obsługa jest wydajna za pomocą indeksu. Aby uzyskać więcej informacji na temat indeksowania przestrzennego, zobacz [indeksowanie geoprzestrzenne](sql-query-geospatial-index.md). Jeśli nie masz indeksu przestrzennego dla określonych ścieżek, zapytanie przeprowadzi skanowanie kontenera.

`ST_WITHIN` można użyć do sprawdzenia, czy punkt leży w obrębie wielokąta. Często wielokątów są używane do reprezentowania granice, takie jak kodów pocztowych, granice stanu lub formacji fizycznych. Ponownie Jeśli dołączysz indeksowania przestrzennego w zasady indeksowania, następnie "w ciągu" zapytania będą ich obsługa jest wydajna za pomocą indeksu.

Argumenty wielokąta w `ST_WITHIN` mogą zawierać tylko jeden pierścień, czyli, wielokąty nie mogą zawierać otworów.

**Zapytanie**

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
> Podobnie jak niezgodne typy działają w kwerendzie Azure Cosmos DB, jeśli wartość lokalizacji określona w obu argumentach jest źle sformułowana lub nieprawidłowa, zostanie ona oceniona jako **niezdefiniowana** i oceniony dokument, który ma zostać pominięty z wyników zapytania. Jeśli zapytanie nie zwraca żadnych wyników, uruchom `ST_ISVALIDDETAILED`, aby debugować dlaczego typ przestrzenny jest nieprawidłowy.
>
>

Usługa Azure Cosmos DB obsługuje także wykonywanie kwerendy odwrotne, oznacza to, że możesz indeksowanie, wielokąty lub wiersze w usłudze Azure Cosmos DB, a następnie zapytanie dla obszarów, które zawierają określony punkt. Ten wzorzec często służy logistycznych do identyfikowania, na przykład podczas ciężarówki wprowadza lub go opuszcza wyznaczony obszar.

**Zapytanie**

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

`ST_ISVALID` i `ST_ISVALIDDETAILED` mogą służyć do sprawdzenia, czy obiekt przestrzenny jest prawidłowy. Na przykład następujące zapytanie sprawdza ważność punkt z poza zakresu wartość szerokości geograficznej (-132.8). `ST_ISVALID` zwraca tylko wartość logiczną, a `ST_ISVALIDDETAILED` zwraca wartość logiczną i ciąg zawierający powód, dlaczego jest uznawany za nieprawidłowy.

**Zapytanie**

```sql
    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })
```

**Results**

```json
    [{
      "$1": false
    }]
```

Te funkcje można również sprawdzić wielokątów. Na przykład w tym miejscu użyjemy `ST_ISVALIDDETAILED` do walidacji wielokąta, który nie jest zamknięty.

**Zapytanie**

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

## <a name="linq-querying-in-the-net-sdk"></a>Zapytania LINQ w zestawie SDK platformy .NET

Zestaw SDK programu SQL .NET jest również dostawcą metod zastępczych `Distance()` i `Within()` do użycia w wyrażeniach LINQ. Dostawca LINQ do SQL tłumaczy tej metody wywołania równoważne wywołań funkcji wbudowanej SQL (ST_DISTANCE i ST_WITHIN odpowiednio).

Oto przykład zapytania LINQ, które znajduje wszystkie dokumenty w kontenerze usługi Azure Cosmos, którego wartość `location` jest w promieniu 30 km od określonego punktu przy użyciu LINQ.

**Zapytanie LINQ dla odległości**

```csharp
    foreach (UserProfile user in container.GetItemLinqQueryable<UserProfile>(allowSynchronousQueryExecution: true)
        .Where(u => u.ProfileType == "Public" && u.Location.Distance(new Point(32.33, -4.66)) < 30000))
    {
        Console.WriteLine("\t" + user);
    }
```

Podobnie Oto zapytanie do znajdowania wszystkich dokumentów, których `location` znajduje się w określonym polu/wielokąta.

**Zapytanie LINQ dla w ramach**

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

Teraz, gdy wiesz jak rozpocząć pracę z obsługą dane geograficzne w usłudze Azure Cosmos DB, następnie można wykonywać następujące czynności:

* Dowiedz się więcej o [Azure Cosmos DB Query](sql-query-getting-started.md)
* Dowiedz się więcej o [danych lokalizacji geograficznej i GEOJSON w Azure Cosmos DB](sql-query-geospatial-intro.md)
* Dowiedz się więcej o [indeksowaniu danych przestrzennych za pomocą Azure Cosmos DB](sql-query-geospatial-index.md)
