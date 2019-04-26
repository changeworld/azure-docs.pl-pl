---
title: Szybki start — tworzenie aplikacji dla systemu iOS przy użyciu usługi Azure Spatial Anchors | Microsoft Docs
description: Z tego przewodnika Szybki start dowiesz się, jak utworzyć aplikację dla systemu iOS przy użyciu usługi Spatial Anchors.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: b8ee1bdfece35d774cdc79595a495b0b3719a836
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60234101"
---
# <a name="quickstart-create-an-ios-app-with-azure-spatial-anchors-in-either-swift-or-objective-c"></a>Szybki start: Tworzenie aplikacji dla systemu iOS przy użyciu usługi Azure Spatial Anchors w języku Swift lub Objective-C

W tym przewodniku Szybki start opisano, jak utworzyć aplikację dla systemu iOS przy użyciu usługi [Azure Spatial Anchors](../overview.md) w języku Swift lub Objective-C. Azure Spatial Anchors to usługa dla deweloperów programujących dla wielu platform, która pozwala kreować rozwiązania z rzeczywistością mieszaną z użyciem obiektów, których lokalizacja jest taka sama na różnych urządzeniach mimo upływu czasu. Gdy skończysz, będziesz mieć aplikację ARKit dla systemu iOS, która może zapisywać i przywoływać kotwicę przestrzenną.

Omawiane tematy:

> [!div class="checklist"]
> * Tworzenie konta usługi Spatial Anchors
> * Konfigurowanie identyfikatora i klucza konta usługi Spatial Anchors
> * Wdrażanie i uruchamianie na urządzeniu z systemem iOS

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start, upewnij się, że dysponujesz następującymi elementami:

- Deweloperska maszyna z systemem macOS oraz zainstalowanymi programami <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode 10 lub nowszym</a> i <a href="https://cocoapods.org" target="_blank">CocoaPods</a>.
- Git zainstalowanych za pomocą oprogramowania HomeBrew. Wprowadź następujące polecenie w jednym wierszu w terminalu: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Następnie uruchom `brew install git`.
- Deweloperskie urządzenie z systemem iOS, <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">zgodne z zestawem ARKit</a>.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Otwieranie przykładowego projektu

Użyj terminalu, aby wykonać następujące czynności.

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Zainstaluj konieczne zasobniki przy użyciu programu CocoaPods:

# <a name="swifttabopenproject-swift"></a>[Swift](#tab/openproject-swift)

Przejdź do adresu `iOS/Swift/`.

```bash
cd ./iOS/Swift/
```

# <a name="objective-ctabopenproject-objc"></a>[Objective-C](#tab/openproject-objc)

Przejdź do adresu `iOS/Objective-C/`.

```bash
cd ./iOS/Objective-C/
```

***

Uruchom polecenie `pod install --repo-update` w celu zainstalowania programu CocoaPods dla projektu.

Następnie otwórz plik `.xcworkspace` w programie Xcode.

# <a name="swifttabopenproject-swift"></a>[Swift](#tab/openproject-swift)

```bash
open ./SampleSwift.xcworkspace
```

# <a name="objective-ctabopenproject-objc"></a>[Objective-C](#tab/openproject-objc)

```bash
open ./SampleObjC.xcworkspace
```

***

## <a name="configure-account-identifier-and-key"></a>Konfigurowanie identyfikatora i klucza konta

Następnym krokiem jest, aby skonfigurować aplikację do używania Twojego identyfikatora konta i klucz konta. Skopiowane do edytora tekstu po [Definiowanie zasobów przestrzenne kotwic](#create-a-spatial-anchors-resource).

# <a name="swifttabopenproject-swift"></a>[Swift](#tab/openproject-swift)

Otwórz plik `iOS/Swift/SampleSwift/ViewControllers/BaseViewController.swift`.

Znajdź pole `spatialAnchorsAccountKey` i zastąp wartość `Set me` kluczem konta.

Znajdź pole `spatialAnchorsAccountId` i zastąp wartość `Set me` identyfikatorem konta.

# <a name="objective-ctabopenproject-objc"></a>[Objective-C](#tab/openproject-objc)

Otwórz plik `iOS/Objective-C/SampleObjC/BaseViewController.m`.

Znajdź pole `SpatialAnchorsAccountKey` i zastąp wartość `Set me` kluczem konta.

Znajdź pole `SpatialAnchorsAccountId` i zastąp wartość `Set me` identyfikatorem konta.

***

## <a name="deploy-the-app-to-your-ios-device"></a>Wdrażanie aplikacji na urządzeniu z systemem iOS

Podłącz urządzenie z systemem iOS do komputera Mac i ustaw **aktywny schemat** na urządzenie z systemem iOS.

![Wybieranie urządzenia](./media/get-started-ios/select-device.png)

Wybierz pozycję **Build and then run the current scheme (Skompiluj i uruchom bieżący schemat)**.

![Wdrażanie i uruchamianie](./media/get-started-ios/deploy-run.png)

> [!NOTE]
> Jeśli widzisz błąd `library not found for -lPods-SampleObjC`, prawdopodobnie został otwarty plik `.xcodeproj` zamiast `.xcworkspace`. Otwórz plik `.xcworkspace` i ponów próbę.

W programie Xcode zatrzymaj aplikację, naciskając pozycję **Stop**.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Samouczek: Udostępnianie kotwic przestrzennych na różnych urządzeniach](../tutorials/tutorial-share-anchors-across-devices.md)
