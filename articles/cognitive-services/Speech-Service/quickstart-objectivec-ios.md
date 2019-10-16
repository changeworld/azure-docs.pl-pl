---
title: 'Szybki Start: Rozpoznawanie mowy, obiektyw-C-Speech Service'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak rozpoznawać mowę w celu języka C w systemie iOS przy użyciu zestawu Speech SDK
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: chlandsi
ms.openlocfilehash: f943f47cdef901f80aa455d3d1e02a753e0f06e4
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72327757"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-ios-by-using-the-speech-sdk"></a>Szybki Start: Rozpoznawanie mowy w celu języka C w systemie iOS przy użyciu zestawu Speech SDK

Przewodniki Szybki Start są również dostępne dla [syntezy mowy](quickstart-text-to-speech-objectivec-ios.md).

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

W tym artykule dowiesz się, jak utworzyć aplikację dla systemu iOS w celu założenia języka C przy użyciu zestawu Azure Cognitive Services Speech SDK, aby transkrypcja mowę na tekst z mikrofonu lub z pliku z zarejestrowanym dźwiękiem.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy:

* [Klucz subskrypcji](get-started.md) usługi mowy.
* Maszyna macOS z [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) lub nowszą.
* Element docelowy jest ustawiony na system iOS w wersji 9,3 lub nowszej.

## <a name="get-the-speech-sdk-for-ios"></a>Pobierz zestaw SDK usługi Mowa dla systemu iOS

