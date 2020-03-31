---
title: Geofencing i agregacja geoprzestrzenna dzięki usłudze Azure Stream Analytics
description: W tym artykule opisano sposób używania usługi Azure Stream Analytics do geofencingu i agregacji geoprzestrzennej.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: 5a3aa3786469c3df37b53cb82bdd396871689297
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443639"
---
# <a name="geofencing-and-geospatial-aggregation-scenarios-with-azure-stream-analytics"></a>Scenariusze geofencingu i agregacji geoprzestrzennej za pomocą usługi Azure Stream Analytics

Dzięki wbudowanym funkcjom geoprzestrzennym możesz używać usługi Azure Stream Analytics do tworzenia aplikacji dla scenariuszy, takich jak zarządzanie flotą, udostępnianie przejazdów, połączone samochody i śledzenie zasobów.

## <a name="geofencing"></a>Geofencing

Usługa Azure Stream Analytics obsługuje obliczenia geofencingowe w czasie rzeczywistym o małym opóźnieniu w chmurze i w czasie wykonywania usługi IoT Edge.

### <a name="geofencing-scenario"></a>Scenariusz geofencingu

Firma produkcyjna musi śledzić aktywa na swoich budynkach. Wyposażyli każde urządzenie w GPS i chcą otrzymywać powiadomienia, jeśli urządzenie opuści określony obszar.

Dane referencyjne użyte w tym przykładzie mają informacje geofence dla budynków i urządzeń, które są dozwolone w każdym z budynków. Należy pamiętać, że dane referencyjne mogą być statyczne lub powolne zmiany. Statyczne dane referencyjne są używane w tym scenariuszu. Strumień danych w sposób ciągły emituje identyfikator urządzenia i jego bieżącą pozycję.

### <a name="define-geofences-in-reference-data"></a>Definiowanie geofences w danych referencyjnych

Geofence można zdefiniować za pomocą obiektu GeoJSON. W przypadku zadań o zgodności w wersji 1.2 lub nowszej można również zdefiniować `NVARCHAR(MAX)`geofences przy użyciu dobrze znanego tekstu (WKT) jako . WKT jest standardem Open Geospatial Consortium (OGC), który jest używany do reprezentowania danych przestrzennych w formacie tekstowym.

Wbudowane funkcje geoprzestrzenne mogą używać zdefiniowanych geofences, aby dowiedzieć się, czy element znajduje się w określonym wielokącie geofence lub poza nią.

Poniższa tabela jest przykładem danych referencyjnych geofence, które mogą być przechowywane w magazynie obiektów blob platformy Azure lub tabeli SQL platformy Azure. Każda lokacja jest reprezentowana przez wielokąt geoprzestrzenne, a każde urządzenie jest skojarzone z dozwolonym identyfikatorem witryny.

|SiteID (SiteID)|Nazwa witryny|Geofence|AllowedDeviceID|
|------|--------|--------|---------------|
|1|"Budynek Redmond 41"|"POLYGON(-122.1337357922017 47.63782998329432,-122.13373042778369 47.637634793257305,-122.13346757130023 47.637642022530954,-122.13348902897235 47.637508280806806,-122.13361777500506 47.637508280806806,-122.13361241058703 47.63732393354484,-122.13265754417773 47.63730947490855,-122.13266290859576 47.637519124743164,-122.13302232460376 47.637515510097955,-122.13301696018573 47.63764925180358,-122.13272728161212 47.63764925180358,-122.13274873928424 47.63784082716388,-122.13373579220172 47.63782998329432))"|"B"|
|2|"Budynek Redmond 40"|"POLYGON(-122.1336154507967 47.6366745947009,-122.13361008637867 47.636483015064535,-122.13349206918201 47.636479400347675,-122.13349743360004 47.63636372927573,-122.13372810357532 47.63636372927573,-122.13373346799335 47.63617576323771,-122.13263912671528 47.63616491902258,-122.13264985555134 47.63635649982525,-122.13304682248554 47.636367344000604,-122.13305218690357 47.63650831807564,-122.13276250832996 47.636497473929516,-122.13277323716602 47.63668543881025,-122.1336154507967 47.6366745947009))"|"A"|
|3|"Budynek Redmond 22"|"POLYGON(-122.13611660248233 47.63758544698554,-122.13635263687564 47.6374083293018,-122.13622389084293 47.63733603619712,-122.13622389084293 47.63717699101473,-122.13581619507266 47.63692757827657,-122.13559625393344 47.637046862778135,-122.13569281345798 47.637144458985965,-122.13570890671207 47.637314348246214,-122.13611660248233 47.63758544698554))"|"C"|

