---
title: Skalowanie w górę funkcje i możliwości — w usłudze Azure App Service | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skalować aplikację w usłudze Azure App Service do dodawania pojemności i funkcji.
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: mollybos
ms.assetid: f7091b25-b2b6-48da-8d4a-dcf9b7baccab
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 98d3d1f6fc0f2f30196f360811808579dfbab312
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60832156"
---
# <a name="scale-up-an-app-in-azure"></a>Skalowanie aplikacji na platformie Azure

> [!NOTE]
> Nowy **PremiumV2** warstwy umożliwia szybsze procesory, magazynem dysków SSD i rozwiązanie quorum zwiększa dwukrotnie stosunek pamięć rdzeń istniejących warstw cenowych. Za pomocą korzyści wydajności można zapisać pieniądze, po uruchomieniu aplikacji na mniejszej liczby wystąpień. Skalowanie do **PremiumV2** warstwy, zobacz [warstwy PremiumV2 konfigurowanie dla usługi App Service](app-service-configure-premium-tier.md).
>

W tym artykule pokazano, jak skalować aplikację w usłudze Azure App Service. Istnieją dwa przepływy pracy na potrzeby skalowania, skalowanie górę i skalowania w poziomie i w tym artykule opisano na skalowaniu w górę przepływu pracy.

* [Skalowanie w górę](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): Uzyskaj więcej procesora CPU, pamięci, miejsca na dysku i dodatkowych funkcji, takich jak dedykowanych maszyn wirtualnych (VM), niestandardowe domeny i certyfikaty, przemieszczania miejsc, skalowanie automatyczne i nie tylko. Skalowanie, zmieniając warstwę cenową planu usługi App Service, do którego należy aplikacja.
* [Skalowanie w poziomie](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): Zwiększ liczbę wystąpień maszyn wirtualnych, które Uruchom aplikację.
  Można skalować w poziomie do maksymalnie 20 wystąpień, w zależności od warstwy cenowej. [Środowiska usługi App Service](environment/intro.md) w **izolowany** warstwy dodatkowo zwiększa się liczba Twoich skalowalnego w poziomie do 100 wystąpień. Aby uzyskać więcej informacji na temat skalowania w poziomie, zobacz [ręczne lub automatyczne skalowanie liczby wystąpień](../monitoring-and-diagnostics/insights-how-to-scale.md). Istnieje Dowiedz się, jak używać automatycznego skalowania, czyli skalować liczbę wystąpień automatycznie na podstawie wstępnie zdefiniowanych reguł i harmonogramy.

Ustawienia skalowania zająć tylko kilka sekund do zastosowania i wpływają na wszystkie aplikacje w Twojej [planu usługi App Service](../app-service/overview-hosting-plans.md).
Nie wymagają one zmian w kodzie lub ponownego wdrażania aplikacji.

