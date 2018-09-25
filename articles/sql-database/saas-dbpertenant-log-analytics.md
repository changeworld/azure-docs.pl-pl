---
title: Używanie programu Log Analytics za pomocą aplikacji wielodostępnych bazy danych SQL Database | Dokumentacja firmy Microsoft
description: Konfigurowanie i używanie programu Log Analytics za pomocą wielodostępnych aplikacji SaaS usługi Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: 60139915e8d8dca382f4ef62b5129f1a84e7e80d
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47056713"
---
# <a name="set-up-and-use-log-analytics-with-a-multitenant-sql-database-saas-app"></a>Konfigurowanie i używanie programu Log Analytics za pomocą wielodostępnych aplikacji SaaS usługi SQL Database

W tym samouczku, konfigurowanie i korzystanie z systemu Azure [usługi Log Analytics](/azure/log-analytics/log-analytics-overview) monitorowania elastycznych pul i baz danych. Ten samouczek opiera się [samouczka zarządzania i monitorowania wydajności](saas-dbpertenant-performance-monitoring.md). Pokazuje, jak używać usługi Log Analytics można rozszerzyć monitorowanie i alerty podane w witrynie Azure portal. Usługa log Analytics obsługuje monitorowania tysięcy elastycznych pul i setki tysięcy baz danych. Log Analytics udostępnia jedno rozwiązanie do monitorowania, które można zintegrować monitorowanie różnych aplikacji i usług platformy Azure w wielu subskrypcjach Azure.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Instalowanie i konfigurowanie usługi Log Analytics.
> * Używanie programu Log Analytics do monitorowana pul i baz danych.

Do wykonania zadań opisanych w tym samouczku niezbędne jest spełnienie następujących wymagań wstępnych:

* Aplikacja SaaS o nazwie Wingtip Tickets bazy danych dla dzierżawcy jest wdrożona. Aby wdrożyć w mniej niż pięć minut, zobacz [wdrażanie i eksplorowanie aplikacji SaaS o nazwie Wingtip Tickets bazy danych dla dzierżawcy](saas-dbpertenant-get-started-deploy.md).
* Zainstalowany jest program Azure PowerShell. Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z programem Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

Zobacz [samouczka zarządzania i monitorowania wydajności](saas-dbpertenant-performance-monitoring.md) dyskusję na temat scenariuszy SaaS oraz wzorców i ich wpływ na wymagania dotyczące monitorowania.

## <a name="monitor-and-manage-database-and-elastic-pool-performance-with-log-analytics"></a>Monitorowanie wydajności i zarządzanie nią bazy danych i elastycznej puli z usługą Log Analytics

Usługi Azure SQL Database monitorowania i zgłaszania alertów jest dostępna dla baz danych i pul w witrynie Azure portal. To wbudowane funkcje monitorowania i zgłaszania alertów jest wygodne, ale jest również specyficznych dla zasobów. Oznacza to, że mniej dobrze nadaje się do monitorowania dużych instalacji lub zapewnić spójny widok wielu zasobach i subskrypcjach.

W przypadku dużej liczby scenariuszy usługi Log Analytics służy do monitorowania i alertów. Usługa log Analytics to osobna usługa Azure umożliwia analizę przez dzienniki diagnostyczne i dane telemetryczne, które są zbierane w obszarze roboczym z potencjalnie wielu usług. Usługa log Analytics oferuje wbudowaną kwerendę, języka i danych narzędzia wizualizacji, umożliwiające analizę danych operacyjnych. Rozwiązanie SQL Analytics zawiera kilka wstępnie zdefiniowanych elastycznej puli i bazy danych monitorowania i zgłaszania alertów zapytań i widoków. Log Analytics udostępnia także projektanta widoków niestandardowych.

Log Analytics obszarów roboczych i rozwiązań analitycznych, Otwórz w witrynie Azure portal i w pakiecie Operations Management Suite. Witryny Azure portal jest nowszym rodzajem punktu dostępu, ale może znajdować się pod portalu Operations Management Suite, w niektórych obszarach.

### <a name="create-performance-diagnostic-data-by-simulating-a-workload-on-your-tenants"></a>Tworzenie wydajnością danych diagnostycznych symulując obciążenie dzierżawców 

