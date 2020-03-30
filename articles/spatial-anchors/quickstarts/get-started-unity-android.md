---
title: 'Szybki start: tworzenie aplikacji Unity na Androida'
description: Z tego przewodnika Szybki start dowiesz się, jak utworzyć aplikację dla systemu Android, używając aparatu Unity i usługi Spatial Anchors.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 7acff7f0249cdedcebd367fc315be92cafb9ab78
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77615443"
---
# <a name="quickstart-create-a-unity-android-app-with-azure-spatial-anchors"></a>Szybki start: tworzenie aplikacji Unity dla systemu Android za pomocą zakotwiczeń przestrzennych platformy Azure

Ten przewodnik Szybki start obejmuje sposób tworzenia aplikacji Unity dla systemu Android przy użyciu [funkcji Azure Spatial Anchors](../overview.md). Azure Spatial Anchors to usługa dla deweloperów programujących dla wielu platform, która pozwala kreować rozwiązania z rzeczywistością mieszaną z użyciem obiektów, których lokalizacja jest taka sama na różnych urządzeniach mimo upływu czasu. Gdy skończysz, będziesz mieć aplikację ARCore dla systemu Android utworzoną za pomocą aparatu Unity, która może zapisywać i przywoływać kotwicę przestrzenną.

Omawiane tematy:

> [!div class="checklist"]
> * Tworzenie konta usługi Spatial Anchors
> * Przygotowywanie ustawień kompilacji aparatu Unity
> * Konfigurowanie identyfikatora i klucza konta usługi Spatial Anchors
> * Eksportowanie projektu programu Android Studio
> * Wdrażanie i uruchamianie na urządzeniu z systemem Android

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start, upewnij się, że dysponujesz następującymi elementami:

- Komputer z systemem Windows lub macOS z <a href="https://unity3d.com/get-unity/download" target="_blank">unity 2019.1 lub 2019.2,</a> w tym android build support i Android SDK & moduły narzędzi NDK.
  - Jeśli działa w systemie Windows, będziesz również potrzebować <a href="https://git-scm.com/download/win" target="_blank">Git dla Windows</a> i <a href="https://git-lfs.github.com/">Git LFS</a>.
  - Jeśli działa na macOS, pobierz Git zainstalowany przez HomeBrew. Wprowadź następujące polecenie w jednym wierszu `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`terminala: . Następnie uruchom `brew install git` `brew install git-lfs`i .
- Urządzenie z systemem Android <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">pracujące w trybie dewelopera</a> i <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">zgodne z platformą ARCore</a>.
  - Aby komputer mógł komunikować się z urządzeniem z Androidem, mogą być wymagane dodatkowe sterowniki urządzeń. Zobacz [tutaj,](https://developer.android.com/studio/run/device.html) aby uzyskać dodatkowe informacje i instrukcje.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Pobieranie i otwieranie przykładowego projektu Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

[!INCLUDE [Android Unity Build Settings](../../../includes/spatial-anchors-unity-android-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>Konfigurowanie identyfikatora i klucza konta

W okienku **Project (Projekt)** przejdź do katalogu `Assets/AzureSpatialAnchors.Examples/Scenes` i otwórz plik sceny `AzureSpatialAnchorsBasicDemo.unity`.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Zapisz scenę, wybierając **pozycję Zapisz plik** -> **Save**.

## <a name="export-the-android-studio-project"></a>Eksportowanie projektu programu Android Studio

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Wybierz urządzenie w **uruchom urządzenie** i kliknij przycisk **Buduj i uruchamiaj**. Zostaniesz poproszony o zapisanie `.apk` pliku, który można wybrać dowolną nazwę.

Postępuj zgodnie z instrukcjami w aplikacji, aby umieścić i przywołać kotwicę.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="rendering-issues"></a>Problemy z renderowaniem

Jeśli podczas uruchamiania aplikacji nie widzisz aparatu jako tła (np. widzisz puste miejsce, niebieski kolor lub inne tekstury), prawdopodobnie musisz ponownie zaimportować zasoby w aparacie Unity. Zatrzymaj aplikację. Z górnego menu aparatu Unity wybierz pozycje **Assets -> Reimport all (Zasoby -> Zaimportuj ponownie wszystko)**. Następnie ponownie uruchom aplikację.

### <a name="unity-20193"></a>Jedność 2019.3

Ze względu na przełomowe zmiany, Unity 2019.3 nie jest obecnie obsługiwany. Prosimy o korzystanie z Unity 2019.1 lub 2019.2.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Samouczek: Udostępnianie zakotwiczeń przestrzennych na różnych urządzeniach](../tutorials/tutorial-share-anchors-across-devices.md)
