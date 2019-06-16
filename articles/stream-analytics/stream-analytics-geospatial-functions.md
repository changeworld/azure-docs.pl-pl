---
title: Wprowadzenie do funkcji geoprzestrzennych w usłudze Azure Stream Analytics
description: W tym artykule opisano funkcje geoprzestrzenne, które są używane w zadaniach usługi Azure Stream Analytics.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: ad789a597da759b9a2d58138c7ed441389a12adb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61479987"
---
# <a name="introduction-to-stream-analytics-geospatial-functions"></a>Wprowadzenie do funkcji geoprzestrzennych usługi Stream Analytics

Funkcji Geoprzestrzennych w usłudze Azure Stream Analytics Włącz analizę w czasie rzeczywistym na strumieniu danych geoprzestrzennych. Przy użyciu zaledwie kilku wierszy kodu można utworzyć rozwiązanie klasy korporacyjnej produkcji w przypadku złożonych scenariuszy. 

Przykładowe scenariusze, które mogą korzystać z funkcji geoprzestrzennych obejmują:

* Udostępnianie jazdy
* Zarządzanie flotą
* Śledzenie zasobów
* Grodzenia
* Śledzenie Phone między lokacjami komórki

Język zapytań usługi Stream Analytics zawiera siedem wbudowanych funkcji geoprzestrzennych: **CreateLineString**, **CreatePoint**, **CreatePolygon**, **ST_DISTANCE**, **ST_OVERLAPS**, **ST_ PRZECINA**, i **ST_WITHIN**.

## <a name="createlinestring"></a>CreateLineString

`CreateLineString` Funkcja przyjmuje punktów i zwraca wartość elementu GeoJSON LineString, które można przedstawić jako linia na mapie. Musi mieć co najmniej dwa punkty, aby utworzyć element LineString. Punkty LineString będą połączone w kolejności.

Następujące zapytanie używa `CreateLineString` utworzyć element LineString przy użyciu trzy punkty. Pierwszym punktem jest tworzony z przesyłania strumieniowego danych wejściowych, a pozostałe dwa są tworzone ręcznie.

```SQL 
SELECT  
     CreateLineString(CreatePoint(input.latitude, input.longitude), CreatePoint(10.0, 10.0), CreatePoint(10.5, 10.5))  
FROM input  
```  

### <a name="input-example"></a>Przykład danych wejściowych  
  
|Szerokość geograficzna|Długość geograficzna|  
|--------------|---------------|  
|3.0|-10.2|  
|-87.33|20.2321|  
  
### <a name="output-example"></a>Przykład danych wyjściowych  

 {"type" : "LineString", "coordinates": [[-10.2, 3.0], [10.0, 10.0], [10.5, 10.5]]}

 {"type" : "LineString", "coordinates": [[20.2321,-87.33], [10.0, 10.0], [10.5, 10.5]]}

