---
title: Samouczek SaaS dla jednej dzierżawy
description: Wdrażanie i eksplorowanie autonomicznej aplikacji SaaS z jedną dzierżawą, która korzysta z bazy danych SQL Azure.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: sstein
ms.date: 11/07/2018
ms.openlocfilehash: e3afc8aa58551b995070ffaca978c8e7c8454da3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822135"
---
# <a name="deploy-and-explore-a-standalone-single-tenant-application-that-uses-azure-sql-database"></a>Wdrażanie i eksplorowanie autonomicznej aplikacji z jedną dzierżawą, która korzysta z bazy danych SQL azure

W tym samouczku można wdrożyć i eksplorować Wingtip Bilety SaaS przykładowej aplikacji opracowany przy użyciu autonomicznej aplikacji lub app-per-tenant, wzorzec.  Aplikacja została zaprojektowana do prezentowania funkcji usługi Azure SQL Database, które upraszczają włączanie scenariuszy SaaS z wieloma dzierżawami.

Autonomiczna aplikacja lub wzorzec aplikacji na dzierżawę wdraża wystąpienie aplikacji dla każdej dzierżawy.  Każda aplikacja jest skonfigurowana dla określonej dzierżawy i wdrożona w oddzielnej grupie zasobów platformy Azure. Wiele wystąpień aplikacji są aprowizowane w celu zapewnienia rozwiązania wielu dzierżawców. Ten wzorzec najlepiej nadaje się do mniejszej liczby dzierżawców, gdzie izolacja dzierżawy jest najwyższym priorytetem. Platforma Azure ma programy partnerskie, które umożliwiają wdrażanie zasobów w subskrypcji dzierżawy i zarządzanie przez dostawcę usług w imieniu dzierżawy. 

W tym samouczku wdrożysz trzy autonomiczne aplikacje dla trzech dzierżaw w ramach subskrypcji platformy Azure.  Masz pełny dostęp do eksplorowania i pracy z poszczególnymi składnikami aplikacji.

Kod źródłowy aplikacji i skrypty zarządzania są dostępne w repozytorium [WingtipTicketsSaaS-StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp) GitHub. Aplikacja została utworzona przy użyciu programu Visual Studio 2015 i nie pomyślnie otworzyć i skompilować w programie Visual Studio 2019 bez aktualizacji.


Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Jak wdrożyć wingtip bilety SaaS autonomicznej aplikacji.
> * Gdzie można uzyskać kod źródłowy aplikacji i skrypty zarządzania.
> * Informacje o serwerach i bazach danych, które tworzą aplikację.

Zostaną wydane dodatkowe samouczki. Umożliwiają one eksplorowanie szeregu scenariuszy zarządzania opartych na tym wzorcu aplikacji.   

## <a name="deploy-the-wingtip-tickets-saas-standalone-application"></a>Wdrażanie aplikacji autonomicznej SaaS Wingtip Tickets

Wdrażanie aplikacji dla trzech dostarczonych dzierżaw:

