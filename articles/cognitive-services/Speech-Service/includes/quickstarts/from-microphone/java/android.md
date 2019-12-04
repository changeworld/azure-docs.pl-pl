---
title: 'Szybki Start: Rozpoznawanie mowy z mikrofonu, Java (Android)-Speech Service'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak rozpoznawać mowę w języku Java w systemie Android przy użyciu zestawu Speech SDK
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 11/05/2019
ms.author: wolfma
ms.openlocfilehash: 3e4045345f8dce54067aebf559bb2554b65f13b3
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74795772"
---
## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem:

> [!div class="checklist"]
> * [Tworzenie zasobu usługi Azure Speech](../../../../get-started.md)
> * [Konfigurowanie środowiska deweloperskiego](../../../../quickstarts/setup-platform.md?tabs=android)
> * Upewnij się, że masz dostęp do mikrofonu do przechwytywania audio

## <a name="create-a-user-interface"></a>Tworzenie interfejsu użytkownika

Teraz utworzysz podstawowy interfejs użytkownika dla aplikacji. Edytuj układ dla głównego działania, `activity_main.xml`. Początkowo układ obejmuje pasek tytułu z nazwą aplikacji oraz TextView, który zawiera tekst "Hello world!".

* Wybierz element TextView. Zmień atrybut ID w prawym górnym rogu na `hello`.

* Z palety w lewym górnym rogu okna `activity_main.xml` przeciągnij przycisk do pustego miejsca powyżej tekstu.

* W atrybutach przycisku po prawej stronie w polu wartości atrybutu `onClick` wprowadź `onSpeechButtonClicked`. Napiszemy metodę o tej nazwie do obsługi zdarzenia przycisku. Zmień atrybut ID w prawym górnym rogu na `button`.

* Użyj ikony różdżki u góry projektanta na potrzeby wnioskowania ograniczeń układu.

  ![Zrzut ekranu przedstawiający ikonę różdżki](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-10-infer-layout-constraints.png)

Tekst i graficzna reprezentacja interfejsu użytkownika powinny teraz wyglądać następująco:

![Interfejs użytkownika](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-11-gui.png)

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java/android/from-microphone/app/src/main/res/layout/activity_main.xml)]

## <a name="add-sample-code"></a>Dodawanie przykładowego kodu

1. Otwórz plik źródłowy `MainActivity.java`. Zastąp cały kod w tym pliku następującym:

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/android/from-microphone/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * Metoda `onCreate` zawiera kod, który żąda uprawnień do mikrofonu i Internetu oraz inicjuje powiązanie z platformą natywną. Konfigurowanie powiązań z platformą natywną jest wymagane tylko raz. Należy to zrobić na początku, podczas inicjowania aplikacji.

   * Metoda `onSpeechButtonClicked` oznacza, jak wspomniano wcześniej, procedurę obsługi naciskania przycisku. Naciśnięcie przycisku powoduje wyzwolenie transkrypcji zamiany mowy na tekst.

1. W tym samym pliku zastąp ciąg `YourSubscriptionKey` kluczem subskrypcji.

1. Zastąp również ciąg `YourServiceRegion` z [regionem](~/articles/cognitive-services/Speech-Service/regions.md) skojarzonym z subskrypcją. Na przykład użyj `westus` subskrypcji bezpłatnej wersji próbnej.

## <a name="build-and-run-the-app"></a>Kompilowanie i uruchamianie aplikacji

1. Połącz urządzenie z systemem Android do komputera projektowego. Upewnij się, że włączono [tryb programowania i debugowanie USB](https://developer.android.com/studio/debug/dev-options) na urządzeniu.

1. Aby skompilować aplikację, wybierz kombinację klawiszy CTRL + F9 lub wybierz opcję **kompiluj** > **Utwórz projekt** na pasku menu.

1. Aby uruchomić aplikację, wybierz polecenie SHIFT + F10 lub wybierz polecenie **uruchom** > **Uruchom "App"** .

1. W wyświetlonym oknie cel wdrożenia wybierz urządzenie z systemem Android.

   ![Zrzut ekranu okna Deployment Target (Cel wdrożenia)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-12-deploy.png)

Wybierz przycisk w aplikacji, aby rozpocząć sekcję rozpoznawania mowy. Kolejne 15-sekundowe głosowanie mowy zostanie wysłane do usług mowy i uzyskanego. Wynik zostanie wyświetlony w aplikacji Android i w oknie programu logcat w programie Android Studio.

![Zrzut ekranu aplikacji Android](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-13-gui-on-device.png)

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [footer](./footer.md)]

