---
title: Zarządzanie planem usługi aplikacji
description: Dowiedz się, jak wykonywać różne zadania do zarządzania planem usługi App Service, takie jak tworzenie, przenoszenie, skalowanie i usuwanie.
keywords: usługa aplikacji, usługa aplikacji platformy Azure, skalowanie, plan usługi aplikacji, zmiana, tworzenie, zarządzanie, zarządzanie
ms.assetid: 4859d0d5-3e3c-40cc-96eb-f318b2c51a3d
ms.topic: article
ms.date: 10/24/2019
ms.custom: seodec18
ms.openlocfilehash: d40f5db65ce9ca90ae978bac4491bdebccc2a328
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811712"
---
# <a name="manage-an-app-service-plan-in-azure"></a>Zarządzanie planem usługi app service na platformie Azure

[Plan usługi Azure App Service](overview-hosting-plans.md) zawiera zasoby, które aplikacja usługi App Service musi uruchomić. W tym przewodniku pokazano, jak zarządzać planem usługi app service.

## <a name="create-an-app-service-plan"></a>Tworzenie planu usługi App Service

> [!TIP]
> Jeśli masz środowisko usługi app service, zobacz [Tworzenie planu usługi app service w środowisku usługi aplikacji](environment/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan).

Możesz utworzyć pusty plan usługi App Service lub utworzyć plan w ramach tworzenia aplikacji.

