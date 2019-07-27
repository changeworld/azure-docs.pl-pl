---
title: 'Szybki start: Rozpoznawanie mowy, usługa SWIFT-mowę'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak rozpoznać mowę w usłudze SWIFT w systemie macOS przy użyciu zestawu Speech SDK
services: cognitive-services
author: cbasoglu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 06/28/2019
ms.author: cbasoglu
ms.openlocfilehash: a952d6ea46f601cc0bc97e3b0af7343defe19f68
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559273"
---
# <a name="quickstart-recognize-speech-in-swift-on-macos-using-the-speech-sdk"></a>Szybki start: Rozpoznawanie mowy w programie SWIFT w systemie macOS przy użyciu zestawu Speech SDK

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

W tym artykule dowiesz się, jak utworzyć aplikację macOS w usłudze SWIFT przy użyciu zestawu SDK mowy Cognitive Services, aby transkrypcja mowę zarejestrowano z mikrofonu do tekstu.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem pracy zapoznaj się z poniższą listą wymagań wstępnych:

* [Klucz subskrypcji](get-started.md) usługi mowy.
* Maszyna macOS z zainstalowanym [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) lub nowszym oraz [CocoaPods](https://cocoapods.org/) .

## <a name="get-the-speech-sdk-for-ios"></a>Pobierz zestaw SDK usługi Mowa dla systemu iOS

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Bieżąca wersja zestawu Speech SDK usługi Cognitive Services to `1.6.0`. Należy zauważyć, że ten samouczek nie będzie działał bez zmian dla żadnej wcześniejszej wersji zestawu SDK.

Zestaw Cognitive Services Speech SDK for macOS jest dystrybuowany jako pakiet platformy.
Może być używany w projektach Xcode jako [CocoaPod](https://cocoapods.org/)lub pobierany z https://aka.ms/csspeech/macosbinary i połączony ręcznie. Ten przewodnik używa CocoaPod.

## <a name="create-an-xcode-project"></a>Tworzenie projektu Xcode

Uruchom program Xcode i rozpocznij nowy projekt, klikając pozycję **File** > **New** > **Project** (Plik > Nowy > Projekt).
W oknie dialogowym wyboru szablonu wybierz szablon „iOS Single View App” (Aplikacja pojedynczego widoku systemu iOS).

W kolejnych oknach dialogowych wybierz następujące opcje:

1. Okno dialogowe Project Options (Opcje projektu)
    1. Podaj nazwę aplikacji szybkiego startu, na przykład `helloworld`.
    1. Wprowadź odpowiednią nazwę organizacji i identyfikator organizacji, jeśli masz już konto dewelopera firmy Apple. Dla celów testowych możesz po prostu wybrać dowolną nazwę, taką jak `testorg`. Aby zarejestrować aplikację, musisz mieć odpowiedni profil aprowizacji. Szczegóły zawiera [witryna dla deweloperów firmy Apple](https://developer.apple.com/).
    1. Upewnij się, że wybrano kod SWIFT jako język dla projektu.
    1. Wyłącz pola wyboru, aby użyć scenorysów i utworzyć aplikację opartą na dokumentach. Prosty interfejs użytkownika dla przykładowej aplikacji zostanie utworzony programowo.
    1. Usuń zaznaczenie wszystkich pól wyboru dla testów i danych podstawowych.
1. Wybieranie katalog projektu
    1. Wybierz katalog, w którym ma zostać umieszczony projekt. Spowoduje to utworzenie `helloworld` katalogu w wybranym katalogu, który zawiera wszystkie pliki dla projektu Xcode.
    1. Wyłącz tworzenie repozytorium Git dla tego przykładowego projektu.
1. Aplikacja musi również zadeklarować użycie mikrofonu w `Info.plist` pliku. Kliknij plik w przeglądzie i Dodaj klucz "Opis użycia mikrofonu prywatności" z wartością, taką jak "mikrofon jest wymagany w przypadku rozpoznawania mowy".
    ![Ustawienia w info. plist](media/sdk/qs-swift-ios-info-plist.png)
1. Zamknij projekt Xcode. Będzie można użyć innego wystąpienia później po skonfigurowaniu CocoaPods.

## <a name="add-the-sample-code"></a>Dodawanie przykładowego kodu

1. Umieść nowy plik nagłówkowy o nazwie `MicrosoftCognitiveServicesSpeech-Bridging-Header.h` `helloworld` w katalogu w projekcie HelloWorld i wklej do niego następujący kod:  
   [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h#code)]
1. Dodaj ścieżkę `helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h` względną do nagłówka mostkowania do ustawień projektu SWIFT dla elementu docelowego HelloWorld w obszarze właściwości ![nagłówka *mostkowania "cel-C"* .](media/sdk/qs-swift-ios-bridging-header.png)
1. Zastąp zawartość automatycznie wygenerowanego pliku `AppDelegate.swift` następującą zawartością:  
   [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/helloworld/AppDelegate.swift#code)]
1. Zastąp zawartość automatycznie wygenerowanego pliku `ViewController.swift` następującą zawartością:  
   [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/helloworld/ViewController.swift#code)]
1. W `ViewController.swift`programie Zamień ciąg `YourSubscriptionKey` na klucz subskrypcji.
1. Zastąp ciąg `YourServiceRegion` [regionem](regions.md) skojarzonym z subskrypcją (na przykład `westus` w przypadku subskrypcji bezpłatnej wersji próbnej).

## <a name="install-the-sdk-as-a-cocoapod"></a>Zainstaluj zestaw SDK jako CocoaPod

1. Zainstaluj Menedżera zależności CocoaPod zgodnie z opisem w [instrukcje dotyczące instalacji](https://guides.cocoapods.org/using/getting-started.html).
1. Przejdź do katalogu aplikacji przykładowej (`helloworld`). Umieść plik tekstowy o nazwie `Podfile` i następującej zawartości w tym katalogu:  
   [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/Podfile)]
1. Przejdź do `helloworld` katalogu w terminalu i uruchom polecenie `pod install`. Spowoduje to wygenerowanie `helloworld.xcworkspace` obszaru roboczego Xcode zawierającego zarówno przykładową aplikację, jak i zestaw mowy SDK jako zależność. Ten obszar roboczy zostanie użyty w poniższej tabeli.

## <a name="build-and-run-the-sample"></a>Kompilowanie i uruchamianie przykładu

1. `helloworld.xcworkspace` Otwórz obszar roboczy w Xcode.
1. Uwidocznij dane wyjściowe debugowania (**View** > **Debug Area** > **Activate Console** — Widok > Obszar debugowania > Aktywuj konsolę).
1. Wybierz symulator systemu iOS lub urządzenie z systemem iOS podłączone do komputera deweloperskiego jako miejsce docelowe aplikacji z listy w menu**miejsce docelowe** **produktu** > .
1. Skompiluj i uruchom przykładowy kod w symulatorze systemu iOS, wybierając pozycję **Product** > **Run** (Produkt > Uruchom) z menu lub klikając przycisk **Play** (Odtwórz).
1. Po kliknięciu przycisku "Rozpoznaj" w aplikacji i wpisaniu kilku wyrazów powinien zostać wyświetlony tekst mówiony w dolnej części ekranu.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami w usłudze GitHub](https://aka.ms/csspeech/samples)

