---
title: Zarządzanie plan usługi App Service na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak do wykonywania różnych zadań, aby zarządzać plan usługi App Service.
keywords: Usługa App service, usłudze azure app service, skala, plan usługi app service, zmiana, tworzenie, zarządzanie i zarządzania
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: 4859d0d5-3e3c-40cc-96eb-f318b2c51a3d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: cephalin
ms.openlocfilehash: 2c08522df598bd5c6313c3f026efe48e1c4a2c56
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39449363"
---
# <a name="manage-an-app-service-plan-in-azure"></a>Zarządzanie plan usługi App Service na platformie Azure

[Planu usługi Azure App Service](azure-web-sites-web-hosting-plans-in-depth-overview.md) zapewnia zasoby, które aplikacja usługi App Service musi zostać uruchomiony. Ten przewodnik pokazuje, jak zarządzać plan usługi App Service.

## <a name="create-an-app-service-plan"></a>Tworzenie planu usługi App Service

> [!TIP]
> Jeśli masz środowisko usługi App Service, zobacz [Utwórz plan usługi App Service w środowisku usługi App Service](environment/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan).

Można utworzyć pustego planu usługi App Service lub utworzyć plan, jako część tworzenia aplikacji.

1. W [witryny Azure portal](https://portal.azure.com), wybierz opcję **New** > **sieci Web i mobilność**, a następnie wybierz pozycję **aplikacji sieci Web** lub inny rodzaj aplikacji usługi app Service.

1. Wybierz istniejący plan usługi App Service lub utworzyć plan dla nowej aplikacji.

   ![Tworzenie aplikacji w witrynie Azure portal.][createWebApp]

   Aby utworzyć plan:

   a. Wybierz **[+] tworzenia nowych**.

      ![Utwórz plan usługi App Service.][createASP] 

   b. Aby uzyskać **planu usługi App Service**, wprowadź nazwę planu.

   c. Aby uzyskać **lokalizacji**, wybierz odpowiednią lokalizację.

   d. Aby uzyskać **warstwa cenowa**, wybierz odpowiednią warstwę cenową dla usługi. Wybierz **Wyświetl wszystkie** do widoku bardziej cennik, takich jak **bezpłatna** i **Shared**. Po wybraniu warstwy cenowej kliknij **wybierz** przycisku.

<a name="move"></a>

## <a name="move-an-app-to-another-app-service-plan"></a>Przenoszenie aplikacji do innego planu usługi App Service

Przenoszenie aplikacji do innego planu usługi App Service, tak długo, jak planu źródłowego i docelowego planu znajdują się w _tej samej grupie zasobów i region geograficzny_.

1. W [witryny Azure portal](https://portal.azure.com), przejdź do aplikacji, którą chcesz przenieść.

1. W menu Wyszukaj **planu usługi App Service** sekcji.

1. Wybierz **planu usługi App Service zmiany** otworzyć **planu usługi App Service** selektora.

   ![Selektor planu usługi App Service.][change] 

1. W **planu usługi App Service** selektor, wybierz istniejący plan można przenieść tej aplikacji do.   

> [!IMPORTANT]
> **Planu usługi App Service wybierz** strona jest filtrowana według następujących kryteriów: 
> - Istnieje w tej samej grupie zasobów 
> - Istnieje w tym samym regionie geograficznym 
> - W tej samej przestrzeni internetowej istnieje  
> 
> A _przestrzeni internetowej_ jest konstrukcją logiczną, w usłudze App Service, który definiuje grupowania zasobów serwera. Region geograficzny (na przykład zachodnie stany USA) zawiera wiele przestrzeni internetowych, aby można było przydzielić klientów, którzy korzystają z usługi App Service. Obecnie nie można przenieść zasobów usługi App Service między przestrzeni internetowych. 
> 

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Każdy plan ma swoją własną warstwę cenową. Na przykład przejście do lokacji **bezpłatna** warstwy do **standardowa** warstwy umożliwia wszystkie aplikacje przypisane do niego, można korzystać z funkcji i zasobów **standardowa** warstwy. Przenoszenie aplikacji z wyższej warstwy planu plan z warstwy niższej oznacza jednak, że nie masz już dostęp do pewnych funkcji. Jeśli aplikacja korzysta z funkcji, która nie jest dostępna w ramach planu docelowego, otrzymasz błąd, który pokazuje, która funkcja jest używana, która nie jest dostępna. 

Na przykład w jednej z aplikacji korzysta z certyfikatów SSL, może zostać wyświetlony ten komunikat o błędzie:

`Cannot update the site with hostname '<app_name>' because its current SSL configuration 'SNI based SSL enabled' is not allowed in the target compute mode. Allowed SSL configuration is 'Disabled'.`

W tym przypadku przed można przenieść ją do planu docelowego, musisz albo:
- Skalowanie w górę do warstwy cenowej planu docelowego **podstawowe** lub nowszej.
- Usuń wszystkie połączenia SSL z aplikacją.

## <a name="move-an-app-to-a-different-region"></a>Przenoszenie aplikacji do innego regionu

Region, w którym działa aplikacja, jest to region, planu usługi App Service, który znajduje się w. Jednak nie można zmienić regionu plan usługi App Service. Jeśli chcesz uruchomić aplikację w innym regionie, co alternatywą jest aplikacji klonowania. Klonowanie sprawia, że kopii aplikacji w nowy lub istniejący plan usługi App Service w dowolnym regionie.

Możesz znaleźć **aplikację** w **narzędzia programistyczne** części menu.

> [!IMPORTANT]
> Klonowanie ma pewne ograniczenia. Informacje o nich w [klonowania aplikacji usługi Azure App Service](app-service-web-app-cloning.md).

## <a name="scale-an-app-service-plan"></a>Skaluj plan usługi App Service

Skalowanie w górę usługi App Service plan w warstwie cenowej, zobacz [skalowanie aplikacji na platformie Azure](web-sites-scale.md).

Aby skalować liczbę wystąpień aplikacji, zobacz [ręczne lub automatyczne skalowanie liczby wystąpień](../monitoring-and-diagnostics/insights-how-to-scale.md).

<a name="delete"></a>

## <a name="delete-an-app-service-plan"></a>Usuwanie planu usługi App Service

Aby uniknąć nieoczekiwanych opłat, po usunięciu ostatniej aplikacji w ramach planu usługi App Service, App Service spowoduje również usunięcie plan domyślnie. Jeśli zdecydujesz się zachować zamiast tego planu, należy zmienić plan **bezpłatna** warstwy, więc nie są naliczane opłaty.

> [!IMPORTANT]
> Plany usługi App Service, w których brak aplikacji skojarzonych z nimi nadal naliczone opłaty, ponieważ nadal zarezerwować skonfigurowanych wystąpień maszyn wirtualnych.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Skalowanie aplikacji na platformie Azure](web-sites-scale.md)

[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
