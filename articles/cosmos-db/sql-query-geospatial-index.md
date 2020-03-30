---
title: Indeksuj dane geoprzestrzenne za pomocą usługi Azure Cosmos DB
description: Indeksuj dane przestrzenne za pomocą usługi Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: eb0a2b2778b3217e185b9883def6eaa54674cc5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137907"
---
# <a name="index-geospatial-data-with-azure-cosmos-db"></a>Indeksuj dane geoprzestrzenne za pomocą usługi Azure Cosmos DB

Zaprojektowaliśmy aparat bazy danych usługi Azure Cosmos DB, aby był naprawdę niezależny od schematu i zapewniał pierwszorzędną obsługę JSON. Aparat bazy danych zoptymalizowany do zapisu usługi Azure Cosmos DB natywnie rozumie dane przestrzenne reprezentowane w standardzie GeoJSON.

W skrócie, geometria jest rzutowana ze współrzędnych geodezyjnych na płaszczyznę 2D, a następnie stopniowo dzielona na komórki za pomocą **quadtree**. Komórki te są mapowane na 1D na podstawie położenia komórki w **krzywej wypełnienia przestrzeni Hilberta,** która zachowuje lokalizację punktów. Ponadto, gdy dane lokalizacji jest indeksowany, przechodzi przez proces znany jako **tesselation**, czyli wszystkie komórki, które przecinają lokalizację są identyfikowane i przechowywane jako klucze w indeksie usługi Azure Cosmos DB. W czasie kwerendy argumenty, takie jak punkty i wielokąty są również tessellated wyodrębnić odpowiednie zakresy identyfikatorów komórek, a następnie używane do pobierania danych z indeksu.

