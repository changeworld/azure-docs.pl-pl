---
title: Wprowadzenie do kontroli map na Androida | Mapy platformy Microsoft Azure
description: W tym artykule dowiesz się, jak rozpocząć pracę z formantu mapy systemu Android przy użyciu microsoft azure maps android SDK.
author: philmea
ms.author: philmea
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 6e0f0f311b7ec8adae6ddb25e01046141adadfa4
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548535"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Wprowadzenie do usługi Azure Maps Android SDK

Zestaw SDK dla systemu Android usługi Azure Maps to biblioteka map wektorowych dla systemu Android. W tym artykule znajdziesz informacje o procesach instalowania zestawu Azure Maps dla systemu Android SDK i ładowania mapy.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="create-an-azure-maps-account"></a>Tworzenie konta usługi Azure Maps

Aby wykonać procedury w tym artykule, należy najpierw [utworzyć konto usługi Azure Maps](quick-demo-map-app.md#create-an-account-with-azure-maps) w warstwie cenowej S1 i uzyskać klucz [podstawowy](quick-demo-map-app.md#get-the-primary-key-for-your-account) dla swojego konta.

Aby uzyskać więcej informacji na temat uwierzytelniania w usłudze Azure Maps, zobacz [zarządzanie uwierzytelnianiem w usłudze Azure Maps](./how-to-manage-authentication.md).

### <a name="download-android-studio"></a>Pobierz Android Studio

Pobierz android Studio i utwórz projekt z pustym działaniem przed zainstalowaniem pakietu Azure Maps Android SDK. Możesz [pobrać Android Studio](https://developer.android.com/studio/) za darmo z Google. 

## <a name="create-a-project-in-android-studio"></a>Tworzenie projektu w Android Studio

Najpierw utwórz nowy projekt z pustym działaniem. Wykonaj następujące kroki, aby utworzyć projekt Android Studio:

1. W obszarze **Wybierz swój projekt**wybierz pozycję Telefon i **tablet**. Aplikacja będzie działać na tym obudowie.
2. Na karcie **Telefon i tablet** wybierz pozycję **Pusta aktywność**, a następnie wybierz pozycję **Dalej**.
3. W obszarze **Konfigurowanie** `API 21: Android 5.0.0 (Lollipop)` projektu wybierz jako minimalny SDK. Jest to najwcześniejsza wersja obsługiwana przez zestaw SDK systemu Azure Maps dla systemu Android.
4. Zaakceptuj wartość `Activity Name` `Layout Name` domyślną i wybierz pozycję **Zakończ**.

Więcej informacji na temat instalowania programu Android Studio i tworzenia nowego projektu można znaleźć w [dokumentacji programu Android Studio.](https://developer.android.com/studio/intro/)

![Tworzenie projektu w programie Android Studio ](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>Konfigurowanie urządzenia wirtualnego

Android Studio pozwala skonfigurować wirtualne urządzenie z Androidem na komputerze. W ten sposób można pomóc w testowaniu aplikacji podczas tworzenia. Aby skonfigurować urządzenie wirtualne, wybierz ikonę Menedżera urządzeń wirtualnych systemu Android (AVD) w prawym górnym rogu ekranu projektu, a następnie wybierz pozycję **Utwórz urządzenie wirtualne**. Możesz również uzyskać informacje o Menedżerze AVD, wybierając **narzędzia** > **Android** > **AVD Manager** z paska narzędzi. W kategorii **Telefony** wybierz **Nexus 5X**, a następnie wybierz pozycję **Dalej**.

Więcej informacji na temat konfigurowania avd można znaleźć w [dokumentacji programu Android Studio](https://developer.android.com/studio/run/managing-avds).

![Android Emulator](./media/how-to-use-android-map-control-library/android-emulator.png)

## <a name="install-the-azure-maps-android-sdk"></a>Instalowanie pakietu Azure Maps android SDK

Następnym krokiem w tworzeniu aplikacji jest zainstalowanie pakietu Azure Maps Android SDK. Wykonaj następujące czynności, aby zainstalować pakiet SDK:

1. Otwórz plik **build.gradle** najwyższego poziomu i dodaj następujący kod do **sekcji wszystkie projekty**, **repozytoria** bloku:

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Zaktualizuj **aplikację/build.gradle** i dodaj do niej następujący kod:
    
    1. Upewnij się, że **minSdkVersion** projektu jest w interfejsie API 21 lub nowszym.

    2. Dodaj następujący kod do sekcji Android:

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    3. Zaktualizuj blok zależności i dodaj nową linię zależności implementacji dla najnowszego pakietu Azure Maps dla systemu Android SDK:

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.2"
        ```
    
    4. Przejdź do **pliku** na pasku narzędzi, a następnie kliknij **przycisk Synchronizuj projekt z plikami gradle**.
3. Dodaj fragment mapy do działania głównego \> \> (działanie\_układu oze main.xml):
    
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
    
    * dodawanie importów dla zestawu SDK usługi Azure Maps
    * ustawianie informacji uwierzytelniających usługi Azure Maps
    * pobierz wystąpienie formantu mapy w **onCreate** metody

    Ustawienie informacji uwierzytelniania `AzureMaps` w klasie `setSubscriptionKey` `setAadProperties` globalnie przy użyciu lub metody sprawia, że nie trzeba dodawać informacje uwierzytelniania w każdym widoku. 

    Formant mapy zawiera własne metody cyklu życia do zarządzania cyklem życia OpenGL systemu Android. Te metody cyklu życia muszą być wywoływane bezpośrednio z działania zawierającego. Aby aplikacja poprawnie wywołać metody cyklu życia formantu mapy, należy zastąpić następujące metody cyklu życia w działania, który zawiera formant mapy. I należy wywołać odpowiednią metodę kontroli mapy. 

    * onCreate(Pakiet) 
    * onStart() 
    * onResume() 
    * onPause() 
    * onStop() 
    * onDestroy() 
    * onSaveInstanceState(Pakiet) 
    * onLowMemory() 

    Edytuj plik **MainActivity.java** w następujący sposób:
    
    ```java
    package com.example.myapplication;

    //For older versions use: import android.support.v7.app.AppCompatActivity; 
    import androidx.appcompat.app.AppCompatActivity;
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

## <a name="import-classes"></a>Importuj klasy

Po wykonaniu powyższych kroków prawdopodobnie otrzymasz ostrzeżenia od Android Studio dotyczące niektórych kodu. Aby rozwiązać te ostrzeżenia, należy `MainActivity.java`zaimportować klasy, do których odwołuje się .

Klasy te można zaimportować automatycznie, wybierając klawisze Alt+Enter (opcja+Powrót na komputerze Mac).

Wybierz przycisk uruchom, jak pokazano na poniższej grafice (lub naciśnij klawisze Control+R na komputerze Mac), aby utworzyć aplikację.

![Kliknij pozycję Uruchom.](./media/how-to-use-android-map-control-library/run-app.png)

Android Studio zajmie kilka sekund, aby zbudować aplikację. Po zakończeniu kompilacji można przetestować aplikację na emulowanym urządzeniu z systemem Android. Powinieneś zobaczyć mapę taką jak ta:

<center>

![Usługa Azure Maps w aplikacji dla systemu Android](./media/how-to-use-android-map-control-library/android-map.png)</center>

## <a name="localizing-the-map"></a>Lokalizowanie mapy

Zestaw SDK dla systemu Android usługi Azure Maps udostępnia trzy różne sposoby ustawiania języka i widoku regionalnego mapy. Poniższy kod pokazuje, jak ustawić język na francuski ("fr-FR") i widok regionalny na "auto". 

Pierwszą opcją jest przekazanie języka i wyświetlenie informacji regionalnych do `AzureMaps` klasy przy użyciu statycznych `setLanguage` i `setView` metod globalnie. Spowoduje to ustawienie domyślnego języka i widoku regionalnego we wszystkich formantach usługi Azure Maps załadowanych w aplikacji.

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

Drugą opcją jest przekazanie języka i wyświetlenie informacji do kodu XML formantu mapy.

```XML
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="auto"
    />
```

Trzecią opcją jest programowe ustawienie języka i widoku regionalnego mapy `setStyle` przy użyciu metody map. Można to zrobić w dowolnym momencie, aby zmienić język i widok regionalny mapy.

```Java
mapControl.onReady(map -> {
    map.setStyle(StyleOptions.language("fr-FR"));
    map.setStyle(StyleOptions.view("auto"));
});
```

Oto przykład usługi Azure Maps z językiem ustawionym na "fr-FR" i widokiem regionalnym ustawionym na "auto".

<center>

![Usługa Azure Maps, obraz mapy przedstawiający etykiety w języku francuskim](./media/how-to-use-android-map-control-library/android-localization.png)
</center>

Pełna lista obsługiwanych języków i widoków regionalnych jest udokumentowana [tutaj](supported-languages.md).

## <a name="navigating-the-map"></a>Poruszanie się po mapie

Istnieje kilka różnych sposobów powiększania, przesuwania, obracania i rozbijania mapy. Poniżej przedstawiono wszystkie sposoby poruszania się po mapie.

**Powiększanie mapy**

- Dotknij mapy dwoma palcami i zsuń się, aby pomniejszyć lub rozłożyć palce, aby powiększyć.
- Stuknij dwukrotnie mapę, aby powiększyć jeden poziom.
- Naciśnij dwukrotnie dwoma palcami, aby powiększyć mapę o jeden poziom.
- Dotknij dwukrotnie; przy drugim dotknięciu przytrzymaj palec na mapie i przeciągnij w górę, aby powiększyć lub w dół, aby pomniejszyć widok.

**Przesuwanie mapy**

- Dotknij mapy i przeciągnij w dowolnym kierunku.

**Obracanie mapy**

- Dotknij mapy dwoma palcami i obróć.

**Rozbij mapę**

- Dotknij mapy dwoma palcami i przeciągnij je razem w górę lub w dół.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak dodać dane nakładki na mapie:

> [!div class="nextstepaction"]
> [Dodawanie warstwy symboli do mapy systemu Android](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Dodawanie kształtów do mapy systemu Android](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)

> [!div class="nextstepaction"]
> [Zmienianie stylów map na mapach systemu Android](https://docs.microsoft.com/azure/azure-maps/set-android-map-styles)
