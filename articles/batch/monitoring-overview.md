---
title: Monitorowanie partii zadań Azure | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o monitorowania usług platformy Azure, metryki dzienników diagnostycznych i inne funkcje monitorowania dla partii zadań Azure.
services: batch
author: dlepow
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.workload: na
ms.date: 04/05/2018
ms.author: danlep
ms.openlocfilehash: 29ac86ed5c744d37150b0f1b2db17f60306fe77e
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="monitor-batch-solutions"></a>Monitorowanie rozwiązań usługi Batch

Azure i usługa partia zadań zapewnia szereg usług, narzędzia i interfejsy API służące do monitorowania rozwiązań partii. Ten artykuł pomoże Ci wybrać monitorowania podejście, który odpowiada Twoim potrzebom.

Omówienie usługi Azure składników i usług dostępnych do monitorowania zasobów platformy Azure, zobacz [Azure monitorowania aplikacji i zasobów](../monitoring-and-diagnostics/monitoring-overview.md).

## <a name="subscription-level-monitoring"></a>Monitorowanie poziom subskrypcji

Na poziomie subskrypcji, łącznie z konta usługi partia zadań, [dziennik aktywności platformy Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) zbiera dane zdarzeń operacyjnych w [kilka kategorii](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md#categories-in-the-activity-log).

Dla konta usługi partia zadań w szczególności dziennika aktywności zbiera zdarzenia związane z zarządzaniem tworzenie i usuwanie oraz klucza konta.

Jednym ze sposobów pobrać zdarzenia z dziennika aktywności jest korzystanie z portalu Azure. Kliknij przycisk **wszystkie usługi** > **dziennik aktywności**. Lub kwerendę dla zdarzeń przy użyciu wiersza polecenia platformy Azure, poleceń cmdlet programu PowerShell lub interfejsu API REST Azure monitora. Można również wyeksportować dziennik aktywności lub skonfigurować [alertów dotyczących działań w dzienniku](../monitoring-and-diagnostics/monitoring-activity-log-alerts-new-experience.md).

## <a name="batch-account-level-monitoring"></a>Monitorowanie poziomie konta usługi partia zadań

Każde konto usługi partia zadań za pomocą funkcji monitorowania [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md). Zbiera dane monitora Azure [metryki](../monitoring-and-diagnostics/monitoring-overview-metrics.md) i opcjonalnie [dzienników diagnostycznych](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) zasobów zakres na poziomie konta usługi partia zadań, takich jak pule, zadań i zadań. Zbieranie i używanie tych danych, ręcznie lub programowo monitorowanie działań na koncie usługi partia zadań i diagnozowanie problemów. Aby uzyskać więcej informacji, zobacz [partii metryki, alerty i dzienniki diagnostyczne oceny i monitorowania](batch-diagnostics.md).
 
> [!NOTE]
> Metryki domyślnie są dostępne w ramach konta usługi partia zadań bez dodatkowej konfiguracji i mają historię stopniowego 30 dni. Należy włączyć rejestrowanie diagnostyczne dla konta usługi partia zadań i może pociągnąć za sobą dodatkowe koszty przechowywania lub przetwarzać dane dzienników diagnostycznych. 

## <a name="batch-resource-monitoring"></a>Monitorowanie zasobów usługi partia zadań

W aplikacjach partii przy użyciu interfejsów API partii monitorowania lub zbadać stan tym zadań, zadań, węzły i pul zasobów. Na przykład:

* [Liczba zadań według stanu](batch-get-task-counts.md)
* [Wydajnie tworzyć zapytania do listy zasobów usługi partia zadań](batch-efficient-list-queries.md)
* [Tworzenie zależności zadań](batch-task-dependencies.md)
* Użyj [zadania Menedżera zadań](/rest/api/batchservice/job/add#jobmanagertask)
* Monitor [zadań stanu](/rest/api/batchservice/task/list#taskstate)
* Monitor [stan węzła](/rest/api/batchservice/computenode/list#computenodestate)
* Monitor [puli stanu](/rest/api/batchservice/pool/get#poolstate)
* Monitor [puli do użycia w ramach konta](/rest/api/batchservice/pool/listusagemetrics)
* [Liczba węzłów puli według stanu](/rest/api/batchservice/account/listpoolnodecounts)

## <a name="vm-performance-counters-and-application-monitoring"></a>Liczniki wydajności maszyny Wirtualnej i monitorowania aplikacji

* [Usługa Application Insights](../application-insights/app-insights-overview.md) jest usługą platformy Azure, można programowo monitorowanie dostępności, wydajności i użycia partii zadań i zadań. Łatwo get liczników wydajności z obliczeniowe węzły (maszyny wirtualne) i niestandardowych informacji dla zadania znajdujące się na nich maszyn wirtualnych. 

  Na przykład zobacz [monitora i debugowania aplikacji .NET partii z usługą Application Insights](monitor-application-insights.md) oraz towarzyszące [przykładowy kod](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights).

  > [!NOTE]
  > Może pociągnąć za sobą dodatkowe koszty używania usługi Application Insights. Zobacz [cennik opcje](https://azure.microsoft.com/pricing/details/application-insights/). 
  >

* [BatchLabs](https://github.com/Azure/BatchLabs) to bezpłatne, bogate w funkcje, autonomiczne narzędzie klienta pomagające tworzyć, debugować i monitorować aplikacje usługi Azure Batch. Pobierz [pakiet instalacyjny](https://azure.github.io/BatchLabs/) dla komputerów Mac lub systemu Linux albo Windows. Opcjonalnie skonfiguruj rozwiązanie partii, aby [dane usługi Application Insights](https://github.com/Azure/batch-insights) takich jak wirtualna liczniki wydajności w BatchLabs.


## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [interfejsach API i narzędziach usługi Batch](batch-apis-tools.md) umożliwiających tworzenie rozwiązań usługi Batch.
* Dowiedz się więcej o [rejestrowania diagnostycznego](batch-diagnostics.md) z partii.