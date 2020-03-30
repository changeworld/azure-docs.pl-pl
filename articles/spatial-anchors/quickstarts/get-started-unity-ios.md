---
title: 'Szybki start: tworzenie aplikacji Unity na iOS'
description: Z tego przewodnika Szybki start dowiesz się, jak utworzyć aplikację dla systemu iOS, używając aparatu Unity i usługi Spatial Anchors.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 37a82c4001dd42a4cfbbb9dabec29f28359afd75
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240590"
---
# <a name="quickstart-create-a-unity-ios-app-with-azure-spatial-anchors"></a>Szybki start: tworzenie aplikacji Unity na iOS za pomocą zakotwiczeń przestrzennych platformy Azure

Ten przewodnik Szybki start obejmuje sposób tworzenia aplikacji Unity na iOS przy użyciu [funkcji Azure Spatial Anchors](../overview.md). Azure Spatial Anchors to usługa dla deweloperów programujących dla wielu platform, która pozwala kreować rozwiązania z rzeczywistością mieszaną z użyciem obiektów, których lokalizacja jest taka sama na różnych urządzeniach mimo upływu czasu. Gdy skończysz, będziesz mieć aplikację ARKit dla systemu iOS utworzoną za pomocą aparatu Unity, która może zapisywać i przywoływać kotwicę przestrzenną.

Omawiane tematy:

> [!div class="checklist"]
> * Tworzenie konta usługi Spatial Anchors
> * Przygotowywanie ustawień kompilacji aparatu Unity
> * Konfigurowanie identyfikatora i klucza konta usługi Spatial Anchors
> * Eksportowanie projektu Xcode
> * Wdrażanie i uruchamianie na urządzeniu z systemem iOS

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start, upewnij się, że dysponujesz następującymi elementami:

- Komputer z systemem macOS z <a href="https://unity3d.com/get-unity/download" target="_blank">unity 2019.1 lub 2019.2</a>, najnowszą wersją <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode</a>i <a href="https://cocoapods.org" target="_blank">CocoaPods zainstalowany.</a>
- Git zainstalowany przez HomeBrew. Wprowadź następujące polecenie w jednym wierszu `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`terminala: . Następnie uruchom `brew install git` `brew install git-lfs`i .
- Deweloperskie urządzenie z systemem iOS, <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">zgodne z zestawem ARKit</a>.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Pobieranie i otwieranie przykładowego projektu Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

[!INCLUDE [iOS Unity Build Settings](../../../includes/spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>Konfigurowanie identyfikatora i klucza konta

W okienku **Project (Projekt)** przejdź do katalogu `Assets/AzureSpatialAnchors.Examples/Scenes` i otwórz plik sceny `AzureSpatialAnchorsBasicDemo.unity`.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Zapisz scenę, wybierając **pozycję Zapisz plik** -> **Save**.

## <a name="export-the-xcode-project"></a>Eksportowanie projektu Xcode

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

[!INCLUDE [Configure Xcode](../../../includes/spatial-anchors-unity-ios-xcode.md)]

Postępuj zgodnie z instrukcjami w aplikacji, aby umieścić i przywołać kotwicę.

Po zakończeniu zatrzymaj aplikację, naciskając **przycisk Zatrzymaj** w xcode.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="rendering-issues"></a>Problemy z renderowaniem

Jeśli podczas uruchamiania aplikacji nie widzisz aparatu jako tła (np. widzisz puste miejsce, niebieski kolor lub inne tekstury), prawdopodobnie musisz ponownie zaimportować zasoby w aparacie Unity. Zatrzymaj aplikację. Z górnego menu w unity wybierz pozycję **Zasoby -> Ponownie zaimportuj wszystkie**. Następnie ponownie uruchom aplikację.

### <a name="cocoapods-issues-on-macos-catalina-1015"></a>Problemy z cocoaPods w systemie macOS Catalina (10.15)

Jeśli niedawno zaktualizowano do systemu macOS Catalina (10.15) i miał CocoaPods zainstalowany wcześniej, CocoaPods może być `.xcworkspace` w stanie uszkodzonym i nie poprawnie skonfigurować strąki i pliki projektu. Aby rozwiązać ten problem, musisz ponownie zainstalować CocoaPods, uruchamiając następujące polecenia:

```shell
brew update
brew install cocoapods --build-from-source
brew link --overwrite cocoapods
```

### <a name="unity-20193"></a>Jedność 2019.3

Ze względu na przełomowe zmiany, Unity 2019.3 nie jest obecnie obsługiwany. Prosimy o korzystanie z Unity 2019.1 lub 2019.2.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Samouczek: Udostępnianie zakotwiczeń przestrzennych na różnych urządzeniach](../tutorials/tutorial-share-anchors-across-devices.md)
