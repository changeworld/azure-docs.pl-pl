---
title: Szybki start — tworzenie aplikacji aparatu Unity dla systemu Android przy użyciu usługi Azure Spatial Anchors | Microsoft Docs
description: Z tego przewodnika Szybki start dowiesz się, jak utworzyć aplikację dla systemu Android, używając aparatu Unity i usługi Spatial Anchors.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: e92c812ffc8b72fe79248c602e48ff01ef9fefcb
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2019
ms.locfileid: "56961018"
---
# <a name="quickstart-create-an-android-unity-app-with-azure-spatial-anchors"></a>Szybki start: Tworzenie aplikacji aparatu Unity dla systemu Android przy użyciu usługi Azure Spatial Anchors

W tym przewodniku Szybki start opisano, jak utworzyć aplikację aparatu Unity dla systemu Android przy użyciu usługi [Azure Spatial Anchors](../overview.md). Azure Spatial Anchors to usługa dla deweloperów programujących dla wielu platform, która pozwala kreować rozwiązania z rzeczywistością mieszaną z użyciem obiektów, których lokalizacja jest taka sama na różnych urządzeniach mimo upływu czasu. Gdy skończysz, będziesz mieć aplikację ARCore dla systemu Android utworzoną za pomocą aparatu Unity, która może zapisywać i przywoływać kotwicę przestrzenną.

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

- Maszyna z systemem Windows lub macOS i aparatem <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2018.3 lub nowszym</a> oraz programem <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.3 lub nowszym</a>.
- Urządzenie z systemem Android <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">pracujące w trybie dewelopera</a> i <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">zgodne z platformą ARCore</a>.

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

Wybierz pozycję **Export (Eksportuj)**, aby otworzyć okno dialogowe. Następnie wybierz folder, aby wyeksportować projekt programu Android Studio.

Po zakończeniu eksportu zostanie wyświetlony folder zawierający wyeksportowany projekt programu Android Studio z podfolderem o nazwie **HelloAR U3D**.

## <a name="deploy-the-android-application"></a>Wdrażanie aplikacji systemu Android

Otwórz program Android Studio i wybierz pozycję **Open an existing Android Studio project (Otwórz istniejący projekt Android Studio)**. Następnie wybierz podfolder **HelloAR U3D** z wyeksportowanego projektu programu Android Studio i kliknij przycisk **OK**.

Podczas otwierania zostanie wyświetlony monit z pytaniem o użycie otoki Gradle. Wybierz przycisk **OK**, aby użyć otoki Gradle i otworzyć projekt.

Włącz urządzenie z systemem Android, zaloguj się i połącz to urządzenie z komputerem PC za pomocą kabla USB.

Wybierz polecenie **Run (Uruchom)** na pasku narzędzi programu Android Studio.

![Wdrażanie i uruchamianie w programie Android Studio](./media/get-started-unity-android/android-studio-deploy-run.png)

Wybierz urządzenie z systemem Android w oknie dialogowym **Select Deployment Target (Wybierz miejsce docelowe wdrożenia)**, a następnie wybierz przycisk **OK**, aby uruchomić aplikację na urządzeniu z systemem Android.

Postępuj zgodnie z instrukcjami w aplikacji, aby umieścić i przywołać kotwicę.

> [!NOTE]
> Jeśli podczas uruchamiania aplikacji nie widzisz aparatu jako tła (np. widzisz puste miejsce, niebieski kolor lub inne tekstury), prawdopodobnie musisz ponownie zaimportować zasoby w aparacie Unity. Zatrzymaj aplikację. Z górnego menu aparatu Unity wybierz pozycje **Assets -> Reimport all (Zasoby -> Zaimportuj ponownie wszystko)**. Następnie ponownie uruchom aplikację.

Zatrzymaj aplikację, wybierając polecenie **Stop (Zatrzymaj)** na pasku narzędzi programu Android Studio.

![Zatrzymywanie w programie Android Studio](./media/get-started-unity-android/android-studio-stop.png)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Samouczek: Udostępnianie kotwic przestrzennych na różnych urządzeniach](../tutorials/tutorial-share-anchors-across-devices.md)
