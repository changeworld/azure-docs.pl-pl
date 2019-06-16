---
title: Dzienniki usługi Azure Monitor za pomocą aplikacji wielodostępnej bazie danych SQL | Dokumentacja firmy Microsoft
description: Konfigurowanie i używanie usługi Azure Monitor dzienniki wielodostępnych aplikacji SaaS usługi Azure SQL Database
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
ms.date: 01/25/2019
ms.openlocfilehash: 6380488faa9a4554df5df5ea67e11dbeb8853fff
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61390547"
---
# <a name="set-up-and-use-azure-monitor-logs-with-a-multitenant-sql-database-saas-app"></a>Konfigurowanie i używanie usługi Azure Monitor dzienniki wielodostępnych aplikacji SaaS usługi SQL Database

W tym samouczku, konfigurowania i używania [dzienniki usługi Azure Monitor](/azure/log-analytics/log-analytics-overview) monitorowania elastycznych pul i baz danych. Ten samouczek opiera się [samouczka zarządzania i monitorowania wydajności](saas-dbpertenant-performance-monitoring.md). Pokazuje, jak rozszerzyć monitorowanie za pomocą dzienników usługi Azure Monitor i zgłaszania alertów podane w witrynie Azure portal. Usługa Azure Monitor rejestruje obsługuje tysiące elastycznych pul i setki tysięcy baz danych monitorowania. Dzienniki platformy Azure Monitor udostępnia jedno rozwiązanie do monitorowania, które można zintegrować monitorowanie różnych aplikacji i usług platformy Azure w wielu subskrypcjach Azure.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Zainstaluj i skonfiguruj dzienniki usługi Azure Monitor.
> * Dzienniki użycia usługi Azure Monitor do monitorowania pul i baz danych.

Do wykonania zadań opisanych w tym samouczku niezbędne jest spełnienie następujących wymagań wstępnych:

* Aplikacja SaaS o nazwie Wingtip Tickets bazy danych dla dzierżawcy jest wdrożona. Aby wdrożyć w mniej niż pięć minut, zobacz [wdrażanie i eksplorowanie aplikacji SaaS o nazwie Wingtip Tickets bazy danych dla dzierżawcy](saas-dbpertenant-get-started-deploy.md).
* Zainstalowany jest program Azure PowerShell. Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z programem Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

Zobacz [samouczka zarządzania i monitorowania wydajności](saas-dbpertenant-performance-monitoring.md) dyskusję na temat scenariuszy SaaS oraz wzorców i ich wpływ na wymagania dotyczące monitorowania.

## <a name="monitor-and-manage-database-and-elastic-pool-performance-with-azure-monitor-logs"></a>Monitorowanie wydajności i zarządzanie nią bazy danych i elastycznej puli przy użyciu dzienników usługi Azure Monitor

Usługi Azure SQL Database monitorowania i zgłaszania alertów jest dostępna dla baz danych i pul w witrynie Azure portal. To wbudowane funkcje monitorowania i zgłaszania alertów jest wygodne, ale jest również specyficznych dla zasobów. Oznacza to, że mniej dobrze nadaje się do monitorowania dużych instalacji lub zapewnić spójny widok wielu zasobach i subskrypcjach.

W przypadku dużej liczby scenariuszy dzienniki usługi Azure Monitor służy do monitorowania i alertów. Usługa Azure Monitor to osobna usługa Azure umożliwia analizę przez dzienniki diagnostyczne i dane telemetryczne, które są zbierane w obszarze roboczym z potencjalnie wielu usług. Dzienniki platformy Azure Monitor udostępnia wbudowaną kwerendę, języka i danych narzędzia wizualizacji, umożliwiające analizę danych operacyjnych. Rozwiązanie SQL Analytics zawiera kilka wstępnie zdefiniowanych elastycznej puli i bazy danych monitorowania i zgłaszania alertów zapytań i widoków. Dzienniki platformy Azure Monitor udostępnia także projektanta widoków niestandardowych.