### <a name="generate-alerts-with-geofence"></a>Generowanie alertów z geofence

Urządzenia mogą emitować swój identyfikator i lokalizację `DeviceStreamInput`co minutę przez strumień o nazwie . Poniższa tabela jest strumień danych wejściowych.

|Deviceid|Położenie geograficzne|
|--------|-----------|
|"A"|"POINT(-122.13292341559497 47.636318374032726)"|
|"B"|"POINT(-122.13338475555553 47.63743531308874)"|
|"C"|"POINT(-122.13354001095752 47.63627622505007)"|

Można napisać kwerendę, która łączy strumień urządzenia z danymi referencyjnymi geofence i generuje alert za każdym razem, gdy urządzenie znajduje się poza dozwolonym budynkiem.

```SQL
SELECT DeviceStreamInput.DeviceID, SiteReferenceInput.SiteID, SiteReferenceInput.SiteName 
INTO Output
FROM DeviceStreamInput 
JOIN SiteReferenceInput
ON st_within(DeviceStreamInput.GeoPosition, SiteReferenceInput.Geofence) = 0
WHERE DeviceStreamInput.DeviceID = SiteReferenceInput.AllowedDeviceID
```

Poniższy obraz przedstawia geofences. Możesz zobaczyć, gdzie urządzenia są zgodne z wejściem danych strumienia.

![Budowanie geofences](./media/geospatial-scenarios/building-geofences.png)

Urządzenie "C" znajduje się wewnątrz budynku ID 2, co nie jest dozwolone zgodnie z danymi referencyjnym. To urządzenie powinno znajdować się wewnątrz budynku ID 3. Uruchomienie tego zadania spowoduje wygenerowanie alertu dla tego konkretnego naruszenia.

### <a name="site-with-multiple-allowed-devices"></a>Witryna z wieloma dozwolonymi urządzeniami

