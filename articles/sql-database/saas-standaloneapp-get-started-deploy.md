---
title: Samouczek SaaS wielodostępne — baza danych SQL Azure | Dokumentacja firmy Microsoft
description: Wdrażanie i Eksploruj autonomicznej aplikacji SaaS pojedynczej dzierżawy, który używa bazy danych SQL Azure.
keywords: samouczek usługi sql database
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: genemi
ms.openlocfilehash: 86a5bc31639cbbcdac1468f3bc2e35a547068882
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="deploy-and-explore-a-standalone-single-tenant-application-that-uses-azure-sql-database"></a>Wdrażanie i Eksploruj aplikacja pojedynczej dzierżawy autonomiczna, która używa bazy danych SQL Azure

W tym samouczku wdrażanie i Eksploruj przykładowej aplikacji SaaS biletów Wingtip utworzony przy użyciu wzorca aplikacja autonomiczna lub aplikacji na dzierżawy.  Aplikacja jest przeznaczona do uwzględnienia funkcji bazy danych SQL Azure upraszczające włączania scenariusze SaaS wielodostępnej.

Aplikacja autonomiczna lub wzorca aplikacji na dzierżawy wdraża wystąpienia aplikacji dla każdego dzierżawcy.  Każda aplikacja skonfigurowanym dla określonych dzierżawcy i wdrożenia w grupie oddzielnych zasobów platformy Azure. Aby zapewnić rozwiązanie dla wielu dzierżawców za wiele wystąpień aplikacji. Ten wzorzec jest najlepiej nadaje się do mniejsze liczby dzierżawców, gdzie izolacji dzierżawy ma najwyższy priorytet. Platforma Azure ma partnera programy, które umożliwia zasobów wdrażanych w subskrypcji dzierżawcy i zarządzane przez dostawcę usług w imieniu dzierżawcy. 

W tym samouczku zostanie wdrożona trzy aplikacje autonomiczne dla trzech dzierżawców w ramach subskrypcji platformy Azure.  Masz pełny dostęp do eksplorowania i pracować z poszczególnych składników aplikacji.

Skrypty zarządzania i kodu źródłowego aplikacji są dostępne w [WingtipTicketsSaaS StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp) repozytorium GitHub.


Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Jak wdrożyć aplikacji autonomicznej Wingtip biletów SaaS.
> * Gdzie można pobrać kodu źródłowego aplikacji i skrypty zarządzania.
> * Temat serwerów i baz danych, które tworzą aplikacji.

Dodatkowe samouczki będzie publikowana. Umożliwi ich Eksploruj zakresu scenariuszy zarządzania na podstawie wzorca tej aplikacji.   

## <a name="deploy-the-wingtip-tickets-saas-standalone-application"></a>Wdrażanie aplikacji SaaS autonomiczny biletów Wingtip

Wdrażanie aplikacji dla trzech dzierżaw podana:

1. Kliknij każdy blue **wdrażanie na platformie Azure** przycisk, aby otworzyć szablon wdrożenia w [portalu Azure](https://portal.azure.com). Każdy szablon wymaga dwóch wartości parametru; Nazwa nowej grupy zasobów i nazwę użytkownika, która odróżnia to wdrożenie od innych wdrożeń aplikacji. Następnym krokiem szczegółowe informacje dotyczące ustawiania tych wartości.<br><br>
    <a href="http://aka.ms/deploywingtipsa-contoso" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp; **Hall porozumieniu firmy Contoso**
<br><br>
    <a href="http://aka.ms/deploywingtipsa-dogwood" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp; **Dojo dereń**
<br><br>
    <a href="http://aka.ms/deploywingtipsa-fabrikam" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp; **Fabrikam Jazz Club**

2. Wprowadź wartości wymaganego parametru dla każdego wdrożenia.

    > [!IMPORTANT]
    > Niektóre zapory uwierzytelniania i serwera są celowo niezabezpieczona w celach demonstracyjnych. **Utwórz nową grupę zasobów** dla każdego wdrożenia aplikacji.  Nie należy używać istniejącej grupy zasobów. Nie należy używać tej aplikacji lub wszystkie zasoby, które tworzy, w środowisku produkcyjnym. Po zakończeniu pracy z aplikacjami, aby zatrzymać rozliczeń powiązanego, należy usunąć wszystkie grupy zasobów.

    Najlepiej użyć tylko małe litery, cyfry i łączniki w nazwach zasobów.
    * Aby uzyskać **grupy zasobów**, wybierz opcję Utwórz nowy, a następnie podaj nazwę małe dla grupy zasobów. **Wingtip-sa -\<venueName\>-\<użytkownika\>**  jest zalecane wzorca.  Aby uzyskać \<venueName\>, zastąp nazwę miejscową nie może zawierać spacji. Aby uzyskać \<użytkownika\>, zastąp wartość użytkownika poniżej.  Z tego wzorca nazwy grup zasobów mogą być *wingtip-sa-contosoconcerthall-af1*, *wingtip-sa-dogwooddojo-af1*, *wingtip-sa-fabrikamjazzclub-af1*.
    * Wybierz **lokalizacji** z listy rozwijanej.

    * Dla **użytkownika** — zaleca się wartość krótkich użytkownika, na przykład z Twoimi inicjałami plus cyfrę: na przykład *af1*.


3. **Wdróż aplikację**.

    * Kliknij, aby zaakceptować warunki i postanowienia.
    * Kliknij pozycję **Kup**.

4. Monitorowanie stanu wszystkich trzech wdrożeń, klikając **powiadomienia** (ikonę dzwonka z prawej strony pola wyszukiwania). Wdrażanie aplikacji trwa około pięciu minut.


## <a name="run-the-applications"></a>Uruchamianie aplikacji

Miejsc, które hostują zdarzeń aplikacji, których są wyświetlane.  Miejsc są dzierżawcami tej aplikacji. Każda właściwość pobiera spersonalizowane witryny sieci web, aby wyświetlić ich zdarzenia i sprzedawać bilety. Właściwość typy koncertowe, jazz klubów i sportowych. W przykładzie typ miejscową określa fotografii tła wyświetlany w witrynie sieci web miejsce.   W modelu aplikacji autonomicznej każda właściwość ma oddzielne aplikację z własną autonomiczna baza danych SQL.

1. Otwórz stronę zdarzeń dla każdego z trzech dzierżaw w osobnych kartach przeglądarki.

    - http://events.contosoconcerthall.&lt;user&gt;.trafficmanager.net
    - http://events.dogwooddojo.&lt;user&gt;.trafficmanager.net
    - http://events.fabrikamjazzclub.&lt;user&gt;.trafficmanager.net

    (W przypadku każdego adresu URL, Zastąp &lt;użytkownika&gt; o wartości użytkownika w danym wdrożeniu.)

   ![Zdarzenia](./media/saas-standaloneapp-get-started-deploy/fabrikam.png)

Aby kontrolować dystrybucję żądań przychodzących, aplikacja używa [ *usługi Azure Traffic Manager*](../traffic-manager/traffic-manager-overview.md). Każde wystąpienie aplikacji specyficznego dla dzierżawy zawiera nazwę dzierżawy jako część nazwy domeny w adresie URL. Wszystkie dzierżawy adresów URL obejmują konkretnej **użytkownika** wartość. Adresy URL wykonaj następujący format:
- http://events.&lt;venuename&gt;.&lt;user&gt;.trafficmanager.net

Bazy danych z każdej dzierżawy **lokalizacji** znajduje się w ustawieniach aplikacji w odpowiednich wdrożonej aplikacji.

W środowisku produkcyjnym zwykle należy utworzyć rekord CNAME DNS, aby [ *punktu firmowej domeny internetowej* ](../traffic-manager/traffic-manager-point-internet-domain.md) na adres URL profilu Menedżera ruchu.


## <a name="explore-the-servers-and-tenant-databases"></a>Eksploruj serwerów i baz danych dzierżawy

Oto niektóre zasoby, które zostały wdrożone:

1. W [portalu Azure](http://portal.azure.com), przejdź do listy grup zasobów.
2. Powinna zostać wyświetlona dzierżawy trzech grup zasobów.
3. Otwórz **wingtip-sa-fabrikam -&lt;użytkownika&gt;**  grupy zasobów, która zawiera zasoby wdrożenia klub Jazz firmy Fabrikam.  **Fabrikamjazzclub -&lt;użytkownika&gt;**  serwer zawiera **fabrikamjazzclub** bazy danych.

Każda baza danych dzierżawy jest 50 DTU *autonomiczny* bazy danych.

## <a name="additional-resources"></a>Zasoby dodatkowe

<!--
* Additional [tutorials that build on the Wingtip SaaS application](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* To learn about elastic pools, see [*What is an Azure SQL elastic pool*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool)
* To learn about elastic jobs, see [*Managing scaled-out cloud databases*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-jobs-overview)
-->

- Aby dowiedzieć się więcej na temat wielodostępnych aplikacji SaaS, zobacz [wzorce dla aplikacji SaaS wielodostępne projektowe](saas-tenancy-app-design-patterns.md).

 
## <a name="delete-resource-groups-to-stop-billing"></a>Usuwanie grup zasobów, aby zatrzymać rozliczeń ##

Po zakończeniu użycia przykładowej należy usunąć wszystkie grupy zasobów został utworzony w celu zatrzymania skojarzone rozliczeń.

## <a name="next-steps"></a>Kolejne kroki

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Jak wdrożyć aplikacji autonomicznej Wingtip biletów SaaS.
> * Temat serwerów i baz danych, które tworzą aplikacji.
> * Jak usunąć przykładowych zasobów, aby zatrzymać rozliczeń powiązanego.

Następnie spróbuj [udostępniania i wykaz](saas-standaloneapp-provision-and-catalog.md) samouczek, w którym będzie zbadać użycie katalogu dzierżawcy, który umożliwia zakresu dzierżawy różnych scenariuszy, takich jak schematu analytics zarządzaniem i dzierżawami.
 

