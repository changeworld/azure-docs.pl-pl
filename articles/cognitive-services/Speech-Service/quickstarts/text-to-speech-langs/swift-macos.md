---
title: 'Szybki start: Syntetyzacja mowy, Swift - Usługa mowy'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak syntetyzować mowę w programie Swift w systemie macOS przy użyciu sdk mowy
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: yulili
ms.openlocfilehash: ea0f61ddd2c60d2806af0f6dcf97c7d2388335d0
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975877"
---
# <a name="quickstart-synthesize-speech-in-swift-on-macos-using-the-speech-sdk"></a>Szybki start: synteza mowy w trybie Swift w systemie macOS przy użyciu sdk mowy

W tym artykule dowiesz się, jak utworzyć aplikację systemu macOS w języku Swift przy użyciu SDK mowy usług Cognitive Services do syntezy mowy z tekstu i odtwarzania go z domyślnym wyjściem audio.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem pracy zapoznaj się z poniższą listą wymagań wstępnych:

* [Klucz subskrypcji](~/articles/cognitive-services/Speech-Service/get-started.md) usługi Mowy.
* Urządzenie z systemem macOS z [zainstalowanym kodem Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) lub [nowszym i CocoaPods.](https://cocoapods.org/)

## <a name="get-the-speech-sdk-for-macos"></a>Pobierz sdk mowy dla systemu macOS

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Zestaw SDK mowy usług Cognitive Services dla systemu macOS jest dystrybuowany jako pakiet framework.
Może być używany w projektach Xcode jako [CocoaPod](https://cocoapods.org/)lub pobierany i https://aka.ms/csspeech/macosbinary połączony ręcznie. W tym przewodniku użyto cocoaPoda.

> [!NOTE] 
> Ten samouczek nie będzie działać z wersjami zestawu SDK wcześniej niż 1.7.0.

## <a name="create-an-xcode-project"></a>Tworzenie projektu Xcode

Uruchom xcode i rozpocznij nowy projekt, klikając **pozycję Plik** > **nowego** > **projektu**.
W oknie wyboru szablonu wybierz szablon "Aplikacja kakaowa".

W kolejnych oknach dialogowych wybierz następujące opcje:

1. Okno dialogowe Project Options (Opcje projektu)
    1. Podaj nazwę aplikacji szybkiego startu, na przykład `helloworld`.
    1. Wprowadź odpowiednią nazwę organizacji i identyfikator organizacji, jeśli masz już konto dewelopera firmy Apple. Dla celów testowych możesz po prostu wybrać dowolną nazwę, taką jak `testorg`. Aby zarejestrować aplikację, musisz mieć odpowiedni profil aprowizacji. Szczegóły zawiera [witryna dla deweloperów firmy Apple](https://developer.apple.com/).
    1. Upewnij się, że swift został wybrany jako język projektu.
    1. Wyłącz pola wyboru, aby używać scenoskoków i tworzyć aplikację opartą na dokumentach. Prosty interfejs użytkownika dla przykładowej aplikacji zostanie utworzony programowo.
    1. Usuń zaznaczenie wszystkich pól wyboru dla testów i danych podstawowych.
1. Wybieranie katalog projektu
    1. Wybierz katalog, w który ma być umieszczony projekt. Spowoduje to `helloworld` utworzenie katalogu w wybranym katalogu, który zawiera wszystkie pliki dla projektu Xcode.
    1. Wyłącz tworzenie repozytorium Git dla tego przykładowego projektu.
1. Zamknij projekt Xcode. Użyjesz innego wystąpienia później po skonfigurowaniu CocoaPods.

## <a name="add-the-sample-code"></a>Dodawanie przykładowego kodu

1. Umieść nowy plik nagłówka `MicrosoftCognitiveServicesSpeech-Bridging-Header.h` o `helloworld` nazwie w katalogu wewnątrz projektu helloworld i wklej do niego następujący kod:

   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift/macos/text-to-speech/helloworld/helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h#code)]
1. Dodawanie ścieżki `helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h` względnej do nagłówka pomostowego do ustawień projektu Swift dla obiektu docelowego helloworld we właściwościach *nagłówka nagłówka* ![pomostowego objective-C](~/articles/cognitive-services/Speech-Service/media/sdk/qs-swift-macos-bridging-header.png)
1. Zastąp zawartość automatycznie wygenerowanego pliku `AppDelegate.swift` następującą zawartością:

   [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift/macos/text-to-speech/helloworld/helloworld/AppDelegate.swift#code)]
1. W `AppDelegate.swift`, zastąp ciąg `YourSubscriptionKey` kluczem subskrypcji.
1. Zastąp ciąg `YourServiceRegion`regionem skojarzonym z subskrypcją (na przykład `westus` w przypadku subskrypcji bezpłatnej wersji próbnej).

## <a name="install-the-sdk-as-a-cocoapod"></a>Zainstaluj SDK jako CocoaPod

1. Zainstaluj menedżera zależności CocoaPod, jak opisano w [instrukcji instalacji.](https://guides.cocoapods.org/using/getting-started.html)
1. Przejdź do katalogu przykładowej aplikacji`helloworld`( ). Umieść plik tekstowy `Podfile` z nazwą i następującą zawartością w tym katalogu:

   [!code-ruby[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift/macos/text-to-speech/helloworld/Podfile)]
1. Przejdź do `helloworld` katalogu w terminalu i `pod install`uruchom polecenie . Spowoduje to `helloworld.xcworkspace` wygenerowanie obszaru roboczego Xcode zawierającego zarówno przykładową aplikację, jak i zestaw SDK mowy jako zależność. Ten obszar roboczy będzie używany w następujących obszarach.

## <a name="build-and-run-the-sample"></a>Kompilowanie i uruchamianie przykładu

1. Otwórz `helloworld.xcworkspace` obszar roboczy w xcode.
1. Uwidocznić dane wyjściowe debugowania **(Wyświetl** >  > **konsolę aktywacji****obszaru debugowania).**
1. Skompiluj i uruchom przykładowy kod, wybierając **polecenie Uruchom produkt** > **Run** z menu lub klikając przycisk **Odtwórz.**
1. Po wprowadzeniu tekstu i kliknięciu przycisku w aplikacji powinieneś usłyszeć odtworzony dźwięk syntetyzowany.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami w usłudze GitHub](https://aka.ms/csspeech/samples)

