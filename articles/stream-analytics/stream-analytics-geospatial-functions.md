---
title: Wprowadzenie do Azure Stream Analytics funkcji geoprzestrzennych
description: W tym artykule opisano funkcje geograficzne, które są używane w Azure Stream Analytics zadaniach.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: dfbe7e607395006f9bd7da0be0d5673353e2801f
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73162598"
---
# <a name="introduction-to-stream-analytics-geospatial-functions"></a>Wprowadzenie do Stream Analytics funkcji geoprzestrzennych

Funkcje geoprzestrzenne w Azure Stream Analytics włączają analizę w czasie rzeczywistym w przypadku przesyłania strumieniowego danych geoprzestrzennych. Za pomocą zaledwie kilku wierszy kodu możesz opracować rozwiązanie klasy produkcyjnej dla złożonych scenariuszy. 

Przykładowe scenariusze, które mogą korzystać z funkcji geoprzestrzennych, to m.in.:

* Udostępnianie przez jadące
* Zarządzanie flotą
* Śledzenie zasobów
* Ogrodzenie geograficzne
* Śledzenie telefonów w witrynach komórkowych

Język zapytań Stream Analytics ma siedem wbudowanych funkcji geoprzestrzennych: **CreateLineString**, **ST_DISTANCE**, ST_OVERLAPS **,** **ST_INTERSECTS**i **ST_WITHIN**.

## <a name="createlinestring"></a>CreateLineString

Funkcja `CreateLineString` akceptuje punkty i zwraca LineString GEOJSON, które mogą być kreślone jako linia na mapie. Musisz mieć co najmniej dwa punkty, aby utworzyć LineString. Punkty LineString zostaną połączone w pożądanej kolejności.

Poniższe zapytanie używa `CreateLineString`, aby utworzyć LineString przy użyciu trzech punktów. Pierwszy punkt jest tworzony na podstawie danych wejściowych przesyłanych strumieniowo, a pozostałe dwa są tworzone ręcznie.

```SQL 
SELECT  
     CreateLineString(CreatePoint(input.latitude, input.longitude), CreatePoint(10.0, 10.0), CreatePoint(10.5, 10.5))  
FROM input  
```  

### <a name="input-example"></a>Przykład danych wejściowych  
  
|rodziny|Długość geograficzna|  
|--------------|---------------|  
|3.0|-10,2|  
|-87,33|20,2321|  
  
### <a name="output-example"></a>Przykład danych wyjściowych  

 {"Type": "LineString", "współrzędne": [[-10,2, 3,0], [10,0, 10,0], [10,5, 10,5]]}

 {"Type": "LineString", "współrzędne": [[20,2321,-87,33], [10,0, 10,0], [10,5, 10,5]]}

