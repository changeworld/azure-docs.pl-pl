---
title: Zbieranie i analizowanie dzienników aktywności platformy Azure w obszarze roboczym Log Analytics | Microsoft Docs
description: Zbierz dziennik aktywności platformy Azure w Azure Monitor dziennikach i użyj rozwiązania do monitorowania, aby przeanalizować i przeszukać dziennik aktywności platformy Azure we wszystkich subskrypcjach platformy Azure.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/30/2019
ms.openlocfilehash: f4612232acfe6099c56d365e482cbc82c8436dee
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2020
ms.locfileid: "75745632"
---
# <a name="collect-and-analyze-azure-activity-logs-in-log-analytics-workspace-in-azure-monitor"></a>Zbieranie i analizowanie dzienników aktywności platformy Azure w obszarze roboczym Log Analytics w Azure Monitor

> [!WARNING]
> Teraz można zbierać dane dziennika aktywności w obszarze roboczym Log Analytics przy użyciu ustawienia diagnostycznego podobnego do sposobu zbierania dzienników zasobów. Zobacz [zbieranie i analizowanie dzienników aktywności platformy Azure w obszarze roboczym log Analytics w Azure monitor](diagnostic-settings-subscription.md).

[Dziennik aktywności platformy Azure](platform-logs-overview.md) zapewnia wgląd w zdarzenia na poziomie subskrypcji, które wystąpiły w ramach subskrypcji platformy Azure. W tym artykule opisano, jak zebrać dziennik aktywności do obszaru roboczego Log Analytics i jak korzystać z rozwiązania do [monitorowania](../insights/solutions.md)Activity Log Analytics, które udostępnia zapytania dziennika i widoki służące do analizowania tych danych. 

Połączenie dziennika aktywności z obszarem roboczym Log Analytics zapewnia następujące korzyści:

- Konsolidacja dziennika aktywności z wielu subskrypcji platformy Azure w jednej lokalizacji na potrzeby analizy.
- Przechowywanie wpisów dziennika aktywności przez dłużej niż 90 dni.
- Skorelowanie danych dziennika aktywności z innymi danymi monitorowania zbieranymi przez Azure Monitor.
- Za pomocą [zapytań dzienników](../log-query/log-query-overview.md) można wykonywać złożone analizy i uzyskiwać szczegółowe informacje o wpisach dziennika aktywności.

## <a name="connect-to-log-analytics-workspace"></a>Łączenie z obszarem roboczym Log Analytics
Pojedynczy obszar roboczy może być połączony z dziennikiem aktywności dla wielu subskrypcji w tej samej dzierżawie platformy Azure. Aby uzyskać dostęp do kolekcji dla wielu dzierżawców, zobacz [zbieranie dzienników aktywności platformy Azure w obszarze roboczym log Analytics w wielu subskrypcjach w różnych dzierżawach Azure Active Directory](activity-log-collect-tenants.md).

> [!IMPORTANT]
> Jeśli dostawcy zasobów Microsoft. OperationalInsights i Microsoft. OperationsManagement nie są zarejestrowani dla Twojej subskrypcji, może wystąpić błąd w poniższej procedurze. Zobacz [dostawcy zasobów platformy Azure i typy](../../azure-resource-manager/resource-manager-supported-services.md) , aby zarejestrować tych dostawców.

Aby połączyć dziennik aktywności z obszarem roboczym Log Analytics, wykonaj czynności opisane w poniższej procedurze:

1. Z menu **log Analytics obszary robocze** w Azure Portal wybierz obszar roboczy, w którym ma zostać zebrany dziennik aktywności.
1. W sekcji **źródła danych obszaru roboczego** menu obszaru roboczego wybierz pozycję **Dziennik aktywności platformy Azure**.
1. Kliknij subskrypcję, którą chcesz połączyć.

    ![Obszary robocze](media/activity-log-export/workspaces.png)

1. Kliknij przycisk **Połącz** , aby połączyć dziennik aktywności w subskrypcji z wybranym obszarem roboczym. Jeśli subskrypcja jest już połączona z innym obszarem roboczym, kliknij przycisk **Rozłącz** jako pierwszy, aby rozłączyć ją.

    ![Połącz obszary robocze](media/activity-log-export/connect-workspace.png)

