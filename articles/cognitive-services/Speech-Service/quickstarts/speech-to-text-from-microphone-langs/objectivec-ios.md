---
title: 'Szybki start: rozpoznawanie mowy z mikrofonu, Objective-C - Usługa mowy'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak rozpoznać mowę w języku Objective-C w systemu iOS przy użyciu sdk mowy
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/23/2019
ms.author: chlandsi
ms.openlocfilehash: c1246b19670a18f8dadc0c5e1c64dd5af4c1b210
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75380783"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-ios-by-using-the-speech-sdk"></a>Szybki start: rozpoznawanie mowy w języku Objective-C w uos systemu iOS przy użyciu sdk mowy

Szybkie starty są również dostępne do [syntezy mowy.](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech-langs/objectivec-ios.md)

W tym artykule dowiesz się, jak utworzyć aplikację systemu iOS w języku Objective-C przy użyciu narzędzia Azure Cognitive Services Speech SDK do transkrypcji mowy na tekst z mikrofonu lub z pliku z nagranym dźwiękiem.

## <a name="prerequisites"></a>Wymagania wstępne

Zanim zaczniesz, będziesz potrzebować:

* [Klucz subskrypcji](~/articles/cognitive-services/Speech-Service/get-started.md) usługi Mowy.
* Urządzenie z systemem macOS z [kodem Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) lub nowszym.
* Obiekt docelowy ustawiony na system iOS w wersji 9.3 lub nowszej.

## <a name="get-the-speech-sdk-for-ios"></a>Pobierz zestaw SDK usługi Mowa dla systemu iOS

[!INCLUDE [License notice](~/includes/cognitive-services-speech-service-license-notice.md)]

SDK mowy usług Cognitive Services dla systemu iOS jest obecnie dystrybuowany jako struktura kakaowca.
Można go pobrać z [tej strony internetowej](https://aka.ms/csspeech/iosbinary). Pobierz plik do katalogu macierzystego.

## <a name="create-an-xcode-project"></a>Tworzenie projektu Xcode

Uruchom xcode i rozpocznij nowy projekt, wybierając **pozycję Plik** > **nowego** > **projektu**.
W oknie dialogowym wyboru szablonu wybierz szablon aplikacji pojedynczego widoku systemu **iOS.**

W następnych oknach dialogowych dokonaj następujących wyborów.

1. W oknie dialogowym **Opcje projektu:**
    1. Wprowadź nazwę aplikacji szybki start, na przykład *helloworld*.
    1. Wprowadź odpowiednią nazwę organizacji i identyfikator organizacji, jeśli masz już konto dewelopera firmy Apple. Do celów testowych należy użyć nazwy takiej jak *testorg*. Aby zarejestrować aplikację, musisz mieć odpowiedni profil aprowizacji. Aby uzyskać więcej informacji, zobacz [witrynę dewelopera firmy Apple](https://developer.apple.com/).
    1. Upewnij **się, że objective-C** jest wybrany jako język dla projektu.
    1. Wyczyść wszystkie pola wyboru dla testów i podstawowych danych.

    ![Ustawienia projektu](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-project-settings.png)

1. Wybierz katalog projektu:
   1. Wybierz katalog macierzysty, aby umieścić w nim projekt. Ten krok tworzy katalog helloworld w katalogu domowym, który zawiera wszystkie pliki dla projektu Xcode.
   1. Wyłącz tworzenie repozytorium Git dla tego przykładowego projektu.
   1. Dostosuj ścieżki do SDK na ekranie ustawień projektu.
      1. Na karcie **Ogólne** pod nagłówkiem **Osadzone pliki binarne** dodaj bibliotekę SDK jako platformę, wybierając pozycję **Dodaj osadzone pliki binarne** > **Dodaj inne**. Przejdź do katalogu domowego i `MicrosoftCognitiveServicesSpeech.framework`wybierz plik . Ta akcja automatycznie dodaje bibliotekę SDK do nagłówka **Połączone framework i biblioteki.**
         ![Dodano strukturę](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-framework.png)
      1. Przejdź do karty **Ustawienia kompilacji** i wybierz ustawienie **Wszystkie.**
      1. Dodaj katalog $(SRCROOT)/.. do **ścieżek wyszukiwania w ramach** w nagłówku Ścieżki **wyszukiwania.**

      ![Ustawienie Ścieżki wyszukiwania w ramach](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-framework-search-paths.png)

## <a name="set-up-the-ui"></a>Konfigurowanie Interfejsu użytkownika

Przykładowa aplikacja ma bardzo prosty interfejs użytkownika. Posiada dwa przyciski, aby rozpocząć rozpoznawanie mowy z pliku lub z wejścia mikrofonu i etykiety tekstowej, aby wyświetlić wynik. Interfejs użytkownika jest skonfigurowany w części `Main.storyboard` projektu. Otwórz widok XML scenorysu, klikając `Main.storyboard` prawym przyciskiem myszy pozycję wpisu drzewa projektu i wybierając polecenie **Otwórz jako** > **kod źródłowy**.

Zastąp wygenerowany automatycznie kod XML następującym kodem:

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/from-microphone/helloworld/helloworld/Base.lproj/Main.storyboard)]

