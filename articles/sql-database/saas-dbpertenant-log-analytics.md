---
title: Dzienniki usługi Azure Monitor za pomocą aplikacji wielodostępnych
description: Konfigurowanie dzienników usługi Azure Monitor i używanie ich przy użyciu wielodostępnych aplikacji Azure SQL Database SaaS
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
ms.date: 01/25/2019
ms.openlocfilehash: 7429a9d5e9a803f0e9a6f900c5d81e77e7477a48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214501"
---
# <a name="set-up-and-use-azure-monitor-logs-with-a-multitenant-sql-database-saas-app"></a>Konfigurowanie dzienników usługi Azure Monitor i używanie ich przy użyciu aplikacji SaaS bazy danych SQL

W tym samouczku skonfigurować i używać [dzienników usługi Azure Monitor](/azure/log-analytics/log-analytics-overview) do monitorowania pul elastycznych i baz danych. Ten samouczek opiera się na [monitorowanie wydajności i zarządzania samouczek](saas-dbpertenant-performance-monitoring.md). Pokazano w nim, jak używać dzienników usługi Azure Monitor w celu rozszerzenia monitorowania i alertów udostępnianych w witrynie Azure portal. Dzienniki usługi Azure Monitor obsługują monitorowanie tysięcy elastycznych pul i setek tysięcy baz danych. Dzienniki usługi Azure Monitor zapewniają jedno rozwiązanie do monitorowania, które może integrować monitorowanie różnych aplikacji i usług platformy Azure w wielu subskrypcjach platformy Azure.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Instalowanie i konfigurowanie dzienników usługi Azure Monitor.
> * Dzienniki usługi Azure Monitor są używane do monitorowania pul i baz danych.

Do wykonania zadań opisanych w tym samouczku niezbędne jest spełnienie następujących wymagań wstępnych:

* Wdrożono aplikację bazy danych SaaS dla wingtip. Aby wdrożyć w mniej niż pięć minut, zobacz [Wdrażanie i eksplorowanie aplikacji bazy danych SaaS Wingtip na dzierżawę.](saas-dbpertenant-get-started-deploy.md)
* Zainstalowany jest program Azure PowerShell. Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z programem Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

Zobacz [samouczek monitorowania wydajności i zarządzania,](saas-dbpertenant-performance-monitoring.md) aby zapoznać się ze scenariuszami i wzorcami SaaS oraz jak wpływają one na wymagania dotyczące rozwiązania do monitorowania.

## <a name="monitor-and-manage-database-and-elastic-pool-performance-with-azure-monitor-logs"></a>Monitorowanie danych i wydajności puli elastycznej i zarządzanie nimi za pomocą dzienników usługi Azure Monitor

W przypadku usługi Azure SQL Database monitorowanie i alerty są dostępne w bazach danych i pulach w witrynie Azure portal. To wbudowane monitorowanie i alerty jest wygodne, ale jest również specyficzne dla zasobów. Oznacza to, że jest mniej odpowiedni do monitorowania dużych instalacji lub zapewniają ujednolicony widok w zasobach i subskrypcjach.

W przypadku scenariuszy dużych ilości można używać dzienników usługi Azure Monitor do monitorowania i alertów. Usługa Azure Monitor to oddzielna usługa platformy Azure, która umożliwia analizę dzienników zebranych w obszarze roboczym z potencjalnie wielu usług. Dzienniki usługi Azure Monitor udostępnia wbudowany język zapytań i narzędzia do wizualizacji danych, które umożliwiają analizę danych operacyjnych. Rozwiązanie SQL Analytics zapewnia kilka wstępnie zdefiniowanych elastycznej puli i monitorowania bazy danych oraz alertów widoków i zapytań. Dzienniki usługi Azure Monitor udostępnia również projektanta widoku niestandardowego.

Obszary robocze OMS są teraz nazywane obszarami roboczymi usługi Log Analytics. Obszary robocze usługi Log Analytics i rozwiązania analityczne są otwierane w witrynie Azure portal. Portal Azure jest nowszym punktem dostępu, ale może to być to, co znajduje się za portalem Pakietu Operations Management Suite w niektórych obszarach.