1. W [witrynie Azure portal](https://portal.azure.com)wybierz pozycję **Utwórz zasób**.

   ![Tworzenie zasobu w witrynie Azure portal.][createResource] 

1. Wybierz **pozycję Nowa** > **aplikacja sieci Web** lub inny rodzaj aplikacji usługi aplikacji.

   ![Tworzenie aplikacji w witrynie Azure portal.][createWebApp] 

2. Skonfiguruj sekcję **Szczegóły wystąpienia** przed skonfigurowaniem planu usługi app service. Ustawienia, takie jak **Publikowanie** i **systemy operacyjne,** mogą zmieniać dostępne warstwy cenowe planu usługi App Service. **Region** określa, gdzie jest tworzony plan usługi aplikacji. 
   
3. W sekcji **Plan usługi aplikacji** wybierz istniejący plan lub utwórz plan, wybierając pozycję **Utwórz nowy**.

   ![Utwórz plan usługi app service.][createASP] 

4. Podczas tworzenia planu można wybrać warstwę cenową nowego planu. W **obszarze Sku i rozmiar**wybierz pozycję Zmień **rozmiar,** aby zmienić warstwę cenową. 

<a name="move"></a>

## <a name="move-an-app-to-another-app-service-plan"></a>Przenoszenie aplikacji do innego planu usługi aplikacji

Aplikację można przenieść do innego planu usługi App Service, o ile plan źródłowy i plan docelowy znajdują się w _tej samej grupie zasobów i regionie geograficznym_.

> [!NOTE]
> Platforma Azure wdraża każdy nowy plan usługi app service w jednostce wdrażania, wewnętrznie nazywane przestrzeni sieci web. Każdy region może mieć wiele obszarów sieci Web, ale aplikacja może przenosić się tylko między planami utworzonymi w tej samej przestrzeni internetowej. Środowisko usługi aplikacji jest izolowanym obszarem sieci Web, dzięki czemu aplikacje mogą być przenoszone między planami w tym samym środowisku usługi aplikacji, ale nie między planami w różnych środowiskach usługi app service.
>
> Podczas tworzenia planu nie można określić żądanej przestrzeni sieci Web, ale można upewnić się, że plan jest tworzony w tej samej przestrzeni sieci Web, co istniejący plan. W skrócie wszystkie plany utworzone przy tej samej kombinacji grupy zasobów i regionu są wdrażane w tej samej przestrzeni internetowej. Na przykład jeśli plan został utworzony w grupie zasobów A i regionie B, każdy plan, który następnie utworzysz w grupie zasobów A i regionie B, zostanie wdrożony w tym samym obszarze internetowym. Należy zauważyć, że plany nie mogą przenosić przestrzeni sieci Web po ich utworzeniu, więc nie można przenieść planu do "tej samej przestrzeni internetowej" co inny plan, przenosząc go do innej grupy zasobów.
> 

1. W [witrynie Azure portal](https://portal.azure.com)wyszukaj i wybierz **usługi aplikacji** oraz wybierz aplikację, którą chcesz przenieść.

2. W menu po lewej stronie wybierz pozycję **Zmień plan usługi app service**.

3. W menu rozwijanym **planu usługi aplikacji** wybierz istniejący plan, do który chcesz przenieść aplikację. W rozwijanej pokazuje tylko plany, które znajdują się w tej samej grupie zasobów i regionie geograficznym co bieżący plan usługi app service. Jeśli taki plan nie istnieje, umożliwia utworzenie planu domyślnie. Nowy plan można również utworzyć ręcznie, wybierając pozycję **Utwórz nowy**.

4. Jeśli tworzysz plan, możesz wybrać warstwę cenową nowego planu. W **warstwie cenowej**wybierz istniejącą warstwę, aby ją zmienić. 
   
   > [!IMPORTANT]
   > Jeśli przenosisz aplikację z planu wyższego poziomu do planu niższego poziomu, takiego jak **D1** do **F1,** aplikacja może utracić pewne możliwości w planie docelowym. Jeśli na przykład aplikacja korzysta z certyfikatów TLS/SSL, może zostać wyświetlony ten komunikat o błędzie:
   >
   > `Cannot update the site with hostname '<app_name>' because its current SSL configuration 'SNI based SSL enabled' is not allowed in the target compute mode. Allowed SSL configuration is 'Disabled'.`

5. Po zakończeniu wybierz przycisk **OK**.
   
   ![Selektor planu usługi app service.][change] 

## <a name="move-an-app-to-a-different-region"></a>Przenoszenie aplikacji do innego regionu

Region, w którym działa aplikacja, to region planu usługi app service, w którym się znajduje. Nie można jednak zmienić regionu planu usługi aplikacji. Jeśli chcesz uruchomić aplikację w innym regionie, jedną z alternatyw jest klonowanie aplikacji. Klonowanie tworzy kopię aplikacji w nowym lub istniejącym planie usługi app service w dowolnym regionie.

**Aplikację Klonuj** można znaleźć w sekcji **Narzędzia programistyczne** menu.

> [!IMPORTANT]
> Klonowanie ma pewne ograniczenia. Możesz przeczytać o nich w [klonowaniu aplikacji usługi Azure App Service](app-service-web-app-cloning.md).

## <a name="scale-an-app-service-plan"></a>Skalowanie planu usługi aplikacji

Aby zwiększyć poziom cenowy planu usługi app service, zobacz [Skalowanie aplikacji na platformie Azure.](manage-scale-up.md)

Aby skalować w poziomie liczbę wystąpień aplikacji, zobacz [Skalowanie liczby wystąpień ręcznie lub automatycznie](../monitoring-and-diagnostics/insights-how-to-scale.md).

<a name="delete"></a>

## <a name="delete-an-app-service-plan"></a>Usuwanie planu usługi aplikacji

Aby uniknąć nieoczekiwanych opłat, po usunięciu ostatniej aplikacji w planie usługi app service usługa app service również domyślnie usuwa plan. Jeśli zamiast tego zdecydujesz się zachować plan, zmień plan na **Warstwę bezpłatną,** aby nie naliczana była opłata.

> [!IMPORTANT]
> Plany usługi App Service, które nie mają żadnych aplikacji skojarzonych z nimi nadal ponoszą opłaty, ponieważ nadal rezerwują skonfigurowane wystąpienia maszyn wirtualnych.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Skalowanie aplikacji w górę na platformie Azure](manage-scale-up.md)

[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
[createResource]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-a-resource.png
