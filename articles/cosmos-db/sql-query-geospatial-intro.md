---
title: Dane lokalizacji geoprzestrzennej i geojson w usłudze Azure Cosmos DB
description: Dowiedz się, jak tworzyć obiekty przestrzenne za pomocą usługi Azure Cosmos DB i interfejsu API SQL.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: 59c8b31dcc8594d2cafb2db7832e290b01026f60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367588"
---
# <a name="geospatial-and-geojson-location-data-in-azure-cosmos-db"></a>Dane lokalizacji geoprzestrzennej i geojson w usłudze Azure Cosmos DB

Ten artykuł jest wprowadzeniem do funkcji geoprzestrzennych w usłudze Azure Cosmos DB. Obecnie przechowywanie danych geoprzestrzennych i uzyskiwanie do nich dostępu jest obsługiwane tylko przez konta interfejsu API usługi Azure Cosmos DB SQL. Po zapoznaniu się z naszą dokumentacją dotyczącą indeksowania geoprzestrzennego będziesz mógł odpowiedzieć na następujące pytania:

* Jak przechowywać dane przestrzenne w usłudze Azure Cosmos DB?
* Jak można wysyłać zapytania do danych geoprzestrzennych w usłudze Azure Cosmos DB w językach SQL i LINQ?
* Jak włączyć lub wyłączyć indeksowanie przestrzenne w usłudze Azure Cosmos DB?

## <a name="introduction-to-spatial-data"></a>Wprowadzenie do danych przestrzennych

Dane przestrzenne opisują położenie i kształt obiektów w przestrzeni. W większości zastosowań odpowiadają one obiektom na ziemi i danym geoprzestrzennym. Dane przestrzenne mogą być używane do reprezentowania lokalizacji osoby, miejsca zainteresowania lub granicy miasta lub jeziora. Typowe przypadki użycia często dotyczą zapytań zbliżeniowych, na przykład "znajdź wszystkie kawiarnie w pobliżu mojej obecnej lokalizacji".

Interfejs API SQL usługi Azure Cosmos DB obsługuje dwa typy danych przestrzennych: typ danych **geometrii** i typ danych **geografii.**

- Typ **geometrii** reprezentuje dane w układzie współrzędnych euklidesowych (płaskich)
- Typ **geografii** reprezentuje dane w układzie współrzędnych okrągłego.

## <a name="supported-data-types"></a>Obsługiwane typy danych

