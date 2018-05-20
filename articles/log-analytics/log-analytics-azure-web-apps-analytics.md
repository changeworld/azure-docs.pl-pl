---
title: Wyświetl dane analityczne Azure Web Apps | Dokumentacja firmy Microsoft
description: Uzyskaj informacje o aplikacjach sieci Web platformy Azure przez zbieranie metryk różnych różnych zasobów aplikacji sieci Web platformy Azure umożliwia rozwiązania analizy aplikacji sieci Web platformy Azure.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: 20ff337f-b1a3-4696-9b5a-d39727a94220
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2018
ms.author: magoedte
ms.openlocfilehash: c44546ffe65a0849b485fb0d2dd169e4216ae80a
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
---
# <a name="view-analytic-data-for-metrics-across-all-your-azure-web-app-resources"></a>Wyświetl dane analityczne metryki różnych zasobów aplikacji sieci Web Azure

![Symbol aplikacji sieci Web](./media/log-analytics-azure-web-apps-analytics/azure-web-apps-analytics-symbol.png)  

> [!NOTE]
> Rozwiązania Azure Web Apps Analytics jest przestarzała.  Klienci, którzy zainstalowali rozwiązania można nadal z niego korzystać, ale analiza aplikacji sieci Web Azure nie można dodać do żadnych nowych obszarów roboczych.  Do monitorowania aplikacji sieci web, zalecane jest użycie [usługi Application Insights](../application-insights/app-insights-overview.md). 

Rozwiązanie Analytics aplikacji sieci Web platformy Azure (wersja zapoznawcza) zapewnia wgląd w Twoje [Azure Web Apps](../app-service/app-service-web-overview.md) przez zbieranie metryk różnych różnych zasobów aplikacji sieci Web platformy Azure. Dzięki rozwiązaniu można było analizować i wyszukiwać dane metryki zasobów aplikacji sieci web.

Za pomocą rozwiązania, można wyświetlić:

- Górny aplikacje sieci Web z najwyższym czas odpowiedzi
- Liczba żądań w aplikacji sieci Web, włącznie z żądaniami udane i nieudane
- Górny aplikacje sieci Web z najwyższym ruchu przychodzącego i wychodzącego
- Plany usługi TOP z wysokie użycie procesora CPU i pamięci
- Operacje dziennika aktywności aplikacji sieci Web platformy Azure

## <a name="connected-sources"></a>Połączone źródła

W przeciwieństwie do większości innych rozwiązań analizy dzienników danych nie jest zbieranych dla aplikacji sieci Web Azure przez agentów. Wszystkie dane używane przez to rozwiązanie pochodzi bezpośrednio z platformy Azure.

| Połączone źródło | Obsługiwane | Opis |
| --- | --- | --- |
| [Agenci dla systemu Windows](log-analytics-windows-agent.md) | Nie | Rozwiązanie nie zbiera informacje z agentów systemu Windows. |
| [Agenci dla systemu Linux](log-analytics-linux-agents.md) | Nie | Rozwiązanie nie zbiera informacje z agentów systemu Linux. |
| [Grupa zarządzania programu SCOM](log-analytics-om-agents.md) | Nie | Rozwiązanie nie zbiera informacje z agentów w podłączonej grupy zarządzania SCOM. |
| [Konto usługi Azure Storage](log-analytics-azure-storage.md) | Nie | Rozwiązanie nie nie zbierają informacje z usługi Azure storage. |

## <a name="prerequisites"></a>Wymagania wstępne

- Aby uzyskać dostęp do aplikacji sieci Web Azure informacje dotyczące pomiaru zasobów, musi mieć subskrypcję platformy Azure.

## <a name="configuration"></a>Konfigurowanie

Wykonaj poniższe kroki, aby skonfigurować rozwiązanie Analytics aplikacji sieci Web platformy Azure dla obszarów roboczych.

1. Włącz rozwiązania analizy aplikacji sieci Web platformy Azure z [witrynę Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureWebAppsAnalyticsOMS?tab=Overview) lub przy użyciu procesu opisanego w [rozwiązań dodać analizy dzienników z galerii rozwiązań](log-analytics-add-solutions.md).
2. [Włącz rejestrowanie metryki zasobów platformy Azure, aby Analiza dzienników przy użyciu programu PowerShell](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell).

Rozwiązania Azure Web Apps Analytics zbiera dwóch zestawów metryki z platformy Azure:

- Metryki aplikacji sieci Web platformy Azure
  - Średni zestaw roboczy pamięci
  - Średni czas odpowiedzi
  - Odebrano wysłane bajty
  - Czas procesora CPU
  - Żądania
  - Zestaw roboczy pamięci
  - Httpxxx
- Metryki planu usługi aplikacji
  - Odebrano wysłane bajty
  - Procent użycia procesora CPU
  - Długość kolejki dysku
  - Długość kolejki HTTP
  - Procent pamięci

Metryki planu usługi aplikacji są zbierane tylko, jeśli używasz planu dedykowanych usługi. To nie ma zastosowania do planów usługi aplikacji — warstwa bezpłatna lub udostępnionego.