1. Otwórz w programie PowerShell ISE *... \\WingtipTicketsSaaS MultiTenantDb wzorca\\Learning Modules\\zarządzania i monitorowania wydajności\\Demo-PerformanceMonitoringAndManagement.ps1*. Nie zamykaj tego skryptu, ponieważ możesz chcieć uruchomić kilka obciążenia scenariuszy generowania podczas tego samouczka.
1. Jeśli nie zostało to zrobione już, aprowizowanie partii dzierżaw się bardziej interesująca monitorowania kontekstu. Ten proces trwa kilka minut.

   a. Ustaw **$DemoScenario = 1**, _Aprowizowanie partii dzierżaw_.

   b. Aby uruchomić skrypt i wdrożyć dodatkowe 17 dzierżaw, naciśnij klawisz F5.

1. Teraz uruchom generator obciążenia, aby uruchomić symulowane obciążenia na wszystkich dzierżaw.

    a. Ustaw **$DemoScenario = 2**, _obciążenia o normalnym natężeniu Generuj (o 30 jednostek DTU)_.

    b. Aby uruchomić skrypt, naciśnij klawisz F5.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Pobierz skrypty bazy danych dla dzierżawcy aplikacji SaaS o nazwie Wingtip Tickets

SaaS o nazwie Wingtip Tickets wielodostępnej bazie danych skrypty i kod źródłowy aplikacji są dostępne w [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) repozytorium GitHub. Aby uzyskać instrukcje dotyczące pobierania i odblokować skryptów aplikacji Wingtip Tickets PowerShell, zobacz [ogólne wskazówki dotyczące](saas-tenancy-wingtip-app-guidance-tips.md).

## <a name="install-and-configure-log-analytics-and-the-azure-sql-analytics-solution"></a>Instalowanie i konfigurowanie usługi Log Analytics i rozwiązania Azure SQL Analytics

Log Analytics jest oddzielną usługą, którą musi być skonfigurowany. Usługa log Analytics służy do zbierania danych dzienników, dane telemetryczne i metryki w obszarze roboczym usługi Log Analytics. Podobnie jak inne zasoby na platformie Azure można utworzyć obszar roboczy usługi Log Analytics. Obszar roboczy nie muszą zostać utworzone w tej samej grupie zasobów aplikacji, którą monitoruje. Wykonując tak często najodpowiedniejszy jednak. Dla aplikacji Wingtip Tickets umożliwia upewnij się, że obszar roboczy zostanie usunięty z aplikacją pojedynczą grupę zasobów.

1. Otwórz w programie PowerShell ISE *... \\WingtipTicketsSaaS MultiTenantDb wzorca\\Learning Modules\\zarządzania i monitorowania wydajności\\Log Analytics\\Demo-LogAnalytics.ps1*.
1. Aby uruchomić skrypt, naciśnij klawisz F5.

Teraz możesz otworzyć usługi Log Analytics w witrynie Azure portal lub portalu Operations Management Suite. Trwa kilka minut, aby zbierać dane telemetryczne w obszarze roboczym usługi Log Analytics i aby była widoczna. Pozostanie im dłużej system będzie zbierał danych diagnostycznych, tym bardziej interesujące środowisko działa. 

## <a name="use-log-analytics-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>Używanie programu Log Analytics i rozwiązania SQL Analytics do monitorowana pul i baz danych


W tym ćwiczeniu Otwórz Log Analytics i portalu Operations Management Suite, aby wyświetlić dane telemetryczne zebrane dla baz danych i pul.

