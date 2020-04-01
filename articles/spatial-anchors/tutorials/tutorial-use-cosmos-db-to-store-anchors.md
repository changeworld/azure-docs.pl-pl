---
title: 'Samouczek: Udostępnianie kotwic za pomocą usługi Azure Cosmos DB'
description: W tym samouczku dowiesz się, jak udostępniać identyfikatory zakotwiczenia przestrzenne platformy Azure na urządzeniach z systemem Android/iOS w unity z usługą zaplecza i usługą Azure Cosmos DB.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 71b3027d86400d6921895f86e257ddff2961f91f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77615151"
---
# <a name="tutorial-sharing-azure-spatial-anchors-across-sessions-and-devices-with-an-azure-cosmos-db-back-end"></a>Samouczek: Udostępnianie zakotwiczenia przestrzennego platformy Azure w sesjach i urządzeniach za pomocą zaplecza usługi Azure Cosmos DB

Ten samouczek jest kontynuacją [udostępniania zakotwiczenia przestrzennego platformy Azure w sesjach i na urządzeniach.](../../../articles/spatial-anchors/tutorials/tutorial-share-anchors-across-devices.md) Przeprowadzi Cię przez proces dodawania kilku innych funkcji, aby usługa Azure Cosmos DB służyła jako magazyn zaplecza podczas udostępniania zakotwiczenia przestrzennego platformy Azure w sesjach i na urządzeniach.

![GIF ilustrujący trwałość obiektu](./media/persistence.gif)

Warto zauważyć, że chociaż będziesz używać Unity i Usługi Azure Cosmos DB w tym samouczku, to tylko podać przykład sposobu udostępniania identyfikatorów kotwic przestrzennych na różnych urządzeniach. Do osiągniecia tego celu można użyć innych języków i technologii zaplecza. Ponadto ASP.NET Core aplikacji sieci web używane w tym samouczku wymaga .NET Core 2.2 SDK. Działa dobrze w aplikacjach sieci Web dla systemu Windows, ale nie będzie obecnie działać w aplikacjach sieci Web dla systemu Linux.

## <a name="create-a-database-account"></a>Tworzenie konta bazy danych

Dodaj bazę danych usługi Azure Cosmos database do grupy zasobów utworzonej wcześniej.

[!INCLUDE [cosmos-db-create-dbaccount-table](../../../includes/cosmos-db-create-dbaccount-table.md)]

Skopiuj, `Connection String` ponieważ będziesz go potrzebować.

## <a name="make-minor-changes-to-the-sharingservice-files"></a>Wprowadzanie drobnych zmian w plikach usługi SharingService

W **Eksploratorze rozwiązań**otwórz `SharingService\Startup.cs`.

Znajdź `#define INMEMORY_DEMO` w górnej części pliku i skomentuj ten wiersz. Zapisz plik.

W **Eksploratorze rozwiązań**otwórz `SharingService\appsettings.json`.

Zlokalizuj `StorageConnectionString` właściwość i ustaw wartość `Connection String` tak samo jak wartość skopiowaną w [kroku tworzenia konta bazy danych](#create-a-database-account). Zapisz plik.

Możesz ponownie opublikować usługę udostępniania i uruchomić przykładową aplikację.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="unity-20193"></a>Jedność 2019.3

Ze względu na przełomowe zmiany, Unity 2019.3 nie jest obecnie obsługiwany. Prosimy o korzystanie z Unity 2019.1 lub 2019.2.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku użyto usługi Azure Cosmos DB do udostępnienia identyfikatorów kotwic między urządzeniami. Aby dowiedzieć się więcej na temat korzystania z funkcji Azure Spatial Anchors w nowej aplikacji Unity HoloLens, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Uruchamianie nowej aplikacji Unity HoloLens](./tutorial-new-unity-hololens-app.md)
