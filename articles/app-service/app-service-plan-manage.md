---
title: Zarządzanie planem App Service — platforma Azure | Microsoft Docs
description: Dowiedz się, jak wykonywać różne zadania związane z zarządzaniem planem App Service.
keywords: App Service, Azure App Service, skala, plan usługi App Service, zmiana, tworzenie, zarządzanie, zarządzanie
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: 4859d0d5-3e3c-40cc-96eb-f318b2c51a3d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: a5e69209c30eae816837ce8f00a065231a5fd821
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70067203"
---
# <a name="manage-an-app-service-plan-in-azure"></a>Zarządzanie planem App Service na platformie Azure

[Plan Azure App Service](overview-hosting-plans.md) zapewnia zasoby, które aplikacja App Service musi uruchomić. W tym przewodniku pokazano, jak zarządzać planem App Service.

## <a name="create-an-app-service-plan"></a>Tworzenie planu usługi App Service

> [!TIP]
> Jeśli masz App Service Environment, zobacz [Tworzenie planu App Service w App Service Environment](environment/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan).

Można utworzyć pusty plan App Service lub utworzyć plan w ramach tworzenia aplikacji.

1. W [Azure Portal](https://portal.azure.com)wybierz pozycję **Nowy** > **Sieć Web**i mobilność, a następnie wybierz pozycję **aplikacja internetowa** lub inny rodzaj aplikacji App Service.

2. Wybierz istniejący plan App Service lub Utwórz plan dla nowej aplikacji.

   ![Utwórz aplikację w Azure Portal.][createWebApp]

   Aby utworzyć plan:

   a. Wybierz pozycję **[+] Utwórz nowy**.

      ![Utwórz plan App Service.][createASP] 

   b. W polu **plan App Service**wprowadź nazwę planu.

   c. W obszarze **Lokalizacja**wybierz odpowiednią lokalizację.

   d. W obszarze **warstwa cenowa**wybierz odpowiednią warstwę cenową dla usługi. Wybierz pozycję **Wyświetl wszystko** , aby wyświetlić więcej opcji cenowych, takich jak **bezpłatne** i **udostępnione**. Po wybraniu warstwy cenowej kliknij przycisk **Wybierz** .

<a name="move"></a>

## <a name="move-an-app-to-another-app-service-plan"></a>Przenoszenie aplikacji do innego planu App Service

Możesz przenieść aplikację do innego planu App Service, o ile plan źródłowy i plan docelowy znajdują się w _tej samej grupie zasobów i regionie_geograficznym.

> [!NOTE]
> Platforma Azure wdraża każdy nowy plan App Service w jednostce wdrożenia wewnętrznie nazywanej przestrzenią sieci Web. Każdy region może mieć wiele przestrzeni sieci Web, ale aplikacja może przenosić się tylko między planami, które są tworzone w tym samym obszarze sieci Web. App Service Environment jest izolowanym miejscem sieci Web, dzięki czemu aplikacje mogą być przenoszone między planami w tym samym App Service Environment, ale nie między planami w różnych środowiskach App Service.
>
> Nie można określić miejsca sieci Web, które ma być używane podczas tworzenia planu, ale istnieje możliwość upewnienia się, że plan jest tworzony w tym samym obszarze sieci Web co istniejący plan. We wszystkich planach utworzonych za pomocą tej samej kombinacji grupy zasobów i regionu są wdrażane w tym samym obszarze sieci Web. Na przykład jeśli utworzono plan w grupie zasobów a i w regionie B, to każdy plan, który następnie utworzysz w grupie zasobów a i region B jest wdrażany w tym samym obszarze sieci Web. Należy pamiętać, że plany nie mogą przenosić obszarów sieci Web po ich utworzeniu, dlatego nie można przenieść planu do "tego samego obszaru sieci Web" jako innego planu przez przeniesienie go do innej grupy zasobów.
> 

1. W [Azure Portal](https://portal.azure.com)przejdź do aplikacji, którą chcesz przenieść.

1. W menu poszukaj sekcji **Plan App Service** .

1. Wybierz pozycję **zmień App Service plan** , aby otworzyć selektor **planu App Service** .

   ![Selektor planu App Service.][change] 

1. W selektorze **planu App Service** wybierz istniejący plan, do którego chcesz przenieść tę aplikację.   

Na stronie **Wybierz plan App Service** są wyświetlane tylko plany, które znajdują się w tej samej grupie zasobów i regionie geograficznym, co plan App Service bieżącej aplikacji.

Każdy plan ma własną warstwę cenową. Na przykład przeniesienie lokacji z warstwy **bezpłatna** do warstwy **standardowa** umożliwia wszystkim aplikacjom przypisanym do korzystania z funkcji i zasobów warstwy **standardowa** . Przeniesienie aplikacji z planu wyższego poziomu do planu niższej warstwy oznacza jednak, że nie masz już dostępu do niektórych funkcji. Jeśli aplikacja korzysta z funkcji, która nie jest dostępna w planie docelowym, zostanie wyświetlony komunikat o błędzie, który pokazuje, która funkcja jest używana, która jest niedostępna. 

Na przykład jeśli jedna z aplikacji używa certyfikatów SSL, może zostać wyświetlony następujący komunikat o błędzie:

`Cannot update the site with hostname '<app_name>' because its current SSL configuration 'SNI based SSL enabled' is not allowed in the target compute mode. Allowed SSL configuration is 'Disabled'.`

W tym przypadku zanim będzie można przenieść aplikację do planu docelowego, należy wykonać jedną z:
- Skaluj warstwę cenową planu docelowego do warstwy **podstawowa** lub wyższej.
- Usuń wszystkie połączenia SSL z aplikacją.

## <a name="move-an-app-to-a-different-region"></a>Przenoszenie aplikacji do innego regionu

Region, w którym jest uruchomiona aplikacja, jest regionem App Service planu. Nie można jednak zmienić regionu planu App Service. Jeśli chcesz uruchomić aplikację w innym regionie, jedną alternatywą jest klonowanie aplikacji. Klonowanie tworzy kopię aplikacji w nowym lub istniejącym planie App Service w dowolnym regionie.

**Klonowanie aplikacji** można znaleźć w sekcji **Narzędzia programistyczne** w menu.

> [!IMPORTANT]
> Klonowanie ma pewne ograniczenia. Informacje o nich można znaleźć w [Azure App Service klonowania aplikacji](app-service-web-app-cloning.md).

## <a name="scale-an-app-service-plan"></a>Skalowanie planu App Service

Aby skalować w górę warstwę cenową planu App Service, zobacz [skalowanie w górę aplikacji na platformie Azure](manage-scale-up.md).

Aby skalować liczbę wystąpień aplikacji, zobacz [Ręczne lub automatyczne skalowanie liczby wystąpień](../monitoring-and-diagnostics/insights-how-to-scale.md).

<a name="delete"></a>

## <a name="delete-an-app-service-plan"></a>Usuwanie planu App Service

Aby uniknąć nieoczekiwanych opłat, po usunięciu ostatniej aplikacji w planie App Service, App Service również domyślnie usuwa plan. Jeśli wybierzesz opcję zachowania planu, należy zmienić plan na **bezpłatna** , aby nie naliczać opłat.

> [!IMPORTANT]
> Plany App Service, do których nie są skojarzone żadne aplikacje, nadal będą naliczane opłaty, ponieważ nadal rezerwują skonfigurowane wystąpienia maszyn wirtualnych.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Skalowanie aplikacji w górę na platformie Azure](manage-scale-up.md)

[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
