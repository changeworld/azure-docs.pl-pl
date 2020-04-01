---
title: 'Samouczek: Migracja aplikacji internetowej z Map Google | Mapy platformy Microsoft Azure'
description: Jak przeprowadzić migrację aplikacji internetowej z Map Google do Map Platformy Microsoft Azure.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: bdbf2a975cbdc3d06745b9375c1e6f8e751ddfd6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77914096"
---
# <a name="migrate-a-web-app-from-google-maps"></a>Migrowanie aplikacji internetowej z Map Google

Większość aplikacji internetowych korzystających z Map Google korzysta z SDK JavaScript Google Maps V3. Zestaw SDK usługi Azure Maps Web jest odpowiednim zestawem SDK opartym na platformie Azure do migracji. Zestaw Azure Maps Web SDK umożliwia dostosowywanie interaktywnych map za pomocą własnej zawartości i obrazów. Aplikację można uruchamiać zarówno w aplikacjach internetowych, jak i mobilnych. Kontrolka korzysta z technologii WebGL, co umożliwia renderowanie dużych zestawów danych z wysoką wydajnością. Opracuj za pomocą tego sdk przy użyciu języka JavaScript lub TypeScript.

Podczas migracji istniejącej aplikacji sieci web sprawdź, czy używa biblioteki kontroli map typu open source. Przykładami biblioteki kontroli map open source są: Cesium, Ulotka i OpenLayers. Nadal można migrować aplikację, nawet jeśli używa biblioteki kontroli map typu open source i nie chcesz używać zestawu SDK sieci Web usługi Azure Maps. W takim przypadku należy podłączyć aplikację do usług kafelków usługi Usługi Azure Maps \| [(kafelki satelitarne](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile)[kafelków drogowych).](https://docs.microsoft.com/rest/api/maps/render/getmaptile) Następujące punkty szczegółowo dotyczące korzystania z usługi Azure Maps w niektórych często używanych bibliotekach kontroli map typu open source.

- Cez - kontrolka mapy 3D dla sieci. [Przykładowa](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20Cesium%20JS) \| [dokumentacja](https://cesiumjs.org/) kodu
- Ulotka – Lekka kontrola mapy 2D w internecie. [Przykładowa](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Azure%20Maps%20Raster%20Tiles%20in%20Leaflet%20JS) \| [dokumentacja](https://leafletjs.com/) kodu
- OpenLayers - kontrolka mapy 2D dla sieci Web, która obsługuje projekcje. [Przykładowa](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20OpenLayers) \| [dokumentacja](https://openlayers.org/) kodu

## <a name="key-features-support"></a>Obsługa kluczowych funkcji

Tabela zawiera listę kluczowych funkcji interfejsu API w zestawie SDK JavaScript Google Maps V3 i funkcji obsługiwanego interfejsu API w zestawie Azure Maps Web SDK.

| Funkcja Mapy Google     | Obsługa zestawów SDK usługi Azure Maps Web |
|-------------------------|:--------------------------:|
| Znaczniki                 | ✓                          |
| Klastrowanie znaczników       | ✓                          |
| Polilinie & wielokąty    | ✓                          |
| Warstwy danych             | ✓                          |
| Nakładki naziemie         | ✓                          |
| Mapy ciepła               | ✓                          |
| Warstwy kafelków             | ✓                          |
| Warstwa KML               | ✓                          |
| Narzędzia do rysowania           | ✓                          |
| Usługa Geocoder        | ✓                          |
| Usługa wskazówki dojazdu      | ✓                          |
| Usługa Macierzy odległości | ✓                          |
| Usługa podniesienia poziomu       | Planowany                    |

## <a name="notable-differences-in-the-web-sdks"></a>Znaczące różnice w internetowych SDK

Oto kilka kluczowych różnic między zestawami SDK Google Maps i Azure Maps Web, o których należy pamiętać:

- Oprócz zapewnienia hostowanego punktu końcowego dostępu do zestawu Azure Maps Web SDK, dostępny jest pakiet NPM. Osadź pakiet zestawu SDK sieci Web w aplikacjach. Aby uzyskać więcej informacji, zobacz tę [dokumentację](how-to-use-map-control.md). Ten pakiet zawiera również definicje TypeScript.
- Najpierw należy utworzyć wystąpienie klasy Map w usłudze Azure Maps. Poczekaj, aż `ready` `load` mapy lub zdarzenie będą uruchamiane, zanim programowo wejdzie w interakcję z mapą. To zamówienie zapewni, że wszystkie zasoby mapy zostały załadowane i będą gotowe do uzyskania dostępu.
- Obie platformy używają podobnego systemu kafli dla map podstawowych. Kafelki w Mapach Google mają wymiar 256 pikseli; jednak kafelki w usłudze Azure Maps mają wymiar 512 pikseli. Aby uzyskać ten sam widok mapy w Mapach Platformy Azure co Mapy Google, odejmij poziom powiększenia Map Google o numer jeden w usłudze Azure Maps.
- Współrzędne w Mapach Google są określane jako "szerokość geograficzna, długość geograficzna", podczas gdy usługa Azure Maps używa "długości geograficznej, szerokości geograficznej". Format usługi Azure Maps jest `[x, y]`wyrównany ze standardem , po którym następuje większość platform GIS.
- Kształty w zestawie Azure Maps Web SDK są oparte na schemacie GeoJSON. Klasy pomocnika są udostępniane za pośrednictwem obszaru nazw [ *atlas.data* ](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data?view=azure-iot-typescript-latest). Jest też [*atlas. Klasa kształtu.*](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape) Ta klasa służy do zawijania obiektów GeoJSON, aby ułatwić aktualizowanie i obsługa danych można powiązać sposób.
- Współrzędne w usłudze Azure Maps są definiowane jako obiekty położenia. Współrzędna jest określona `[longitude,latitude]`jako tablica liczbowa w formacie . Lub jest określony przy użyciu nowego atlas.data.Position(długość geograficzna, szerokość geograficzna).
    > [!TIP]
    > Position Klasa ma statyczną metodę pomocnika do importowania współrzędnych, które są w formacie "szerokość geograficzna, długość geograficzna". Metoda [atlas.data.Position.fromLatLng](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.position?view=azure-iot-typescript-latest) może być często `new google.maps.LatLng` zastąpiona metodą w kodzie Map Google.
- Zamiast określać informacje o stylu dla każdego kształtu, który jest dodawany do mapy, usługa Azure Maps oddziela style od danych. Dane są przechowywane w źródłach danych i są połączone z warstwami renderowania. Kod usługi Azure Maps używa źródeł danych do renderowania danych. Takie podejście zapewnia większą wydajność korzyści. Ponadto wiele warstw obsługuje styl oparty na danych, w którym logika biznesowa może być dodawana do opcji stylu warstwy. Ta obsługa zmienia sposób renderowania poszczególnych kształtów w warstwie na podstawie właściwości zdefiniowanych w kształcie.

## <a name="web-sdk-side-by-side-examples"></a>Przykłady sdk sieci Web obok siebie

Ta kolekcja ma przykłady kodu dla każdej platformy, a każdy przykład obejmuje typowy przypadek użycia. Jego celem jest ułatwienie migracji aplikacji sieci web z zestawu JavaScript Google Maps V3 do sdk azure maps web sdk. Przykłady kodu związane z aplikacjami sieci web są dostarczane w języku JavaScript. Jednak usługa Azure Maps udostępnia również definicje TypeScript jako dodatkową opcję za pośrednictwem [modułu NPM.](how-to-use-map-control.md)

### <a name="load-a-map"></a>Ładowanie mapy

Oba zestawy SDK mają te same kroki, aby załadować mapę:

- Dodaj odwołanie do SDK mapy.
- Dodaj `div` znacznik do treści strony, który będzie działał jako symbol zastępczy mapy.
- Utwórz funkcję JavaScript, która zostanie wywołana po załadowaniu strony.
- Utwórz wystąpienie odpowiedniej klasy mapy.

**Niektóre kluczowe różnice**

- Mapy Google wymagają podania klucza konta w odwołaniu do skryptu interfejsu API. Poświadczenia uwierzytelniania dla usługi Azure Maps są określone jako opcje klasy mapy. To poświadczenie może być kluczem subskrypcji lub informacjami usługi Azure Active Directory.
- Mapy Google akceptują funkcję wywołania zwrotnego w odwołaniu do skryptu interfejsu API, który jest używany do wywołania funkcji inicjowania w celu załadowania mapy. W usłudze Azure Maps należy użyć zdarzenia onload strony.
- Podczas odwoływania `div` się do elementu, w którym `Map` mapa będzie renderowana, klasa w usłudze Azure Maps wymaga tylko `id` wartości, gdy Mapy Google wymaga `HTMLElement` obiektu.
- Współrzędne w usłudze Azure Maps są definiowane jako obiekty `[longitude, latitude]`położenia, które można określić jako prostą tablicę liczbową w formacie .
- Poziom powiększenia w usłudze Azure Maps jest o jeden poziom niższy niż poziom powiększenia w Mapach Google. Ta rozbieżność wynika z różnicy w rozmiarach systemu kafli na obu platformach.
- Usługa Azure Maps nie dodaje żadnych kontrolek nawigacji do obszaru roboczego mapy. Tak więc, domyślnie mapa nie ma przycisków powiększenia i przycisków stylu mapy. Istnieją jednak opcje sterowania dodawania selektora stylu mapy, przycisków powiększenia, kompasu lub kontroli obrotu oraz kontroli skoku.
- Program obsługi zdarzeń jest dodawany `ready` w usłudze Azure Maps w celu monitorowania zdarzenia wystąpienia mapy. To zdarzenie zostanie podpalone po zakończeniu ładowania kontekstu WebGL i wszystkich potrzebnych zasobów. Dodaj dowolny kod, który chcesz uruchomić po zakończeniu ładowania mapy, do tego programu obsługi zdarzeń.

Poniższe podstawowe przykłady wykorzystują Mapy Google do załadowania mapy wyśrodkowanej nad Nowym Jorkiem ze współrzędnymi. Długość geograficzna: -73.985, szerokość geograficzna: 40.747, a mapa jest na poziomie powiększenia 12.

**Przed: Mapy Google**

Wyświetl mapę Google wyśrodkowaną i powiększoną nad lokalizacją.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(40.747, -73.985),
                zoom: 12
            });
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

Uruchomienie tego kodu w przeglądarce spowoduje wyświetlenie mapy wygląda następującej:

<center>

![Proste Mapy Google](media/migrate-google-maps-web-app/simple-google-map.png)</center>

**Po: Mapy platformy Azure**

Załaduj mapę z tym samym widokiem w usłudze Azure Maps wraz z przyciskami sterowania stylem mapy i powiększenia.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-73.985, 40.747],  //Format coordinates as longitude, latitude.
                zoom: 11,   //Subtract the zoom level by one.

                //Specify authentication information when loading the map.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {
                //Add zoom controls to bottom right of map.
                map.controls.add(new atlas.control.ZoomControl(), {
                    position: 'bottom-right'
                });

                //Add map style control in top left corner of map.
                map.controls.add(new atlas.control.StyleControl(), {
                    position: 'top-left'
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

Uruchomienie tego kodu w przeglądarce spowoduje wyświetlenie mapy wygląda następującej:

<center>

![Proste mapy platformy Azure](media/migrate-google-maps-web-app/simple-azure-maps.png)</center>

Znajdź szczegółową dokumentację dotyczącą konfigurowania i używania formantu mapy usługi Azure Maps w aplikacji internetowej, klikając [tutaj](how-to-use-map-control.md).

> [!NOTE]
> W przeciwieństwie do Map Google usługa Azure Maps nie wymaga początkowego środka i poziomu powiększenia, aby załadować mapę. Jeśli te informacje nie są dostarczane podczas ładowania mapy, mapy platformy Azure spróbuje określić miasto użytkownika. Wyśrodkowa i powiększy mapę.

**Dodatkowe zasoby:**

- Usługa Azure Maps udostępnia również kontrolki nawigacji do obracania i rozstawiania widoku mapy, zgodnie z tym, co udokumentowano [poniżej.](map-add-controls.md)

### <a name="localizing-the-map"></a>Lokalizowanie mapy

Jeśli twoi odbiorcy są rozmieszczeń w wielu krajach lub mówią różnymi językami, lokalizacja jest ważna.

**Przed: Mapy Google**

Aby zlokalizować Mapy Google, dodaj parametry języka i regionu.

```html
<script type="text/javascript" src=" https://maps.googleapis.com/maps/api/js?callback=initMap&key=[api_key]& language=[language_code]&region=[region_code]" async defer></script>
```

Oto przykład Map Google z językiem ustawionym na "fr-FR".

<center>

![Lokalizacja w Mapach Google](media/migrate-google-maps-web-app/google-maps-localization.png)</center>

**Po: Mapy platformy Azure**

Usługa Azure Maps udostępnia dwa różne sposoby ustawiania języka i widoku regionalnego mapy. Pierwszą opcją jest dodanie tych informacji do globalnej przestrzeni nazw *atlasu.* Spowoduje to, że wszystkie wystąpienia kontroli mapy w aplikacji domyślnie te ustawienia. Poniżej ustawia język na francuski (fr-FR) i widok regionalny na "auto":

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('auto');
```

Drugą opcją jest przekazanie tych informacji do opcji mapy podczas ładowania mapy. Jak to:

```javascript
map = new atlas.Map('myMap', {
    language: 'fr-FR',
    view: 'auto',

    authOptions: {
        authType: 'subscriptionKey',
        subscriptionKey: '<Your Azure Maps Key>'
    }
});
```

> [!NOTE]
> Za pomocą usługi Azure Maps można załadować wiele wystąpień mapy na tej samej stronie z różnymi ustawieniami języka i regionu. Możliwe jest również zaktualizowanie tych ustawień na mapie po załadowaniu. 

Znajdź szczegółową listę [obsługiwanych języków](supported-languages.md) w usłudze Azure Maps.

Oto przykład usługi Azure Maps z językiem ustawionym na "fr" i regionem użytkownika ustawionym na "fr-FR".

<center>

![Lokalizacja usługi Azure Maps](media/migrate-google-maps-web-app/azure-maps-localization.png)</center>

### <a name="setting-the-map-view"></a>Ustawianie widoku mapy

Mapy dynamiczne zarówno na platformie Azure, jak i w Mapach Google można programowo przenosić do nowych lokalizacji geograficznych. Aby to zrobić, należy wywołać odpowiednie funkcje w javascript. Przykłady pokazują, jak sprawić, by mapa wyświetlała zdjęcia lotnicze z satelity, wyśrodkować mapę nad lokalizacją i zmieniać poziom powiększenia na 15 w Mapach Google. Używane są następujące współrzędne lokalizacji: długość geograficzna: -111.0225 i szerokość geograficzna: 35.0272.

> [!NOTE]
> Mapy Google używają kafelków o wymiarach 256 pikseli, podczas gdy usługa Azure Maps używa większego kafelka o przekątnych 512 pikseli. W związku z tym usługa Azure Maps wymaga mniejszej liczby żądań sieciowych, aby załadować ten sam obszar mapy co Mapy Google. Ze względu na sposób działania stomatów kafelków w formantach mapy należy odjąć poziom powiększenia używany w Mapach Google przez numer jeden podczas korzystania z usługi Azure Maps. Ta operacja arytmetyczna zapewnia, że większe kafelki w usłudze Azure Maps renderują ten sam obszar mapy, co w Mapach Google,

**Przed: Mapy Google**

Przesuń kontrolka `setOptions` mapy Map Google za pomocą tej metody. Ta metoda umożliwia określenie środka mapy i poziomu powiększenia.

```javascript
map.setOptions({
    mapTypeId: google.maps.MapTypeId.SATELLITE,
    center: new google.maps.LatLng(35.0272, -111.0225),
    zoom: 15
});
```

<center>

![Widok zestawu Map Google](media/migrate-google-maps-web-app/google-maps-set-view.png)</center>

**Po: Mapy platformy Azure**

W usłudze Azure Maps zmień `setCamera` położenie mapy przy użyciu `setStyle` metody i zmień styl mapy przy użyciu metody. Współrzędne w usłudze Azure Maps są w formacie "długość geograficzna, szerokość geograficzna", a wartość poziomu powiększenia jest odejmowana przez jeden.

```javascript
map.setCamera({
    center: [-111.0225, 35.0272],
    zoom: 14
});

map.setStyle({
    style: 'satellite'
});
```

<center>

![Widok zestawu usługi Azure Maps](media/migrate-google-maps-web-app/azure-maps-set-view.jpeg)</center>

**Dodatkowe zasoby:**

- [Wybieranie stylu mapy](choose-map-style.md)
- [Obsługiwane style map](supported-map-styles.md)

### <a name="adding-a-marker"></a>Dodawanie znacznika

W usłudze Azure Maps istnieje wiele sposobów renderowania danych punktowych na mapie:

- **Znaczniki HTML** — renderuje punkty przy użyciu tradycyjnych elementów DOM. Znaczniki HTML obsługują przeciąganie.
- **Warstwa symboli** — renderuje punkty ikoną lub tekstem w kontekście WebGL.
- **Warstwa bąbelkowa** — renderuje punkty jako okręgi na mapie. Promienie okręgów mogą być skalowane na podstawie właściwości w danych.

Renderuj warstwy symboli i warstwy Bąbelków w kontekście WebGL. Obie warstwy mogą renderować duże zestawy punktów na mapie. Warstwy te wymagają przechowywania danych w źródle danych. Źródła danych i warstwy renderowania powinny zostać `ready` dodane do mapy po wygenerowaniu zdarzenia. Znaczniki HTML są renderowane jako elementy DOM na stronie i nie używają źródła danych. Im więcej elementów DOM ma strona, tym wolniej staje się strona. Jeśli renderowanie więcej niż kilkaset punktów na mapie, zaleca się użycie jednej z warstw renderowania zamiast.

Dodajmy znacznik do mapy z numerem 10 nałożona jako etykieta. Użyj długości geograficznej: -0,2 i szerokości geograficznej: 51,5.

**Przed: Mapy Google**

Dzięki Mapom Google dodaj znaczniki `google.maps.Marker` do mapy, korzystając z klasy i określ mapę jako jedną z opcji.

```javascript
//Create a marker and add it to the map.
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(51.5, -0.2),
    label: '10',
    map: map
});
```

<center>

![Znacznik Map Google](media/migrate-google-maps-web-app/google-maps-marker.png)</center>

**Po: Usługa Azure Maps przy użyciu znaczników HTML**

W usłudze Azure Maps użyj znaczników HTML, aby wyświetlić punkt na mapie. Znaczniki HTML są zalecane dla aplikacji, które muszą wyświetlać tylko niewielką liczbę punktów na mapie. Aby użyć znacznika HTML, utwórz wystąpienie `atlas.HtmlMarker` klasy. Ustaw opcje tekstu i położenia oraz dodaj `map.markers.add` znacznik do mapy za pomocą metody.

```javascript
//Create a HTML marker and add it to the map.
map.markers.add(new atlas.HtmlMarker({
    text: '10',
    position: [-0.2, 51.5]
}));
```

<center>

![Znacznik HTML usługi Azure Maps](media/migrate-google-maps-web-app/azure-maps-html-marker.png)</center>

**Po: Usługa Azure Maps przy użyciu warstwy symboli**

W przypadku warstwy Symbol dodaj dane do źródła danych. Dołącz źródło danych do warstwy. Ponadto źródło danych i warstwa powinny zostać dodane `ready` do mapy po zwolnieniu zdarzenia. Aby renderować unikatową wartość tekstową nad symbolem, informacje tekstowe muszą być przechowywane jako właściwość punktu danych. Właściwość musi być odwołuje `textField` się w opcji warstwy. To podejście jest nieco więcej pracy niż przy użyciu znaczników HTML, ale lepszą wydajność.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-0.2, 51.5],
                zoom: 9,
                
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Create a point feature, add a property to store a label for it, and add it to the data source.
                datasource.add(new atlas.data.Feature(new atlas.data.Point([-0.2, 51.5]), {
                    label: '10'
                }));

                //Add a layer for rendering point data as symbols.
                map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
                    textOptions: {
                        //Use the label property to populate the text for the symbols.
                        textField: ['get', 'label'],
                        color: 'white',
                        offset: [0, -1]
                    }
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Warstwa symboli usługi Azure Maps](media/migrate-google-maps-web-app/azure-maps-symbol-layer.png)</center>

**Dodatkowe zasoby:**

- [Tworzenie źródła danych](create-data-source-web-sdk.md)
- [Dodawanie warstwy symbolu](map-add-pin.md)
- [Dodawanie warstwy bąbelkowej](map-add-bubble-layer.md)
- [Dane punktu klastra](clustering-point-data-web-sdk.md)
- [Dodawanie znaczników HTML](map-add-custom-html.md)
- [Korzystanie z wyrażeń stylu opartych na danych](data-driven-style-expressions-web-sdk.md)
- [Opcje ikon warstwy symboli](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)
- [Opcja Tekst warstwy symboli](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)
- [Klasa znacznika HTML](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)
- [Opcje znaczników HTML](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

### <a name="adding-a-custom-marker"></a>Dodawanie znacznika niestandardowego

Obrazy niestandardowe mogą być używane do reprezentowania punktów na mapie. Poniższa mapa używa niestandardowego obrazu do wyświetlania punktu na mapie. Punkt jest wyświetlany na szerokości geograficznej: 51,5 i długości geograficznej: -0,2. Zakotwiczenie przesuwa położenie znacznika, tak aby punkt ikony pineki był wyrównany z właściwą pozycją na mapie.

<center>

![żółty pinez obraz](media/migrate-google-maps-web-app/ylw_pushpin.png)<br/>
ylw\_pushpin.png</center>

**Przed: Mapy Google**

Utwórz znacznik niestandardowy, `Icon` określając obiekt, który zawiera `url` obraz. Określ `anchor` punkt, aby wyrównać punkt obrazu pinezmy z współrzędną na mapie. Wartość zakotwiczenia w Mapach Google jest względem lewego górnego rogu obrazu.

```javascript
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(51.5, -0.2),
    icon: {
        url: 'https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png',
        anchor: new google.maps.Point(5, 30)
    },
    map: map
});
```

<center>

![Niestandardowy znacznik Map Google](media/migrate-google-maps-web-app/google-maps-custom-marker.png)</center>

**Po: Usługa Azure Maps przy użyciu znaczników HTML**

Aby dostosować znacznik HTML, należy `string` `HTMLElement` przekazać `htmlContent` kod HTML lub opcję znacznika. Użyj `anchor` opcji, aby określić względne położenie znacznika względem współrzędnej położenia. Przypisz do `anchor` opcji jeden z dziewięciu zdefiniowanych punktów odniesienia. Te zdefiniowane punkty to: "center", "top", "bottom", "left", "right", "top-left", "top right", "bottom-left", "bottom-right". Zawartość jest domyślnie zakotwiczona w dolnej części zawartości html. Aby ułatwić migrację kodu z Map Google, `anchor` ustaw opcję "w lewym `pixelOffset` górnym rogu", a następnie użyj opcji z tym samym przesunięciem, która jest używana w Mapach Google. Przesunięcia w usłudze Azure Maps poruszają się w przeciwnym kierunku odsunięć w Mapach Google. Pomnóż więc przesunięcia przez minus jeden.

> [!TIP]
> Dodaj `pointer-events:none` jako styl zawartości html, aby wyłączyć domyślne zachowanie przeciągania w programie Microsoft Edge, który wyświetli niepożądaną ikonę.

```javascript
map.markers.add(new atlas.HtmlMarker({
    htmlContent: '<img src="https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png" style="pointer-events: none;" />',
    anchor: 'top-left',
    pixelOffset: [-5, -30],
    position: [-0.2, 51.5]
}));
```

<center>

![Niestandardowy znacznik HTML usługi Azure Maps](media/migrate-google-maps-web-app/azure-maps-custom-html-marker.png)</center>

**Po: Usługa Azure Maps przy użyciu warstwy symboli**

Warstwy symboli w usłudze Azure Maps obsługują również obrazy niestandardowe. Najpierw załaduj obraz do zasobów mapy i przypisz go z unikatowym identyfikatorem. Odwołują się do obrazu w warstwie symboli. Użyj `offset` tej opcji, aby wyrównać obraz do właściwego punktu na mapie. Użyj `anchor` opcji, aby określić względne położenie symbolu względem współrzędnych położenia. Użyj jednego z dziewięciu zdefiniowanych punktów odniesienia. Punkty te to: "center", "top", "bottom", "left", "right", "top-left", "top right", "bottom-left", "bottom-right". Zawartość jest domyślnie zakotwiczona w dolnej części zawartości html. Aby ułatwić migrację kodu z Map Google, `anchor` ustaw opcję "w lewym `offset` górnym rogu", a następnie użyj opcji z tym samym przesunięciem, która jest używana w Mapach Google. Przesunięcia w usłudze Azure Maps poruszają się w przeciwnym kierunku odsunięć w Mapach Google. Pomnóż więc przesunięcia przez minus jeden.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-0.2, 51.5],
                zoom: 9,
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Load the custom image icon into the map resources.
                map.imageSprite.add('my-yellow-pin', 'https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png').then(function () {

                    //Create a data source and add it to the map.
                    datasource = new atlas.source.DataSource();
                    map.sources.add(datasource);

                    //Create a point and add it to the data source.
                    datasource.add(new atlas.data.Point([-0.2, 51.5]));

                    //Add a layer for rendering point data as symbols.
                    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
                        iconOptions: {
                            //Set the image option to the id of the custom icon that was loaded into the map resources.
                            image: 'my-yellow-pin',
                            anchor: 'top-left',
                            offset: [-5, -30]
                        }
                    }));
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Niestandardowa warstwa symboli ikon usługi Azure Maps](media/migrate-google-maps-web-app/azure-maps-custom-icon-symbol-layer.png)</center>

> [!TIP]
> Aby renderować zaawansowane punkty niestandardowe, należy użyć wielu warstw renderowania razem. Załóżmy na przykład, że chcesz mieć wiele pinezków, które mają tę samą ikonę w różnych kolorowych okręgach. Zamiast tworzyć kilka obrazów dla każdej nakładki kolorów, dodaj warstwę symboli na warstwie bąbelkowej. Niech pineki odwołują się do tego samego źródła danych. Takie podejście będzie bardziej wydajne niż tworzenie i utrzymywanie kilku różnych obrazów.

**Dodatkowe zasoby:**

- [Tworzenie źródła danych](create-data-source-web-sdk.md)
- [Dodawanie warstwy symbolu](map-add-pin.md)
- [Dodawanie znaczników HTML](map-add-custom-html.md)
- [Korzystanie z wyrażeń stylu opartych na danych](data-driven-style-expressions-web-sdk.md)
- [Opcje ikon warstwy symboli](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)
- [Opcja Tekst warstwy symboli](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)
- [Klasa znacznika HTML](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)
- [Opcje znaczników HTML](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

### <a name="adding-a-polyline"></a>Dodawanie polilinii

Polilinie są używane do reprezentowania linii lub ścieżki na mapie. Utwórzmy na mapie polilinię przerywaną.

**Przed: Mapy Google**

Klasa Poliline akceptuje zestaw opcji. Przekaż tablicę współrzędnych w `path` opcji polilinii.

```javascript
//Get the center of the map.
var center = map.getCenter();

