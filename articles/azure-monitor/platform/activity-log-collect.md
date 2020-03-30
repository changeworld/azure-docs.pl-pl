---
title: Zbieranie dziennika aktywności platformy Azure w obszarze roboczym usługi Log Analytics
description: Zbieranie dziennika aktywności platformy Azure w dziennikach usługi Azure Monitor i używanie rozwiązania do monitorowania do analizowania i przeszukiwania dziennika aktywności platformy Azure we wszystkich subskrypcjach platformy Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/30/2019
ms.openlocfilehash: 407bff10e2480c5210d3057bcccd6c60e591c165
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80055307"
---
# <a name="collect-and-analyze-azure-activity-logs-in-log-analytics-workspace-in-azure-monitor"></a>Zbieranie i analizowanie dzienników aktywności platformy Azure w obszarze roboczym usługi Log Analytics w usłudze Azure Monitor

> [!WARNING]
> Teraz można zbierać dziennik aktywności w obszarze roboczym usługi Log Analytics przy użyciu ustawienia diagnostycznego podobnego do sposobu zbierania dzienników zasobów. Zobacz [Zbieranie i analizowanie dzienników aktywności platformy Azure w obszarze roboczym usługi Log Analytics w usłudze Azure Monitor.](diagnostic-settings-legacy.md)

[Dziennik aktywności platformy Azure](platform-logs-overview.md) zapewnia wgląd w zdarzenia na poziomie subskrypcji, które wystąpiły w ramach subskrypcji platformy Azure. W tym artykule opisano sposób zbierania dziennika aktywności w obszarze roboczym usługi Log Analytics i jak korzystać z rozwiązania do [monitorowania](../insights/solutions.md)usługi Analizy dzienników aktywności, które udostępnia zapytania dziennika i widoki do analizowania tych danych. 

Łączenie dziennika aktywności z obszarem roboczym usługi Log Analytics zapewnia następujące korzyści:

- Skonsoliduj dziennik aktywności z wielu subskrypcji platformy Azure w jedną lokalizację do analizy.
- Wpisy dziennika aktywności sklepu przez okres dłuższy niż 90 dni.
- Skoreluj dane dziennika aktywności z innymi danymi monitorowania zebranymi przez usługę Azure Monitor.
- Użyj [zapytań dziennika](../log-query/log-query-overview.md) do wykonywania złożonych analiz i uzyskać szczegółowe informacje na temat wpisów dziennika aktywności.

## <a name="connect-to-log-analytics-workspace"></a>Łączenie się z obszarem roboczym usługi Log Analytics
Pojedynczy obszar roboczy może być połączony z dziennikiem aktywności dla wielu subskrypcji w tej samej dzierżawie platformy Azure. W przypadku zbierania w wielu dzierżawach zobacz [Zbieranie dzienników aktywności platformy Azure w obszarze roboczym usługi Log Analytics w różnych subskrypcjach w różnych dzierżawach usługi Azure Active Directory.](activity-log-collect-tenants.md)

> [!IMPORTANT]
> Może pojawić się błąd z poniższą procedurą, jeśli microsoft.OperationalInsights i Microsoft.OperationsManagement dostawców zasobów nie są zarejestrowane dla subskrypcji. Zobacz [dostawców zasobów platformy Azure i typów,](../../azure-resource-manager/management/resource-providers-and-types.md) aby zarejestrować tych dostawców.

Aby połączyć dziennik aktywności z obszarem roboczym usługi Log Analytics, użyj następującej procedury:

1. Z menu **obszarów roboczych usługi Log Analytics** w witrynie Azure portal wybierz obszar roboczy do zbierania dziennika aktywności.
1. W sekcji **Źródła danych obszaru roboczego** w menu obszaru roboczego wybierz pozycję **Dziennik aktywności platformy Azure**.
1. Kliknij subskrypcję, którą chcesz połączyć.

    ![Obszary robocze](media/activity-log-export/workspaces.png)

1. Kliknij **przycisk Połącz,** aby połączyć dziennik aktywności w subskrypcji z wybranym obszarem roboczym. Jeśli subskrypcja jest już połączona z innym obszarem roboczym, kliknij przycisk **Rozłącz** najpierw, aby ją odłączyć.

    ![Łączenie obszarów roboczych](media/activity-log-export/connect-workspace.png)

