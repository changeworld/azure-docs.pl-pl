---
title: Scenariusze agregacji Geofencing i dane geograficzne za pomocą usługi Azure Stream Analytics
description: W tym artykule opisano sposób użycia usługi Azure Stream Analytics dla agregacji geofencing i dane geograficzne.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: cc301855e4cdcb8eb687e753835577399cfe72b0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60789567"
---
# <a name="geofencing-and-geospatial-aggregation-scenarios-with-azure-stream-analytics"></a>Scenariusze agregacji Geofencing i dane geograficzne za pomocą usługi Azure Stream Analytics

Za pomocą wbudowanych funkcji geoprzestrzennych można użyć usługi Azure Stream Analytics, tworzenie aplikacji na potrzeby scenariuszy, takich jak zarządzanie flotą, zastąpienie, udostępnianie podłączone samochody i śledzenie zasobów.

## <a name="geofencing"></a>Wirtualne grodzenie

Usługa Azure Stream Analytics obsługuje małymi opóźnieniami wirtualnego grodzenia w czasie rzeczywistym obliczeń w chmurze i środowisku uruchomieniowym usługi IoT Edge.

### <a name="geofencing-scenario"></a>Geofencing scenariusza

Firmy produkcyjnej związanych musi śledzić zasoby na ich budynki. One wyposażone każdego urządzenia za pomocą systemu GPS i otrzymywać powiadomienia, jeśli urządzenie opuści określonym obszarze.

Dane referencyjne używane w tym przykładzie ma informacje o wirtualnym ogrodzeniu budynki i urządzeń, które są dozwolone w każdym budynków. Należy pamiętać, że danych referencyjnych mogące być statyczne lub powolne zmiana. Statyczne dane referencyjne jest używany w tym scenariuszu. Strumień danych emituje stale identyfikator urządzenia i jego bieżącej pozycji.

### <a name="define-geofences-in-reference-data"></a>Zdefiniuj wirtualne ogrodzenia w danych referencyjnych

W wirtualnym ogrodzeniu można zdefiniować przy użyciu obiektu GeoJSON. Dla zadań z wersji 1.2 lub nowszej, wirtualne ogrodzenia można także definiować przy użyciu dobrze znanych tekstu (WELL-KNOWN) jako `NVARCHAR(MAX)`. WELL-KNOWN jest Otwórz dane geograficzne Consortium (OGC) standard, która jest używana do reprezentowania danych przestrzennych w formacie tekstowym.

Wbudowanych funkcji geoprzestrzennych można użyć zdefiniowanych wirtualne ogrodzenia, aby dowiedzieć się, jeśli element jest w lub poza nią wielokąta określonego wirtualnego ogrodzenia.

Poniższa tabela znajduje się przykład danych referencyjnych wirtualnego ogrodzenia, który może być przechowywany w usłudze Azure blob storage lub tabeli usługi Azure SQL. Każda witryna jest reprezentowany przez wielokąta dane geograficzne i każdego urządzenia jest skojarzony z identyfikatora dozwoloną witrynę

|SiteID|SiteName|Geofencing|AllowedDeviceID|
|------|--------|--------|---------------|
|1|"Redmond Building 41"|"WIELOKĄTA ((-122.1337357922017 47.63782998329432,-122.13373042778369 47.637634793257305,-122.13346757130023 47.637642022530954,-122.13348902897235 47.637508280806806,-122.13361777500506 47.637508280806806,-122.13361241058703 47.63732393354484,-122.13265754417773 47.63730947490855,-122.13266290859576 47.637519124743164,-122.13302232460376 47.637515510097955,-122.13301696018573 47.63764925180358,-122.13272728161212 47.63764925180358,-122.13274873928424 47.63784082716388,-122.13373579220172 47.63782998329432)) "|"B"|
|2|"Redmond Building 40"|"WIELOKĄTA ((-122.1336154507967 47.6366745947009,-122.13361008637867 47.636483015064535,-122.13349206918201 47.636479400347675,-122.13349743360004 47.63636372927573,-122.13372810357532 47.63636372927573,-122.13373346799335 47.63617576323771,-122.13263912671528 47.63616491902258,-122.13264985555134 47.63635649982525,-122.13304682248554 47.636367344000604,-122.13305218690357 47.63650831807564,-122.13276250832996 47.636497473929516,-122.13277323716602 47.63668543881025,-122.1336154507967 47.6366745947009)) "|"A"|
|3|"Redmond Building 22"|"WIELOKĄTA ((-122.13611660248233 47.63758544698554,-122.13635263687564 47.6374083293018,-122.13622389084293 47.63733603619712,-122.13622389084293 47.63717699101473,-122.13581619507266 47.63692757827657,-122.13559625393344 47.637046862778135,-122.13569281345798 47.637144458985965,-122.13570890671207 47.637314348246214,-122.13611660248233 47.63758544698554)) "|"C"|

