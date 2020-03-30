---
title: Szczegóły obsługiwanego formatu danych | Mapy platformy Microsoft Azure
description: Dowiedz się, jak rozdzielone dane przestrzenne są analizowane w module we/wy przestrzennej.
author: philmea
ms.author: philmea
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 3353620f1751e939a04543115fe704555fb3bc21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334081"
---
# <a name="supported-data-format-details"></a>Szczegóły obsługiwanego formatu danych

Ten artykuł zawiera szczegółowe informacje na temat obsługi odczytu i zapisu dla wszystkich tagów XML i typów geometrii dobrze znanego tekstu. Wszczegła również sposób analizowanie rozdzielonych danych przestrzennych w module we/wy przestrzennej.

## <a name="supported-xml-namespaces"></a>Obsługiwane przestrzenie nazw XML

Przestrzenny moduł We/Wy obsługuje znaczniki XML z następujących obszarów nazw.

| Prefiks obszaru nazw | Identyfikator URI obszaru nazw   | Uwagi                                                                    |
|:------------------|:-----------------|:----------------------------------------|
| `atom`           | `http://www.w3.org/2005/Atom`   |                                         |
| `geo`            | `http://www.w3.org/2003/01/geo/wgs84_pos#`  | Obsługa tylko do odczytu w plikach GeoRSS.           |
| `georss`         | `http://www.georss.org/georss`  |                                                |
| `geourl`         | `http://geourl.org/rss/module/` | Obsługa tylko do odczytu w plikach GeoRSS.                       |
| `gml`            | `http://www.opengis.net/gml`    |                                                        |
| `gpx`            | `http://www.topografix.com/GPX/1/1` |                                                   |
| `gpxx`           | `http://www.garmin.com/xmlschemas/GpxExtensions/v3` | Obsługa tylko do odczytu w plikach GPX. Analizuje i używa DisplayColor. Wszystkie inne właściwości dodane do metadanych kształtu. |
| `gpx_style`      | `http://www.topografix.com/GPX/gpx_style/0/2`      | Obsługiwane w plikach GPX. Używa koloru linii. |
| `gx`             | `http://www.google.com/kml/ext/2.2` |                                                      |
| `kml`            | `http://www.opengis.net/kml/2.2`    |                                                      |
| `rss`            |                                 | Tylko do odczytu. GeoRSS pisze przy użyciu formatu Atom.              |

## <a name="supported-xml-elements"></a>Obsługiwane elementy XML

Przestrzenny moduł We/Wy obsługuje następujące elementy XML. Wszystkie znaczniki XML, które nie są obsługiwane, zostaną przekonwertowane na obiekt JSON. Następnie każdy znacznik zostanie dodany jako `properties` właściwość w polu kształtu lub warstwy nadrzędnej.

### <a name="kml-elements"></a>Elementy KML

Przestrzenny moduł We/Wy obsługuje następujące elementy KML.

