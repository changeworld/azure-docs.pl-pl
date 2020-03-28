---
title: 'Samouczek: Tworzenie nowej aplikacji na Androida'
description: W tym samouczku dowiesz się, jak utworzyć nową aplikację dla systemu Android przy użyciu zakotwiczeń przestrzennych platformy Azure.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 04/03/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: e1773ef81a5b727187a9a69ccc7ce7ad0421fb2c
ms.sourcegitcommit: 940e16ff194d5163f277f98d038833b1055a1a3e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2020
ms.locfileid: "80246776"
---
# <a name="tutorial-step-by-step-instructions-to-create-a-new-android-app-using-azure-spatial-anchors"></a>Samouczek: Instrukcje krok po kroku dotyczące tworzenia nowej aplikacji dla systemu Android przy użyciu zakotwiczeń przestrzennych platformy Azure

W tym samouczku pokazano, jak utworzyć nową aplikację dla systemu Android, która integruje funkcję ARCore z zakotwiczeniami przestrzennymi platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć kroki tego samouczka, upewnij się, że dysponujesz następującymi elementami:

- Komputer z systemem Windows lub macOS z <a href="https://developer.android.com/studio/" target="_blank">systemem Android Studio 3.4+</a>.
- Urządzenie z systemem Android <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">pracujące w trybie dewelopera</a> i <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">zgodne z platformą ARCore</a>.

## <a name="getting-started"></a>Wprowadzenie

Uruchom Android Studio. W oknie Witamy w **Android Studio** kliknij przycisk **Rozpocznij nowy projekt Android Studio**. Lub, jeśli masz projekt już otwarty, wybierz **plik**->**nowego projektu**.

W oknie **Tworzenie nowego projektu** w sekcji Telefon i **tablet** wybierz pozycję **Puste działanie**i kliknij przycisk **Dalej**. Następnie w obszarze Minimalny `API 26: Android 8.0 (Oreo)`poziom interfejsu **API**wybierz opcję i upewnij się, że **język** jest ustawiony na `Java`. Można zmienić nazwę projektu & lokalizację i nazwę pakietu. Pozostaw inne opcje w ich stanie. Kliknij przycisk **Zakończ**. Instalator **składników** zostanie uruchomiony. Gdy już to zrobisz, kliknij przycisk **Zakończ**. Po pewnym przetworzeniu Android Studio otworzy IDE.

## <a name="trying-it-out"></a>Wypróbowanie go

Aby przetestować nową aplikację, podłącz urządzenie z obsługą dewelopera do komputera deweloperskiego za pomocą kabla USB. Kliknij **przycisk Uruchom**->**polecenie Uruchom "app"**. W oknie **Wybierz miejsce docelowe wdrożenia** wybierz urządzenie i kliknij przycisk **OK**. Android Studio instaluje aplikację na podłączonym urządzeniu i uruchamia ją. Powinieneś teraz zobaczyć "Hello World!" w aplikacji uruchomionej na urządzeniu. Kliknij **pozycję Uruchom**->**stop 'app'**.

## <a name="integrating-_arcore_"></a>Integracja _ARCore_

<a href="https://developers.google.com/ar/discover/" target="_blank">_ARCore_</a> to platforma Google do tworzenia doświadczeń rozszerzonej rzeczywistości, umożliwiając urządzeniu śledzenie jego pozycji w miarę ruchu i budowanie własnego zrozumienia świata rzeczywistego.

Zmodyfikuj, `app\manifests\AndroidManifest.xml` aby `<manifest>` uwzględnić następujące wpisy wewnątrz węzła głównego. Ten fragment kodu wykonuje kilka czynności:

- Umożliwi to aplikacji dostęp do aparatu urządzenia.
- Zapewni to również, że aplikacja jest widoczna tylko w Sklepie Google Play na urządzeniach obsługujących ARCore.
- Skonfiguruje sklep Google Play, aby pobrać i zainstalować ARCore, jeśli nie jest jeszcze zainstalowany, gdy aplikacja jest zainstalowana.

```xml
<uses-permission android:name="android.permission.CAMERA" />
<uses-feature android:name="android.hardware.camera.ar" />

<application>
    ...
    <meta-data android:name="com.google.ar.core" android:value="required" />
    ...
</application>
```

