---
title: 'Szybki start: rozpoznawanie mowy, język Objective-C — usługi mowy'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak rozpoznawanie mowy w języku Objective C w systemie macOS przy użyciu zestawu SDK rozpoznawania mowy
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/03/2019
ms.author: chlandsi
ms.openlocfilehash: f25c3801553b0ac0c725170cda95f5c1eacc3637
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65020739"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-macos-using-the-speech-sdk"></a>Szybki start: Rozpoznawanie mowy w języku Objective C w systemie macOS przy użyciu zestawu SDK rozpoznawania mowy

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

W tym artykule dowiesz się, jak utworzyć aplikację dla systemu macOS w języku Objective-C, transkrypcja mowy rejestrowane z mikrofonu do tekstu za pomocą Cognitive Services SDK rozpoznawania mowy.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem pracy zapoznaj się z poniższą listą wymagań wstępnych:

* [Klucz subskrypcji](get-started.md) dla usługi Mowa
* Maszyna z systemem macOS z [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) lub nowszej i macOS 10.13 lub nowszy

## <a name="get-the-speech-sdk-for-macos"></a>Pobierz zestaw SDK rozpoznawania mowy dla systemu macOS

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Bieżąca wersja zestawu Speech SDK usługi Cognitive Services to `1.5.0`.

Cognitive Services mowy zestawu SDK dla komputerów Mac jest dystrybuowane jako pakiet framework.
Może służyć w projekty Xcode jako [CocoaPod](https://cocoapods.org/), lub pobrane ze https://aka.ms/csspeech/macosbinary i połączone ręcznie. Ten przewodnik używa CocoaPod.

## <a name="create-an-xcode-project"></a>Tworzenie projektu Xcode

Uruchom program Xcode i rozpocznij nowy projekt, klikając pozycję **File** > **New** > **Project** (Plik > Nowy > Projekt).
W oknie dialogowym wyboru szablonu wybierz szablon "Aplikacja cocoa dla".

W kolejnych oknach dialogowych wybierz następujące opcje:

1. Okno dialogowe Project Options (Opcje projektu)
    1. Podaj nazwę aplikacji szybkiego startu, na przykład `helloworld`.
    1. Wprowadź nazwę odpowiedniej organizacji i identyfikator organizacji, jeśli masz już konto programu Apple developer. Dla celów testowych możesz po prostu wybrać dowolną nazwę, taką jak `testorg`. Aby zarejestrować aplikację, musisz mieć odpowiedni profil aprowizacji. Szczegóły zawiera [witryna dla deweloperów firmy Apple](https://developer.apple.com/).
    1. Upewnij się, że jako język projektu wybrano język Objective-C.
    1. Wyłącz pola wyboru, aby użyć scenorysy i do tworzenia aplikacji opartej na dokumentach. Prosty interfejs użytkownika dla przykładowej aplikacji zostanie utworzony programowo.
    1. Usuń zaznaczenie wszystkich pól wyboru dla testów i danych podstawowych.
    ![Ustawienia projektu](media/sdk/qs-objectivec-macos-project-settings.png)
1. Wybieranie katalog projektu
    1. Wybierz katalog, aby przełączyć projekt. Powoduje to utworzenie katalogu `helloworld` w katalogu macierzystym, który zawiera wszystkie pliki projektu programu Xcode.
    1. Wyłącz tworzenie repozytorium Git dla tego przykładowego projektu.
1. Ustaw uprawnienia dostępu do sieci i mikrofon. Kliknij nazwę aplikacji, w pierwszym wierszu w ramach przeglądu po lewej stronie, aby przejść do konfiguracji aplikacji, a następnie wybierz kartę "Funkcji".
    1. Włącz ustawienie "Piaskownica aplikacji" dla aplikacji.
    1. Zaznacz pola wyboru "Wychodzące połączenia" i "Mikrofon" dostępu.
    ![Ustawienia piaskownicy](media/sdk/qs-objectivec-macos-sandbox.png)
1. Aplikacja musi również zadeklarować użycia mikrofonu w `Info.plist` pliku. Kliknij plik, w ramach przeglądu, a następnie dodaj klucz "Opis użycia mikrofonu — ochrona prywatności" o wartości, takie jak "Mikrofonu jest niezbędny do rozpoznawania mowy".
    ![Ustawienia w pliku Info.plist](media/sdk/qs-objectivec-macos-info-plist.png)
1. Zamknij projekt Xcode. Użyjesz innego wystąpienia programu go później, po konfigurowaniu zasobniki CocoaPods.

## <a name="install-the-sdk-as-a-cocoapod"></a>Instalowanie zestawu SDK jako CocoaPod

1. Zainstaluj Menedżera zależności CocoaPod, zgodnie z opisem w jego [instrukcje dotyczące instalacji](https://guides.cocoapods.org/using/getting-started.html).
1. Przejdź do katalogu aplikacji przykładowych (`helloworld`). Umieść plik tekstowy o nazwie `Podfile` i następującej zawartości w tym katalogu:
    ```
    target 'helloworld' do
        platform :osx, '10.13'
        pod 'MicrosoftCognitiveServicesSpeech-macOS', '~> 1.5.0'
    end
    ```
1. Przejdź do `helloworld` katalogu w terminalu i uruchom polecenie `pod install`. Spowoduje to wygenerowanie `helloworld.xcworkspace` obszaru roboczego Xcode, zawierający przykładową aplikację i zestawu SDK mowy jako zależność. Ten obszar roboczy będzie używany w następujących.

## <a name="add-the-sample-code"></a>Dodawanie przykładowego kodu

1. Otwórz `helloworld.xcworkspace` obszaru roboczego w programie Xcode.
1. Zastąp zawartość wygenerowany automatycznie `AppDelegate.m` pliku przez: [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-macos/helloworld/helloworld/AppDelegate.m#code)]
1. Zastąp ciąg `YourSubscriptionKey` kluczem subskrypcji.
1. Zastąp ciąg `YourServiceRegion` [regionem](regions.md) skojarzonym z subskrypcją (na przykład `westus` w przypadku subskrypcji bezpłatnej wersji próbnej).

## <a name="build-and-run-the-sample"></a>Kompilowanie i uruchamianie przykładu

1. Uwidocznij dane wyjściowe debugowania (**View** > **Debug Area** > **Activate Console** — Widok > Obszar debugowania > Aktywuj konsolę).
1. Kompilowanie i uruchamianie przykładowego kodu, wybierając **produktu** -> **Uruchom** z menu lub klikając **Odtwórz** przycisku.
1. Po kliknij przycisk i podać kilka słów, tekst, który ma być używany powinny być widoczne w dolnej części ekranu. Po uruchomieniu aplikacji po raz pierwszy monit należy przypisać aplikacji dostęp do mikrofonu na komputerze.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami dla języka Objective-C w serwisie GitHub](https://aka.ms/csspeech/samples)

