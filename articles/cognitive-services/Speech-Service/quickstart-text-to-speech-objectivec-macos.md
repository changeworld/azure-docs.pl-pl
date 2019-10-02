---
title: 'Szybki Start: wykluczanie mowy, obiektyw-C-Speech Service'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak przeprowadzić funkcję syntezy mowy w celu macOS przy użyciu zestawu Speech SDK
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/20/2019
ms.author: yulili
ms.openlocfilehash: fc134de0c0a27f50aa8f3526cce59925dfa96982
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803971"
---
# <a name="quickstart-synthesize-speech-in-objective-c-on-macos-using-the-speech-sdk"></a>Szybki Start: wykluczanie mowy w celu języka C w systemie macOS przy użyciu zestawu Speech SDK

Przewodniki Szybki Start są również dostępne dla [funkcji rozpoznawania mowy](quickstart-objective-c-macos.md).

W tym artykule dowiesz się, jak utworzyć aplikację macOS w zamierzeniu-C przy użyciu zestawu Speech SDK Cognitive Services, aby przeprowadzić funkcję syntezy mowy z tekstu i odtworzyć ją z domyślnym wyjściem audio.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem pracy zapoznaj się z poniższą listą wymagań wstępnych:

* [Klucz subskrypcji](get-started.md) dla usługi Mowa
* Maszyna macOS z [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) lub nowszym oraz macOS 10,13 lub nowszym

## <a name="get-the-speech-sdk-for-macos"></a>Pobieranie zestawu Speech SDK dla macOS

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Należy zauważyć, że ten samouczek nie będzie działał z wersją zestawu SDK wcześniejszą niż 1.7.0.

Zestaw SDK mowy Cognitive Services dla komputerów Mac jest dystrybuowany jako pakiet platformy.
Mogą być używane w projektach Xcode jako [CocoaPod](https://cocoapods.org/)lub pobierane z https://aka.ms/csspeech/macosbinary i połączone ręcznie. Ten przewodnik używa CocoaPod.

## <a name="create-an-xcode-project"></a>Tworzenie projektu Xcode

Uruchom program Xcode i rozpocznij nowy projekt, klikając pozycję **File** > **New** > **Project** (Plik > Nowy > Projekt).
W oknie dialogowym Wybieranie szablonu wybierz szablon "aplikacja kakaowa".

W kolejnych oknach dialogowych wybierz następujące opcje:

1. Okno dialogowe Project Options (Opcje projektu)
    1. Podaj nazwę aplikacji szybkiego startu, na przykład `helloworld`.
    1. Wprowadź odpowiednią nazwę organizacji i identyfikator organizacji, jeśli masz już konto dewelopera firmy Apple. Dla celów testowych możesz po prostu wybrać dowolną nazwę, taką jak `testorg`. Aby zarejestrować aplikację, musisz mieć odpowiedni profil aprowizacji. Szczegóły zawiera [witryna dla deweloperów firmy Apple](https://developer.apple.com/).
    1. Upewnij się, że jako język projektu wybrano język Objective-C.
    1. Wyłącz pola wyboru, aby użyć scenorysów i utworzyć aplikację opartą na dokumentach. Prosty interfejs użytkownika dla przykładowej aplikacji zostanie utworzony programowo.
    1. Usuń zaznaczenie wszystkich pól wyboru dla testów i danych podstawowych.
    ![Ustawienia projektu](media/sdk/qs-objectivec-macos-project-settings.png)
1. Wybieranie katalog projektu
    1. Wybierz katalog, w którym ma zostać umieszczony projekt. Powoduje to utworzenie katalogu `helloworld` w katalogu macierzystym, który zawiera wszystkie pliki projektu programu Xcode.
    1. Wyłącz tworzenie repozytorium Git dla tego przykładowego projektu.
1. Ustaw uprawnienia dostępu do sieci. Kliknij nazwę aplikacji w pierwszym wierszu przeglądu po lewej stronie, aby przejść do konfiguracji aplikacji, a następnie wybierz kartę "możliwości".
    1. Włącz ustawienie "piaskownica aplikacji" dla aplikacji.
    1. Włącz pola wyboru dla dostępu wychodzącego "połączenia".
    ![Sandbox Settings @ no__t-1
1. Zamknij projekt Xcode. Będzie można użyć innego wystąpienia później po skonfigurowaniu CocoaPods.

## <a name="install-the-sdk-as-a-cocoapod"></a>Zainstaluj zestaw SDK jako CocoaPod

1. Zainstaluj Menedżera zależności CocoaPod zgodnie z opisem w [instrukcje dotyczące instalacji](https://guides.cocoapods.org/using/getting-started.html).
1. Przejdź do katalogu aplikacji przykładowej (`helloworld`). Umieść plik tekstowy o nazwie `Podfile` i następującej zawartości w tym katalogu:  
   [!code-ruby[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/objectivec-macos/helloworld/Podfile)]
1. Przejdź do katalogu `helloworld` w terminalu i uruchom polecenie `pod install`. Spowoduje to wygenerowanie @no__tego obszaru roboczego Xcode-0 zawierającego zarówno przykładową aplikację, jak i zestaw Speech SDK jako zależność. Ten obszar roboczy zostanie użyty w poniższej tabeli.

## <a name="add-the-sample-code"></a>Dodawanie przykładowego kodu

1. Otwórz obszar roboczy `helloworld.xcworkspace` w Xcode.
1. Zastąp zawartość automatycznie wygenerowanego pliku `AppDelegate.m` następującą zawartością:  
   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/objectivec-macos/helloworld/helloworld/AppDelegate.m#code)]
1. Zastąp ciąg `YourSubscriptionKey` kluczem subskrypcji.
1. Zastąp ciąg `YourServiceRegion` [regionem](regions.md) skojarzonym z subskrypcją (na przykład `westus` w przypadku subskrypcji bezpłatnej wersji próbnej).

## <a name="build-and-run-the-sample"></a>Kompilowanie i uruchamianie przykładu

1. Uwidocznij dane wyjściowe debugowania (**View** > **Debug Area** > **Activate Console** — Widok > Obszar debugowania > Aktywuj konsolę).
1. Kompiluj i uruchamiaj przykładowy kod, wybierając **produkt** -> **Run** z menu lub klikając przycisk **Odtwórz** .
1. Po wprowadzeniu tekstu i kliknięciu tego przycisku w aplikacji należy usłyszeć dźwięk, który jest odtwarzany.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami dla języka Objective-C w serwisie GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Zobacz także

- [Dostosuj czcionki głosowe](how-to-customize-voice-font.md)
- [Rejestruj przykłady głosu](record-custom-voice-samples.md)
