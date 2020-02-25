---
title: Indeksowanie danych geoprzestrzennych przy użyciu Azure Cosmos DB
description: Indeksuj dane przestrzenne za pomocą Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: 2cf682a404154b9c1bb94680b3adb673892c1c72
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/23/2020
ms.locfileid: "77566376"
---
# <a name="index-geospatial-data-with-azure-cosmos-db"></a>Indeksowanie danych geoprzestrzennych przy użyciu Azure Cosmos DB

Zaprojektowano aparat bazy danych Azure Cosmos DB, aby niezależny od schemat i zapewnić obsługę pierwszej klasy w formacie JSON. Aparat zoptymalizowanej bazy danych z możliwością zapisu Azure Cosmos DB natywnie rozumie dane przestrzenne reprezentowane w standardzie GEOJSON.

W Nutshell geometria jest rzutowana od współrzędnych Geodetic do płaszczyzny 2D, a następnie przedzielona stopniowo na komórki przy użyciu **QuadTree**. Te komórki są mapowane na 1D w oparciu o lokalizację komórki w **Hilbertej krzywej wypełniania**, która zachowuje miejscowość punktów. Ponadto, gdy dane lokalizacji są indeksowane, przechodzi przez proces znany jako **mozaikowania**, czyli wszystkie komórki, które przecinają lokalizację, są identyfikowane i przechowywane jako klucze w indeksie Azure Cosmos DB. Podczas przeszukiwania argumentów, takich jak punktów i wielokątów są również tesselowaną można wyodrębnić zakresów identyfikator odpowiednich komórek, a następnie używane do pobierania danych z indeksu.

W przypadku określenia zasad indeksowania, które zawierają indeks przestrzenny dla/* (wszystkie ścieżki), wszystkie dane znajdujące się w kontenerze są indeksowane pod kątem wydajnych zapytań przestrzennych.

> [!NOTE]
> Azure Cosmos DB obsługuje indeksowanie punktów, LineStrings, wielokątów i wielowielokątnych
>
>

## <a name="geography-data-indexing-examples"></a>Przykłady indeksowania danych geograficznych

Poniższy fragment kodu JSON przedstawia zasady indeksowania z włączonym indeksem przestrzennym dla typu danych **Geografia** . Jest ona prawidłowa dla danych przestrzennych z typem danych Geografia i będzie indeksować dowolny punkt GEOJSON, Wielokąt, MultiPolygon lub LineString znaleziony w dokumentach dla zapytań przestrzennych. W przypadku modyfikowania zasad indeksowania przy użyciu Azure Portal można określić następujące dane JSON dla zasad indeksowania, aby włączyć indeksowanie przestrzenne w kontenerze:

**Plik JSON zasad indeksowania kontenera z indeksowaniem przestrzennym geografii**

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
> Jeśli lokalizacja GeoJSON wartości w obrębie dokumentu jest źle sformułowany lub nieprawidłowy, następnie go będzie nie indeksowanie zapytań przestrzennych. Można sprawdzić poprawność wartości lokalizacji przy użyciu ST_ISVALID i ST_ISVALIDDETAILED.
>
>
>

[Zasady indeksowania](how-to-manage-indexing-policy.md) można także modyfikować za pomocą interfejsu wiersza polecenia platformy Azure, programu PowerShell lub dowolnego zestawu SDK.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz jak rozpocząć pracę z obsługą dane geograficzne w usłudze Azure Cosmos DB, następnie można wykonywać następujące czynności:

* Dowiedz się więcej o [Azure Cosmos DB Query](sql-query-getting-started.md)
* Dowiedz się więcej o wysyłaniu [zapytań do danych przestrzennych za pomocą Azure Cosmos DB](sql-query-geospatial-query.md)
* Dowiedz się więcej o [danych lokalizacji geograficznej i GEOJSON w Azure Cosmos DB](sql-query-geospatial-intro.md)