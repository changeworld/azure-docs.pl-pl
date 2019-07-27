---
title: 'Szybki start: Rozpoznawanie mowy, obiektyw-C-Speech Service'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak rozpoznawać mowę w celu macOS przy użyciu zestawu Speech SDK
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: chlandsi
ms.openlocfilehash: 45cd1210ee6af3c456171a427729f6e16caf2d58
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559355"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-macos-using-the-speech-sdk"></a>Szybki start: Rozpoznawanie mowy w celu języka C na macOS przy użyciu zestawu Speech SDK

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

W tym artykule dowiesz się, jak utworzyć aplikację macOS w zamierzeniu C Cognitive Services przy użyciu zestawu Speech SDK do transkrypcja mowy zarejestrowanej z mikrofonu do tekstu.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem pracy zapoznaj się z poniższą listą wymagań wstępnych:

* [Klucz subskrypcji](get-started.md) dla usługi Mowa
* Maszyna macOS z [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) lub nowszym oraz macOS 10,13 lub nowszym

## <a name="get-the-speech-sdk-for-macos"></a>Pobieranie zestawu Speech SDK dla macOS

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Bieżąca wersja zestawu Speech SDK usługi Cognitive Services to `1.6.0`.

Zestaw SDK mowy Cognitive Services dla komputerów Mac jest dystrybuowany jako pakiet platformy.
Może być używany w projektach Xcode jako [CocoaPod](https://cocoapods.org/)lub pobierany z https://aka.ms/csspeech/macosbinary i połączony ręcznie. Ten przewodnik używa CocoaPod.

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
1. Ustaw uprawnienia dostępu do sieci i mikrofonu. Kliknij nazwę aplikacji w pierwszym wierszu przeglądu po lewej stronie, aby przejść do konfiguracji aplikacji, a następnie wybierz kartę "możliwości".
    1. Włącz ustawienie "piaskownica aplikacji" dla aplikacji.
    1. Włącz pola wyboru dla dostępu "połączenia wychodzące" i "mikrofon".
    ![Ustawienia piaskownicy](media/sdk/qs-objectivec-macos-sandbox.png)
1. Aplikacja musi również zadeklarować użycie mikrofonu w `Info.plist` pliku. Kliknij plik w przeglądzie i Dodaj klucz "Opis użycia mikrofonu prywatności" z wartością, taką jak "mikrofon jest wymagany w przypadku rozpoznawania mowy".
    ![Ustawienia w info. plist](media/sdk/qs-objectivec-macos-info-plist.png)
1. Zamknij projekt Xcode. Będzie można użyć innego wystąpienia później po skonfigurowaniu CocoaPods.

## <a name="install-the-sdk-as-a-cocoapod"></a>Zainstaluj zestaw SDK jako CocoaPod

1. Zainstaluj Menedżera zależności CocoaPod zgodnie z opisem w [instrukcje dotyczące instalacji](https://guides.cocoapods.org/using/getting-started.html).
1. Przejdź do katalogu aplikacji przykładowej (`helloworld`). Umieść plik tekstowy o nazwie `Podfile` i następującej zawartości w tym katalogu:  
   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-macos/helloworld/Podfile)]
1. Przejdź do `helloworld` katalogu w terminalu i uruchom polecenie `pod install`. Spowoduje to wygenerowanie `helloworld.xcworkspace` obszaru roboczego Xcode zawierającego zarówno przykładową aplikację, jak i zestaw mowy SDK jako zależność. Ten obszar roboczy zostanie użyty w poniższej tabeli.

## <a name="add-the-sample-code"></a>Dodawanie przykładowego kodu

1. `helloworld.xcworkspace` Otwórz obszar roboczy w Xcode.
1. Zastąp zawartość automatycznie wygenerowanego pliku `AppDelegate.m` następującą zawartością:  
   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-macos/helloworld/helloworld/AppDelegate.m#code)]
1. Zastąp ciąg `YourSubscriptionKey` kluczem subskrypcji.
1. Zastąp ciąg `YourServiceRegion` [regionem](regions.md) skojarzonym z subskrypcją (na przykład `westus` w przypadku subskrypcji bezpłatnej wersji próbnej).

## <a name="build-and-run-the-sample"></a>Kompilowanie i uruchamianie przykładu

1. Uwidocznij dane wyjściowe debugowania (**View** > **Debug Area** > **Activate Console** — Widok > Obszar debugowania > Aktywuj konsolę).
1. Kompiluj i uruchamiaj przykładowy kod **, wybierając** > pozycję**Uruchom** z menu lub klikając przycisk **Odtwórz** .
1. Po kliknięciu przycisku i rozpoczęciu kilku wyrazów powinien zostać wyświetlony tekst mówiony w dolnej części ekranu. Po uruchomieniu aplikacji po raz pierwszy powinien zostać wyświetlony monit o przyznanie aplikacji dostępu do mikrofonu na komputerze.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami dla języka Objective-C w serwisie GitHub](https://aka.ms/csspeech/samples)
