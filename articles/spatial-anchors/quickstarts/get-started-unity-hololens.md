---
title: Przewodnik Szybki Start — tworzenie aplikacji HoloLens Unity z Azure kotwic przestrzenne | Dokumentacja firmy Microsoft
description: Z tego przewodnika Szybki start dowiesz się, jak utworzyć aplikację dla urządzenia HoloLens, używając aparatu Unity i usługi Spatial Anchors.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 56b164d9612fd1bef8caf9ae0de679f65595bc39
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58629477"
---
# <a name="quickstart-create-a-hololens-unity-app-that-uses-azure-spatial-anchors"></a>Szybki start: Tworzenie aplikacji HoloLens Unity, która używa kotwic przestrzenne platformy Azure

W tym przewodniku Szybki Start utworzysz aplikację HoloLens Unity, który używa [kotwic przestrzenne Azure](../overview.md). Kotwice przestrzennego jest usługa programistycznych dla wielu platform, która umożliwia tworzenie środowisk rzeczywistość mieszana z obiektami, które ulegają zmianie lokalizacji urządzenia wraz z upływem czasu. Gdy skończysz, będziesz mieć aplikację dla urządzenia HoloLens utworzoną za pomocą aparatu Unity, która może zapisywać i przywoływać kotwicę przestrzenną.

Omawiane tematy:

- Utwórz konto przestrzenne zakotwiczenia.
- Przygotowywanie ustawień kompilacji platformy Unity.
- Konfigurowanie zakotwiczenia przestrzenne konta identyfikator i klucz konta.
- Eksportowanie projektu programu HoloLens Visual Studio.
- Wdróż aplikację i uruchom go na urządzeniu HoloLens.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start:


- Potrzebny jest komputer Windows, na którym <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2018.3</a> lub nowszej i <a href="https://www.visualstudio.com/downloads/" target="_blank">programu Visual Studio 2017</a> lub nowszy. Instalację programu Visual Studio musi zawierać **programowania na platformę uniwersalną Windows** obciążenia. Należy również zainstalować <a href="https://git-scm.com/download/win" target="_blank">Git dla Windows</a>.
- Potrzebujesz urządzenia HoloLens, na którym [tryb dewelopera](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio) włączone. [Windows 10 października 2018 Update](https://docs.microsoft.com/windows/mixed-reality/release-notes-october-2018) (znany także jako RS5) musi być zainstalowany na urządzeniu. Aby uaktualnić do najnowszej wersji na HoloLens, otwórz **ustawienia** aplikację, przejdź do **aktualizacja i zabezpieczenia**, a następnie wybierz pozycję **sprawdzać dostępność aktualizacji**.
- W aplikacji, musisz włączyć **SpatialPerception** możliwości. To ustawienie znajduje się w **ustawieniach kompilacji** > **ustawienia odtwarzacza** > **ustawień publikowania**  >   **Możliwości**.
- W aplikacji, musisz włączyć **obsługiwane rzeczywistości wirtualnej** z **zestawu SDK systemu Windows mieszane mogą stać się rzeczywistością**. To ustawienie znajduje się w **ustawieniach kompilacji** > **ustawienia odtwarzacza** > **ustawienia XR**.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project-in-unity"></a>Otwieranie przykładowego projektu w aparacie Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Na platformie Unity Otwórz projekt w folderze aparatu Unity.

Otwórz obszar **Build Settings (Ustawienia kompilacji)**, wybierając pozycje **File (Plik)** > **Build Settings (Ustawienia kompilacji)**.

W sekcji **Platform (Platforma)** wybierz pozycję **Universal Windows Platform (Platforma uniwersalna systemu Windows)**. Zmiana **urządzenie docelowe** do **HoloLens**.

Wybierz pozycję **Switch Platform (Przełącz platformę)**, aby zmienić platformę na **Universal Windows Platform (Platforma uniwersalna systemu Windows)**. Unity może spowodować wyświetlenie monitu do zainstalowania składniki obsługi platformy uniwersalnej systemu Windows, jeśli są one Brak.

   ![Okno ustawień kompilacji platformy Unity](./media/get-started-unity-hololens/unity-build-settings.png)

Zamknij okno **Build Settings (Ustawienia kompilacji)**.

## <a name="configure-the-account-identifier-and-key"></a>Skonfiguruj identyfikator konta i klucz

W **projektu** okienko, przejdź do `Assets/AzureSpatialAnchorsPlugin/Examples` , a następnie otwórz `AzureSpatialAnchorsBasicDemo.unity` pliku sceny.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Zapisz scenę, wybierając pozycje **File (Plik)** > **Save (Zapisz)**.

## <a name="export-the-hololens-visual-studio-project"></a>Eksportowanie projektu programu Visual Studio dla urządzenia HoloLens

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Wybierz **kompilacji**. W oknie dialogowym Wybierz folder, w której mają zostać wyeksportowane projektu programu HoloLens Visual Studio.

Po zakończeniu eksportu, pojawi się folder zawierający wyeksportowany projektu HoloLens.

## <a name="deploy-the-hololens-application"></a>Wdrażanie aplikacji dla urządzenia HoloLens

W tym folderze kliknij dwukrotnie **HelloAR U3D.sln** otworzyć projektu w programie Visual Studio.

Zmień **konfiguracji rozwiązania** do **wersji**, zmień **platforma rozwiązania** do **x86**i wybierz **urządzenia**  z target — opcje wdrażania.

   ![Konfiguracja serwera Visual Studio](./media/get-started-unity-hololens/visual-studio-configuration.png)

Włącz urządzenie HoloLens, zaloguj się i połączyć to urządzenie do komputera za pomocą kabla USB.

Wybierz pozycje **Debugowanie** > **Rozpocznij debugowanie**, aby wdrożyć swoją aplikację i rozpocząć debugowanie.

Postępuj zgodnie z instrukcjami w aplikacji, aby umieścić i przywołać kotwicę.

W programie Visual Studio, Zatrzymaj aplikację, wybierając opcję **Zatrzymaj debugowanie** lub Shift + F5.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Samouczek: Udostępniaj przestrzenne zakotwiczenia między urządzeniami](../tutorials/tutorial-share-anchors-across-devices.md)
