---
title: 'Szybki Start: Rozpoznawanie mowy w języku Objective C w systemie iOS przy użyciu Cognitive Services SDK rozpoznawania mowy'
titleSuffix: Microsoft Cognitive Services
description: Dowiedz się, jak rozpoznawanie mowy w języku Objective C w systemie iOS przy użyciu Cognitive Services SDK rozpoznawania mowy
services: cognitive-services
author: chlandsi
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 09/24/2018
ms.author: chlandsi
ms.openlocfilehash: e343c24a5ef223e1fd6dc618f41d4acf89fc2f5d
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2018
ms.locfileid: "47226028"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-ios-using-the-cognitive-services-speech-sdk"></a>Szybki Start: Rozpoznawanie mowy w języku Objective C w systemie iOS przy użyciu Cognitive Services SDK rozpoznawania mowy

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

W tym artykule dowiesz się, jak utworzyć aplikację dla systemu iOS w języku Objective-C przy użyciu Cognitive Services SDK mowy także plik audio z zarejestrowanych zamiana mowy na tekst.

## <a name="prerequisites"></a>Wymagania wstępne

* Klucz subskrypcji dla usługi mowy. Zobacz [bezpłatnie wypróbować usługę rozpoznawania mowy](get-started.md).
* Mac za pomocą edytora Xcode 9.4.1 zainstalowany jako środowisko programistyczne dla systemu iOS. Ten samouczek jest przeznaczony dla systemu iOS w wersji 11.4. Jeśli nie masz programu Xcode, ale można zainstalować go z [App Store](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12).

## <a name="get-the-speech-sdk-for-ios"></a>Pobierz zestaw SDK rozpoznawania mowy dla systemu iOS

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Bieżąca wersja zestawu SDK mowy usługi Cognitive Services to `1.0.0`.

Cognitive Services mowy zestawu SDK dla systemów Mac i iOS aktualnie jest rozpowszechniany jako struktura Cocoa.
Można go pobrać ze https://aka.ms/csspeech/iosbinary. Pobierz plik do katalogu macierzystego.


## <a name="create-an-xcode-project"></a>Tworzenie projektu Xcode 

Uruchom program Xcode i rozpocząć nowy projekt, klikając **pliku** > **New** > **projektu**.
W oknie dialogowym wyboru szablonu, wybierz "dla systemu iOS w jednym widoku aplikacji" szablonu.

W oknach dialogowych, należy wykonać, wybierz następujące opcje:

1. Okno dialogowe Opcje projektu
    1. Wprowadź nazwę aplikacji Szybki Start, na przykład `helloworld`.
    1. Wprowadź nazwę odpowiedniej organizacji i identyfikator organizacji, jeśli masz już konto programu Apple developer. Do celów testowych możesz po prostu wybrać dowolną nazwę, takich jak `testorg`. Aby zarejestrować aplikację, należy również prawidłowego profilu aprowizacji za. Zapoznaj się [witryny dla deweloperów firmy Apple](https://developer.apple.com/) Aby uzyskać szczegółowe informacje.
    1. Upewnij się, że języka Objective-C jest wybierany jako język dla projektu.
    1. Wyłącz wszystkie pola wyboru dla danych podstawowych i testów.
    ![Ustawienia projektu](media/sdk/qs-objectivec-project-settings.png)
1. Wybierz katalog projektu
    1. Wybierz umieszczenie projektu w katalogu macierzystym. Spowoduje to utworzenie `helloworld` katalogu w katalogu macierzystym, który zawiera wszystkie pliki w projekcie Xcode.
    1. Wyłącz tworzenie repozytorium Git dla tego projektu w przykładzie.
    1. Dostosuj ścieżki do zestawu SDK w *ustawienia projektu*.
        1. W **ogólne** karcie **osadzone pliki binarne** nagłówka, Dodaj bibliotekę zestawu SDK jako struktura: **Dodaj osadzone pliki binarne** > **dodać inne ...**  > Przejdź do katalogu macierzystego i wybierz plik `MicrosoftCognitiveServicesSpeech.framework`. To również automatycznie doda Biblioteka zestawu SDK do nagłówka **połączone struktury i biblioteki**.
        ![Dodano Framework](media/sdk/qs-objectivec-framework.png)
        1. Przejdź do **ustawieniach kompilacji** kartę i Aktywuj **wszystkich** ustawienia.
        1. Dodaj katalog `$(SRCROOT)/..` do *ścieżki wyszukiwania struktur* w obszarze **ścieżki wyszukiwania** nagłówka.
        ![Ustawienia ścieżki wyszukiwania Framework](media/sdk/qs-objectivec-framework-search-paths.png)


## <a name="set-up-the-ui"></a>Konfiguruj interfejs użytkownika

Przykładowa aplikacja będzie mieć bardzo prosty interfejs użytkownika: przycisk, aby rozpocząć przetwarzanie pliku i etykietę tekstową, aby wyświetlić wynik.
Interfejs użytkownika jest skonfigurowana w `Main.storyboard` wchodzi w skład projektu.
Otwórz Widok XML scenorysu, klikając prawym przyciskiem myszy `Main.storyboard` wpis projektu drzewa i wybierając polecenie **Otwórz jako...**   >  **Kod źródłowy**.
Zastąp wygenerowany automatycznie XML to:

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-ios/helloworld/helloworld/Base.lproj/Main.storyboard)]

