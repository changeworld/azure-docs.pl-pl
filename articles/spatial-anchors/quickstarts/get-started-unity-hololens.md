---
title: Szybki start — tworzenie aplikacji aparatu Unity dla urządzenia HoloLens przy użyciu usługi Azure Spatial Anchors| Microsoft Docs
description: Z tego przewodnika Szybki start dowiesz się, jak utworzyć aplikację dla urządzenia HoloLens, używając aparatu Unity i usługi Spatial Anchors.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: c8fc99426751e9aaaec53cbb6b64a84b6e7f6734
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2019
ms.locfileid: "57894329"
---
# <a name="quickstart-create-a-hololens-unity-app-using-azure-spatial-anchors"></a>Szybki start: Tworzenie aplikacji aparatu Unity dla urządzenia HoloLens przy użyciu usługi Azure Spatial Anchors

W tym przewodniku Szybki start opisano, jak utworzyć aplikację aparatu Unity dla urządzenia HoloLens przy użyciu usługi [Azure Spatial Anchors](../overview.md). Azure Spatial Anchors to usługa dla deweloperów programujących dla wielu platform, która pozwala kreować rozwiązania z rzeczywistością mieszaną z użyciem obiektów, których lokalizacja jest taka sama na różnych urządzeniach mimo upływu czasu. Gdy skończysz, będziesz mieć aplikację dla urządzenia HoloLens utworzoną za pomocą aparatu Unity, która może zapisywać i przywoływać kotwicę przestrzenną.

Omawiane tematy:

> [!div class="checklist"]
> * Tworzenie konta usługi Spatial Anchors
> * Przygotowywanie ustawień kompilacji aparatu Unity
> * Konfigurowanie identyfikatora i klucza konta usługi Spatial Anchors
> * Eksportowanie projektu programu Visual Studio dla urządzenia HoloLens
> * Wdrażanie i uruchamianie na urządzeniu HoloLens

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start, upewnij się, że dysponujesz następującymi elementami:

- Maszyna Windows z <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2018.3 +</a>, <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017 +</a> zainstalowane z **programowania na platformę uniwersalną Windows** obciążenia, i <a href="https://git-scm.com/download/win" target="_blank">narzędzia Git Windows</a>.
- Urządzenie HoloLens z włączonym [trybem dewelopera](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio). Ten artykuł wymaga urządzenia HoloLens z [aktualizacją systemu Windows z 10 października 2018 r.](https://docs.microsoft.com/en-us/windows/mixed-reality/release-notes-october-2018) (znaną także jako RS5). Aby wykonać aktualizację do najnowszej wersji na urządzeniu HoloLens, otwórz aplikację **Ustawienia**, przejdź do pozycji **Aktualizacja i zabezpieczenia**, a następnie wybierz przycisk **Sprawdź dostępność aktualizacji**.
- Aplikacja musi ustawiać możliwość **SpatialPerception** w obszarze **Build Settings (Ustawienia kompilacji)**->**Player Settings (Ustawienia odtwarzacza)**->**Publishing Settings (Ustawienia publikowania)**->**Capabilities (Możliwości)**.
- Aplikacja musi włączać ustawienie **Virtual Reality Supported (Obsługa rzeczywistości wirtualnej)** z zestawem **Windows Mixed Reality SDK** w obszarze **Build Settings (Ustawienia kompilacji)**->**Player Settings (Ustawienia odtwarzacza)**->**XR Settings (Ustawienia XR)**.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project-in-unity"></a>Otwieranie przykładowego projektu w aparacie Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Otwórz aparat Unity i otwórz projekt w folderze `Unity`.

Otwórz obszar **Build Settings (Ustawienia kompilacji)**, wybierając pozycje **File (Plik)** -> **Build Settings (Ustawienia kompilacji)**.

W sekcji **Platform (Platforma)** wybierz pozycję **Universal Windows Platform (Platforma uniwersalna systemu Windows)**. Następnie zmień wartość ustawienia **Target Device (Urządzenie docelowe)** na **HoloLens**.

Wybierz pozycję **Switch Platform (Przełącz platformę)**, aby zmienić platformę na **Universal Windows Platform (Platforma uniwersalna systemu Windows)**. Unity może poprosić Zainstaluj składniki obsługi platformy uniwersalnej systemu Windows, jeśli są one Brak.

![Ustawienia kompilacji aparatu Unity](./media/get-started-unity-hololens/unity-build-settings.png)

Zamknij okno **Build Settings (Ustawienia kompilacji)**.

## <a name="configure-account-identifier-and-key"></a>Konfigurowanie identyfikatora i klucza konta

W okienku **Project (Projekt)** przejdź do katalogu `Assets/AzureSpatialAnchorsPlugin/Examples` i otwórz plik sceny `AzureSpatialAnchorsBasicDemo.unity`.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Zapisz scenę, wybierając pozycje **File (Plik)** -> **Save (Zapisz)**.

## <a name="export-the-hololens-visual-studio-project"></a>Eksportowanie projektu programu Visual Studio dla urządzenia HoloLens

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Wybierz pozycję **Build (Kompilacja)**, aby otworzyć okno dialogowe. Następnie wybierz folder, aby wyeksportować projekt programu Visual Studio dla urządzenia HoloLens.

Po zakończeniu eksportowania zostanie wyświetlony folder zawierający wyeksportowany projekt dla urządzenia HoloLens.

## <a name="deploy-the-hololens-application"></a>Wdrażanie aplikacji dla urządzenia HoloLens

W folderze kliknij dwukrotnie plik `HelloAR U3D.sln`, aby otworzyć projekt w programie Visual Studio.

Zmień **konfigurację rozwiązania** na **Wydanie**, zmień **platformę rozwiązania** na **x86** i wybierz **urządzenie** spośród opcji miejsc docelowych wdrażania.

![Konfiguracja programu Visual Studio](./media/get-started-unity-hololens/visual-studio-configuration.png)

Włącz urządzenie HoloLens, zaloguj się i połącz to urządzenie z komputerem PC za pomocą kabla USB.

Wybierz pozycje **Debugowanie** > **Rozpocznij debugowanie**, aby wdrożyć swoją aplikację i rozpocząć debugowanie.

Postępuj zgodnie z instrukcjami w aplikacji, aby umieścić i przywołać kotwicę.

W programie Visual Studio zatrzymaj aplikację, wybierając pozycję **Zatrzymaj debugowanie** lub naciskając klawisze **Shift + F5**.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Samouczek: Udostępnianie kotwic przestrzennych na różnych urządzeniach](../tutorials/tutorial-share-anchors-across-devices.md)