//Define a symbol using SVG path notation, with an opacity of 1.
var lineSymbol = {
    path: 'M 0,-1 0,1',
    strokeOpacity: 1,
    scale: 4
};

//Create the polyline.
var line = new google.maps.Polyline({
    path: [
        center,
        new google.maps.LatLng(center.lat() - 0.5, center.lng() - 1),
        new google.maps.LatLng(center.lat() - 0.5, center.lng() + 1)
    ],
    strokeColor: 'red',
    strokeOpacity: 0,
    strokeWeight: 4,
    icons: [{
        icon: lineSymbol,
        offset: '0',
        repeat: '20px'
    }]
});

//Add the polyline to the map.
line.setMap(map);
```

<center>

![Polilinia Map Google](media/migrate-google-maps-web-app/google-maps-polyline.png)</center>

**Po: Mapy platformy Azure**

Polilinie są `LineString` wywoływane lub `MultiLineString` obiekty. Obiekty te można dodawać do źródła danych i renderować za pomocą warstwy liniowej. Dodaj `LineString` do źródła danych, a następnie `LineLayer` dodaj źródło danych do a, aby je renderować.

```javascript
//Get the center of the map.
var center = map.getCamera().center;

//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a line and add it to the data source.
datasource.add(new atlas.data.LineString([
    center,
    [center[0] - 1, center[1] - 0.5],
    [center[0] + 1, center[1] - 0.5]
]));