| Nazwa elementu         | Odczyt    | Zapisywanie   | Uwagi                                                                                                                      |
|----------------------|---------|---------|----------------------------------------------------------------------------------------------------------------------------|
| `address`            | partial | tak     | Obiekt jest analizowany, ale nie jest używany do pozycjonowania kształtu.                                                                    |
| `AddressDetails`     | partial | nie      | Obiekt jest analizowany, ale nie jest używany do pozycjonowania kształtu.                                                                    |
| `atom:author`        | tak     | tak     |                                                                                                                            |
| `atom:link`          | tak     | tak     |                                                                                                                            |
| `atom:name`          | tak     | tak     |                                                                                                                            |
| `BalloonStyle`       | partial | partial | `displayMode`nie jest obsługiwany. Konwertowane `PopupTemplate`na . Aby napisać, `popupTemplate` dodaj właściwość jako właściwość funkcji, dla której chcesz ją napisać. |
| `begin`              | tak     | tak     |                                                                                                                            |
| `color`              | tak     | tak     | Zawiera `#AABBGGRR` `#BBGGRR`i . Analizowane w ciągu koloru CSS                                                           |
| `colorMode`          | tak     | nie      |                                                                                                                            |
| `coordinates`        | tak     | tak     |                                                                                                                            |
| `Data`               | tak     | tak     |                                                                                                                            |
| `description`        | tak     | tak     |                                                                                                                            |
| `displayName`        | tak     | tak     |                                                                                                                            |
| `Document`           | tak     | tak     |                                                                                                                            |
| `drawOrder`          | partial | nie      | Przeczytaj o nakładki uziemienia i używane do ich sortowania. 
| `east`               | tak     | tak     |                                                                                                                            |
| `end`                | tak     | tak     |                                                                                                                            |
| `ExtendedData`       | tak     | tak     | `Data`Obsługuje nietypowe lub `SimpleData` `Schema`, i zastąpienie `$[dataName]`encji formularza .                      |
| `extrude`            | partial | partial | Obsługiwane tylko dla wielokątów. MultiGeometria, które mają wielokąty o różnych wysokościach, zostanie podzielona na poszczególne obiekty. Style linii nie są obsługiwane. Wielokąty o wysokości 0 będą renderowane jako płaski wielokąt. Podczas odczytu wysokość pierwszej współrzędnej w pierścieniu zewnętrznym zostanie dodana jako właściwość wysokości wielokąta. Następnie wysokość pierwszej współrzędnej zostanie użyta do renderowania wielokąta na mapie. |
| `fill`               | tak     | tak     |                                                                                                                            |
| `Folder`             | tak     | tak     |                                                                                                                            |
| `GroundOverlay`      | tak     | tak     | `color`nie jest obsługiwany                                                                                                   |
| `heading`            | partial | nie      | Przeanalizowane, ale nie `SimpleDataLayer`renderowane przez . Zapisuje tylko wtedy, gdy dane są przechowywane we właściwości kształtu.                 |
| `hotSpot`            | tak     | partial | Zapisuje tylko wtedy, gdy dane są przechowywane we właściwości kształtu. Jednostki są wyprowadzane tylko jako "piksele".                         |
| `href`               | tak     | tak     |                                                                                                                            |
| `Icon`               | partial | partial | Przeanalizowane, ale nie `SimpleDataLayer`renderowane przez . Pisze właściwość ikony kształtu tylko wtedy, gdy zawiera dane identyfikatora URI. Obsługiwane `href` jest tylko. |
| `IconStyle`          | partial | partial | `icon`, `heading` `colorMode`, `hotspots` a wartości są analizowane, ale nie są renderowane przez`SimpleDataLayer`         |
| `innerBoundaryIs`    | tak     | tak     |                                                                                                                            |
| `kml`                | tak     | tak     |                                                                                                                            |
| `LabelStyle`         | nie      | nie      |                                                                                                                            |
| `LatLonBox`          | tak     | tak     |                                                                                                                            |
| `gx:LatLonQuad`      | tak     | tak     |                                                                                                                            |
| `LinearRing`         | tak     | tak     |                                                                                                                            |
| `LineString`         | tak     | tak     |                                                                                                                            |
| `LineStyle`          | tak     | tak     | `colorMode`nie jest obsługiwany.                                                                                         |
| `Link`               | tak     | nie      | Tylko `href` właściwość jest obsługiwana dla łączy sieciowych.                                                                   |
| `MultiGeometry`      | partial | partial | Może być podzielony na poszczególne funkcje podczas czytania.                                                                     |
| `name`               | tak     | tak     |                                                                                                                            |
| `NetworkLink`        | tak     | nie      | Łącza muszą znajdować się w tej samej domenie co dokument.                                                                  |
| `NetworkLinkControl` | nie      | nie      |                                                                                                                            |
| `north`              | tak     | tak     |                                                                                                                            |
| `open`               | tak     | tak     |                                                                                                                            |
| `outerBoundaryIs`    | tak     | tak     |                                                                                                                            |
| `outline`            | tak     | tak     |                                                                                                                            |
| `overlayXY`          | nie      | nie      |                                                                                                                            |
| `Pair`               | partial | nie      | Obsługiwany `normal` jest tylko `StyleMap` styl w a. `highlight`nie jest obsługiwany.                                   |
| `phoneNumber`        | tak     | tak     |                                                                                                                            |
| `PhotoOverlay`       | nie      | nie      |                                                                                                                            |
| `Placemark`          | tak     | tak     |                                                                                                                            |
| `Point`              | tak     | tak     |                                                                                                                            |
| `Polygon`            | tak     | tak     |                                                                                                                            |
| `PolyStyle`          | tak     | tak     |                                                                                                                            |
| `Region`             | partial | partial | `LatLongBox`jest obsługiwana na poziomie dokumentu.                                                                      |
| `rotation`           | nie      | nie      |                                                                                                                            |
| `rotationXY`         | nie      | nie      |                                                                                                                            |
| `scale`              | nie      | nie      |                                                                                                                            |
| `Schema`             | tak     | tak     |                                                                                                                            |
| `SchemaData`         | tak     | tak     |                                                                                                                            |
| `schemaUrl`          | partial | tak     | Nie obsługuje stylów ładowania z dokumentów zewnętrznych, które nie są uwzględnione w pliku KMZ.                             |
| `ScreenOverlay`      | nie      | nie      |                                                                                                                            |
| `screenXY`           | nie      | nie      |                                                                                                                            |
| `SimpleData`         | tak     | tak     |                                                                                                                            |
| `SimpleField`        | tak     | tak     |                                                                                                                            |
| `size`               | nie      | nie      |                                                                                                                            |
| `Snippet`            | partial | partial | `maxLines`atrybut jest ignorowany.                                                                                  |
| `south`              | tak     | tak     |                                                                                                                            |
| `Style`              | tak     | tak     |                                                                                                                            |
| `StyleMap`           | partial | nie      | Obsługiwany jest tylko `StyleMap` normalny styl w a.                                                                        |
| `styleUrl`           | partial | tak     | Adresy URL stylu zewnętrznego nie są obsługiwane.                                                                         |
| `text`               | tak     | tak     | Wymiana `$[geDirections]` nie jest obsługiwana                                                                          |
| `textColor`          | tak     | tak     |                                                                                                                            |
| `TimeSpan`           | tak     | tak     |                                                                                                                            |
| `TimeStamp`          | tak     | tak     |                                                                                                                            |
| `value`              | tak     | tak     |                                                                                                                            |
| `viewRefreshMode`    | partial | nie      |  Jeśli wskazuje na usługę WMS, `onStop` to tylko jest obsługiwany dla nakładek uziemienia. Dołączy `BBOX=[bboxWest],[bboxSouth],[bboxEast],[bboxNorth]` do adresu URL i zaktualizuje się w miarę przesuwania się mapy.  |
| `visibility`         | tak     | tak     |                                                                                                                            |
| `west`               | tak     | tak     |                                                                                                                            |
| `when`               | tak     | tak     |                                                                                                                            |
| `width`              | tak     | tak     |                                                                                                                            |