[!INCLUDE [License notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Zestaw Cognitive Services Speech SDK dla systemu iOS jest obecnie dystrybuowany jako platforma kakao.
Można go pobrać z [tej witryny sieci Web](https://aka.ms/csspeech/iosbinary). Pobierz plik do katalogu macierzystego.

## <a name="create-an-xcode-project"></a>Tworzenie projektu Xcode

Rozpocznij Xcode i Rozpocznij nowy projekt, wybierając pozycję **plik** > **Nowy** > **projektu**.
W oknie dialogowym Wybieranie szablonu wybierz szablon aplikacji z **pojedynczym widokiem systemu iOS** .

W poniższych oknach dialogowych wybierz następujące opcje.

1. W oknie dialogowym **Opcje projektu** :
    1. Wprowadź nazwę aplikacji szybkiego startu, na przykład *HelloWorld*.
    1. Wprowadź odpowiednią nazwę organizacji i identyfikator organizacji, jeśli masz już konto dewelopera firmy Apple. Do celów testowych Użyj nazwy, takiej jak *testorg*. Aby zarejestrować aplikację, musisz mieć odpowiedni profil aprowizacji. Aby uzyskać więcej informacji, zobacz [witrynę dla deweloperów firmy Apple](https://developer.apple.com/).
    1. **Upewnij się, że** zaznaczono jako język dla projektu.
    1. Wyczyść wszystkie pola wyboru dla testów i danych podstawowych.

    ![Ustawienia projektu](media/sdk/qs-objectivec-project-settings.png)

1. Wybierz katalog projektu:
   1. Wybierz katalog macierzysty, aby umieścić w nim projekt. W tym kroku zostanie utworzony katalog HelloWorld w katalogu macierzystym, który zawiera wszystkie pliki projektu Xcode.
   1. Wyłącz tworzenie repozytorium Git dla tego przykładowego projektu.
   1. Dostosuj ścieżki do zestawu SDK na ekranie Ustawienia projektu.
      1. Na karcie **Ogólne** w nagłówku **osadzone pliki binarne** Dodaj bibliotekę zestawu SDK jako platformę, wybierając pozycję **Dodaj osadzone pliki binarne** > **Dodaj inne**. Przejdź do katalogu macierzystego i wybierz plik `MicrosoftCognitiveServicesSpeech.framework`. Ta akcja powoduje automatyczne dodanie biblioteki SDK do **połączonej struktury i bibliotek** nagłówka.
         @no__t — 0Added Framework @ no__t-1
      1. Przejdź do karty **Ustawienia kompilacji** i wybierz ustawienie **wszystkie** .
      1. Dodaj katalog $ (SRCROOT)/... na **ścieżki wyszukiwania struktury** pod nagłówkiem **ścieżki wyszukiwania** .

      ![Ustawienie ścieżek wyszukiwania struktury](media/sdk/qs-objectivec-framework-search-paths.png)

## <a name="set-up-the-ui"></a>Konfigurowanie Interfejsu użytkownika

Przykładowa aplikacja ma bardzo prosty interfejs użytkownika. Ma dwa przyciski do uruchamiania rozpoznawania mowy z pliku lub danych wejściowych mikrofonu oraz etykieta tekstowa, aby wyświetlić wynik. Interfejs użytkownika jest skonfigurowany w części `Main.storyboard` projektu. Otwórz widok XML scenorysu, klikając prawym przyciskiem myszy wpis `Main.storyboard` drzewa projektu i wybierając pozycję **Otwórz jako** **kod źródłowy** > .

Zastąp wygenerowany automatycznie kod XML następującym kodem:

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-ios/helloworld/helloworld/Base.lproj/Main.storyboard)]

## <a name="add-the-sample-code"></a>Dodawanie przykładowego kodu

1. Pobierz [przykładowy plik WAV](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-speech-sdk/f9807b1079f3a85f07cbb6d762c6b5449d536027/samples/cpp/windows/console/samples/whatstheweatherlike.wav) , klikając prawym przyciskiem myszy łącze i wybierając pozycję **Zapisz element docelowy jako**.
   Dodaj plik wav do projektu jako zasób, przeciągając go z okna programu Finder do poziomu głównego widoku projektu.
   Wybierz pozycję **Zakończ** w następującym oknie dialogowym bez zmiany ustawień.
1. Zastąp zawartość automatycznie generowanego pliku `ViewController.m` następującym kodem:

   [!code-objectivec[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-ios/helloworld/helloworld/ViewController.m#code)]
1. Zastąp ciąg `YourSubscriptionKey` kluczem subskrypcji.
1. Zastąp ciąg `YourServiceRegion` z [regionem](regions.md) skojarzonym z subskrypcją. Na przykład w subskrypcji bezpłatnej wersji próbnej Użyj `westus`.
1. Dodaj żądanie, aby uzyskać dostęp do mikrofonu. Kliknij prawym przyciskiem myszy wpis `Info.plist` drzewa projektu i wybierz polecenie **Otwórz jako** > **kod źródłowy**. Dodaj następujące wiersze do sekcji `<dict>`, a następnie Zapisz plik.

    ```xml
    <key>NSMicrophoneUsageDescription</key>
    <string>Need microphone access for speech recognition from microphone.</string>
    ```

## <a name="build-and-run-the-sample"></a>Kompilowanie i uruchamianie przykładu

1. Aby wyświetlić dane wyjściowe debugowania, wybierz pozycję **wyświetl** > **obszar debugowania** > **Aktywuj konsolę**.
1. Wybierz symulator systemu iOS lub urządzenie z systemem iOS podłączone do komputera deweloperskiego jako miejsce docelowe aplikacji z listy w menu**miejsce docelowe** @no__t **produktu**.
1. Kompiluj i uruchamiaj przykładowy kod w symulatorze systemu iOS, wybierając **produkt** > **Run** z menu. Możesz również wybrać przycisk **Odtwórz** .
1. Po wybraniu przycisku **Rozpoznaj (plik)** w aplikacji powinna zostać wyświetlona zawartość pliku audio "co to jest pogoda"? w dolnej części ekranu.

   ![Symulowana aplikacja systemu iOS](media/sdk/qs-objectivec-simulated-app.png)

1. Po wybraniu przycisku **rozpoznawania (mikrofon)** w aplikacji i wpisaniu kilku wyrazów powinien zostać wyświetlony tekst mówiony w dolnej części ekranu.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami dla języka Objective-C w serwisie GitHub](https://aka.ms/csspeech/samples)
