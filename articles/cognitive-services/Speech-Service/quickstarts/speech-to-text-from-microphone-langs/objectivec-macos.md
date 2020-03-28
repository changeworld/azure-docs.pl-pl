---
title: 'Szybki start: rozpoznawanie mowy z mikrofonu, Objective-C - Usługa mowy'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak rozpoznać mowę w języku Objective-C w systemie macOS przy użyciu sdk mowy
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/23/2019
ms.author: chlandsi
ms.openlocfilehash: c2f0fbe66b26c6eca6e0c0b2530efacba9bae958
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75380596"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-macos-by-using-the-speech-sdk"></a>Szybki start: rozpoznawanie mowy w języku Objective-C w systemie macOS przy użyciu sdk mowy

Szybkie starty są również dostępne do [syntezy mowy.](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech-langs/objectivec-macos.md)

W tym artykule dowiesz się, jak utworzyć aplikację systemu macOS w języku Objective-C przy użyciu narzędzia Azure Cognitive Services Speech SDK do transkrypcji mowy nagranej z mikrofonu na tekst.

## <a name="prerequisites"></a>Wymagania wstępne

Zanim zaczniesz, będziesz potrzebować:

* [Klucz subskrypcji](~/articles/cognitive-services/Speech-Service/get-started.md) usługi Mowy.
* Komputer z systemem macOS z [kodem Xcode 9.4.1 lub nowszym](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) i macOS 10.13 lub nowszym.

## <a name="get-the-speech-sdk-for-macos"></a>Pobierz sdk mowy dla systemu macOS

[!INCLUDE [License notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Zestaw SDK mowy usług Cognitive Services dla komputerów Mac jest dystrybuowany jako pakiet framework. Może być używany w projektach Xcode jako [CocoaPod](https://cocoapods.org/) lub pobierany z https://aka.ms/csspeech/macosbinary i połączony ręcznie. W tym artykule użyto CocoaPod.

## <a name="create-an-xcode-project"></a>Tworzenie projektu Xcode

Uruchom xcode i rozpocznij nowy projekt, wybierając **pozycję Plik** > **nowego** > **projektu**. W oknie dialogowym wyboru szablonu wybierz szablon **Aplikacji Kakao.**

W następnych oknach dialogowych dokonaj następujących wyborów.

1. W oknie dialogowym **Opcje projektu:**
    1. Wprowadź nazwę aplikacji szybki start, na przykład *helloworld*.
    1. Wprowadź odpowiednią nazwę organizacji i identyfikator organizacji, jeśli masz już konto dewelopera firmy Apple. Do celów testowych należy użyć nazwy takiej jak *testorg*. Aby zarejestrować aplikację, musisz mieć odpowiedni profil aprowizacji. Aby uzyskać więcej informacji, zobacz [witrynę dewelopera firmy Apple](https://developer.apple.com/).
    1. Upewnij **się, że objective-C** jest wybrany jako język dla projektu.
    1. Wyczyść pola wyboru, aby używać scenoki i tworzyć aplikację opartą na dokumentach. Prosty interfejs użytkownika dla przykładowej aplikacji jest tworzony programowo.
    1. Wyczyść wszystkie pola wyboru dla testów i podstawowych danych.

    ![Ustawienia projektu](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-project-settings.png)

1. Wybierz katalog projektu:
    1. Wybierz katalog, w który ma być umieszczony projekt. Ten krok tworzy katalog helloworld w katalogu domowym, który zawiera wszystkie pliki dla projektu Xcode.
    1. Wyłącz tworzenie repozytorium Git dla tego przykładowego projektu.
1. Ustaw uprawnienia dostępu do sieci i mikrofonu. Wybierz nazwę aplikacji w pierwszym wierszu w przeglądzie po lewej stronie, aby przejść do konfiguracji aplikacji. Następnie wybierz kartę **Możliwości.**
    1. Włącz ustawienie **piaskownicy aplikacji** dla aplikacji.
    1. Zaznacz pola wyboru dla **połączeń wychodzących** i dostępu do **mikrofonu.**

    ![Ustawienia piaskownicy](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-sandbox.png)

1. Aplikacja musi również zadeklarować użycie mikrofonu w `Info.plist` pliku. Wybierz plik w przeglądzie i dodaj klucz **Prywatność — Opis użycia mikrofonu** o wartości takiej jak *Mikrofon jest potrzebny do rozpoznawania mowy*.

    ![Ustawienia w Info.plist](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-info-plist.png)

1. Zamknij projekt Xcode. Po skonfigurowaniu CocoaPods można użyć innego wystąpienia później.

## <a name="install-the-sdk-as-a-cocoapod"></a>Zainstaluj SDK jako CocoaPod

1. Zainstaluj menedżera zależności CocoaPod, jak opisano w [instrukcji instalacji.](https://guides.cocoapods.org/using/getting-started.html)
1. Przejdź do katalogu przykładowej aplikacji, która jest helloworld. Umieść plik tekstowy o nazwie *Podfile* i następującej zawartości w tym katalogu:

   [!code-ruby[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/macos/from-microphone/helloworld/Podfile)]
1. Przejdź do katalogu helloworld w terminalu i `pod install`uruchom polecenie . To polecenie generuje `helloworld.xcworkspace` obszar roboczy Xcode, który zawiera zarówno przykładową aplikację, jak i zestaw SDK mowy jako zależność. Ten obszar roboczy jest używany w poniższych krokach.

## <a name="add-the-sample-code"></a>Dodawanie przykładowego kodu

1. Otwórz obszar `helloworld.xcworkspace` roboczy w xcode.
1. Zastąp zawartość automatycznie `AppDelegate.m` generowanego pliku następującym kodem:

   [!code-objectivec[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/macos/from-microphone/helloworld/helloworld/AppDelegate.m#code)]
1. Zastąp ciąg `YourSubscriptionKey` kluczem subskrypcji.
1. Zastąp ciąg `YourServiceRegion` [regionem](~/articles/cognitive-services/Speech-Service/regions.md) skojarzonym z subskrypcją. Na przykład `westus` użyj bezpłatnej subskrypcji próbnej.

## <a name="build-and-run-the-sample"></a>Kompilowanie i uruchamianie przykładu

1. Ujrzaj dane wyjściowe debugowania, wybierając **pozycję Wyświetl** > obszar > **debugowania****Aktywuj konsolę**.
1. Skompiluj i uruchom przykładowy kod, wybierając z menu **przebieg produktu.** > **Run** Można również wybrać **Przycisk Odtwórz**.
1. Po wybraniu przycisku i wyponieniu kilku słów powinieneś zobaczyć tekst, który wypowiedziałeś w dolnej części ekranu. Po uruchomieniu aplikacji po raz pierwszy należy poprosić o przyznanie aplikacji dostępu do mikrofonu komputera.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami dla języka Objective-C w serwisie GitHub](https://aka.ms/csspeech/samples)
