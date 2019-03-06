---
title: Samouczek — udostępnianie między sesjami i urządzeniami za pomocą usługi Azure Spatial Anchors | Microsoft Docs
description: Z tego samouczka dowiesz się, jak udostępniać identyfikatory usługi Azure Spatial Anchors między urządzeniami w aparacie Unity za pomocą usługi zaplecza.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: df7f8aa5b49e3fe17be3b17a6e0f5d8861b26253
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/24/2019
ms.locfileid: "56753509"
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

Warto zauważyć, że chociaż w tym samouczku będzie używany aparat Unity i aplikacja internetowa platformy ASP.NET Core, ma to na celu jedynie zaprezentowanie przykładu udostępniania identyfikatorów kotwic usługi Azure Spatial Anchors innym urządzeniom. Do osiągniecia tego celu można użyć innych języków i technologii zaplecza. Ponadto aplikacja internetowa platformy ASP.NET Core używana w tym samouczku zależy od zestawu .NET Core 2.2 SDK. Działa poprawnie w normalnej usłudze Azure Web Apps (dla systemu Windows), ale obecnie nie będzie działać w usłudze Azure Web Apps dla systemu Linux.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="deploy-your-sharing-anchors-service"></a>Wdrażanie usługi do udostępniania kotwic

Otwórz program Visual Studio i otwórz projekt w folderze `Sharing\SharingServiceSample`.

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku została wdrożona aplikacja internetowa platformy ASP.NET Core na platformie Azure, a następnie została skonfigurowana i wdrożona aplikacja aparatu Unity. W aplikacji zostały utworzone kotwice przestrzenne i udostępniono je innym urządzeniom za pomocą aplikacji internetowej platformy ASP.NET Core. Aby dowiedzieć się, jak poprawić aplikację internetową platformy ASP.NET Core tak, aby przechowywała udostępnione kotwice przestrzenne, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Samouczek: Używanie usługi Cosmos DB do przechowywania kotwic](./tutorial-use-cosmos-db-to-store-anchors.md)