Jeśli witryna zezwala na wiele urządzeń, można zdefiniować `AllowedDeviceID` tablicę identyfikatorów urządzeń i `WHERE` funkcja zdefiniowana przez użytkownika może służyć w klauzuli, aby sprawdzić, czy identyfikator urządzenia strumienia jest zgodny z dowolnym identyfikatorem urządzenia na tej liście. Aby uzyskać więcej informacji, zobacz samouczek [Javascript UDF](stream-analytics-javascript-user-defined-functions.md) dla zadań w chmurze i samouczek [UDF języka C#](stream-analytics-edge-csharp-udf.md) dla zadań brzegowych.

## <a name="geospatial-aggregation"></a>Agregacja geoprzestrzenne

Usługa Azure Stream Analytics obsługuje agregację geoprzestrzenną w czasie rzeczywistym o małym opóźnieniu w chmurze i w czasie wykonywania usługi IoT Edge.

### <a name="geospatial-aggregation-scenario"></a>Scenariusz agregacji geoprzestrzennej

Firma taksówkarska chce stworzyć aplikację w czasie rzeczywistym, która poprowadzi swoich taksówkarzy poszukujących jazdy w kierunku obszarów miast, w których obecnie występuje większy popyt.

Firma przechowuje logiczne regiony miasta jako dane referencyjne. Każdy region jest definiowany przez RegionID, RegionName i Geofence.

### <a name="define-the-geofences"></a>Definiowanie geofences

Poniższa tabela jest przykładem danych referencyjnych geofence, które mogą być przechowywane w magazynie obiektów blob platformy Azure lub tabeli SQL platformy Azure. Każdy region jest reprezentowany przez wielokąt geoprzestrzenne, który jest używany do korelowania z żądaniami pochodzącymi z danych strumieniowych.

Te wielokąty są tylko w celach informacyjnych i nie reprezentują rzeczywistych separacji logicznych lub fizycznych miasta.

|RegionID|Regionname|Geofence|
|--------|----------|--------|
|1|"SoHo"|"POLYGON(-74.00279525078275 40.72833625216264,-74.00547745979765 40.721929158663244,-74.00125029839018 40.71893680218994,-73.9957785919998 40.72521409075776,-73.9972377137039 40.72557184584898,-74.00279525078275 40.72833625216264) )"|
|2|"Chinatown"|"POLYGON(-73.99712367114876 40.71281582267133,-73.9901070123658 40.71336881907936,-73.99023575839851 40.71452359088633,-73.98976368961189 40.71554823078944,-73.799551434573982 40.717337246783735,-73.99480624255989 40.718491949759304,-73.99652285632942 40.719109951574,-73.99776740131233 40.7168005470334,-73.99903340396736 40.71727219249899,-74.00193018970344 40.71938642421256,-74.00409741458748 40.71688186545551,-74.00051398334358 40.71517415773184,-74.0004281526551 40.714377212470005,-73.99849696216438 40.713450141693166,-73.99748845157478 40.71405192594819,-73.99712367114876 40.71281582267133))"|
|3|"Tribeca"|"POLYGON((-74.01091641815208 40.72583120006787,-74.01338405044578 40.71436586362705,-74.01370591552757 40.713617702123415,-74.00862044723533 40.711308107057235,-74.00194711120628 40.7194238654018,-74.01091641815208 40.72583120006787))"|

### <a name="aggregate-data-over-a-window-of-time"></a>Agregowanie danych w danym oknie czasu

Poniższa tabela zawiera dane przesyłania strumieniowego "przejazdy".

|UserID|OdLokacja|ToLokacja|Czas podróży|
|------|------------|----------|-----------------|
|"A"|"POINT(-74.00726861389182 40.71610611981975)"|"POINT(-73.98615095917779 40.703107386025835)"|"2019-03-12T07:00:00Z"|
|"B"|"POINT(-74.00249841021645 40.723827238895666)"|"POINT(-74.01160699942085 40.71378884930115)"|"2019-03-12T07:01:00Z"|
|"C"|"POINT(-73.99680120565864 40.716439898624024)"|"POINT(-73.98289663412544 40.72582343969828)"|"2019-03-12T07:02:00Z"|
|„D”|"POINT(-74.00741090068288 40.71615626755086)"|"POINT(-73.97999843120539 40.73477895807408)"|"2019-03-12T07:03:00Z"|

Następująca kwerenda łączy strumień urządzenia z danymi referencyjnymi geofence i oblicza liczbę żądań na region w oknie czasu 15 minut co minutę.

```SQL
SELECT count(*) as NumberOfRequests, RegionsRefDataInput.RegionName 
FROM UserRequestStreamDataInput
JOIN RegionsRefDataInput 
ON st_within(UserRequestStreamDataInput.FromLocation, RegionsRefDataInput.Geofence) = 1
GROUP BY RegionsRefDataInput.RegionName, hoppingwindow(minute, 1, 15)
```

Ta kwerenda wyprowadza liczbę żądań co minutę przez ostatnie 15 minut przez każdy region w mieście. Te informacje mogą być łatwo wyświetlane przez pulpit nawigacyjny usługi Power BI lub mogą być transmitowane do wszystkich sterowników jako wiadomości tekstowe SMS za pośrednictwem integracji z usługami, takimi jak funkcje platformy Azure.

Poniższa ilustracja przedstawia dane wyjściowe kwerendy na pulpicie nawigacyjnym usługi Power BI. 

![Dane wyjściowe wyników na pulpicie nawigacyjnym usługi Power BI](./media/geospatial-scenarios/power-bi-output.png)


## <a name="next-steps"></a>Następne kroki

* [Wprowadzenie do funkcji geoprzestrzennych usługi Stream Analytics](stream-analytics-geospatial-functions.md)
* [Funkcje geoprzestrzenne (usługa Azure Stream Analytics)](https://docs.microsoft.com/stream-analytics-query/geospatial-functions)
