---
title: 'Szybki start: rozpoznawanie mowy w języku Objective-C w systemie IOS przy użyciu zestawu SDK usługi Mowa'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak rozpoznawać mowę w języku Objective-C w systemie IOS przy użyciu zestawu SDK usługi Mowa
services: cognitive-services
author: chlandsi
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 11/06/2018
ms.author: chlandsi
ms.openlocfilehash: 7d1e05e13e55b8b7bc07eda71d63f96f12c81ff9
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2018
ms.locfileid: "51219192"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-ios-using-the-speech-service-sdk"></a>Szybki start: rozpoznawanie mowy w języku Objective-C w systemie IOS przy użyciu zestawu SDK usługi Mowa

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Z tego artykułu dowiesz się, jak utworzyć aplikację języka Objective-C dla systemu iOS przy użyciu zestawu SDK usługi Mowa z usługi Cognitive Services, aby wykonać transkrypcję pliku dźwiękowego z zarejestrowaną mową na tekst.

## <a name="prerequisites"></a>Wymagania wstępne

* Klucz subskrypcji dla usługi Mowa. Zobacz [Wypróbuj bezpłatnie usługę Mowa](get-started.md).
* Komputer Mac z programem Xcode 9.4.1 zainstalowanym jako środowisko programistyczne systemu iOS. Ten samouczek jest przeznaczony dla systemu iOS w wersji 11.4. Jeśli nie masz jeszcze programu Xcode, możesz zainstalować go z witryny [App Store](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12).

## <a name="get-the-speech-sdk-for-ios"></a>Pobierz zestaw SDK usługi Mowa dla systemu iOS

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Bieżąca wersja zestawu Speech SDK usługi Cognitive Services to `1.1.0`.

Zestaw SDK usługi Mowa z zestawu Cognitive Services dla komputerów Mac i systemu iOS jest aktualnie dystrybuowany w ramach platformy Cocoa.
Można go pobrać ze strony https://aka.ms/csspeech/iosbinary. Pobierz plik do katalogu macierzystego.

## <a name="create-an-xcode-project"></a>Tworzenie projektu programu Xcode 

Uruchom program Xcode i rozpocznij nowy projekt, klikając pozycję **File** > **New** > **Project** (Plik > Nowy > Projekt).
W oknie dialogowym wyboru szablonu wybierz szablon „iOS Single View App” (Aplikacja pojedynczego widoku systemu iOS).

W kolejnych oknach dialogowych wybierz następujące opcje:

1. Okno dialogowe Project Options (Opcje projektu)
    1. Podaj nazwę aplikacji szybkiego startu, na przykład `helloworld`.
    1. Podaj odpowiednią nazwę organizacji i identyfikator organizacji, jeśli masz już konto programu Apple Developer. Dla celów testowych możesz po prostu wybrać dowolną nazwę, taką jak `testorg`. Aby zarejestrować aplikację, musisz mieć również odpowiedni profil aprowizacji. Szczegóły zawiera [witryna dla deweloperów firmy Apple](https://developer.apple.com/).
    1. Upewnij się, że jako język projektu wybrano język Objective-C.
    1. Usuń zaznaczenie wszystkich pól wyboru dla testów i danych podstawowych.
    ![Ustawienia projektu](media/sdk/qs-objectivec-project-settings.png)
1. Wybieranie katalog projektu
    1. Wybierz katalog macierzysty, aby umieścić w nim projekt. Spowoduje to utworzenie w katalogu macierzystym katalogu `helloworld`, który zawiera wszystkie pliki projektu programu Xcode.
    1. Wyłącz tworzenie repozytorium Git dla tego przykładowego projektu.
    1. Dostosuj ścieżki do zestawu SDK w pozycji *Project Settings* (Ustawienia projektu).
        1. Na karcie **General** (Ogólne) w ramach nagłówka **Embedded Binaries** (Osadzone pliki binarne) dodaj bibliotekę zestawu SDK jako strukturę: **Add embedded binaries** > **Add other...** > Navigate to your home directory and choose the file`MicrosoftCognitiveServicesSpeech.framework` (Dodaj osadzone pliki binarne > Dodaj inne... > przejdź do katalogu macierzystego i wybierz plik). To spowoduje także automatyczne dodanie biblioteki zestawu SDK do nagłówka **Linked Framework and Libraries** (Połączone struktury i biblioteki).
        ![Dodano strukturę](media/sdk/qs-objectivec-framework.png)
        1. Przejdź do karty **Build Settings** (Ustawienia kompilacji) i aktywuj ustawienia **All** (Wszystko).
        1. Dodaj katalog `$(SRCROOT)/..` do pozycji *Framework Search Paths* (Ścieżki wyszukiwania struktury) w ramach nagłówka **Search Paths** (Ścieżki wyszukiwania).
        ![Ustawienie Framework Search Path (Ścieżka wyszukiwania struktury)](media/sdk/qs-objectivec-framework-search-paths.png)

## <a name="set-up-the-ui"></a>Konfigurowanie Interfejsu użytkownika

Przykładowa aplikacja będzie mieć bardzo prosty interfejs użytkownika: dwa przyciski umożliwiające rozpoczęcie rozpoznawania mowy na podstawie danych pochodzących z pliku lub z mikrofonu i etykietę tekstową na potrzeby wyświetlenia wyniku.
Interfejs użytkownika jest skonfigurowany w części `Main.storyboard` projektu.
Otwórz widok kodu XML scenorysu, klikając prawym przyciskiem myszy pozycję `Main.storyboard` drzewa projektu i wybierając polecenie **Open As...** >  **Source Code** (Otwórz jako... > Kod źródłowy).
Zastąp wygenerowany automatycznie kod XML następującym:

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-ios/helloworld/helloworld/Base.lproj/Main.storyboard)]

