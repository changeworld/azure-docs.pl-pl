---
title: Dane lokalizacji geograficznej i GEOJSON w Azure Cosmos DB
description: Dowiedz się, jak tworzyć obiekty przestrzenne za pomocą Azure Cosmos DB i interfejsu API SQL.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: 59c8b31dcc8594d2cafb2db7832e290b01026f60
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/14/2020
ms.locfileid: "79367588"
---
# <a name="geospatial-and-geojson-location-data-in-azure-cosmos-db"></a>Dane lokalizacji geograficznej i GEOJSON w Azure Cosmos DB

Ten artykuł stanowi wprowadzenie do funkcji geoprzestrzennych w usłudze Azure Cosmos DB. Obecnie przechowywanie i dostęp do danych geoprzestrzennych jest obsługiwane tylko przez Azure Cosmos DB kont interfejsu API SQL. Po zapoznaniu się z naszą dokumentacją dotyczącą indeksowania geograficznego można odpowiedzieć na następujące pytania:

* Jak przechowywać dane przestrzenne w usłudze Azure Cosmos DB?
* Jak tworzyć zapytania dane geograficzne w usłudze Azure Cosmos DB SQL i LINQ
* Jak włączyć lub wyłączyć indeksowania przestrzennego w usłudze Azure Cosmos DB?

## <a name="introduction-to-spatial-data"></a>Wprowadzenie do danych przestrzennych

Dane przestrzenne w tym artykule opisano położenie i kształt obiektów w przestrzeni. W większości aplikacji te odnoszą się do obiektów na ziemi i dane geograficzne danych. Dane przestrzenne może służyć do reprezentowania lokalizacji osoby, miejsca lub granic mieście lub usługi Data lake. Typowe przypadki użycia często obejmują zapytań dotyczących odległości, na przykład, "Znajdź wszystkich kawiarnie blisko swojej bieżącej lokalizacji."

Interfejs API SQL Azure Cosmos DB obsługuje dwa typy danych przestrzennych: typ danych **geometrii** i typ danych **Geografia** .

- Typ **geometrii** reprezentuje dane w układzie współrzędnych Euclidean (płaski)
- Typ **geografii** reprezentuje dane w układzie współrzędnych rundy-ziemi.

## <a name="supported-data-types"></a>Obsługiwane typy danych

