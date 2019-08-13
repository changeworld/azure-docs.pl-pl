---
title: 'Samouczek: Tworzenie aplikacji dla systemu iOS, która pobiera zdjęcie i uruchamia ją w czytniku immersyjny (SWIFT)'
titlesuffix: Azure Cognitive Services
description: W tym samouczku utworzysz aplikację dla systemu iOS od podstaw i dodasz obraz do funkcji czytnika immersyjny.
services: cognitive-services
author: MeganRoach
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 08/01/2019
ms.author: t-meroa
ms.openlocfilehash: 586d05fe5765e711cce5553ab75ad9dcb491bfb9
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/12/2019
ms.locfileid: "68950877"
---
# <a name="tutorial-create-an-ios-app-that-launches-the-immersive-reader-with-content-from-a-photo-swift"></a>Samouczek: Tworzenie aplikacji dla systemu iOS, która uruchamia czytnik immersyjny z zawartością ze zdjęcia (SWIFT)

[Czytnik immersyjny](https://www.onenote.com/learningtools) jest specjalnie zaprojektowanym narzędziem, które implementuje sprawdzone techniki w celu zwiększenia czytelności.

[Interfejs API odczytu przetwarzanie obrazów Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/computer-vision/concept-recognizing-text) wykrywa zawartość tekstową na obrazie przy użyciu najnowszych modeli rozpoznawania firmy Microsoft i konwertuje zidentyfikowany tekst na strumień znaków do odczytu maszynowego.

W tym samouczku utworzysz aplikację dla systemu iOS od podstaw i zintegrujesz interfejs API odczytu oraz czytnik immersyjny przy użyciu zestawu SDK czytnika immersyjny. Pełny przykład pracy w tym samouczku jest dostępny [tutaj](https://github.com/microsoft/immersive-reader-sdk/iOS/samples/picture-to-immersive-reader-swift).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

* [Xcode](https://apps.apple.com/us/app/xcode/id497799835?mt=12)
* Zasób czytnika immersyjny skonfigurowany do uwierzytelniania Azure Active Directory (Azure AD). Postępuj zgodnie z [tymi instrukcjami](./azure-active-directory-authentication.md) , aby rozpocząć konfigurację. W przypadku konfigurowania przykładowych właściwości projektu konieczne będzie utworzenie niektórych wartości. Zapisz dane wyjściowe sesji w pliku tekstowym do użycia w przyszłości.
* Użycie tego przykładu wymaga subskrypcji platformy Azure w usłudze przetwarzanie obrazów poznawczej. [Utwórz zasób usługi przetwarzanie obrazów poznawczej w Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision).

## <a name="create-an-xcode-project"></a>Tworzenie projektu Xcode

Utwórz nowy projekt w Xcode.

![Nowy projekt](./media/ios/xcode-create-project.png)

Wybierz **aplikację z pojedynczym widokiem**.

![Nowa aplikacja z pojedynczym widokiem](./media/ios/xcode-single-view-app.png)

## <a name="get-the-sdk-cocoapod"></a>Pobierz zestaw SDK CocoaPod
Najprostszym sposobem korzystania z zestawu SDK czytnika immersyjny jest za pośrednictwem CocoaPods. Aby zainstalować za pośrednictwem Cocoapods:
1. [Install CocoaPods](http://guides.cocoapods.org/using/getting-started.html) — postępuj zgodnie z przewodnikiem wprowadzenie, aby zainstalować CocoaPods.
2. Utwórz element plik podfile, uruchamiając `pod init` w katalogu głównym projektu Xcode.
3.  Dodaj CocoaPod do plik podfile przez dodanie `pod 'immersive-reader-sdk', :path => 'https://github.com/microsoft/immersive-reader-sdk/iOS/immersive-reader-sdk'`. Twoja plik podfile powinna wyglądać podobnie do poniższego, a nazwa docelowa:
 ```ruby
  platform :ios, '9.0'

  target 'picture-to-immersive-reader-swift' do
  use_frameworks!
  # Pods for picture-to-immersive-reader-swift
  pod 'immersive-reader-sdk', :path => 'https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/immersive-reader-sdk'
  end
```
4. W terminalu, w katalogu projektu Xcode, uruchom polecenie `pod install` , aby zainstalować zestaw SDK programu immersyjny pod.
5. Dodaj `import immersive_reader_sdk` do wszystkich plików, które muszą odwoływać się do zestawu SDK.
6. Upewnij się, że otwarto projekt, `.xcworkspace` otwierając plik, a `.xcodeproj` nie plik.

## <a name="acquire-an-azure-ad-authentication-token"></a>Uzyskiwanie tokenu uwierzytelniania usługi Azure AD

Wymagane są pewne wartości z kroku wymagań wstępnych konfiguracji uwierzytelniania usługi Azure AD dla tej części. Odwołaj się do pliku tekstowego zapisanego w tej sesji.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

W głównym folderze projektu, który zawiera plik plik viewcontroller. Swift, Utwórz plik klasy SWIFT o nazwie stałych. Swift. Zastąp klasę następującym kodem, dodając je do wartości, o ile ma to zastosowanie. Zachowaj ten plik jako plik lokalny, który istnieje tylko na komputerze i upewnij się, że nie zatwierdzisz tego pliku w kontroli źródła, ponieważ zawiera on klucze tajne, które nie powinny być publiczne. Zaleca się, aby nie przechowywać wpisów tajnych w aplikacji. Zamiast tego zalecamy użycie usługi wewnętrznej bazy danych w celu uzyskania tokenu, w którym wpisy tajne mogą być przechowywane poza aplikacją i poza urządzeniem. Punkt końcowy interfejsu API zaplecza powinien być zabezpieczony za pomocą uwierzytelniania (na przykład [OAuth](https://oauth.net/2/)), aby uniemożliwić nieautoryzowanym użytkownikom uzyskanie tokenów, które mają być używane z usługą czytnika danych immersyjny i rozliczeniami. Ta praca wykracza poza zakres tego samouczka.

[!code-swift[Constants](~/ImmersiveReaderSdk/iOS/samples/picture-to-immersive-reader-swift/picture-to-immersive-reader-swift/Constants.swift)]

## <a name="set-up-the-app-to-run-without-a-storyboard"></a>Skonfiguruj aplikację do uruchamiania bez scenorysu

Otwórz plik AppDelegate. Swift i zastąp go następującym kodem.

[!code-swift[AppDelegate](~/ImmersiveReaderSdk/iOS/samples/picture-to-immersive-reader-swift/picture-to-immersive-reader-swift/AppDelegate.swift)]

## <a name="add-functionality-for-taking-and-uploading-photos"></a>Dodawanie funkcji do pobierania i przekazywania zdjęć

Zmień nazwę plik viewcontroller. Swift na PictureLaunchViewController. Swift i Zastąp plik poniższym kodem.

[!code-swift[PictureLaunchViewController](~/ImmersiveReaderSdk/iOS/samples/picture-to-immersive-reader-swift/picture-to-immersive-reader-swift/PictureLaunchViewController.swift)]

## <a name="build-and-run-the-app"></a>Kompilowanie i uruchamianie aplikacji

Ustaw schemat Archiwum w Xcode, wybierając symulator lub urządzenie docelowe.
![Schemat Archiwum](./media/ios/xcode-archive-scheme.png)<br/>
![Wybierz element docelowy](./media/ios/xcode-select-target.png)

W Xcode naciśnij klawisze CTRL + R lub kliknij przycisk Odtwórz, aby uruchomić projekt, a aplikacja powinna zostać uruchomiona w określonym symulatorze lub urządzeniu.

W aplikacji powinny zostać wyświetlone następujące tematy:

![Przykładowa aplikacja](./media/ios/picture-to-immersive-reader-ipad-app.png)

W aplikacji Zrób lub Przekaż zdjęcie tekstu, naciskając przycisk "Wypełnij fotografię" lub "Wybierz zdjęcie z biblioteki", a następnie czytnik immersyjny uruchomi tekst ze zdjęcia.

![Czytnik immersyjny](./media/ios/picture-to-immersive-reader-ipad.png)

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z [zestawem SDK programu immersyjny dla systemu iOS](https://github.com/microsoft/immersive-reader-sdk/iOS) i [Kompendium zestawu SDK systemu iOS dla czytnika immersyjny](./ios-reference.md)
