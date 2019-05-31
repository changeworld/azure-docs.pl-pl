---
title: Zbieraj i Analizuj Dzienniki aktywności platformy Azure, w obszarze roboczym usługi Log Analytics | Dokumentacja firmy Microsoft
description: Zbieraj dziennika aktywności platformy Azure w usłudze Azure Monitor dziennikach i wykorzystać rozwiązanie do monitorowania do analizowania i wyszukiwanie w dzienniku aktywności platformy Azure w Twojej subskrypcji platformy Azure.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: dbac4c73-0058-4191-a906-e59aca8e2ee0
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/19/2019
ms.author: bwren
ms.openlocfilehash: 5839fd40a128097e400f13acbe4fb6ef90c656b7
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66248132"
---
# <a name="collect-and-analyze-azure-activity-logs-in-log-analytics-workspace-in-azure-monitor"></a>Zbieranie i analizowanie dzienników aktywności platformy Azure, w obszarze roboczym Log Analytics w usłudze Azure Monitor
[Dziennika aktywności platformy Azure](activity-logs-overview.md) zapewnia wgląd w zdarzenia na poziomie subskrypcji, które miały miejsce w ramach subskrypcji platformy Azure. W tym artykule opisano sposób zbierania dzienników aktywności do obszaru roboczego usługi Log Analytics i sposobu użycia Activity Log Analytics [rozwiązanie do monitorowania](../insights/solutions.md), który zapewnia dziennika zapytań i widoków do analizowania tych danych. 

Dziennik aktywności nawiązywania połączenia z obszarem roboczym usługi Log Analytics zapewnia następujące korzyści:

- Konsolidacja dziennik aktywności z wieloma subskrypcjami platformy Azure w jednym miejscu na potrzeby analizy.
- Store wpisy dziennika aktywności przez dłużej niż 90 dni.
- Korelowanie danych dziennika aktywności przy użyciu innych danych monitorowania, które zostały zebrane przez usługi Azure Monitor.
- Użyj [rejestrowania zapytań](../log-query/log-query-overview.md) do wykonywania złożonych analiz i uzyskiwanie szczegółowych informacji we wpisach dziennika aktywności.

## <a name="connect-to-log-analytics-workspace"></a>Połącz z obszarem roboczym usługi Log Analytics
Dziennik aktywności może być połączona z tylko jednym obszarem roboczym, ale jeden obszar roboczy może być połączony z dziennika aktywności dla wielu subskrypcji w tej samej dzierżawie platformy Azure. Dla kolekcji w wielu dzierżawach, zobacz [zbieranie dzienników aktywności platformy Azure do obszaru roboczego usługi Log Analytics w subskrypcjach w innej usłudze Azure Active Directory dzierżawy](activity-log-collect-tenants.md).

Użyj poniższej procedury do łączenia z dziennika aktywności do obszaru roboczego usługi Log Analytics:

1. Z **obszarów roboczych usługi Log Analytics** menu w witrynie Azure portal wybierz obszar roboczy, aby zebrać dziennik aktywności.
1. W **źródła danych obszaru roboczego** części menu obszaru roboczego, wybierz opcję **dziennik aktywności platformy Azure**.
1. Kliknij subskrypcję, którą chcesz się połączyć.

    ![Obszary robocze](media/activity-log-export/workspaces.png)

1. Kliknij przycisk **Connect** do łączenia z dziennika aktywności w ramach subskrypcji z wybranym obszarem roboczym. Jeśli subskrypcja jest już połączony z innym obszarem roboczym, kliknij przycisk **rozłączenia** aby najpierw odłączeniem go.

    ![Łączenie obszarów roboczych](media/activity-log-export/connect-workspace.png)

