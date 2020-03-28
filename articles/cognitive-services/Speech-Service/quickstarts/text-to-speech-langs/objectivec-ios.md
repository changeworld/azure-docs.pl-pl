---
title: 'Szybki start: Synteza mowy, Objective-C - Usługa mowy'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak syntetyzować mowę w języku Objective-C w uos iOS przy użyciu sdk mowy
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: yulili
ms.openlocfilehash: 1f5a569f9c3bfa91b78c836e37a22642238674be
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975945"
---
# <a name="quickstart-synthesize-speech-in-objective-c-on-ios-using-the-speech-sdk"></a>Szybki start: syntezy mowy w języku Objective-C w iOS przy użyciu SDK mowy

W tym artykule dowiesz się, jak utworzyć aplikację systemu iOS w języku Objective-C przy użyciu SDK mowy usług Cognitive Services do syntezy mowy z tekstu.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem pracy zapoznaj się z poniższą listą wymagań wstępnych:

* [Klucz subskrypcji](~/articles/cognitive-services/Speech-Service/get-started.md) usługi Mowy
* Maszyna z systemem macOS z oprogramowaniem [Xcode w wersji 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) lub nowszej
* Obiekt docelowy ustawiony na system iOS w wersji 9.3 lub nowszej

## <a name="get-the-speech-sdk-for-ios"></a>Pobierz zestaw SDK usługi Mowa dla systemu iOS

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Należy zauważyć, że ten samouczek nie będzie działać z wersją zestawu SDK wcześniej niż 1.7.0.

SDK mowy usług Cognitive Services dla systemu iOS jest obecnie dystrybuowany jako struktura cocoa.
Może być używany w projektach Xcode jako [CocoaPod](https://cocoapods.org/)lub pobierany i https://aka.ms/csspeech/iosbinary połączony ręcznie. W tym przewodniku użyto cocoaPoda.

## <a name="create-an-xcode-project"></a>Tworzenie projektu programu Xcode

Uruchom xcode i rozpocznij nowy projekt, klikając **pozycję Plik** > **nowego** > **projektu**.
W oknie dialogowym wyboru szablonu wybierz szablon „iOS Single View App” (Aplikacja pojedynczego widoku systemu iOS).

W kolejnych oknach dialogowych wybierz następujące opcje:

1. Okno dialogowe Project Options (Opcje projektu)
    1. Podaj nazwę aplikacji szybkiego startu, na przykład `helloworld`.
    1. Podaj odpowiednią nazwę organizacji i identyfikator organizacji, jeśli masz już konto programu Apple Developer. Dla celów testowych możesz po prostu wybrać dowolną nazwę, taką jak `testorg`. Aby zarejestrować aplikację, musisz mieć odpowiedni profil aprowizacji. Szczegóły zawiera [witryna dla deweloperów firmy Apple](https://developer.apple.com/).
    1. Upewnij się, że jako język projektu wybrano język Objective-C.
    1. Usuń zaznaczenie wszystkich pól wyboru dla testów i danych podstawowych.
    ![Ustawienia projektu](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-project-settings.png)
1. Wybieranie katalog projektu
    1. Wybierz katalog macierzysty, aby umieścić w nim projekt. Powoduje to utworzenie katalogu `helloworld` w katalogu macierzystym, który zawiera wszystkie pliki projektu programu Xcode.
    1. Wyłącz tworzenie repozytorium Git dla tego przykładowego projektu.

## <a name="install-the-sdk-as-a-cocoapod"></a>Zainstaluj SDK jako CocoaPod

1. Zainstaluj menedżera zależności CocoaPod, jak opisano w [instrukcji instalacji.](https://guides.cocoapods.org/using/getting-started.html)
1. Przejdź do katalogu przykładowej aplikacji`helloworld`( ). Umieść plik tekstowy `Podfile` z nazwą i następującą zawartością w tym katalogu:  
   [!code-ruby[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/text-to-speech/helloworld/Podfile)]
1. Przejdź do `helloworld` katalogu w terminalu i `pod install`uruchom polecenie . Spowoduje to `helloworld.xcworkspace` wygenerowanie obszaru roboczego Xcode zawierającego zarówno przykładową aplikację, jak i zestaw SDK mowy jako zależność. Ten obszar roboczy będzie używany w następujących obszarach.

## <a name="add-the-sample-code"></a>Dodawanie przykładowego kodu

1. Otwórz `helloworld.xcworkspace` obszar roboczy w xcode.
1. Zastąp zawartość automatycznie wygenerowanego pliku `AppDelegate.m` następującą zawartością:  
   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/text-to-speech/helloworld/helloworld/AppDelegate.m#code)]
1. Zastąp zawartość automatycznie wygenerowanego pliku `ViewController.m` następującą zawartością:  
   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/text-to-speech/helloworld/helloworld/ViewController.m#code)]
1. Zastąp ciąg `YourSubscriptionKey` kluczem subskrypcji.
1. Zastąp ciąg `YourServiceRegion`[regionem](~/articles/cognitive-services/Speech-Service/regions.md) skojarzonym z subskrypcją (na przykład `westus` w przypadku subskrypcji bezpłatnej wersji próbnej).

## <a name="build-and-run-the-sample"></a>Kompilowanie i uruchamianie przykładu

1. Uwidocznić dane wyjściowe debugowania **(Wyświetl** >  > **konsolę aktywacji****obszaru debugowania).**
1. Wybierz symulator systemu iOS lub urządzenie z systemem iOS podłączone do komputera dewelopera jako miejsce docelowe aplikacji z listy w menu**Miejsce docelowe** **produktu.** > 
1. Skompiluj i uruchom przykładowy kod w symulatorze systemu iOS, wybierając z menu**przebieg** **produktu** > lub klikając przycisk **Odtwórz.**

   ![Symulowana aplikacja systemu iOS](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-simulated-app-tts.png)

1. Po wprowadzeniu tekstu i kliknięciu przycisku w aplikacji powinieneś usłyszeć odtworzony dźwięk syntetyzowany.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami dla języka Objective-C w serwisie GitHub](https://aka.ms/csspeech/samples)