1. Kliknij każdy niebieski przycisk **Wdrażanie na platformie Azure,** aby otworzyć szablon wdrożenia w [witrynie Azure portal](https://portal.azure.com). Każdy szablon wymaga dwóch wartości parametrów; nazwa nowej grupy zasobów i nazwa użytkownika, która odróżnia to wdrożenie od innych wdrożeń aplikacji. Następny krok zawiera szczegółowe informacje dotyczące ustawiania tych wartości.<br><br>
    <a href="https://aka.ms/deploywingtipsa-contoso" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a>&nbsp; **Sala koncertowa Contoso**
<br><br>
    <a href="https://aka.ms/deploywingtipsa-dogwood" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a>&nbsp; **Dereń Dojo**
<br><br>
    <a href="https://aka.ms/deploywingtipsa-fabrikam" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a>&nbsp; **Klub Jazzowy Fabrikam**

2. Wprowadź wymagane wartości parametrów dla każdego wdrożenia.

    > [!IMPORTANT]
    > Niektóre uwierzytelniania i zapory serwera są celowo niezabezpieczone do celów demonstracyjnych. **Utwórz nową grupę zasobów** dla każdego wdrożenia aplikacji.  Nie należy używać istniejącej grupy zasobów. Nie należy używać tej aplikacji lub żadnych zasobów, które tworzy, do produkcji. Usuń wszystkie grupy zasobów po zakończeniu pracy z aplikacjami, aby zatrzymać powiązane rozliczenia.

    Najlepiej używać tylko małych liter, cyfr i łączników w nazwach zasobów.
    * W przypadku **grupy zasobów**wybierz pozycję Utwórz nowy, a następnie podaj małe litery Nazwa grupy zasobów. **wingtip-sa-\<\>-\<venueName\> użytkownik** jest zalecanym wzorem.  W \<przypadku\>venueName należy zastąpić nazwę miejsca bez spacji. Dla \<\>użytkownika zastąp wartość użytkownika od dołu.  Z tego wzorca, nazwy grup zasobów może być *wingtip-sa-contosoconcerthall-af1*, *wingtip-sa-derwooddojo-af1*, *wingtip-sa-fabrikamjazzclub-af1*.
    * Wybierz **lokalizację** z listy rozwijanej.

    * Dla **użytkownika** - Zalecamy krótką wartość użytkownika, taką jak inicjały plus cyfra: na przykład *af1*.


3. **Wdrażanie aplikacji**.

    * Kliknij, aby zaakceptować Warunki korzystania z serwisu.
    * Kliknij pozycję **Kup**.

4. Monitoruj stan wszystkich trzech wdrożeń, klikając **pozycję Powiadomienia** (ikona dzwonka po prawej stronie pola wyszukiwania). Wdrażanie aplikacji trwa około pięciu minut.


## <a name="run-the-applications"></a>Uruchamianie aplikacji

Aplikacja prezentuje miejsca, w których odbywają się wydarzenia.  Lokale są najemcami aplikacji. Każde miejsce otrzymuje spersonalizowaną stronę internetową, aby wymienić swoje wydarzenia i sprzedawać bilety. Rodzaje miejsc obejmują sale koncertowe, kluby jazzowe i kluby sportowe. W próbce typ miejsca określa zdjęcie w tle pokazane na stronie internetowej obiektu.   W modelu aplikacji autonomicznej każde miejsce ma oddzielne wystąpienie aplikacji z własną autonomiczną bazą danych SQL.

1. Otwórz stronę zdarzeń dla każdej z trzech dzierżaw w oddzielnych kartach przeglądarki:

   - http://events.contosoconcerthall.&lt;użytkownik&gt;.trafficmanager.net
   - http://events.dogwooddojo.&lt;użytkownik&gt;.trafficmanager.net
   - http://events.fabrikamjazzclub.&lt;użytkownik&gt;.trafficmanager.net

     (W każdym adresie URL zastąp &lt;użytkownika&gt; wartością użytkownika wdrożenia).

   ![Zdarzenia](./media/saas-standaloneapp-get-started-deploy/fabrikam.png)

Aby kontrolować dystrybucję żądań przychodzących, aplikacja korzysta z [*usługi Azure Traffic Manager*](../traffic-manager/traffic-manager-overview.md). Każde wystąpienie aplikacji specyficzne dla dzierżawy zawiera nazwę dzierżawy jako część nazwy domeny w adresie URL. Wszystkie adresy URL dzierżawy zawierają określoną wartość **użytkownika.** Adresy URL mają następujący format:
- http://events.&lt;nazwa miejsca&gt;. &lt;użytkownik&gt;.trafficmanager.net

Lokalizacja bazy danych każdej dzierżawy znajduje **się** w ustawieniach aplikacji odpowiedniej wdrożonej aplikacji.

W środowisku produkcyjnym zazwyczaj tworzysz rekord DNS CNAME, aby [*skierować firmową domenę internetową*](../traffic-manager/traffic-manager-point-internet-domain.md) na adres URL profilu menedżera ruchu.


## <a name="explore-the-servers-and-tenant-databases"></a>Eksplorowanie serwerów i baz danych dzierżawy

Przyjrzyjmy się niektórym zasobom, które zostały wdrożone:

1. W [witrynie Azure portal](https://portal.azure.com)przejdź do listy grup zasobów.
2. Powinny być widoczne trzy grupy zasobów dzierżawy.
3. Otwórz grupę zasobów **&lt;użytkownika&gt; wingtip-sa-fabrikam,** która zawiera zasoby dla wdrożenia Fabrikam Jazz Club.  Serwer **&lt;użytkownika&gt; fabrikamjazzclub** zawiera bazę danych **fabrikamjazzclub.**

Każda baza danych dzierżawy jest *autonomiczną bazą* danych 50 DTU.

## <a name="additional-resources"></a>Zasoby dodatkowe

<!--
* Additional [tutorials that build on the Wingtip SaaS application](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* To learn about elastic pools, see [*What is an Azure SQL elastic pool*](sql-database-elastic-pool.md)
* To learn about elastic jobs, see [*Managing scaled-out cloud databases*](elastic-jobs-overview.md)
-->

- Aby dowiedzieć się więcej o wielodostępnych aplikacjach SaaS, zobacz [Wzorce projektowania dla aplikacji SaaS z wieloma dzierżawami.](saas-tenancy-app-design-patterns.md)

 
## <a name="delete-resource-groups-to-stop-billing"></a>Usuwanie grup zasobów w celu zatrzymania rozliczeń ##

Po zakończeniu korzystania z próbki usuń wszystkie grupy zasobów utworzone w celu zatrzymania skojarzonego rozliczeń.

## <a name="next-steps"></a>Następne kroki

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Jak wdrożyć wingtip bilety SaaS autonomicznej aplikacji.
> * Informacje o serwerach i bazach danych, które tworzą aplikację.
> * Jak usunąć przykładowe zasoby, aby zatrzymać powiązane rozliczenia.

Następnie wypróbuj samouczek [aprowizowania i wykazu,](saas-standaloneapp-provision-and-catalog.md) w którym będziesz eksplorować użycie katalogu dzierżawców, który umożliwia szereg scenariuszy między dzierżawcami, takich jak zarządzanie schematem i analizy dzierżawy.
 