Aby uzyskać informacji na temat cen i funkcji poszczególnych planów usługi App Service, zobacz [szczegóły cennika usługi App Service](https://azure.microsoft.com/pricing/details/web-sites/).  

> [!NOTE]
> Przed przełączeniem plan usługi App Service z **bezpłatna** warstwy, należy najpierw usunąć [limitów wydatków](https://azure.microsoft.com/pricing/spending-limits/) w miejscu, aby Twoja subskrypcja platformy Azure. Aby wyświetlić lub zmienić opcje dla Twojej subskrypcji Microsoft Azure App Service, zobacz [subskrypcji platformy Microsoft Azure][azuresubscriptions].
> 
> 

<a name="scalingsharedorbasic"></a>
<a name="scalingstandard"></a>

## <a name="scale-up-your-pricing-tier"></a>Skalowanie w górę do warstwy cenowej
1. Otwórz w przeglądarce, [witryny Azure portal][portal].
2. Na stronie aplikacji usługi App Service, kliknij przycisk **wszystkie ustawienia**, a następnie kliknij przycisk **Skaluj w górę**.
   
    ![Przejdź do skalowania w górę aplikacji platformy Azure.][ChooseWHP]
3. Wybieranie warstwy, a następnie kliknij przycisk **Zastosuj**.
   
    **Powiadomienia** karta będzie flash zielona **Powodzenie** po zakończeniu operacji.

<a name="ScalingSQLServer"></a>

## <a name="scale-related-resources"></a>Skalowanie powiązane zasoby
Jeśli aplikacja jest zależna od innych usług, takich jak Azure SQL Database lub usługi Azure Storage, możesz skalować w górę te zasoby osobno. Te zasoby nie są zarządzane przez plan usługi App Service.

1. W **Essentials**, kliknij przycisk **grupy zasobów** łącza.
   
    ![Skalowanie aplikacji platformy Azure powiązane zasoby](./media/web-sites-scale/RGEssentialsLink.png)
2. W **Podsumowanie** wchodzi w skład **grupy zasobów** kliknij zasób, który ma być skalowana. Poniższy zrzut ekranu przedstawia zasobów bazy danych SQL i zasobów usługi Azure Storage.
   
    ![Przejdź do strony grupy zasobów, aby skalować w górę aplikacji platformy Azure](./media/web-sites-scale/ResourceGroup.png)
3. Zasoby bazy danych SQL, kliknij przycisk **ustawienia** > **warstwa cenowa** skalowanie warstwy cenowej.
   
    ![Skalowanie w górę do wewnętrznej bazy danych SQL Database dla aplikacji platformy Azure](./media/web-sites-scale/ScaleDatabase.png)
   
    Można też włączyć [geografickou replikaci](../sql-database/sql-database-geo-replication-overview.md) wystąpienia bazy danych SQL.
   
    W przypadku zasobów usługi Azure Storage, kliknij przycisk **ustawienia** > **konfiguracji** skalowanie w górę opcji magazyn.
   
    ![Skalowanie konta usługi Azure Storage używanymi przez aplikację platformy Azure](./media/web-sites-scale/ScaleStorage.png)

<a name="OtherFeatures"></a>
<a name="devfeatures"></a>

## <a name="compare-pricing-tiers"></a>Porównanie warstw cenowych
Aby uzyskać szczegółowe informacje, takie jak rozmiarów maszyn wirtualnych dla każdej warstwy cenowej, zobacz [szczegóły cennika usługi App Service](https://azure.microsoft.com/pricing/details/web-sites/).
Dla tabeli limity, przydziały i ograniczenia i funkcji obsługiwanych w poszczególnych warstwach, zobacz [limity usługi App Service](../azure-subscription-service-limits.md#app-service-limits).

<a name="Next Steps"></a>

## <a name="next-steps"></a>Kolejne kroki
* Aby uzyskać informacje o cenach, pomocy technicznej i umowy SLA skorzystaj z następujących linków:
  
    [Szczegóły cennika transferów danych](https://azure.microsoft.com/pricing/details/data-transfers/)
  
    [Plany pomocy technicznej systemu Azure firmy Microsoft](https://azure.microsoft.com/support/plans/)
  
    [Umowy dotyczące poziomu usług](https://azure.microsoft.com/support/legal/sla/)
  
    [SQL Database — szczegóły cennika](https://azure.microsoft.com/pricing/details/sql-database/)
  
    [Maszyna wirtualna i rozmiary usług w chmurze dla platformy Microsoft Azure][vmsizes]
  
* Aby uzyskać informacje o usłudze Azure App Service najlepszych rozwiązań, w tym tworzenia architektury skalowalne i odporne na błędy, zobacz [najlepsze rozwiązania: Usługa Azure App Service Web Apps](https://azure.microsoft.com/blog/best-practices-windows-azure-websites-waws/).
* Dla filmów wideo na temat skalowania aplikacji usługi App Service zobacz następujące zasoby:
  
  * [Kiedy skalować witryny sieci Web platformy Azure — za pomocą Stefan Schackow](https://azure.microsoft.com/resources/videos/azure-web-sites-free-vs-standard-scaling/)
  * [Automatyczne skalowanie usługi Azure Websites, procesora CPU lub według harmonogramu — za pomocą Stefan Schackow](https://azure.microsoft.com/resources/videos/auto-scaling-azure-web-sites/)
  * [Jak usługa Azure skalowanie witryny sieci Web — za pomocą Stefan Schackow](https://azure.microsoft.com/resources/videos/how-azure-web-sites-scale/)

<!-- LINKS -->
[vmsizes]:https://azure.microsoft.com/pricing/details/app-service/
[SQLaccountsbilling]:https://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]:https://account.windowsazure.com/subscriptions
[portal]: https://portal.azure.com/

<!-- IMAGES -->
[ChooseWHP]: ./media/web-sites-scale/scale1ChooseWHP.png
[ResourceGroup]: ./media/web-sites-scale/scale10ResourceGroup.png
[ScaleDatabase]: ./media/web-sites-scale/scale11SQLScale.png
[GeoReplication]: ./media/web-sites-scale/scale12SQLGeoReplication.png
