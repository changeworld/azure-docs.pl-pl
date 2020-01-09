---
title: Migrowanie aplikacji systemu Android | Microsoft Docs
description: Samouczek dotyczący sposobu migrowania aplikacji systemu Android z usługi Mapy Google do usługi Microsoft Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 60d8fcc9879e89276aad80bbaf3a0edf244a45b8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75481401"
---
# <a name="migrate-an-android-app-from-google-maps"></a>Migrowanie aplikacji systemu Android ze sklepu Google Maps

Android SDK Azure Maps ma interfejs API, który jest bardzo podobny do zestawu SDK sieci Web. Jeśli zostały opracowane z jednego z tych zestawów SDK, mają zastosowanie wiele z tych samych koncepcji, najlepszych rozwiązań i architektur, a użytkownik powinien być w stanie łatwo przenieść swoją wiedzę z jednego do drugiego.

Android SDK Azure Maps obsługuje minimalną wersję systemu Android dla interfejsu API 21: Android 5.0.0 (lizak).

Wszystkie przykłady zawarte w języku Java Kotlin mogą być również używane z Android SDK Azure Maps.

Więcej informacji na temat programowania za pomocą tego zestawu SDK można znaleźć w [przewodnikach dotyczących wykonywania Azure Maps Android SDK](how-to-use-android-map-control-library.md) .

## <a name="load-a-map"></a>Załaduj mapę

Załadowanie mapy w aplikacji systemu Android przy użyciu usługi Google lub Azure Maps obejmuje wiele z tych samych kroków. W przypadku korzystania z jednego z zestawów SDK należy:

- Uzyskaj interfejs API lub klucz subskrypcji, aby uzyskać dostęp do dowolnej platformy.
- Dodaj plik XML do działania, aby określić, gdzie powinna być renderowana Mapa i jak powinna zostać ustanowiona.
- Przekazuj wszystkie metody cyklu życiowego z działania zawierającego widok mapy do odpowiednich z nich w klasie mapy. W szczególności należy przesłać dalej na następujące metody:
    - `onCreate(Bundle)`
    - `onStart()`
    - `onResume()`
    - `onPause()`
    - `onStop()`
    - `onDestroy()`
    - `onSaveInstanceState(Bundle)`
    - `onLowMemory()`
- Poczekaj, aż mapa będzie gotowa, zanim spróbuje uzyskać do niej dostęp programowo.

**Wcześniej: Google Maps**

Aby wyświetlić mapę przy użyciu zestawu SDK usługi Google Maps dla systemu Android, należy wykonać następujące czynności:

1.  Upewnij się, że zainstalowano usługi Google Play Services.
2.  Dodaj zależność usługi Google Maps do pliku **Gradle. Build** modułu: 

    `implementation 'com.google.android.gms:play-services-maps:17.0.0'`

1.  Dodaj klucz interfejsu API usługi Google Maps w sekcji aplikacji w programie **google\_Maps\_pliku API. XML** :
    
    ```xml
    <meta-data android:name="com.google.android.geo.API_KEY" android:value="YOUR_GOOGLE_MAPS_KEY"/>
    ```

1.  Dodaj fragment mapy do działania głównego:

    ```xml
    <com.google.android.gms.maps.MapView
            android:id="@+id/myMap"
            android:layout_width="match_parent"
            android:layout_height="match_parent"/>
    ```