## <a name="analyze-in-log-analytics-workspace"></a>Analizowanie w obszarze roboczym Log Analytics
Po połączeniu dziennika aktywności z obszarem roboczym Log Analytics w obszarze roboczym wpisy zostaną zapisaniu do tabeli o nazwie **Azure** , którą można pobrać przy użyciu [zapytania dziennika](../log-query/log-query-overview.md). Struktura tej tabeli różni się w zależności od [kategorii wpisu dziennika](activity-log-view.md#categories-in-the-activity-log). Zobacz [schemat zdarzeń dziennika aktywności platformy Azure](activity-log-schema.md) , aby uzyskać opis każdej kategorii.

## <a name="activity-logs-analytics-monitoring-solution"></a>Rozwiązanie do monitorowania dzienników aktywności
Rozwiązanie do monitorowania Log Analytics platformy Azure obejmuje wiele zapytań dzienników i widoków służących do analizowania rekordów dziennika aktywności w obszarze roboczym Log Analytics.

### <a name="install-the-solution"></a>Instalowanie rozwiązania
Aby zainstalować rozwiązanie **Activity Log Analytics** , należy wykonać procedurę opisaną w temacie [Instalowanie rozwiązania monitorowania](../insights/solutions.md#install-a-monitoring-solution) . Nie jest wymagana żadna dodatkowa konfiguracja.

### <a name="use-the-solution"></a>Korzystanie z odpowiedniego rozwiązania
Do monitorowania są dostępne rozwiązania z menu **Monitoruj** w Azure Portal. Wybierz pozycję **więcej** w sekcji **szczegółowe informacje** , aby otworzyć stronę **Przegląd** z kafelkami rozwiązania. Kafelek **dzienniki aktywności platformy Azure** przedstawia liczbę rekordów operacji **platformy Azure** w obszarze roboczym.

![Kafelek Dzienniki aktywności platformy Azure](media/collect-activity-logs/azure-activity-logs-tile.png)


Kliknij kafelek **dzienniki aktywności platformy Azure** , aby otworzyć widok **dzienniki aktywności platformy Azure** . Widok zawiera części wizualizacji w poniższej tabeli. Każda część wykazuje do 10 elementów spełniających kryteria tych części dla określonego zakresu czasu. Można uruchomić kwerendę dziennika, która zwraca wszystkie zgodne rekordy, klikając pozycję **Zobacz wszystko** u dołu części.

![Pulpitu nawigacyjnego dzienników aktywności platformy Azure](media/collect-activity-logs/activity-log-dash.png)

| Część wizualizacji | Opis |
| --- | --- |
| Wpisy dziennika aktywności platformy Azure | Pokazuje wykres słupkowy najważniejszych rekordów wpisów dziennika aktywności platformy Azure dla wybranego zakresu dat i pokazuje listę głównych elementów wywołujących działania. Kliknij wykres słupkowy Aby uruchomić wyszukiwanie w dzienniku dla `AzureActivity`. Kliknij element wywołujący, aby uruchomić przeszukiwanie dziennika zwracające wszystkie wpisy dziennika aktywności dla tego elementu. |
| Dzienniki aktywności według stanu | Pokazuje wykres pierścieniowy dla stanu dziennika aktywności platformy Azure dla wybranego zakresu dat oraz listę pierwszych dziesięciu rekordów stanu. Kliknij wykres, aby uruchomić zapytanie dziennika dla `AzureActivity | summarize AggregatedValue = count() by ActivityStatus`. Kliknij element status, aby uruchomić przeszukiwanie dziennika zwracające wszystkie wpisy dziennika aktywności dla tego rekordu stanu. |
| Dzienniki aktywności według zasobu | Pokazuje łączną liczbę zasobów z dziennikami aktywności i zawiera dziesięć najważniejszych zasobów z liczbami rekordów dla każdego zasobu. Kliknij obszar całkowitej, aby uruchomić wyszukiwanie w dzienniku dla `AzureActivity | summarize AggregatedValue = count() by Resource`, które wyświetla wszystkie zasoby platformy Azure dostępnych do rozwiązania. Kliknij zasób, aby uruchomić zapytanie dziennika zwracające wszystkie rekordy działań dla tego zasobu. |
| Dzienniki aktywności przez dostawcę zasobów | Pokazuje łączną liczbę dostawców zasobów, którzy generują dzienniki aktywności i wyświetlają dziesięć najważniejszych. Kliknij łączny obszar, aby uruchomić zapytanie dziennika dla `AzureActivity | summarize AggregatedValue = count() by ResourceProvider`, w którym są wyświetlane wszyscy dostawcy zasobów platformy Azure. Kliknij dostawcę zasobów, aby uruchomić zapytanie dziennika zwracające wszystkie rekordy działań dla dostawcy. |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [dzienniku aktywności](platform-logs-overview.md).
- Dowiedz się więcej o [platformie danych Azure monitor](data-platform.md).
- Użyj [zapytań dzienników](../log-query/log-query-overview.md) , aby wyświetlić szczegółowe informacje z dziennika aktywności.
