---
title: Dodawanie warstwy symboli do map systemu Android | Mapy platformy Microsoft Azure
description: W tym artykule dowiesz się, jak renderować dane punktowe na mapie, dodając do niej warstwę symboli przy użyciu sdk Microsoft Azure Maps android.
author: philmea
ms.author: philmea
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 0eca73b5acae715283c05125181e12729ed5a772
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335587"
---
# <a name="add-a-symbol-layer-to-a-map-using-azure-maps-android-sdk"></a>Dodawanie warstwy symboli do mapy przy użyciu zestawu SDK usługi Azure Maps dla systemu Android

W tym artykule pokazano, jak renderować dane punktowe ze źródła danych jako warstwę symboli na mapie przy użyciu sdk systemu Azure Maps w systemie Android.

## <a name="prerequisites"></a>Wymagania wstępne

Aby całkowicie wykonać kroki opisane w tym artykule, należy zainstalować [zestaw SDK usługi Azure Maps dla systemu Android,](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) aby załadować mapę.

## <a name="add-a-symbol-layer"></a>Dodawanie warstwy symboli

Aby dodać znacznik na mapie za pomocą warstwy symboli, wykonaj poniższe czynności:

1. Edytuj**układ** >  **res** > **activity_main.xml,** aby wyglądał jak następujący kod XML:
    
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
            app:mapcontrol_centerLat="47.64"
            app:mapcontrol_centerLng="-122.33"
            app:mapcontrol_zoom="12"
            />

    </FrameLayout>
    ```

2. Skopiuj poniższy fragment kodu do metody **onCreate()** danej `MainActivity.java` klasy.

    ```Java
    mapControl.onReady(map -> {
    
        //Create a data source and add it to the map.
        DataSource dataSource = new DataSource();
        map.sources.add(dataSource);
    
        //Create a point feature and add it to the data source.
        dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));
    
        //Add a custom image icon to the map resources.
        map.images.add("my-icon", R.drawable.mapcontrol_marker_red);
    
        //Create a symbol layer and add it to the map.
        map.layers.add(new SymbolLayer(dataSource,
            iconImage("my-icon")));
        });
    
    ```
    
    Fragment kodu powyżej najpierw uzyskuje wystąpienie kontroli mapy usługi Azure Maps przy użyciu **onReady()** metody wywołania zwrotnego. Następnie tworzy obiekt źródła danych przy użyciu **Klasy DataSource** i dodaje go do mapy. Następnie dodaje **do niego operację** zawierającą geometrię punktu. Czerwony obraz znacznika jest następnie ustawiany jako ikona symbolu. **Warstwa symboli** używa tekstu lub ikon do renderowania danych opartych na punktach zawiniętych w źródle danych jako symbolu na mapie. Następnie tworzona jest warstwa symboli, a źródło danych jest przekazywane do niej w celu renderowania, a następnie dodawane do warstw mapy.
    
    Po dodaniu fragmentu kodu powyżej, `MainActivity.java` powinien wyglądać jak poniżej:
    
    ```Java
    package com.example.myapplication;
    
    import android.app.Activity;
    import android.os.Bundle;
    import com.mapbox.geojson.Feature;
    import com.mapbox.geojson.Point;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;
    import static com.microsoft.azure.maps.mapcontrol.options.SymbolLayerOptions.iconImage;
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
    
                //Create a data source and add it to the map.
                DataSource dataSource = new DataSource();
                map.sources.add(dataSource);
            
                //Create a point feature and add it to the data source.
                dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));
            
                //Add a custom image icon to the map resources.
                map.images.add("my-icon", R.drawable.mapcontrol_marker_red);
            
                //Create a symbol layer and add it to the map.
                map.layers.add(new SymbolLayer(dataSource,
                    iconImage("my-icon")));
            });
        }
    
        @Override
        public void onStart() {
            super.onStart();
            mapControl.onStart();
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
    
W tym momencie po uruchomieniu aplikacji powinien zostać wyświetlony znacznik na mapie, jak pokazano poniżej:

<center>

![Pin mapy Androida](./media/how-to-add-symbol-to-android-map/android-map-pin.png)</center>


## <a name="next-steps"></a>Następne kroki

Aby dodać więcej rzeczy do mapy, zobacz:

> [!div class="nextstepaction"]
> [Dodawanie kształtów do mapy systemu Android](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)

> [!div class="nextstepaction"]
> [Wyświetlanie informacji o funkcjach](display-feature-information-android.md)