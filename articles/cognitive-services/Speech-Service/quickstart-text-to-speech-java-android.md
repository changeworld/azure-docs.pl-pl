---
title: 'Szybki Start: synteza mowy, Java (Android) — usługa mowy'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak przeprowadzić funkcję syntezy mowy w języku Java w systemie Android przy użyciu zestawu Speech SDK
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/19/2019
ms.author: yulili
ms.openlocfilehash: 17fb0c1a4ba5495506ceb658eb61d0ac2959caf8
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71804041"
---
# <a name="quickstart-synthesize-speech-in-java-on-android-by-using-the-speech-sdk"></a>Szybki Start: synteza mowy w języku Java w systemie Android przy użyciu zestawu Speech SDK

Przewodniki Szybki Start są również dostępne dla [funkcji rozpoznawania mowy](quickstart-java-android.md) i [wirtualnego asystenta głosowego](quickstart-virtual-assistant-java-android.md).

W tym artykule dowiesz się, jak opracowywać aplikację Java dla systemu Android przy użyciu zestawu Speech SDK Cognitive Services, aby wyszukiwać mowę z tekstu.
Aplikacja jest oparta na pakiecie Maven zestawu mowy SDK, w wersji 1.7.0 i Android Studio 3,3.
Zestaw Speech SDK jest obecnie zgodny z urządzeniami z systemem Android oraz 32 lub 64-bitowymi procesorami ARM i procesorami x86 lub x64 firmy Intel.

> [!NOTE]
> W przypadku zestawu Speech Devices SDK i urządzenia Roobo zobacz [Speech Devices SDK](speech-devices-sdk.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki Start, potrzebujesz klucza subskrypcji usługi Speech Services. Możesz go uzyskać bezpłatnie. Aby uzyskać szczegółowe informacje [, zobacz Wypróbuj bezpłatne usługi mowy](get-started.md) .

## <a name="create-and-configure-a-project"></a>Tworzenie i konfigurowanie projektu

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-android-create-proj.md)]

## <a name="create-user-interface"></a>Tworzenie interfejsu użytkownika

Utworzymy podstawowy interfejs użytkownika dla aplikacji. Edytuj układ dla głównego działania, `activity_main.xml`. Początkowo układ zawiera pasek tytułu z nazwą aplikacji i element TextView zawierający tekst „Hello World!”.

1. Kliknij element TextView. Zmień jego atrybut ID w prawym górnym rogu, aby `outputMessage`, i przeciągnij go na dolny ekran. Usuń jego tekst.

1. Z palety w lewym górnym rogu okna `activity_main.xml` przeciągnij przycisk do pustego miejsca nad tekstem.

1. W atrybutach przycisku po prawej stronie w polu wartości atrybutu `onClick` wprowadź `onSpeechButtonClicked`. Napiszemy metodę o tej nazwie do obsługi zdarzenia przycisku.  Zmień atrybut ID w prawym górnym rogu na `button`.

1. Przeciągnij zwykły tekst do obszaru powyżej przycisku; Zmień jego atrybut ID na `speakText` i Zmień atrybut text na `Hi there!`.

1. Użyj ikony różdżki u góry projektanta na potrzeby wnioskowania ograniczeń układu.


    ![Zrzut ekranu przedstawiający ikonę różdżki](media/sdk/qs-java-android-10-infer-layout-constraints.png)

Tekst i graficzna reprezentacja interfejsu użytkownika powinny teraz wyglądać następująco:

![](media/sdk/qs-java-android-11-2-tts-gui.png)

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/java-android/app/src/main/res/layout/activity_main.xml)]

## <a name="add-sample-code"></a>Dodawanie przykładowego kodu

1. Otwórz plik źródłowy `MainActivity.java`. Zastąp cały zawarty w tym pliku kod poniższym kodem.

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/java-android/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * Metoda `onSpeechButtonClicked` oznacza, jak wspomniano wcześniej, procedurę obsługi naciskania przycisku. Naciśnięcie przycisku uruchamia funkcję synteza mowy.

1. W tym samym pliku zastąp ciąg `YourSubscriptionKey` kluczem subskrypcji.

1. Zastąp także ciąg `YourServiceRegion` [regionem](regions.md) skojarzonym z subskrypcją (na przykład `westus` w przypadku subskrypcji bezpłatnej wersji próbnej).

## <a name="build-and-run-the-app"></a>Kompilowanie i uruchamianie aplikacji

1. Połącz urządzenie z systemem Android do komputera projektowego. Upewnij się, że na urządzeniu włączono [tryb projektowania i debugowanie USB](https://developer.android.com/studio/debug/dev-options). Alternatywnie możesz utworzyć [emulator systemu Android](https://developer.android.com/studio/run/emulator).

1. Aby skompilować aplikację, naciśnij klawisze Ctrl + F9 lub wybierz na pasku menu pozycje **Build** > **Make Project** (Kompilacja) (Utwórz projekt).

1. Aby uruchomić aplikację, naciśnij klawisze Shift + F10 lub wybierz pozycje **Run** > **Run 'app'** (Uruchom) (Uruchom „aplikację”).

1. W wyświetlonym oknie cel wdrożenia wybierz urządzenie z systemem Android lub emulator.

   ![Zrzut ekranu okna Deployment Target (Cel wdrożenia)](media/sdk/qs-java-android-12-deploy.png)

Wprowadź tekst i naciśnij przycisk w aplikacji, aby rozpocząć sekcję synteza mowy. Na ekranie zostanie wyświetlona informacja o syntezie dźwiękowej z domyślnego głośnika i zobaczysz informacje o `speech synthesis succeeded`.

![Zrzut ekranu aplikacji Android](media/sdk/qs-java-android-13-2-gui-on-device-tts.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami dla języka Java w usłudze GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Zobacz także

- [Dostosuj czcionki głosowe](how-to-customize-voice-font.md)
- [Rejestruj przykłady głosu](record-custom-voice-samples.md)