### <a name="generate-alerts-with-geofence"></a>Generowanie alarmów przy użyciu wirtualnego ogrodzenia

Urządzenia może emitować ich nazwy i lokalizacji co minutę przez strumień o nazwie `DeviceStreamInput`. Poniższa tabela dotyczy strumienia danych wejściowych.

|Identyfikator urządzenia|GeoPosition|
|--------|-----------|
|"A"|"POINT(-122.13292341559497 47.636318374032726)"|
|"B"|"POINT(-122.13338475554553 47.63743531308874)"|
|"C"|"POINT(-122.13354001095752 47.63627622505007)"|

Można napisać zapytanie, które dołącza strumień urządzeń z danymi referencyjnymi w wirtualnym ogrodzeniu i generuje alert za każdym razem, gdy urządzenie znajduje się poza dozwolone tworzenie.

```SQL
SELECT DeviceStreamInput.DeviceID, SiteReferenceInput.SiteID, SiteReferenceInput.SiteName 
INTO Output
FROM DeviceStreamInput 
JOIN SiteReferenceInput
ON st_within(DeviceStreamInput.GeoPosition, SiteReferenceInput.Geofence) = 0
WHERE DeviceStreamInput.DeviceID = SiteReferenceInput.AllowedDeviceID
```

Poniższa ilustracja przedstawia wirtualne ogrodzenia. Aby zobaczyć, których urządzenia zgodnie z przesyłanie strumieniowe danych wejściowych.

![Tworzenie wirtualne ogrodzenia](./media/geospatial-scenarios/building-geofences.png)

Urządzenie "C" znajduje się w budynku 2 identyfikator, który nie jest dozwolone zgodnie z danych referencyjnych. To urządzenie powinien znajdować się wewnątrz tworzenia Identyfikatorem 3. Uruchomienie tego zadania będzie generować alert dla tego określonego naruszenia.

### <a name="site-with-multiple-allowed-devices"></a>Lokacji z wieloma urządzeniami dozwolone