### <a name="create-performance-diagnostic-data-by-simulating-a-workload-on-your-tenants"></a>Tworzenie danych diagnostycznych wydajności przez symulowanie obciążenia dzierżawców 

1. W programie PowerShell ISE otwórz *.. WingtipTicketsSaaS-MultiTenantDb-master\\Uczenie\\modułów\\Monitorowanie wydajności i zarządzanie Demo-PerformanceMonitoringAndManagement.ps1 . \\* Zachowaj ten skrypt otwarty, ponieważ można uruchomić kilka scenariuszy generowania obciążenia w tym samouczku.
1. Jeśli jeszcze tego nie zrobiono, aprowizuj partię dzierżaw, aby uczynić kontekst monitorowania bardziej interesującym. Ten proces trwa kilka minut.

   a. Ustaw **$DemoScenario = 1**, _Aprowizuj partię dzierżawców_.

   b. Aby uruchomić skrypt i wdrożyć dodatkowe 17 dzierżaw, naciśnij klawisz F5.

1. Teraz uruchom generator obciążenia, aby uruchomić symulowane obciążenie dla wszystkich dzierżawców.

    a. Ustaw **$DemoScenario = 2**, _Generowanie normalnego obciążenia intensywnością (około 30 DTU)_.

    b. Aby uruchomić skrypt, naciśnij klawisz F5.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Pobierz skrypty aplikacji bazy danych SaaS dla bazy danych Wingtip na dzierżawcę

