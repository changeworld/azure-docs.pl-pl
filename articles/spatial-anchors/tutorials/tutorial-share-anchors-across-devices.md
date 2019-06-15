---
title: Samouczek — Azure udziału przestrzenne zakotwiczenia między sesjami i urządzeniami | Dokumentacja firmy Microsoft
description: W tym samouczku dowiesz się, jak udostępniać identyfikatory Azure przestrzenne zakotwiczenia między Android/urządzenia z systemem iOS na platformie Unity usługi zaplecza.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 734e1d08413867a438270660fa97bb8c5737e087
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "67135384"
---
# <a name="tutorial-share-azure-spatial-anchors-across-sessions-and-devices"></a>Samouczek: Udostępnianie kotwic przestrzenne Azure między sesjami i urządzeniami

W tym samouczku dowiesz się, jak używać [kotwic przestrzenne Azure](../overview.md) tworzyć kotwic podczas jednej sesji i Znajdź je na tym samym urządzeniu lub na inną. Również znajdować się te sam kotwic przez wiele urządzeń, w tym samym miejscu, a w tym samym czasie.

![Trwałość](./media/persistence.gif)

Azure Spatial Anchors to usługa dla deweloperów programujących dla wielu platform, która pozwala kreować rozwiązania z rzeczywistością mieszaną z użyciem obiektów, których lokalizacja jest taka sama na różnych urządzeniach mimo upływu czasu. Gdy skończysz, będziesz mieć aplikację, którą można wdrożyć na dwóch urządzeniach lub więcej. Kotwice usługi Azure Spatial Anchors utworzone przez jedno wystąpienie mogą być udostępniane innym wystąpieniom.

Omawiane tematy:

> [!div class="checklist"]
> * Wdrażanie na platformie Azure aplikacji internetowej platformy ASP.NET Core, która może służyć do udostępniania kotwic i przechowywania ich w pamięci przez pewien czas.
> * Konfigurowanie sceny AzureSpatialAnchorsLocalSharedDemo w ramach przykładu aparatu Unity z naszych przewodników Szybki start, aby skorzystać z aplikacji internetowej do udostępniania kotwic.
> * Wdrażanie i uruchamianie na co najmniej jednym urządzeniu.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

Warto zauważyć, że chociaż w tym samouczku będzie używany aparat Unity i aplikacja internetowa platformy ASP.NET Core, ma to na celu jedynie zaprezentowanie przykładu udostępniania identyfikatorów kotwic usługi Azure Spatial Anchors innym urządzeniom. Aby osiągnąć ten sam, można użyć innych języków i technologii zaplecza. Ponadto aplikacja internetowa platformy ASP.NET Core używana w tym samouczku zależy od zestawu .NET Core 2.2 SDK. Działa poprawnie w normalnej usłudze Azure Web Apps (dla systemu Windows), ale obecnie nie będzie działać w usłudze Azure Web Apps dla systemu Linux.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-the-unity-sample-project"></a>Pobierz przykładowy projekt aparatu Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

## <a name="deploy-your-sharing-anchors-service"></a>Wdrażanie usługi do udostępniania kotwic

Otwórz program Visual Studio i otwórz projekt w folderze `Sharing\SharingServiceSample`.

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku została wdrożona aplikacja internetowa platformy ASP.NET Core na platformie Azure, a następnie została skonfigurowana i wdrożona aplikacja aparatu Unity. W aplikacji zostały utworzone kotwice przestrzenne i udostępniono je innym urządzeniom za pomocą aplikacji internetowej platformy ASP.NET Core.

Aby dowiedzieć się więcej o sposobach poprawiania aplikacji internetowej ASP.NET Core, korzystającą z usługi Azure Cosmos DB do przechowywania Twojego udostępnionego identyfikatory zakotwiczenia przestrzennych, przejdź do następnego samouczka. Usługa Azure Cosmos DB umożliwi trwałości aplikacji internetowej ASP.NET Core. Spowoduje to więc umożliwi aplikacji do tworzenia elementu zakotwiczenia już dziś i wróć dni później, aby można go odnaleźć, korzystając z identyfikatorem kotwicy przechowywanych w aplikacji sieci web.

> [!div class="nextstepaction"]
> [Samouczek: Użyj usługi Azure Cosmos DB do kotwic Store](./tutorial-use-cosmos-db-to-store-anchors.md)
