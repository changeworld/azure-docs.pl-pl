---
title: 'Szybki start: tworzenie aplikacji na iOS'
description: Z tego przewodnika Szybki start dowiesz się, jak utworzyć aplikację dla systemu iOS przy użyciu usługi Spatial Anchors.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 6304077a26f5c0ecb91e1ec4936bd79b3d839d95
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79471221"
---
# <a name="quickstart-create-an-ios-app-with-azure-spatial-anchors-in-either-swift-or-objective-c"></a>Szybki start: tworzenie aplikacji na iOS za pomocą zakotwiczeń przestrzennych platformy Azure w trybie Swift lub Objective-C

W tym przewodniku Szybki start opisano, jak utworzyć aplikację dla systemu iOS przy użyciu usługi [Azure Spatial Anchors](../overview.md) w języku Swift lub Objective-C. Azure Spatial Anchors to usługa dla deweloperów programujących dla wielu platform, która pozwala kreować rozwiązania z rzeczywistością mieszaną z użyciem obiektów, których lokalizacja jest taka sama na różnych urządzeniach mimo upływu czasu. Gdy skończysz, będziesz mieć aplikację ARKit dla systemu iOS, która może zapisywać i przywoływać kotwicę przestrzenną.

Omawiane tematy:

> [!div class="checklist"]
> * Tworzenie konta usługi Spatial Anchors
> * Konfigurowanie identyfikatora i klucza konta usługi Spatial Anchors
> * Wdrażanie i uruchamianie na urządzeniu z systemem iOS

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start, upewnij się, że dysponujesz następującymi elementami:

- Urządzenie z systemem macOS z zainstalowaną najnowszą wersją <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">xcode</a> i <a href="https://cocoapods.org" target="_blank">cocoaPods.</a>
- Git zainstalowany przez HomeBrew:
  1. Wprowadź następujące polecenie jako pojedynczy wiersz `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`w terminalu: . 
  1. Uruchom polecenia `brew install git` i `brew install git-lfs`.
  1. Zaktualizuj swój `git lfs install` gif z (dla `git lfs install --system` bieżącego użytkownika) lub (dla całego systemu).
- Deweloperskie urządzenie z systemem iOS, <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">zgodne z zestawem ARKit</a>.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Otwieranie przykładowego projektu

Użyj terminalu, aby wykonać następujące czynności.

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Zainstaluj konieczne zasobniki przy użyciu programu CocoaPods:

# <a name="swift"></a>[Swift](#tab/openproject-swift)

Przejdź do adresu `iOS/Swift/`.

```bash
cd ./iOS/Swift/
```

# <a name="objective-c"></a>[Cel C](#tab/openproject-objc)

Przejdź do adresu `iOS/Objective-C/`.

```bash
cd ./iOS/Objective-C/
```

---

Uruchom polecenie `pod install --repo-update` w celu zainstalowania programu CocoaPods dla projektu.

Następnie otwórz plik `.xcworkspace` w programie Xcode.

> [!NOTE]
> Zapoznaj się z instrukcjami rozwiązywania [problemów tutaj,](#cocoapods-issues-on-macos-catalina-1015) jeśli masz problemy z CocoaPod po uaktualnieniu do systemu macOS Catalina (10.15).

# <a name="swift"></a>[Swift](#tab/openproject-swift)

```bash
open ./SampleSwift.xcworkspace
```

# <a name="objective-c"></a>[Cel C](#tab/openproject-objc)

```bash
open ./SampleObjC.xcworkspace
```

---

## <a name="configure-account-identifier-and-key"></a>Konfigurowanie identyfikatora i klucza konta

Następnym krokiem jest skonfigurowanie aplikacji do używania identyfikatora konta i klucza konta. Skopiowano je do edytora tekstu podczas [konfigurowania zasobu Zakotwiczenia przestrzenne](#create-a-spatial-anchors-resource).

# <a name="swift"></a>[Swift](#tab/openproject-swift)

Otwórz plik `iOS/Swift/SampleSwift/ViewControllers/BaseViewController.swift`.

Znajdź pole `spatialAnchorsAccountKey` i zastąp wartość `Set me` kluczem konta.

Znajdź pole `spatialAnchorsAccountId` i zastąp wartość `Set me` identyfikatorem konta.

# <a name="objective-c"></a>[Cel C](#tab/openproject-objc)

Otwórz plik `iOS/Objective-C/SampleObjC/BaseViewController.m`.

Znajdź pole `SpatialAnchorsAccountKey` i zastąp wartość `Set me` kluczem konta.

Znajdź pole `SpatialAnchorsAccountId` i zastąp wartość `Set me` identyfikatorem konta.

---

## <a name="deploy-the-app-to-your-ios-device"></a>Wdrażanie aplikacji na urządzeniu z systemem iOS

Podłącz urządzenie z systemem iOS do komputera Mac i ustaw **aktywny schemat** na urządzenie z systemem iOS.

![Wybieranie urządzenia](./media/get-started-ios/select-device.png)

Wybierz pozycję **Build and then run the current scheme (Skompiluj i uruchom bieżący schemat)**.

![Wdrażanie i uruchamianie](./media/get-started-ios/deploy-run.png)

> [!NOTE]
> Jeśli widzisz błąd `library not found for -lPods-SampleObjC`, prawdopodobnie został otwarty plik `.xcodeproj` zamiast `.xcworkspace`. Otwórz plik `.xcworkspace` i ponów próbę.

W programie Xcode zatrzymaj aplikację, naciskając pozycję **Stop**.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="cocoapods-issues-on-macos-catalina-1015"></a>Problemy z cocoaPods w systemie macOS Catalina (10.15)

Jeśli niedawno zaktualizowano do systemu macOS Catalina (10.15) i miał CocoaPods zainstalowany wcześniej, CocoaPods może być `.xcworkspace` w stanie uszkodzonym i nie poprawnie skonfigurować strąki i pliki projektu. Aby rozwiązać ten problem, musisz ponownie zainstalować CocoaPods, uruchamiając następujące polecenia:

```shell
brew update
brew install cocoapods --build-from-source
brew link --overwrite cocoapods
```

### <a name="app-crashes-when-deploying-to-ios-1031-from-a-personal-provisioning-profiledeveloper-account"></a>Aplikacja ulega awarii podczas wdrażania w uos 10.3.1 z osobistego profilu aprowizacji/konta dewelopera 

Jeśli wdrożysz aplikację dla systemu iOS w systemie iOS 10.3.1 z osobistego `Library not loaded: @rpath/ADAL...`profilu aprowizacji/konta dewelopera, może zostać wyświetlony ten błąd: . 

Aby rozwiązać ten problem:

- Użyj profilu inicjowania obsługi administracyjnej, który nie jest profilem zespołu osobistego (płatne konto dewelopera).
- Wdrażanie aplikacji na urządzeniu z systemem iOS z systemem iOS 13.3 lub starszym lub na urządzeniu z systemem iOS 13.4 w wersji beta lub wersji.
- Dowiedz się więcej o tym problemie na [przepełnienie stosu](https://stackoverflow.com/questions/60015309/running-ios-apps-causes-runtime-error-for-frameworks-code-signature-invalid).


[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Samouczek: Udostępnianie zakotwiczeń przestrzennych na różnych urządzeniach](../tutorials/tutorial-share-anchors-across-devices.md)