## <a name="add-the-sample-code"></a>Dodawanie przykładowego kodu

1. Pobierz [przykładowy plik wav,](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-speech-sdk/f9807b1079f3a85f07cbb6d762c6b5449d536027/samples/cpp/windows/console/samples/whatstheweatherlike.wav) klikając prawym przyciskiem myszy łącze i wybierając **pozycję Zapisz obiekt docelowy jako**.
   Dodaj plik wav do projektu jako zasób, przeciągając go z okna programu Finder do poziomu głównego widoku projektu.
   Wybierz **pozycję Zakończ** w następującym oknie dialogowym bez zmiany ustawień.
1. Zastąp zawartość automatycznie `ViewController.m` generowanego pliku następującym kodem:

   [!code-objectivec[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/from-microphone/helloworld/helloworld/ViewController.m#code)]
1. Zastąp ciąg `YourSubscriptionKey` kluczem subskrypcji.
1. Zastąp ciąg `YourServiceRegion` [regionem](~/articles/cognitive-services/Speech-Service/regions.md) skojarzonym z subskrypcją. Na przykład `westus` użyj bezpłatnej subskrypcji próbnej.
1. Dodaj żądanie, aby uzyskać dostęp do mikrofonu. Kliknij prawym `Info.plist` przyciskiem myszy wpis drzewa projektu i wybierz polecenie **Otwórz jako** > **kod źródłowy**. Dodaj następujące wiersze `<dict>` do sekcji, a następnie zapisz plik.

    ```xml
    <key>NSMicrophoneUsageDescription</key>
    <string>Need microphone access for speech recognition from microphone.</string>
    ```

## <a name="build-and-run-the-sample"></a>Kompilowanie i uruchamianie przykładu

1. Ujrzaj dane wyjściowe debugowania, wybierając **pozycję Wyświetl** > obszar > **debugowania****Aktywuj konsolę**.
1. Wybierz symulator systemu iOS lub urządzenie z systemem iOS podłączone do komputera dewelopera jako miejsce docelowe aplikacji z listy w menu**Miejsce docelowe** **produktu.** > 
1. Skompiluj i uruchom przykładowy kod w symulatorze systemu iOS, wybierając z menu**przebieg** **produktu.** >  Można również wybrać przycisk **Odtwórz.**
1. Po wybraniu przycisku **Rozpoznaj (Plik)** w aplikacji powinna zostać wyświetlena zawartość pliku audio "Jaka jest pogoda?" w dolnej części ekranu.

   ![Symulowana aplikacja na iOS](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-simulated-app.png)

1. Po wybraniu przycisku **Rozpoznaj (Mikrofon)** w aplikacji i wyponieniu kilku słów, w dolnej części ekranu powinien zostać wyświetlony tekst, który został wypowiedziany.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami dla języka Objective-C w serwisie GitHub](https://aka.ms/csspeech/samples)