### <a name="georss-elements"></a>Elementy GeoRSS

Przestrzenny moduł We/Wy obsługuje następujące elementy GeoRSS.

| Nazwa elementu             | Odczyt    | Zapisywanie | Uwagi                                                                                          |
|--------------------------|---------|-------|------------------------------------------------------------------------------------------------|
| `atom:author`            | tak     | tak   |                                                                                                |
| `atom:category`          | tak     | tak   |                                                                                                |
| `atom:content`           | tak     | tak   |                                                                                                |
| `atom:contributor`       | tak     | tak   |                                                                                                |
| `atom:email`             | tak     | tak   |                                                                                                |
| `atom:entry`             | tak     | tak   |                                                                                                |
| `atom:feed`              | tak     | tak   |                                                                                                |
| `atom:icon`              | tak     | tak   |                                                                                                |
| `atom:id`                | tak     | tak   |                                                                                                |
| `atom:link`              | tak     | tak   |                                                                                                |
| `atom:logo`              | tak     | tak   |                                                                                                |
| `atom:name`              | tak     | tak   |                                                                                                |
| `atom:published`         | tak     | tak   |                                                                                                |
| `atom:rights`            | tak     | tak   |                                                                                                |
| `atom:source`            | tak     | tak   |                                                                                                |
| `atom:subtitle`          | tak     | tak   |                                                                                                |
| `atom:summary`           | tak     | tak   |                                                                                                |
| `atom:title`             | tak     | tak   |                                                                                                |
| `atom:updated`           | tak     | tak   |                                                                                                |
| `atom:uri`               | tak     | tak   |                                                                                                |
| `geo:lat`                | tak     | nie    | Napisane jako `georss:point`.                                                                   |
| `geo:lon`                | tak     | nie    | Napisane jako `georss:point`.                                                                   |
| `geo:long`               | tak     | nie    | Napisane jako `georss:point`.                                                                   |
| `georss:box`             | tak     | nie    | Czytaj jako wielokąt i `subType` nadaj właściwość "Prostokąt"                                |
| `georss:circle`          | tak     | tak   |                                                                                                |
| `georss:elev`            | tak     | tak   |                                                                                                |
| `georss:featurename`     | tak     | tak   |                                                                                                |
| `georss:featuretypetag`  | tak     | tak   |                                                                                                |
| `georss:floor`           | tak     | tak   |                                                                                                |
| `georss:line`            | tak     | tak   |                                                                                                |
| `georss:point`           | tak     | tak   |                                                                                                |
| `georss:polygon`         | tak     | tak   |                                                                                                |
| `georss:radius`          | tak     | tak   |                                                                                                |
| `georss:relationshiptag` | tak     | tak   |                                                                                                |
| `georss:where`           | tak     | tak   |                                                                                                |
| `geourl:latitude`        | tak     | nie    | Napisane jako `georss:point`.                                                                   |
| `geourl:longitude`       | tak     | nie    | Napisane jako `georss:point`.                                                                   |
| `position`               | tak     | nie    | Niektóre źródła danych XML będą zawijać GML znacznikiem pozycji zamiast zawijać go tagiem. `georss:where` Odczytie tego tagu, ale `georss:where` napisze za pomocą tagu. |
| `rss`                    | tak     | nie    | GeoRSS napisany w formacie ATOM.                                                                 |
| `rss:author`             | tak     | partial | Napisane jako `atom:author`.                                                                 |
| `rss:category`           | tak     | partial | Napisane jako `atom:category`.                                                               |
| `rss:channel`            | tak     | nie    |                                                                                                |
| `rss:cloud`              | tak     | nie    |                                                                                                |
| `rss:comments`           | tak     | nie    |                                                                                                |
| `rss:copyright`          | tak     | partial | Napisany jako `atom:rights` kształt if nie ma `rights` `properties` już właściwości.       |
| `rss:description`        | tak     | partial | Napisany jako `atom:content` kształt if nie ma `content` `properties` już właściwości.      |
| `rss:docs`               | tak     | nie    |                                                                                                |
| `rss:enclosure`          | tak     | nie    |                                                                                                |
| `rss:generator`          | tak     | nie    |                                                                                                |
| `rss:guid`               | tak     | partial | Napisany jako `atom:id` kształt if nie ma `id` `properties` już właściwości.         |
| `rss:image`              | tak     | partial | Napisany jako `atom:logo` kształt if nie ma `logo` `properties` już właściwości.      |
| `rss:item`               | tak     | partial | Napisane jako `atom:entry`.                                                                  |
| `rss:language`           | tak     | nie    |                                                                                                |
| `rss:lastBuildDate`      | tak     | partial | Napisany jako `atom:updated` kształt if nie ma `updated` `properties` już właściwości.     |
| `rss:link`               | tak     | partial | Napisane jako `atom:link`.                                                                   |
| `rss:managingEditor`     | tak     | partial | Napisane jako `atom:contributor`.                                                            |
| `rss:pubDate`            | tak     | partial | Napisany jako `atom:published` kształt if nie ma `published` `properties` już właściwości.  |
| `rss:rating`             | tak     | nie    |                                                                                                |
| `rss:skipDays`           | tak     | nie    |                                                                                                |
| `rss:skipHours`          | tak     | nie    |                                                                                                |
| `rss:source`             | tak     | partial | Napisane jako `atom:source` zawierające `atom:link`.                                       |
| `rss:textInput`          | tak     | nie    |                                                                                                |
| `rss:title`              | tak     | partial | Napisane jako `atom:title`.                                                                  |
| `rss:ttl`                | tak     | nie    |                                                                                                |
| `rss:webMaster`          | tak     | nie    |                                                                                                |