Zmodyfikuj, `Gradle Scripts\build.gradle (Module: app)` aby uwzględnić następujący wpis. Ten kod zapewni, że aplikacja jest przeznaczona dla ARCore w wersji 1.8. Po tej zmianie możesz otrzymać powiadomienie od Gradle z prośbą o synchronizację: kliknij przycisk **Synchronizuj teraz**.

```
dependencies {
    ...
    implementation 'com.google.ar:core:1.11.0'
    ...
}
```

## <a name="integrating-_sceneform_"></a>Integracja _scenoformy_

[_Form sceneform_](https://developers.google.com/sceneform/develop/) ułatwia renderowanie realistycznych scen 3D w aplikacjach Augmented Reality, bez konieczności uczenia się OpenGL.

Zmodyfikuj, `Gradle Scripts\build.gradle (Module: app)` aby uwzględnić następujące wpisy. Ten kod pozwoli aplikacji używać konstrukcji języka z java `Sceneform` 8, który wymaga. Zapewni również, że `Sceneform` aplikacja jest przeznaczona dla wersji 1.8, ponieważ powinna być zgodna z wersją arcore, z których korzysta aplikacja. Po tej zmianie możesz otrzymać powiadomienie od Gradle z prośbą o synchronizację: kliknij przycisk **Synchronizuj teraz**.

```
android {
    ...

    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}

dependencies {
    ...
    implementation 'com.google.ar.sceneform.ux:sceneform-ux:1.11.0'
    ...
}
```

Otwórz `app\res\layout\activity_main.xml`swój element i zastąp istniejący element Hello Wolrd `<TextView>` następującym elementem ArFragment. Ten kod spowoduje, że na ekranie pojawi się kanał kamery, dzięki czemu arcore będzie śledzić pozycję urządzenia w miarę jego ruchu.

```xml
<fragment android:name="com.google.ar.sceneform.ux.ArFragment"
    android:id="@+id/ux_fragment"
    android:layout_width="match_parent"
    android:layout_height="match_parent" />
```

[Ponownie rozmieszcz](#trying-it-out) aplikację na urządzeniu, aby ponownie ją zweryfikować. Tym razem należy poprosić o uprawnienia do kamery. Po zatwierdzeniu na ekranie powinno być widoczne renderowanie kanału z kamery.

## <a name="place-an-object-in-the-real-world"></a>Umieść obiekt w świecie rzeczywistym

Utwórzmy & umieść obiekt za pomocą aplikacji. Najpierw dodaj do swojego: `app\java\<PackageName>\MainActivity`

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=23-33)]

Następnie dodaj do `MainActivity` swojej klasy następujące zmienne członkowskie:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=52-57)]

Następnie dodaj następujący kod `app\java\<PackageName>\MainActivity` `onCreate()` do metody. Ten kod spowoduje podłączenie odbiornika, o nazwie `handleTap()`, który wykryje, gdy użytkownik dotknie ekranu na urządzeniu. Jeśli kran stanie się na powierzchni świata rzeczywistego, który został już rozpoznany przez śledzenie ARCore, odbiornik zostanie uruchomiony.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=68-74,85&highlight=6-7)]

Na koniec dodaj `handleTap()` następującą metodę, która powiąże wszystko razem. Utworzy kulę i umieści ją w lokalizacji stukniętej. Kula początkowo będzie czarna, `this.recommendedSessionProgress` ponieważ jest teraz ustawiona na zero. Ta wartość zostanie skorygowana później.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=150-158,170-171,174-182,198-199)]

