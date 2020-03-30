---
title: Dodawanie warstwy kafelków do map systemu Android | Mapy platformy Microsoft Azure
description: W tym artykule dowiesz się, jak renderować warstwę kafli na mapie przy użyciu sdk Microsoft Azure Maps Android.
author: philmea
ms.author: philmea
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: f98598bd1307bb1b46ff23814780c5f809b9ac90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335559"
---
# <a name="add-a-tile-layer-to-a-map-using-the-azure-maps-android-sdk"></a>Dodawanie warstwy kafelków do mapy przy użyciu zestawu SDK usługi Azure Maps dla systemu Android

W tym artykule pokazano, jak renderować warstwę kafli na mapie przy użyciu sdk systemu Android usługi Azure Maps. Warstwy kafelków umożliwiają nakładanie obrazów na kafelki map bazowych usługi Azure Maps. Więcej informacji na temat systemu kafli usługi Azure Maps można znaleźć w dokumentacji [poziomów powiększenia i siatki kafli.](zoom-levels-and-tile-grid.md)

Warstwa kafli jest ładowana w kafelkach z serwera. Te obrazy mogą być wstępnie renderowane i przechowywane jak każdy inny obraz na serwerze, przy użyciu konwencji nazewnictwa, że warstwa kafli rozumie. Lub te obrazy mogą być renderowane za pomocą usługi dynamicznej, która generuje obrazy w czasie zbliżonym do rzeczywistego. Istnieją trzy różne konwencje nazewnictwa usługi kafelków obsługiwane przez klasę Azure Maps TileLayer:

* X, Y, Powiększenie notacji - Na podstawie poziomu powiększenia, x jest kolumną i y jest położeniem wiersza kafelka w siatce kafelków.
* Notacja quadkey - Kombinacja x, y, powiększenie informacji do pojedynczej wartości ciągu, która jest unikatowym identyfikatorem kafelka.
* Obwiednia — współrzędne obwiedni mogą `{west},{south},{east},{north}` służyć do określania obrazu w formacie, który jest powszechnie używany przez [usługi mapowania sieci Web (WMS).](https://www.opengeospatial.org/standards/wms)

> [!TIP]
> A TileLayer to świetny sposób wizualizacji dużych zestawów danych na mapie. Warstwa kafli może być nie tylko generowana z obrazu, ale także dane wektorowe mogą być renderowane jako warstwa kafli. Renderowanie danych wektorowych jako warstwy kafelków, formant mapy musi tylko załadować kafelki, które mogą być znacznie mniejsze w rozmiarze pliku niż dane wektorowe, które reprezentują. Ta technika jest używana przez wielu, którzy muszą renderować miliony wierszy danych na mapie.

Adres URL kafelka przekazany do warstwy kafelków musi być adresem URL http/https do zasobu TileJSON lub szablonem adresu URL kafelka, który używa następujących parametrów: 

* `{x}`- X położenie płytki. Również `{y}` potrzebuje `{z}`i .
* `{y}`- Pozycja Y płytki. Również `{x}` potrzebuje `{z}`i .
* `{z}`- Poziom powiększenia płytki. Również `{x}` potrzebuje `{y}`i .
* `{quadkey}`- Identyfikator poczwórki kafelków oparty na konwencji nazewnictwa systemu kafli Bing Maps.
* `{bbox-epsg-3857}`- Ciąg obwiedni z `{west},{south},{east},{north}` formatem w systemie odniesienia przestrzennego EPSG 3857.
* `{subdomain}`- Symbol zastępczy dla wartości poddomeny, jeśli określono wartość poddomeny.

## <a name="prerequisites"></a>Wymagania wstępne

Aby zakończyć proces w tym artykule, należy zainstalować [zestaw SDK systemu Azure Maps dla systemu Android,](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) aby załadować mapę.


## <a name="add-a-tile-layer-to-the-map"></a>Dodawanie warstwy kafelków do mapy

 W tym przykładzie pokazano, jak utworzyć warstwę kafli, która wskazuje zestaw kafelków. Te kafelki używają systemu kafli "x, y, zoom". Źródłem tej warstwy płytek jest nakładka radaru pogodowego z [Iowa Environmental Mesonet z Iowa State University.](https://mesonet.agron.iastate.edu/ogc/) 

Warstwę kafelków można dodać do mapy, wykonując poniższe czynności.

1. Edytuj **układ > res > activity_main.xml,** aby wyglądał jak poniższy:

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
            app:mapcontrol_centerLat="40.75"
            app:mapcontrol_centerLng="-99.47"
            app:mapcontrol_zoom="3"
            />
    
    </FrameLayout>
    ```

2. Skopiuj poniższy fragment kodu do metody **onCreate()** danej `MainActivity.java` klasy.

    ```Java
    mapControl.onReady(map -> {
        //Add a tile layer to the map, below the map labels.
        map.layers.add(new TileLayer(
            tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
            opacity(0.8f),
            tileSize(256)
        ), "labels");
    });
    ```
    
    Fragment kodu powyżej najpierw uzyskuje wystąpienie kontroli mapy usługi Azure Maps przy użyciu **onReady()** metody wywołania zwrotnego. Następnie tworzy `TileLayer` obiekt i przekazuje sformatowany adres URL `tileUrl` kafelka **xyz** do opcji. Krycie warstwy jest ustawione `0.8` na i ponieważ kafelki z usługi kafelków są 256 pikseli kafelków, informacje te są przekazywane do `tileSize` opcji. Warstwa kafli jest następnie przekazywana do menedżera warstw map.

    Po dodaniu fragmentu kodu powyżej, `MainActivity.java` powinien wyglądać jak poniżej:
    
    ```Java
    package com.example.myapplication;

    import android.app.Activity;
    import android.os.Bundle;
    import android.support.v7.app.AppCompatActivity;
    import com.microsoft.azure.maps.mapcontrol.layer.TileLayer;
    import java.util.Arrays;
    import java.util.List;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import static com.microsoft.azure.maps.mapcontrol.options.TileLayerOptions.tileSize;
    import static com.microsoft.azure.maps.mapcontrol.options.TileLayerOptions.tileUrl;
        
    public class MainActivity extends AppCompatActivity {
    
        static{
            AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
        }
    
        MapControl mapControl;
        @Override
        protected void onCreate(Bundle savedInstanceState) {
    
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            mapControl = findViewById(R.id.mapcontrol);
    
            mapControl.onCreate(savedInstanceState);
    
            mapControl.onReady(map -> {

                //Add a tile layer to the map, below the map labels.
                map.layers.add(new TileLayer(
                    tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
                    opacity(0.8f),
                    tileSize(256)
                ), "labels");
            });    
        }
    
        @Override
        public void onResume() {
            super.onResume();
            mapControl.onResume();
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

Jeśli uruchomisz aplikację teraz, powinieneś zobaczyć linię na mapie, jak widać poniżej:

<center>

![Linia mapy Androida](./media/how-to-add-tile-layer-android-map/xyz-tile-layer-android.png)</center>

## <a name="next-steps"></a>Następne kroki

Zobacz poniższy artykuł, aby dowiedzieć się więcej o sposobach ustawiania stylów mapy

> [!div class="nextstepaction"]
> [Zmienianie stylów map na mapach systemu Android](https://docs.microsoft.com/azure/azure-maps/set-android-map-styles)