1.  W pliku **MAINS. Java** należy dodać Importy dla zestawu SDK usługi Google Maps. Przekazuj wszystkie metody cyklu życiowego z działania zawierającego widok mapy do odpowiednich z nich w klasie mapy. Wystąpienie `MapView` można pobrać ze fragmentu mapy za pomocą metody `getMapAsync(OnMapReadyCallback)`. `MapView` automatycznie inicjuje system map i widok. Edytuj plik **MAINS. Java** w następujący sposób:

    ```java
    import com.google.android.gms.maps.GoogleMap;
    import com.google.android.gms.maps.MapView;
    import com.google.android.gms.maps.OnMapReadyCallback;
    
    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    
    public class MainActivity extends AppCompatActivity implements OnMapReadyCallback {
    
        MapView mapView;
    
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            mapView = findViewById(R.id.myMap);
    
            mapView.onCreate(savedInstanceState);
            mapView.getMapAsync(this);
        }
    
        @Override
    
        public void onMapReady(GoogleMap map) {
            //Add your post map load code here.
        }
    
        @Override
        public void onResume() {
            super.onResume();
            mapView.onResume();
        }
    
        @Override
        protected void onStart(){
            super.onStart();
            mapView.onStart();
        }
    
        @Override
        public void onPause() {
            super.onPause();
            mapView.onPause();
        }
    
        @Override
        public void onStop() {
            super.onStop();
            mapView.onStop();
        }
    
        @Override
        public void onLowMemory() {
            super.onLowMemory();
            mapView.onLowMemory();
        }
    
        @Override
        protected void onDestroy() {
            super.onDestroy();
            mapView.onDestroy();
        }
    
        @Override
        protected void onSaveInstanceState(Bundle outState) {
            super.onSaveInstanceState(outState);
            mapView.onSaveInstanceState(outState);
        }
    }
    ```

Po uruchomieniu w aplikacji formant mapy zostanie załadowany w następujący sposób.

<center>

![proste usługi Google Maps](media/migrate-google-maps-android-app/simple-google-maps.png)</center>

**Po: Azure Maps**

Aby wyświetlić mapę przy użyciu zestawu Azure Maps SDK dla systemu Android, należy wykonać następujące czynności:

1. Otwórz plik **Build. Gradle** najwyższego poziomu i Dodaj następujący kod do sekcji **wszystkie projekty**, blok **repozytoriów** :

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Zaktualizuj **aplikację/Build. Gradle** i Dodaj do niej następujący kod:
    
    1. Upewnij się, że **minSdkVersion** projektu ma interfejs API w wersji 21 lub nowszej.

    2. Dodaj następujący kod do sekcji systemu Android:

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    3. Zaktualizuj blok zależności i Dodaj nową linię zależności implementacji dla najnowszej Azure Maps Android SDK:

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.2"
        ```

        > [!Note]
        > Android SDK Azure Maps są regularnie uaktualniane i rozszerzane. Aby uzyskać najnowszy numer wersji implementacji Azure Maps, można zapoznać się z dokumentacją dotyczącą rozpoczynania [pracy z kontrolką mapy systemu Android](how-to-use-android-map-control-library.md) . Ponadto można ustawić numer wersji z "0,2" na "0 +", aby zawsze wskazywał najnowszą wersję.
    
    4. Przejdź do **pliku** na pasku narzędzi, a następnie kliknij pozycję **Synchronizuj projekt z plikami Gradle**.
3. Dodaj fragment mapy do działania głównego (\> układu zasobów \> działania\_Main. xml):
    
    ```XML
    <?xml version="1.0" encoding="utf-8"?>
    <FrameLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        >

        <com.microsoft.azure.maps.mapcontrol.MapControl
            android:id="@+id/mapcontrol"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            />
    </FrameLayout>
    ```

4. W pliku **MAINS. Java** należy wykonać następujące:
    
    * Dodaj Importy dla zestawu SDK Azure Maps
    * Ustawianie informacji o uwierzytelnianiu Azure Maps
    * Pobieranie wystąpienia kontrolki mapy w metodzie **OnCreate**

    Ustawienie informacji o uwierzytelnianiu w klasie `AzureMaps` globalnie przy użyciu metod `setSubscriptionKey` lub `setAadProperties` sprawia, że nie będzie trzeba dodawać informacji o uwierzytelnianiu w każdym widoku. 

    Kontrolka mapy zawiera własne metody cyklu życia do zarządzania cyklem życia OpenGL dla systemu Android, które muszą być wywoływane bezpośrednio z działania zawierającego. Aby aplikacja mogła prawidłowo, wywołaj metody cyklu życia kontrolki mapy, należy zastąpić następujące metody cyklu życia w działaniu zawierającym formant mapy i wywołać odpowiednią metodę kontroli mapy. 

    * `onCreate(Bundle)` 
    * `onStart()` 
    * `onResume()` 
    * `onPause()` 
    * `onStop()` 
    * `onDestroy()` 
    * `onSaveInstanceState(Bundle)` 
    * `onLowMemory()`

    Edytuj plik **MAINS. Java** w następujący sposób:
    
    ```java
    package com.example.myapplication;

    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.options.MapStyle;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;

    public class MainActivity extends AppCompatActivity {
        
        static {
            AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
        }

        MapControl mapControl;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            mapControl = findViewById(R.id.mapcontrol);

            mapControl.onCreate(savedInstanceState);
    
            //Wait until the map resources are ready.
            mapControl.onReady(map -> {
                //Add your post map load code here.
    
            });
        }

        @Override
        public void onResume() {
            super.onResume();
            mapControl.onResume();
        }

        @Override
        protected void onStart(){
            super.onStart();
            mapControl.onStart();
        }

        @Override
        public void onPause() {
            super.onPause();
            mapControl.onPause();
        }

        @Override
        public void onStop() {
            super.onStop();
            mapControl.onStop();
        }

        @Override
        public void onLowMemory() {
            super.onLowMemory();
            mapControl.onLowMemory();
        }

        @Override
        protected void onDestroy() {
            super.onDestroy();
            mapControl.onDestroy();
        }

        @Override
        protected void onSaveInstanceState(Bundle outState) {
            super.onSaveInstanceState(outState);
            mapControl.onSaveInstanceState(outState);
        }
    }
    ```

Po uruchomieniu aplikacji formant mapy zostanie załadowany w następujący sposób.

<center>

![proste Azure Maps](media/migrate-google-maps-android-app/simple-azure-maps.png)</center>

Zauważ, że formant Azure Maps obsługuje bardziej powiększanie i oferuje więcej informacji o widoku światowym.

> [!TIP]
> W przypadku korzystania z emulatora systemu Android w systemie Windows mapa może nie być renderowana z powodu konfliktów z renderowaniem grafiki OpenGL i oprogramowania. Aby rozwiązać ten problem, działały następujące czynności. Otwórz Menedżera AVD i wybierz urządzenie wirtualne do edycji. W sekcji **emulowana wydajność** ustaw opcję **grafika** na **sprzęt**.

## <a name="localizing-the-map"></a>Lokalizowanie mapy

Jeśli odbiorcy są rozproszeni w wielu krajach lub mówisz w różnych językach, lokalizacja jest ważna.

**Wcześniej: Google Maps**

Język mapy można ustawić w metodzie `onCreate` głównego działania, dodając następujący kod przed ustawieniem widoku kontekstu mapy. Poniższe wartości ograniczają język do języka francuskiego przy użyciu kodu języka "fr".

```java
String languageToLoad = "fr";
Locale locale = new Locale(languageToLoad);
Locale.setDefault(locale);
Configuration config = new Configuration();
config.locale = locale;
getBaseContext().getResources().updateConfiguration(config,
        getBaseContext().getResources().getDisplayMetrics());