Po skonfigurowaniu rozwiązania danych należy zacząć przepływać do obszaru roboczego w ciągu 15 minut.

## <a name="using-the-solution"></a>Użycie rozwiązania

Po dodaniu rozwiązania analizy aplikacji sieci Web platformy Azure do swojego obszaru roboczego **Analytics aplikacji sieci Web Azure** kafelka jest dodawany do pulpitu nawigacyjnego przeglądu. Ten Kafelek jest wyświetlana liczba liczba aplikacji sieci Web Azure czy rozwiązania ma dostęp do w Twojej subskrypcji platformy Azure.

![Kafelek Analytics aplikacji sieci Web platformy Azure](./media/log-analytics-azure-web-apps-analytics/azure-web-apps-analytics-tile.png)

### <a name="view-azure-web-apps-analytics-information"></a>Wyświetlanie informacji o Analytics aplikacji sieci Web Azure

Kliknij przycisk **Analytics aplikacji sieci Web Azure** Kafelek, aby otworzyć **Analytics aplikacji sieci Web Azure** pulpitu nawigacyjnego. Pulpit nawigacyjny zawiera bloki w poniższej tabeli. Każdy blok zawiera listę elementów do dziesięciu spełniających kryteria tego bloku dla określonego zakresu i zakres czasu. Można uruchomić wyszukiwania dziennika, który zwraca wszystkie rekordy, klikając **zobaczyć wszystkie** w dolnej części bloku lub przez kliknięcie nagłówka bloku.


| Kolumna | Opis |
| --- | --- |
| Azure Webapps |   |
| Trendy żądania aplikacji sieci Web | Przedstawia wykres liniowy trendu żądania aplikacji sieci Web wybranego zakresu i pokazuje listę żądań top dziesięć sieci web. Kliknij wykres liniowy, aby uruchomić wyszukiwanie dziennika <code>AzureMetrics &#124; where ResourceId == "/MICROSOFT.WEB/SITES/" and (MetricName == "Requests" or MetricName startswith_cs "Http") &#124; summarize AggregatedValue = avg(Average) by MetricName, bin(TimeGenerated, 1h)</code> <br>Kliknij element żądania sieci web, aby uruchomić wyszukiwanie dziennika trendu metryki żądania sieci web, które żądania. |
| Czas odpowiedzi aplikacji sieci Web | Przedstawia wykres liniowy, czas odpowiedzi aplikacji sieci Web wybranego zakresu. Również pokazuje listę listę górnej dziesięć Web Apps odpowiedzi czasu. Kliknij na wykresie, aby uruchomić wyszukiwanie dziennika <code>AzureMetrics &#124; where ResourceId == "/MICROSOFT.WEB/SITES/" and MetricName == "AverageResponseTime" &#124; summarize AggregatedValue = avg(Average) by Resource, bin(TimeGenerated, 1h)</code><br> Kliknij aplikację sieci Web, aby uruchomić wyszukiwanie dziennika zwracanie czasy odpowiedzi dla aplikacji sieci Web. |
| Ruch w aplikacji sieci Web | Przedstawia wykres liniowy dla ruchu aplikacje sieci Web w MB i list górnej ruchu aplikacji sieci Web. Kliknij na wykresie, aby uruchomić wyszukiwanie dziennika <code>AzureMetrics &#124; where ResourceId == "/MICROSOFT.WEB/SITES/" and (MetricName == "BytesSent" or MetricName == "BytesReceived") &#124; summarize AggregatedValue = sum(Average) by Resource, bin(TimeGenerated, 1h)</code><br> Wszystkie aplikacje sieci Web z ruchem będzie wyświetlana w ciągu ostatniej minuty. Kliknij aplikację sieci Web, aby uruchomić wyszukiwanie dziennika przedstawiający bajtów odebranych i wysłanych do aplikacji sieci Web. |
| Plany usługi aplikacji Azure |   |
| Planów usługi App Service z procesora &gt; 80% | List top 10 plany usługi App Service według użycia Procesora i przedstawia sumę plany usługi App Service, który ma użycie procesora CPU przekracza 80%. Kliknij przycisk całkowity obszar, aby uruchomić wyszukiwanie dziennika <code>AzureMetrics &#124; where ResourceId == "/MICROSOFT.WEB/SERVERFARMS/" and MetricName == "CpuPercentage" &#124; summarize AggregatedValue = avg(Average) by Resource</code><br> Będzie wyświetlana lista planów usługi aplikacji i ich średnie wykorzystanie procesora CPU. Kliknij przycisk Plan usługi App Service przeprowadzić wyszukiwanie dziennika przedstawiający jego średnie wykorzystanie procesora CPU. |
| Planów usługi App Service z wykorzystanie pamięci &gt; 80% | List top 10 plany usługi App Service przez użycie pamięci i jest wyświetlana łączna liczba plany usługi App Service, który ma użycie pamięci jest większa niż 80%. Kliknij przycisk całkowity obszar, aby uruchomić wyszukiwanie dziennika <code>AzureMetrics &#124; where ResourceId == "/MICROSOFT.WEB/SERVERFARMS/" and MetricName == "MemoryPercentage" &#124; summarize AggregatedValue = avg(Average) by Resource</code><br> Będzie wyświetlana lista planów usługi aplikacji i ich średnie wykorzystanie pamięci. Kliknij przycisk Plan usługi App Service przeprowadzić wyszukiwanie dziennika przedstawiający jego średnie wykorzystanie pamięci. |
| Dzienniki aktywności aplikacji sieci Web platformy Azure |   |
| Inspekcji działania aplikacji sieci Web platformy Azure | Zawiera całkowitą liczbę aplikacji sieci Web za pomocą [Dzienniki aktywności](log-analytics-activity.md) i wyświetla operacje dziennika aktywności pierwszych 10. Kliknij przycisk całkowity obszar, aby uruchomić wyszukiwanie dziennika <code>AzureActivity #124; where ResourceProvider == "Azure Web Sites" #124; summarize AggregatedValue = count() by OperationName</code><br> Listę operacje tworzenia dzienników działania będzie wyświetlana. Kliknij przycisk wyszukiwania dziennika, który zawiera listę rekordów do operacji wykonanie operacji dziennika aktywności. |