## <a name="analyze-in-log-analytics-workspace"></a>Analizowanie w obszarze roboczym usługi Log Analytics
Po podłączeniu dziennika aktywności do obszaru roboczego usługi Log Analytics wpisy zostaną zapisane w obszarze roboczym do tabeli o nazwie **AzureActivity,** którą można pobrać za pomocą [kwerendy dziennika.](../log-query/log-query-overview.md) Struktura tej tabeli różni się w zależności od [kategorii wpisu dziennika](activity-log-view.md#categories-in-the-activity-log). Zobacz [schemat zdarzeń dziennika aktywności platformy Azure,](activity-log-schema.md) aby uzyskać opis każdej kategorii.

## <a name="activity-logs-analytics-monitoring-solution"></a>Rozwiązanie do monitorowania usługi Activity Logs Analytics
Rozwiązanie do monitorowania usługi Azure Log Analytics zawiera wiele zapytań dziennika i widoków do analizowania rekordów dziennika aktywności w obszarze roboczym usługi Log Analytics.

### <a name="install-the-solution"></a>Zainstaluj rozwiązanie
Użyj procedury w [zainstaluj rozwiązanie do monitorowania,](../insights/solutions.md#install-a-monitoring-solution) aby zainstalować rozwiązanie **analizy dzienników aktywności.** Nie jest wymagana żadna dodatkowa konfiguracja.

### <a name="use-the-solution"></a>Użyj rozwiązania
Rozwiązania do monitorowania są dostępne z menu **Monitor** w witrynie Azure portal. Wybierz **pozycję Więcej** w sekcji **Statystyki,** aby otworzyć stronę **Przegląd** z kafelkami rozwiązania. Kafelek **Dzienniki aktywności platformy Azure** wyświetla liczbę rekordów **usługi AzureActivity** w obszarze roboczym.

![Kafelek dzienników aktywności platformy Azure](media/collect-activity-logs/azure-activity-logs-tile.png)


Kliknij **kafelek Dzienniki aktywności platformy Azure,** aby otworzyć widok **dzienniki aktywności platformy Azure.** Widok zawiera części wizualizacji w poniższej tabeli. Każda część zawiera listę do 10 elementów pasujących do kryteriów tej części dla określonego zakresu czasu. Można uruchomić kwerendę dziennika, która zwraca wszystkie pasujące rekordy, klikając **zobacz wszystko** u dołu części.

![Pulpit nawigacyjny dzienników aktywności platformy Azure](media/collect-activity-logs/activity-log-dash.png)

| Część wizualizacji | Opis |
| --- | --- |
| Wpisy dziennika aktywności platformy Azure | Pokazuje wykres słupkowy najwyższej sumy rekordów dziennika aktywności platformy Azure dla wybranego zakresu dat i pokazuje listę 10 najlepszych wywołań aktywności. Kliknij wykres słupkowy, aby `AzureActivity`uruchomić wyszukiwanie dziennika . Kliknij element wywołującego, aby uruchomić wyszukiwanie dziennika zwracające wszystkie wpisy dziennika aktywności dla tego elementu. |
| Dzienniki aktywności według stanu | Pokazuje wykres pierścieniowy dla stanu dziennika aktywności platformy Azure dla wybranego zakresu dat i listę dziesięciu pierwszych rekordów stanu. Kliknij wykres, aby uruchomić `AzureActivity | summarize AggregatedValue = count() by ActivityStatus`kwerendę dziennika dla . Kliknij element stanu, aby uruchomić wyszukiwanie dziennika zwracające wszystkie wpisy dziennika aktywności dla tego rekordu stanu. |
| Dzienniki aktywności według zasobów | Pokazuje całkowitą liczbę zasobów za pomocą dzienników aktywności i wyświetla dziesięć pierwszych zasobów z liczbą rekordów dla każdego zasobu. Kliknij całkowity obszar, aby uruchomić `AzureActivity | summarize AggregatedValue = count() by Resource`wyszukiwanie dziennika , który pokazuje wszystkie zasoby platformy Azure dostępne dla rozwiązania. Kliknij zasób, aby uruchomić kwerendę dziennika zwracającą wszystkie rekordy działań dla tego zasobu. |
| Dzienniki aktywności według dostawcy zasobów | Pokazuje całkowitą liczbę dostawców zasobów, którzy produkują dzienniki aktywności i wyświetla listę dziesięciu najlepszych. Kliknij całkowity obszar, aby uruchomić `AzureActivity | summarize AggregatedValue = count() by ResourceProvider`kwerendę dziennika dla , który pokazuje wszystkich dostawców zasobów platformy Azure. Kliknij dostawcę zasobów, aby uruchomić kwerendę dziennika zwracającą wszystkie rekordy aktywności dla dostawcy. |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [dzienniku aktywności](platform-logs-overview.md).
- Dowiedz się więcej o [platformie danych Usługi Azure Monitor](data-platform.md).
- Użyj [zapytań dziennika,](../log-query/log-query-overview.md) aby wyświetlić szczegółowe informacje z dziennika aktywności.