[Ponownie rozmieszcz](#trying-it-out) aplikację na urządzeniu, aby ponownie ją zweryfikować. Tym razem możesz poruszać się po urządzeniu, aby uzyskać ARCore, aby rozpocząć rozpoznawanie środowiska. Następnie dotknij ekranu, aby utworzyć & umieścić czarną kulę na wybranej powierzchni.

## <a name="attach-a-local-azure-spatial-anchor"></a>Dołączanie lokalnej kotwicy przestrzennej platformy Azure

Zmodyfikuj, `Gradle Scripts\build.gradle (Module: app)` aby uwzględnić następujący wpis. Ten kod zapewni, że aplikacja jest przeznaczona dla platformy Azure Spatial Anchors w wersji 2.2.0. To powiedziawszy, odwoływanie się do najnowszej wersji zakotwiczenia przestrzennego platformy Azure powinno działać. Informacje o wersji można znaleźć [tutaj.](https://github.com/Azure/azure-spatial-anchors-samples/releases)

```
dependencies {
    ...
    implementation "com.microsoft.azure.spatialanchors:spatialanchors_jni:[2.2.0]"
    implementation "com.microsoft.azure.spatialanchors:spatialanchors_java:[2.2.0]"
    ...
}
```

Kliknij prawym `app\java\<PackageName>` ->przyciskiem myszy **pozycję Nowa**->**klasa Java**. Ustaw **nazwę** na _MyFirstApp_i **Superclass** na _android.app.Application_. Pozostaw inne opcje w ich stanie. Kliknij przycisk **OK**. Zostanie utworzony `MyFirstApp.java` plik o nazwie. Dodaj do niego następujący import:

```java
import com.microsoft.CloudServices;
```

Następnie dodaj następujący kod wewnątrz `MyFirstApp` nowej klasy, co zapewni, że zakotwiczenia przestrzenne platformy Azure zostaną zainicjowane w kontekście aplikacji.

```java
    @Override
    public void onCreate() {
        super.onCreate();
        CloudServices.initialize(this);
    }
```

Teraz zmodyfikuj, `app\manifests\AndroidManifest.xml` aby `<application>` uwzględnić następujący wpis wewnątrz węzła głównego. Ten kod spowoduje podłączenie Application klasy utworzonej w aplikacji.

```xml
    <application
        android:name=".MyFirstApp"
        ...
    </application>
```

Powrót `app\java\<PackageName>\MainActivity`do , dodaj do niego następujące importy:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=33-40&highlight=2-8)]

Następnie dodaj do `MainActivity` swojej klasy następujące zmienne członkowskie:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=57-60&highlight=3-4)]

Następnie dodajmy następującą `initializeSession()` metodę wewnątrz `mainActivity` klasy. Po wywołaniu zapewni sesji Azure Spatial Anchors jest tworzony i poprawnie zainicjowane podczas uruchamiania aplikacji.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=89-97,146)]

Teraz podłączmy metodę `initializeSession()` do metody. `onCreate()` Ponadto zapewnimy, że ramki z pliku danych z kamery są wysyłane do usługi Azure Spatial Anchors SDK do przetwarzania.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=68-85&highlight=9-17)]

Na koniec dodaj następujący kod `handleTap()` do metody. Dołączy lokalną kotwicę przestrzenną platformy Azure do czarnej kuli, którą umieszczamy w świecie rzeczywistym.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=150-158,170-182,198-199&highlight=12-13)]

[Ponownie rozmieszczaj](#trying-it-out) aplikację po raz kolejny. Poruszaj się po urządzeniu, dotknij ekranu i umieść czarną kulę. Tym razem jednak kod będzie tworzenie i dołączanie lokalnej platformy Azure Spatial Anchor do sfery.

Przed kontynuowaniem dalszych, należy utworzyć identyfikator konta zakotwiczenia przestrzennego platformy Azure i klucz, jeśli jeszcze ich nie masz. Postępuj zgodnie z poniższą sekcją, aby je uzyskać.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="upload-your-local-anchor-into-the-cloud"></a>Przesyłanie lokalnej kotwicy do chmury

Po uzyskaniu identyfikatora konta i klucza zakotwiczenia `app\java\<PackageName>\MainActivity`przestrzennego platformy Azure możemy wrócić do tego, dodaj do niego następujące importy:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=40-45&highlight=3-6)]

Następnie dodaj do `MainActivity` swojej klasy następujące zmienne członkowskie:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=60-65&highlight=3-6)]

Teraz dodaj następujący kod `initializeSession()` do metody. Po pierwsze ten kod pozwoli aplikacji do monitorowania postępu, który azure przestrzenne kotwice SDK sprawia, że podczas zbiera ramek z pliku danych z kamery. Podobnie jak w tym samym roku, kolor twojej kuli zacznie się zmieniać z oryginalnej czerni na szarą. Następnie zmieni kolor na biały po zebraniu wystarczającej liczby klatek, aby przesłać kotwicę do chmury. Po drugie ten kod zapewni poświadczenia potrzebne do komunikowania się z zaplecza chmury. W tym miejscu skonfigurujesz aplikację do używania identyfikatora konta i klucza. Skopiowano je do edytora tekstu podczas [konfigurowania zasobu Zakotwiczenia przestrzenne](#create-a-spatial-anchors-resource).

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=89-120,142-146&highlight=11-36)]