1. Przejdź do witryny [Azure Portal](https://portal.azure.com). Wybierz **wszystkich usług** można otworzyć usługi Log Analytics. Następnie wyszukaj usługi Log Analytics.

   ![Otwórz dziennik analizy](media/saas-dbpertenant-log-analytics/log-analytics-open.png)

1. Wybierz obszar roboczy o nazwie _wtploganalytics -&lt;użytkownika&gt;_.

1. Wybierz opcję **Przegląd**, aby otworzyć rozwiązanie Log Analytics w witrynie Azure Portal.

   ![Przegląd](media/saas-dbpertenant-log-analytics/click-overview.png)

    > [!IMPORTANT]
    > Może upłynąć kilka minut, zanim rozwiązanie będzie aktywny. 

1. Wybierz **usługi Azure SQL Analytics** Kafelek, aby go otworzyć.

    ![Kafelek przeglądu](media/saas-dbpertenant-log-analytics/overview.png)

1. Widoki w rozwiązaniu bok, przewiń przy użyciu ich własnych paska przewijania wewnętrzny u dołu. Jeśli to konieczne, należy odświeżyć stronę.

1. Aby zapoznać się na stronie podsumowania, wybierz Kafelki lub poszczególnych baz danych, aby otworzyć Eksplorator szczegółów.

    ![Pulpit nawigacyjny analizy dzienników](media/saas-dbpertenant-log-analytics/log-analytics-overview.png)

1. Zmień ustawienia filtru, aby zmodyfikować przedział czasu. Na potrzeby tego samouczka wybierz **ostatniej 1 godziny**.

    ![Filtr czasu](media/saas-dbpertenant-log-analytics/log-analytics-time-filter.png)

1. Wybierz pojedynczą bazę danych, aby zapoznać się z użycia zapytania i metryki dla tej bazy danych.

    ![Analiza bazy danych](media/saas-dbpertenant-log-analytics/log-analytics-database.png)

1. Aby wyświetlić metryki użycia, przewiń stronę analizy, aby po prawej stronie.
 
     ![Metryki bazy danych](media/saas-dbpertenant-log-analytics/log-analytics-database-metrics.png)

1. Przewiń stronę analizy po lewej stronie i wybierz Kafelek serwera w **informacje o zasobie** listy.  

    ![Lista informacji zasobów](media/saas-dbpertenant-log-analytics/log-analytics-resource-info.png)

    Zostanie otwarta strona, która pokazuje pul i baz danych na serwerze.

    ![Serwer z pul i baz danych](media/saas-dbpertenant-log-analytics/log-analytics-server.png)

1. Wybierz pulę. Na stronie puli przewiń w prawo, aby wyświetlić metryki puli. 

    ![Metryki puli](media/saas-dbpertenant-log-analytics/log-analytics-pool-metrics.png)


1. W obszarze roboczym usługi Log Analytics wybierz **portalu pakietu OMS** celu otwórz obszar roboczy istnieje.

    ![Operations Management Suite Portal kafelka](media/saas-dbpertenant-log-analytics/log-analytics-workspace-oms-portal.png)

W portalu Operations Management Suite możesz eksplorować dane dzienników i metryk, w obszarze roboczym dalej. 

Monitorowanie i alerty w usłudze Log Analytics są oparte na zapytania względem danych w obszarze roboczym, inaczej niż w przypadku alertów zdefiniowane dla każdego zasobu w witrynie Azure portal. Użycie alerty dla zapytań, można zdefiniować pojedynczy alert, który wygląda za pośrednictwem wszystkich baz danych, a nie definiujące po jednym dla każdej bazy danych. Zapytania są ograniczone tylko przez dostępnych danych w obszarze roboczym.

Aby uzyskać więcej informacji na temat korzystania z usługi Log Analytics do tworzenia zapytań i ustawiania alertów, zobacz [Praca z regułami alertów w usłudze Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts-creating).

Usługa log Analytics dla bazy danych SQL Database opłaty zgodnie z ilością danych w obszarze roboczym. W tym samouczku utworzono bezpłatny obszar roboczy, który jest ograniczony do 500 MB dziennie. Po osiągnięciu tego limitu dane przestaną być dodawane do obszaru roboczego.


## <a name="next-steps"></a>Kolejne kroki

W tym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Instalowanie i konfigurowanie usługi Log Analytics.
> * Używanie programu Log Analytics do monitorowana pul i baz danych.

Spróbuj [samouczek analiz dzierżaw](saas-dbpertenant-log-analytics.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Dodatkowe samouczki, które są kompilowane w na początkowym wdrożeniu bazy danych dla dzierżawcy aplikacji SaaS o nazwie Wingtip Tickets](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Program Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md)