Jeśli lokacji pozwala na wielu urządzeniach, tablicę identyfikatorów urządzeń można zdefiniować w `AllowedDeviceID` i funkcja zdefiniowane przez użytkownika mogą być używane na `WHERE` klauzulę, aby sprawdzić, czy identyfikator urządzenia usługi stream odpowiada identyfikator dowolnego urządzenia na tej liście. Aby uzyskać więcej informacji, Wyświetl [UDF języka Javascript](stream-analytics-javascript-user-defined-functions.md) samouczek dotyczący zadania w chmurze i [ C# UDF](stream-analytics-edge-csharp-udf.md) samouczek dotyczący zadań edge.

## <a name="geospatial-aggregation"></a>Agregacja danych Geoprzestrzennych

Usługa Azure Stream Analytics obsługuje agregacji w czasie rzeczywistym dane geograficzne małe opóźnienia w chmurze i środowisku uruchomieniowym usługi IoT Edge.

### <a name="geospatial-aggregation-scenario"></a>Scenariusz agregacji danych Geoprzestrzennych

Firma cab potrzebuje do tworzenia aplikacji w czasie rzeczywistym przeprowadzenie ich sterowniki cab szukasz jazdy kierunku obszary miast występują większe zapotrzebowanie.

Firmy przechowuje logiczne regionów miasta w postaci danych referencyjnych. Każdy region jest definiowany przez RegionID, RegionName i wirtualnym ogrodzeniu.

### <a name="define-the-geofences"></a>Zdefiniuj wirtualne ogrodzenia

Poniższa tabela znajduje się przykład danych referencyjnych wirtualnego ogrodzenia, który może być przechowywany w usłudze Azure blob storage lub tabeli usługi Azure SQL. Każdy region jest reprezentowany przez wielokąta geoprzestrzenne, który jest używany do skorelować z żądaniami pochodzące z danych przesyłanych strumieniowo.

Te wielokątów są tylko dla celów informacyjnych i nie reprezentuje rzeczywisty Miasto odejścia logiczny lub fizyczny.

|RegionID|RegionName|Geofencing|
|--------|----------|--------|
|1|"SoHo"|"WIELOKĄTA ((-74.00279525078275 40.72833625216264,-74.00547745979765 40.721929158663244,-74.00125029839018 40.71893680218994,-73.9957785919998 40.72521409075776,-73.9972377137039 40.72557184584898,-74.00279525078275 40.72833625216264) )"|
|2|"Chinatown"|"WIELOKĄTA ((-73.99712367114876 40.71281582267133,-73.9901070123658 40.71336881907936,-73.99023575839851 40.71452359088633,-73.98976368961189 40.71554823078944,-73.99551434573982 40.717337246783735,-73.99480624255989 40.718491949759304,-73.99652285632942 40.719109951574,-73.99776740131233 40.7168005470334,-73.99903340396736 40.71727219249899,-74.00193018970344 40.71938642421256,-74.00409741458748 40.71688186545551,-74.00051398334358 40.71517415773184,-74.0004281526551 40.714377212470005,-73.99849696216438 40.713450141693166,-73.99748845157478 40.71405192594819,-73.99712367114876 40.71281582267133)) "|
|3|"Tribeca"|"WIELOKĄTA ((-74.01091641815208 40.72583120006787,-74.01338405044578 40.71436586362705,-74.01370591552757 40.713617702123415,-74.00862044723533 40.711308107057235,-74.00194711120628 40.7194238654018,-74.01091641815208 40.72583120006787))"|

### <a name="aggregate-data-over-a-window-of-time"></a>Zagregowane dane w przedziale czasu

Poniższa tabela zawiera dane przesyłane strumieniowo z "było."

|Identyfikator użytkownika|FromLocation|ToLocation|TripRequestedTime|
|------|------------|----------|-----------------|
|"A"|"POINT(-74.00726861389182 40.71610611981975)"|"POINT(-73.98615095917779 40.703107386025835)"|"2019-03-12T07:00:00Z"|
|"B"|"POINT(-74.00249841021645 40.723827238895666)"|"POINT(-74.01160699942085 40.71378884930115)"|"2019-03-12T07:01:00Z"|
|"C"|"POINT(-73.99680120565864 40.716439898624024)"|"POINT(-73.98289663412544 40.72582343969828)"|"2019-03-12T07:02:00Z"|
|"D"|"POINT(-74.00741090068288 40.71615626755086)"|"POINT(-73.97999843120539 40.73477895807408)"|"2019-03-12T07:03:00Z"|

Następujące zapytanie sprzężenia strumień urządzeń z danymi referencyjnymi w wirtualnym ogrodzeniu i oblicza liczbę żądań na region na przedział czasu, 15 minut na minutę.

```SQL
SELECT count(*) as NumberOfRequests, RegionsRefDataInput.RegionName 
FROM UserRequestStreamDataInput
JOIN RegionsRefDataInput 
ON st_within(UserRequestStreamDataInput.FromLocation, RegionsRefDataInput.Geofence) = 1
GROUP BY RegionsRefDataInput.RegionName, hoppingwindow(minute, 1, 15)
```

To zapytanie Wyświetla liczbę żądań na minutę dla ostatnich 15 minut przez każdego regionu, w ramach miasta. Te informacje mogą być wyświetlane w łatwy sposób przez pulpit nawigacyjny usługi Power BI lub może być wysłano do wszystkich sterowników jako wiadomości SMS za pośrednictwem integracji z usługami, takimi jak usługa Azure functions.

Na poniższej ilustracji przedstawiono wyniki kwerendy do pulpitu nawigacyjnego usługi Power BI. 

![Wynik wyjściowy na pulpicie nawigacyjnym usługi Power BI](./media/geospatial-scenarios/power-bi-output.png)


## <a name="next-steps"></a>Kolejne kroki

* [Wprowadzenie do funkcji geoprzestrzennych usługi Stream Analytics](stream-analytics-geospatial-functions.md)
* [Funkcje Geoprzestrzenne (usługa Azure Stream Analytics)](https://docs.microsoft.com/stream-analytics-query/geospatial-functions)
