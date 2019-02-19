---
title: Jak używać kontrolki mapy dla systemu Android w usługi Azure Maps | Dokumentacja firmy Microsoft
description: Użyj kontrolki mapy dla systemu Android w usłudze Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 57cc585d621c71872a4b7658c74f581c8998b245
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2019
ms.locfileid: "56341083"
---
# <a name="how-to-use-azure-maps-android-sdk"></a>Jak używać usługi Azure Maps Android SDK

Azure mapuje zestawu Android SDK jest biblioteką map wektorowych dla systemu Android. Ten artykuł przeprowadzi Cię przez proces Instalowanie zestawu Azure SDK dla systemu Android map, trwa ładowanie mapy i wprowadzania numeru pin na nim.

## <a name="prerequisites-to-get-started"></a>Wymagania wstępne, aby rozpocząć pracę

### <a name="create-an-azure-maps-account"></a>Tworzenie konta usługi Azure Maps 

Aby wykonać kroki opisane w tym przewodniku, należy najpierw zobacz [Zarządzanie kontem i kluczami](how-to-manage-account-keys.md) do tworzenia i zarządzania subskrypcją konta S1 warstwy cenowej.

### <a name="download-android-studio"></a>Pobierz program Android studio

Możesz pobrać [Android Studio](https://developer.android.com/studio/) bezpłatnie od firmy Google. Aby zainstalować zestaw Azure SDK dla systemu Android mapy, musisz najpierw Pobierz program Android Studio i Utwórz projekt za pomocą pustego działania.

## <a name="create-a-project-in-android-studio"></a>Utwórz projekt w programie Android Studio

Należy utworzyć nowy projekt za pomocą pustego działania. Wykonaj poniższe kroki, aby utworzyć nowy projekt programu Android Studio:

1. W obszarze *wybierz projekt*, sprawdź "Tablet i telefon" jako współczynnika postaci, która aplikacja jest uruchamiana na.
2. Kliknij przycisk *puste działanie* w standardzie, a następnie kliknij przycisk **dalej**.
3. W obszarze *Skonfiguruj projekt*, wybierz opcję `API 21: Android 5.0.0 (Lollipop)` jako minimalny zestaw SDK. Jest to najniższa wersja obsługiwana przez usługi Azure Maps Android SDK.
4. Zaakceptuj wartość domyślną `Activity Name` i `Layout Name` i kliknij przycisk **Zakończ**

Zobacz [dokumentacji programu Android Studio](https://developer.android.com/studio/intro/) więcej pomocy przy instalowaniu programu Android Studio i tworzenia nowego projektu.

![Utwórz nowy projekt](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>Konfigurowanie urządzenia wirtualnego

Program android Studio umożliwia skonfigurowanie urządzenia wirtualnego systemu Android na tym komputerze. Co może pomóc w testowania aplikacji, podczas tworzenia. Do skonfigurowania urządzenia wirtualnego kliknij ikonę Android Virtual Device (AVD) Manager w górnym rogu ekranu projektu. Następnie kliknij przycisk **tworzenie urządzenia wirtualnego** przycisku. Można również uzyskać z menedżerem za pośrednictwem **Narzędzia > Android > Menedżera AVD** na pasku narzędzi. Z **telefony** kategorii, wybierz opcję **Nexus 5 X** i kliknij przycisk **dalej**.

Dowiedz się więcej o konfigurowaniu AVD w [dokumentacji programu Android Studio](https://developer.android.com/studio/run/managing-avds).

![Emulator systemu android](./media/how-to-use-android-map-control-library/android-emulator.png)

## <a name="install-azure-maps-android-sdk"></a>Zainstaluj zestaw SDK systemu Android z usługi Azure Maps

Przed przejściem do przodu do tworzenia aplikacji, wykonaj poniższe kroki, aby zainstalować zestaw SDK systemu Android mapy platformy Azure. 

1. Dodaj następujące polecenie, aby **wszystkie projekty**, bloku repozytoriów usługi **build.gradle** pliku.

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Aktualizacja usługi **app/build.gradle** i Dodaj następujący kod do jego:

    1. Dodaj następujący kod do bloku dla systemu Android:

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    2. Zaktualizuj swoje bloku zależności i Dodaj następujący kod do jego:

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.1"
        ```

3. Skonfiguruj uprawnienia, dodając następujące polecenie, aby Twoje **pliku AndroidManifest.xml**

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <manifest>
        ...
        <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
        ...
    </manifest>
    ```

4. Edytuj **res > Układ > activity_main.xml**, więc wygląda jak poniższy kod XML:
    
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
            app:mapcontrol_cameraTargetLat="47.64"
            app:mapcontrol_cameraTargetLng="-122.33"
            app:mapcontrol_cameraZoom="12"
            />

    </FrameLayout>
    ```

5. Edytuj **MainActivity.java** do utworzenia klasy działania widoku mapy. Po zakończeniu edycji to wyglądać klasy poniżej:

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
            AzureMaps.setSubscriptionKey("{subscription-key}");
        }

        MapControl mapControl;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            mapControl = findViewById(R.id.mapcontrol);

            mapControl.onCreate(savedInstanceState);

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

## <a name="import-classes"></a>Importowanie klas

Po wykonaniu powyższych czynności, najprawdopodobniej wystąpi ostrzeżenia z systemem Android Studio na fragment tekstu w kodzie. Aby usunąć te ostrzeżenia, należy zaimportować klasy, do którego odwołuje się `MainActivity.java`.

Te klasy mógł automatycznie zaimportować, naciskając klawisz `Alt` + `Enter`(`Option` + `Return` na komputerze Mac). 

Kliknij przycisk **Run 'App'** przycisk (lub `Control` + `R` na komputerze Mac) do budowania aplikacji.

![Kliknij przycisk Uruchom](./media/how-to-use-android-map-control-library/run-app.png)

Potrwa kilka sekund dla programu android studio, aby skompilować aplikację. Zakończ po kompilacji można przetestować aplikację w urządzeniu z systemem Android emulowane. Zostanie wyświetlony mapę, tak jak poniżej.

![Mapy dla systemu android](./media/how-to-use-android-map-control-library/android-map.png)

## <a name="add-a-marker-to-the-map"></a>Dodaj znacznik do mapy

Aby dodać znacznik na mapie, Dodaj `mapView.getMapAsync()` funkcja `MainActivity.java`. Końcowe `MainActivity.java` powinien wyglądać podobnie do poniższego:

```java
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
            AzureMaps.setSubscriptionKey("{subscription-key}");
        }

    MapControl mapControl;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mapControl = findViewById(R.id.mapcontrol);

        mapControl.onCreate(savedInstanceState);

        mapControl.getMapAsync(map -> {
            DataSource dataSource = new DataSource();
            dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

            SymbolLayer symbolLayer = new SymbolLayer(dataSource);
            symbolLayer.setOptions(iconImage("my-icon"));

            map.images.add("my-icon", R.drawable.mapcontrol_marker_red);
            map.sources.add(dataSource);
            map.layers.add(symbolLayer);
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

Ponowne uruchomienie aplikacji i powinien zostać wyświetlony znacznik na mapie, tak jak poniżej.

![Przypnij mapę dla systemu android](./media/how-to-use-android-map-control-library/android-map-pin.png)