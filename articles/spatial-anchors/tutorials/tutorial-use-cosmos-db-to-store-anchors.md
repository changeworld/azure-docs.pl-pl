---
title: 'Samouczek: Udostępnianie między sesjami i urządzeniami za pomocą usługi Azure Spatial Anchors i zaplecza usługi Azure Cosmos DB | Microsoft Docs'
description: Z tego samouczka dowiesz się, jak udostępniać identyfikatory usługi Azure Spatial Anchors między urządzeniami w aparacie Unity za pomocą usługi zaplecza i usługi Azure Cosmos DB.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: f0cd42fc37727099ed95a1c6fc2d427b7862412e
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/24/2019
ms.locfileid: "56753492"
---
# <a name="tutorial-sharing-across-sessions-and-devices-with-azure-spatial-anchors-and-an-azure-cosmos-db-back-end"></a>Samouczek: Udostępnianie między sesjami i urządzeniami za pomocą usługi Azure Spatial Anchors i zaplecza usługi Azure Cosmos DB

Z tego samouczka dowiesz się, jak używać usługi [Azure Spatial Anchors](../overview.md) do wykonywania następujących czynności:

1. Tworzenie kotwic w jednej sesji, a następnie znajdowanie ich w innej sesji na tym samym lub innym urządzeniu. Na przykład innego dnia.
2. Tworzenie kotwic, które mogą być znajdowane przez wiele urządzeń w tym samym miejscu i czasie.

![Trwałość](./media/persistence.gif)

[Azure Spatial Anchors](../overview.md) to usługa dla deweloperów programujących dla wielu platform, która pozwala kreować rozwiązania z rzeczywistością mieszaną z użyciem obiektów, których lokalizacja jest taka sama na różnych urządzeniach mimo upływu czasu. Gdy skończysz, będziesz mieć aplikację, którą można wdrożyć na dwóch urządzeniach lub więcej. Kotwice usługi Azure Spatial Anchors utworzone przez jedno wystąpienie będą udostępniać swoje identyfikatory innym wystąpieniom za pomocą usługi Cosmos DB.

Omawiane tematy:

> [!div class="checklist"]
> * Wdrażanie na platformie Azure aplikacji internetowej platformy ASP.NET Core, która może służyć do udostępniania kotwic i przechowywania ich w bazie danych Cosmos DB.
> * Konfigurowanie sceny AzureSpatialAnchorsLocalSharedDemo w ramach przykładu aparatu Unity z naszych przewodników Szybki start, aby skorzystać z aplikacji internetowej do udostępniania kotwic.
> * Wdrażanie i uruchamianie na co najmniej jednym urządzeniu.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

Warto zauważyć, że chociaż w tym samouczku będzie używany aparat Unity i usługa Azure Cosmos DB, ma to na celu jedynie zaprezentowanie przykładu udostępniania identyfikatorów kotwic usługi Azure Spatial Anchors innym urządzeniom. Do osiągniecia tego celu można użyć innych języków i technologii zaplecza. Ponadto aplikacja internetowa platformy ASP.NET Core używana w tym samouczku zależy od zestawu .NET Core 2.2 SDK. Działa poprawnie w normalnej usłudze Azure Web Apps (dla systemu Windows), ale obecnie nie będzie działać w usłudze Azure Web Apps dla systemu Linux.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="create-a-database-account"></a>Tworzenie konta bazy danych

[!INCLUDE [cosmos-db-create-dbaccount-table](../../../includes/cosmos-db-create-dbaccount-table.md)]

Zanotuj parametr `Connection String`, ponieważ będzie on używany później.

## <a name="deploy-your-sharing-anchors-service"></a>Wdrażanie usługi do udostępniania kotwic

Otwórz program Visual Studio i otwórz projekt w folderze `Sharing\SharingServiceSample`.

### <a name="configure-the-service-so-that-it-uses-your-cosmos-db"></a>Konfigurowanie usługi pod kątem używania bazy danych Cosmos DB

W **Eksploratorze rozwiązań** otwórz plik `SharingService\Startup.cs`.

Znajdź wiersz `#define INMEMORY_DEMO` w górnej części pliku i oznacz go jako komentarz. Zapisz plik.

W **Eksploratorze rozwiązań** otwórz plik `SharingService\appsettings.json`.

Znajdź właściwość `StorageConnectionString`, a następnie ustaw wartość na wartość parametru `Connection String` zanotowaną podczas [tworzenia konta bazy danych](#create-a-database-account). Zapisz plik.

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku użyto usługi Azure Cosmos DB do udostępnienia identyfikatorów kotwic między urządzeniami. Aby dowiedzieć się więcej o bibliotece usługi Azure Spatial Anchors, kontynuuj pracę z naszym przewodnikiem w sekcji dotyczącej sposobu tworzenia i znajdowania kotwic.

> [!div class="nextstepaction"]
> [Tworzenie i znajdowanie kotwic przy użyciu usługi Azure Spatial Anchors](../create-locate-anchors-overview.md)