## <a name="add-the-sample-code"></a>Dodawanie przykładowego kodu

1. Pobierz [przykładowy plik wav](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-speech-sdk/f9807b1079f3a85f07cbb6d762c6b5449d536027/samples/cpp/windows/console/samples/whatstheweatherlike.wav), klikając link prawym przyciskiem myszy i wybierając pozycję **Save target as...** (Zapisz element docelowy jako...). Dodaj plik wav do projektu jako zasób, przeciągając go z okna programu Finder do poziomu głównego widoku projektu.
Kliknij przycisk **Finish** (Zakończ) w następującym oknie dialogowym bez modyfikowania ustawień.
1. Zastąp zawartość automatycznie wygenerowanego pliku `ViewController.m` następującą zawartością:

   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-ios/helloworld/helloworld/ViewController.m#code)]
1. Zastąp ciąg `YourSubscriptionKey` kluczem subskrypcji.
1. Zastąp ciąg `YourServiceRegion` [regionem](regions.md) skojarzonym z subskrypcją (na przykład `westus` w przypadku subskrypcji bezpłatnej wersji próbnej).
1. Dodaj żądanie, aby uzyskać dostęp do mikrofonu. Kliknij prawym przyciskiem myszy pozycję `Info.plist` w drzewie projektu, a następnie wybierz pozycję **Open As...** >  **Source Code** (Otwórz jako... > Kod źródłowy). Dodaj następujące wiersze do sekcji `<dict>`, a następnie zapisz plik.
    ```xml
    <key>NSMicrophoneUsageDescription</key>
    <string>Need microphone access for speech recognition from microphone.</string>
    ```

## <a name="building-and-running-the-sample"></a>Kompilowanie i uruchamianie przykładu

1. Uwidocznij dane wyjściowe debugowania (**View** > **Debug Area** > **Activate Console** — Widok > Obszar debugowania > Aktywuj konsolę).
1. Wybierz symulator systemu iOS lub urządzenie z systemem iOS podłączone do maszyny deweloperskiej jako lokalizację docelową dla aplikacji z listy w menu **Product** -> **Destination** (Produkt > Cel).
1. Skompiluj i uruchom przykładowy kod w symulatorze systemu iOS, wybierając pozycję **Product** -> **Run** (Produkt > Uruchom) z menu lub klikając przycisk **Play** (Odtwórz).
Obecnie zestaw SDK usługi Mowa obsługuje tylko 64-bitowe platformy systemu iOS.
1. Po kliknięciu przycisku „Recognize (File)” (Rozpoznaj — plik) w aplikacji zawartość pliku dźwiękowego „What's the weather like?” (Jaka jest pogoda?) powinna zostać wyświetlona w dolnej części ekranu.

 ![Symulowana aplikacja systemu iOS](media/sdk/qs-objectivec-simulated-app.png)

1. Po kliknięciu przycisku „Recognize (Microphone)” (Rozpoznaj — mikrofon) w aplikacji i wypowiedzeniu kilku słów wypowiedziany tekst powinien zostać wyświetlony w dolnej części ekranu.

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Poszukaj tego przykładu w folderze `quickstart/objectivec-ios`.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Pobierz nasze przykłady](speech-sdk.md#get-the-samples)

