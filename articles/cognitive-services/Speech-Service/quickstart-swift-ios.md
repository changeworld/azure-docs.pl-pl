---
title: 'Szybki Start: Rozpoznawanie mowy, usługa SWIFT-mowę'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak rozpoznać mowę w usłudze SWIFT w systemie iOS przy użyciu zestawu Speech SDK
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: chlandsi
ms.openlocfilehash: cdd09a860c3840823661e30c9f8ebcb900574531
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803202"
---
# <a name="quickstart-recognize-speech-in-swift-on-ios-using-the-speech-sdk"></a>Szybki Start: Rozpoznawanie mowy w usłudze SWIFT w systemie iOS przy użyciu zestawu Speech SDK

Przewodniki Szybki Start są również dostępne dla [syntezy mowy](quickstart-text-to-speech-swift-ios.md).

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

W tym artykule dowiesz się, jak utworzyć aplikację dla systemu iOS za pomocą narzędzia Cognitive Services Speech SDK, aby transkrypcja mowę zarejestrowaną z mikrofonu do tekstu.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem pracy zapoznaj się z poniższą listą wymagań wstępnych:

* [Klucz subskrypcji](get-started.md) usługi mowy.
* Maszyna macOS z zainstalowanym [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) lub nowszym oraz [CocoaPods](https://cocoapods.org/) .

## <a name="get-the-speech-sdk-for-ios"></a>Pobierz zestaw SDK usługi Mowa dla systemu iOS

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Należy zauważyć, że ten samouczek nie będzie działał z wersją zestawu SDK wcześniejszą niż 1.6.0.

Zestaw SDK mowy Cognitive Services dla systemu iOS jest dystrybuowany jako pakiet platformy.
Mogą być używane w projektach Xcode jako [CocoaPod](https://cocoapods.org/)lub pobierane z https://aka.ms/csspeech/macosbinary i połączone ręcznie. Ten przewodnik używa CocoaPod.

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
    1. Wybierz katalog, w którym ma zostać umieszczony projekt. Spowoduje to utworzenie katalogu `helloworld` w wybranym katalogu, który zawiera wszystkie pliki dla projektu Xcode.
    1. Wyłącz tworzenie repozytorium Git dla tego przykładowego projektu.
1. Aplikacja musi również zadeklarować użycie mikrofonu w pliku `Info.plist`. Kliknij plik w przeglądzie i Dodaj klucz "Opis użycia mikrofonu prywatności" z wartością, taką jak "mikrofon jest wymagany w przypadku rozpoznawania mowy".
    ![Settings w info. plist @ no__t-1
1. Zamknij projekt Xcode. Będzie można użyć innego wystąpienia później po skonfigurowaniu CocoaPods.

## <a name="add-the-sample-code"></a>Dodawanie przykładowego kodu

1. Umieść nowy plik nagłówka o nazwie `MicrosoftCognitiveServicesSpeech-Bridging-Header.h` w katalogu `helloworld` w projekcie HelloWorld i wklej do niego następujący kod:  
   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h#code)]
1. Dodaj ścieżkę względną `helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h` do nagłówka mostkowania do ustawień projektu SWIFT dla elementu docelowego HelloWorld w polu *nagłówka mostkowania "cel-C* " ![Header właściwości @ no__t-3
1. Zastąp zawartość automatycznie wygenerowanego pliku `AppDelegate.swift` następującą zawartością:  
   [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/helloworld/AppDelegate.swift#code)]
1. Zastąp zawartość automatycznie wygenerowanego pliku `ViewController.swift` następującą zawartością:  
   [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/helloworld/ViewController.swift#code)]
1. W `ViewController.swift` Zastąp ciąg `YourSubscriptionKey` kluczem subskrypcji.
1. Zastąp ciąg `YourServiceRegion` [regionem](regions.md) skojarzonym z subskrypcją (na przykład `westus` w przypadku subskrypcji bezpłatnej wersji próbnej).

## <a name="install-the-sdk-as-a-cocoapod"></a>Zainstaluj zestaw SDK jako CocoaPod

1. Zainstaluj Menedżera zależności CocoaPod zgodnie z opisem w [instrukcje dotyczące instalacji](https://guides.cocoapods.org/using/getting-started.html).
1. Przejdź do katalogu aplikacji przykładowej (`helloworld`). Umieść plik tekstowy o nazwie `Podfile` i następującej zawartości w tym katalogu:  
   [!code-ruby[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/Podfile)]
1. Przejdź do katalogu `helloworld` w terminalu i uruchom polecenie `pod install`. Spowoduje to wygenerowanie @no__tego obszaru roboczego Xcode-0 zawierającego zarówno przykładową aplikację, jak i zestaw Speech SDK jako zależność. Ten obszar roboczy zostanie użyty w poniższej tabeli.

## <a name="build-and-run-the-sample"></a>Kompilowanie i uruchamianie przykładu

1. Otwórz obszar roboczy `helloworld.xcworkspace` w Xcode.
1. Uwidocznij dane wyjściowe debugowania (**View** > **Debug Area** > **Activate Console** — Widok > Obszar debugowania > Aktywuj konsolę).
1. Wybierz symulator systemu iOS lub urządzenie z systemem iOS podłączone do komputera deweloperskiego jako miejsce docelowe aplikacji z listy w menu**miejsce docelowe** @no__t **produktu**.
1. Skompiluj i uruchom przykładowy kod w symulatorze systemu iOS, wybierając pozycję **Product** > **Run** (Produkt > Uruchom) z menu lub klikając przycisk **Play** (Odtwórz).
1. Po kliknięciu przycisku "Rozpoznaj" w aplikacji i wpisaniu kilku wyrazów powinien zostać wyświetlony tekst mówiony w dolnej części ekranu.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami w usłudze GitHub](https://aka.ms/csspeech/samples)