//Add a layer for rendering line data.
map.layers.add(new atlas.layer.LineLayer(datasource, null, {
    strokeColor: 'red',
    strokeWidth: 4,
    strokeDashArray: [3, 3]
}));
```

<center>

![Polilinia usługi Azure Maps](media/migrate-google-maps-web-app/azure-maps-polyline.png)</center>

**Dodatkowe zasoby:**

- [Dodawanie linii do mapy](map-add-line-layer.md)
- [Opcje warstwy linii](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)
- [Korzystanie z wyrażeń stylu opartych na danych](data-driven-style-expressions-web-sdk.md)

### <a name="adding-a-polygon"></a>Dodawanie wielokąta

Usługi Azure Maps i Mapy Google zapewniają podobną obsługę wielokątów. Wielokąty są używane do reprezentowania obszaru na mapie. Poniższe przykłady pokazują, jak utworzyć wielokąt, który tworzy trójkąt na podstawie środkowej współrzędnej mapy.

**Przed: Mapy Google**

Klasa Wielokąt akceptuje zestaw opcji. Przekaż tablicę współrzędnych do `paths` opcji wielokąta.

```javascript
//Get the center of the map.
var center = map.getCenter();

//Create a polygon.
var polygon = new google.maps.Polygon({
    paths: [
        center,
        new google.maps.LatLng(center.lat() - 0.5, center.lng() - 1),
        new google.maps.LatLng(center.lat() - 0.5, center.lng() + 1),
        center
    ],
    strokeColor: 'red',
    strokeWeight: 2,
    fillColor: 'rgba(0, 255, 0, 0.5)'
});

