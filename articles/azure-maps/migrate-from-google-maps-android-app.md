---
title: 'Samouczek: Migracja aplikacji na Androida | Mapy platformy Microsoft Azure'
description: Jak przeprowadzić migrację aplikacji na Androida z Map Google do Map Platformy Microsoft Azure.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 9514398ec6a84becd1283e4b0975804101b64086
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77209736"
---
# <a name="migrate-an-android-app-from-google-maps"></a>Migrowanie aplikacji na Androida z Map Google

Zestaw SDK systemu Android usługi Azure Maps ma interfejs interfejsu API, który jest podobny do sdk sieci Web. Jeśli zostałeś opracowany przy jednym z tych sks, wiele z tych samych pojęć, najlepszych rozwiązań i architektura mają zastosowanie.

Zestaw SDK dla systemu Azure Maps dla systemu Android obsługuje minimalną wersję interfejsu API 21 dla systemu Android: Android 5.0.0 (Lollipop).

Wszystkie przykłady są podane w języku Java; Można jednak używać kotlin z azure maps android SDK.

Aby uzyskać więcej informacji na temat tworzenia za pomocą sdk systemu Android przez usługi Azure Maps, zobacz [instrukcje dotyczące usługi Azure Maps Android SDK](how-to-use-android-map-control-library.md).

## <a name="load-a-map"></a>Ładowanie mapy

Ładowanie mapy w aplikacji na Androida przy użyciu Google lub Usługi Azure Maps składa się z podobnych kroków. W przypadku korzystania z jednego z sdk należy:

- Uzyskaj interfejs API lub klucz subskrypcji, aby uzyskać dostęp do każdej z platform.
- Dodaj niektóre XML do działania, aby określić, gdzie mapa powinna być renderowana i jak powinna być rozmieszczona.
- Zastąpokaj wszystkie metody cyklu życia z działania zawierającego widok mapy do odpowiednich metod w klasie mapy. W szczególności należy zastąpić następujące metody:
    - `onCreate(Bundle)`
    - `onStart()`
    - `onResume()`
    - `onPause()`
    - `onStop()`
    - `onDestroy()`
    - `onSaveInstanceState(Bundle)`
    - `onLowMemory()`
- Poczekaj, aż mapa będzie gotowa, zanim spróbujesz uzyskać do niej dostęp i zaprogramować ją.

**Przed: Mapy Google**

Aby wyświetlić mapę przy użyciu SDK Map Google dla Systemu Android, należy wykonać następujące czynności:

1.  Upewnij się, że usługi Google Play są zainstalowane.
2.  Dodaj zależność usługi Mapy Google do pliku **gradle.build** modułu: 

    `implementation 'com.google.android.gms:play-services-maps:17.0.0'`

1.  Dodaj klucz interfejsu API Map Google w sekcji aplikacji pliku **api.xml\_map\_google:**
    
    ```xml
    <meta-data android:name="com.google.android.geo.API_KEY" android:value="YOUR_GOOGLE_MAPS_KEY"/>
    ```

1.  Dodaj fragment mapy do głównego działania:

    ```xml
    <com.google.android.gms.maps.MapView
            android:id="@+id/myMap"
            android:layout_width="match_parent"
            android:layout_height="match_parent"/>
    ```

1.  W pliku **MainActivity.java** musisz zaimportować sdk Map Google. Prześlij dalej wszystkie metody cyklu życia z działania zawierającego widok mapy do odpowiednich metod w klasie mapy. Pobierz `MapView` wystąpienie z fragmentu `getMapAsync(OnMapReadyCallback)` mapy przy użyciu metody. Automatycznie `MapView` inicjuje system map i widok. Edytuj plik **MainActivity.java** w następujący sposób:

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

Po uruchomieniu aplikacji formant mapy ładuje się jak na poniższej ilustracji.

<center>

![Proste Mapy Google](media/migrate-google-maps-android-app/simple-google-maps.png)</center>

