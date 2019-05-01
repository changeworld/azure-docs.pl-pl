---
title: Przewodnik Szybki Start — tworzenie aplikacji dla systemu Android Unity z Azure kotwic przestrzenne | Dokumentacja firmy Microsoft
description: Z tego przewodnika Szybki start dowiesz się, jak utworzyć aplikację dla systemu Android, używając aparatu Unity i usługi Spatial Anchors.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 001042a5499b492db151853172e999cf7a3c5f88
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64687806"
---
# <a name="quickstart-create-a-unity-android-app-with-azure-spatial-anchors"></a>Szybki start: Tworzenie aplikacji dla systemu Android Unity z Azure kotwic przestrzenne

Ten przewodnik Szybki Start opisano, jak utworzyć aplikację dla systemu Android Unity za pomocą [kotwic przestrzenne Azure](../overview.md). Azure Spatial Anchors to usługa dla deweloperów programujących dla wielu platform, która pozwala kreować rozwiązania z rzeczywistością mieszaną z użyciem obiektów, których lokalizacja jest taka sama na różnych urządzeniach mimo upływu czasu. Gdy skończysz, będziesz mieć aplikację ARCore dla systemu Android utworzoną za pomocą aparatu Unity, która może zapisywać i przywoływać kotwicę przestrzenną.

Omawiane tematy:

> [!div class="checklist"]
> * Tworzenie konta usługi Spatial Anchors
> * Przygotowywanie ustawień kompilacji aparatu Unity
> * Pobierz i zaimportuj zestaw ARCore SDK dla aparatu Unity
> * Konfigurowanie identyfikatora i klucza konta usługi Spatial Anchors
> * Eksportowanie projektu programu Android Studio
> * Wdrażanie i uruchamianie na urządzeniu z systemem Android

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start, upewnij się, że dysponujesz następującymi elementami:

- Windows lub macOS komputera za pomocą <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2018.3 +</a> i <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.3 +</a>.
  - Jeśli w systemie Windows, należy także <a href="https://git-scm.com/download/win" target="_blank">Git dla Windows</a>.
  - Jeśli uruchomiona w systemie macOS, skorzystaj z usługi Git, zainstalowanych za pomocą oprogramowania HomeBrew. Wprowadź następujące polecenie w jednym wierszu w terminalu: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Następnie uruchom `brew install git`.
- Urządzenie z systemem Android <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">pracujące w trybie dewelopera</a> i <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">zgodne z platformą ARCore</a>.
- Aplikację należy użyć wersji **1.7** ARCore zestawu SDK dla platformy Unity.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project-in-unity"></a>Otwieranie przykładowego projektu w aparacie Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Android Unity Build Settings](../../../includes/spatial-anchors-unity-android-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>Konfigurowanie identyfikatora i klucza konta

W okienku **Project (Projekt)** przejdź do katalogu `Assets/AzureSpatialAnchorsPlugin/Examples` i otwórz plik sceny `AzureSpatialAnchorsBasicDemo.unity`.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Zapisz scenę, wybierając pozycje **File (Plik)** -> **Save (Zapisz)**.

## <a name="export-the-android-studio-project"></a>Eksportowanie projektu programu Android Studio

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Upewnij się, że pole wyboru **Export Project (Eksportuj projekt)** nie jest zaznaczone. Kliknij pozycję **Build And Run (Skompiluj i uruchom)**. Zobaczysz pytanie o zapisanie pliku `.apk` — możesz wybrać dla niego dowolną nazwę.

Postępuj zgodnie z instrukcjami w aplikacji, aby umieścić i przywołać kotwicę.

> [!NOTE]
> Jeśli podczas uruchamiania aplikacji nie widzisz aparatu jako tła (np. widzisz puste miejsce, niebieski kolor lub inne tekstury), prawdopodobnie musisz ponownie zaimportować zasoby w aparacie Unity. Zatrzymaj aplikację. Z górnego menu aparatu Unity wybierz pozycje **Assets -> Reimport all (Zasoby -> Zaimportuj ponownie wszystko)**. Następnie ponownie uruchom aplikację.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Samouczek: Udostępnianie kotwic przestrzennych na różnych urządzeniach](../tutorials/tutorial-share-anchors-across-devices.md)