Usługa Azure Cosmos DB obsługuje indeksowanie i wykonywanie zapytań o dane punktów geoprzestrzennych reprezentowanych przy użyciu [specyfikacji GeoJSON.](https://tools.ietf.org/html/rfc7946) Struktury danych GeoJSON są zawsze prawidłowe obiekty JSON, dzięki czemu mogą być przechowywane i przeszukiwane przy użyciu usługi Azure Cosmos DB bez żadnych specjalistycznych narzędzi lub bibliotek.

Usługa Azure Cosmos DB obsługuje następujące typy danych przestrzennych:

- Punkt
- Linestring
- Wielokąt
- Multipolygon

### <a name="points"></a>Punkty

**Punkt** oznacza pojedynczą pozycję w przestrzeni. W danych geoprzestrzennych punkt reprezentuje dokładną lokalizację, która może być adresem ulicy sklepu spożywczego, kiosku, samochodu lub miasta.  Punkt jest reprezentowany w GeoJSON (i Azure Cosmos DB) przy użyciu jego pary współrzędnych lub długości geograficznej i szerokości geograficznej.

Oto przykład JSON dla punktu:

**Punkty w usłudze Azure Cosmos DB**

```json
{
    "type":"Point",
    "coordinates":[ 31.9, -4.8 ]
}
```

Typy danych przestrzennych mogą być osadzone w dokumencie usługi Azure Cosmos DB, jak pokazano w tym przykładzie profilu użytkownika zawierającego dane lokalizacji:

**Używanie profilu z lokalizacją przechowywaną w usłudze Azure Cosmos DB**

```json
{
    "id":"cosmosdb-profile",
    "screen_name":"@CosmosDB",
    "city":"Redmond",
    "topics":[ "global", "distributed" ],
    "location":{
        "type":"Point",
        "coordinates":[ 31.9, -4.8 ]
    }
}
```

### <a name="points-in-a-geometry-coordinate-system"></a>Punkty w układzie współrzędnych geometrii

Dla typu danych **geometrii** specyfikacja GeoJSON określa oś poziomą jako pierwszą i drugą oś pionową.

### <a name="points-in-a-geography-coordinate-system"></a>Punkty układu współrzędnych geografii

Dla typu danych **geografii** specyfikacja GeoJSON określa długość geograficzną pierwszy i szerokość geograficzną drugi. Podobnie jak w innych aplikacjach mapowania, długość geograficzna i szerokość geograficzna są kątami i są reprezentowane pod względem stopni. Wartości długości geograficznej są mierzone od południka głównego i są od -180 stopni do 180,0 stopni, a wartości szerokości geograficznej są mierzone od równika i są od -90,0 stopni do 90,0 stopni.

Usługa Azure Cosmos DB interpretuje współrzędne zgodnie z systemem referencyjnym WGS-84. Poniżej znajdziesz więcej informacji na temat układów odniesienia współrzędnych.

### <a name="linestrings"></a>Linestrings

**LineStrings** reprezentują serię dwóch lub więcej punktów w przestrzeni i segmentów linii, które je łączą. W danych geoprzestrzennych LineStrings są powszechnie używane do reprezentowania autostrad lub rzek.

**Sznurki liniowe w geojson**

```json
    "type":"LineString",
    "coordinates":[ [
        [ 31.8, -5 ],
        [ 31.8, -4.7 ]
    ] ]
```

### <a name="polygons"></a>Wielokątów

**Wielokąt** jest granicą połączonych punktów, która tworzy zamknięty LineString. Wielokąty są powszechnie używane do reprezentowania naturalnych formacji, takich jak jeziora lub jurysdykcje polityczne, takie jak miasta i państwa. Oto przykład wielokąta w usłudze Azure Cosmos DB:

**Wielokąty w GeoJSON**

```json
{
    "type":"Polygon",
    "coordinates":[ [
        [ 31.8, -5 ],
        [ 31.8, -4.7 ],
        [ 32, -4.7 ],
        [ 32, -5 ],
        [ 31.8, -5 ]
    ] ]
}
```

> [!NOTE]
> Specyfikacja GeoJSON wymaga, aby w przypadku prawidłowych wielokątów ostatnia podana para współrzędnych była taka sama jak pierwsza, aby utworzyć zamknięty kształt.
>
> Punkty w wielokątach muszą być określone w kolejności odwrotnej do ruchu wskazówek zegara. Wielokąt określony w kolejności zgodnie z ruchem wskazówek zegara reprezentuje odwrotność regionu w nim.
>
>

### <a name="multipolygons"></a>MultiPolygony

**MultiPolygon** jest tablicą zero lub więcej wielokątów. **MultiPolygony** nie mogą nakładać się na boki ani mieć żadnego wspólnego obszaru. Mogą dotykać w jednym lub więcej punktach.

**MultiPolygony w GeoJSON**

```json
{
    "type":"MultiPolygon",
    "coordinates":[[[
        [52.0, 12.0],
        [53.0, 12.0],
        [53.0, 13.0],
        [52.0, 13.0],
        [52.0, 12.0]
        ]],
        [[
        [50.0, 0.0],
        [51.0, 0.0],
        [51.0, 5.0],
        [50.0, 5.0],
        [50.0, 0.0]
        ]]]
}
```

## <a name="coordinate-reference-systems"></a>Układy odniesienia współrzędnych

Ponieważ kształt ziemi jest nieregularny, współrzędne geograficznych danych geoprzestrzennych geograficznych geograficznych są reprezentowane w wielu układach odniesienia współrzędnych (CRS), każdy z własnymi ramkami odniesienia i jednostkami miary. Na przykład "National Grid of Britain" jest systemem referencyjnym jest dokładny dla Zjednoczonego Królestwa, ale nie poza nim.

Najbardziej popularnym CRS w użyciu obecnie jest World Geodetic System [WGS-84](https://earth-info.nga.mil/GandG/update/index.php). Urządzenia GPS i wiele usług mapowania, w tym Google Maps i interfejsów API Map Bing, korzysta z programu WGS-84. Usługa Azure Cosmos DB obsługuje indeksowanie i wykonywanie zapytań o dane geoprzestrzenne geografii przy użyciu tylko programu WGS-84 CRS.

## <a name="creating-documents-with-spatial-data"></a>Tworzenie dokumentów z danymi przestrzennymi
Podczas tworzenia dokumentów, które zawierają wartości GeoJSON, są one automatycznie indeksowane z indeksem przestrzennym zgodnie z zasadami indeksowania kontenera. Jeśli pracujesz z zestawem SDK usługi Azure Cosmos DB w dynamicznie wpisanym języku, takim jak Python lub Node.js, musisz utworzyć prawidłową usługę GeoJSON.

**Tworzenie dokumentu z danymi geoprzestrzanymi w pliku Node.js**

```javascript
var userProfileDocument = {
    "id":"cosmosdb",
    "location":{
        "type":"Point",
        "coordinates":[ -122.12, 47.66 ]
    }
};

client.createDocument(`dbs/${databaseName}/colls/${collectionName}`, userProfileDocument, (err, created) => {
    // additional code within the callback
});
```

Jeśli pracujesz z interfejsami API SQL, możesz `Point` `LineString`użyć `Polygon`programu `MultiPolygon` , `Microsoft.Azure.Cosmos.Spatial` i klas w obszarze nazw, aby osadzać informacje o lokalizacji w obiektach aplikacji. Te klasy ułatwiają serializacji i deserializacji danych przestrzennych do GeoJSON.

**Tworzenie dokumentu z danymi geoprzestrzanymi w .NET**

```csharp
using Microsoft.Azure.Cosmos.Spatial;

public class UserProfile
{
    [JsonProperty("id")]
    public string id { get; set; }

    [JsonProperty("location")]
    public Point Location { get; set; }

    // More properties
}

await container.CreateItemAsync( new UserProfile
    {
        id = "cosmosdb",
        Location = new Point (-122.12, 47.66)
    });
```

Jeśli nie masz informacji o szerokości i długości geograficznej, ale masz adresy fizyczne lub nazwę lokalizacji, takie jak miasto lub kraj/region, możesz wyszukać rzeczywiste współrzędne, korzystając z usługi geokodowania, takiej jak usługi REST Mapy Bing. Dowiedz się więcej o geokodowaniu map Bing [tutaj](https://msdn.microsoft.com/library/ff701713.aspx).

## <a name="next-steps"></a>Następne kroki

Teraz, gdy dowiesz się, jak rozpocząć korzystanie z pomocy technicznej geoprzestrzennej w usłudze Azure Cosmos DB, możesz następnie:

* Dowiedz się więcej o [zapytaniu usługi Azure Cosmos DB](sql-query-getting-started.md)
* Dowiedz się więcej o [wyszukiwaniu danych przestrzennych za pomocą usługi Azure Cosmos DB](sql-query-geospatial-query.md)
* Dowiedz się więcej o [indeksowaniu danych przestrzennych za pomocą usługi Azure Cosmos DB](sql-query-geospatial-index.md)