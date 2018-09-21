---
title: SaaS pojedynczej dzierżawy — samouczek — Azure SQL Database | Dokumentacja firmy Microsoft
description: Wdrażanie i eksplorowanie aplikacji SaaS pojedynczej dzierżawy autonomicznego, który używa usługi Azure SQL Database.
keywords: samouczek usługi sql database
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 09/19/2018
ms.author: genemi
ms.openlocfilehash: 806cea75a9050c5d74f4c6b929d76712e51dd9d1
ms.sourcegitcommit: 8b694bf803806b2f237494cd3b69f13751de9926
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/20/2018
ms.locfileid: "46497777"
---
# <a name="deploy-and-explore-a-standalone-single-tenant-application-that-uses-azure-sql-database"></a>Wdrażanie i eksplorowanie oddzielną aplikację pojedynczej dzierżawy, który używa usługi Azure SQL Database

W tym samouczku, wdrażanie i eksplorowanie przykładowej aplikacji SaaS o nazwie Wingtip Tickets opracowanych za pomocą wzorca aplikacji autonomicznej lub aplikacji dla dzierżawcy.  Aplikacja jest przeznaczona do prezentowania funkcji usługi Azure SQL Database, które upraszczają włączenie scenariuszy SaaS z wieloma dzierżawami.

Aplikacja autonomiczna lub wzorzec aplikacji dla dzierżawcy wdraża wystąpienie aplikacji, dla każdej dzierżawy.  Każda aplikacja jest skonfigurowany do określonej dzierżawy i wdrożone w grupie oddzielne zasoby platformy Azure. Wiele wystąpień aplikacji są aprowizowane zapewnienie rozwiązanie dla wielu dzierżawców. Ten wzorzec jest najlepiej nadaje się do mniejszej liczby, dzierżaw, gdzie izolacji dzierżawy ma najwyższy priorytet. Platforma Azure oferuje partnera programów, które umożliwiają zasobom można wdrożyć w subskrypcji dzierżawcy i zarządzane przez dostawcę usług w imieniu dzierżawcy. 

W tym samouczku wdrożysz trzy aplikacje autonomiczne trzy dzierżaw do subskrypcji platformy Azure.  Masz pełny dostęp i pracować z poszczególnych składników aplikacji.

Skrypty kodu i zarządzanie źródłowy aplikacji są dostępne w [WingtipTicketsSaaS StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp) repozytorium GitHub.


Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Jak wdrożyć aplikację Wingtip Tickets SaaS autonomicznych.
> * Gdzie można uzyskać kod źródłowy aplikacji i skrypty zarządzania.
> * Temat serwerów i baz danych, które składają się na aplikację.

Dodatkowe samouczki zostaną wydane. Pozwoli ich do eksplorowania szeroką gamę scenariuszy zarządzania, na podstawie tego wzorca aplikacji.   

## <a name="deploy-the-wingtip-tickets-saas-standalone-application"></a>Wdróż aplikację Wingtip Tickets autonomiczny SaaS

Wdróż aplikację dla trzech podana dzierżaw:

1. Kliknij każdy niebieski **Wdróż na platformie Azure** przycisk, aby otworzyć szablon wdrożenia w [witryny Azure portal](https://portal.azure.com). Każdy szablon wymaga dwóch wartości parametru. Nazwa nowej grupy zasobów, a nazwę użytkownika, która odróżnia to wdrożenie, od innych wdrożeń aplikacji. Następnym krokiem zawiera szczegółowe informacje, do ustawiania tych wartości.<br><br>
    <a href="http://aka.ms/deploywingtipsa-contoso" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp; **Firmy Contoso Concert Hall**
<br><br>
    <a href="http://aka.ms/deploywingtipsa-dogwood" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp; **Dojo dereń**
<br><br>
    <a href="http://aka.ms/deploywingtipsa-fabrikam" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp; **Fabrikam Jazz Club**

2. Wprowadź wymagane wartości parametrów dla każdego wdrożenia.

    > [!IMPORTANT]
    > Niektóre zapory uwierzytelniania i serwera są celowo niezabezpieczone w celach demonstracyjnych. **Utwórz nową grupę zasobów** dla każdego wdrożenia aplikacji.  Nie należy używać istniejącej grupy zasobów. Nie należy używać tej aplikacji lub wszystkie zasoby, które tworzy, w środowisku produkcyjnym. Po zakończeniu pracy z aplikacjami, aby zatrzymać ich rozliczanie, należy usunąć wszystkie grupy zasobów.

    Najlepiej użyć tylko małe litery, cyfry i łączniki w nazwach zasobów.
    * Aby uzyskać **grupy zasobów**, wybierz pozycję Utwórz nowy, a następnie podaj nazwę małe grupy zasobów. **firmy Wingtip-sa -\<venueName\>-\<użytkownika\>**  jest zalecany wzorzec.  Aby uzyskać \<venueName\>, zastąp nazwą miejsca, bez spacji. Aby uzyskać \<użytkownika\>, zastąp wartość użytkownika poniżej.  W ramach tego wzorca nazwy grup zasobów mogą być *wingtip-sa-contosoconcerthall-af1*, *wingtip-sa-dogwooddojo-af1*, *wingtip-sa-fabrikamjazzclub-af1*.
    * Wybierz **lokalizacji** z listy rozwijanej.

    * Dla **użytkownika** — firma Microsoft zaleca wartość krótki użytkownika, takie jak inicjały i cyfrą: na przykład *af1*.


3. **Wdróż aplikację**.

    * Kliknij, aby zaakceptować warunki i postanowienia.
    * Kliknij pozycję **Kup**.

4. Monitorowanie stanu wszystkich trzech wdrożeń, klikając **powiadomienia** (ikona dzwonka z prawej strony pola wyszukiwania). Wdrażanie aplikacji trwa około pięciu minut.


## <a name="run-the-applications"></a>Uruchamianie aplikacji

Aplikacja prezentuje miejsca, które obsługują zdarzenia.  Lokalizacje są dzierżaw w aplikacji. Każde miejsce otrzymuje spersonalizowane witryny sieci web, aby wyświetlić listę ich zdarzenia i sprzedawać bilety. Typów miejsc obejmują koncertowe, kluby jazz i kluby sportowe. W tym przykładzie typ właściwości określa fotografii tła wyświetlane w witrynie sieci web miejsca.   W modelu aplikacji autonomicznej każde miejsce znajduje się wystąpienie innej aplikacji, za pomocą własnej autonomicznej bazy danych SQL.

1. Otwórz stronę wydarzenia dla każdego z trzech dzierżaw w osobnych kartach przeglądarki:

    - http://events.contosoconcerthall.&lt; użytkownika&gt;. trafficmanager.net
    - http://events.dogwooddojo.&lt; użytkownika&gt;. trafficmanager.net
    - http://events.fabrikamjazzclub.&lt; użytkownika&gt;. trafficmanager.net

    (W przypadku każdego adresu URL, Zastąp &lt;użytkownika&gt; wartością użytkownika wdrożenia.)

   ![Zdarzenia](./media/saas-standaloneapp-get-started-deploy/fabrikam.png)

Aby kontrolować dystrybucję żądań przychodzących, ta aplikacja używa [ *usługi Azure Traffic Manager*](../traffic-manager/traffic-manager-overview.md). Każde wystąpienie aplikacji specyficznym dla dzierżawy zawiera nazwę dzierżawy jako część nazwy domeny w adresie URL. Wszystkie dzierżawy adresów URL zawierają **użytkownika** wartość. Adresy URL należy wykonać następujący format:
- http://events.&lt; venuename&gt;.&lt; Użytkownik&gt;. trafficmanager.net

Bazy danych z każdej dzierżawy **lokalizacji** znajduje się w ustawieniach aplikacji odpowiednie wdrożonej aplikacji.

W środowisku produkcyjnym zwykle możesz utworzyć rekordu CNAME DNS, aby [ *punktu firmowej domeny internetowej* ](../traffic-manager/traffic-manager-point-internet-domain.md) adres URL profilu usługi traffic manager.


## <a name="explore-the-servers-and-tenant-databases"></a>Zapoznaj się z serwerów i baz danych dzierżaw

Spójrzmy na niektóre zasoby, które zostały wdrożone:

1. W [witryny Azure portal](http://portal.azure.com), przejdź do listy grup zasobów.
2. Powinien zostać wyświetlony dzierżawy trzy grupy zasobów.
3. Otwórz **wingtip-sa-fabrikam -&lt;użytkownika&gt;**  grupy zasobów, która zawiera zasoby do wdrożenia Fabrikam Jazz Club.  **Fabrikamjazzclub -&lt;użytkownika&gt;**  serwer zawiera **fabrikamjazzclub** bazy danych.

Każda baza danych dzierżawy jest 50 jednostek DTU *autonomiczny* bazy danych.

## <a name="additional-resources"></a>Zasoby dodatkowe

<!--
* Additional [tutorials that build on the Wingtip SaaS application](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* To learn about elastic pools, see [*What is an Azure SQL elastic pool*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool)
* To learn about elastic jobs, see [*Managing scaled-out cloud databases*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-jobs-overview)
-->

- Aby dowiedzieć się więcej o wielodostępnych aplikacji SaaS, zobacz [wzorce projektowe dla wielodostępnych aplikacji SaaS](saas-tenancy-app-design-patterns.md).

 
## <a name="delete-resource-groups-to-stop-billing"></a>Usuwanie grup zasobów, aby zatrzymać Naliczanie opłaty ##

Po zakończeniu, korzystając z przykładu, należy usunąć wszystkie grupy zasobów został utworzony, aby zatrzymać Naliczanie opłat za skojarzone.

## <a name="next-steps"></a>Kolejne kroki

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Jak wdrożyć aplikację Wingtip Tickets SaaS autonomicznych.
> * Temat serwerów i baz danych, które składają się na aplikację.
> * Jak usunąć przykładowych zasobów w celu zakończenia ich rozliczania.

Następnie spróbuj [Provision and Catalog](saas-standaloneapp-provision-and-catalog.md) samouczek, w którym przedstawimy korzystanie z katalogu dzierżawy, który umożliwia szeroką gamę scenariuszy międzydzierżawowa, takich jak schemat, zarządzaniem i dzierżawami analizy.
 

