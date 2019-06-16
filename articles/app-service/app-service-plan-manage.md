---
title: Zarządzanie planu usługi App Service — Azure | Dokumentacja firmy Microsoft
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
ms.custom: seodec18
ms.openlocfilehash: 936abe80a66c1dbe99e7d8a255fe8995a2df0803
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60852284"
---
# <a name="manage-an-app-service-plan-in-azure"></a>Zarządzanie plan usługi App Service na platformie Azure

[Planu usługi Azure App Service](overview-hosting-plans.md) zapewnia zasoby, które aplikacja usługi App Service musi zostać uruchomiony. Ten przewodnik pokazuje, jak zarządzać plan usługi App Service.

## <a name="create-an-app-service-plan"></a>Tworzenie planu usługi App Service

> [!TIP]
> Jeśli masz środowisko usługi App Service, zobacz [Utwórz plan usługi App Service w środowisku usługi App Service](environment/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan).

Można utworzyć pustego planu usługi App Service lub utworzyć plan, jako część tworzenia aplikacji.

1. W [witryny Azure portal](https://portal.azure.com), wybierz opcję **New** > **sieci Web i mobilność**, a następnie wybierz pozycję **aplikacji sieci Web** lub inny rodzaj aplikacji usługi app Service.

2. Wybierz istniejący plan usługi App Service lub utworzyć plan dla nowej aplikacji.

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

> [!NOTE]
> Platforma Azure wdroży każdego nowego planu usługi App Service do jednostki wdrożenia, wywoływana wewnętrznie przestrzeń sieci Web. Każdy region może mieć wiele przestrzeni internetowych, ale aplikacji mogą być tylko przenoszone między plany, które są tworzone w tej samej przestrzeni internetowej. Środowiska usługi App Service jest izolowane przestrzeni internetowej, dzięki czemu aplikacje mogą być przenoszone między planami w tym samym środowisku usługi App Service, ale nie między planami w różnych środowiskach usługi App Service.
>
> Nie można określić podczas tworzenia planu przestrzeni internetowej, ale istnieje możliwość upewnić się, że plan jest tworzony w tej samej przestrzeni internetowej jako istniejącego planu. Krótko mówiąc wszystkie plany utworzone za pomocą tej samej grupie zasobów i region kombinacji są wdrażane w tej samej przestrzeni internetowej. Na przykład jeśli utworzono planu w grupie zasobów, A i region B wszelkie planu, który następnie utworzysz w grupie zasobów, A i region B jest wdrażana w tej samej przestrzeni internetowej. Pamiętaj, że planów nie można przenieść przestrzeni internetowych po ich utworzeniu, więc planu nie można przenieść do "tej samej przestrzeni internetowej" jako innego planu przez przeniesienie ich do innej grupy zasobów.
> 

1. W [witryny Azure portal](https://portal.azure.com), przejdź do aplikacji, którą chcesz przenieść.

1. W menu Wyszukaj **planu usługi App Service** sekcji.

1. Wybierz **planu usługi App Service zmiany** otworzyć **planu usługi App Service** selektora.

   ![Selektor planu usługi App Service.][change] 

1. W **planu usługi App Service** selektor, wybierz istniejący plan można przenieść tej aplikacji do.   

**Planu usługi App Service wybierz** strona zawiera tylko te plany, które znajdują się w tej samej grupie zasobów i regionu geograficznego co planu usługi App Service w bieżącej aplikacji.

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