## <a name="analyze-in-log-analytics-workspace"></a>Analizowanie w obszarze roboczym usługi Log Analytics
Po nawiązaniu połączenia dziennika aktywności do obszaru roboczego usługi Log Analytics, wpisy zostaną zapisane do obszaru roboczego do tabeli o nazwie **AzureActivity** możliwe do pobrania z [zapytanie dziennika](../log-query/log-query-overview.md). Struktura ta tabela zmienia się zależnie od [kategorii wpis dziennika](activity-logs-overview.md#categories-in-the-activity-log). Zobacz [schemat zdarzeń dziennika aktywności platformy Azure](activity-log-schema.md) opis każdej kategorii.

## <a name="activity-logs-analytics-monitoring-solution"></a>Rozwiązania do monitorowania aktywności analizy dzienników
Rozwiązania do monitorowania usługi Azure Log Analytics obejmuje wiele dziennika zapytań i widoków do analizowania rekordów dziennika aktywności w Twoim obszarze roboczym usługi Log Analytics.

### <a name="install-the-solution"></a>Zainstalować rozwiązanie
Procedura [zainstalować rozwiązanie do monitorowania](../insights/solutions.md#install-a-monitoring-solution) zainstalował **Activity Log Analytics** rozwiązania. Brak konieczności dodatkowej konfiguracji.

### <a name="use-the-solution"></a>Korzystanie z odpowiedniego rozwiązania
Rozwiązania do monitorowania są dostępne z **Monitor** menu w witrynie Azure portal. Wybierz **więcej** w **Insights** sekcji, aby otworzyć **Przegląd** strony z kafelkami rozwiązania. **Dzienników aktywności platformy Azure** Kafelek Wyświetla liczbę **AzureActivity** rekordy, w obszarze roboczym.

![Kafelek Dzienniki aktywności platformy Azure](media/collect-activity-logs/azure-activity-logs-tile.png)


Kliknij przycisk **dzienników aktywności platformy Azure** Kafelek, aby otworzyć **dzienników aktywności platformy Azure** widoku. Widok zawiera części wizualizacji w poniższej tabeli. Każda część zawiera maksymalnie 10 elementów spełniających kryteria tej części dla określonego zakresu czasu. Można uruchomić zapytanie dziennika, które zwraca wszystkie rekordy, klikając **holograficznych** w dolnej części.

![Pulpitu nawigacyjnego dzienników aktywności platformy Azure](media/collect-activity-logs/activity-log-dash.png)

| Części wizualizacji | Opis |
| --- | --- |
| Wpisy dziennika aktywności platformy Azure | Przedstawia wykres słupkowy w górny wpis dziennika aktywności platformy Azure rekordu sumy dla zakresu dat, który wybrano i wyświetla listę najważniejszych wywołań 10 działania. Kliknij wykres słupkowy Aby uruchomić wyszukiwanie w dzienniku dla `AzureActivity`. Kliknij element wywołujący, aby uruchomić wyszukiwanie w dzienniku zwraca wszystkie wpisy dziennika aktywności dla tego elementu. |
| Dzienniki aktywności według stanu | Przedstawia wykres pierścieniowy stanu dziennika aktywności platformy Azure dla wybranego zakresu dat. oraz listę pierwszych rekordów stanu dziesięć. Kliknij wykres, aby uruchomić zapytanie dziennika `AzureActivity | summarize AggregatedValue = count() by ActivityStatus`. Kliknij element stanu, aby uruchomić wyszukiwanie w dzienniku zwraca wszystkie wpisy dziennika aktywności dla tego rekordu stanu. |
| Dzienniki aktywności według zasobu | Zawiera całkowitą liczbę zasobów przy użyciu dzienników aktywności i zawiera listę pierwszych dziesięciu zasobów przy użyciu rekordu jest liczona dla każdego zasobu. Kliknij obszar całkowitej, aby uruchomić wyszukiwanie w dzienniku dla `AzureActivity | summarize AggregatedValue = count() by Resource`, które wyświetla wszystkie zasoby platformy Azure dostępnych do rozwiązania. Kliknij zasób, aby uruchomić zapytanie dziennika, zwracanie wszystkich rekordów działań dla tego zasobu. |
| Dzienniki aktywności przez dostawcę zasobów | Zawiera całkowitą liczbę dostawców zasobów, które generują dzienników aktywności i zawiera listę pierwszych dziesięć. Kliknij obszar całkowitej, aby uruchomić zapytanie dziennika dla `AzureActivity | summarize AggregatedValue = count() by ResourceProvider`, które wyświetla wszystkich dostawców zasobów platformy Azure. Kliknij dostawcę zasobów, aby uruchomić zapytanie dziennika, zwracanie wszystkich rekordów działań dla dostawcy. |

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [dziennika aktywności](activity-logs-overview.md).
- Dowiedz się więcej o [platforma danych usługi Azure Monitor](data-platform.md).
- Użyj [rejestrowania zapytań](../log-query/log-query-overview.md) Aby wyświetlić szczegółowe informacje z dziennika aktywności.
