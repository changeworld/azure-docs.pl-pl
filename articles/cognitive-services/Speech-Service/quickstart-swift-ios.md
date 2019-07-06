---
title: 'Szybki start: Rozpoznawanie mowy, Swift — usługi mowy'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak rozpoznawanie mowy w języku Swift w systemie iOS przy użyciu zestawu SDK rozpoznawania mowy
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: chlandsi
ms.openlocfilehash: 98c37d4a39235e5ed1a82df72886d196cbf19e49
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605040"
---
# <a name="quickstart-recognize-speech-in-swift-on-ios-using-the-speech-sdk"></a>Szybki start: Rozpoznawanie mowy w języku Swift w systemie iOS przy użyciu zestawu SDK rozpoznawania mowy

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

W tym artykule dowiesz się, jak utworzyć aplikację dla systemu iOS w języku Swift transkrypcja mowy rejestrowane z mikrofonu do tekstu za pomocą Cognitive Services SDK rozpoznawania mowy.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem pracy zapoznaj się z poniższą listą wymagań wstępnych:

* A [klucz subskrypcji](get-started.md) usługi mowy.
* Maszyna z systemem macOS z [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) lub nowszej i [CocoaPods](https://cocoapods.org/) zainstalowane.

## <a name="get-the-speech-sdk-for-ios"></a>Pobierz zestaw SDK usługi Mowa dla systemu iOS

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Bieżąca wersja zestawu Speech SDK usługi Cognitive Services to `1.6.0`. Należy pamiętać, że w tym samouczku nie będą działać bez zmiany jego wcześniejszą wersję zestawu SDK.

Cognitive Services mowy zestawu SDK dla systemu iOS jest dystrybuowane jako pakiet framework.
Może służyć w projekty Xcode jako [CocoaPod](https://cocoapods.org/), lub pobrane ze https://aka.ms/csspeech/macosbinary i połączone ręcznie. Ten przewodnik używa CocoaPod.

## <a name="create-an-xcode-project"></a>Tworzenie projektu Xcode

Uruchom program Xcode i rozpocznij nowy projekt, klikając pozycję **File** > **New** > **Project** (Plik > Nowy > Projekt).
W oknie dialogowym wyboru szablonu wybierz szablon „iOS Single View App” (Aplikacja pojedynczego widoku systemu iOS).

W kolejnych oknach dialogowych wybierz następujące opcje:

1. Okno dialogowe Project Options (Opcje projektu)
    1. Podaj nazwę aplikacji szybkiego startu, na przykład `helloworld`.
    1. Wprowadź nazwę odpowiedniej organizacji i identyfikator organizacji, jeśli masz już konto programu Apple developer. Dla celów testowych możesz po prostu wybrać dowolną nazwę, taką jak `testorg`. Aby zarejestrować aplikację, musisz mieć odpowiedni profil aprowizacji. Szczegóły zawiera [witryna dla deweloperów firmy Apple](https://developer.apple.com/).
    1. Upewnij się, że kod Swift jest wybierany jako język dla projektu.
    1. Wyłącz pola wyboru, aby użyć scenorysy i do tworzenia aplikacji opartej na dokumentach. Prosty interfejs użytkownika dla przykładowej aplikacji zostanie utworzony programowo.
    1. Usuń zaznaczenie wszystkich pól wyboru dla testów i danych podstawowych.
1. Wybieranie katalog projektu
    1. Wybierz katalog, aby przełączyć projekt. Spowoduje to utworzenie `helloworld` katalogu w wybranym katalogu, który zawiera wszystkie pliki w projekcie Xcode.
    1. Wyłącz tworzenie repozytorium Git dla tego przykładowego projektu.
1. Aplikacja musi również zadeklarować użycia mikrofonu w `Info.plist` pliku. Kliknij plik, w ramach przeglądu, a następnie dodaj klucz "Opis użycia mikrofonu — ochrona prywatności" o wartości, takie jak "Mikrofonu jest niezbędny do rozpoznawania mowy".
    ![Ustawienia w pliku Info.plist](media/sdk/qs-swift-ios-info-plist.png)
1. Zamknij projekt Xcode. Użyjesz innego wystąpienia programu go później, po konfigurowaniu zasobniki CocoaPods.

## <a name="add-the-sample-code"></a>Dodawanie przykładowego kodu

1. Umieścić nowy plik nagłówkowy o nazwie `MicrosoftCognitiveServicesSpeech-Bridging-Header.h` do `helloworld` katalog wewnątrz projektu helloworld i wklej poniższy kod do niego: [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h#code)]
1. Dodaj ścieżkę względną `helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h` do nagłówka mostkowania do języka Swift ustawienia dla elementu docelowego helloworld w projektu *nagłówków mostkowania języka Objective-C* pola ![właściwości nagłówka](media/sdk/qs-swift-ios-bridging-header.png)
1. Zastąp zawartość wygenerowany automatycznie `AppDelegate.swift` pliku przez: [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/helloworld/AppDelegate.swift#code)]
1. Zastąp zawartość wygenerowany automatycznie `ViewController.swift` pliku przez: [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/helloworld/ViewController.swift#code)]
1. W `ViewController.swift`, Zastąp ciąg `YourSubscriptionKey` z kluczem subskrypcji.
1. Zastąp ciąg `YourServiceRegion` [regionem](regions.md) skojarzonym z subskrypcją (na przykład `westus` w przypadku subskrypcji bezpłatnej wersji próbnej).

## <a name="install-the-sdk-as-a-cocoapod"></a>Instalowanie zestawu SDK jako CocoaPod

1. Zainstaluj Menedżera zależności CocoaPod, zgodnie z opisem w jego [instrukcje dotyczące instalacji](https://guides.cocoapods.org/using/getting-started.html).
1. Przejdź do katalogu aplikacji przykładowych (`helloworld`). Umieść plik tekstowy o nazwie `Podfile` i następującej zawartości w tym katalogu: [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/Podfile)]
1. Przejdź do `helloworld` katalogu w terminalu i uruchom polecenie `pod install`. Spowoduje to wygenerowanie `helloworld.xcworkspace` obszaru roboczego Xcode, zawierający przykładową aplikację i zestawu SDK mowy jako zależność. Ten obszar roboczy będzie używany w następujących.

## <a name="build-and-run-the-sample"></a>Kompilowanie i uruchamianie przykładu

1. Otwórz `helloworld.xcworkspace` obszaru roboczego w programie Xcode.
1. Uwidocznij dane wyjściowe debugowania (**View** > **Debug Area** > **Activate Console** — Widok > Obszar debugowania > Aktywuj konsolę).
1. Wybierz symulatora systemu iOS lub urządzenie z systemem iOS połączone na maszynie deweloperskiej jako lokalizacja docelowa dla aplikacji z listy w **produktu** > **docelowy** menu.
1. Skompiluj i uruchom przykładowy kod w symulatorze systemu iOS, wybierając pozycję **Product** > **Run** (Produkt > Uruchom) z menu lub klikając przycisk **Play** (Odtwórz).
1. Po kliknij przycisk "Rozpoznaj" w aplikacji i podać kilka słów, tekst, który ma być używany powinny być widoczne w dolnej części ekranu.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami w usłudze GitHub](https://aka.ms/csspeech/samples)
