---
title: Metryki, alerty i dzienniki diagnostyczne dla partii zadań Azure | Dokumenty Microsoft
description: Rejestruj i analizowanie dzienników diagnostycznych zdarzeń dla zasobów konta partii zadań Azure, takich jak pule i zadania.
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 04/05/2018
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: e64d272695c4e47c972df040d1c1c2a63bf3dddd
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
ms.locfileid: "31788198"
---
# <a name="batch-metrics-alerts-and-logs-for-diagnostic-evaluation-and-monitoring"></a>Metryki partii, alerty i dzienniki diagnostyczne oceny i monitorowania

W tym artykule wyjaśniono, jak monitorować konto usługi partia zadań przy użyciu funkcji [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md). Zbiera dane monitora Azure [metryki](../monitoring-and-diagnostics/monitoring-overview-metrics.md) i [dzienników diagnostycznych](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) dla zasobów w ramach konta usługi partia zadań. Zbieranie i korzystać z tych danych na wiele sposobów, aby monitorować konta partii zadań i diagnozowanie problemów. Można również skonfigurować [metryki alerty](../monitoring-and-diagnostics/monitoring-overview-alerts.md#alerts-on-azure-monitor-data) Aby otrzymywać powiadomienia, gdy metryki osiąga określoną wartość. 

## <a name="batch-metrics"></a>Metryki usługi partia zadań

Metryki są danych telemetrycznych platformy Azure (nazywanych również liczniki wydajności) emitowane przez zasobów platformy Azure, które są używane przez usługę Azure Monitor. Przykład metryki w ramach konta usługi partia zadań obejmują: zdarzenia tworzenie puli, liczba węzłów niskiego priorytetu i zdarzenia ukończenia zadania. 

Zobacz [listę obsługiwanych metryki partii](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftbatchbatchaccounts).

Metryki są:

* Domyślnie włączona w każdym konta usługi partia zadań bez dodatkowej konfiguracji
* Generowane co minutę.
* Nie utrwalone automatycznie, ale w przeszłości stopniowego 30-dniową. Można ją utrwalić metryki działań w ramach [rejestrowania diagnostycznego](#work-with-diagnostic-logs).

### <a name="view-metrics"></a>Wyświetlaj metryki

Wyświetlaj metryki dla konta wsadowego w portalu Azure. **Omówienie** strony konta domyślnie wyświetlane węzła klucza, podstawowe i metryki zadań. 

Aby wyświetlić wszystkie metryki konta usługi partia zadań: 

1. W portalu, kliknij przycisk **wszystkie usługi** > **partii kont**, a następnie kliknij nazwę konta wsadowego.
2. W obszarze **monitorowanie**, kliknij przycisk **metryki**.
3. Wybierz co najmniej jeden metryki. Należy wybrać metryki dodatkowych zasobów za pomocą **subskrypcje**, **grupy zasobów**, **typu zasobu**, i **zasobów** listę rozwijaną.

    ![Metryki usługi partia zadań](media/batch-diagnostics/metrics-portal.png)

Aby pobrać metryki programowo, należy użyć Monitora interfejsów API usługi Azure. Na przykład, zobacz [metryki pobrać Monitor Azure z platformą .NET](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/).

## <a name="batch-metric-alerts"></a>Alerty metryki usługi partia zadań

Opcjonalnie możesz skonfigurować niemal w czasie rzeczywistym *metryki alerty* który wyzwalane w razie wartości progowej, którą należy przypisać przecina wartość określonej metryki. Generuje alert [powiadomień](../monitoring-and-diagnostics/insights-alerts-portal.md) możesz wybrać, gdy alert jest "aktywny" (po przekroczeniu progu i spełnieniu warunku alertu), a także gdy "Problemu" (po przekroczeniu progu ponownie i spełnieniu warunku nie już spełniany). 

Na przykład można skonfigurować metryki alert, gdy liczba rdzeni o niskim priorytecie znajduje się na poziom, który umożliwia dostosowanie kompozycji sieci pule.

Aby skonfigurować alert metryki w portalu:

1. Kliknij pozycję **Wszystkie usługi** > **Konta usługi Batch** i kliknij nazwę odpowiedniego konta usługi Batch.
2. W obszarze **monitorowanie**, kliknij przycisk **reguły alertów** > **Dodaj alert metryki**.
3. Wybierz metrykę, stan alertu (na przykład gdy metrykę przekracza określoną wartość w okresie) i co najmniej jedno powiadomienie.

Można również skonfigurować near w czasie rzeczywistym alertu using [interfejsu API REST](). Aby uzyskać więcej informacji, zobacz [użyj nowszej alerty metryki dla usługi Azure w portalu Azure](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md)
## <a name="batch-diagnostics"></a>Diagnostyka usługi partia zadań

Dzienniki diagnostyczne zawierają informacje emitowane przez zasobów Azure, które opisano każdego zasobu. Dla partii można zebrać następujące dzienniki:

* **Dzienniki usługi** emitowane przez usługi partia zadań Azure przez cały okres istnienia pojedynczego zasobu partii zdarzeń, takich jak puli lub zadania. 

* **Metryki** dzienniki na poziomie konta. 

Ustawienia, aby włączyć zbieranie dzienników diagnostycznych nie są domyślnie włączone. Jawnie włączyć ustawienia diagnostyki dla każdego konta usługi partia zadań, które chcesz monitorować.

### <a name="log-destinations"></a>Dziennik miejsc docelowych

Typowy scenariusz polega na wybraniu konta usługi Azure Storage jako miejsce docelowe dziennika. Do przechowywania dzienników w usłudze Azure Storage, należy utworzyć konto przed włączeniem zbierania dzienników. Jeśli konto magazynu jest skojarzony z Twoim kontem usługi partia zadań, jako miejsce docelowe dziennika można wybrać tego konta. 

Inne opcjonalne miejsc docelowych dla dzienników diagnostycznych:

* Strumienia zdarzeń dzienników diagnostycznych partii [Azure Event Hub](../event-hubs/event-hubs-what-is-event-hubs.md). Centra zdarzeń może obsługiwać miliony zdarzeń na sekundę, który można przekształcić i magazynu przy użyciu dowolnego dostawcy analiz w czasie rzeczywistym. 

* Wysyłanie dzienników diagnostycznych do [Azure Log Analytics](../log-analytics/log-analytics-overview.md), gdzie można analizować je w portalu Operations Management Suite (OMS) lub wyeksportować je do analizy w programie Excel lub usługi Power BI.

> [!NOTE]
> Może pociągnąć za sobą dodatkowe koszty przechowywania lub przetwarzać dane dzienników diagnostycznych z usługami Azure. 
>

### <a name="enable-collection-of-batch-diagnostic-logs"></a>Włącz zbieranie dzienników diagnostycznych partii

1. W portalu, kliknij przycisk **wszystkie usługi** > **partii kont**, a następnie kliknij nazwę konta wsadowego.
2. W obszarze **monitorowanie**, kliknij przycisk **dzienniki diagnostyczne** > **Włącz diagnostykę**.
3. W **ustawień diagnostycznych**, wprowadź nazwę dla ustawienia i wybierz miejsce docelowe dziennika (istniejącego magazynu konto Centrum zdarzeń i analizy dzienników). Wybierz jednego lub obu **ServiceLog** i **AllMetrics**.

    Po wybraniu konta magazynu, opcjonalnie ustawić zasady przechowywania. Jeśli nie określisz liczbę dni okresu przechowywania danych są przechowywane w okresie obowiązywania konta magazynu.

4. Kliknij pozycję **Zapisz**.

    ![Diagnostyka usługi partia zadań](media/batch-diagnostics/diagnostics-portal.png)

Inne opcje, aby włączyć zbieranie danych dziennika obejmują: Użyj monitora Azure w portalu w celu konfigurowania ustawień diagnostycznych, użyj [szablonu usługi Resource Manager](../monitoring-and-diagnostics/monitoring-enable-diagnostic-logs-using-template.md), lub użyj programu Azure PowerShell lub wiersza polecenia platformy Azure. zobacz [zbierania i wykorzystywania danych dziennika z zasobów platformy Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-resource-diagnostic-logs).


### <a name="access-diagnostics-logs-in-storage"></a>Diagnostyka dostępu rejestruje się w magazynie

Jeśli archiwum partii dzienników diagnostycznych na konto magazynu, kontenera magazynu zostanie utworzony na koncie magazynu zaraz po wystąpieniu zdarzenia powiązane. Obiekty BLOB są tworzone zgodnie ze skonfigurowanym następujący wzór nazewnictwa:

```
insights-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/
RESOURCEGROUPS/{resource group name}/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/{batch account name}/y={four-digit numeric year}/
m={two-digit numeric month}/d={two-digit numeric day}/
h={two-digit 24-hour clock hour}/m=00/PT1H.json
```
Przykład:

```
insights-metrics-pt1m/resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/
RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/MYBATCHACCOUNT/y=2018/m=03/d=05/h=22/m=00/PT1H.json
```
Zdarzenia w formacie JSON, które wystąpiły w ciągu godziny określonego w adresie URL obiektu blob zawiera każdego pliku blob PT1H.json (na przykład h = 12). Zdarzenia występujące w danej chwili są na bieżąco dołączane do pliku PT1H.json. Wartości minutowe (m = 00) jest zawsze 00, ponieważ dziennik diagnostyczny zdarzenia są podzielone na poszczególne obiekty BLOB na godzinę. (Wszystkie godziny są w formacie UTC).


Aby uzyskać więcej informacji o schemacie dzienników diagnostycznych na koncie magazynu, zobacz [dzienników diagnostycznych platformy Azure archiwum](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md#schema-of-diagnostic-logs-in-the-storage-account).

Aby programowy dostęp dzienniki na koncie magazynu, należy użyć interfejsów API magazynu. 

### <a name="service-log-events"></a>Usługa dziennika zdarzeń
Azure dzienniki usługi partii, jeśli zbierane, zawiera zdarzenia emitowane przez usługi partia zadań Azure przez cały okres istnienia pojedynczego zasobu puli lub zadania wsadowego. Każde zdarzenie emitowane przez partię jest rejestrowane w formacie JSON. Na przykład to jest treść próbki **puli utworzyć zdarzenia**:

```json
{
    "poolId": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Small",
    "cloudServiceConfiguration": {
        "osFamily": "5",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "resizeTimeout": "300000",
    "targetDedicatedComputeNodes": 2,
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoscale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

Usługa partia zadań emituje obecnie następujące zdarzenia logowania do usługi. Ta lista nie może być pełne, ponieważ dodatkowe zdarzenia mogły zostać dodane od momentu ostatniej aktualizacji w tym artykule.

| **Usługa dziennika zdarzeń** |
| --- |
| [Tworzenie puli](batch-pool-create-event.md) |
| [Start usuwania puli](batch-pool-delete-start-event.md) |
| [Usuwanie puli ukończone](batch-pool-delete-complete-event.md) |
| [Początkowy rozmiar puli](batch-pool-resize-start-event.md) |
| [Zmiana rozmiaru puli ukończone](batch-pool-resize-complete-event.md) |
| [Rozpoczęcia zadania](batch-task-start-event.md) |
| [Zadania ukończone](batch-task-complete-event.md) |
| [Niepowodzenie zadania](batch-task-fail-event.md) |



## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [interfejsach API i narzędziach usługi Batch](batch-apis-tools.md) umożliwiających tworzenie rozwiązań usługi Batch.
* Dowiedz się więcej o [monitorowanie rozwiązań partii](monitoring-overview.md).