```

Oto przykład mapy Google z ustawionym językiem "fr".

<center>

![lokalizację usługi Google Maps](media/migrate-google-maps-android-app/google-maps-localization.png)</center>

**Po: Azure Maps**

Azure Maps udostępnia trzy różne sposoby ustawiania języka i widoku regionalnego mapy. Pierwszym z nich jest przekazywanie informacji o języku i widoku regionalnym do klasy `AzureMaps` przy użyciu statycznych `setLanguage` i `setView` Metoda globalnie. Spowoduje to ustawienie języka domyślnego i widoku regionalnego dla wszystkich kontrolek Azure Maps załadowanych w aplikacji. Poniżej można ograniczyć język do języka francuskiego przy użyciu kodu języka "fr-FR".

```java
static {
    //Set your Azure Maps Key.
    AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

    //Set the language to be used by Azure Maps.
    AzureMaps.setLanguage("fr-FR");

    //Set the regional view to be used by Azure Maps.
    AzureMaps.setView("auto");
}
```

Druga opcja polega na przejściu języka i wyświetleniu informacji w kodzie XML kontrolki mapy.

```xml
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="auto"
    />
```

Trzecia opcja polega na programowo ustawić język i widok regionalny mapy przy użyciu metody Maps `setStyle`. Można to zrobić w dowolnym momencie, aby zmienić język i widok regionalny mapy.

```java
mapControl.onReady(map -> {
    map.setStyle(StyleOptions.language("fr-FR"));
    map.setStyle(StyleOptions.view("auto"));
});
```

Oto przykład Azure Maps z językiem ustawionym na "fr-FR".

<center>

![lokalizacji Azure Maps](media/migrate-google-maps-android-app/azure-maps-localization.png)</center>

Pełna lista obsługiwanych języków i widoków regionalnych została udokumentowana [tutaj](supported-languages.md).

## <a name="setting-the-map-view"></a>Ustawianie widoku mapy

Mapy dynamiczne na platformie Azure i w usłudze Google Maps można programistycznie przenieść do nowych lokalizacji geograficznych, wywołując odpowiednie metody. W poniższych przykładach pokazano, jak utworzyć mapę wyświetlania zdjęć satelitarnych, Wyśrodkuj mapę względem lokalizacji ze współrzędnymi (Latitude: 35,0272, Długość geograficzna:-111,0225) i zmień poziom powiększenia na 15 w usłudze mapy Google.

> [!NOTE]
> Usługa mapy Google używa kafelków, które są 256 pikseli w wymiarach, podczas gdy Azure Maps używa większego rozmiaru 512 pikseli. Zmniejsza to liczbę żądań sieci wymaganych przez Azure Maps do załadowania tego samego obszaru mapy co Google Maps. Jednak ze względu na sposób, w jaki diagramy kafelków działają w kontrolkach mapy, większe kafelki w Azure Maps oznacza, że aby osiągnąć ten sam obszar widoczny jako mapa w usłudze Google Maps, należy odjąć poziom powiększenia używany w usłudze Google Maps przez 1 podczas korzystania z Azure Maps.

**Wcześniej: Google Maps**

Aparat kontrolki mapy usługi Google Maps można programistycznie przenieść przy użyciu metody `moveCamera`, która umożliwia określenie centrum mapy i poziomu powiększenia. Metoda `setMapType` może służyć do zmiany typu wyświetlanej mapy.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.moveCamera(CameraUpdateFactory.newLatLngZoom(new LatLng(35.0272, -111.0225), 15));
    mapView.setMapType(GoogleMap.MAP_TYPE_SATELLITE);
}
```