//Add the polygon to the map
polygon.setMap(map);
```

<center>

![Wielokąt Map Google](media/migrate-google-maps-web-app/google-maps-polygon.png)</center>

**Po: Mapy platformy Azure**

Dodawanie `Polygon` lub `MultiPolygon` obiekt do źródła danych. Renderuj obiekt na mapie przy użyciu warstw. Renderuj obszar wielokąta za pomocą warstwy wielokąta. Można również renderować kontur wielokąta za pomocą warstwy liniowej.

```javascript
//Get the center of the map.
var center = map.getCamera().center;

//Create a data source and add it to the map.
datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a polygon and add it to the data source.
datasource.add(new atlas.data.Polygon([
    center,
    [center[0] - 1, center[1] - 0.5],
    [center[0] + 1, center[1] - 0.5],
    center
]));

//Add a polygon layer for rendering the polygon area.
map.layers.add(new atlas.layer.PolygonLayer(datasource, null, {
    fillColor: 'rgba(0, 255, 0, 0.5)'
}));

//Add a line layer for rendering the polygon outline.
map.layers.add(new atlas.layer.LineLayer(datasource, null, {
    strokeColor: 'red',
    strokeWidth: 2
}));
```

<center>

![Wielokąt usługi Azure Maps](media/migrate-google-maps-web-app/azure-maps-polygon.png)</center>

**Dodatkowe zasoby:**

- [Dodawanie wielokąta do mapy](map-add-shape.md)
- [Dodawanie okręgu do mapy](map-add-shape.md#add-a-circle-to-the-map)
- [Opcje warstwy wielokąta](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)
- [Opcje warstwy linii](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)
- [Korzystanie z wyrażeń stylu opartych na danych](data-driven-style-expressions-web-sdk.md)

### <a name="display-an-info-window"></a>Wyświetlanie okna informacyjnego

Dodatkowe informacje dla encji mogą być wyświetlane `google.maps.InfoWindow` na mapie jako klasa w Mapach Google. W usłudze Azure Maps tę funkcję `atlas.Popup` można osiągnąć przy użyciu klasy. Następne przykłady dodają znacznik do mapy. Po kliknięciu znacznika zostanie wyświetlone okno informacyjne lub okno podręczne.

**Przed: Mapy Google**

Tworzenie wystąpienia okna informacji `google.maps.InfoWindow` przy użyciu konstruktora.

```javascript
//Add a marker in which to display an infowindow for.
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(47.6, -122.33),
    map: map
});

//Create an infowindow.
var infowindow = new google.maps.InfoWindow({
    content: '<div style="padding:5px"><b>Hello World!</b></div>'
});

//Add a click event to the marker to open the infowindow.
marker.addListener('click', function () {
    infowindow.open(map, marker);
});
```

<center>

![Wyskakujące okienko Map Google](media/migrate-google-maps-web-app/google-maps-popup.png)</center>

**Po: Mapy platformy Azure**

Użyjmy okna podręcznego, aby wyświetlić dodatkowe informacje o lokalizacji. Przekaż kod `string` `HTMLElement` HTML lub `content` obiekt do opcji okna podręcznego. Jeśli chcesz, wyskakujące okienka mogą być wyświetlane niezależnie od dowolnego kształtu. W związku z tym `position` pop-ups wymagają wartości, która ma być określona. Określ `position` wartość. Aby wyświetlić wyskakujące okienko, wywołaj `open` metodę i przekaż `map` wyskakujące okienko.

```javascript
//Add a marker to the map in which to display a popup for.
var marker = new atlas.HtmlMarker({
    position: [-122.33, 47.6]
});

//Add the marker to the map.
map.markers.add(marker);

//Create a popup.
var popup = new atlas.Popup({
    content: '<div style="padding:5px"><b>Hello World!</b></div>',
    position: [-122.33, 47.6],
    pixelOffset: [0, -35]
});

