---
title: Samouczek — instrukcje krok po kroku, aby utworzyć nową aplikację systemu Android przy użyciu Azure przestrzenne kotwic | Dokumentacja firmy Microsoft
description: W tym samouczku dowiesz się, jak utworzyć nową aplikację systemu Android przy użyciu kotwic przestrzenne platformy Azure.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 04/03/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 64f78b04d433c81302499addf15c3d19621bbf9f
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/04/2019
ms.locfileid: "58919602"
---
# <a name="tutorial-step-by-step-instructions-to-create-a-new-android-app-using-azure-spatial-anchors"></a>Samouczek: Instrukcje krok po kroku, aby utworzyć nową aplikację systemu Android przy użyciu kotwic przestrzenne platformy Azure

Ten samouczek przedstawia sposób tworzenia nowej aplikacji dla systemu Android, która łączy w sobie funkcje ARCore przy użyciu Azure przestrzenne kotwic.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć kroki tego samouczka, upewnij się, że dysponujesz następującymi elementami:

- Maszyna z systemem Windows lub macOS i programem <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.3 lub nowszym</a>.
- Urządzenie z systemem Android <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">pracujące w trybie dewelopera</a> i <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">zgodne z platformą ARCore</a>.

## <a name="getting-started"></a>Wprowadzenie

Uruchom program Android Studio. W **Witamy w programie Android Studio** okna, kliknij przycisk **Utwórz nowy projekt programu Android Studio**. Lub, jeśli masz projekt, który został już otwarty, wybierz **pliku**->**nowy projekt**.

W **Utwórz nowy projekt** okna, w obszarze **telefony i tablety** wybierz pozycję **puste działanie**i kliknij przycisk **dalej**. Następnie w obszarze **poziom interfejsu API Minimum**, wybierz `API 26: Android 8.0 (Oreo)`i upewnij się, **języka** ustawiono `Java`. Można zmienić nazwę projektu i lokalizację i nazwę pakietu. Pozostaw inne opcje, ponieważ są one. Kliknij przycisk **Zakończ**. **Instalator składników** zostaną uruchomione. Gdy wszystko będzie gotowe, kliknij przycisk **Zakończ**. Po zakończeniu niektóre przetwarzania Android Studio zostanie otwarty IDE.

## <a name="trying-it-out"></a>Próba użycia zasobu

Do przetestowania Twojej nowej aplikacji, podłącz urządzenie z obsługą dla deweloperów na maszynie deweloperskiej za pomocą kabla USB. Kliknij przycisk **Uruchom**->**Uruchom aplikację**. W **wybierz cel wdrożenia** okna, wybierz urządzenie, a następnie kliknij przycisk **OK**. Program android Studio instaluje aplikację na podłączonym urządzeniu i uruchamia go. Powinien zostać wyświetlony "Hello World!" wyświetlany w aplikacji uruchomionych na urządzeniu. Kliknij przycisk **Uruchom**->**Stop "aplikacja"**.

## <a name="integrating-arcore"></a>Integrowanie _ARCore_

<a href="https://developers.google.com/ar/discover/" target="_blank">_ARCore_ </a> to platforma firmy Google umożliwiające tworzenie środowisk w rzeczywistości rozszerzonej, umożliwiając urządzeniu, aby śledzić jego położenie przenosi i tworzy własną wiedzę na temat rzeczywistych.

Modyfikowanie `app\manifests\AndroidManifest.xml` obejmujący następujące wpisy w katalogu głównym `<manifest>` węzła. Ten fragment kodu wykonuje kilka rzeczy:

- Umożliwia aplikacji dostęp do aparatu fotograficznego urządzenia.
- Także pewność, że aplikacja jest widoczna tylko w Google Play Store na urządzeniach, które obsługują ARCore.
- Zostanie skonfigurowany Store Google Play, aby pobrać i zainstalować ARCore, jeśli nie jest on zainstalowany, po zainstalowaniu aplikacji.

```xml
<uses-permission android:name="android.permission.CAMERA" />
<uses-feature android:name="android.hardware.camera.ar" />

<application>
    ...
    <meta-data android:name="com.google.ar.core" android:value="required" />
    ...
</application>
```

Modyfikowanie `Gradle Scripts\build.gradle (Module: app)` obejmujący następujący wpis. Ten kod będzie upewnij się, że aplikacja jest przeznaczona na ARCore w wersji 1.5 (Pomoc techniczna dla ARCore 1.6 + na kotwic przestrzenne Azure będzie dostępna wkrótce). Po tej zmianie, można uzyskać powiadomienia z narzędzia Gradle prośbą do synchronizacji: kliknij **Synchronizuj teraz**.