<center>

](media/migrate-google-maps-android-app/google-maps-set-view.png)</center> widok zestawu usługi Google Maps ![

**Po: Azure Maps**

Jak wspomniano wcześniej, aby osiągnąć ten sam obszar widoczny w Azure Maps, Odejmij poziom powiększenia używany w usłudze Google Maps według jednego, w tym przypadku należy użyć poziomu powiększenia o wartości 14.

Początkowy widok mapy można ustawić w atrybutach XML w kontrolce mapy.

```xml
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_cameraLat="35.0272"
    app:mapcontrol_cameraLng="-111.0225"
    app:mapcontrol_zoom="14"
    app:mapcontrol_style="satellite"
    />
```

Widok mapy można aktualizować programowo przy użyciu metod Maps `setCamera` i `setStyle`.

```java
mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(35.0272, -111.0225), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

<center>

![widok zestawu Azure Maps](media/migrate-google-maps-android-app/azure-maps-set-view.png)</center>

**Dodatkowe zasoby:**

- [Obsługiwane style mapy](supported-map-styles.md)

## <a name="adding-a-marker"></a>Dodawanie znacznika

Dane punktów są często renderowane na mapie przy użyciu obrazu na mapie. Te obrazy są często określane jako znaczniki, pinezki, numery PIN lub symbole. Poniższe przykłady renderują dane punktu jako znaczniki na mapie na (Szerokość geograficzna: 51,5, Długość geograficzna:-0,2).

**Wcześniej: Google Maps**

Dzięki usłudze Google Maps znaczniki są dodawane przy użyciu metody Maps `addMarker`.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33)));
}
```

<center>

znacznik ![Google Maps](media/migrate-google-maps-android-app/google-maps-marker.png)</center>

**Po: Azure Maps**

W Azure Maps dane punktu mogą być renderowane na mapie przez dodanie danych do źródła danych, a następnie połączenie tego źródła danych z warstwą symboli. Źródło danych optymalizuje zarządzanie danymi przestrzennymi w kontrolce mapy, a warstwa symboli określa sposób renderowania danych punktu przy użyciu jako obrazu i/lub tekstu.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create a point feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

    //Create a symbol layer and add it to the map.
    map.layers.add(new SymbolLayer(dataSource));
});
```

<center>

![znacznik Azure Maps](media/migrate-google-maps-android-app/azure-maps-marker.png)</center>

## <a name="adding-a-custom-marker"></a>Dodawanie znacznika niestandardowego

Obrazy niestandardowe mogą służyć do reprezentowania punktów na mapie. Poniższy obraz jest używany w poniższych przykładach Użyj obrazu niestandardowego do wyświetlania punktu na mapie (Latitude: 51,5, Długość geograficzna:-0,2) i przesunięcia położenia znacznika, tak aby punkt ikony pinezki wyrównany do poprawnego położenia na mapie.

<center>

![żółty obraz pinezki](media/migrate-google-maps-web-app/ylw_pushpin.png)<br/>
YLW\_pinezki. png</center>

W obu przykładach Powyższy obraz jest dodawany do folderu do rysowania zasobów aplikacji.

**Wcześniej: Google Maps**

Za pomocą usługi Google Maps można używać obrazów niestandardowych dla znaczników przez załadowanie ich przy użyciu opcji `icon` znacznika. Aby wyrównać punkt obrazu do współrzędnej, można użyć opcji `anchor`. Zakotwiczenie jest względne dla wymiarów obrazu, w tym przypadku 0,2 jednostek szerokości i 1 jednostki.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33))
    .icon(BitmapDescriptorFactory.fromResource(R.drawable.ylw_pushpin))
    .anchor(0.2f, 1f));
}
```

