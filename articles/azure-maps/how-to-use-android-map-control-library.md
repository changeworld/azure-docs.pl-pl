---
title: Wprowadzenie do kontrolki mapy dla systemu Android w usłudze Azure Maps | Dokumentacja firmy Microsoft
description: Kontrolka mapy dla systemu Android w usłudze Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 9df5eb9fa4493f82c6efd4a8e30eee324e4eac2a
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273834"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Wprowadzenie do usługi Azure Maps Android SDK

Zestaw Azure SDK dla systemu Android mapy jest biblioteka map wektorów dla systemu Android. Ten artykuł przeprowadzi Cię przez procesy Instalowanie zestawu Azure SDK dla systemu Android mapy i ładowanie mapy.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="create-an-azure-maps-account"></a>Tworzenie konta usługi Azure Maps

Aby wykonać procedury opisane w tym artykule, należy najpierw [utworzyć konto usługi Azure Maps](how-to-manage-account-keys.md) w warstwę cenową S1.

### <a name="download-android-studio"></a>Pobierz program Android Studio

Musisz Pobierz program Android Studio i Utwórz projekt za pomocą pustego działania, przed zainstalowaniem zestawu Azure SDK dla systemu Android mapy. Możesz [Pobierz program Android Studio](https://developer.android.com/studio/) bezpłatnie od firmy Google. 

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

1. Otwórz najwyższego poziomu **build.gradle** pliku i Dodaj następujący kod do **wszystkie projekty**, **repozytoriów** zablokować sekcji:

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Aktualizacja usługi **app/build.gradle** i Dodaj do niej następujący kod:
    
    1. Upewnij się, że projektu **minSdkVersion** jest na poziomie 21 interfejsu API lub nowszej.

    2. Dodaj następujący kod do sekcji dla systemu Android:

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    3. Zaktualizuj swoje bloku zależności i Dodaj nowy wiersz zależności implementacji dla najnowszych Azure Maps zestawu Android SDK:

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.2"
        ```

    > [!Note]
    > Zestaw Azure SDK dla systemu Android mapy regularnie jest uaktualniony i ulepszone. Możesz zobaczyć [wprowadzenie do kontrolki mapy dla systemu Android](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) dokumentację, aby uzyskać numer najnowszej wersji wdrożenia usługi Azure Maps. Ponadto można ustawić numer wersji od "0,2" do "0 +" Aby zawsze wskazywała do najnowszej wersji.

3. Edytuj **res** > **układ** > **activity_main.xml** i zastąp go następującym kodem:
    
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

4. W **MainActivity.java** pliku będzie konieczne:
    
    * Dodaj Import dla zestawu SDK usługi Azure Maps
    * Ustaw informacje o uwierzytelnianiu usługi Azure Maps
    * Pobierz wystąpienie kontrolki mapy w **onCreate** — metoda

    Ustawianie informacji o uwierzytelnianiu dla klasy AzureMaps globalnie za pomocą metody setSubscriptionKey lub setAadProperties sprawia, że tak nie będzie trzeba dodać informacje o uwierzytelnianiu w każdym widoku. Kontrolka mapy zawiera własnej metody cyklu życia zarządzania cyklem życia OpenGL dla urządzeń systemu Android, które muszą być wywoływane bezpośrednio z zawierającego działanie. Aby poprawnie, wywołania metod cyklu życia kontrolki mapy aplikacji należy zastąpić następujące metody cyklu życia w działaniu, który zawiera formant mapy i wywołać metodę kontrolki mapy odpowiednich. 

    Edytuj **MainActivity.java** pliku w następujący sposób:
    
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

<center>

![Mapy dla systemu android](./media/how-to-use-android-map-control-library/android-map.png)</center>

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak dodawać dane nakładki na mapie:

> [!div class="nextstepaction"]
> [Dodaj warstwę symbol do mapowania dla systemu Android](https://review.docs.microsoft.com/azure/azure-maps/how-to-add-symbol-to-android-map)

> [!div class="nextstepaction"]
> [Dodawanie kształtów do mapowania dla systemu Android](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)

> [!div class="nextstepaction"]
> [Zmień style mapy w społeczności maps dla systemu Android](https://docs.microsoft.com/azure/azure-maps/set-android-map-styles)
