---
title: 'Szybki start: tworzenie aplikacji platformy Xamarin na Androida'
description: W tym przewodniku Szybki start dowiesz się, jak utworzyć aplikację dla systemu Android za pomocą platformy Xamarin przy użyciu zakotwiczeń przestrzennych.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 07/22/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 94906d645ec38a24d54536ee8aa93e7418c8dc35
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "75465179"
---
# <a name="quickstart-create-a-xamarin-android-app-with-azure-spatial-anchors"></a>Szybki start: tworzenie aplikacji platformy Xamarin dla systemu Android za pomocą zakotwiczeń przestrzennych platformy Azure

Ten przewodnik Szybki start obejmuje sposób tworzenia aplikacji dla systemu Android za pomocą platformy Xamarin przy użyciu [funkcji Azure Spatial Anchors](../overview.md). Azure Spatial Anchors to usługa dla deweloperów programujących dla wielu platform, która pozwala kreować rozwiązania z rzeczywistością mieszaną z użyciem obiektów, których lokalizacja jest taka sama na różnych urządzeniach mimo upływu czasu. Po zakończeniu będziesz mieć aplikację na Androida, która może zapisywać i odwoływać kotwicę przestrzenną.

Omawiane tematy:

> [!div class="checklist"]
> * Tworzenie konta usługi Spatial Anchors
> * Konfigurowanie identyfikatora i klucza konta usługi Spatial Anchors
> * Wdrażanie i uruchamianie na urządzeniu z systemem Android

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start, upewnij się, że dysponujesz następującymi elementami:
- Komputer z systemem Windows lub macOS:
  - W przypadku korzystania z systemu Windows:
    - Aktualna wersja <a href="https://www.visualstudio.com/downloads/" target="_blank">programu Visual Studio 2019 16.2+</a>.
    - <a href="https://git-scm.com/download/win" target="_blank">Git dla Windows</a>.
    - <a href="https://git-lfs.github.com/">Git LFS</a>.
  - W przypadku korzystania z systemu macOS:
    - Aktualna wersja programu <a href="https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019" target="_blank">Visual Studio dla komputerów Mac 8.1+</a>.
    - <a href="https://git-scm.com/download/mac" target="_blank">Git dla systemu macOS</a>.
    - <a href="https://git-lfs.github.com/">Git LFS</a>.
- Najnowsza wersja platformy Xamarin.Android zainstalowana i uruchomiona na wybranej platformie. Przewodnik po instalacji platformy Xamarin.Android można znaleźć w podręcznikach [instalacji platformy Xamarin.Android.](https://docs.microsoft.com/xamarin/android/get-started/installation/index)
- Urządzenie z systemem Android <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">pracujące w trybie dewelopera</a> i <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">zgodne z platformą ARCore</a>.
  - Aby komputer mógł komunikować się z urządzeniem z Androidem, mogą być wymagane dodatkowe sterowniki urządzeń. Aby uzyskać więcej informacji, zobacz [tutaj](https://developer.android.com/studio/run/device.html).
- Aplikacja musi być skierowana do ARCore **1.8**.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Otwieranie przykładowego projektu

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Otwórz plik `Xamarin/SampleXamarin.sln` w programie Visual Studio.

## <a name="configure-account-identifier-and-key"></a>Konfigurowanie identyfikatora i klucza konta

Następnym krokiem jest skonfigurowanie aplikacji do używania identyfikatora konta i klucza konta. Skopiowano je do edytora tekstu podczas [konfigurowania zasobu Zakotwiczenia przestrzenne](#create-a-spatial-anchors-resource).

Otwórz plik `Xamarin/SampleXamarin.Common/AccountDetails.cs`.

Znajdź pole `SpatialAnchorsAccountKey` i zastąp wartość `Set me` kluczem konta.

Znajdź pole `SpatialAnchorsAccountId` i zastąp wartość `Set me` identyfikatorem konta.

## <a name="deploy-the-app-to-your-android-device"></a>Wdrażanie aplikacji na urządzeniu z systemem Android

Włącz urządzenie z Systemem Android, zaloguj się i podłącz go do komputera za pomocą kabla USB.

Ustaw projekt uruchamiania na **SampleXamarin.Android**, zmień **konfigurację rozwiązania** na **Wydanie**i wybierz urządzenie, które chcesz wdrożyć w z listy rozwijanej selektora urządzeń.

# <a name="windows"></a>[Windows](#tab/deploy-windows)

![Konfiguracja programu Visual Studio](./media/get-started-xamarin-android/visual-studio-windows-configuration.png)

Wybierz **debugowanie** > **Rozpocznij debugowanie,** aby wdrożyć i uruchomić aplikację.

# <a name="macos"></a>[Macos](#tab/deploy-macos)

![Konfiguracja programu Visual Studio](./media/get-started-xamarin-android/visual-studio-macos-configuration.jpg)

Wybierz **pozycję Uruchom** > **start bez debugowania,** aby wdrożyć i uruchomić aplikację.

---

W aplikacji wybierz **opcję Podstawowe,** aby uruchomić demo i postępuj zgodnie z instrukcjami, aby umieścić i przywołać kotwicę.

> ![Zrzut ekranu](./media/get-started-xamarin-android/screenshot-1.jpg)
> ![1](./media/get-started-xamarin-android/screenshot-2.jpg)
> ![Zrzut ekranu 2 Zrzut ekranu 3](./media/get-started-xamarin-android/screenshot-3.jpg)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Samouczek: Udostępnianie zakotwiczeń przestrzennych na różnych urządzeniach](../tutorials/tutorial-share-anchors-across-devices.md)