### <a name="gml-elements"></a>Elementy GML

Przestrzenny moduł We/Wy obsługuje następujące elementy GML. 

| Nazwa elementu            | Odczyt | Zapisywanie | Uwagi                                                                                  |
|-------------------------|------|-------|----------------------------------------------------------------------------------------|
| `gml:coordinates`       | tak  | nie    | Napisane jako `gml:posList`.                                                              |
| `gml:curveMember`       | tak  | nie    |                                                                                        |
| `gml:curveMembers`      | tak  | nie    |                                                                                        |
| `gml:Box`               | tak  | nie    | Napisane jako `gml:Envelope`.                                                             |
| `gml:description`       | tak  | tak   |                                                                                        |
| `gml:Envelope`          | tak  | tak   |                                                                                        |
| `gml:exterior`          | tak  | tak   |                                                                                        |
| `gml:Feature`           | tak  | nie    | Napisany jako kształt.                                                                    |
| `gml:FeatureCollection` | tak  | nie    | Napisana jako kolekcja geometrii.                                                      |
| `gml:featureMember`     | tak  | nie    | Napisana jako kolekcja geometrii.                                                      |
| `gml:geometry`          | tak  | nie    | Napisany jako kształt.                                                                    |
| `gml:geometryMember`    | tak  | tak   |                                                                                        |
| `gml:geometryMembers`   | tak  | tak   |                                                                                        |
| `gml:identifier`        | tak  | tak   |                                                                                        |
| `gml:innerBoundaryIs`   | tak  | nie    | Napisane przy `gml.interior`użyciu .                                                          |
| `gml:interior`          | tak  | tak   |                                                                                        |
| `gml:LinearRing`        | tak  | tak   |                                                                                        |
| `gml:LineString`        | tak  | tak   |                                                                                        |
| `gml:lineStringMember`  | tak  | tak   |                                                                                        |
| `gml:lineStringMembers` | tak  | nie    |                                                                                        |
| `gml:MultiCurve`        | tak  | nie    | Odczytuje `gml:LineString` tylko członków. Napisane jako`gml.MultiLineString`                  |
| `gml:MultiGeometry`     | partial  | partial   | Czytaj tylko jako FeatureCollection.                                              |
| `gml:MultiLineString`   | tak  | tak   |                                                                                        |
| `gml:MultiPoint`        | tak  | tak   |                                                                                        |
| `gml:MultiPolygon`      | tak  | tak   |                                                                                        |
| `gml:MultiSurface`      | tak  | nie    | Odczytuje `gml:Polygon` tylko członków. Napisane jako`gml.MultiPolygon`                        |
| `gml:name`              | tak  | tak   |                                                                                        |
| `gml:outerBoundaryIs`   | tak  | nie    | Napisane przy `gml.exterior`użyciu .                                                          |
| `gml:Point`             | tak  | tak   |                                                                                        |
| `gml:pointMember`       | tak  | tak   |                                                                                        |
| `gml:pointMembers`      | tak  | nie    |                                                                                        |
| `gml:Polygon`           | tak  | tak   |                                                                                        |
| `gml:polygonMember`     | tak  | tak   |                                                                                        |
| `gml:polygonMembers`    | tak  | nie    |                                                                                        |
| `gml:pos`               | tak  | tak   |                                                                                        |
| `gml:posList`           | tak  | tak   |                                                                                        |
| `gml:surfaceMember`     | tak  | tak   |                                                                                        |