Skrypty wielodostępne bazy danych Wingtip Tickets SaaS i kod źródłowy aplikacji są dostępne w repozytorium [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub. Aby uzyskać kroki, aby pobrać i odblokować skrypty programu PowerShell Wingtip Tickets, zobacz [ogólne wskazówki](saas-tenancy-wingtip-app-guidance-tips.md).

## <a name="install-and-configure-log-analytics-workspace-and-the-azure-sql-analytics-solution"></a>Instalowanie i konfigurowanie obszaru roboczego usługi Log Analytics i rozwiązania usługi Azure SQL Analytics

Usługa Azure Monitor to oddzielna usługa, która musi być skonfigurowana. Dzienniki usługi Azure Monitor zbiera dane dziennika, dane telemetryczne i metryki w obszarze roboczym usługi Log Analytics. Podobnie jak inne zasoby na platformie Azure, należy utworzyć obszar roboczy usługi Log Analytics. Obszar roboczy nie musi być tworzony w tej samej grupie zasobów, co aplikacje, które monitoruje. Robi to często ma jednak największy sens. W przypadku aplikacji Bilety Wingtip użyj jednej grupy zasobów, aby upewnić się, że obszar roboczy został usunięty z aplikacją.

1. W programie PowerShell ISE otwórz *.. WingtipTicketsSaaS-MultiTenantDb-master\\Uczenie\\modułów\\Monitorowanie\\wydajności i zarządzanie Log Analytics Demo-LogAnalytics.ps1 . \\*
1. Aby uruchomić skrypt, naciśnij klawisz F5.

Teraz możesz otwierać dzienniki usługi Azure Monitor w witrynie Azure portal. Trwa kilka minut, aby zebrać dane telemetryczne w obszarze roboczym usługi Log Analytics i aby były widoczne. Im dłużej opuszczasz system zbierający dane diagnostyczne, tym bardziej interesujące jest to doświadczenie. 

## <a name="use-log-analytics-workspace-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>Monitorowanie pul i baz danych za pomocą obszaru roboczego usługi Log Analytics i rozwiązania SQL Analytics


W tym ćwiczeniu otwórz obszar roboczy usługi Log Analytics w witrynie Azure portal, aby przyjrzeć się danych telemetrycznych zebranych dla baz danych i pul.

1. Przejdź do [witryny Azure portal](https://portal.azure.com). Wybierz **wszystkie usługi,** aby otworzyć obszar roboczy usługi Log Analytics. Następnie wyszukaj analizę dziennika.

   ![Otwórz obszar roboczy analizy dzienników](media/saas-dbpertenant-log-analytics/log-analytics-open.png)

1. Wybierz obszar roboczy o nazwie _wtploganalytics-&lt;user&gt;_.

1. Wybierz **omówienie,** aby otworzyć rozwiązanie do analizy dzienników w witrynie Azure portal.

   ![Omówienie](media/saas-dbpertenant-log-analytics/click-overview.png)

    > [!IMPORTANT]
    > Może upłynąć kilka minut, zanim rozwiązanie będzie aktywne. 

1. Wybierz kafelek **usługi Azure SQL Analytics,** aby go otworzyć.

    ![Kafelek Przegląd](media/saas-dbpertenant-log-analytics/overview.png)

1. Widoki w rozwiązaniu przewijają się na boki, z własnym wewnętrznym paskiem przewijania u dołu. W razie potrzeby odśwież stronę.

1. Aby eksplorować stronę podsumowania, wybierz kafelki lub poszczególne bazy danych, aby otworzyć eksploratora przechodzenia do szczegółów.

    ![Pulpit nawigacyjny analizy dzienników](media/saas-dbpertenant-log-analytics/log-analytics-overview.png)

1. Zmień ustawienie filtru, aby zmodyfikować zakres czasu. W tym samouczku wybierz **opcję Ostatnie 1 godzinę**.

    ![Filtr czasu](media/saas-dbpertenant-log-analytics/log-analytics-time-filter.png)

1. Wybierz indywidualną bazę danych, aby eksplorować użycie kwerendy i metryki dla tej bazy danych.

    ![Analiza baz danych](media/saas-dbpertenant-log-analytics/log-analytics-database.png)

1. Aby wyświetlić dane użycia, przewiń stronę analizy w prawo.
 
     ![Dane bazy danych](media/saas-dbpertenant-log-analytics/log-analytics-database-metrics.png)

1. Przewiń stronę analizy w lewo i wybierz kafelek serwera na liście **Informacje o zasobie.**  

    ![Lista informacji o zasobie](media/saas-dbpertenant-log-analytics/log-analytics-resource-info.png)

    Zostanie otwarta strona z pulami i bazami danych na serwerze.

    ![Serwer z pulami i bazami danych](media/saas-dbpertenant-log-analytics/log-analytics-server.png)

1. Wybierz pulę. Na stronie puli, która zostanie otwarta, przewiń w prawo, aby wyświetlić metryki puli. 

    ![Dane puli](media/saas-dbpertenant-log-analytics/log-analytics-pool-metrics.png)


1. W obszarze roboczym usługi Log Analytics wybierz pozycję **Portal OMS,** aby otworzyć tam obszar roboczy.

    ![Obszar roboczy usługi Log Analytics](media/saas-dbpertenant-log-analytics/log-analytics-workspace-oms-portal.png)

W obszarze roboczym usługi Log Analytics można dalej eksplorować dane dziennika i metryki. 

Monitorowanie i alerty w dziennikach usługi Azure Monitor są oparte na zapytaniach dotyczących danych w obszarze roboczym, w przeciwieństwie do alertów zdefiniowanych w każdym zasobie w witrynie Azure portal. Opierając alerty na kwerendach, można zdefiniować pojedynczy alert, który wygląda na wszystkie bazy danych, zamiast definiować jeden na bazę danych. Zapytania są ograniczone tylko przez dane dostępne w obszarze roboczym.

Aby uzyskać więcej informacji na temat używania dzienników usługi Azure Monitor do wykonywania zapytań i ustawiania alertów, zobacz [Praca z regułami alertów w dziennikach usługi Azure Monitor.](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts-creating)

Usługa Azure Monitor rejestruje opłaty za bazę danych SQL na podstawie woluminu danych w obszarze roboczym. W tym samouczku utworzono bezpłatny obszar roboczy, który jest ograniczony do 500 MB dziennie. Po osiągnięciu tego limitu dane nie są już dodawane do obszaru roboczego.


## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Instalowanie i konfigurowanie dzienników usługi Azure Monitor.
> * Dzienniki usługi Azure Monitor są używane do monitorowania pul i baz danych.

Wypróbuj [samouczek analizy dzierżawy](saas-dbpertenant-log-analytics.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Dodatkowe samouczki, które opierają się na początkowym wdrożeniu aplikacji bazy danych SaaS Wingtip Tickets na dzierżawę](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Dzienniki usługi Azure Monitor](../azure-monitor/insights/azure-sql.md)