Aby dowiedzieć się więcej, odwiedź stronę [CreateLineString](https://msdn.microsoft.com/azure/stream-analytics/reference/createlinestring) odwołania.

## <a name="createpoint"></a>CreatePoint

`CreatePoint` Funkcja akceptuje szerokości i długości geograficznej i zwraca punkt GeoJSON, który można przedstawić na mapie. Twoje długości i szerokości geograficzne musi być **float** typu danych.

Poniższe przykładowe zapytanie używa `CreatePoint` spowoduje utworzenie punktu, przy użyciu długości i szerokości geograficzne z przesyłania strumieniowego danych wejściowych.

```SQL 
SELECT  
     CreatePoint(input.latitude, input.longitude)  
FROM input 
```  

### <a name="input-example"></a>Przykład danych wejściowych  
  
|Szerokość geograficzna|Długość geograficzna|  
|--------------|---------------|  
|3.0|-10.2|  
|-87.33|20.2321|  
  
### <a name="output-example"></a>Przykład danych wyjściowych
  
 {"type" : "Point", "coordinates": [-10.2, 3.0]}  
  
 {"type" : "Point", "coordinates": [20.2321,-87.33]}  

Aby dowiedzieć się więcej, odwiedź stronę [CreatePoint](https://msdn.microsoft.com/azure/stream-analytics/reference/createpoint) odwołania.

## <a name="createpolygon"></a>CreatePolygon

`CreatePolygon` Funkcja przyjmuje punktów i zwraca rekord wielokąta GeoJSON. Kolejność punktów musi stosować się orientacja pierścienia po prawej stronie, lub przeciwnie do ruchu wskazówek zegara. Wyobraź sobie zalet z jednego miejsca do drugiego w kolejności, w której zostały zgłoszone. Środek wielokąta byłoby po lewej stronie, przez cały czas.

Poniższe przykładowe zapytanie używa `CreatePolygon` utworzyć wielokąta na podstawie trzy punkty. Pierwsze dwa punkty są tworzone ręcznie, a ostatnim punktem jest tworzony w danych wejściowych.

```SQL 
SELECT  
     CreatePolygon(CreatePoint(input.latitude, input.longitude), CreatePoint(10.0, 10.0), CreatePoint(10.5, 10.5), CreatePoint(input.latitude, input.longitude))  
FROM input  
```  

### <a name="input-example"></a>Przykład danych wejściowych  
  
|Szerokość geograficzna|Długość geograficzna|  
|--------------|---------------|  
|3.0|-10.2|  
|-87.33|20.2321|  
  
### <a name="output-example"></a>Przykład danych wyjściowych  

 {"type" : "Polygon", "coordinates": [[[-10.2, 3.0], [10.0, 10.0], [10.5, 10.5], [-10.2, 3.0]]]}
 
 {"type" : "Polygon", "coordinates": [[[20.2321,-87.33], [10.0, 10.0], [10.5, 10.5], [20.2321,-87.33]]]}

Aby dowiedzieć się więcej, odwiedź stronę [CreatePolygon](https://msdn.microsoft.com/azure/stream-analytics/reference/createpolygon) odwołania.


## <a name="stdistance"></a>ST_DISTANCE
`ST_DISTANCE` Funkcja zwraca odległość między dwoma punktami w metrach. 

Następujące zapytanie używa `ST_DISTANCE` wygenerować zdarzenie, kiedy benzynowej jest mniejsza niż 10 km od samochodów.

```SQL
SELECT Cars.Location, Station.Location 
FROM Cars c  
JOIN Station s ON ST_DISTANCE(c.Location, s.Location) < 10 * 1000
```

Aby dowiedzieć się więcej, odwiedź stronę [ST_DISTANCE](https://msdn.microsoft.com/azure/stream-analytics/reference/st-distance) odwołania.

## <a name="stoverlaps"></a>ST_OVERLAPS
`ST_OVERLAPS` Funkcja porównuje dwa wielokątów. Jeśli wielokątów zachodziły na siebie, funkcja zwraca wartość 1. Funkcja zwraca 0, jeśli się nie nakładały wielokątów. 

Następujące zapytanie używa `ST_OVERLAPS` wygenerować zdarzenie, kiedy budynku mieści się możliwe przepełnieniu strefy.

```SQL
SELECT Building.Polygon, Building.Polygon 
FROM Building b 
JOIN Flooding f ON ST_OVERLAPS(b.Polygon, b.Polygon) 
```

Poniższe przykładowe zapytanie generuje zdarzenie, gdy jej kierunku samochodu platformy storm.

```SQL
SELECT Cars.Location, Storm.Course
FROM Cars c, Storm s
JOIN Storm s ON ST_OVERLAPS(c.Location, s.Course)
```

Aby dowiedzieć się więcej, odwiedź stronę [ST_OVERLAPS](https://msdn.microsoft.com/azure/stream-analytics/reference/st-overlaps) odwołania.

## <a name="stintersects"></a>ST_INTERSECTS
`ST_INTERSECTS` Funkcja porównuje dwa LineString. Jeśli część wspólna LineString, funkcja zwraca 1. Jeśli nie przecinają LineString, funkcja zwraca wartość 0.

Poniższe przykładowe zapytanie używa `ST_INTERSECTS` ustalenie, jeśli ułożonych drogowej przecina drogowej damska.

```SQL 
SELECT  
     ST_INTERSECTS(input.pavedRoad, input.dirtRoad)  
FROM input  
```  

### <a name="input-example"></a>Przykład danych wejściowych  
  
|datacenterArea|stormArea|  
|--------------------|---------------|  
|{"type": "LineString", "coordinates": [[-10.0, 0.0], [0.0, 0.0], [10.0, 0.0]]}|{"type": "LineString", "coordinates": [[0.0, 10.0], [0.0, 0.0], [0.0,-10.0]]}|  
|{"type": "LineString", "coordinates": [[-10.0, 0.0], [0.0, 0.0], [10.0, 0.0]]}|{"type": "LineString", "coordinates": [[-10.0, 10.0], [0.0, 10.0], [10.0, 10.0]]}|  
  
### <a name="output-example"></a>Przykład danych wyjściowych  

 1  
  
 0  

Aby dowiedzieć się więcej, odwiedź stronę [ST_INTERSECTS](https://msdn.microsoft.com/azure/stream-analytics/reference/st-intersects) odwołania.

## <a name="stwithin"></a>ST_WITHIN
`ST_WITHIN` Funkcji określa, czy punkt lub wielokątów w ramach wielokąta. Jeśli wielokąta zawiera punktów i wielokątów, funkcja zwróci wartość 1. Funkcja zwróci wartość 0 Jeśli punktów i wielokątów nie znajduje się w ramach zadeklarowany wielokąta.

Poniższe przykładowe zapytanie używa `ST_WITHIN` ustalenie, czy dostarczania docelowy punkt znajduje się w wielokąta danego magazynu.

```SQL 
SELECT  
     ST_WITHIN(input.deliveryDestination, input.warehouse)  
FROM input 
```  

### <a name="input-example"></a>Przykład danych wejściowych  
  
|deliveryDestination|Magazyn|  
|-------------------------|---------------|  
|{"type": "Point", "coordinates": [76.6, 10.1]}|{"type": "Polygon", "coordinates": [[0.0, 0.0], [10.0, 0.0], [10.0, 10.0], [0.0, 10.0], [0.0, 0.0]]}|  
|{"type": "Point", "coordinates": [15.0, 15.0]}|{"type": "Polygon", "coordinates": [[10.0, 10.0], [20,0, 10.0], [20.0, 20.0], [10.0, 20,0], [10.0, 10.0]]}|  
  
### <a name="output-example"></a>Przykład danych wyjściowych  

 0  
  
 1  

Aby dowiedzieć się więcej, odwiedź stronę [ST_WITHIN](https://msdn.microsoft.com/azure/stream-analytics/reference/st-within) odwołania.

## <a name="next-steps"></a>Kolejne kroki

* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)