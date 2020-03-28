---
title: 'Samouczek: Udostępnianie zakotwiczenia w sesjach i urządzeniach'
description: W tym samouczku dowiesz się, jak udostępniać identyfikatory kotwicy przestrzennej platformy Azure między urządzeniami z systemem Android/iOS w unity za pomocą usługi zaplecza.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 3b377f87bdba40c90cb3af6caef2c089d7b7de49
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77615496"
---
# <a name="tutorial-share-azure-spatial-anchors-across-sessions-and-devices"></a>Samouczek: Udostępnianie zakotwiczenia przestrzennego platformy Azure w sesjach i na urządzeniach

W tym samouczku dowiesz się, jak używać [zakotwiczenia przestrzennego platformy Azure](../overview.md) do tworzenia zakotwiczeń podczas jednej sesji, a następnie lokalizowania ich na tym samym urządzeniu lub na innym urządzeniu. Te same kotwice mogą być również zlokalizowane przez wiele urządzeń w tym samym miejscu i w tym samym czasie.

![Trwałość](./media/persistence.gif)

Azure Spatial Anchors to usługa dla deweloperów programujących dla wielu platform, która pozwala kreować rozwiązania z rzeczywistością mieszaną z użyciem obiektów, których lokalizacja jest taka sama na różnych urządzeniach mimo upływu czasu. Gdy skończysz, będziesz mieć aplikację, którą można wdrożyć na dwóch urządzeniach lub więcej. Kotwice usługi Azure Spatial Anchors utworzone przez jedno wystąpienie mogą być udostępniane innym wystąpieniom.

Omawiane tematy:

> [!div class="checklist"]
> * Wdrażanie na platformie Azure aplikacji internetowej platformy ASP.NET Core, która może służyć do udostępniania kotwic i przechowywania ich w pamięci przez pewien czas.
> * Konfigurowanie sceny AzureSpatialAnchorsLocalSharedDemo w ramach przykładu aparatu Unity z naszych przewodników Szybki start, aby skorzystać z aplikacji internetowej do udostępniania kotwic.
> * Wdrażanie i uruchamianie na co najmniej jednym urządzeniu.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

Warto zauważyć, że chociaż w tym samouczku będzie używany aparat Unity i aplikacja internetowa platformy ASP.NET Core, ma to na celu jedynie zaprezentowanie przykładu udostępniania identyfikatorów kotwic usługi Azure Spatial Anchors innym urządzeniom. Aby osiągnąć ten sam cel, można użyć innych języków i technologii zaplecza. Ponadto aplikacja internetowa platformy ASP.NET Core używana w tym samouczku zależy od zestawu .NET Core 2.2 SDK. Działa poprawnie w normalnej usłudze Azure Web Apps (dla systemu Windows), ale obecnie nie będzie działać w usłudze Azure Web Apps dla systemu Linux.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-the-sample-project"></a>Pobierz przykładowy projekt

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

## <a name="deploy-your-sharing-anchors-service"></a>Wdrażanie usługi do udostępniania kotwic

## <a name="visual-studio"></a>[Visual Studio](#tab/VS)

Otwórz program Visual Studio i otwórz projekt w folderze `Sharing\SharingServiceSample`.

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

## <a name="visual-studio-code"></a>[Kod programu Visual Studio](#tab/VSC)

Przed wdrożeniem usługi w programie VS Code należy utworzyć grupę zasobów i plan usługi aplikacji.

### <a name="sign-in-to-azure"></a>Logowanie się do platformy Azure

Przejdź do <a href="https://portal.azure.com/" target="_blank">witryny Azure portal</a> i zaloguj się do subskrypcji platformy Azure.

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

[!INCLUDE [resource group intro text](../../../includes/resource-group.md)]

Obok pozycji **Grupa zasobów** wybierz pozycję **Nowa**.

Nadaj grupie zasobów nazwę **myResourceGroup**, a następnie kliknij przycisk **OK**.

### <a name="create-an-app-service-plan"></a>Tworzenie planu usługi App Service

[!INCLUDE [app-service-plan](../../../includes/app-service-plan.md)]

Obok pozycji **Plan hostingu** wybierz pozycję **Nowy**.

W oknie dialogowym **Konfigurowanie planu hostingu** użyj następujących ustawień:

| Ustawienie | Sugerowana wartość | Opis |
|-|-|-|
|Plan usługi App Service| MySharingServicePlan | Nazwa planu usługi App Service. |
| Lokalizacja | Zachodnie stany USA | Centrum danych, w którym hostowana jest aplikacja internetowa. |
| Rozmiar | Bezpłatna | [Warstwa cenowa](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) określająca funkcje hostingu. |

Kliknij przycisk **OK**.

Otwórz program Visual Studio Code i `Sharing\SharingServiceSample` otwórz projekt w folderze. Wykonaj <a href="https://docs.microsoft.com/aspnet/core/tutorials/publish-to-azure-webapp-using-vscode?view=aspnetcore-2.2#open-it-with-visual-studio-code" target="_blank">ten samouczek,</a> aby wdrożyć usługę udostępniania za pośrednictwem programu Visual Studio Code. Możesz wykonać kroki, począwszy od sekcji "Otwórz go za pomocą kodu programu Visual Studio". Nie należy tworzyć innego projektu mvc, jak wyjaśniono w kroku powyżej, jak już projekt, który musi zostać wdrożony i opublikowany- SharingServiceSample.

---

## <a name="deploy-the-sample-app"></a>Wdrażanie przykładowej aplikacji

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="unity-20193"></a>Jedność 2019.3

Ze względu na przełomowe zmiany, Unity 2019.3 nie jest obecnie obsługiwany. Prosimy o korzystanie z Unity 2019.1 lub 2019.2.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku została wdrożona aplikacja internetowa platformy ASP.NET Core na platformie Azure, a następnie została skonfigurowana i wdrożona aplikacja aparatu Unity. W aplikacji zostały utworzone kotwice przestrzenne i udostępniono je innym urządzeniom za pomocą aplikacji internetowej platformy ASP.NET Core.

Można ulepszyć ASP.NET Core Web App, tak aby używała usługi Azure Cosmos DB do utrwalania magazynu udostępnionych identyfikatorów zakotwiczenia przestrzennego. Dodanie usługi Azure Cosmos DB pomocy technicznej pozwoli aplikacji ASP.NET Core Web App utworzyć kotwicę już dziś i wrócić kilka dni później, aby móc zlokalizować ją ponownie, przy użyciu identyfikatora kotwicy przechowywanego w aplikacji sieci web.

> [!div class="nextstepaction"]
> [Przechowywanie zakotwiczeń za pomocą usługi Azure Cosmo DB](./tutorial-use-cosmos-db-to-store-anchors.md)