Azure Cosmos DB obsługuje indeksowanie i wykonywanie zapytań dotyczących danych punktów geoprzestrzennych, które są reprezentowane przy użyciu [specyfikacji GEOJSON](https://tools.ietf.org/html/rfc7946). GeoJSON struktury danych są zawsze prawidłowe obiekty JSON, dzięki czemu mogą być przechowywane i wyświetlić przy użyciu usługi Azure Cosmos DB bez specjalistycznych narzędzi i bibliotek.

Azure Cosmos DB obsługuje następujące typy danych przestrzennych:

- Moment
- LineString
- Tworząc
- MultiPolygon

### <a name="points"></a>Punkty

**Punkt** oznacza pojedynczą pozycję w miejscu. W danych geoprzestrzennych punkt reprezentuje dokładnej lokalizacji, co może być adres magazynu połówce, kiosku, samochodów i Miasto.  Punkt jest reprezentowana w parę GeoJSON (i usługi Azure Cosmos DB) przy użyciu jego współrzędnych lub długość i szerokość geograficzną.

Oto przykład JSON dla punktu:

**Punkty w Azure Cosmos DB**

```json
{
    "type":"Point",
    "coordinates":[ 31.9, -4.8 ]
}
```

Typy danych przestrzennych można osadzić w dokumencie Azure Cosmos DB, jak pokazano w tym przykładzie profilu użytkownika zawierającego dane lokalizacji:

**Użyj profilu z lokalizacją przechowywaną w Azure Cosmos DB**

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

W przypadku typu danych **geometria** Specyfikacja GEOJSON określa najpierw oś poziomą i drugą oś pionową.

### <a name="points-in-a-geography-coordinate-system"></a>Punkty w układzie współrzędnych geograficznych

W przypadku typu danych **Geografia** Specyfikacja GEOJSON określa długość i szerokość geograficzną. Podobnie jak w innych aplikacjach mapowania długości i szerokości geograficznej są kąty i reprezentowane w stopniach. Wartości długości geograficznej jest mierzony od południków Prime należą do zakresu od stopni-180 i 180.0 stopni i wartości szerokości geograficznej jest mierzony od równika i należą do zakresu od stopni-90.0 i 90.0 stopni.

Usługa Azure Cosmos DB interpretuje współrzędne reprezentowane na WGS 84 systemu odniesienia. Poniżej zamieszczono więcej informacji na temat systemów współrzędnych odniesienia.

### <a name="linestrings"></a>LineStrings

**LineStrings** reprezentuje serię dwóch lub więcej punktów w miejscu i segmentów linii, które je łączą. W danych geoprzestrzennych LineStrings są często używane do reprezentowania drogami lub rzek.

**LineStrings w GeoJSON**

```json
    "type":"LineString",
    "coordinates":[ [
        [ 31.8, -5 ],
        [ 31.8, -4.7 ]
    ] ]
```

### <a name="polygons"></a>Wielokąty

**Wielokąt** jest granicą połączonych punktów, które tworzą zamkniętą LineString. Wielokąty są często używane do reprezentowania formacji fizyczne, takie jak lakes lub polityczną jurysdykcjach, takich jak miast i stanów. Oto przykład wielokąta w Azure Cosmos DB:

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
> Specyfikacją GeoJSON wymaga, aby uzyskać prawidłowy wielokątów ostatnią parę współrzędnych podane powinien taka sama jak pierwsza, aby utworzyć kształt zamknięty.
>
> Punkty, w ramach Wielokąt musi być określona w kolejności przeciwnie do ruchu wskazówek zegara. Wielokąt podane w kolejności do ruchu wskazówek zegara reprezentuje odwrotność region znajdujący się w nim.
>
>

### <a name="multipolygons"></a>Wielowielokąty

**MultiPolygon** jest tablicą z zero lub więcej wielokątów. **Wielowielokąty** nie mogą nakładać się na strony ani mieć żadnego wspólnego obszaru. Mogą one stykać się z jednym lub wieloma punktami.

**Wielowielokątne w GeoJSON**

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

## <a name="coordinate-reference-systems"></a>Koordynowanie systemów odniesienia

Ze względu na to, że kształt ziemi jest nieregularny, współrzędne danych geoprzestrzennych są reprezentowane w wielu systemach odniesienia współrzędnych (KSR), z których każdy ma własne ramki referencyjne i jednostki miary. Na przykład "National siatki z Brytanii" jest systemu odniesienia dokładne dla Wielkiej Brytanii, ale nie poza nim.

Najbardziej popularnym KSR w korzystaniu z dzisiaj jest świat Geodetic System [WGS-84](https://earth-info.nga.mil/GandG/update/index.php). Urządzenia GPS i wieloma usługami mapowania, w tym map Google i interfejsów API usługi mapy Bing używają WGS 84. Azure Cosmos DB obsługuje indeksowanie i wykonywanie zapytań dotyczących danych geoprzestrzennych geograficznych tylko przy użyciu WGS-84 KSR.

## <a name="creating-documents-with-spatial-data"></a>Tworzenie dokumentów z danymi przestrzennymi
Podczas tworzenia dokumentów, które zawierają wartości GeoJSON one są zgodnie z zasad indeksowania kontenera automatycznie indeksowane za pomocą indeksu przestrzennego. Jeśli pracujesz za pomocą usługi Azure Cosmos DB zestawu SDK w języku o typach określanych dynamicznie, takich jak Python lub Node.js, należy utworzyć prawidłowy GeoJSON.

**Tworzenie dokumentu z danymi geograficznymi w języku Node. js**

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

Jeśli pracujesz z interfejsami API SQL, możesz użyć klas `Point`, `LineString`, `Polygon`i `MultiPolygon` w przestrzeni nazw `Microsoft.Azure.Cosmos.Spatial`, aby osadzić informacje o lokalizacji w obiektach aplikacji. W ramach tych zajęć ułatwiają serializacji i deserializacji danych przestrzennych w GeoJSON.

**Tworzenie dokumentu z danymi geograficznymi w programie .NET**

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

Jeśli nie masz informacji o szerokości geograficznej i długości geograficznej, ale masz adresy fizyczne lub lokalizacje, takie jak miasto lub kraj/region, można wyszukiwać rzeczywiste współrzędne przy użyciu usługi geokodowania, takiej jak usługi w usłudze mapy Bing. Dowiedz się więcej o geokodowania map Bing [tutaj](https://msdn.microsoft.com/library/ff701713.aspx).

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz jak rozpocząć pracę z obsługą dane geograficzne w usłudze Azure Cosmos DB, następnie można wykonywać następujące czynności:

* Dowiedz się więcej o [Azure Cosmos DB Query](sql-query-getting-started.md)
* Dowiedz się więcej o wysyłaniu [zapytań do danych przestrzennych za pomocą Azure Cosmos DB](sql-query-geospatial-query.md)
* Dowiedz się więcej o [indeksowaniu danych przestrzennych za pomocą Azure Cosmos DB](sql-query-geospatial-index.md)