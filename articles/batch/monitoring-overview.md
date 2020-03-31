---
title: Monitorowanie usługi Azure Batch | Dokumenty firmy Microsoft
description: Dowiedz się więcej o usługach monitorowania platformy Azure, metrykach, dziennikach diagnostycznych i innych funkcjach monitorowania usługi Azure Batch.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.workload: na
ms.date: 04/05/2018
ms.author: labrenne
ms.openlocfilehash: d251229c522bd4d6daca894513eaae14d244d8a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025864"
---
# <a name="monitor-batch-solutions"></a>Monitorowanie rozwiązań usługi Batch

Platforma Azure i usługa Batch zapewniają szereg usług, narzędzi i interfejsów API do monitorowania rozwiązań wsadowych. Ten artykuł omówienie pomaga wybrać podejście monitorowania, które pasuje do Twoich potrzeb.

Aby zapoznać się ze składnikami i usługami platformy Azure dostępnymi do monitorowania zasobów platformy Azure, zobacz [Monitorowanie aplikacji i zasobów platformy Azure.](../monitoring-and-diagnostics/monitoring-overview.md)

## <a name="subscription-level-monitoring"></a>Monitorowanie na poziomie subskrypcji

Na poziomie subskrypcji, który obejmuje konta usługi Batch, [dziennik aktywności platformy Azure](../azure-monitor/platform/platform-logs-overview.md) zbiera dane zdarzeń operacyjnych w kilku [kategoriach.](../azure-monitor/platform/activity-log-view.md#categories-in-the-activity-log)

W szczególności w przypadku kont usługi Batch dziennik aktywności zbiera zdarzenia związane z tworzeniem i usuwaniem kont oraz zarządzaniem kluczami.

Jednym ze sposobów pobierania zdarzeń z dziennika aktywności jest użycie witryny Azure portal. Kliknij pozycję**Dziennik aktywności** **wszystkie usługi** > . Lub kwerendy dla zdarzeń przy użyciu interfejsu wiersza polecenia platformy Azure, polecenia cmdlet programu PowerShell lub interfejsu API REST usługi Azure Monitor. Można również wyeksportować dziennik aktywności lub skonfigurować [alerty dziennika aktywności](../monitoring-and-diagnostics/monitoring-activity-log-alerts-new-experience.md).

## <a name="batch-account-level-monitoring"></a>Monitorowanie na poziomie konta wsadowego

Monitoruj każde konto usługi Batch przy użyciu funkcji [usługi Azure Monitor](../azure-monitor/overview.md). Usługa Azure Monitor zbiera [metryki](../azure-monitor/platform/data-platform-metrics.md) i opcjonalnie [dzienniki diagnostyczne](../azure-monitor/platform/platform-logs-overview.md) dla zasobów o zakresie na poziomie konta usługi Batch, takich jak pule, zadania i zadania. Zbieraj i konsumuj te dane ręcznie lub programowo, aby monitorować działania na koncie usługi Batch i diagnozować problemy. Aby uzyskać szczegółowe informacje, zobacz [metryki partii, alerty i dzienniki do oceny diagnostycznej i monitorowania.](batch-diagnostics.md)
 
> [!NOTE]
> Metryki są domyślnie dostępne na koncie usługi Batch bez dodatkowej konfiguracji i mają 30-dniową historię kroczącą. Należy włączyć rejestrowanie diagnostyczne dla konta usługi Batch i może ponieść dodatkowe koszty przechowywania lub przetwarzania danych dziennika diagnostycznego. 

## <a name="batch-resource-monitoring"></a>Monitorowanie zasobów wsadowych

W aplikacjach usługi Batch użyj interfejsów API usługi Batch do monitorowania lub wykonywania zapytań o stan zasobów, w tym zadań, zadań, węzłów i pul. Przykład:

* [Zliczanie zadań i węzłów obliczeniowych według stanu](batch-get-resource-counts.md)
* [Efektywne tworzenie kwerend w celu efektywnego wyświetlenia zasobów usługi Batch](batch-efficient-list-queries.md)
* [Tworzenie zależności zadań](batch-task-dependencies.md)
* Używanie [zadania menedżera zadań](/rest/api/batchservice/job/add#jobmanagertask)
* Monitorowanie [stanu zadania](/rest/api/batchservice/task/list#taskstate)
* Monitorowanie [stanu węzła](/rest/api/batchservice/computenode/list#computenodestate)
* Monitorowanie [stanu puli](/rest/api/batchservice/pool/get#poolstate)
* Monitorowanie [użycia puli na koncie](/rest/api/batchservice/pool/listusagemetrics)
* [Zliczanie węzłów puli według stanu](/rest/api/batchservice/account/listpoolnodecounts)

## <a name="vm-performance-counters-and-application-monitoring"></a>Liczniki wydajności maszyn wirtualnych i monitorowanie aplikacji

* [Usługa Application Insights](../azure-monitor/app/app-insights-overview.md) to usługa platformy Azure, której można używać do programowego monitorowania dostępności, wydajności i użycia zadań i zadań wsadowych. Łatwe wykonywanie liczników wydajności z węzłów obliczeniowych (maszyn wirtualnych) i informacji niestandardowych dla zadań poza maszynami wirtualnymi. 

  Na przykład zobacz [Monitorowanie i debugowanie aplikacji Batch .NET za pomocą usługi Application Insights](monitor-application-insights.md) i towarzyszącego mu [przykładu kodu](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights).

  > [!NOTE]
  > Korzystanie ze usługi Application Insights może ponieść dodatkowe koszty. Zobacz [opcje cenowe](https://azure.microsoft.com/pricing/details/application-insights/). 
  >

* [Wytwórcy partii](https://github.com/Azure/BatchExplorer) to bezpłatne, bogate, funkcjonalne, autonomiczne narzędzie klienta ułatwiające tworzenie, debugowanie i monitorowanie aplikacji usługi Azure Batch. Pobierz [pakiet instalacyjny](https://azure.github.io/BatchExplorer/) dla komputerów Mac lub systemu Linux albo Windows. Opcjonalnie skonfiguruj rozwiązanie batch do [wyświetlania danych usługi Application Insights,](https://github.com/Azure/batch-insights) takich jak liczniki wydajności maszyn wirtualnych w Eksploratorze wsadowym.


## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [interfejsach API i narzędziach usługi Batch](batch-apis-tools.md) umożliwiających tworzenie rozwiązań usługi Batch.
* Dowiedz się więcej o [rejestrowaniu diagnostycznym](batch-diagnostics.md) za pomocą usługi Batch.