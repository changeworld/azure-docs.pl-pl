---
title: 'Samouczek: Tworzenie aplikacji na iOS, która robi zdjęcie i uruchamia je w czytniku Immersive Reader (Swift)'
titleSuffix: Azure Cognitive Services
description: W tym samouczku zbudujemy aplikację dla systemu iOS od podstaw i dodasz funkcję Obraz do programu Immersive Reader.
services: cognitive-services
author: metanMSFT
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: 69ff58d6cdabe49000b00afecfc6b4ad1a3f2daa
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841850"
---
# <a name="tutorial-create-an-ios-app-that-launches-the-immersive-reader-with-content-from-a-photo-swift"></a>Samouczek: Tworzenie aplikacji na iOS, która uruchamia Immersive Reader z zawartością ze zdjęcia (Swift)

[Immersive Reader](https://www.onenote.com/learningtools) to zaprojektowane z włącznie narzędzie, które wdraża sprawdzone techniki poprawiające rozumienie czytania.

[Interfejs API odczytu usług Computer Vision Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/computer-vision/concept-recognizing-text) wykrywa zawartość tekstową obrazu przy użyciu najnowszych modeli rozpoznawania firmy Microsoft i konwertuje zidentyfikowany tekst na strumień znaków do odczytu maszynowego.

W tym samouczku skompilujesz aplikację dla systemu iOS od podstaw i zintegrujesz interfejs API odczytu oraz program Immersive Reader przy użyciu zestawu Immersive Reader SDK. Pełny przykład pracy tego samouczka jest dostępny [tutaj](https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/samples/picture-to-immersive-reader-swift).

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

* [Xcode](https://apps.apple.com/us/app/xcode/id497799835?mt=12)
* Zasób programu Immersive Reader skonfigurowany do uwierzytelniania usługi Azure Active Directory. Postępuj zgodnie [z tymi instrukcjami,](./how-to-create-immersive-reader.md) aby skonfigurować. Podczas konfigurowania przykładowych właściwości projektu potrzebne będą niektóre wartości utworzone w tym miejscu. Zapisz dane wyjściowe sesji w pliku tekstowym do wykorzystania w przyszłości.
* Użycie tego przykładu wymaga subskrypcji platformy Azure do usługi Computer Vision Cognitive Service. [Utwórz zasób usługi Cognitive Service w witrynie Azure portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision).

## <a name="create-an-xcode-project"></a>Tworzenie projektu Xcode

Utwórz nowy projekt w xcode.

![Nowy projekt](./media/ios/xcode-create-project.png)

Wybierz **pozycję Aplikacja do pojedynczego widoku**.

![Nowa aplikacja jednoznaka](./media/ios/xcode-single-view-app.png)

## <a name="get-the-sdk-cocoapod"></a>Pobierz cocoaPod SDK
Najprostszym sposobem korzystania z immersive Reader SDK jest cocoaPods. Aby zainstalować za pośrednictwem cocoapods:
1. [Zainstaluj CocoaPods](http://guides.cocoapods.org/using/getting-started.html) - Postępuj zgodnie z przewodnikiem wprowadzenie do instalacji Cocoapods.
2. Utwórz plik podfile, uruchamiając `pod init` w katalogu głównym projektu Xcode.
3.  Dodaj CocoaPod do pliku Podfile, dodając `pod 'immersive-reader-sdk', :path => 'https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/immersive-reader-sdk'`. Twój Podfile powinien wyglądać następująco, a nazwa twojego celu zastępuje picture-to-immersive-reader-swift:
 ```ruby
  platform :ios, '9.0'

  target 'picture-to-immersive-reader-swift' do
  use_frameworks!
  # Pods for picture-to-immersive-reader-swift
  pod 'immersive-reader-sdk', :path => 'https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/immersive-reader-sdk'
  end
```
4. W terminalu w katalogu projektu Xcode uruchom polecenie, `pod install` aby zainstalować kapsułę Immersive Reader SDK.
5. Dodaj `import immersive_reader_sdk` do wszystkich plików, które muszą odwoływać się do SDK.
6. Upewnij się, aby otworzyć `.xcworkspace` projekt, `.xcodeproj` otwierając plik, a nie plik.

## <a name="acquire-an-azure-ad-authentication-token"></a>Uzyskiwanie tokenu uwierzytelniania usługi Azure AD

Dla tej części potrzebne są pewne wartości z powyższego kroku wstępnego konfiguracji uwierzytelniania usługi Azure AD. Odsyłanie z powrotem do pliku tekstowego zapisanego w tej sesji.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

W głównym folderze projektu, który zawiera plik ViewController.swift, utwórz plik klasy Swift o nazwie Constants.swift. Zastąp klasę następującym kodem, dodając w razie potrzeby wartości. Zachowaj ten plik jako plik lokalny, który istnieje tylko na komputerze i upewnij się, że nie zatwierdzić tego pliku do kontroli źródła, ponieważ zawiera wpisy tajne, które nie powinny być upublicznione. Zaleca się, aby nie przechowywać wpisów tajnych w aplikacji. Zamiast tego zaleca się użycie usługi wewnętrznej bazy danych w celu uzyskania tokenu, gdzie wpisy tajne mogą być przechowywane poza aplikacją i poza urządzeniem. Punkt końcowy interfejsu API wewnętrznej bazy danych powinien być zabezpieczony za jakąś formą uwierzytelniania (na przykład [OAuth),](https://oauth.net/2/)aby uniemożliwić nieautoryzowanym użytkownikom uzyskiwanie tokenów do użycia w usłudze Immersive Reader i rozliczeniach; że praca wykracza poza zakres tego samouczka.

## <a name="set-up-the-app-to-run-without-a-storyboard"></a>Konfigurowanie aplikacji do uruchamiania bez serii ujęć

Otwórz appdelegate.swift i zastąp plik następującym kodem.

## <a name="add-functionality-for-taking-and-uploading-photos"></a>Dodawanie funkcji do robienia i przesyłania zdjęć

Zmień nazwę ViewController.swift na PictureLaunchViewController.swift i zastąp plik następującym kodem.

## <a name="build-and-run-the-app"></a>Kompilowanie i uruchamianie aplikacji

Ustaw schemat archiwizacji w xcode, wybierając symulator lub urządzenie docelowe.
![Schemat archiwizacji](./media/ios/xcode-archive-scheme.png)<br/>
![Wybieranie obiektu docelowego](./media/ios/xcode-select-target.png)

W xcode naciśnij klawisze Ctrl + R lub kliknij przycisk odtwarzania, aby uruchomić projekt, a aplikacja powinna zostać uruchomiona na określonym symulatorze lub urządzeniu.

W aplikacji powinieneś zobaczyć:

![Przykładowa aplikacja](./media/ios/picture-to-immersive-reader-ipad-app.png)

Wewnątrz aplikacji zrób lub prześlij zdjęcie tekstu, naciskając przycisk "Zrób zdjęcie" lub "Wybierz zdjęcie z biblioteki", a czytnik Immersive uruchomi wyświetlanie tekstu ze zdjęcia.

![Czytnik immersyjny](./media/ios/picture-to-immersive-reader-ipad.png)

## <a name="next-steps"></a>Następne kroki

* Poznaj [dokumentację immersyjnego sdk czytnika](./reference.md)
