---
title: Samouczek — udostępnianie między sesjami i urządzeniami za pomocą usługi Azure Spatial Anchors | Microsoft Docs
description: W tym samouczku dowiesz się, jak udostępniać identyfikatory Azure przestrzenne zakotwiczenia między Android/urządzenia z systemem iOS na platformie Unity usługi zaplecza.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 7d9fe58b7db60513eed81aae628ebd7ca754a53a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/18/2019
ms.locfileid: "57901308"
---
# <a name="tutorial-sharing-across-sessions-and-devices-with-azure-spatial-anchors"></a>Samouczek: Udostępnianie między sesjami i urządzeniami za pomocą usługi Azure Spatial Anchors

Z tego samouczka dowiesz się, jak używać usługi [Azure Spatial Anchors](../overview.md) do wykonywania następujących czynności:

1. Tworzenie kotwic w jednej sesji, a następnie znajdowanie ich w innej sesji na tym samym lub innym urządzeniu. Na przykład innego dnia.
2. Tworzenie kotwic, które mogą być znajdowane przez wiele urządzeń w tym samym miejscu i czasie.

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

## <a name="open-the-sample-project-in-unity"></a>Otwieranie przykładowego projektu w aparacie Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

## <a name="deploy-your-sharing-anchors-service"></a>Wdrażanie usługi do udostępniania kotwic

Otwórz program Visual Studio i otwórz projekt w folderze `Sharing\SharingServiceSample`.

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

## <a name="open-the-sample-project-in-unity"></a>Otwieranie przykładowego projektu w aparacie Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku została wdrożona aplikacja internetowa platformy ASP.NET Core na platformie Azure, a następnie została skonfigurowana i wdrożona aplikacja aparatu Unity. W aplikacji zostały utworzone kotwice przestrzenne i udostępniono je innym urządzeniom za pomocą aplikacji internetowej platformy ASP.NET Core.

Aby dowiedzieć się więcej o sposobach poprawiania aplikacji internetowej ASP.NET Core, korzystającą z usługi Azure Cosmos DB do przechowywania Twojego udostępnionego kotwic przestrzennych, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Samouczek: Użyj usługi Azure Cosmos DB do kotwic Store](./tutorial-use-cosmos-db-to-store-anchors.md)
