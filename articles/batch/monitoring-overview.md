---
title: Monitorowanie usługi Azure Batch | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o usług monitorowania platformy Azure, metryk, dzienników diagnostycznych i inne funkcje monitorowania dla usługi Azure Batch.
services: batch
author: laurenhughes
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.workload: na
ms.date: 04/05/2018
ms.author: lahugh
ms.openlocfilehash: b0243b37f725fc977337b72998d610e9bda71a86
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128859"
---
# <a name="monitor-batch-solutions"></a>Monitorowanie rozwiązań usługi Batch

Portal Azure i usługi Batch zapewniają szeroką gamę usług, narzędzi i interfejsów API do monitorowania rozwiązań usługi Batch. Ten artykuł pomaga w wyborze metody monitorowania, który spełnia Twoje potrzeby.

Aby zapoznać się z omówieniem składników platformy Azure i usług, które są dostępne do monitorowania zasobów platformy Azure, zobacz [monitorowania platformy Azure, aplikacje i zasoby](../monitoring-and-diagnostics/monitoring-overview.md).

## <a name="subscription-level-monitoring"></a>Monitorowanie na poziomie subskrypcji

Na poziomie subskrypcji, która zawiera konta usługi Batch, [dziennika aktywności platformy Azure](../azure-monitor/platform/activity-logs-overview.md) zbiera dane zdarzeń operacyjnych w [kilka kategorii](../azure-monitor/platform/activity-logs-overview.md#categories-in-the-activity-log).

Konta usługi Batch w szczególności dziennika aktywności zbiera zdarzenia związane z zarządzaniem tworzenia i usuwania i klucz konta.

Jednym ze sposobów, aby pobrać zdarzenia z dziennika aktywności jest za pomocą witryny Azure portal. Kliknij przycisk **wszystkich usług** > **dziennika aktywności**. Lub zapytanie dla zdarzenia przy użyciu wiersza polecenia platformy Azure, poleceń cmdlet programu PowerShell lub interfejsu API REST usługi Azure Monitor. Można również wyeksportować dziennik aktywności lub skonfigurować [alertów dziennika aktywności](../monitoring-and-diagnostics/monitoring-activity-log-alerts-new-experience.md).

## <a name="batch-account-level-monitoring"></a>Monitorowanie na poziomie konta usługi Batch

Każde konto usługi Batch za pomocą funkcji monitorowania [usługi Azure Monitor](../azure-monitor/overview.md). Usługa Azure Monitor umożliwia zbieranie informacji o [metryki](../azure-monitor/platform/data-platform-metrics.md) i opcjonalnie [dzienniki diagnostyczne](../azure-monitor/platform/diagnostic-logs-overview.md) zasobów należących do zakresu na poziomie konta usługi Batch, takie jak pule, zadania i zadania. Zbieranie i używanie tych danych, ręcznie lub programowo do monitorowania działań na koncie usługi Batch i diagnozowanie problemów. Aby uzyskać więcej informacji, zobacz [Batch metryki, alerty i dzienniki diagnostyczne oceny i monitorowania](batch-diagnostics.md).
 
> [!NOTE]
> Metryki są dostępne, domyślnie na koncie usługi Batch bez dodatkowej konfiguracji i mają 30-dniową historię stopniowe. Należy włączyć rejestrowanie diagnostyczne dla konta usługi Batch i może pociągnąć za sobą dodatkowe koszty przechowywania lub przetwarzania danych dzienników diagnostycznych. 

## <a name="batch-resource-monitoring"></a>Monitorowanie zasobów usługi Batch

W aplikacji usługi Batch należy użyć interfejsów API usługi Batch do monitorowania lub wykonać zapytanie o stan usługi zasobów, w tym zadań, zadań, węzły i pul. Na przykład:

* [Liczba zadań i obliczeń węzły według stanu](batch-get-resource-counts.md)
* [Wydajny sposób tworzyć zapytania w celu wyświetlenia listy zasobów usługi Batch](batch-efficient-list-queries.md)
* [Tworzenie zależności między zadaniami](batch-task-dependencies.md)
* Użyj [zadanie podrzędne Menedżera zadań](/rest/api/batchservice/job/add#jobmanagertask)
* Monitor [zadań stanu](/rest/api/batchservice/task/list#taskstate)
* Monitor [stan węzła](/rest/api/batchservice/computenode/list#computenodestate)
* Monitor [puli stanu](/rest/api/batchservice/pool/get#poolstate)
* Monitor [puli użycie w ramach konta](/rest/api/batchservice/pool/listusagemetrics)
* [Liczba węzłów puli według stanu](/rest/api/batchservice/account/listpoolnodecounts)

## <a name="vm-performance-counters-and-application-monitoring"></a>Liczniki wydajności maszyn wirtualnych i monitorowania aplikacji

* [Usługa Application Insights](../azure-monitor/app/app-insights-overview.md) jest usługą platformy Azure służących do programowego monitorować dostępność, wydajność i użycie zadań wsadowych oraz zadań. Łatwo get liczników wydajności z węzłów obliczeniowych (maszyn wirtualnych) i niestandardowych informacji do zadań zniżki w stosunku do maszyn wirtualnych. 

  Aby uzyskać przykład, zobacz [monitorowania i debugowania aplikacji .NET usługi Batch za pomocą usługi Application Insights](monitor-application-insights.md) i towarzyszących [przykładowy kod](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights).

  > [!NOTE]
  > Może pociągnąć za sobą dodatkowe koszty używania usługi Application Insights. Zobacz [opcje cennika](https://azure.microsoft.com/pricing/details/application-insights/). 
  >

* [Batch Explorer](https://github.com/Azure/BatchExplorer) to bezpłatne, bogate w funkcje, autonomiczne narzędzie klienta pomagające tworzyć, debugować i monitorować aplikacje usługi Azure Batch. Pobierz [pakiet instalacyjny](https://azure.github.io/BatchExplorer/) dla komputerów Mac lub systemu Linux albo Windows. Opcjonalnie skonfiguruj rozwiązania usługi Batch w taki sposób, aby [Wyświetl dane usługi Application Insights](https://github.com/Azure/batch-insights) takich jak liczniki wydajności maszyn wirtualnych w Eksploratorze usługi Batch.


## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [interfejsach API i narzędziach usługi Batch](batch-apis-tools.md) umożliwiających tworzenie rozwiązań usługi Batch.
* Dowiedz się więcej o [rejestrowania diagnostycznego](batch-diagnostics.md) przy użyciu usługi Batch.