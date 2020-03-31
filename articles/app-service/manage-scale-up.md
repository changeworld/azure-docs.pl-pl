---
title: Skalowanie w górę funkcji i pojemności
description: Dowiedz się, jak skalować aplikację w usłudze Azure App Service. Uzyskaj więcej procesora, pamięci, miejsca na dysku i dodatkowych funkcji.
ms.assetid: f7091b25-b2b6-48da-8d4a-dcf9b7baccab
ms.topic: article
ms.date: 08/19/2019
ms.custom: seodec18
ms.openlocfilehash: dfd9297e80836978b8a185df3fc4659676383802
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75659903"
---
# <a name="scale-up-an-app-in-azure-app-service"></a>Skalowanie aplikacji w usłudze Azure App Service

W tym artykule pokazano, jak skalować aplikację w usłudze Azure App Service. Istnieją dwa przepływy pracy skalowania, skalowania w górę i skalowania w poziomie, a w tym artykule opisano przepływ pracy skalowania w górę.

* [Skalowanie w górę:](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling)Uzyskaj więcej procesora, pamięci, miejsca na dysku i dodatkowych funkcji, takich jak dedykowane maszyny wirtualne (maszyny wirtualne), niestandardowe domeny i certyfikaty, miejsca przejściowe, skalowanie automatyczne i inne. Skalowanie w górę, zmieniając warstwę cenową planu usługi app service, do którego należy aplikacja.
* [Skaluj w poziomie:](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling)zwiększ liczbę wystąpień maszyn wirtualnych, które uruchamiają aplikację.
  Można skalować w poziomie do 30 wystąpień, w zależności od warstwy cenowej. [Środowiska usługi aplikacji](environment/intro.md) w warstwie **Izolowane** dodatkowo zwiększa liczbę skalowania w poziomie do 100 wystąpień. Aby uzyskać więcej informacji na temat skalowania w poziomie, zobacz [Skalowanie liczby wystąpień ręcznie lub automatycznie](../monitoring-and-diagnostics/insights-how-to-scale.md). Tam dowiesz się, jak używać skalowania automatycznego, czyli skalowania liczby wystąpień automatycznie na podstawie wstępnie zdefiniowanych reguł i harmonogramów.

Zastosowanie ustawień skali zajmuje tylko kilka sekund i wpływa na wszystkie aplikacje w [planie usługi App Service.](../app-service/overview-hosting-plans.md)
Nie wymagają one, aby zmienić kod lub ponownie wdrożyć aplikację.

Aby uzyskać informacje na temat cen i funkcji poszczególnych planów usługi App Service, zobacz [Szczegóły cen usługi App Service](https://azure.microsoft.com/pricing/details/web-sites/).  

> [!NOTE]
> Przed przełączeniem planu usługi app service z **bezpłatnej** warstwy, należy najpierw usunąć [limity wydatków](https://azure.microsoft.com/pricing/spending-limits/) w miejscu dla subskrypcji platformy Azure. Aby wyświetlić lub zmienić opcje subskrypcji usługi Microsoft Azure App Service, zobacz [Subskrypcje platformy Microsoft Azure][azuresubscriptions].
> 
> 

<a name="scalingsharedorbasic"></a>
<a name="scalingstandard"></a>

## <a name="scale-up-your-pricing-tier"></a>Skalowanie warstwy cenowej w górę

> [!NOTE]
> Aby skalować do warstwy **PremiumV2,** zobacz [Konfigurowanie warstwy PremiumV2 dla usługi App Service.](app-service-configure-premium-tier.md)
>

1. W przeglądarce otwórz witrynę [Azure Portal][portal].

1. Na stronie aplikacji Usługi App Service z lewego menu wybierz pozycję **Skaluj w górę (plan usługi app service).**
   
3. Wybierz warstwę, a następnie wybierz pozycję **Zastosuj**. Wybierz różne kategorie (na przykład **Produkcja),** a także **Zobacz dodatkowe opcje,** aby wyświetlić więcej warstw.
   
    ![Przejdź do celu skalowania aplikacji platformy Azure.][ChooseWHP]

    Po zakończeniu operacji zostanie wyświetlone okno podręczne powiadomień z zielonym znacznikiem wyboru sukcesu.

<a name="ScalingSQLServer"></a>

## <a name="scale-related-resources"></a>Skalowanie powiązanych zasobów
Jeśli aplikacja zależy od innych usług, takich jak Azure SQL Database lub Azure Storage, można skalować te zasoby oddzielnie. Te zasoby nie są zarządzane przez plan usługi app service.

1. Na stronie **Przegląd** aplikacji wybierz łącze **Grupa zasobów.**
   
    ![Skalowanie zasobów powiązanych z aplikacją Platformy Azure](./media/web-sites-scale/RGEssentialsLink.png)

2. Na części **Podsumowanie** grupy **zasobów** wybierz zasób, który chcesz skalować. Poniższy zrzut ekranu przedstawia zasób bazy danych SQL.
   
    ![Przejdź do strony grupy zasobów, aby skalować aplikację platformy Azure](./media/web-sites-scale/ResourceGroup.png)

    Aby skalować w górę powiązanego zasobu, zobacz dokumentację określonego typu zasobu. Na przykład, aby skalować w górę pojedynczą bazę danych SQL, zobacz [Skalowanie zasobów pojedynczej bazy danych w bazie danych SQL azure](../sql-database/sql-database-single-database-scale.md). Aby zwiększyć skalę usługi Azure Database dla zasobu MySQL, zobacz [Skalowanie zasobów MySQL](../mysql/concepts-pricing-tiers.md#scale-resources).

<a name="OtherFeatures"></a>
<a name="devfeatures"></a>

## <a name="compare-pricing-tiers"></a>Porównanie warstw cenowych

Aby uzyskać szczegółowe informacje, takie jak rozmiary maszyn wirtualnych dla każdej warstwy cenowej, zobacz [Szczegóły cen usługi app service](https://azure.microsoft.com/pricing/details/app-service).

Aby zapoznać się z tabelą limitów usług, przydziałów i ograniczeń oraz obsługiwanych funkcji w każdej warstwie, zobacz [Limity usługi App Service](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).

<a name="Next Steps"></a>

## <a name="more-resources"></a>Więcej zasobów

[Ręczne lub automatyczne skalowanie liczby wystąpień](../monitoring-and-diagnostics/insights-how-to-scale.md)  
[Konfigurowanie warstwy PremiumV2 dla usługi aplikacji](app-service-configure-premium-tier.md)

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