<center>

![znacznika niestandardowego usługi Google Maps](media/migrate-google-maps-android-app/google-maps-custom-marker.png)</center>

**Po: Azure Maps**

Warstwy symboli w Azure Maps obsługują również obrazy niestandardowe, ale obraz musi zostać załadowany najpierw do zasobów mapy i przypisany unikatowy identyfikator. Warstwa symboli może odwoływać się do tego identyfikatora. Symbol może być przesunięty, aby wyrównać do poprawnego punktu na obrazie przy użyciu opcji `iconOffset`. Należy zauważyć, że przesunięcie ikony jest w pikselach. Domyślnie przesunięcie jest względem środkowego środka obrazu, ale można je dostosować przy użyciu opcji `iconAnchor`. W tym przykładzie ustawiono opcję `iconAnchor`, aby `"center"` i używała przesunięcia ikony do przenoszenia obrazu pięć pikseli do prawej i 15 pikseli, aby wyrównać punkt obrazu pinezki.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create a point feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

    //Load the custom image icon into the map resources.
    map.images.add("my-yellow-pin", R.drawable.ylw_pushpin);

    //Create a symbol that uses the custom image icon and add it to the map.
    map.layers.add(new SymbolLayer(dataSource,
        iconImage("my-yellow-pin"),
        iconAnchor(AnchorType.CENTER
        iconOffset(new Float[]{5f, -15f})));
});
```

<center>

![Azure Maps znacznik niestandardowy](media/migrate-google-maps-android-app/azure-maps-custom-marker.png)</center>

## <a name="adding-a-polyline"></a>Dodawanie linii łamanej

Linie łamane są używane do reprezentowania linii lub ścieżki na mapie. W poniższych przykładach pokazano, jak utworzyć kreskowaną linię łamaną na mapie.

**Wcześniej: Google Maps**

Dzięki usłudze Google Maps można utworzyć linię łamaną przy użyciu klasy `PolylineOptions` i dodać ją do mapy przy użyciu metody `addPolyline`. Kolor obrysu można ustawić przy użyciu opcji `color`, szerokość obrysu jest ustawiana za pomocą opcji Szerokość, a tablica kreskowana może być ustawiana przy użyciu opcji `pattern`.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the polyline.
    PolylineOptions lineOptions = new PolylineOptions()
            .add(new LatLng(46, -123))
            .add(new LatLng(49, -122))
            .add(new LatLng(46, -121))
            .color(Color.RED)
            .width(10f)
            .pattern(Arrays.<PatternItem>asList(
                    new Dash(30f), new Gap(30f)));

    //Add the polyline to the map.
    Polyline polyline = mapView.addPolyline(lineOptions);
}
```

<center>

![linię łamaną w usłudze Google Maps](media/migrate-google-maps-android-app/google-maps-polyline.png)</center>