#### <a name="additional-notes"></a>dodatkowe uwagi

- Elementy członkowskie będą wyszukiwane w poszukiwaniu geometrii, która może być zakopana w elementach podrzędnych. Ta operacja wyszukiwania jest konieczne, jak wiele formatów XML, które rozciągają się od GML nie może umieścić geometrii jako bezpośredni element podrzędny elementu członkowskiego.
- `srsName`jest częściowo obsługiwany dla współrzędnych WGS84 i następujących kodów:[EPSG:4326](https://epsg.io/4326)) i web Mercator ([EPSG:3857](https://epsg.io/3857) lub jeden z jego alternatywnych kodów. Każdy inny układ współrzędnych będzie analizowany jako WGS84 w stanie as-is.
- O ile nie określono podczas odczytywania źródła danych XML, kolejność osi jest określana na podstawie wskazówek w kanale XML. Preferuje się kolejność osi "szerokość geograficzna, długość geograficzna".
- O ile niestandardowy obszar nazw GML jest określony dla właściwości podczas zapisywania do pliku GML, dodatkowe informacje o właściwościach nie zostaną dodane.

### <a name="gpx-elements"></a>Elementy GPX

Przestrzenny moduł We/Wy obsługuje następujące elementy GPX.

| Nazwa elementu             | Odczyt    | Zapisywanie   | Uwagi                                                                                       |
|--------------------------|---------|---------|---------------------------------------------------------------------------------------------|
| `gpx:ageofdgpsdata`      | tak     | tak     |                                                                                             |
| `gpx:author`             | tak     | tak     |                                                                                             |
| `gpx:bounds`             | tak     | tak     | Konwertowane na LocationRect podczas odczytu.                                                    |
| `gpx:cmt`                | tak     | tak     |                                                                                             |
| `gpx:copyright`          | tak     | tak     |                                                                                             |
| `gpx:desc`               | tak     | tak     | Skopiowane do właściwości opisu podczas odczytu, aby wyrównać z innymi formatami XML.               |
| `gpx:dgpsid`             | tak     | tak     |                                                                                             |
| `gpx:ele`                | tak     | tak     |                                                                                             |
| `gpx:extensions`         | partial | partial | Podczas odczytu informacje o stylu są wyodrębniane. Wszystkie inne rozszerzenia zostaną spłaszczone w prosty obiekt JSON. Zapisywane są tylko informacje o stylu kształtu. |
| `gpx:geoidheight`        | tak     | tak     |                                                                                             |
| `gpx:gpx`                | tak     | tak     |                                                                                             |
| `gpx:hdop`               | tak     | tak     |                                                                                             |
| `gpx:link`               | tak     | tak     |                                                                                             |
| `gpx:magvar`             | tak     | tak     |                                                                                             |
| `gpx:metadata`           | tak     | tak     |                                                                                             |
| `gpx:name`               | tak     | tak     |                                                                                             |
| `gpx:pdop`               | tak     | tak     |                                                                                             |
| `gpx:rte`                | tak     | tak     |                                                                                             |
| `gpx:rtept`              | tak     | tak     |                                                                                             |
| `gpx:sat`                | tak     | tak     |                                                                                             |
| `gpx:src`                | tak     | tak     |                                                                                             |
| `gpx:sym`                | tak     | tak     | Wartość jest przechwytywany, ale nie jest używany do zmiany ikony pinezki.                               |
| `gpx:text`               | tak     | tak     |                                                                                             |
| `gpx:time`               | tak     | tak     |                                                                                             |
| `gpx:trk`                | tak     | tak     |                                                                                             |
| `gpx:trkpt`              | tak     | tak     |                                                                                             |
| `gpx:trkseg`             | tak     | tak     |                                                                                             |
| `gpx:type`               | tak     | tak     |                                                                                             |
| `gpx:vdop`               | tak     | tak     |                                                                                             |
| `gpx:wpt`                | tak     | tak     |                                                                                             |
| `gpx_style:color`        | tak     | tak     |                                                                                             |
| `gpx_style:line`         | partial | partial | `color`, `opacity` `width`, `lineCap` są obsługiwane.                                           |
| `gpx_style:opacity`      | tak     | tak     |                                                                                             |
| `gpx_style:width`        | tak     | tak     |                                                                                             |
| `gpxx:DisplayColor`      | tak     | nie      | Służy do określania koloru kształtu. Podczas pisania `gpx_style:line` zamiast tego zostanie użyty kolor.  |
| `gpxx:RouteExtension`    | partial | nie      | Wszystkie właściwości są `properties`odczytywane w pliku . Używany `DisplayColor` jest tylko plik.                     |
| `gpxx:TrackExtension`    | partial | nie      | Wszystkie właściwości są `properties`odczytywane w pliku . Używany `DisplayColor` jest tylko plik.                     |
| `gpxx:WaypointExtension` | partial | nie      | Wszystkie właściwości są `properties`odczytywane w pliku . Używany `DisplayColor` jest tylko plik.                     |
| `gpx:keywords`           | tak     | tak     |                                                                                             |
| `gpx:fix`                | tak     | tak     |                                                                                             |

#### <a name="additional-notes"></a>dodatkowe uwagi

Podczas pisania;

- MultiPoints zostaną podzielone na poszczególne punkty trasy.
- Wielokąty i Wielopologony będą zapisywane jako utwory. 
  
## <a name="supported-well-known-text-geometry-types"></a>Obsługiwane typy geometrii dobrze znanego tekstu

| Typ geometrii | Odczyt | Zapisywanie |
|--------------|:----:|:-----:|
| Punkt | x | x |
| PUNKT Z | x | x | 
| PUNKT M | x | x<sup>[2]</sup> |
| PUNKT ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| Linestring | x | x |
| SZNUREK Z | x | x | 
| SZNUREK M | x | x<sup>[2]</sup> |
| SZNUREK ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| Wielokąt | x | x |
| POLYGON Z | x | x |
| POLYGON M | x | x<sup>[2]</sup> |
| POLYGON ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| Multipoint | x | x |
| MULTIPOINT Z | x | x | 
| MULTIPOINT M | x | x<sup>[2]</sup> |
| POMULTIPOINTINT ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| Multilinestring | x | x |
| WIELOLINIOWY Z | x | x | 
| WIELOLINIOWY M | x | x<sup>[2]</sup> |
| WIELOLINIOWY ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| Multipolygon | x | x |
| MULTIPOLYGON Z | x | x | 
| WIELOPOLYGON M | x | x<sup>[2]</sup> |
| WIELOPOLONIOWY ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| Geometrycollection | x | x |
| GEOMETRIAKOLEJA Z | x | x | 
| GEOMETRIAKOLEJA M | x | x<sup>[2]</sup> | 
| GEOMETRYCOLLECTION ZM | x<sup>[1]</sup><sup>[2]</sup> | x | 

\[1\] Tylko parametr Z jest przechwytywany i dodawany jako trzecia wartość w wartości Position.

\[Parametr\] 2 M nie jest przechwytywany.

## <a name="delimited-spatial-data-support"></a>Obsługa rozdzielonych danych przestrzennych

Rozdzielane dane przestrzenne, takie jak pliki wartości oddzielone przecinkami (CSV), często zawierają kolumny zawierające dane przestrzenne. Na przykład mogą istnieć kolumny zawierające informacje o szerokości i długości geograficznej. W formacie Dobrze znany tekst może istnieć kolumna zawierająca dane geometrii przestrzennej.

### <a name="spatial-data-column-detection"></a>Wykrywanie kolumn danych przestrzennych

Podczas odczytywania rozdzielanego pliku zawierającego dane przestrzenne nagłówek będzie analizowany w celu określenia, które kolumny zawierają pola lokalizacji. Jeśli nagłówek zawiera informacje o typie, będzie on używany do rzutu wartości komórek na odpowiedni typ. Jeśli nie zostanie określony nagłówek, pierwszy wiersz zostanie przeanalizowany i użyty do wygenerowania nagłówka. Podczas analizowania pierwszego wiersza, sprawdzanie jest wykonywane w celu dopasowania nazwy kolumn z następujących nazw w sposób bez uwzględniania wielkości liter. Kolejność nazw jest priorytetem, w przypadku gdy w pliku istnieją dwie lub więcej nazw.

#### <a name="latitude"></a>Szerokość geograficzna

- `latitude`
- `lat`
- `latdd`
- `lat_dd`
- `latitude83`
- `latdecdeg`
- `y`
- `ycenter`
- `point-y`

#### <a name="longitude"></a>Długość geograficzna

- `longitude`
- `lon`
- `lng`
- `long`
- `longdd`
- `long_dd`
- `longitude83`
- `longdecdeg`
- `x`
- `xcenter`
- `point-x`

#### <a name="elevation"></a>Wysokość

- `elevation`
- `elv`
- `altitude`
- `alt`
- `z`

#### <a name="geography"></a>Lokalizacja geograficzna

Pierwszy wiersz danych zostanie przeskanowany w poszukiwaniu ciągów w formacie Dobrze znany tekst. 

### <a name="delimited-data-column-types"></a>Rozdzielane typy kolumn danych

Podczas skanowania wiersza nagłówka wszelkie informacje o typie, które znajdują się w nazwie kolumny, zostaną wyodrębnione i użyte do rzutowania komórek w tej kolumnie. Oto przykład nazwy kolumny, która ma wartość typu: "ColumnName (typeName)". Obsługiwane są następujące nazwy typów bez uwzględniania wielkości liter:

#### <a name="numbers"></a>Numery

- edm.int64
- int
- long
- edm.double
- float
- double
- numer

#### <a name="booleans"></a>Wartości logiczne

- edm.boolean
- bool
- wartość logiczna

#### <a name="dates"></a>Daty

- edm.datetime
- date
- datetime

#### <a name="geography"></a>Lokalizacja geograficzna

- edm.geografia
- Geografia

#### <a name="strings"></a>Ciągi

- ciąg edm.string
- varchar
- tekst
- ciąg

Jeśli z nagłówka nie można wyodrębnić żadnych informacji o typie, a opcja dynamicznego pisania jest włączona podczas odczytywania, każda komórka zostanie indywidualnie przeanalizowana w celu określenia, jaki typ danych najlepiej nadaje się do rzutowania.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej przykładów kodu do dodania do map, zobacz następujące artykuły:

> [!div class="nextstepaction"]
> [Odczytywanie i zapisywanie danych przestrzennych](spatial-io-read-write-spatial-data.md)
