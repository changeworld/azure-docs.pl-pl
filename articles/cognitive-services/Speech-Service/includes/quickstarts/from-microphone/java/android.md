---
title: 'Szybki start: rozpoznawanie mowy z mikrofonu, Java (Android) - Usługa mowy'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak rozpoznawać mowę w języku Java w systemie Android przy użyciu zestawu Speech SDK
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 11/05/2019
ms.author: wolfma
ms.openlocfilehash: c37a52ea2a7d5f1d3e325c2f6d17bc075f50c5e0
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2020
ms.locfileid: "80671096"
---
## <a name="prerequisites"></a>Wymagania wstępne

Zanim zaczniesz:

> [!div class="checklist"]
> * [Tworzenie zasobu mowy platformy Azure](../../../../get-started.md)
> * [Konfigurowanie środowiska programistycznego](../../../../quickstarts/setup-platform.md?tabs=android&pivots=programming-language-java)
> * Upewnij się, że masz dostęp do mikrofonu do przechwytywania dźwięku

## <a name="create-a-user-interface"></a>Tworzenie interfejsu użytkownika

Teraz utworzymy podstawowy interfejs użytkownika dla aplikacji. Edytuj układ dla głównego działania, `activity_main.xml`. Początkowo układ zawiera pasek tytułu z nazwą aplikacji i TextView, który zawiera tekst "Hello World!".

* Zaznacz element TextView. Zmień atrybut ID w prawym górnym rogu na `hello`.

* Z palety w lewym górnym `activity_main.xml` rogu okna przeciągnij przycisk do pustego miejsca nad tekstem.

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

   * Metoda `onSpeechButtonClicked` oznacza, jak wspomniano wcześniej, procedurę obsługi naciskania przycisku. Naciśnięcie przycisku powoduje transkrypcję mowy na tekst.

1. W tym samym pliku zastąp ciąg `YourSubscriptionKey` kluczem subskrypcji.

1. Należy również `YourServiceRegion` zastąpić ciąg **identyfikatorem Region** z [regionu skojarzonego](https://aka.ms/speech/sdkregion) z subskrypcją. Na przykład `westus` użyj bezpłatnej subskrypcji próbnej.

## <a name="build-and-run-the-app"></a>Kompilowanie i uruchamianie aplikacji

1. Połącz urządzenie z systemem Android do komputera projektowego. Upewnij się, że włączono [tryb programowania i debugowanie USB](https://developer.android.com/studio/debug/dev-options) na urządzeniu.

1. Aby utworzyć aplikację, wybierz ctrl+F9 lub wybierz z paska menu pozycję **Zbuduj** > **projekt.**

1. Aby uruchomić aplikację, wybierz klawisze Shift+F10 lub wybierz **pozycję Uruchom** > **polecenie Uruchom "app"**.

1. W wyświetlonym oknie docelowym wdrożenia wybierz urządzenie z Androidem.

   ![Zrzut ekranu okna Deployment Target (Cel wdrożenia)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-12-deploy.png)

Wybierz przycisk w aplikacji, aby rozpocząć sekcję rozpoznawania mowy. Kolejne 15 sekund mowy w języku angielskim zostanie wysłanych do usługi rozpoznawania mowy i poddanych transkrypcji. Wynik zostanie wyświetlony w aplikacji Android i w oknie programu logcat w programie Android Studio.

![Zrzut ekranu aplikacji Android](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-13-gui-on-device.png)

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [footer](./footer.md)]