**Po: Azure Maps**

W Azure Maps linie łamane są nazywane obiektami LineString lub MultiLineString. Te obiekty mogą być dodawane do źródła danych i renderowane przy użyciu warstwy liniowej. Należy zauważyć, że szerokość obrysu i tablica kreskowana "pikseli" są wyrównane z zestawem SDK sieci Web Azure Maps w tym samym wykorzystaniu tych samych wartości w obu zestawach SDK da te same wyniki.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create an array of points.
    List<Point> points = Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46));

    //Create a LineString feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(LineString.fromLngLats(points)));

    //Create a line layer and add it to the map.
    map.layers.add(new LineLayer(dataSource,
        strokeColor("red"),
        strokeWidth(4f),
        strokeDashArray(new Float[]{3f, 3f})));
});
```

<center>

![Azure Maps łamaną](media/migrate-google-maps-android-app/azure-maps-polyline.png)</center>

## <a name="adding-a-polygon"></a>Dodawanie wielokątu

Wielokąty są używane do reprezentowania obszaru na mapie. W poniższych przykładach pokazano, jak utworzyć wielokąt, który tworzy Trójkąt w oparciu o współrzędne środkowe mapy.

**Wcześniej: Google Maps**

Za pomocą usługi Google Maps można utworzyć wielokąt przy użyciu klasy `PolygonOptions` i dodać do mapy przy użyciu metody `addPolygon`. Kolory wypełnienia i obrysu można ustawić przy użyciu opcji `fillColor` i `strokeColor`, szerokość obrysu jest ustawiana za pomocą opcji `strokeWidth`.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the polygon.
    PolygonOptions polygonOptions = new PolygonOptions()
            .add(new LatLng(46, -123))
            .add(new LatLng(49, -122))
            .add(new LatLng(46, -121))
            .add(new LatLng(46, -123))  //Close the polygon.
            .fillColor(Color.argb(128, 0, 128, 0))
            .strokeColor(Color.RED)
            .strokeWidth(5f);

    //Add the polygon to the map.
    Polygon polygon = mapView.addPolygon(polygonOptions);
}
```

<center>

![Wielokąt usługi Google Maps](media/migrate-google-maps-android-app/google-maps-polygon.png)</center>

**Po: Azure Maps**

W Azure Maps obiektów Wielokąt i MultiPolygon można dodawać do źródła danych i renderowane na mapie przy użyciu warstw. Obszar wielokąta może być renderowany w warstwie wielokąta. Konspekt wielokąta może być renderowany przy użyciu warstwy linii. Należy zauważyć, że szerokość obrysu i tablica kreskowana "pikseli" są wyrównane z zestawem SDK sieci Web Azure Maps w tym samym wykorzystaniu tych samych wartości w obu zestawach SDK da te same wyniki.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create an array of point arrays to create polygon rings.
    List<List<Point>> rings = Arrays.asList(Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46),
        Point.fromLngLat(-123, 46)));

    //Create a Polygon feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Polygon.fromLngLats(rings)));

    //Add a polygon layer for rendering the polygon area.
    map.layers.add(new PolygonLayer(dataSource,
        fillColor("green"),
        fillOpacity(0.5f)));

    //Add a line layer for rendering the polygon outline.
    map.layers.add(new LineLayer(dataSource,
        strokeColor("red"),
        strokeWidth(2f)));
});
```

<center>

![Wielokąt Azure Maps](media/migrate-google-maps-android-app/azure-maps-polygon.png)</center>

## <a name="overlay-a-tile-layer"></a>Nałóż warstwę kafelków

Warstwy kafelków, znane także jako nakładki obrazów w usłudze Google Maps, umożliwiają nakładanie obrazów warstwowych, które zostały podzielone na mniejsze obrazy z sąsiadującymi obrazami, które są wyrównane do systemu fragmentów map. Jest to typowy sposób nakładania obrazów warstwowych lub bardzo dużych zestawów danych.

Poniższe przykłady nakładają warstwę kafelków radarowych z Iowa środowiska Mesonet of Iowa State University. Rozmiary kafelków są 256 pikseli.

**Wcześniej: Google Maps**

Dzięki usłudze Google Maps warstwa kafelków może zostać zastąpiona na podstawie mapy Użyj klasy `TileOverlayOptions` i dodana do mapy za pomocą metody `addTileLauer`. Aby sprawić, że kafelki są częściowo przezroczyste, opcja `transparency` jest ustawiona na 0,2 lub przezroczyste 20%.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the tile layer.
    TileOverlayOptions tileLayer = new TileOverlayOptions()
        .tileProvider(new UrlTileProvider(256, 256) {
            @Override
            public URL getTileUrl(int x, int y, int zoom) {

                try {
                    //Define the URL pattern for the tile images.
                    return new URL(String.format("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/%d/%d/%d.png", zoom, x, y));
                }catch (MalformedURLException e) {
                    throw new AssertionError(e);
                }
            }
        }).transparency(0.2f);

    //Add the tile layer to the map.
    mapView.addTileOverlay(tileLayer);
}
```

