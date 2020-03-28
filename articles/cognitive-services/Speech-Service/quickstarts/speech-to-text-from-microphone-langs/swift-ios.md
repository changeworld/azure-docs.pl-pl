---
title: 'Szybki start: rozpoznawanie mowy z mikrofonu, Swift - Usługa mowy'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak rozpoznać mowę w programie Swift w uos w systemu iOS przy użyciu sdk mowy
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/23/2019
ms.author: chlandsi
ms.openlocfilehash: 31f5339c70d52019400ca5f1fe873de4790a3bd6
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75380528"
---
# <a name="quickstart-recognize-speech-in-swift-on-ios-by-using-the-speech-sdk"></a>Szybki start: rozpoznawanie mowy w trybie Swift w uos systemu iOS przy użyciu sdk mowy

Szybkie starty są również dostępne do [syntezy mowy.](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech-langs/swift-ios.md)

W tym artykule dowiesz się, jak utworzyć aplikację systemu iOS w języku Swift przy użyciu narzędzia Azure Cognitive Services Speech SDK do transkrypcji mowy nagranej z mikrofonu na tekst.

## <a name="prerequisites"></a>Wymagania wstępne

Zanim zaczniesz, będziesz potrzebować:

* [Klucz subskrypcji](~/articles/cognitive-services/Speech-Service/get-started.md) usługi Mowy.
* Urządzenie z systemem macOS z [zainstalowanym kodem Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) lub [nowszym i CocoaPods.](https://cocoapods.org/)

## <a name="get-the-speech-sdk-for-ios"></a>Pobierz zestaw SDK usługi Mowa dla systemu iOS

[!INCLUDE [License notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Ten samouczek nie będzie działać z wersją zestawu SDK wcześniej niż 1.6.0.

Zestaw SDK mowy usług Cognitive Services dla systemu iOS jest dystrybuowany jako pakiet framework. Może być używany w projektach Xcode jako [CocoaPod](https://cocoapods.org/) lub pobierany z https://aka.ms/csspeech/iosbinary i połączony ręcznie. W tym artykule użyto CocoaPod.

## <a name="create-an-xcode-project"></a>Tworzenie projektu Xcode

Uruchom xcode i rozpocznij nowy projekt, wybierając **pozycję Plik** > **nowego** > **projektu**.
W oknie dialogowym wyboru szablonu wybierz szablon aplikacji pojedynczego widoku systemu **iOS.**

W następnych oknach dialogowych dokonaj następujących wyborów.

1. W oknie dialogowym **Opcje projektu:**
    1. Wprowadź nazwę aplikacji szybki start, na przykład *helloworld*.
    1. Wprowadź odpowiednią nazwę organizacji i identyfikator organizacji, jeśli masz już konto dewelopera firmy Apple. Do celów testowych należy użyć nazwy takiej jak *testorg*. Aby zarejestrować aplikację, musisz mieć odpowiedni profil aprowizacji. Aby uzyskać więcej informacji, zobacz [witrynę dewelopera firmy Apple](https://developer.apple.com/).
    1. Upewnij się, że **swift** został wybrany jako język projektu.
    1. Wyłącz pola wyboru, aby używać scenoki i tworzyć aplikację opartą na dokumentach. Prosty interfejs użytkownika dla przykładowej aplikacji jest tworzony programowo.
    1. Wyczyść wszystkie pola wyboru dla testów i podstawowych danych.
1. Wybierz katalog projektu:
    1. Wybierz katalog, w który ma być umieszczony projekt. Ten krok tworzy katalog helloworld w wybranym katalogu, który zawiera wszystkie pliki dla projektu Xcode.
    1. Wyłącz tworzenie repozytorium Git dla tego przykładowego projektu.
1. Aplikacja musi również zadeklarować użycie mikrofonu w `Info.plist` pliku. Wybierz plik w przeglądzie i dodaj klucz **Prywatność — Opis użycia mikrofonu** o wartości takiej jak *Mikrofon jest potrzebny do rozpoznawania mowy*.

    ![Ustawienia w Info.plist](~/articles/cognitive-services/Speech-Service/media/sdk/qs-swift-ios-info-plist.png)

1. Zamknij projekt Xcode. Po skonfigurowaniu CocoaPods można użyć innego wystąpienia później.

## <a name="add-the-sample-code"></a>Dodawanie przykładowego kodu

1. Umieść nowy plik nagłówka `MicrosoftCognitiveServicesSpeech-Bridging-Header.h` o nazwie w katalogu helloworld wewnątrz projektu helloworld. Wklej do niego następujący kod:

   [!code-objectivec[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h#code)]

1. Dodaj ścieżkę `helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h` względną do nagłówka pomostowego do ustawień projektu Swift dla obiektu docelowego helloworld w polu **Nagłówek pomostowy Objective-C.**

   ![Właściwości nagłówka](~/articles/cognitive-services/Speech-Service/media/sdk/qs-swift-ios-bridging-header.png)

1. Zastąp zawartość automatycznie `AppDelegate.swift` generowanego pliku następującym kodem:

   [!code-swift[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/helloworld/AppDelegate.swift#code)]
1. Zastąp zawartość automatycznie `ViewController.swift` generowanego pliku następującym kodem:

   [!code-swift[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/helloworld/ViewController.swift#code)]
1. W `ViewController.swift`, zastąp ciąg `YourSubscriptionKey` kluczem subskrypcji.
1. Zastąp ciąg `YourServiceRegion` [regionem](~/articles/cognitive-services/Speech-Service/regions.md) skojarzonym z subskrypcją. Na przykład `westus` użyj bezpłatnej subskrypcji próbnej.

## <a name="install-the-sdk-as-a-cocoapod"></a>Zainstaluj SDK jako CocoaPod

1. Zainstaluj menedżera zależności CocoaPod, jak opisano w [instrukcji instalacji.](https://guides.cocoapods.org/using/getting-started.html)
1. Przejdź do katalogu przykładowej aplikacji, która jest helloworld. Umieść plik tekstowy o nazwie *Podfile* i następującej zawartości w tym katalogu:

   [!code-ruby[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/Podfile)]
1. Przejdź do katalogu helloworld w terminalu i `pod install`uruchom polecenie . To polecenie generuje `helloworld.xcworkspace` obszar roboczy Xcode, który zawiera zarówno przykładową aplikację, jak i zestaw SDK mowy jako zależność. Ten obszar roboczy jest używany w poniższych krokach.

## <a name="build-and-run-the-sample"></a>Kompilowanie i uruchamianie przykładu

1. Otwórz obszar `helloworld.xcworkspace` roboczy w xcode.
1. Ujrzaj dane wyjściowe debugowania, wybierając **pozycję Wyświetl** > obszar > **debugowania****Aktywuj konsolę**.
1. Wybierz symulator systemu iOS lub urządzenie z systemem iOS podłączone do komputera dewelopera jako miejsce docelowe aplikacji z listy w menu**Miejsce docelowe** **produktu.** > 
1. Skompiluj i uruchom przykładowy kod w symulatorze systemu iOS, wybierając z menu**przebieg** **produktu.** >  Można również wybrać przycisk **Odtwórz.**
1. Po wybraniu przycisku **Rozpoznaj** w aplikacji i wyponieniu kilku słów w dolnej części ekranu powinien zostać wyświetlony tekst, który wypowiedziałeś.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami w usłudze GitHub](https://aka.ms/csspeech/samples)