Jeśli określisz zasadę indeksowania, która zawiera indeks przestrzenny dla /* (wszystkie ścieżki), wszystkie dane znalezione w kontenerze są indeksowane pod kątem wydajnych zapytań przestrzennych.

> [!NOTE]
> Usługa Azure Cosmos DB obsługuje indeksowanie punktów, sznurków linii, wielokątów i wielokątów
>
>

## <a name="modifying-geospatial-data-type"></a>Modyfikowanie typu danych geoprzestrzennych

W kontenerze `geospatialConfig` określa sposób indeksowane dane geoprzestrzenne. Należy określić `geospatialConfig` jeden na kontener: geografia lub geometria. Jeśli nie zostanie `geospatialConfig` określony, domyślnie będzie typu danych geografii. Po zmodyfikowaniu `geospatialConfig`, wszystkie istniejące dane geoprzestrzenne w kontenerze zostaną ponownie zindeksowane.

> [!NOTE]
> Usługa Azure Cosmos DB obecnie obsługuje modyfikacje geospatialConfig w .NET SDK tylko w wersjach 3.6 i powyżej.
>

Oto przykład modyfikowania typu danych geoprzestrzennych, `geometry` `geospatialConfig` ustawiając właściwość i dodając **obwiednia:**

```csharp
    //Retrieve the container's details
    ContainerResponse containerResponse = await client.GetContainer("db", "spatial").ReadContainerAsync();
    //Set GeospatialConfig to Geometry
    GeospatialConfig geospatialConfig = new GeospatialConfig(GeospatialType.Geometry);
    containerResponse.Resource.GeospatialConfig = geospatialConfig;
    // Add a spatial index including the required boundingBox
    SpatialPath spatialPath = new SpatialPath
            {  
                Path = "/locations/*",
                BoundingBox = new BoundingBoxProperties(){
                    Xmin = 0,
                    Ymin = 0,
                    Xmax = 10,
                    Ymax = 10
                }
            };
    spatialPath.SpatialTypes.Add(SpatialType.Point);
    spatialPath.SpatialTypes.Add(SpatialType.LineString);
    spatialPath.SpatialTypes.Add(SpatialType.Polygon);
    spatialPath.SpatialTypes.Add(SpatialType.MultiPolygon);

    containerResponse.Resource.IndexingPolicy.SpatialIndexes.Add(spatialPath);

    // Update container with changes
    await client.GetContainer("db", "spatial").ReplaceContainerAsync(containerResponse.Resource);
```

## <a name="geography-data-indexing-examples"></a>Przykłady indeksowania danych geograficznych

Poniższy fragment kodu JSON przedstawia zasady indeksowania z włączoną indeksacją przestrzenną dla typu danych **geografii.** Jest prawidłowy dla danych przestrzennych z typem danych geografii i będzie indeksować wszelkie GeoJSON Point, Polygon, MultiPolygon lub LineString znalezione w dokumentach do wykonywania zapytań przestrzennych. Jeśli modyfikujesz zasady indeksowania przy użyciu portalu Azure portal, można określić następujące JSON dla zasad indeksowania, aby włączyć indeksowanie przestrzenne w kontenerze:

**Zasady indeksowania kontenerów JSON z geografii indeksowania przestrzennego**

```json
    {
       "automatic":true,
       "indexingMode":"Consistent",
        "includedPaths": [
        {
            "path": "/*"
        }
        ],
        "spatialIndexes": [
        {
            "path": "/*",
            "types": [
                "Point",
                "Polygon",
                "MultiPolygon",
                "LineString"
            ]
        }
    ],
       "excludedPaths":[]
    }
```

> [!NOTE]
> Jeśli wartość GeoJSON lokalizacji w dokumencie jest nieprawidłowo sformułowany lub nieprawidłowy, a następnie nie zostanie zindeksowany do wykonywania zapytań przestrzennych. Wartości lokalizacji można sprawdzać sprawdzać przy użyciu ST_ISVALID i ST_ISVALIDDETAILED.

Można również [zmodyfikować zasady indeksowania](how-to-manage-indexing-policy.md) przy użyciu interfejsu wiersza polecenia platformy Azure, programu PowerShell lub dowolnego sdk.

## <a name="geometry-data-indexing-examples"></a>Przykłady indeksowania danych geometrii

W przypadku typu danych **geometrii,** podobnego do typu danych geografii, należy określić odpowiednie ścieżki i typy do indeksu. Ponadto należy również określić `boundingBox` w ramach zasad indeksowania, aby wskazać żądany obszar, który ma być indeksowany dla tej określonej ścieżki. Każda ścieżka geoprzestrzenna`boundingBox`wymaga własnego .

Obwiednia składa się z następujących właściwości:

- **xmin**: minimalna zindeksowana współrzędna x
- **ymin**: minimalna zindeksowana współrzędna y
- **xmax**: maksymalna zindeksowana współrzędna x
- **ymax**: maksymalna wydymiona współrzędna y

Obwiednia jest wymagana, ponieważ dane geometryczne zajmują płaszczyznę, która może być nieskończona. Indeksy przestrzenne wymagają jednak ograniczonej przestrzeni. Dla typu danych **geografii** Ziemia jest granicą i nie trzeba ustawiać obwiedni.

Należy utworzyć obwiednię zawierającą wszystkie (lub większość) danych. Tylko operacje obliczone na obiektach, które są całkowicie wewnątrz obwiedni będą mogły korzystać z indeksu przestrzennego. Nie należy obwiednia znacznie większe niż jest to konieczne, ponieważ będzie to negatywnie wpłynąć na wydajność kwerendy.

Oto przykładowa zasada indeksowania, która indeksuje dane **geometrii** za `geometry`pomocą **geospatialConfig** ustawiona na:

```json
 {
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/\"_etag\"/?"
        }
    ],
    "spatialIndexes": [
        {
            "path": "/locations/*",
            "types": [
                "Point",
                "LineString",
                "Polygon",
                "MultiPolygon"
            ],
            "boundingBox": {
                "xmin": -10,
                "ymin": -20,
                "xmax": 10,
                "ymax": 20
            }
        }
    ]
}
```

Powyższa zasada indeksowania ma **granicęBox** (-10, 10) dla współrzędnych x i (-20, 20) dla współrzędnych y. Kontener z powyższymi zasadami indeksowania zindeksuje wszystkie punkty, wielokąty, wielokąty i sznurki liniowe, które znajdują się całkowicie w tym regionie.

> [!NOTE]
> Jeśli spróbujesz dodać zasady indeksowania z **boundingBox** do kontenera z `geography` typem danych, zakończy się niepowodzeniem. Należy zmodyfikować **geospatialConfig** kontenera `geometry` przed dodaniem **boundingBox**. Można dodawać dane i modyfikować pozostałą część zasad indeksowania (takich jak ścieżki i typy) przed lub po wybraniu typu danych geoprzestrzennych dla kontenera.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy dowiesz się, jak rozpocząć korzystanie z pomocy technicznej geoprzestrzennej w usłudze Azure Cosmos DB, możesz następnie:

* Dowiedz się więcej o [zapytaniu usługi Azure Cosmos DB](sql-query-getting-started.md)
* Dowiedz się więcej o [wyszukiwaniu danych przestrzennych za pomocą usługi Azure Cosmos DB](sql-query-geospatial-query.md)
* Dowiedz się więcej o [danych lokalizacji Geospatial i GeoJSON w usłudze Azure Cosmos DB](sql-query-geospatial-intro.md)