Aby dowiedzieć się więcej, odwiedź odwołanie [CreateLineString](https://docs.microsoft.com/stream-analytics-query/createlinestring) .

## <a name="createpoint"></a>CreatePoint

Funkcja `CreatePoint` akceptuje szerokość i długość geograficzną oraz zwraca punkt GEOJSON, który można wykreślić na mapie. Latitudes i Długość geograficzna muszą być typu **zmiennoprzecinkowego** .

Poniższe przykładowe zapytanie używa `CreatePoint` do tworzenia punktów przy użyciu Latitudes i długości geograficznej danych wejściowych przesyłanych strumieniowo.

```SQL 
SELECT  
     CreatePoint(input.latitude, input.longitude)  
FROM input 
```  

### <a name="input-example"></a>Przykład danych wejściowych  
  
|rodziny|Długość geograficzna|  
|--------------|---------------|  
|3.0|-10,2|  
|-87,33|20,2321|  
  
### <a name="output-example"></a>Przykład danych wyjściowych
  
 {"Type": "Point", "współrzędne": [-10,2, 3,0]}  
  
 {"Type": "Point", "współrzędne": [20,2321,-87,33]}  

Aby dowiedzieć się więcej, odwiedź odwołanie do [punktu](https://docs.microsoft.com/stream-analytics-query/createpoint) .

## <a name="createpolygon"></a>CreatePolygon

Funkcja `CreatePolygon` akceptuje punkty i zwraca rekord wielokąta GEOJSON. Kolejność punktów musi następować po prawej stronie lub w prawo. Wyobraź sobie, że jest to idące od jednego punktu do drugiego w kolejności, w jakiej zostały zgłoszone. Środek wielokąta będzie znajdował się w lewej i cały czas.

Poniższe przykładowe zapytanie używa `CreatePolygon` do utworzenia wielokąta z trzech punktów. Pierwsze dwa punkty są tworzone ręcznie, a ostatni punkt jest tworzony na podstawie danych wejściowych.

```SQL 
SELECT  
     CreatePolygon(CreatePoint(input.latitude, input.longitude), CreatePoint(10.0, 10.0), CreatePoint(10.5, 10.5), CreatePoint(input.latitude, input.longitude))  
FROM input  
```  

### <a name="input-example"></a>Przykład danych wejściowych  
  
|rodziny|Długość geograficzna|  
|--------------|---------------|  
|3.0|-10,2|  
|-87,33|20,2321|  
  
### <a name="output-example"></a>Przykład danych wyjściowych  

 {"Type": "Wielokąt", "współrzędne": [[[-10,2, 3,0], [10,0, 10,0], [10,5, 10,5], [-10,2, 3,0]]]}
 
 {"Type": "Wielokąt", "współrzędne": [[[20,2321,-87,33], [10,0, 10,0], [10,5, 10,5], [20,2321,-87,33]]]}

Aby dowiedzieć się więcej, odwiedź odwołanie do [wielokąta](https://docs.microsoft.com/stream-analytics-query/createpolygon) .


## <a name="st_distance"></a>ST_DISTANCE
Funkcja `ST_DISTANCE` zwraca odległość między dwoma punktami w licznikach. 

Poniższe zapytanie używa `ST_DISTANCE` do wygenerowania zdarzenia, gdy stacja gazowa jest mniejsza niż 10 km od samochodu.

```SQL
SELECT Cars.Location, Station.Location 
FROM Cars c  
JOIN Station s ON ST_DISTANCE(c.Location, s.Location) < 10 * 1000
```

Aby dowiedzieć się więcej, odwiedź odwołanie [ST_DISTANCE](https://docs.microsoft.com/stream-analytics-query/st-distance) .

## <a name="st_overlaps"></a>ST_OVERLAPS
Funkcja `ST_OVERLAPS` porównuje dwa wielokąty. Jeśli wielokąty nakładają się, funkcja zwraca 1. Funkcja zwraca wartość 0, jeśli wielokąty nie nakładają się. 

Poniższe zapytanie używa `ST_OVERLAPS` do wygenerowania zdarzenia, gdy kompilacja znajduje się w obrębie możliwej strefy zalania.

```SQL
SELECT Building.Polygon, Building.Polygon 
FROM Building b 
JOIN Flooding f ON ST_OVERLAPS(b.Polygon, b.Polygon) 
```

Poniższe przykładowe zapytanie generuje zdarzenie, gdy burza jest skierowana do samochodu.

```SQL
SELECT Cars.Location, Storm.Course
FROM Cars c, Storm s
JOIN Storm s ON ST_OVERLAPS(c.Location, s.Course)
```

Aby dowiedzieć się więcej, odwiedź odwołanie [ST_OVERLAPS](https://docs.microsoft.com/stream-analytics-query/st-overlaps) .

## <a name="st_intersects"></a>ST_INTERSECTS
Funkcja `ST_INTERSECTS` porównuje dwie LineString. Jeśli LineString Intersect, funkcja zwraca 1. Funkcja zwraca wartość 0, jeśli LineString nie przecina.

Poniższe przykładowe zapytanie używa `ST_INTERSECTS`, aby określić, czy droga ukryte przecina drogę.

```SQL 
SELECT  
     ST_INTERSECTS(input.pavedRoad, input.dirtRoad)  
FROM input  
```  

### <a name="input-example"></a>Przykład danych wejściowych  
  
|datacenterArea|stormArea|  
|--------------------|---------------|  
|{"Type": "LineString", "współrzędne": [[-10,0, 0,0], [0,0, 0,0], [10,0, 0,0]]}|{"Type": "LineString", "współrzędne": [[0,0, 10,0], [0,0, 0,0], [0,0,-10,0]]}|  
|{"Type": "LineString", "współrzędne": [[-10,0, 0,0], [0,0, 0,0], [10,0, 0,0]]}|{"Type": "LineString", "współrzędne": [[-10,0, 10,0], [0,0, 10,0], [10,0, 10,0]]}|  
  
### <a name="output-example"></a>Przykład danych wyjściowych  

 1  
  
 0  

Aby dowiedzieć się więcej, odwiedź odwołanie [ST_INTERSECTS](https://docs.microsoft.com/stream-analytics-query/st-intersects) .

## <a name="st_within"></a>ST_WITHIN
Funkcja `ST_WITHIN` określa, czy punkt lub Wielokąt znajduje się w obrębie wielokąta. Jeśli Wielokąt zawiera punkt lub Wielokąt, funkcja zwróci wartość 1. Funkcja zwróci wartość 0, jeśli punkt lub Wielokąt nie znajdują się w obrębie zadeklarowanego wielokąta.

Poniższe przykładowe zapytanie używa `ST_WITHIN`, aby określić, czy punkt docelowy dostawy znajduje się w obrębie danego wielokąta magazynu.

```SQL 
SELECT  
     ST_WITHIN(input.deliveryDestination, input.warehouse)  
FROM input 
```  

### <a name="input-example"></a>Przykład danych wejściowych  
  
|deliveryDestination|hurtowni|  
|-------------------------|---------------|  
|{"Type": "Point", "współrzędne": [76,6, 10,1]}|{"Type": "Wielokąt", "współrzędne": [[0,0, 0,0], [10,0, 0,0], [10,0, 10,0], [0,0, 10,0], [0,0, 0,0]]}|  
|{"Type": "Point", "współrzędne": [15,0, 15,0]}|{"Type": "Wielokąt", "współrzędne": [[10,0, 10,0], [20,0, 10,0], [20,0, 20,0], [10,0, 20,0], [10,0, 10,0]]}|  
  
### <a name="output-example"></a>Przykład danych wyjściowych  

 0  
  
 1  

Aby dowiedzieć się więcej, odwiedź odwołanie [ST_WITHIN](https://docs.microsoft.com/stream-analytics-query/st-within) .

## <a name="next-steps"></a>Następne kroki

* [Wprowadzenie do Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)