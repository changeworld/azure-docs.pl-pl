---
title: Jak używać kontrolki mapy dla systemu Android w usługi Azure Maps | Dokumentacja firmy Microsoft
description: Kontrolka mapy dla systemu Android w usłudze Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 15706addbe6b7f6310223978130158c792a47c89
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60770373"
---
# <a name="how-to-use-the-azure-maps-android-sdk"></a>Jak używać usługi Azure Maps SDK dla systemu Android

Zestaw Azure SDK dla systemu Android mapy jest biblioteka map wektorów dla systemu Android. Ten artykuł przeprowadzi Cię przez procesy, Instalowanie zestawu Azure SDK dla systemu Android map, trwa ładowanie mapy i wprowadzania numeru pin na mapie.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="create-an-azure-maps-account"></a>Tworzenie konta usługi Azure Maps

Aby wykonać procedury opisane w tym artykule, należy najpierw [utworzyć konto usługi Azure Maps](how-to-manage-account-keys.md) w warstwę cenową S1.

### <a name="download-android-studio"></a>Pobierz program Android Studio

Należy pobrać program Android Studio i Utwórz projekt za pomocą pustego działania, zanim będzie można zainstalować zestawu Azure SDK dla systemu Android mapy. Możesz [Pobierz program Android Studio](https://developer.android.com/studio/) bezpłatnie od firmy Google. 

## <a name="create-a-project-in-android-studio"></a>Utwórz projekt w programie Android Studio

Najpierw musisz utworzyć nowy projekt z działaniem puste. Wykonaj następujące kroki, aby utworzyć projekt programu Android Studio:

1. W obszarze **wybierz projekt**, wybierz opcję **telefony i tablety**. Aplikacja jest uruchamiana w tym współczynniku postaci.
2. Na **telefony i tablety** zaznacz **puste działanie**, a następnie wybierz pozycję **dalej**.
3. W obszarze **Skonfiguruj projekt**, wybierz opcję `API 21: Android 5.0.0 (Lollipop)` jako minimalny zestaw SDK. Jest to najstarszą wersję obsługiwane przez usługi Azure Maps SDK dla systemu Android.
4. Zaakceptuj wartość domyślną `Activity Name` i `Layout Name` i wybierz **Zakończ**.

Zobacz [dokumentacji programu Android Studio](https://developer.android.com/studio/intro/) Aby uzyskać więcej pomocy instalacji programu Android Studio i tworzenia nowego projektu.

![Tworzenie projektu](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>Konfigurowanie urządzenia wirtualnego

Program android Studio umożliwia skonfigurowanie urządzenia wirtualnego systemu Android na tym komputerze. Ten sposób może pomóc w testowania aplikacji podczas programowania. Aby skonfigurować urządzenie wirtualne, wybierz ikonę Manager dla systemu Android Virtual Device (AVD) w prawym górnym rogu ekranu projektu, a następnie wybierz **tworzenie urządzenia wirtualnego**. Można również uzyskać, wybierając Menedżera AVD **narzędzia** > **Android** > **Menedżera AVD** z paska narzędzi. W **telefony** kategorii, wybierz opcję **Nexus 5 X**, a następnie wybierz pozycję **dalej**.

Dowiedz się więcej o konfigurowaniu AVD w [dokumentacji programu Android Studio](https://developer.android.com/studio/run/managing-avds).

![Emulator systemu android](./media/how-to-use-android-map-control-library/android-emulator.png)

## <a name="install-the-azure-maps-android-sdk"></a>Instalowanie zestawu Android SDK usługi Azure Maps

Następnym krokiem w tworzeniu aplikacji jest Zainstaluj zestaw Azure SDK dla systemu Android mapy. Wykonaj następujące kroki, aby zainstalować zestaw SDK:

1. Dodaj następujący kod do **wszystkie projekty**, **repozytoriów** blok w swojej **build.gradle** pliku.

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Aktualizacja usługi **app/build.gradle** i Dodaj do niej następujący kod:

    1. Dodaj następujący kod do bloku dla systemu Android:

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    2. Zaktualizuj swoje bloku zależności i Dodaj do niej następujący kod:

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.1"
        ```

3. Skonfiguruj uprawnienia, dodając następujący kod XML do Twojej **AndroidManifest.xml** pliku:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <manifest>
        ...
        <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
        ...
    </manifest>
    ```

4. Edytuj **res** > **układ** > **activity_main.xml** tak wygląda jak poniższy kod XML:
    
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

5. Edytuj **MainActivity.java** do utworzenia klasy działania widoku mapy. Po zakończeniu edycji, powinien on wyglądać podobnie do tej klasy:

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

Po wykonaniu powyższych kroków, prawdopodobnie otrzymasz ostrzeżeń z programu Android Studio o część kodu. Aby usunąć te ostrzeżenia, należy zaimportować klasy, do którego odwołuje się `MainActivity.java`.

Te klasy można automatycznie zaimportować, wybierając klawisze Alt + Enter (Option + Return na komputerze Mac).

Kliknij przycisk Uruchom, jak pokazano w poniższej grafiki (lub klawisz formantu + R na komputerze Mac), do budowania aplikacji.

![Kliknij przycisk Uruchom](./media/how-to-use-android-map-control-library/run-app.png)

Program android Studio zajmie kilka sekund, aby skompilować aplikację. Po ukończeniu kompilacji można przetestować aplikację w urządzeniu z systemem Android emulowane. Powinna pojawić się mapa, podobny do poniższego:

![Mapy dla systemu android](./media/how-to-use-android-map-control-library/android-map.png)

## <a name="add-a-marker-to-the-map"></a>Dodaj znacznik do mapy

Aby dodać znacznik do mapy `mapView.getMapAsync()` funkcja `MainActivity.java`. Końcowe `MainActivity.java` kod powinien wyglądać następująco:

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

Uruchom ponownie aplikację. Znacznik powinny zostać wyświetlone na mapie, jak pokazano poniżej:

![Przypnij mapę dla systemu android](./media/how-to-use-android-map-control-library/android-map-pin.png)