//Add a click event to the marker to open the popup.
map.events.add('click', marker, function () {
    //Open the popup
    popup.open(map);
});
```

<center>

![Wyskakujące okienko usługi Azure Maps](media/migrate-google-maps-web-app/azure-maps-popup.png)</center>

> [!NOTE]
> To samo można zrobić z warstwą symbolu, bąbelka, linii lub wielokąta, przekazując wybraną warstwę do kodu zdarzenia mapy zamiast znacznika.

**Dodatkowe zasoby:**

- [Dodawanie menu podręcznego](map-add-popup.md)
- [Wyskakujące okienko z zawartością multimedialną](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popup%20with%20Media%20Content)
- [Wyskakujące okienka na kształtach](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popups%20on%20Shapes)
- [Ponowne wyskakowanie z wieloma pinami](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Reusing%20Popup%20with%20Multiple%20Pins)
- [Klasa popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)
- [Opcje wyskakujące okienka](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions?view=azure-iot-typescript-latest)

### <a name="import-a-geojson-file"></a>Importowanie pliku GeoJSON

Mapy Google obsługują ładowanie i dynamiczne stylizacje `google.maps.Data` danych GeoJSON za pośrednictwem klasy. Funkcjonalność tej klasy jest znacznie bardziej wyrównana z stylizowaną na dane usługi Azure Maps. Ale jest kluczowa różnica. W Mapach Google określasz funkcję wywołania zwrotnego. Logika biznesowa do stylizacji każdej funkcji przetwarzanej indywidualnie w wątku interfejsu użytkownika. Jednak w usłudze Azure Maps warstwy obsługują określanie wyrażeń opartych na danych jako opcje stylizacji. Wyrażenia te są przetwarzane w czasie renderowania w oddzielnym wątku. Podejście usługi Azure Maps zwiększa wydajność renderowania. Ta zaleta jest zauważalna, gdy większe zestawy danych muszą być renderowane szybko.

Poniższe przykłady załadować geojson paszy wszystkich trzęsień ziemi w ciągu ostatnich siedmiu dni z USGS. Dane trzęsienia ziemi renderuje jako skalowane okręgi na mapie. Kolor i skala każdego okręgu jest oparta na wielkości każdego `"mag"` trzęsienia ziemi, które jest przechowywane we właściwości każdej operacji w zestawie danych. Jeśli wielkość jest większa lub równa pięciu, okrąg będzie czerwony. Jeśli jest większa lub równa trzem, ale mniej niż pięć, okrąg będzie pomarańczowy. Jeśli jest mniejsza niż trzy, okrąg będzie zielony. Promień każdego okręgu będzie wykładniczym wielkości pomnożonej przez 0,1.

**Przed: Mapy Google**

Określ pojedynczą funkcję `map.data.setStyle` wywołania zwrotnego w metodzie. Wewnątrz funkcji wywołania zwrotnego zastosuj logikę biznesową do każdej funkcji. Załaduj źródło `map.data.loadGeoJson` danych GeoJSON za pomocą metody.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2
            });

            //Define a callback to style each feature.
            map.data.setStyle(function (feature) {

                //Extract the 'mag' property from the feature.
                var mag = parseFloat(feature.getProperty('mag'));

                //Set the color value to 'green' by default.
                var color = 'green';

                //If the mag value is greater than 5, set the color to 'red'.
                if (mag >= 5) {
                    color = 'red';
                }
                //If the mag value is greater than 3, set the color to 'orange'.
                else if (mag >= 3) {
                    color = 'orange';
                }

                return /** @type {google.maps.Data.StyleOptions} */({
                    icon: {
                        path: google.maps.SymbolPath.CIRCLE,

                        //Scale the radius based on an exponential of the 'mag' value.
                        scale: Math.exp(mag) * 0.1,
                        fillColor: color,
                        fillOpacity: 0.75,
                        strokeWeight: 2,
                        strokeColor: 'white'
                    }
                });
            });

            //Load the data feed.
            map.data.loadGeoJson(earthquakeFeed);
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Google Maps GeoJSON](media/migrate-google-maps-web-app/google-maps-geojson.png)</center>

**Po: Mapy platformy Azure**

GeoJSON to podstawowy typ danych w usłudze Azure Maps. Zaimportuj go `datasource.importFromUrl` do źródła danych przy użyciu metody. Użyj warstwy bąbelkowej. Warstwa bąbelkowa zapewnia funkcje renderowania skalowanych okręgów na podstawie właściwości obiektów w źródle danych. Zamiast funkcji wywołania zwrotnego logika biznesowa jest konwertowana na wyrażenie i przekazywana do opcji stylu. Wyrażenia definiują działanie logiki biznesowej. Wyrażenia mogą być przekazywane do innego wątku i oceniane względem danych elementów. Wiele źródeł danych i warstw można dodać do usługi Azure Maps, z których każdy ma inną logikę biznesową. Ta funkcja umożliwia renderowanie wielu zestawów danych na mapie na różne sposoby.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Load the earthquake data.
                datasource.importDataFromUrl(earthquakeFeed);

                //Create a layer to render the data points as scaled circles.
                map.layers.add(new atlas.layer.BubbleLayer(datasource, null, {
                    //Make the circles semi-transparent.
                    opacity: 0.75,

                    color: [
                        'case',

                        //If the mag value is greater than 5, return 'red'.
                        ['>=', ['get', 'mag'], 5],
                        'red',

                        //If the mag value is greater than 3, return 'orange'.
                        ['>=', ['get', 'mag'], 3],
                        'orange',

                        //Return 'green' as a fallback.
                        'green'
                    ],

                    //Scale the radius based on an exponential of the 'mag' value.
                    radius: ['*', ['^', ['e'], ['get', 'mag']], 0.1]
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Azure Maps GeoJSON](media/migrate-google-maps-web-app/azure-maps-geojson.png)</center>

**Dodatkowe zasoby:**

- [Dodawanie warstwy symbolu](map-add-pin.md)
- [Dodawanie warstwy bąbelkowej](map-add-bubble-layer.md)
- [Dane punktu klastra](clustering-point-data-web-sdk.md)
- [Korzystanie z wyrażeń stylu opartych na danych](data-driven-style-expressions-web-sdk.md)

### <a name="marker-clustering"></a>Klastrowanie znaczników

Podczas wizualizacji wielu punktów danych na mapie punkty mogą nakładać się na siebie. Nakładanie się sprawia, że mapa wygląda na zaśmieconą, a mapa staje się trudna do odczytania i użycia. Dane punktów klastrowania to proces łączenia punktów danych, które znajdują się blisko siebie i reprezentujących je na mapie jako pojedynczy punkt danych klastrowanych. Gdy użytkownik powiększa mapę, klastry rozpadają się na poszczególne punkty danych. Punkty danych klastra w celu poprawy środowiska użytkownika i wydajności map.

W poniższych przykładach kod ładuje źródło danych geojson trzęsienie ziemi z ostatniego tygodnia i dodaje go do mapy. Klastry są renderowane jako skalowane i kolorowe okręgi. Skala i kolor okręgów zależy od liczby punktów, które zawierają.

> [!NOTE]
> Mapy Google i Usługi Azure Maps używają nieco innych algorytmów klastrowania. W związku z tym czasami rozkład punktów w klastrach jest różny.

**Przed: Mapy Google**

Użyj biblioteki MarkerCluster do znaczników klastra. Ikony klastra są ograniczone do obrazów, które mają numery od jednego do pięciu jako ich nazwy. Są one hostowane w tym samym katalogu.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2
            });

            //Download the GeoJSON data.
            fetch(earthquakeFeed)
                .then(function (response) {
                    return response.json();
                }).then(function (data) {
                    //Loop through the GeoJSON data and create a marker for each data point.
                    var markers = [];

                    for (var i = 0; i < data.features.length; i++) {

                        markers.push(new google.maps.Marker({
                            position: new google.maps.LatLng(data.features[i].geometry.coordinates[1], data.features[i].geometry.coordinates[0])
                        }));
                    }

                    //Create a marker clusterer instance and tell it where to find the cluster icons.
                    var markerCluster = new MarkerClusterer(map, markers,
                        { imagePath: 'https://developers.google.com/maps/documentation/javascript/examples/markerclusterer/m' });
                });
        }
    </script>

    <!-- Load the marker cluster library. -->
    <script src="https://developers.google.com/maps/documentation/javascript/examples/markerclusterer/markerclusterer.js"></script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Klastrowanie Map Google](media/migrate-google-maps-web-app/google-maps-clustering.png)</center>

**Po: Mapy platformy Azure**

Dodawanie danych w źródle danych i zarządzanie nimi. Połącz źródła i warstwy danych, a następnie renderuj dane. Klasa `DataSource` w usłudze Azure Maps udostępnia kilka opcji klastrowania.

- `cluster`– Informuje źródło danych o danych punktu klastra.
- `clusterRadius`- Promień w pikselach do klastra punktów razem.
- `clusterMaxZoom`- Maksymalny poziom powiększenia, w którym odbywa się klastrowanie. Jeśli powiększysz więcej niż ten poziom, wszystkie punkty będą renderowane jako symbole.
- `clusterProperties`- Definiuje właściwości niestandardowe, które są obliczane przy użyciu wyrażeń względem wszystkich punktów w każdym klastrze i dodawane do właściwości każdego punktu klastra.

Gdy klastrowanie jest włączone, źródło danych będzie wysyłać klastrowane i niesklastrowane punkty danych do warstw w celu renderowania. Źródło danych może klastrować setki tysięcy punktów danych. Klastrowany punkt danych ma następujące właściwości:

| Nazwa właściwości             | Typ    | Opis   |
|---------------------------|---------|---------------|
| `cluster`                 | wartość logiczna | Wskazuje, czy funkcja reprezentuje klaster. |
| `cluster_id`              | ciąg  | Unikatowy identyfikator klastra, który może być `getClusterExpansionZoom`używany `getClusterChildren`z `getClusterLeaves` datasource , i metody. |
| `point_count`             | numer  | Liczba punktów, które zawiera klaster.  |
| `point_count_abbreviated` | ciąg  | Ciąg, który skraca `point_count` wartość, jeśli jest długa. (na przykład 4000 staje się 4K)  |

Klasa `DataSource` ma następującą funkcję pomocnika dostępu do dodatkowych informacji `cluster_id`o klastrze przy użyciu .

| Metoda | Zwracany typ | Opis |
|--------|-------------|-------------|
| `getClusterChildren(clusterId: number)` | Geometria&lt;&lt;operacji tablicy&gt; \| Promise,&lt;dowolny kształt&gt;&gt; | Pobiera korle danego klastra na następnym poziomie powiększenia. Te dzieci mogą być kombinacją kształtów i podklastrów. Podklastery będą funkcje z właściwości pasujących ClusteredProperties. |
| `getClusterExpansionZoom(clusterId: number)` | Numer&lt;obietnicy&gt; | Oblicza poziom powiększenia, przy którym klaster rozpocznie rozszerzanie lub rozpadanie. |
| `getClusterLeaves(clusterId: number, limit: number, offset: number)` | Geometria&lt;&lt;operacji tablicy&gt; \| Promise,&lt;dowolny kształt&gt;&gt; | Pobiera wszystkie punkty w klastrze. Ustaw, `limit` aby zwrócić podzbiór punktów `offset` i użyj strony do punktu. |

Podczas renderowania danych grupowanych na mapie często najlepiej jest użyć dwóch lub więcej warstw. W poniższym przykładzie użyto trzech warstw. Warstwa bąbelkowa do rysowania skalowanych kolorowych okręgów na podstawie rozmiaru klastrów. Warstwa symbolu, która renderuje rozmiar klastra jako tekst. I używa drugiej warstwy symbolu do renderowania niesklastrowanych punktów. Istnieje wiele innych sposobów renderowania danych klastrowanych. Aby uzyskać więcej informacji, zobacz dokumentację [danych punktu klastra.](clustering-point-data-web-sdk.md)

Bezpośrednio importuj dane GeoJSON przy użyciu `importDataFromUrl` funkcji na `DataSource` klasy, wewnątrz mapy usługi Azure Maps.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource(null, {
                    //Tell the data source to cluster point data.
                    cluster: true
                });
                map.sources.add(datasource);

                //Create layers for rendering clusters, their counts and unclustered points and add the layers to the map.
                map.layers.add([
                    //Create a bubble layer for rendering clustered data points.
                    new atlas.layer.BubbleLayer(datasource, null, {
                        //Scale the size of the clustered bubble based on the number of points inthe cluster.
                        radius: [
                            'step',
                            ['get', 'point_count'],
                            20,         //Default of 20 pixel radius.
                            100, 30,    //If point_count >= 100, radius is 30 pixels.
                            750, 40     //If point_count >= 750, radius is 40 pixels.
                        ],

                        //Change the color of the cluster based on the value on the point_cluster property of the cluster.
                        color: [
                            'step',
                            ['get', 'point_count'],
                            'lime',            //Default to lime green. 
                            100, 'yellow',     //If the point_count >= 100, color is yellow.
                            750, 'red'         //If the point_count >= 750, color is red.
                        ],
                        strokeWidth: 0,
                        filter: ['has', 'point_count'] //Only rendered data points which have a point_count property, which clusters do.
                    }),

                    //Create a symbol layer to render the count of locations in a cluster.
                    new atlas.layer.SymbolLayer(datasource, null, {
                        iconOptions: {
                            image: 'none' //Hide the icon image.
                        },
                        textOptions: {
                            textField: ['get', 'point_count_abbreviated'],
                            offset: [0, 0.4]
                        }
                    }),

                    //Create a layer to render the individual locations.
                    new atlas.layer.SymbolLayer(datasource, null, {
                        filter: ['!', ['has', 'point_count']] //Filter out clustered points from this layer.
                    })
                ]);

                //Retrieve a GeoJSON data set and add it to the data source. 
                datasource.importDataFromUrl(earthquakeFeed);
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Klastrowanie usługi Azure Maps](media/migrate-google-maps-web-app/azure-maps-clustering.png)</center>

**Dodatkowe zasoby:**

- [Dodawanie warstwy symbolu](map-add-pin.md)
- [Dodawanie warstwy bąbelkowej](map-add-bubble-layer.md)
- [Dane punktu klastra](clustering-point-data-web-sdk.md)
- [Korzystanie z wyrażeń stylu opartych na danych](data-driven-style-expressions-web-sdk.md)

### <a name="add-a-heat-map"></a>Dodawanie mapy cieplnej

Mapy skupień, znane również jako mapy gęstości punktów, są rodzajem wizualizacji danych. Są one używane do reprezentowania gęstości danych przy użyciu zakresu kolorów. I są one często używane do pokazywalenia danych "hot spotów" na mapie. Mapy skupień to świetny sposób na renderowanie dużych zestawów danych punktowych.

Poniższe przykłady załadować źródło danych GeoJSON wszystkich trzęsień ziemi w ciągu ostatniego miesiąca, z USGS, i renderuje je jako ważona mapa ciepła. Właściwość `"mag"` jest używana jako waga.

**Przed: Mapy Google**

Aby utworzyć mapę cieplną, załaduj bibliotekę "wizualizacji", dodając `&libraries=visualization` go do adresu URL skryptu interfejsu API. Warstwa mapy skupień w Mapach Google nie obsługuje bezpośrednio danych GeoJSON. Najpierw pobierz dane i przekonwertuj je na szereg ważonych punktów danych:

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_month.geojson';

        function initMap() {

            var map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2,
                mapTypeId: 'satellite'
            });

            //Download the GeoJSON data.
            fetch(url).then(function (response) {
                return response.json();
            }).then(function (res) {
                var points = getDataPoints(res);

                var heatmap = new google.maps.visualization.HeatmapLayer({
                    data: points
                });
                heatmap.setMap(map);
            });
        }

        function getDataPoints(geojson, weightProp) {
            var points = [];

            for (var i = 0; i < geojson.features.length; i++) {
                var f = geojson.features[i];

                if (f.geometry.type === 'Point') {
                    points.push({
                        location: new google.maps.LatLng(f.geometry.coordinates[1], f.geometry.coordinates[0]),
                        weight: f.properties[weightProp]
                    });
                }
            }

            return points;
        } 
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]&libraries=visualization" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Mapa cieplna Map Google](media/migrate-google-maps-web-app/google-maps-heatmap.png)</center>

**Po: Mapy platformy Azure**

Załaduj dane GeoJSON do źródła danych i połącz źródło danych z warstwą mapy cieplnej. Właściwość, która będzie używana dla wagi `weight` mogą być przekazywane do opcji za pomocą wyrażenia. Bezpośrednio importuj dane GeoJSON `importDataFromUrl` do usługi `DataSource` Azure Maps przy użyciu funkcji w klasie.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_month.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,
                style: 'satellite',

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Load the earthquake data.
                datasource.importDataFromUrl(earthquakeFeed);

                //Create a layer to render the data points as a heat map.
                map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
                    weight: ['get', 'mag'],
                    intensity: 0.005,
                    opacity: 0.65,
                    radius: 10
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Mapa cieplna usługi Azure Maps](media/migrate-google-maps-web-app/azure-maps-heatmap.png)</center>

**Dodatkowe zasoby:**

- [Dodawanie warstwy mapy cieplnej](map-add-heat-map-layer.md)
- [Klasa warstwy mapy cieplnej](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.heatmaplayer?view=azure-iot-typescript-latest)
- [Opcje warstwy mapy na ciepło](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)
- [Korzystanie z wyrażeń stylu opartych na danych](data-driven-style-expressions-web-sdk.md)

### <a name="overlay-a-tile-layer"></a>Nakładanie warstwy kafli

Warstwy kafli w usłudze Azure Maps są nazywane nakładkimi obrazów w Mapach Google. Warstwy kafelków umożliwiają nakładanie dużych obrazów podzielonych na mniejsze obrazy sąsiadująco, które są zgodne z systemem kafli. Takie podejście jest powszechnie używane do nakładania dużych obrazów lub dużych zestawów danych.

Poniższe przykłady nakładają warstwę kafli radaru pogodowego z Iowa Environmental Mesonet z Iowa State University.

**Przed: Mapy Google**

W Mapach Google warstwy kafelków `google.maps.ImageMapType` można tworzyć przy użyciu klasy.

```javascript
map.overlayMapTypes.insertAt(0, new google.maps.ImageMapType({
    getTileUrl: function (coord, zoom) {
        return "https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/" + zoom + "/" + coord.x + "/" + coord.y;
    },
    tileSize: new google.maps.Size(256, 256),
    opacity: 0.8
}));
```

<center>

![Warstwa kafelków Map Google](media/migrate-google-maps-web-app/google-maps-tile-layer.png)</center>

**Po: Mapy platformy Azure**

Dodaj warstwę kafli do mapy podobnie jak każdą inną warstwę. Użyj sformatowanego adresu URL, który ma symbole zastępcze x, y, zoom; `{x}`, `{y}` `{z}` aby poinformować warstwę, gdzie mają być dostępne kafelki. Warstwy kafli usługi `{quadkey}` `{bbox-epsg-3857}`Azure `{subdomain}` Maps obsługują również symbole zastępcze i zastępcze.

> [!TIP]
> W usłudze Azure Maps warstwy mogą być łatwo renderowane poniżej innych warstw, w tym warstw map bazowych. Często pożądane jest renderowanie warstw kafelków pod etykietami map, tak aby były łatwe do odczytania. Metoda `map.layers.add` przyjmuje drugi parametr, który jest identyfikatorem warstwy, w którym można wstawić nową warstwę poniżej. Aby wstawić warstwę kafli pod etykietami mapy, użyj tego kodu:`map.layers.add(myTileLayer, "labels");`

```javascript
//Create a tile layer and add it to the map below the label layer.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