**Po: Mapy platformy Azure**

Aby wyświetlić mapę przy użyciu zestawu SDK usługi Azure Maps dla systemu Android, należy wykonać następujące kroki:

1. Otwórz plik **build.gradle** najwyższego poziomu i dodaj następujący kod do sekcji **wszystkich bloków projektów:**

    ```JAVA
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Zaktualizuj **aplikację/build.gradle** i dodaj do niej następujący kod:
    
    1. Upewnij się, że **minSdkVersion** projektu jest w interfejsie API 21 lub nowszym.

    2. Dodaj następujący kod do sekcji Android:

        ```java
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    3. Zaktualizuj blok zależności. Dodaj nową linię zależności implementacji dla najnowszego sdk usługi Azure Maps dla systemu Android:

        ```java
        implementation "com.microsoft.azure.maps:mapcontrol:0.2"
        ```

        > [!Note]
        > Zestaw SDK systemu Android usługi Azure Maps jest regularnie uaktualniany i udoskonalany. Możesz zobaczyć [kontrolki Wprowadzenie do mapy systemu Android,](how-to-use-android-map-control-library.md) aby uzyskać najnowszy numer wersji usługi Azure Maps. Ponadto można ustawić numer wersji z "0.2" na "0+", aby kod zawsze wskazywał najnowszą wersję.
    
    4. Przejdź do **pliku** na pasku narzędzi, a następnie kliknij **przycisk Synchronizuj projekt z plikami gradle**.
3. Dodaj fragment mapy do działania \> głównego \> \_(działanie układu zasobów main.xml):
    
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

4. W pliku **MainActivity.java** musisz:
    
    * Importuje zestaw SDK usługi Azure Maps
    * Ustawianie informacji uwierzytelniających usługi Azure Maps
    * Pobierz wystąpienie formantu mapy w **onCreate** metody

     Ustaw informacje uwierzytelniania `AzureMaps` w `setSubscriptionKey` klasie `setAadProperties` przy użyciu lub metody. Ta globalna aktualizacja, upewnij się, że dodasz informacje uwierzytelniania do każdego widoku.

    Formant mapy zawiera własne metody cyklu życia do zarządzania cyklem życia OpenGL systemu Android. Metody te muszą być wywoływane bezpośrednio z zawartej aktywności. Aby poprawnie wywołać metody cyklu życia formantu mapy, należy zastąpić następujące metody cyklu życia w działania, który zawiera formant mapy. Wywołanie odpowiedniej metody sterowania mapą.

    * `onCreate(Bundle)` 
    * `onStart()` 
    * `onResume()` 
    * `onPause()` 
    * `onStop()` 
    * `onDestroy()` 
    * `onSaveInstanceState(Bundle)` 
    * `onLowMemory()`

    Edytuj plik **MainActivity.java** w następujący sposób:
    
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

Po uruchomieniu aplikacji formant mapy zostanie załadowany, jak na poniższej ilustracji.

<center>

![Proste mapy platformy Azure](media/migrate-google-maps-android-app/simple-azure-maps.png)</center>

Należy zauważyć, że kontrolka usługi Azure Maps obsługuje pomniejszanie więcej i zapewnia więcej widoku świata.

> [!TIP]
> Jeśli używasz emulatora systemu Android na komputerze z systemem Windows, mapa może nie być renderowana z powodu konfliktów z OpenGL i programowym renderowaniem grafiki. Dla niektórych osób rozwiązanie tego problemu zostało rozwiązane przez następujące osoby. Otwórz Menedżera AVD i wybierz urządzenie wirtualne do edycji. Przewiń w dół w panelu **Weryfikuj konfigurację.** W sekcji **Emulowana wydajność** ustaw opcję **Grafika** na **Sprzęt**.

## <a name="localizing-the-map"></a>Lokalizowanie mapy