## <a name="add-the-sample-code"></a>Dodaj kod przykładowy

1. Pobierz [przykładowy plik wav](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-speech-sdk/f9807b1079f3a85f07cbb6d762c6b5449d536027/samples/cpp/windows/console/samples/whatstheweatherlike.wav) , kliknij łącze prawym przyciskiem myszy i wybierając pozycję **Zapisz element docelowy jako...** . Dodaj plik wav do projektu jako zasób, przeciągając go z okna wyszukiwania na poziomie głównym w widoku projektu.
Kliknij przycisk **Zakończ** w oknie dialogowym poniżej bez modyfikowania ustawień.
1. Zastąp zawartość wygenerowany automatycznie `ViewController.m` pliku przez:

   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-ios/helloworld/helloworld/ViewController.m#code)]
1. Dodaj żądanie, aby uzyskać dostęp do mikrofonu. Kliknij prawym przyciskiem myszy wpis w pliku info.plist drzewa projektu i wybierz **Otwórz jako...**   >  **Kod źródłowy**. Dodaj następujące wiersze do `<dict>` sekcji, a następnie zapisz plik.
    ```xml
    <key>NSMicrophoneUsageDescription</key>

    <string>Need microphone access for speech recognition from microphone.</string>
    ```
1. Zastąp ciąg `YourSubscriptionKey` z kluczem subskrypcji.
1. Zastąp ciąg `YourServiceRegion` z [region](regions.md) skojarzonych z subskrypcją (na przykład `westus` bezpłatnej subskrypcji wersji próbnej).


## <a name="building-and-running-the-sample"></a>Tworzenie i uruchamianie przykładu

1. Uwidacznianie danych wyjściowych debugowania (**widoku** > **debugowania obszaru** > **aktywować konsoli**).
1. Kompilowanie i uruchamianie przykładowego kodu w symulatorze systemu iOS, wybierając **produktu** -> **Uruchom** z menu lub klikając **Odtwórz** przycisku. Aby uruchomić na urządzeniu z systemem iOS, podłącz urządzenie do komputera deweloperskiego, a następnie wybierz urządzenie, jako cel uruchomionego. Zestaw SDK rozpoznawania mowy aktualnie obsługuje tylko 64-bitowej platformy systemu iOS.
1. Po kliknięciu pozycji "Rozpoznaj!" znajdujący się w aplikacji, powinna zostać wyświetlona zawartość audio pliku "Nowości pogodowych, np." w dolnej części ekranu.

 ![Symulowane aplikacji dla systemu iOS](media/sdk/qs-objectivec-simulated-app.png)

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Poszukaj tego przykładu w `quickstart/objectivec-ios` folderu.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Pobierz nasze przykłady](speech-sdk.md#get-the-samples)