Następnie dodaj następującą `uploadCloudAnchorAsync()` metodę `mainActivity` wewnątrz klasy. Po wywołaniu ta metoda będzie asynchronicznie czekać, aż wystarczająca liczba ramek są zbierane z urządzenia. Jak tylko tak się stanie, przełączy kolor kuli na żółty, a następnie rozpocznie przekazywanie lokalnej kotwicy przestrzennej platformy Azure do chmury. Po zakończeniu przekazywania kod zwróci identyfikator kotwicy.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=uploadCloudAnchorAsync)]

Na koniec połączmy wszystko. W `handleTap()` metodzie dodaj następujący kod. Wywoła metodę, `uploadCloudAnchorAsync()` gdy tylko zostanie utworzona twoja kula. Po powrocie metody poniższy kod wykona jedną ostateczną aktualizację kuli, zmieniając jej kolor na niebieski.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=150-158,170-199&highlight=24-37)]

[Ponownie rozmieszczaj](#trying-it-out) aplikację po raz kolejny. Poruszaj się po urządzeniu, dotknij ekranu i umieść kulę. Tym razem jednak twoja kula zmieni swój kolor z czarnego na biały, ponieważ zbierane są ramki kamery. Gdy mamy wystarczająco dużo klatek, kula zmieni się na żółtą, a rozpocznie się przesyłanie w chmurze. Po zakończeniu przesyłania kula zmieni kolor na niebieski. Opcjonalnie można również użyć `Logcat` okna wewnątrz Android Studio do monitorowania wiadomości dziennika, które wysyła aplikacja. Na przykład postęp sesji podczas przechwytywania ramki i identyfikator zakotwiczenia, który chmura zwraca po zakończeniu przekazywania.

## <a name="locate-your-cloud-spatial-anchor"></a>Lokalizowanie kotwicy przestrzennej chmury

Jedna kotwica jest przesyłana do chmury, jesteśmy gotowi, aby spróbować zlokalizować go ponownie. Najpierw dodajmy następujące importy do kodu.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=45-48&highlight=3-4)]

Następnie dodajmy następujący kod do `handleTap()` metody. Ten kod będzie:

- Usuń naszą istniejącą niebieską kulę z ekranu.
- Ponownie zaikwaluj naszą sesję zakotwiczenia przestrzenne platformy Azure. Ta akcja zapewni, że kotwica, którą zamierzamy zlokalizować, pochodzi z chmury zamiast lokalnej kotwicy, którą stworzyliśmy.
- Wystawiaj kwerendę dla kotwicy, którą przekazyliśmy do chmury.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=handleTap&highlight=10-19)]

Teraz przyśpmy kod, który zostanie wywołany, gdy znajduje się kotwica, dla których prowadzi zapytanie. Wewnątrz `initializeSession()` metody dodaj następujący kod. Ten fragment kodu utworzy & umieszczenia zielonej kuli po zlokalizowaniu kotwicy przestrzennej chmury. Umożliwi to również ponowne stuknięcie ekranu, dzięki czemu możesz powtórzyć cały scenariusz jeszcze raz: utworzyć kolejną lokalną kotwicę, przesłać ją i zlokalizować ponownie.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=initializeSession&highlight=34-53)]

Gotowe. [Ponownie rozmieszczaj](#trying-it-out) aplikację po raz ostatni, aby wypróbować cały scenariusz od końca do końca. Poruszaj się po urządzeniu i umieść czarną kulę. Następnie poruszaj urządzenie, aby rejestrować ramki kamery, aż kula zmieni kolor na żółty. Twoja lokalna kotwica zostanie przesłana, a kula zmieni kolor na niebieski. Na koniec ponownie stuknij ekran, aby twoja lokalna kotwica została usunięta, a następnie zapytamy o jej odpowiednik w chmurze. Kontynuuj przenoszenie urządzenia, aż do zlokalizowania kotwicy przestrzennej chmury. Zielona kula powinna pojawić się we właściwym miejscu i można spłukać & powtórzyć cały scenariusz ponownie.

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-new-android-app-finished.md)]