Lokalizacja jest ważna, jeśli odbiorcy są rozmieszczeń w wielu krajach lub mówią różnymi językami.

**Przed: Mapy Google**

Dodaj następujący kod `onCreate` do metody, aby ustawić język mapy. Kod należy dodać przed ustawieniem widoku kontekstowego mapy. Kod języka "fr" ogranicza język do francuskiego.

```java
String languageToLoad = "fr";
Locale locale = new Locale(languageToLoad);
Locale.setDefault(locale);
Configuration config = new Configuration();
config.locale = locale;
getBaseContext().getResources().updateConfiguration(config,
        getBaseContext().getResources().getDisplayMetrics());
```

Oto przykład Map Google z językiem ustawionym na "fr".

<center>

![Lokalizacja w Mapach Google](media/migrate-google-maps-android-app/google-maps-localization.png)</center>

**Po: Mapy platformy Azure**

Usługa Azure Maps udostępnia trzy różne sposoby ustawiania języka i widoku regionalnego mapy. Pierwszą opcją jest przekazanie informacji o języku `AzureMaps` i widoku regionalnym do klasy. Ta opcja używa `setLanguage` statycznych `setView` i metod globalnie. Co oznacza, że domyślny język i widok regionalny są ustawiane we wszystkich formantach usługi Azure Maps załadowanych w aplikacji. W tym przykładzie ustawia francuski przy użyciu kodu języka "fr-FR".

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

Drugą opcją jest przekazanie języka i wyświetlenie informacji do kodu XML formantu mapy.

```xml
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="auto"
    />
```

Trzecią opcją jest zaprogramowanie widoku mapy języka `setStyle` i regionu przy użyciu metody map. Ta opcja aktualizuje język i widok regionalny w dowolnym momencie wykonania kodu.

```java
mapControl.onReady(map -> {
    map.setStyle(StyleOptions.language("fr-FR"));
    map.setStyle(StyleOptions.view("auto"));
});
```

Oto przykład usługi Azure Maps z językiem ustawionym na "fr-FR".

<center>

![Lokalizacja usługi Azure Maps](media/migrate-google-maps-android-app/azure-maps-localization.png)</center>

Przejrzyj pełną listę [obsługiwanych języków](supported-languages.md).

## <a name="setting-the-map-view"></a>Ustawianie widoku mapy

Mapy dynamiczne w mapach Platformy Azure i Map Google można programowo przenosić do nowych lokalizacji geograficznych, wywołując odpowiednie metody. Sprawmy, aby mapa wyświetlała satelitarne zdjęcia lotnicze, wyśrodkować mapę nad lokalizacją ze współrzędnymi i zmieniać poziom powiększenia. W tym przykładzie użyjemy szerokości geograficznej: 35.0272, długość geograficzna: -111.0225 i poziom powiększenia 15.

**Przed: Mapy Google**

Kamera sterowania mapami Google może być programowo przesuwana za pomocą tej `moveCamera` metody. Metoda `moveCamera` umożliwia określenie środka mapy i poziomu powiększenia. Metoda `setMapType` zmienia typ mapy do wyświetlenia.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.moveCamera(CameraUpdateFactory.newLatLngZoom(new LatLng(35.0272, -111.0225), 15));
    mapView.setMapType(GoogleMap.MAP_TYPE_SATELLITE);
}
```

<center>

![Widok zestawu Map Google](media/migrate-google-maps-android-app/google-maps-set-view.png)</center>

> [!NOTE]
> Mapy Google używają kafelków o wymiarach 256 pikseli, podczas gdy usługa Azure Maps używa większego kafelka o przekątnych 512 pikseli. Zmniejsza to liczbę żądań sieciowych potrzebnych w usłudze Azure Maps do załadowania tego samego obszaru mapy co Mapy Google. Aby osiągnąć ten sam widoczny obszar co mapa w Mapach Google, musisz odjąć poziom powiększenia używany w Mapach Google o jeden podczas korzystania z usługi Azure Maps. 

**Po: Mapy platformy Azure**

Jak wspomniano wcześniej, aby osiągnąć ten sam widoczny obszar w usłudze Azure Maps, odejmij po jednym poziomie powiększenia używany w Mapach Google. W takim przypadku należy użyć poziomu powiększenia równego 14.

Początkowy widok mapy można ustawić w atrybutach XML na formancie mapy.

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

Widok mapy można zaprogramować za `setCamera` `setStyle` pomocą map i metod.

```java
mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(35.0272, -111.0225), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

