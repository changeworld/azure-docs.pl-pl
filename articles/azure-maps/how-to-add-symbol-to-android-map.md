---
title: Dodaj warstwę symbol do systemu Android mapy w usługi Azure Maps | Dokumentacja firmy Microsoft
description: Jak dodać symboli do mapy, przy użyciu zestawu SDK usługi Azure Maps systemu Android
author: walsehgal
ms.author: v-musehg
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: add6e23d023753e217c102dc946837a71a64c781
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2019
ms.locfileid: "64871080"
---
# <a name="add-a-symbol-layer-to-a-map-using-azure-maps-android-sdk"></a>Dodaj warstwę symbol do mapy, przy użyciu zestawu SDK usługi Azure Maps systemu Android

W tym artykule przedstawiono sposób renderowania punktu danych ze źródła danych jako warstwa symboli na mapie za pomocą usługi Azure Maps SDK dla systemu Android.

## <a name="prerequisites"></a>Wymagania wstępne

Aby całkowicie wykonaj kroki opisane w tym artykule, musisz zainstalować [zestawu SDK usługi Azure Maps Android](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) można załadować mapy.

## <a name="add-a-symbol-layer"></a>Dodawanie warstwy symboli

Aby dodać znacznik na mapie za pomocą warstwy symboli, wykonaj następujące czynności:

1. Edytuj **res** > **układ** > **activity_main.xml** tak wygląda jak następujący kod XML:
    
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

2. Skopiuj poniższy fragment kodu do **onCreate()** metody usługi `MainActivity.java` klasy.

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
    
    Najpierw uzyskuje powyższym fragmencie kodu usługi Azure Maps mapy formantu wystąpienia za pomocą **onReady()** metody wywołania zwrotnego. Następnie tworzy źródła danych obiektu przy użyciu **DataSource** klasy i dodaje je do mapy. Następnie dodaje **funkcji** zawierający geometrii punkt, do niego. Obraz znacznika czerwony zostanie następnie ustawiona jako ikona dla symbolu. A **warstwy symbol** używa tekstu lub ikony do renderowania oparta na punkcie danych, opakowane w źródle danych jako symbol na mapie. Warstwa symbol zostanie utworzony i źródło danych jest przekazywany do jej do renderowania, a następnie jest dodawany do warstwy mapy.
    
    Po dodaniu fragment kodu powyżej, Twoje `MainActivity.java` powinien wyglądać podobnie do poniższego:
    
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

![Przypnij mapę dla systemu android](./media/how-to-add-symbol-to-android-map/android-map-pin.png)</center>


## <a name="next-steps"></a>Kolejne kroki

Aby dodać więcej rzeczy do mapy, zobacz:

> [!div class="nextstepaction"]
> [Dodawanie kształtów do mapowania dla systemu Android](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)