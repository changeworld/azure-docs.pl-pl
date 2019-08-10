---
title: Szybki Start — Tworzenie aplikacji platformy Xamarin dla systemu iOS z zakotwiczeniami przestrzennymi Azure | Microsoft Docs
description: W tym przewodniku szybki start dowiesz się, jak utworzyć aplikację dla systemu iOS za pomocą platformy Xamarin przy użyciu kotwic przestrzennych.
author: craigktreasure
manager: virivera
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 07/22/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: ef25cf07326220be36ce8f67267428ffe1ac0728
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68931644"
---
# <a name="quickstart-create-a-xamarin-ios-app-with-azure-spatial-anchors"></a>Szybki start: Tworzenie aplikacji platformy Xamarin dla systemu iOS przy użyciu kotwic środowiska Azure

W tym przewodniku szybki start opisano sposób tworzenia aplikacji dla systemu iOS przy użyciu funkcji [Azure przestrzenny kotwic](../overview.md). Azure Spatial Anchors to usługa dla deweloperów programujących dla wielu platform, która pozwala kreować rozwiązania z rzeczywistością mieszaną z użyciem obiektów, których lokalizacja jest taka sama na różnych urządzeniach mimo upływu czasu. Po zakończeniu będziesz mieć aplikację systemu iOS, która będzie mogła zapisywać i odwoływać kotwicę przestrzenną.

Omawiane tematy:

> [!div class="checklist"]
> * Tworzenie konta usługi Spatial Anchors
> * Konfigurowanie identyfikatora i klucza konta usługi Spatial Anchors
> * Wdrażanie i uruchamianie na urządzeniu z systemem iOS

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start, upewnij się, że dysponujesz następującymi elementami:
- Komputery Mac z systemem macOS High Sierra (10,13) lub nowszym z:
  - Najnowsza wersja oprogramowania Xcode i zestawu iOS SDK zainstalowana ze [sklepu App Store](https://itunes.apple.com/us/app/xcode/id497799835?mt=12).
  - Aktualna wersja <a href="https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019" target="_blank">Visual Studio dla komputerów Mac 8.1 +</a>.
  - <a href="https://git-scm.com/download/mac" target="_blank">Git for macOS</a>

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Otwieranie przykładowego projektu

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Otwórz plik `Xamarin/SampleXamarin.sln` w programie Visual Studio.

## <a name="configure-account-identifier-and-key"></a>Konfigurowanie identyfikatora i klucza konta

Następnym krokiem jest skonfigurowanie aplikacji w taki sposób, aby korzystała z identyfikatora konta i klucza konta. Skopiowano je do edytora tekstu podczas [konfigurowania zasobów kotwic przestrzennych](#create-a-spatial-anchors-resource).

Otwórz `Xamarin/SampleXamarin.Common/AccountDetails.cs`.

Znajdź pole `SpatialAnchorsAccountKey` i zastąp wartość `Set me` kluczem konta.

Znajdź pole `SpatialAnchorsAccountId` i zastąp wartość `Set me` identyfikatorem konta.

## <a name="deploy-the-app-to-your-ios-device"></a>Wdrażanie aplikacji na urządzeniu z systemem iOS

Włącz urządzenie z systemem iOS, zaloguj się i połącz je z komputerem przy użyciu kabla USB.

Ustaw projekt startowy na **SampleXamarin. iOS**, Zmień **konfigurację rozwiązania** na **Zwolnij**, a następnie wybierz urządzenie, które chcesz wdrożyć, na liście rozwijanej selektora urządzeń.

![Konfiguracja programu Visual Studio](./media/get-started-xamarin-iOS/visual-studio-macos-configuration.jpg)

Wybierz pozycję **Uruchom** > **Uruchom bez debugowania** , aby wdrożyć i uruchomić aplikację.

W aplikacji wybierz pozycję **podstawowa** , aby uruchomić demonstrację, i postępuj zgodnie z instrukcjami, aby umieścić i odwołać kotwicę.

> ![Zrzut ekranu](./media/get-started-xamarin-ios/screenshot-1.jpg)
> 1![zrzut](./media/get-started-xamarin-ios/screenshot-2.jpg)
> ekranu2![zrzut ekranu 3](./media/get-started-xamarin-ios/screenshot-3.jpg)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Samouczek: Udostępnianie kotwic przestrzennych na różnych urządzeniach](../tutorials/tutorial-share-anchors-across-devices.md)