<center>

![Widok zestawu usługi Azure Maps](media/migrate-google-maps-android-app/azure-maps-set-view.png)</center>

**Dodatkowe zasoby:**

- [Obsługiwane style map](supported-map-styles.md)

## <a name="adding-a-marker"></a>Dodawanie znacznika

Dane punktowe są często renderowane przy użyciu obrazu na mapie. Obrazy te są określane jako znaczniki, pinezki, pinezki lub symbole. Poniższe przykłady renderowania danych punktowych jako znaczników na mapie na szerokości geograficznej: 51,5, długość geograficzna: -0,2.

**Przed: Mapy Google**

W Mapach Google znaczniki są `addMarker` dodawane przy użyciu metody map.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33)));
}
```

<center>

![Znacznik Map Google](media/migrate-google-maps-android-app/google-maps-marker.png)</center>

**Po: Mapy platformy Azure**

W usłudze Azure Maps renderuj dane punktów na mapie, najpierw dodając dane do źródła danych. Następnie należy połączyć to źródło danych z warstwą symboli. Źródło danych optymalizuje zarządzanie danymi przestrzennymi w formancie mapy. Warstwa symboli określa sposób renderowania danych punktowych jako obrazu lub tekstu.

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

![Znacznik Usługi Azure Maps](media/migrate-google-maps-android-app/azure-maps-marker.png)</center>

## <a name="adding-a-custom-marker"></a>Dodawanie znacznika niestandardowego

Obrazy niestandardowe mogą być używane do reprezentowania punktów na mapie. Mapa w poniższych przykładach używa niestandardowego obrazu do wyświetlania punktu na mapie. Punkt jest na szerokości geograficznej: 51,5 i długości geograficznej: -0,2. Zakotwiczenie przesuwa położenie znacznika, tak aby punkt ikony pineki był wyrównany z właściwą pozycją na mapie.

<center>

![żółty pinez obraz](media/migrate-google-maps-web-app/ylw_pushpin.png)<br/>
ylw\_pushpin.png</center>

W obu przykładach powyższy obraz jest dodawany do folderu drawable zasobów aplikacji.

**Przed: Mapy Google**

W Mapach Google niestandardowe obrazy mogą być używane do znaczników. Załaduj obrazy `icon` niestandardowe za pomocą opcji znacznika. Aby wyrównać punkt obrazu do współrzędnej, użyj `anchor` tej opcji. Zakotwiczenie jest względem wymiarów obrazu. W tym przypadku kotwica ma 0,2 jednostki szerokości i 1 jednostkę wysokości.

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

![Niestandardowy znacznik Map Google](media/migrate-google-maps-android-app/google-maps-custom-marker.png)</center>

**Po: Mapy platformy Azure**

Warstwy symboli w usłudze Azure Maps obsługują obrazy niestandardowe, ale najpierw obraz musi zostać załadowany do zasobów mapy i przypisany unikatowy identyfikator. Następnie warstwa symbolu musi odwoływać się do tego identyfikatora. Odsuń symbol, aby wyrównać go `iconOffset` do prawidłowego punktu obrazu za pomocą tej opcji. Przesunięcie ikony jest w pikselach. Domyślnie przesunięcie jest względem dolnego środka obrazu, ale tę wartość odsunięcia można regulować za pomocą `iconAnchor` tej opcji. W tym `iconAnchor` przykładzie `"center"`ustawia opcję . Używa przesunięcia ikony, aby przenieść obraz pięć pikseli w prawo i 15 pikseli w górę, aby wyrównać z punktem obrazu pinecy.

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

![Znacznik niestandardowy usługi Azure Maps](media/migrate-google-maps-android-app/azure-maps-custom-marker.png)</center>

## <a name="adding-a-polyline"></a>Dodawanie polilinii

Polilinie są używane do reprezentowania linii lub ścieżki na mapie. Poniższe przykłady pokazują, jak utworzyć polilinię przerywaną na mapie.

**Przed: Mapy Google**

W Mapach Google renderuj `PolylineOptions` polilinię przy użyciu tej klasy. Dodaj polilinię do mapy `addPolyline` za pomocą metody. Ustaw kolor obrysu `color` za pomocą tej opcji. Ustaw szerokość obrysu za `width` pomocą tej opcji. Dodaj tablicę kreski `pattern` obrysowej za pomocą tej opcji.

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

![Polilinia Map Google](media/migrate-google-maps-android-app/google-maps-polyline.png)</center>

**Po: Mapy platformy Azure**

W usłudze Azure Maps polilinie są wywoływane `LineString` lub `MultiLineString` obiekty. Dodaj te obiekty do źródła danych i renderuj je za pomocą warstwy liniowej. Ustaw szerokość obrysu za `strokeWidth` pomocą tej opcji. Dodaj tablicę kreski `strokeDashArray` obrysowej za pomocą tej opcji.

Szerokość obrysu i tablica kreski "piksel" jednostek w usłudze Azure Maps Web SDK, jest taka sama jak w usłudze Mapy Google. Obie akceptują te same wartości, aby uzyskać takie same wyniki.

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

![Polilinia usługi Azure Maps](media/migrate-google-maps-android-app/azure-maps-polyline.png)</center>

## <a name="adding-a-polygon"></a>Dodawanie wielokąta

Wielokąty są używane do reprezentowania obszaru na mapie. W następnych przykładach pokazano, jak utworzyć wielokąt. Ten wielokąt tworzy trójkąt oparty na środkowej współrzędnej mapy.

**Przed: Mapy Google**

W Mapach Google renderuj `PolygonOptions` wielokąt przy użyciu klasy. Dodaj wielokąt do mapy `addPolygon` za pomocą metody. Ustaw kolory wypełnienia i `fillColor` obrysu, korzystając odpowiednio z opcji i. `strokeColor` Ustaw szerokość obrysu za `strokeWidth` pomocą tej opcji.

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

![Wielokąt Map Google](media/migrate-google-maps-android-app/google-maps-polygon.png)</center>

**Po: Mapy platformy Azure**

W usłudze `Polygon` Azure `MultiPolygon` Maps dodaj i obiekty do źródła danych i renderuj je na mapie przy użyciu warstw. Renderowanie obszaru wielokąta w warstwie wielokąta. Renderowanie konturu wielokąta przy użyciu warstwy liniowej. Ustaw kolor i szerokość `strokeColor` obrysu za pomocą opcji i `strokeWidth` opcji.

Jednostki "piksela" szerokości obrysu i tablicy kreskowej w usłudze Azure Maps Web SDK są zgodne z odpowiednimi jednostkami w Mapach Google. Oba akceptują te same wartości i dają takie same wyniki.

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

![Wielokąt usługi Azure Maps](media/migrate-google-maps-android-app/azure-maps-polygon.png)</center>

## <a name="overlay-a-tile-layer"></a>Nakładanie warstwy kafli

 Warstwy kafli służą do nakładania obrazów warstw podzielonych na mniejsze obrazy sąsiadująco, które są zgodne z systemem kafli. Takie podejście jest typowym sposobem nakładania obrazów warstw lub dużych zestawów danych. Warstwy kafli są nazywane nakładkimi obrazów w Mapach Google.

Poniższe przykłady nakładają warstwę kafli radaru pogodowego z Iowa Environmental Mesonet z Iowa State University. Rozmiar kafelków wynosi 256 pikseli.

**Przed: Mapy Google**

Dzięki Mapom Google warstwa kafelków może być nałożone na mapę. Użyj `TileOverlayOptions` klasy. Dodaj warstwę kafli do `addTileLauer` mapy za pomocą metody. Aby płytki były półprzezroczyste, `transparency` opcja jest ustawiona na 0,2 lub 20% przezroczyste.

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

![Warstwa kafelków Map Google](media/migrate-google-maps-android-app/google-maps-tile-layer.png)</center>

**Po: Mapy platformy Azure**

Warstwę kafli można dodać do mapy w podobny sposób jak każda inna warstwa. Sformatowany adres URL zawierający symbole zastępcze x, y i zoom; `{x}`, `{y}` `{z}` , służy odpowiednio do informuj warstwę, gdzie mają być dostępne płytki. Ponadto warstwy kafli w `{quadkey}` `{bbox-epsg-3857}`usłudze `{subdomain}` Azure Maps obsługują i symbole zastępcze. Aby warstwa kafelków była półprzezroczysta, używana jest wartość krycia 0,8. Krycie i przezroczystość, choć podobne, używają wartości odwróconych. Aby przekonwertować obie opcje, odejmij ich wartość od numeru jeden.

> [!TIP]
> W usłudze Azure Maps wygodnie jest renderować warstwy poniżej innych warstw, w tym warstw map bazowych. Często pożądane jest również renderowanie warstw kafelków pod etykietami map, tak aby były łatwe do odczytania. Metoda `map.layers.add` przyjmuje drugi parametr, który jest identyfikatorem warstwy, w którym można wstawić nową warstwę poniżej. Aby wstawić warstwę kafli pod etykietami mapy, można użyć następującego kodu:`map.layers.add(myTileLayer, "labels");`

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

![Warstwa kafli usługi Azure Maps](media/migrate-google-maps-android-app/azure-maps-tile-layer.png)</center>

## <a name="show-traffic"></a>Wyświetlanie ruchu

Zarówno mapy Platformy Azure, jak i Mapy Google mają opcje nakładania danych o ruchu drogowym.

**Przed: Mapy Google**

Dzięki Mapom Google dane o przepływie ruchu można napełnić na mapie, przechodząc zgodnie z `setTrafficEnabled` metodą mapy.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.setTrafficEnabled(true);
}
```

<center>

![Ruch w Mapach Google](media/migrate-google-maps-android-app/google-maps-traffic.png)</center>

**Po: Mapy platformy Azure**

Usługa Azure Maps udostępnia kilka różnych opcji wyświetlania ruchu. Zdarzenia drogowe, takie jak zamknięcia dróg i wypadki, mogą być wyświetlane jako ikony na mapie. Ruch drogowy i kolorowe drogi mogą być nakładane na mapie. Kolory mogą być modyfikowane w celu wyświetlenia w stosunku do zaksięgowanego ograniczenia prędkości, w stosunku do normalnego oczekiwanego opóźnienia lub bezwzględnego opóźnienia. Dane zdarzeń w usłudze Azure Maps są aktualizowane co minutę, a dane przepływu są aktualizowane co dwie minuty.

```java
mapControl.onReady(map -> {
    map.setTraffic(
        incidents(true),
        flow(TrafficFlow.RELATIVE));
});
```

<center>

![Ruch usługi Azure Maps](media/migrate-google-maps-android-app/azure-maps-traffic.png)</center>

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o sdk usługi Azure Maps dla systemu Android.

> [!div class="nextstepaction"]
> [Jak korzystać z formantu mapy Androida](how-to-use-android-map-control-library.md)