### <a name="azure-web-apps"></a>Azure Web Apps

Na pulpicie nawigacyjnym użytkownik może przejść do szczegółów uzyskanie uzyskać lepszy wgląd w Twoje metryki aplikacji sieci Web. To najpierw zestaw bloków Pokaż trend żądań aplikacji sieci Web, liczbę błędów (na przykład HTTP404), ruchu i Średni czas odpowiedzi w czasie. Przedstawiono również podział te metryki dla różnych aplikacji sieci Web.

![Azure używanie bloków](./media/log-analytics-azure-web-apps-analytics/web-apps-dash01.png)

Główną przyczyną pokazujący, że dane są tak, aby można zidentyfikować aplikacji sieci Web z czasem odpowiedzi wysokiej i Sprawdź przyczynę. Próg limitu dotyczy również ułatwić identyfikację tych problemów.

- Aplikacje sieci Web zaznaczone na czerwono mają wyższe niż 1 sekunda czas odpowiedzi.
- Wyświetlane w kolorze pomarańczowym aplikacji sieci Web mają wyższe niż 0,7 sekundę i mniejsza niż 1 sekunda czas odpowiedzi.
- Aplikacje sieci Web wyświetlany na zielono mieć drugi poniżej 0,7 czas odpowiedzi.

Z poniższą ilustracją dziennik wyszukiwania przykładzie, można stwierdzić, że *anugup3* aplikacji sieci web ma znacznie wyższa czas odpowiedzi od innych aplikacji sieci web.

![Przykład wyszukiwania dziennika](./media/log-analytics-azure-web-apps-analytics/web-app-search-example.png)

### <a name="app-service-plans"></a>Plany usługi App Service

Jeśli korzystasz z dedykowanym plany usługi, może również zbierać metryki dla planów usługi aplikacji. W tym widoku, zostanie wyświetlony, Twoje plany usługi App Service z wysokie użycie procesora CPU lub pamięci (&gt; 80%). Przedstawia on także top usługi aplikacji z wysokie użycie pamięci lub zasobów Procesora. Podobnie próg limitu jest stosowany do umożliwiają identyfikację tych problemów.

- Plany usługi App Service zaznaczone na czerwono mają wyższe niż 80% wykorzystania Procesora/pamięci.
- Wyświetlane w kolorze pomarańczowym planów usługi App Service ma wykorzystanie Procesora/pamięci, wyższy niż 60%, a mniejszy niż 80%.
- Plany usługi App Service wyświetlany na zielono ma mniej niż 60% wykorzystania Procesora/pamięci.

![Bloki planów usługi aplikacji Azure](./media/log-analytics-azure-web-apps-analytics/web-apps-dash02.png)

## <a name="azure-web-apps-log-searches"></a>Azure wyszukiwania dzienników aplikacji sieci Web

**Listy z popularnych Azure Web Apps zapytania wyszukiwania** pokazuje wszystkie dzienniki pokrewne działania aplikacji sieci Web, która zapewnia wgląd w operacje wykonywane na zasobów aplikacji sieci Web. Wyświetla również wszystkie operacje pokrewne i liczba powtórzeń ich wystąpienia.

Przy użyciu dowolnego zapytania wyszukiwania dziennika jako punkt początkowy, możesz łatwo utworzyć alertu. Na przykład można utworzyć alertu, gdy metryki Średni czas odpowiedzi jest większy niż co 1 s.

## <a name="next-steps"></a>Kolejne kroki

- Utwórz [alert](log-analytics-alerts-creating.md) dla określonej metryki.
- Użyj [wyszukiwania dziennika](log-analytics-log-searches.md) do wyświetlenia szczegółowych informacji z dzienników działania.
