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
ms.openlocfilehash: 0a62e670587f271eb9a1beba034886c5f95976a7
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72327744"
---
# <a name="quickstart-recognize-speech-in-swift-on-ios-by-using-the-speech-sdk"></a>Szybki Start: Rozpoznawanie mowy w usłudze SWIFT w systemie iOS przy użyciu zestawu Speech SDK

Przewodniki Szybki Start są również dostępne dla [syntezy mowy](quickstart-text-to-speech-swift-ios.md).

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

W tym artykule dowiesz się, jak utworzyć aplikację dla systemu iOS w usłudze SWIFT przy użyciu zestawu Azure Cognitive Services Speech SDK, aby transkrypcja mowę nagraną z mikrofonu do tekstu.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy:

* [Klucz subskrypcji](get-started.md) usługi mowy.
* Maszyna macOS z zainstalowanym [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) lub nowszym oraz [CocoaPods](https://cocoapods.org/) .

## <a name="get-the-speech-sdk-for-ios"></a>Pobierz zestaw SDK usługi Mowa dla systemu iOS

[!INCLUDE [License notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Ten samouczek nie będzie działał z wersją zestawu SDK wcześniejszą niż 1.6.0.

Zestaw SDK mowy Cognitive Services dla systemu iOS jest dystrybuowany jako pakiet platformy. Może być używany w projektach Xcode jako [CocoaPod](https://cocoapods.org/) lub pobranych z https://aka.ms/csspeech/macosbinary i połączone ręcznie. W tym artykule jest stosowany CocoaPod.

## <a name="create-an-xcode-project"></a>Tworzenie projektu Xcode

Rozpocznij Xcode i Rozpocznij nowy projekt, wybierając pozycję **plik** > **Nowy** > **projektu**.
W oknie dialogowym Wybieranie szablonu wybierz szablon aplikacji z **pojedynczym widokiem systemu iOS** .

W poniższych oknach dialogowych wybierz następujące opcje.

1. W oknie dialogowym **Opcje projektu** :
    1. Wprowadź nazwę aplikacji szybkiego startu, na przykład *HelloWorld*.
    1. Wprowadź odpowiednią nazwę organizacji i identyfikator organizacji, jeśli masz już konto dewelopera firmy Apple. Do celów testowych Użyj nazwy, takiej jak *testorg*. Aby zarejestrować aplikację, musisz mieć odpowiedni profil aprowizacji. Aby uzyskać więcej informacji, zobacz [witrynę dla deweloperów firmy Apple](https://developer.apple.com/).
    1. Upewnij się, że wybrano **kod SWIFT** jako język dla projektu.
    1. Wyłącz pola wyboru, aby używać scenorysów i utworzyć aplikację opartą na dokumentach. Prosty interfejs użytkownika dla przykładowej aplikacji jest tworzony programowo.
    1. Wyczyść wszystkie pola wyboru dla testów i danych podstawowych.
1. Wybierz katalog projektu:
    1. Wybierz katalog, w którym ma zostać umieszczony projekt. W tym kroku zostanie utworzony katalog HelloWorld w wybranym katalogu, który zawiera wszystkie pliki dla projektu Xcode.
    1. Wyłącz tworzenie repozytorium Git dla tego przykładowego projektu.
1. Aplikacja musi również zadeklarować użycie mikrofonu w pliku `Info.plist`. Wybierz plik w przeglądzie, a następnie Dodaj klucz **Opis użycia funkcji prywatność-mikrofon** z wartością taką jak *mikrofon jest wymagany do rozpoznawania mowy*.

    ![Ustawienia w info. plist](media/sdk/qs-swift-ios-info-plist.png)

1. Zamknij projekt Xcode. Możesz użyć innego wystąpienia później po skonfigurowaniu CocoaPods.

## <a name="add-the-sample-code"></a>Dodawanie przykładowego kodu

1. Umieść nowy plik nagłówka o nazwie `MicrosoftCognitiveServicesSpeech-Bridging-Header.h` w katalogu HelloWorld w projekcie HelloWorld. Wklej do niego następujący kod:

   [!code-objectivec[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h#code)]

1. Dodaj ścieżkę względną `helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h` do nagłówka mostkowania do ustawień projektu SWIFT dla elementu docelowego HelloWorld w polu **nagłówka mostkowania "cel-C"** .

   ![Właściwości nagłówka](media/sdk/qs-swift-ios-bridging-header.png)

1. Zastąp zawartość automatycznie generowanego pliku `AppDelegate.swift` następującym kodem:

   [!code-swift[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/helloworld/AppDelegate.swift#code)]
1. Zastąp zawartość automatycznie generowanego pliku `ViewController.swift` następującym kodem:

   [!code-swift[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/helloworld/ViewController.swift#code)]
1. W `ViewController.swift` Zastąp ciąg `YourSubscriptionKey` kluczem subskrypcji.
1. Zastąp ciąg `YourServiceRegion` z [regionem](regions.md) skojarzonym z subskrypcją. Na przykład w subskrypcji bezpłatnej wersji próbnej Użyj `westus`.

## <a name="install-the-sdk-as-a-cocoapod"></a>Zainstaluj zestaw SDK jako CocoaPod

1. Zainstaluj Menedżera zależności CocoaPod zgodnie z opisem w [instrukcje dotyczące instalacji](https://guides.cocoapods.org/using/getting-started.html).
1. Przejdź do katalogu aplikacji przykładowej, czyli HelloWorld. Umieść plik tekstowy o nazwie *plik podfile* i następującej zawartości w tym katalogu:

   [!code-ruby[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/Podfile)]
1. Przejdź do katalogu HelloWorld w terminalu i uruchom polecenie `pod install`. To polecenie spowoduje wygenerowanie `helloworld.xcworkspace` obszaru roboczego Xcode, który zawiera zarówno przykładową aplikację, jak i zestaw mowy SDK jako zależność. Ten obszar roboczy jest używany w poniższych krokach.

## <a name="build-and-run-the-sample"></a>Kompilowanie i uruchamianie przykładu

1. Otwórz obszar roboczy `helloworld.xcworkspace` w Xcode.
1. Aby wyświetlić dane wyjściowe debugowania, wybierz pozycję **wyświetl** > **obszar debugowania** > **Aktywuj konsolę**.
1. Wybierz symulator systemu iOS lub urządzenie z systemem iOS podłączone do komputera deweloperskiego jako miejsce docelowe aplikacji z listy w menu**miejsce docelowe** @no__t **produktu**.
1. Kompiluj i uruchamiaj przykładowy kod w symulatorze systemu iOS, wybierając **produkt** > **Run** z menu. Możesz również wybrać przycisk **Odtwórz** .
1. Po wybraniu przycisku **Rozpoznaj** w aplikacji i wpisaniu kilku wyrazów powinien zostać wyświetlony tekst wymawiany w dolnej części ekranu.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami w usłudze GitHub](https://aka.ms/csspeech/samples)