<center>

![Warstwa kafli usługi Azure Maps](media/migrate-google-maps-web-app/azure-maps-tile-layer.png)</center>

> [!TIP]
> Żądania kafelków można `transformRequest` przechwytyć za pomocą opcji mapy. Umożliwi to modyfikowanie lub dodawanie nagłówków do żądania w razie potrzeby.

**Dodatkowe zasoby:**

- [Dodawanie warstw kafelków](map-add-tile-layer.md)
- [Klasa warstwy kafelków](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)
- [Opcje warstwy kafli](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest)

### <a name="show-traffic"></a>Wyświetlanie ruchu

Dane o ruchu drogowym można nakładać zarówno na mapy Azure, jak i Google.

**Przed: Mapy Google**

Nakładanie danych o ruchu drogowym na mapę za pomocą warstwy ruchu.

```javascript
var trafficLayer = new google.maps.TrafficLayer();
trafficLayer.setMap(map);
```

<center>

![Ruch w Mapach Google](media/migrate-google-maps-web-app/google-maps-traffic.png)</center>

**Po: Mapy platformy Azure**

Usługa Azure Maps udostępnia kilka różnych opcji wyświetlania ruchu. Wyświetlanie zdarzeń drogowych, takich jak zamknięcia dróg i wypadków, jako ikony na mapie. Nakładanie ruchu i kolorowane drogi na mapie. Kolory mogą być modyfikowane na podstawie zaksięgowanego ograniczenia prędkości w stosunku do normalnego oczekiwanego opóźnienia lub bezwzględnego opóźnienia. Dane zdarzeń w usłudze Azure Maps są aktualizowane co minutę, a dane przepływu są aktualizowane co dwie minuty.