<center>

warstwa kafelków ![Google Maps](media/migrate-google-maps-android-app/google-maps-tile-layer.png)</center>

**Po: Azure Maps**

W Azure Maps do mapy można dodać warstwę kafelków w taki sam sposób jak każda inna warstwa. Sformatowany adres URL, który ma symbole zastępcze x, y i zoom; `{x}`, `{y}``{z}` odpowiednio używany do określenia warstwy, w której mają być dostępne kafelki. Warstwy kafelków w Azure Maps obsługują również `{quadkey}`, `{bbox-epsg-3857}` i `{subdomain}` symbole zastępcze. Aby warstwa kafelków została częściowo przezroczysta, używana jest wartość nieprzezroczystości 0,8. Należy zauważyć, że nieprzezroczystość i przezroczystość, chociaż podobne, używają odwróconych wartości. Aby przekonwertować między nimi, po prostu Odejmij swoją wartość od liczby.

> [!TIP]
> W Azure Maps warstwach można łatwo renderować poniżej innych warstw, w tym warstw mapy podstawowej. Często wskazane jest renderowanie warstw kafelków poniżej etykiet mapy, dzięki czemu można je łatwo odczytać. Metoda `map.layers.add` przyjmuje drugi parametr, który jest identyfikatorem warstwy, w której ma zostać wstawiona Nowa warstwa poniżej. Aby wstawić warstwę kafelków poniżej etykiet mapy, można użyć następującego kodu: `map.layers.add(myTileLayer, "labels");`

```java
mapControl.onReady(map -> {
    //Add a tile layer to the map, below the map labels.
    map.layers.add(new TileLayer(
        tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
        opacity(0.8f),
        tileSize(256)
    ), "labels");
});
```

<center>

![Azure Maps warstwy kafelków](media/migrate-google-maps-android-app/azure-maps-tile-layer.png)</center>

## <a name="show-traffic"></a>Pokaż ruch

Dane o ruchu można przemieścić zarówno na platformie Azure, jak i w usłudze Google Maps.

**Wcześniej: Google Maps**

Dzięki usłudze Google Maps dane przepływu ruchu mogą zostać zastąpione na podstawie mapy, przekazując wartość true do metody `setTrafficEnabled` mapy.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.setTrafficEnabled(true);
}
```

<center>

![ruch usługi Google Maps](media/migrate-google-maps-android-app/google-maps-traffic.png)</center>

**Po: Azure Maps**

Azure Maps oferuje kilka różnych opcji wyświetlania ruchu. Zdarzenia dotyczące ruchu, takie jak zamknięcie dróg i wypadki, mogą być wyświetlane jako ikony na mapie. Przepływ ruchu, kodowane kolorami dróg można przekroczyć na mapie, a kolory można modyfikować, aby były oparte na limicie limitów, względem normalnego oczekiwanego opóźnienia lub bezwzględnego opóźnienia. Dane zdarzenia w Azure Maps są aktualizowane co minutę i dane przepływu co dwie minuty.

```java
mapControl.onReady(map -> {
    map.setTraffic(
        incidents(true),
        flow(TrafficFlow.RELATIVE));
});
```

<center>

![Azure Maps ruch](media/migrate-google-maps-android-app/azure-maps-traffic.png)</center>

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat Android SDK Azure Maps.

> [!div class="nextstepaction"]
> [Jak używać kontrolki mapy systemu Android](how-to-use-android-map-control-library.md)
