---
title: Wprowadzenie do funkcji geoprzestrzennych usługi Azure Stream Analytics
description: W tym artykule opisano funkcje geoprzestrzenne, które są używane w zadaniach usługi Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: f47f34b60c858bb9a0feafd25176e4a811046630
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426221"
---
# <a name="introduction-to-stream-analytics-geospatial-functions"></a>Wprowadzenie do funkcji geoprzestrzennych usługi Stream Analytics

Funkcje geoprzestrzenne w usłudze Azure Stream Analytics umożliwiają analizę w czasie rzeczywistym na przesyłaniu strumieniowe danych geoprzestrzennych. Za pomocą zaledwie kilku wierszy kodu można opracować rozwiązanie klasy produkcyjnej dla złożonych scenariuszy. 

Przykłady scenariuszy, które mogą korzystać z funkcji geoprzestrzennych obejmują:

* Udostępnianie przejazdów
* Zarządzanie flotą
* Śledzenie zasobów
* Ogrodzenia geograficzne
* Śledzenie telefonu w różnych witrynach komórkowych

Stream Analytics Query Language ma siedem wbudowanych funkcji geoprzestrzennych: **CreateLineString**, **CreatePoint**, **CreatePolygon**, **ST_DISTANCE**, **ST_OVERLAPS**, **ST_INTERSECTS**i **ST_WITHIN**.

## <a name="createlinestring"></a>CreateLineString

Funkcja `CreateLineString` akceptuje punkty i zwraca GeoJSON LineString, który może być kreślony jako wiersz na mapie. Aby utworzyć linestring, musisz mieć co najmniej dwa punkty. Punkty LineString zostaną połączone w kolejności.

Poniższa kwerenda `CreateLineString` używa do utworzenia LineString przy użyciu trzech punktów. Pierwszy punkt jest tworzony na podstawie przesyłania strumieniowego danych wejściowych, podczas gdy pozostałe dwa są tworzone ręcznie.

```SQL 
SELECT  
     CreateLineString(CreatePoint(input.latitude, input.longitude), CreatePoint(10.0, 10.0), CreatePoint(10.5, 10.5))  
FROM input  
```  

### <a name="input-example"></a>Przykład wejścia  
  
|Szerokość geograficzna|Długość geograficzna|  
|--------------|---------------|  
|3.0|-10.2|  
|-87.33|20.2321|  
  
### <a name="output-example"></a>Przykład wyjścia  

 {"type" : "LineString", "coordinates" : [ [-10.2, 3.0], [10.0, 10.0], [10.5, 10.5] ]}

 {"type" : "LineString", "coordinates" : [ [20.2321, -87.33], [10.0, 10.0], [10.5, 10.5] ]}

Aby dowiedzieć się więcej, odwiedź odwołanie [CreateLineString.](https://docs.microsoft.com/stream-analytics-query/createlinestring)

## <a name="createpoint"></a>CreatePoint

Funkcja `CreatePoint` akceptuje szerokość i długość geograficzną i zwraca punkt GeoJSON, który może być wykreślony na mapie. Szerokości i długości geograficzne muszą być rodzajem danych **float.**

Poniższa przykładowa `CreatePoint` kwerenda używa do utworzenia punktu przy użyciu szerokości i długości geograficznych z przesyłania strumieniowego danych wejściowych.

```SQL 
SELECT  
     CreatePoint(input.latitude, input.longitude)  
FROM input 
```  

### <a name="input-example"></a>Przykład wejścia  
  
|Szerokość geograficzna|Długość geograficzna|  
|--------------|---------------|  
|3.0|-10.2|  
|-87.33|20.2321|  
  
### <a name="output-example"></a>Przykład wyjścia
  
 {"type" : "Point", "coordinates" : [-10.2, 3.0]}  
  
 {"type" : "Point", "coordinates" : [20.2321, -87.33]}  

Aby dowiedzieć się więcej, odwiedź odwołanie [do programu CreatePoint.](https://docs.microsoft.com/stream-analytics-query/createpoint)

## <a name="createpolygon"></a>CreatePolygon

Funkcja `CreatePolygon` akceptuje punkty i zwraca rekord wielokąta GeoJSON. Kolejność punktów musi być zgodna z orientacją pierścienia po prawej stronie lub przeciwnie do ruchu wskazówek zegara. Wyobraź sobie chodzenie z jednego punktu do drugiego w kolejności, w jakiej zostały zgłoszone. Środek wielokąta będzie pozostawiony przez cały czas.

Poniższa przykładowa `CreatePolygon` kwerenda używa do utworzenia wielokąta z trzech punktów. Pierwsze dwa punkty są tworzone ręcznie, a ostatni punkt jest tworzony na podstawie danych wejściowych.

```SQL 
SELECT  
     CreatePolygon(CreatePoint(input.latitude, input.longitude), CreatePoint(10.0, 10.0), CreatePoint(10.5, 10.5), CreatePoint(input.latitude, input.longitude))  
FROM input  
```  

### <a name="input-example"></a>Przykład wejścia  
  
|Szerokość geograficzna|Długość geograficzna|  
|--------------|---------------|  
|3.0|-10.2|  
|-87.33|20.2321|  
  
### <a name="output-example"></a>Przykład wyjścia  

 {"type" : "Polygon", "coordinates" : [[-10.2, 3.0], [10.0, 10.0], [10.5, 10.5], [-10.2, 3.0] ]]}
 
 {"type" : "Polygon", "coordinates" : [[20.2321, -87.33], [10.0, 10.0], [10.5, 10.5], [20.2321, -87.33] ]]}