```
dependencies {
    ...
    implementation 'com.google.ar:core:1.5.0'
    ...
}
```

## <a name="integrating-sceneform"></a>Integrowanie _Sceneform_

<a href="https://developers.google.com/ar/develop/java/sceneform/" target="_blank">_Sceneform_ </a> pozwala w prosty sposób renderowania realistyczne scen 3D w aplikacjach w rzeczywistości rozszerzonej, bez konieczności uczenia się ze specyfikacji OpenGL.

Modyfikowanie `Gradle Scripts\build.gradle (Module: app)` obejmujący następujące wpisy. Ten kod pozwoli aplikację do używania konstrukcji języka w języku Java 8, która `Sceneform` wymaga. Zapewni aplikacji jest przeznaczony dla `Sceneform` w wersji 1.5, ponieważ powinna być zgodna wersja ARCore aplikacja używa. Po tej zmianie, można uzyskać powiadomienia z narzędzia Gradle prośbą do synchronizacji: kliknij **Synchronizuj teraz**.

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
    implementation 'com.google.ar.sceneform.ux:sceneform-ux:1.5.0'
    ...
}
```

Otwórz swoje `app\res\layout\activity_main.xml`i Zastąp istniejące Hello Wolrd `<TextView>` element z następujących ArFragment. Ten kod spowoduje, że aparat źródła danych mają być wyświetlane na ekranie, umożliwiając ARCore do śledzenia swojego położenia urządzenia, kiedy przesuwa się on.

```xml
<fragment android:name="com.google.ar.sceneform.ux.ArFragment"
    android:id="@+id/ux_fragment"
    android:layout_width="match_parent"
    android:layout_height="match_parent" />
```

[Ponowne wdrażanie](#trying-it-out) aplikacji urządzenia, aby ponownie sprawdzić poprawność. Tym razem powinien zostać poproszony aparatu uprawnień. Po zatwierdzeniu powinien pojawić się aparatu kanału informacyjnego renderowania na ekranie.

## <a name="place-an-object-in-the-real-world"></a>Umieść obiekt w rzeczywistych warunkach

Umożliwia tworzenie i umieść obiekt przy użyciu aplikacji. Najpierw dodaj następujące instrukcje importu do Twojej `app\java\<PackageName>\MainActivity`:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=23-33)]

Następnie dodaj następujące zmienne Członkowskie w swojej `MainActivity` klasy:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=52-57)]

Następnie dodaj następujący kod do Twojego `app\java\<PackageName>\MainActivity` `onCreate()` metody. Ten kod będzie obsługiwać odbiornika o nazwie `handleTap()`, który wykryje, kiedy użytkownik naciska ekranu na urządzeniu. Jeśli wzorcu tap ma miejsce na powierzchni świata rzeczywistego, który już został uznany przez firmy ARCore śledzenia, odbiornik zostanie uruchomiony.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=68-74,85&highlight=6-7)]

Na koniec należy dodać następujące `handleTap()` metody, które ze sobą wszystko, co będzie powiązać. Wówczas Utwórz kulę i umieść go w lokalizacji naciśnięty. Kula będzie początkowo czarny, ponieważ `this.recommendedSessionProgress` jest ustawiona na zero w tej chwili. Ta wartość zostanie zmieniona później.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=150-158,170-171,174-182,198-199)]

[Ponowne wdrażanie](#trying-it-out) aplikacji urządzenia, aby ponownie sprawdzić poprawność. Tym razem można przenieść na urządzeniu można pobrać ARCore, aby uruchomić rozpoznawanie środowiska. Następnie wybierz ekran, aby umieścić swoje kuli czarny na powierzchnię wybranego & Utwórz.

## <a name="attach-a-local-azure-spatial-anchor"></a>Dołącz lokalnego zakotwiczenia przestrzenne platformy Azure

Modyfikowanie `Gradle Scripts\build.gradle (Module: app)` obejmujący następujący wpis. Ten kod będzie upewnij się, że używana wersja kotwic przestrzenne Azure elementów docelowych aplikacji 1.0.2. Inaczej mówiąc, odwołuje się do dowolnej najnowszej wersji Azure przestrzenne kotwic powinny działać.

```
dependencies {
    ...
    implementation "com.microsoft.azure.spatialanchors:spatialanchors_jni:[1.0.2]"
    implementation "com.microsoft.azure.spatialanchors:spatialanchors_java:[1.0.2]"
    ...
}
```

Kliknij prawym przyciskiem myszy `app\java\<PackageName>` -> **nowe**->**klasy Java**. Ustaw **nazwa** do _Mojapierwszaaplikacja_, i **superklasie** do _android.app.Application_. Pozostaw inne opcje, ponieważ są one. Kliknij przycisk **OK**. Plik o nazwie `MyFirstApp.java` zostanie utworzony. Dodaj następujący import do niego:

```java
import com.microsoft.CloudServices;
```

Następnie należy dodać następujący kod w nowej `MyFirstApp` klasy, która zapewni kotwic przestrzenne Azure jest inicjowany za pomocą kontekstu Twojej aplikacji.

```java
    @Override
    public void onCreate() {
        super.onCreate();
        CloudServices.initialize(this);
    }
