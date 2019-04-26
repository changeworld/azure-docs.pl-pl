---
title: Szybki start — tworzenie aplikacji dla urządzenia HoloLens przy użyciu usługi Azure Spatial Anchors| Microsoft Docs
description: Z tego przewodnika Szybki start dowiesz się, jak utworzyć aplikację dla urządzenia HoloLens, używając usługi Spatial Anchors.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 7a9eedfe851bb8b5519d0831f47cf6787345a1f8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60233561"
---
# <a name="quickstart-create-a-hololens-app-with-azure-spatial-anchors-in-cwinrt-and-directx"></a>Szybki start: Tworzenie aplikacji dla urządzenia HoloLens za pomocą usługi Azure Spatial Anchors w języku C++/WinRT z użyciem programu DirectX

W tym przewodniku Szybki start opisano, jak utworzyć aplikację dla urządzenia HoloLens przy użyciu usługi [Azure Spatial Anchors](../overview.md) w języku C++/WinRT z użyciem programu DirectX. Azure Spatial Anchors to usługa dla deweloperów programujących dla wielu platform, która pozwala kreować rozwiązania z rzeczywistością mieszaną z użyciem obiektów, których lokalizacja jest taka sama na różnych urządzeniach mimo upływu czasu. Gdy skończysz, będziesz mieć aplikację dla urządzenia HoloLens, która może zapisywać i przywoływać kotwicę przestrzenną.

Omawiane tematy:

> [!div class="checklist"]
> * Tworzenie konta usługi Spatial Anchors
> * Konfigurowanie identyfikatora i klucza konta usługi Spatial Anchors
> * Wdrażanie i uruchamianie na urządzeniu HoloLens

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start, upewnij się, że dysponujesz następującymi elementami:

- Maszyna Windows z <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017 +</a> zainstalowane z **programowania na platformę uniwersalną Windows** obciążenia i **zestawu Windows 10 SDK (10.0.17763.0 lub nowszej)** składnik, a <a href="https://git-scm.com/download/win" target="_blank">Git dla Windows</a>.
- Powinno być zainstalowane rozszerzenie [C++/WinRT Visual Studio Extension (VSIX)](https://aka.ms/cppwinrt/vsix) dla programu Visual Studio z usługi [Visual Studio Marketplace](https://marketplace.visualstudio.com/).
- Urządzenie HoloLens z włączonym [trybem dewelopera](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio). Ten artykuł wymaga urządzenia HoloLens z [aktualizacją systemu Windows z 10 października 2018 r.](https://docs.microsoft.com/windows/mixed-reality/release-notes-october-2018 ) (znaną także jako RS5). Aby wykonać aktualizację do najnowszej wersji na urządzeniu HoloLens, otwórz aplikację **Ustawienia**, przejdź do pozycji **Aktualizacja i zabezpieczenia**, a następnie wybierz przycisk **Sprawdź dostępność aktualizacji**.
- Aplikacja musi ustawiać możliwość **spatialPerception** w manifeście AppX.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Otwieranie przykładowego projektu

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Otwórz plik `HoloLens\DirectX\SampleHoloLens.sln` w programie Visual Studio.

## <a name="configure-account-identifier-and-key"></a>Konfigurowanie identyfikatora i klucza konta

Następnym krokiem jest, aby skonfigurować aplikację do używania Twojego identyfikatora konta i klucz konta. Skopiowane do edytora tekstu po [Definiowanie zasobów przestrzenne kotwic](#create-a-spatial-anchors-resource).

Otwórz plik `HoloLens\DirectX\SampleHoloLens\ViewController.cpp`.

Znajdź pole `SpatialAnchorsAccountKey` i zastąp wartość `Set me` kluczem konta.

Znajdź pole `SpatialAnchorsAccountId` i zastąp wartość `Set me` identyfikatorem konta.

## <a name="deploy-the-app-to-your-hololens"></a>Wdrażanie aplikacji na urządzeniu HoloLens

Zmień **konfigurację rozwiązania** na **Wydanie**, zmień **platformę rozwiązania** na **x86** i wybierz **urządzenie** spośród opcji miejsc docelowych wdrażania.

Jeśli przy użyciu HoloLens 2, użyj **ARM** jako **platforma rozwiązania**, zamiast **x86**.

![Konfiguracja programu Visual Studio](./media/get-started-hololens/visual-studio-configuration.png)

Włącz urządzenie HoloLens, zaloguj się i połącz to urządzenie z komputerem PC za pomocą kabla USB.

Wybierz pozycje **Debugowanie** > **Rozpocznij debugowanie**, aby wdrożyć swoją aplikację i rozpocząć debugowanie.

Postępuj zgodnie z instrukcjami w aplikacji, aby umieścić i przywołać kotwicę.

W programie Visual Studio zatrzymaj aplikację, wybierając pozycję **Zatrzymaj debugowanie** lub naciskając klawisze **Shift + F5**.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Samouczek: Udostępnianie kotwic przestrzennych na różnych urządzeniach](../tutorials/tutorial-share-anchors-across-devices.md)