Aby dowiedzieć się więcej, odwiedź odwołanie [CreatePolygon.](https://docs.microsoft.com/stream-analytics-query/createpolygon)


## <a name="st_distance"></a>ST_DISTANCE
Funkcja `ST_DISTANCE` zwraca odległość między dwoma punktami w metrach. 

Poniższa kwerenda `ST_DISTANCE` służy do generowania zdarzenia, gdy stacja benzynowa znajduje się mniej niż 10 km od samochodu.

```SQL
SELECT Cars.Location, Station.Location 
FROM Cars c  
JOIN Station s ON ST_DISTANCE(c.Location, s.Location) < 10 * 1000
```

Aby dowiedzieć się więcej, odwiedź [ST_DISTANCE](https://docs.microsoft.com/stream-analytics-query/st-distance) referencyjne.

## <a name="st_overlaps"></a>ST_OVERLAPS
Funkcja `ST_OVERLAPS` porównuje dwa wielokąty. Jeśli wielokąty nakładają się na siebie, funkcja zwraca wartość 1. Funkcja zwraca wartość 0, jeśli wielokąty nie nakładają się na siebie. 

Poniższa kwerenda `ST_OVERLAPS` używa do generowania zdarzenia, gdy budynek znajduje się w strefie możliwego zalewania.

```SQL
SELECT Building.Polygon, Building.Polygon 
FROM Building b 
JOIN Flooding f ON ST_OVERLAPS(b.Polygon, b.Polygon) 
```

Poniższa przykładowa kwerenda generuje zdarzenie, gdy burza zmierza w kierunku samochodu.

```SQL
SELECT Cars.Location, Storm.Course
FROM Cars c, Storm s
JOIN Storm s ON ST_OVERLAPS(c.Location, s.Course)
```

Aby dowiedzieć się więcej, odwiedź [ST_OVERLAPS](https://docs.microsoft.com/stream-analytics-query/st-overlaps) referencyjne.

## <a name="st_intersects"></a>ST_INTERSECTS
Funkcja `ST_INTERSECTS` porównuje dwa LineString. Jeśli LineString przecinają się, funkcja zwraca wartość 1. Funkcja zwraca wartość 0, jeśli linestring nie przecinają się.

Poniższa przykładowa `ST_INTERSECTS` kwerenda służy do określenia, czy droga utwardzona przecina polną drogę.

```SQL 
SELECT  
     ST_INTERSECTS(input.pavedRoad, input.dirtRoad)  
FROM input  
```  

### <a name="input-example"></a>Przykład wejścia  
  
|centrum danychObejm|burzaObszer|  
|--------------------|---------------|  
|{"type":"LineString", "coordinates": [ [-10.0, 0.0], [0.0, 0.0], [10.0, 0.0] ]}|{"type":"LineString", "coordinates": [ [0.0, 10.0], [0.0, 0.0], [0.0, -10.0] ]}|  
|{"type":"LineString", "coordinates": [ [-10.0, 0.0], [0.0, 0.0], [10.0, 0.0] ]}|{"type":"LineString", "coordinates": [ [-10.0, 10.0], [0.0, 10.0], [10.0, 10.0] ]}|  
  
### <a name="output-example"></a>Przykład wyjścia  

 1  
  
 0  

Aby dowiedzieć się więcej, odwiedź [ST_INTERSECTS](https://docs.microsoft.com/stream-analytics-query/st-intersects) referencyjne.

## <a name="st_within"></a>ST_WITHIN
Funkcja `ST_WITHIN` określa, czy punkt lub wielokąt znajduje się w obrębie wielokąta. Jeśli wielokąt zawiera punkt lub wielokąt, funkcja powróci 1. Funkcja zwróci 0, jeśli punkt lub wielokąt nie znajduje się w zadeklarowanym wielokąta.

Poniższa przykładowa `ST_WITHIN` kwerenda używa do określenia, czy punkt docelowy dostawy znajduje się w danym wielokącie magazynu.

```SQL 
SELECT  
     ST_WITHIN(input.deliveryDestination, input.warehouse)  
FROM input 
```  

### <a name="input-example"></a>Przykład wejścia  
  
|dostarczanieSestynacja|Magazynu|  
|-------------------------|---------------|  
|{"type":"Point", "współrzędne": [76.6, 10.1]}|{"type":"Polygon", "coordinates": [ [0.0, 0.0], [10.0, 0.0], [10.0, 10.0], [0.0, 10.0], [0.0, 0.0] ]}|  
|{"type":"Point", "współrzędne": [15.0, 15.0]}|{"type":"Polygon", "coordinates": [ [10.0, 10.0], [20.0, 10.0], [20.0, 20.0], [10.0, 20.0], [10.0, 10.0] ]}|  
  
### <a name="output-example"></a>Przykład wyjścia  

 0  
  
 1  

Aby dowiedzieć się więcej, odwiedź [ST_WITHIN](https://docs.microsoft.com/stream-analytics-query/st-within) referencyjne.

## <a name="next-steps"></a>Następne kroki

* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)
