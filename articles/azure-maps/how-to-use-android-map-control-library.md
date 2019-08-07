---
title: Wprowadzenie do kontrolki mapy systemu Android w Azure Maps | Microsoft Docs
description: Kontrolka mapy systemu Android w Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: a3423635ab226693e0b3b057e2c2cb441861ea1b
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68839405"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Wprowadzenie do Azure Maps Android SDK

Android SDK Azure Maps jest biblioteką mapy wektorowej dla systemu Android. Ten artykuł przeprowadzi Cię przez proces instalowania Android SDK Azure Maps i ładowania mapy.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="create-an-azure-maps-account"></a>Tworzenie konta usługi Azure Maps

Aby wykonać procedury opisane w tym artykule, musisz najpierw [utworzyć konto Azure Maps](how-to-manage-account-keys.md) w warstwie cenowej S1.

### <a name="download-android-studio"></a>Pobierz program Android Studio

Przed zainstalowaniem Android SDK Azure Maps należy pobrać Android Studio i utworzyć projekt z pustą aktywnością. Android Studio można [pobrać](https://developer.android.com/studio/) bezpłatnie z usługi Google. 

## <a name="create-a-project-in-android-studio"></a>Tworzenie projektu w Android Studio

Najpierw utwórz nowy projekt z pustym działaniem. Wykonaj następujące kroki, aby utworzyć projekt Android Studio:

1. W obszarze **Wybierz projekt**wybierz pozycję **telefon i tablet**. Aplikacja będzie działać na tym formularzu.
2. Na karcie **telefon i tablet** wybierz pozycję **puste działanie**, a następnie wybierz przycisk **dalej**.
3. W obszarze **Konfigurowanie projektu**wybierz `API 21: Android 5.0.0 (Lollipop)` jako minimalny zestaw SDK. Jest to Najwcześniejsza wersja obsługiwana przez Android SDK Azure Maps.
4. Zaakceptuj wartość domyślną `Activity Name` i `Layout Name` wybierz pozycję **Zakończ**.

Zapoznaj się z [dokumentacją Android Studio](https://developer.android.com/studio/intro/) , aby uzyskać pomoc dotyczącą instalowania Android Studio i tworzenia nowego projektu.

![Tworzenie projektu](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>Konfigurowanie urządzenia wirtualnego

Android Studio umożliwia skonfigurowanie wirtualnego urządzenia z systemem Android na komputerze. Wykonanie tej czynności może pomóc w testowaniu aplikacji podczas jej tworzenia. Aby skonfigurować urządzenie wirtualne, wybierz ikonę Menedżera urządzeń wirtualnych systemu Android (AVD) w prawym górnym rogu ekranu projektu, a następnie wybierz pozycję **Utwórz urządzenie wirtualne**. Możesz również uzyskać dostęp do Menedżera AVD, wybierając pozycję **Narzędzia** > **Android** > **AVD Manager** na pasku narzędzi. W kategorii **telefony** wybierz pozycję **Nexus pięciokrotną**, a następnie wybierz pozycję **Next (dalej**).

Więcej informacji na temat konfigurowania AVD można znaleźć w [dokumentacji Android Studio](https://developer.android.com/studio/run/managing-avds).

![Emulator systemu Android](./media/how-to-use-android-map-control-library/android-emulator.png)

## <a name="install-the-azure-maps-android-sdk"></a>Zainstaluj Android SDK Azure Maps

Następnym krokiem tworzenia aplikacji jest zainstalowanie Android SDK Azure Maps. Wykonaj następujące kroki, aby zainstalować zestaw SDK:

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
    > Android SDK Azure Maps są regularnie uaktualniane i rozszerzane. Aby uzyskać najnowszy numer wersji implementacji Azure Maps, można zapoznać się z dokumentacją dotyczącą rozpoczynania [pracy z kontrolką mapy systemu Android](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) . Ponadto można ustawić numer wersji z "0,2" na "0 +", aby zawsze wskazywał najnowszą wersję.

3. Edytuj > układres > **activity_main. XML** i zastąp go kodem:
    
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

    Ustawienie informacji o uwierzytelnianiu w klasie AzureMaps globalnie przy użyciu metod setSubscriptionKey lub setAadProperties powoduje, że nie trzeba dodawać informacji o uwierzytelnianiu w każdym widoku. Kontrolka mapy zawiera własne metody cyklu życia do zarządzania cyklem życia OpenGL dla systemu Android, które muszą być wywoływane bezpośrednio z działania zawierającego. Aby aplikacja mogła prawidłowo, wywołaj metody cyklu życia kontrolki mapy, należy zastąpić następujące metody cyklu życia w działaniu zawierającym formant mapy i wywołać odpowiednią metodę kontroli mapy. 

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

## <a name="import-classes"></a>Importuj klasy

Po wykonaniu powyższych kroków prawdopodobnie otrzymasz ostrzeżenia z Android Studio o niektórym kodzie. Aby rozwiązać te ostrzeżenia, zaimportuj klasy, `MainActivity.java`do których odwołuje się.

Można automatycznie importować te klasy, wybierając klawisze Alt + Enter (opcja + Return na komputerze Mac).

Wybierz przycisk Run (Uruchom), jak pokazano na poniższej ilustracji (lub naciśnij klawisze Control + R na komputerze Mac), aby skompilować aplikację.

![Kliknij przycisk Uruchom](./media/how-to-use-android-map-control-library/run-app.png)

Kompilowanie aplikacji Android Studio potrwa kilka sekund. Po zakończeniu kompilacji można przetestować aplikację na emulowanym urządzeniu z systemem Android. Powinna zostać wyświetlona mapa przypominająca:

<center>

![Mapa systemu Android](./media/how-to-use-android-map-control-library/android-map.png)</center>

## <a name="localizing-the-map"></a>Lokalizowanie mapy

Azure Maps Android SDK oferuje trzy różne sposoby ustawiania języka i widoku regionalnego mapy. Poniższy kod pokazuje, jak ustawić język na francuski ("fr-FR") i widok regionalny na "Auto". 

Pierwsza opcja polega na przejściu języka i wyświetleniu informacji regionalnych do `AzureMaps` klasy przy użyciu metod statycznych `setLanguage` i `setView` globalnie. Spowoduje to ustawienie języka domyślnego i widoku regionalnego dla wszystkich kontrolek Azure Maps załadowanych w aplikacji.

```Java
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

```XML
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="auto"
    />
```

Trzecia opcja polega na programowo ustawić język i widok regionalny mapy przy użyciu metody Maps `setStyle` . Można to zrobić w dowolnym momencie, aby zmienić język i widok regionalny mapy.

```Java
mapControl.onReady(map -> {
    map.setStyle(StyleOptions.language("fr-FR"));
    map.setStyle(StyleOptions.view("auto"));
});
```

Oto przykład Azure Maps z językiem ustawionym na "fr-FR" i widoku regionalnym ustawionym na "Auto".

<center>

![Obraz mapy przedstawiający etykiety w języku francuskim](./media/how-to-use-android-map-control-library/android-localization.png)
</center>

Pełna lista obsługiwanych języków i widoków regionalnych została udokumentowana [tutaj](supported-languages.md).

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak dodać dane nakładki na mapie:

> [!div class="nextstepaction"]
> [Dodawanie warstwy symboli do mapy systemu Android](https://review.docs.microsoft.com/azure/azure-maps/how-to-add-symbol-to-android-map)

> [!div class="nextstepaction"]
> [Dodawanie kształtów do mapy systemu Android](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)

> [!div class="nextstepaction"]
> [Zmień style mapy w usłudze mapy systemu Android](https://docs.microsoft.com/azure/azure-maps/set-android-map-styles)