```

Teraz, zmodyfikować `app\manifests\AndroidManifest.xml` obejmujący następujący wpis w katalogu głównym `<application>` węzła. Ten kod będzie obsługiwać klasy aplikacji, utworzonej w swojej aplikacji.

```xml
    <application
        android:name=".MyFirstApp"
        ...
    </application>
```

Ponownie `app\java\<PackageName>\MainActivity`, Dodaj następujące instrukcje importu do niego:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=33-40&highlight=2-8)]

Następnie dodaj następujące zmienne Członkowskie w swojej `MainActivity` klasy:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=57-60&highlight=3-4)]

Następnie Dodajmy następujące `initializeSession()` metody w ramach Twojej `mainActivity` klasy. Po wywołaniu zapewni sesję kotwic przestrzenne Azure zostanie utworzony i został poprawnie zainicjowany podczas uruchamiania aplikacji.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=89-97,146)]

Teraz umożliwia utworzenie punktu zaczepienia usługi `initializeSession()` metody do Twojej `onCreate()` metody. Ponadto firma Microsoft będzie upewnij się, że ramki na podstawie aparatu źródła danych są wysyłane do zestawu SDK usługi Azure przestrzenne kotwic do przetwarzania.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=68-85&highlight=9-17)]

Na koniec Dodaj następujący kod do Twojego `handleTap()` metody. Wstawi lokalnego zakotwiczenia przestrzenne Azure go na kulę czarny, który możemy umieszcza się w świecie rzeczywistym.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=150-158,170-182,198-199&highlight=12-13)]

[Ponowne wdrażanie](#trying-it-out) aplikację ponownie. Poruszanie się urządzenie, naciśnij ekran i umieścić czarne kuli. Tym razem jednak kodu będziesz tworzyć i dołączanie kotwica przestrzenne lokalnej platformy Azure do swojej dziedzinie.

Przed kontynuowaniem pracy, musisz tworzyć kotwic przestrzenne Azure konta identyfikator i klucz, jeśli nie masz jeszcze je. Postępuj zgodnie z następującą sekcję, aby je uzyskać.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="upload-your-local-anchor-into-the-cloud"></a>Przekaż zakotwiczenia lokalnych do chmury

Po utworzeniu swoje konto Azure przestrzenne kotwic identyfikator i klucz, firma Microsoft może przechodzenie wstecz w `app\java\<PackageName>\MainActivity`, Dodaj następujące instrukcje importu do niego:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=40-45&highlight=3-6)]

Następnie dodaj następujące zmienne Członkowskie w swojej `MainActivity` klasy:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=60-65&highlight=3-6)]

Teraz Dodaj następujący kod do Twojego `initializeSession()` metody. Po pierwsze, ten kod pozwoli aplikację, aby monitorować postęp czy przestrzenne kotwic zestawu SDK usługi Azure sprawia, że klatek zbiera dane z aparatu fotograficznego źródła danych. Samo, jak kolor swojej dziedzinie rozpocznie się zmiana z jego oryginalnym czarny, szary. Następnie go spowoduje wyłączenie białe zebrane wystarczającej liczby klatek przesłać zakotwiczenia w chmurze. Po drugie ten kod zapewni poświadczenia wymagane do komunikacji z zaplecza chmury. Poniżej przedstawiono, gdzie należy skonfigurować aplikację do używania Twojego konta identyfikatora i klucza. Skopiowane do edytora tekstu po [Definiowanie zasobów przestrzenne kotwic](#create-a-spatial-anchors-resource).

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=89-120,142-146&highlight=11-36)]

Następnie dodaj następujące `uploadCloudAnchorAsync()` metody w ramach Twojej `mainActivity` klasy. Po wywołaniu tej metody asynchronicznie czeka na zakończenie wystarczającej liczby klatek są zbierane z urządzenia. Zaraz po takim przełączą kolor swojej dziedzinie na żółty, a następnie rozpocznie się przekazywanie lokalnego zakotwiczenia przestrzenne platformy Azure w chmurze. Po ukończeniu przekazywania kod zwróci identyfikatorem kotwicy.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=uploadCloudAnchorAsync)]

Na koniec spróbujmy dołączyć wszystko ze sobą. W swojej `handleTap()` metody, Dodaj następujący kod. Będzie go wywoływać z `uploadCloudAnchorAsync()` metody, jak Twoje Kula jest tworzony. Gdy metoda zwraca kod poniżej wykona jedną finalna wersja aktualizacji update do Twojej Kula, zmieniając jego kolor na niebieski.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=150-158,170-199&highlight=24-37)]

[Ponowne wdrażanie](#trying-it-out) aplikację ponownie. Poruszanie się urządzenie, naciśnij ekran i umieścić w swojej dziedzinie. Tym razem jednak Twoje kuli ulegnie zmianie jego kolor z czarny na biały, jak ramek aparat są zbierane. Gdy będziemy już mieć wystarczającej liczby klatek, kuli zmieni się na żółty, a rozpocznie się przekazywanie chmury. Po zakończeniu przekazywania Twojego kuli spowoduje wyłączenie niebieski. Opcjonalnie można także użyć `Logcat` okna w programie Android Studio do monitorowania komunikatów dziennika Twoja aplikacja wysyła. Na przykład postęp sesji horyzoncie przechwytuje i identyfikatorem kotwicy chmury zwraca raz przekazywanie zostało zakończone.

## <a name="locate-your-cloud-spatial-anchor"></a>Znajdź zakotwiczenia przestrzenne chmury

Jeden zakotwiczenia zostanie przekazany do chmury, jesteśmy gotowi próby lokalizowania go ponownie. Najpierw Dodajmy następujące instrukcje importu do kodu.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=45-48&highlight=3-4)]

Następnie możemy dodać następujący kod do Twojego `handleTap()` metody. Ten kod wykonują następujące czynności:

- Usuń naszych istniejących kuli niebieski na ekranie.
- Zainicjuj ponownie naszym sesji kotwic przestrzenne platformy Azure. Ta akcja zagwarantuje, że zakotwiczenia, którą Spróbujemy znaleźć pochodzi z chmury, zamiast lokalnego zakotwiczenia, którą utworzyliśmy.
- Wysłać zapytanie zakotwiczenia, że przekazany do chmury.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=handleTap&highlight=10-19)]

Teraz możemy dołączyć kod, który zostanie wywołany, gdy znajduje się zakotwiczenia, firma Microsoft jest wykonywanie zapytań dotyczących. Wewnątrz swojej `initializeSession()` metody, Dodaj następujący kod. Ten fragment kodu będzie & Utwórz umieść zielony kuli po znajduje się zakotwiczenia przestrzenne chmury. Jego można również włączyć ponownie, naciskając pozycję ekranu, dzięki czemu można powtórzyć raz całego scenariusza: tworzenie innego lokalnego zakotwiczenia, przekaż go i ponownie go zlokalizować.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=initializeSession&highlight=34-53)]

Gotowe. [Ponowne wdrażanie](#trying-it-out) jednej aplikacji ostatni raz, aby wypróbować całego scenariusza typu end to end. Poruszanie się urządzenia i umieścić swoje czarne kuli. Następnie Zachowaj przenoszenia urządzeniem, aby przechwycić ramki, aparat fotograficzny, dopóki kuli zmieni kolor na żółty. Zakotwiczenia lokalnych zostaną przekazane i swojej dziedzinie spowoduje wyłączenie niebieski. Na koniec ponownie naciśnij ekranu tak, aby lokalne zakotwiczenia jest usuwany, a następnie firma Microsoft będzie szukać jego odpowiednika w chmurze. Kontynuuj poruszanie się w urządzeniu aż do zlokalizowania zakotwiczenia przestrzenne chmury. Zielony kuli powinien pojawić się we właściwym miejscu i może przepłukać i powtórz cały scenariusz, ponownie.

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-new-android-app-finished.md)]
