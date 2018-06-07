---
title: Analiza dzienników aplikacji korzystać z bazy danych SQL wielodostępnym | Dokumentacja firmy Microsoft
description: Konfigurowanie i używanie analizy dzienników wielodostępnych aplikacji SaaS bazy danych SQL Azure
keywords: samouczek usługi sql database
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.reviewer: billgib
ms.openlocfilehash: 38adf3dd2be0770dd815644ece452a82bc98baf9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34645321"
---
# <a name="set-up-and-use-log-analytics-with-a-multitenant-sql-database-saas-app"></a>Konfigurowanie i używanie analizy dzienników wielodostępnych aplikacji SaaS bazy danych SQL

W tym samouczku, konfigurowania i używania Azure [analizy dzienników](/azure/log-analytics/log-analytics-overview) monitorowania pule elastyczne i baz danych. W tym samouczku opiera się na [Samouczek zarządzania i monitorowania wydajności](saas-dbpertenant-performance-monitoring.md). Widoczny jest sposób rozszerzyć monitorowanie za pomocą analizy dzienników i alertów dostępne w portalu Azure. Analiza dzienników obsługuje monitorowania tysiące pule elastyczne i setkami tysięcy baz danych. Analiza dzienników udostępnia jedno rozwiązanie monitorowania, które można zintegrować monitorowanie różnych aplikacji i usług Azure w wielu subskrypcji platformy Azure.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Instalowanie i konfigurowanie analizy dzienników.
> * Umożliwia monitorowanie pul i bazy danych analizy dzienników.

Do wykonania zadań opisanych w tym samouczku niezbędne jest spełnienie następujących wymagań wstępnych:

* Aplikacja bazy danych dla dzierżawy Wingtip biletów SaaS jest wdrażana. Aby wdrożyć w mniej niż 5 minut, zobacz [Wdróż i eksplorowanie aplikacji bazy danych dla dzierżawy Wingtip biletów SaaS](saas-dbpertenant-get-started-deploy.md).
* Zainstalowany jest program Azure PowerShell. Aby uzyskać więcej informacji, zobacz [wprowadzenie do programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

Zobacz [Samouczek zarządzania i monitorowania wydajności](saas-dbpertenant-performance-monitoring.md) omówienie scenariuszy SaaS, wzorców i ich wpływ na wymagania dotyczące rozwiązanie monitorowania.

## <a name="monitor-and-manage-database-and-elastic-pool-performance-with-log-analytics"></a>Monitorowanie i zarządzanie nimi wydajność puli bazy danych i elastyczna z analizy dzienników

Baza danych SQL Azure monitorowanie i alerty jest dostępna dla baz danych i pul w portalu Azure. Ten wbudowaną funkcję monitorowania i alertów jest wygodne, ale jest również określonych zasobów. Oznacza to, że mniej dobrze nadaje się do monitorowania duże instalacje lub zapewnienia ujednoliconego podglądu całej zasobów i subskrypcje.

W przypadku dużych scenariuszy analizy dzienników służy do monitorowania oraz alertów. Analiza dzienników jest osobnym usługa Azure, która umożliwia analytics przez dzienniki diagnostyczne i dane telemetryczne, które są zbierane w obszarze roboczym z potencjalnie wiele usług. Analiza dzienników udostępnia wbudowaną kwerendę narzędzia wizualizacji języka i danych, które umożliwiają analizy danych operacyjnych. Rozwiązania analizy SQL zawiera kilka wstępnie zdefiniowanych puli elastycznej i bazy danych monitorowania i alertów widoków i zapytań. Analiza dzienników także projektanta widok niestandardowy.

Rozwiązań analitycznych dziennika obszarów roboczych i analiza Otwórz w portalu Azure i usługi Operations Management Suite. Azure portal to nowsza punktu dostępu, ale może być za portal usługi Operations Management Suite w niektórych obszarach.

### <a name="create-performance-diagnostic-data-by-simulating-a-workload-on-your-tenants"></a>Tworzenie danych diagnostycznych wydajności symulując obciążenie dzierżawców 

1. Otwórz w programie PowerShell ISE *... \\WingtipTicketsSaaS MultiTenantDb wzorca\\modułów szkoleniowych\\zarządzania i monitorowania wydajności\\PerformanceMonitoringAndManagement.ps1 pokaz*. Nie zamykaj tego skryptu, ponieważ może chcesz uruchomić kilka obciążenia generowania scenariusze w tym samouczku.
2. Jeśli nie zostało to jeszcze zrobione już, należy udostępnić partii dzierżawcy, aby bardziej interesującego monitorowania kontekstu. Ten proces trwa kilka minut.

   a. Ustaw **$DemoScenario = 1**, _udostępnić partii dzierżaw_.

   b. Aby uruchomić skrypt i wdrożyć dodatkowe dzierżaw 17, naciśnij klawisz F5.

3. Teraz uruchomić generatora obciążenia, aby uruchomić symulowanym obciążeniem na wszystkich dzierżawców.

    a. Ustaw **$DemoScenario = 2**, _Generuj normalnym natężeniu obciążenia (średnio 30 DTU)_.

    b. Aby uruchomić skrypt, naciśnij klawisz F5.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Pobierz skrypty bazy danych dla dzierżawy aplikacji SaaS biletów Wingtip

SaaS biletów Wingtip wielodostępnym bazy danych skrypty i kod źródłowy aplikacji są dostępne w [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) repozytorium GitHub. Aby uzyskać instrukcje dotyczące pobierania i odblokować skryptów PowerShell biletów Wingtip, zobacz [ogólne wskazówki](saas-tenancy-wingtip-app-guidance-tips.md).

## <a name="install-and-configure-log-analytics-and-the-azure-sql-analytics-solution"></a>Instalowanie i konfigurowanie analizy dzienników oraz rozwiązania analizy SQL Azure

Analiza dzienników jest oddzielny usługa, która musi być skonfigurowany. Analiza dzienników umożliwia zbieranie danych dziennika, telemetrii i metryki w obszarze roboczym analizy dzienników. Podobnie jak inne zasoby na platformie Azure można utworzyć obszaru roboczego analizy dzienników. Obszar roboczy nie muszą zostać utworzone w tej samej grupie zasobów co aplikacji, którą monitoruje. Często ten najwygodniejszy jednak. W przypadku aplikacji Wingtip biletów umożliwia upewnij się, że w obszarze roboczym zostanie usunięty z aplikacją pojedyncza grupa zasobów.

1. Otwórz w programie PowerShell ISE *... \\WingtipTicketsSaaS MultiTenantDb wzorca\\modułów szkoleniowych\\zarządzania i monitorowania wydajności\\dziennika analizy\\LogAnalytics.ps1 pokaz*.
2. Aby uruchomić skrypt, naciśnij klawisz F5.

Teraz możesz otworzyć Log Analytics w portalu Azure lub w portalu usługi Operations Management Suite. Trwa kilka minut, aby zbierać dane telemetryczne w obszarze roboczym analizy dzienników i aby go wyświetlić. Już pozostanie system zbierania danych diagnostycznych jest bardziej interesującego środowisko. 

## <a name="use-log-analytics-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>Używanie programu Log Analytics i rozwiązania SQL Analytics do monitorowana pul i baz danych


W tym ćwiczeniu Otwórz analizy dzienników i portalu usługi Operations Management Suite, aby przyjrzeć się telemetryczne zebrane dla baz danych i pul.

1. Przejdź do [portalu Azure](https://portal.azure.com). Wybierz **wszystkie usługi** otworzyć analizy dzienników. Następnie wyszukaj analizy dzienników.

   ![Otwórz dziennik analizy](media/saas-dbpertenant-log-analytics/log-analytics-open.png)

2. Wybierz obszar roboczy o nazwie _wtploganalytics -&lt;użytkownika&gt;_.

3. Wybierz opcję **Przegląd**, aby otworzyć rozwiązanie Log Analytics w witrynie Azure Portal.

   ![Przegląd](media/saas-dbpertenant-log-analytics/click-overview.png)

    > [!IMPORTANT]
    > Może upłynąć kilka minut, zanim rozwiązanie będzie aktywny. 

4. Wybierz **analiza SQL Azure** Kafelek, aby go otworzyć.

    ![Kafelek przeglądu](media/saas-dbpertenant-log-analytics/overview.png)

5. Widoki w rozwiązaniu bok, przewijane wraz z ich własnych paska przewijania wewnętrzny u dołu. Odśwież stronę, jeśli to konieczne.

6. Aby eksplorować strony podsumowania, wybierz Kafelki lub pojedynczych baz danych, aby otworzyć Eksploratora Przechodzenie do szczegółów.

    ![Pulpitu nawigacyjnego Analytics dziennika](media/saas-dbpertenant-log-analytics/log-analytics-overview.png)

7. Zmień ustawienie filtru, aby zmodyfikować zakres czasu. W tym samouczku, wybierz **ostatni 1 godzina**.

    ![Filtr czasu](media/saas-dbpertenant-log-analytics/log-analytics-time-filter.png)

8. Wybierz pojedynczej bazy danych, aby zapoznać się z użycia zapytania i metryki dla tej bazy danych.

    ![Analiza bazy danych](media/saas-dbpertenant-log-analytics/log-analytics-database.png)

9. Aby widzieć metryki użycia, przewiń stronę analytics z prawej strony.
 
     ![metryki bazy danych](media/saas-dbpertenant-log-analytics/log-analytics-database-metrics.png)

10. Przewiń analytics strony do lewej strony i wybierz Kafelek serwera w **informacje o zasobie** listy.  

    ![Lista informacji o zasobów](media/saas-dbpertenant-log-analytics/log-analytics-resource-info.png)

    Zostanie otwarta strona pokazujący pul i baz danych na serwerze.

    ![Serwer z puli i baz danych](media/saas-dbpertenant-log-analytics/log-analytics-server.png)

11. Wybierz pulę. Na stronie puli przewiń w prawo, aby widzieć metryki puli. 

    ![metryki puli](media/saas-dbpertenant-log-analytics/log-analytics-pool-metrics.png)


12. W obszarze roboczym analizy dzienników, wybierz **portalu OMS** do Otwórz obszar roboczy.

    ![Operations Management Suite Portal kafelka](media/saas-dbpertenant-log-analytics/log-analytics-workspace-oms-portal.png)

W portalu usługi Operations Management Suite można eksplorować dane dziennika i metryki w obszarze roboczym dalej. 

Monitorowanie i alerty w analizy dzienników są oparte na kwerend danych w obszarze roboczym, w odróżnieniu od alerty zdefiniowane na każdy zasób w portalu Azure. Użycie alerty dla zapytań, można określić pojedynczy alert, która wygląda przez wszystkie bazy danych, a nie definiującego po jednym dla każdej bazy danych. Zapytania są ograniczone tylko danych dostępnych w obszarze roboczym.

Aby uzyskać więcej informacji na temat korzystania z analizy dzienników do wykonywania zapytań i Ustaw alerty, zobacz [pracować z reguły alertów w analizy dzienników](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts-creating).

Analiza dzienników opłat bazy danych SQL na podstawie rozmiaru danych w obszarze roboczym. W tym samouczku utworzono obszar roboczy wolne, która jest ograniczona do 500 MB na dzień. Po osiągnięciu tego limitu danych jest już dodany do obszaru roboczego.


## <a name="next-steps"></a>Kolejne kroki

W tym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Instalowanie i konfigurowanie analizy dzienników.
> * Umożliwia monitorowanie pul i bazy danych analizy dzienników.

Spróbuj [samouczek analizy dzierżawy](saas-dbpertenant-log-analytics.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Dodatkowe samouczki, w początkowym wdrożeniu aplikacji bazy danych dla dzierżawy Wingtip biletów SaaS](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Program Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md)