Przypisz poszukiwane `setTraffic` wartości dla opcji.

```javascript
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

<center>

![Ruch usługi Azure Maps](media/migrate-google-maps-web-app/azure-maps-traffic.png)</center>

Jeśli klikniesz na jedną z ikon ruchu w usłudze Azure Maps, dodatkowe informacje są wyświetlane w wyskakującym okienku.

<center>

![Zdarzenie drogowe usługi Azure Maps](media/migrate-google-maps-web-app/azure-maps-traffic-incident.png)</center>

**Dodatkowe zasoby:**

- [Pokazywal ruchu na mapie](map-show-traffic.md)
- [Opcje nakładki ruchu](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Traffic%20Overlay%20Options)

### <a name="add-a-ground-overlay"></a>Dodawanie nakładki naziemie

Zarówno mapy Platformy Azure, jak i Google obsługują nakładanie obrazów georeferencyjnych na mapie. Obrazy georeferencyjne są przesuwane i skalowane podczas przesuwania i powiększania mapy. W Mapach Google obrazy georeferencyjne są nazywane nakładkimi uziemienia, podczas gdy w usłudze Azure Maps są nazywane warstwami obrazów. Świetnie nadają się do budowania planów pięter, nakładania starych map lub zdjęć z drona.

**Przed: Mapy Google**

Określ adres URL obrazu, który chcesz nałożyć, oraz obwiednię, aby powiązać obraz na mapie. W tym przykładzie nakładki obraz mapy [Newark New Jersey z 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) roku na mapie.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map, historicalOverlay;

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(40.740, -74.18),
                zoom: 12
            });

            var imageBounds = {
                north: 40.773941,
                south: 40.712216,
                east: -74.12544,
                west: -74.22655
            };

            historicalOverlay = new google.maps.GroundOverlay(
                'https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg',
                imageBounds);
            historicalOverlay.setMap(map);
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id="myMap" style="position:relative;width:600px;height:400px;"></div>
</body>
</html>
```

Uruchomienie tego kodu w przeglądarce spowoduje wyświetlenie mapy wygląda następującej:

<center>

![Nakładka obrazów Map Google](media/migrate-google-maps-web-app/google-maps-image-overlay.png)</center>

**Po: Mapy platformy Azure**

`atlas.layer.ImageLayer` Klasa służy do nakładania obrazów georeferencyjnych. Ta klasa wymaga adresu URL do obrazu i zestaw współrzędnych dla czterech narożników obrazu. Obraz musi być hostowany w tej samej domenie lub mieć włączone cors włączone.