Obszary robocze OMS są teraz nazywane obszarami roboczymi usługi Log Analytics. Otwórz dziennik analizy obszarów roboczych i rozwiązania analityczne w witrynie Azure portal. Witryny Azure portal jest nowszym rodzajem punktu dostępu, ale może być, co znajduje się za portalu Operations Management Suite, w niektórych obszarach.

### <a name="create-performance-diagnostic-data-by-simulating-a-workload-on-your-tenants"></a>Tworzenie wydajnością danych diagnostycznych symulując obciążenie dzierżawców 

1. Otwórz w programie PowerShell ISE *... \\WingtipTicketsSaaS MultiTenantDb wzorca\\Learning Modules\\zarządzania i monitorowania wydajności\\Demo-PerformanceMonitoringAndManagement.ps1*. Nie zamykaj tego skryptu, ponieważ możesz chcieć uruchomić kilka obciążenia scenariuszy generowania podczas tego samouczka.
1. Jeśli nie zostało to zrobione już, aprowizowanie partii dzierżaw się bardziej interesująca monitorowania kontekstu. Ten proces trwa kilka minut.

   a. Ustaw **$DemoScenario = 1**, _Aprowizowanie partii dzierżaw_.

   b. Aby uruchomić skrypt i wdrożyć dodatkowe 17 dzierżaw, naciśnij klawisz F5.

1. Teraz uruchom generator obciążenia, aby uruchomić symulowane obciążenia na wszystkich dzierżaw.

    a. Ustaw **$DemoScenario = 2**, _obciążenia o normalnym natężeniu Generuj (około 30 jednostek DTU)_ .

    b. Aby uruchomić skrypt, naciśnij klawisz F5.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Pobierz skrypty bazy danych dla dzierżawcy aplikacji SaaS o nazwie Wingtip Tickets

SaaS o nazwie Wingtip Tickets wielodostępnej bazie danych skrypty i kod źródłowy aplikacji są dostępne w [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) repozytorium GitHub. Aby uzyskać instrukcje dotyczące pobierania i odblokować skryptów aplikacji Wingtip Tickets PowerShell, zobacz [ogólne wskazówki dotyczące](saas-tenancy-wingtip-app-guidance-tips.md).

## <a name="install-and-configure-log-analytics-workspace-and-the-azure-sql-analytics-solution"></a>Instalowanie i konfigurowanie obszaru roboczego usługi Log Analytics i rozwiązania Azure SQL Analytics

Usługa Azure Monitor jest oddzielną usługą, która musi być skonfigurowana. Usługa Azure Monitor rejestruje gromadzi dane dziennika, dane telemetryczne i metryki w obszarze roboczym usługi Log Analytics. Podobnie jak inne zasoby na platformie Azure można utworzyć obszar roboczy usługi Log Analytics. Obszar roboczy nie muszą zostać utworzone w tej samej grupie zasobów aplikacji, którą monitoruje. Wykonując tak często najodpowiedniejszy jednak. Dla aplikacji Wingtip Tickets umożliwia upewnij się, że obszar roboczy zostanie usunięty z aplikacją pojedynczą grupę zasobów.

1. Otwórz w programie PowerShell ISE *... \\WingtipTicketsSaaS MultiTenantDb wzorca\\Learning Modules\\zarządzania i monitorowania wydajności\\Log Analytics\\Demo-LogAnalytics.ps1*.
1. Aby uruchomić skrypt, naciśnij klawisz F5.

Teraz można otworzyć usługi Azure Monitor rejestruje się w witrynie Azure portal. Trwa kilka minut, aby zbierać dane telemetryczne w obszarze roboczym usługi Log Analytics i aby była widoczna. Pozostanie im dłużej system będzie zbierał danych diagnostycznych, tym bardziej interesujące środowisko działa. 

## <a name="use-log-analytics-workspace-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>Użyj obszaru roboczego usługi Log Analytics i rozwiązania SQL Analytics do monitorowana pul i baz danych


