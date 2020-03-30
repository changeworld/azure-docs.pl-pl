---
title: 'Szybki start: tworzenie aplikacji xamarin na iOS'
description: W tym przewodniku Szybki start dowiesz się, jak utworzyć aplikację dla systemu iOS za pomocą platformy Xamarin przy użyciu zakotwiczeń przestrzennych.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 07/22/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: c5e217a33c8b461a438e2d0209fe6733850634e6
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "75465149"
---
# <a name="quickstart-create-a-xamarin-ios-app-with-azure-spatial-anchors"></a>Szybki start: tworzenie aplikacji xamarin na iOS za pomocą zakotwiczeń przestrzennych platformy Azure

Ten przewodnik Szybki start obejmuje sposób tworzenia aplikacji na iOS za pomocą platformy Xamarin przy użyciu [funkcji Azure Spatial Anchors](../overview.md). Azure Spatial Anchors to usługa dla deweloperów programujących dla wielu platform, która pozwala kreować rozwiązania z rzeczywistością mieszaną z użyciem obiektów, których lokalizacja jest taka sama na różnych urządzeniach mimo upływu czasu. Po zakończeniu będziesz mieć aplikację na iOS, która może zapisywać i odwoływać zakotwiczenie przestrzenne.

Omawiane tematy:

> [!div class="checklist"]
> * Tworzenie konta usługi Spatial Anchors
> * Konfigurowanie identyfikatora i klucza konta usługi Spatial Anchors
> * Wdrażanie i uruchamianie na urządzeniu z systemem iOS

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start, upewnij się, że dysponujesz następującymi elementami:
- Mac z systemem macOS High Sierra (10.13) lub wyższym z:
  - Najnowsza wersja Xcode i iOS SDK zainstalowana w [App Store](https://itunes.apple.com/us/app/xcode/id497799835?mt=12).
  - Aktualna wersja programu <a href="https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019" target="_blank">Visual Studio dla komputerów Mac 8.1+</a>.
  - <a href="https://git-scm.com/download/mac" target="_blank">Git dla systemu macOS</a>.
  - <a href="https://git-lfs.github.com/">Git LFS</a>.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Otwieranie przykładowego projektu

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Otwórz plik `Xamarin/SampleXamarin.sln` w programie Visual Studio.

## <a name="configure-account-identifier-and-key"></a>Konfigurowanie identyfikatora i klucza konta

Następnym krokiem jest skonfigurowanie aplikacji do używania identyfikatora konta i klucza konta. Skopiowano je do edytora tekstu podczas [konfigurowania zasobu Zakotwiczenia przestrzenne](#create-a-spatial-anchors-resource).

Otwórz plik `Xamarin/SampleXamarin.Common/AccountDetails.cs`.

Znajdź pole `SpatialAnchorsAccountKey` i zastąp wartość `Set me` kluczem konta.

Znajdź pole `SpatialAnchorsAccountId` i zastąp wartość `Set me` identyfikatorem konta.

## <a name="deploy-the-app-to-your-ios-device"></a>Wdrażanie aplikacji na urządzeniu z systemem iOS

Włącz urządzenie z systemem iOS, zaloguj się i podłącz go do komputera za pomocą kabla USB.

Ustaw projekt startowy na **SampleXamarin.iOS**, zmień **konfigurację rozwiązania** na **Zwolnij**i wybierz urządzenie, na które chcesz wdrożyć w z listy rozwijanej selektora urządzeń.

![Konfiguracja programu Visual Studio](./media/get-started-xamarin-iOS/visual-studio-macos-configuration.jpg)

Wybierz **pozycję Uruchom** > **start bez debugowania,** aby wdrożyć i uruchomić aplikację.

W aplikacji wybierz **opcję Podstawowe,** aby uruchomić demo i postępuj zgodnie z instrukcjami, aby umieścić i przywołać kotwicę.

> ![Zrzut ekranu](./media/get-started-xamarin-ios/screenshot-1.jpg)
> ![1](./media/get-started-xamarin-ios/screenshot-2.jpg)
> ![Zrzut ekranu 2 Zrzut ekranu 3](./media/get-started-xamarin-ios/screenshot-3.jpg)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Samouczek: Udostępnianie zakotwiczeń przestrzennych na różnych urządzeniach](../tutorials/tutorial-share-anchors-across-devices.md)