> [!TIP]
> Jeśli masz tylko informacje o północy, południu, wschodzie, zachodzie i rotacji, a współrzędne [`atlas.layer.ImageLayer.getCoordinatesFromEdges`](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest#getcoordinatesfromedges-number--number--number--number--number-) dla każdego narożnika obrazu nie są współrzędne, możesz użyć metody statycznej.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-74.18, 40.740],
                zoom: 12,

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create an image layer and add it to the map.
                map.layers.add(new atlas.layer.ImageLayer({
                    url: 'newark_nj_1922.jpg',
                    coordinates: [
                        [-74.22655, 40.773941], //Top Left Corner
                        [-74.12544, 40.773941], //Top Right Corner
                        [-74.12544, 40.712216], //Bottom Right Corner
                        [-74.22655, 40.712216]  //Bottom Left Corner
                    ]
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Nakładka obrazu usługi Azure Maps](media/migrate-google-maps-web-app/azure-maps-image-overlay.png)</center>

**Dodatkowe zasoby:**

- [Nakładka obrazu](map-add-image-layer.md)
- [Klasa warstwy obrazu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)

## <a name="add-kml-to-the-map"></a>Dodawanie KML do mapy

Zarówno mapy Platformy Azure, jak i Google mogą importować i renderować dane KML, KMZ i GeoRSS na mapie. Usługa Azure Maps obsługuje również pliki GPX, GML, przestrzenne PLIKI CSV, GeoJSON, Dobrze znany tekst (WKT), usługi mapowania sieci Web (WMS), usługi mapowania stron internetowych (WMTS) i usługi elementów charakterystycznych sieci Web (WFS). Usługa Azure Maps odczytuje pliki lokalnie do pamięci i w większości przypadków może obsługiwać znacznie większe pliki KML. 

**Przed: Mapy Google**


```javascript
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map, historicalOverlay;

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(0, 0),
                zoom: 1
            });

             var layer = new google.maps.KmlLayer({
              url: 'https://googlearchive.github.io/js-v2-samples/ggeoxml/cta.kml',
              map: map
            });
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id="myMap" style="position:relative;width:600px;height:400px;"></div>
</body>
</html>
```

Uruchomienie tego kodu w przeglądarce spowoduje wyświetlenie mapy wygląda następującej:

<center>

![Nakładka obrazów Map Google](media/migrate-google-maps-web-app/google-maps-kml.png)</center>

**Po: Mapy platformy Azure**

W usłudze Azure Maps, GeoJSON jest głównym formatem danych używanym w web SDK, dodatkowe formaty danych przestrzennych można łatwo zintegrować za pomocą [przestrzennego modułu We/Wy.](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/) Moduł ten posiada funkcje zarówno do odczytu, jak i zapisu danych przestrzennych, a także zawiera prostą warstwę danych, która może łatwo renderować dane z dowolnego z tych formatów danych przestrzennych. Aby odczytać dane w pliku danych przestrzennych, wystarczy przekazać w adresie URL `atlas.io.read` lub nieprzetworzonych danych jako ciąg lub obiekt blob do funkcji. Spowoduje to zwrócenie wszystkich analizowanych danych z pliku, które następnie można dodać do mapy. KML jest nieco bardziej złożony niż większość formatu danych przestrzennych, ponieważ zawiera o wiele więcej informacji o stylizacji. Klasa `SpatialDataLayer` obsługuje renderowanie większości tych stylów, jednak obrazy ikon muszą być ładowane do mapy przed załadowaniem danych obiektów, a nakładki uziemienia muszą być dodawane jako warstwy do mapy oddzielnie. Podczas ładowania danych za pośrednictwem adresu URL, powinny być hostowane w punkcie końcowym obsługującym CORs lub usługi proxy powinny być przekazywane jako opcja do funkcji odczytu. 

```javascript
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <!-- Add reference to the Azure Maps Spatial IO module. -->
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>

    <script type='text/javascript'>
        var map, datasource, layer;

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                view: 'Auto',

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {
            
                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Add a simple data layer for rendering the data.
                layer = new atlas.layer.SimpleDataLayer(datasource);
                map.layers.add(layer);

                //Read a KML file from a URL or pass in a raw KML string.
                atlas.io.read('https://googlearchive.github.io/js-v2-samples/ggeoxml/cta.kml').then(async r => {
                    if (r) {

                        //Check to see if there are any icons in the data set that need to be loaded into the map resources.
                        if (r.icons) {
                            //For each icon image, create a promise to add it to the map, then run the promises in parrallel.
                            var imagePromises = [];

                            //The keys are the names of each icon image.
                            var keys = Object.keys(r.icons);

                            if (keys.length !== 0) {
                                keys.forEach(function (key) {
                                    imagePromises.push(map.imageSprite.add(key, r.icons[key]));
                                });

                                await Promise.all(imagePromises);
                            }
                        }

                        //Load all features.
                        if (r.features && r.features.length > 0) {
                            datasource.add(r.features);
                        }

                        //Load all ground overlays.
                        if (r.groundOverlays && r.groundOverlays.length > 0) {
                            map.layers.add(r.groundOverlays);
                        }

                        //If bounding box information is known for data, set the map view to it.
                        if (r.bbox) {
                            map.setCamera({ bounds: r.bbox, padding: 50 });
                        }
                    }
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Nakładka obrazu usługi Azure Maps](media/migrate-google-maps-web-app/azure-maps-kml.png)</center>

**Dodatkowe zasoby:**

- [atlas.io.read, funkcja](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io?view=azure-maps-typescript-latest#read-string---arraybuffer---blob--spatialdatareadoptions-)
- [Warstwa simpledata](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.layer.simpledatalayer)
- [SimpleDataLayerOptions (Proste warstwy danych)](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.simpledatalayeroptions)

## <a name="additional-code-samples"></a>Dodatkowe przykłady kodu

Poniżej przedstawiono kilka dodatkowych przykładów kodu związanych z migracją w Mapach Google:

- [Narzędzia do rysowania](map-add-drawing-toolbar.md)
- [Ogranicz mapę do przesuwania dwoma palcami](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Limit%20Map%20to%20Two%20Finger%20Panning)
- [Powiększenie kółka przewijania ogranicz](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Limit%20Scroll%20Wheel%20Zoom)
- [Tworzenie kontrolki pełnoekranowej](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Create%20a%20Fullscreen%20Control)

**Usług:**

- [Korzystanie z modułu usług Usługi Azure Maps](how-to-use-services-module.md)
- [Wyszukiwanie punktów orientacyjnych](map-search-location.md)
- [Uzyskaj informacje ze współrzędnej (odwrotny geokod)](map-get-information-from-coordinate.md)
- [Wyświetlanie instrukcji dotyczących trasy z punktu A do punktu B](map-route.md)
- [Szukaj autosuggest z JQuery UI](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Search%20Autosuggest%20and%20JQuery%20UI)

## <a name="google-maps-v3-to-azure-maps-web-sdk-class-mapping"></a>Map Google Od V3 do Map Platformy Azure Web SDK mapowanie klasy

Poniższy dodatek zawiera odsyłacz do często używanych klas w Mapach Google V3 i równoważne Azure Maps Web SDK.

### <a name="core-classes"></a>Podstawowe klasy

| Mapy Google   | Azure Maps  |
|---------------|-------------|
| `google.maps.Map` | [Atlas. Mapę](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)  |
| `google.maps.InfoWindow` | [Atlas. Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)  |
| `google.maps.InfoWindowOptions` | [Atlas. Wyskakujące okienka](https://docs.microsoft.com/) |
| `google.maps.LatLng`  | [atlas.data.Pozycja](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.position?view=azure-iot-typescript-latest)  |
| `google.maps.LatLngBounds` | [atlas.data.Obłąkane pole](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) |
| `google.maps.MapOptions`  | [Atlas. CameraOptions (Ochemaczenie kamery)](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions?view=azure-iot-typescript-latest)<br/>[Atlas. CameraBoundsOptions (Obejm kamery)](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraboundsoptions?view=azure-iot-typescript-latest)<br/>[Atlas. Ochopcje usług](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.serviceoptions?view=azure-iot-typescript-latest)<br/>[Atlas. Opcje stylu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions?view=azure-iot-typescript-latest)<br/>[Atlas. UserInteractionOptions (Ustawienia interwencji użytkownika)](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.userinteractionoptions?view=azure-iot-typescript-latest) |
| `google.maps.Point`  | [Atlas. Pikseli](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.pixel?view=azure-iot-typescript-latest)   |

## <a name="overlay-classes"></a>Klasy nakładek

| Mapy Google  | Azure Maps  |
|--------------|-------------|
| `google.maps.Marker` | [Atlas. Znacznik html](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)<br/>[atlas.data.Punkt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest)  |
| `google.maps.MarkerOptions`  | [Atlas. HtmlMarkerOptions (Niem. html)](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)<br/>[atlas.layer.SymbolLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)<br/>[Atlas. Opcje warstw symboli](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)<br/>[Atlas. Ikonyopcje](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)<br/>[Atlas. Opcje tekstu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)<br/>[atlas.layer.BubbleLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)<br/>[Atlas. BubbleLayerOptions (BubbleLayerOptions)](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest) |
| `google.maps.Polygon`  | [atlas.data.Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)               |
| `google.maps.PolygonOptions` |[atlas.layer.PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)<br/> [Atlas. PolygonLayerOptions (PolygonLayerOptions)](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)<br/> [atlas.layer.LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest)<br/> [Atlas. LineLayerOptions (LineLayerOptions)](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)|
| `google.maps.Polyline` | [atlas.data.LineString](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.linestring?view=azure-iot-typescript-latest)         |
| `google.maps.PolylineOptions` | [atlas.layer.LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-maps-typescript-latest)<br/>[Atlas. LineLayerOptions (LineLayerOptions)](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-maps-typescript-latest) |
| `google.maps.Circle`  | Zobacz [Dodawanie okręgu do mapy](map-add-shape.md#add-a-circle-to-the-map)                                     |
| `google.maps.ImageMapType`  | [Atlas. Warstwa płytek](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)         |
| `google.maps.ImageMapTypeOptions` | [Atlas. TileLayerOptions (TileLayerOptions)](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest) |
| `google.maps.GroundOverlay`  | [atlas.layer.ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)<br/>[Atlas. ImageLayerOptions (Pliki graficzne)](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagelayeroptions?view=azure-iot-typescript-latest) |

## <a name="service-classes"></a>Klasy usług

Zestaw Azure Maps Web SDK zawiera moduł usług, który można załadować oddzielnie. Ten moduł zawija usługi Usługi Azure Maps REST za pomocą internetowego interfejsu API i może być używany w aplikacjach JavaScript, TypeScript i Node.js.

| Mapy Google | Azure Maps  |
|-------------|-------------|
| `google.maps.Geocoder` | [atlas.service.SearchUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest)  |
| `google.maps.GeocoderRequest`  | [Atlas. SzukajAddressOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SearchAddressRevrseOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressreverseoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SearchAddressReverseCrossStreetOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressreversecrossstreetoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SzukajAddressStructuredOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressstructuredoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SzukajDąrouteOpcje](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchalongrouteoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SzukajFuzzyOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchfuzzyoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SearchInsideGeometryOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchinsidegeometryoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SzukajNearbyOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchnearbyoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SearchPOIOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchpoioptions?view=azure-iot-typescript-latest)<br/>[Atlas. SearchPOICategoryOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchpoicategoryoptions?view=azure-iot-typescript-latest) |
| `google.maps.DirectionsService`  | [atlas.service.RouteUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest)  |
| `google.maps.DirectionsRequest`  | [Atlas. Funkcje obliczania](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.calculateroutedirectionsoptions?view=azure-iot-typescript-latest) |
| `google.maps.places.PlacesService` | [atlas.service.SearchUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest)  |

## <a name="libraries"></a>Biblioteki

Biblioteki dodają dodatkowe funkcje do mapy. Wiele z tych bibliotek znajduje się w podstawowym sdk usługi Azure Maps. Oto kilka równoważnych klas do wykorzystania zamiast tych bibliotek Map Google

| Mapy Google           | Azure Maps   |
|-----------------------|--------------|
| Biblioteka rysunków       | [Moduł narzędzi do rysowania](set-drawing-options.md) |
| Biblioteka geometrii      | [atlas.matematyka](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)   |
| Biblioteka wizualizacji | [Warstwa mapy cieplnej](map-add-heat-map-layer.md) |

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o sdk sieci Web usługi Azure Maps.

> [!div class="nextstepaction"]
> [Jak korzystać z kontrolki mapy](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Jak korzystać z modułu usług](how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Jak korzystać z modułu narzędzi do rysowania](set-drawing-options.md)

> [!div class="nextstepaction"]
> [Przykłady kodu](https://docs.microsoft.com/samples/browse/?products=azure-maps)