W tym ćwiczeniu Otwórz obszar roboczy Log Analytics w witrynie Azure portal, aby wyświetlić dane telemetryczne zebrane dla baz danych i pul.

1. Przejdź do witryny [Azure Portal](https://portal.azure.com). Wybierz **wszystkich usług** aby otworzyć obszar roboczy usługi Log Analytics. Następnie wyszukiwania usługi Log Analytics.

   ![Otwórz obszar roboczy usługi Log Analytics](media/saas-dbpertenant-log-analytics/log-analytics-open.png)

1. Wybierz obszar roboczy o nazwie _wtploganalytics -&lt;użytkownika&gt;_ .

1. Wybierz **Przegląd** można otworzyć rozwiązania log analytics w witrynie Azure portal.

   ![Omówienie](media/saas-dbpertenant-log-analytics/click-overview.png)

    > [!IMPORTANT]
    > Może upłynąć kilka minut, zanim rozwiązanie będzie aktywny. 

1. Wybierz **usługi Azure SQL Analytics** Kafelek, aby go otworzyć.

    ![Kafelek przeglądu](media/saas-dbpertenant-log-analytics/overview.png)

1. Widoki w rozwiązaniu bok, przewiń przy użyciu ich własnych paska przewijania wewnętrzny u dołu. Jeśli to konieczne, należy odświeżyć stronę.

1. Aby zapoznać się na stronie podsumowania, wybierz Kafelki lub poszczególnych baz danych, aby otworzyć Eksplorator szczegółów.

    ![Pulpit nawigacyjny analizy dzienników](media/saas-dbpertenant-log-analytics/log-analytics-overview.png)

1. Zmień ustawienia filtru, aby zmodyfikować przedział czasu. Na potrzeby tego samouczka wybierz **ostatniej 1 godziny**.

    ![Filtr czasu](media/saas-dbpertenant-log-analytics/log-analytics-time-filter.png)

1. Wybierz poszczególne bazy danych, aby zapoznać się z użycia zapytania i metryki dla tej bazy danych.

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

    ![Obszar roboczy usługi log Analytics](media/saas-dbpertenant-log-analytics/log-analytics-workspace-oms-portal.png)

W obszarze roboczym usługi Log Analytics możesz eksplorować dalsze dane dzienników i metryk. 

Monitorowania i alertów w usłudze Azure Monitor dzienniki są oparte na zapytaniach względem danych w obszarze roboczym, inaczej niż w przypadku alertów zdefiniowane dla każdego zasobu w witrynie Azure portal. Użycie alerty dla zapytań, można zdefiniować pojedynczy alert, który wygląda za pośrednictwem wszystkich baz danych, a nie definiujące po jednym dla każdej bazy danych. Zapytania są ograniczone tylko przez dostępnych danych w obszarze roboczym.

Aby uzyskać więcej informacji na temat tworzenia zapytań i ustawiania alertów za pomocą dzienników usługi Azure Monitor, zobacz [dzienniki Praca z regułami alertów w usłudze Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts-creating).

Dzienniki usługi Azure Monitor na opłaty za bazy danych SQL Database oparty na wolumenie danych w obszarze roboczym. W tym samouczku utworzono bezpłatny obszar roboczy, który jest ograniczony do 500 MB dziennie. Po osiągnięciu tego limitu dane przestaną być dodawane do obszaru roboczego.


## <a name="next-steps"></a>Kolejne kroki

W tym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Zainstaluj i skonfiguruj dzienniki usługi Azure Monitor.
> * Dzienniki użycia usługi Azure Monitor do monitorowania pul i baz danych.

Spróbuj [samouczek analiz dzierżaw](saas-dbpertenant-log-analytics.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Dodatkowe samouczki, które są kompilowane w na początkowym wdrożeniu bazy danych dla dzierżawcy aplikacji SaaS o nazwie Wingtip Tickets](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Dzienniki usługi Azure Monitor](../azure-monitor/insights/azure-sql.md)
