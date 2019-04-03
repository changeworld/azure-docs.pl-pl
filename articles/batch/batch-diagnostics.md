---
title: Metryki, alerty i dzienniki diagnostyczne — Azure Batch | Dokumentacja firmy Microsoft
description: Rejestrowanie i analizowanie zdarzeń dziennik diagnostyczny dla zasobów konta usługi Azure Batch, np. pulami i zadaniami podrzędnymi.
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 12/05/2018
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 349896a7114fba83d852b8bc066f4f7eb0a070ba
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2019
ms.locfileid: "58850308"
---
# <a name="batch-metrics-alerts-and-logs-for-diagnostic-evaluation-and-monitoring"></a>Metryki usługi Batch, alerty i dzienniki diagnostyczne oceny i monitorowania

 
W tym artykule wyjaśniono, jak monitorować konto usługi Batch przy użyciu funkcji [usługi Azure Monitor](../azure-monitor/overview.md). Usługa Azure Monitor umożliwia zbieranie informacji o [metryki](../azure-monitor/platform/data-platform-metrics.md) i [dzienniki diagnostyczne](../azure-monitor/platform/diagnostic-logs-overview.md) zasobów na koncie usługi Batch. Zbieranie i używanie tych danych na wiele sposobów na monitorowanie konta usługi Batch i diagnozować problemy. Można również skonfigurować [alertów dotyczących metryk](../azure-monitor/platform/alerts-overview.md) Aby otrzymywać powiadomienia, gdy Metryka osiągnie określoną wartość. 

## <a name="batch-metrics"></a>Metryki usługi Batch

Metryki są danych telemetrycznych platformy Azure (nazywanych również liczników wydajności) wyemitowane przez Twoich zasobów platformy Azure, które są używane przez usługę Azure Monitor. Przykładowe metryki na koncie usługi Batch obejmują: Pool Create zdarzeń, liczba węzłów o niskim priorytecie i zadanie ukończenia zdarzenia. 

Zobacz [Lista obsługiwanych metryk usługi Batch](../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts).

Metryki są:

* Domyślnie każde konto usługi Batch bez dodatkowej konfiguracji
* Generowane co minutę
* Nie jest automatycznie zachowywane, ale ma 30-dniową historię stopniowe. Jednak można utrwalić metryki działań w ramach rejestrowania diagnostycznego.

### <a name="view-metrics"></a>Wyświetlanie metryk

Wyświetlanie metryk dla konta usługi Batch w witrynie Azure portal. **Przegląd** strona konta, które domyślnie wyświetla węzła klucza, core i metryk zadań. 

Aby wyświetlić wszystkie metryki konta usługi Batch: 

1. W portalu, kliknij przycisk **wszystkich usług** > **konta usługi Batch**, a następnie kliknij nazwę swojego konta usługi Batch.
2. W obszarze **monitorowanie**, kliknij przycisk **metryki**.
3. Wybierz co najmniej jednej z metryk. Należy wybrać metryki dodatkowych zasobów, przy użyciu **subskrypcje**, **grupy zasobów**, **typ zasobu**, i **zasobów** listy rozwijane.

    ![Metryki usługi Batch](media/batch-diagnostics/metrics-portal.png)

Aby programowo pobrać metryki, należy użyć interfejsów API usługi Azure Monitor. Na przykład zobacz [metryki pobierania usługi Azure Monitor przy użyciu platformy .NET](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/).

## <a name="batch-metric-reliability"></a>Niezawodność metryki usługi Batch

Metryki są przeznaczone do służyć do określania trendów i przeprowadzać analizę danych. Dostarczanie metryk nie jest gwarantowane i może ulec dostarczania poza kolejnością, utraty danych i/lub dublowania. Przy użyciu pojedynczego zdarzenia do funkcji alertu lub wyzwalacz nie jest zalecane. Zobacz [Batch alertów dotyczących metryk](#batch-metric-alerts) sekcji, aby uzyskać więcej informacji na temat sposobu Ustaw progi alertów.

Metryki emitowane w ciągu ostatnich 3 minut mogą nadal być agregowania. Tym horyzoncie czasowym wartości metryk mogą niespójność.

## <a name="batch-metric-alerts"></a>Alerty metryki usługi Batch

Opcjonalnie można skonfigurować w czasie zbliżonym do rzeczywistego *alertów dotyczących metryk* wyzwalacza, gdy wartość określonej metryki przekracza wartość progową, który przypiszesz. Generuje alert [powiadomień](../monitoring-and-diagnostics/insights-alerts-portal.md) możesz wybrać, gdy alert jest "aktywny" (po przekroczeniu progu, i jest spełniony warunek alertu), a także gdy go jest "rozwiązany" (po przekroczeniu progu ponownie i warunek jest nie już spełniany). Alerty oparte na pojedyncze punkty danych nie jest zalecane, ponieważ metryk jest zależna od dostarczania poza kolejnością, utraty danych i/lub dublowania. Alerty należy wprowadzić Użyj wartości progowych, aby uwzględnić problemy powodujące te niespójności.

Na przykład można skonfigurować alertu dotyczącego metryki, gdy Twoje liczba rdzeni o niskim priorytecie znajduje się na określonym poziomie, aby można było zmienić skład puli. Zalecane jest, aby ustawić okres co najmniej 10 minut, gdzie alertów wyzwalacza, jeśli liczba rdzeni średni, niski priorytet spadnie poniżej wartości progowej przez cały okres. Alerty dotyczące okres od 1 do 5 minut, jak metryki nadal może być agregowana jest niezalecane.

Aby skonfigurować alert metryki w portalu:

1. Kliknij pozycję **Wszystkie usługi** > **Konta usługi Batch** i kliknij nazwę odpowiedniego konta usługi Batch.
2. W obszarze **monitorowanie**, kliknij przycisk **reguły alertów** > **Dodaj alert dotyczący metryki**.
3. Wybierz metrykę, warunek alertu (na przykład gdy Metryka przekracza określoną wartość w okresie) i co najmniej jedno powiadomienie.

Możesz również skonfigurować, niemal w czasie rzeczywistym alertu przy użyciu [interfejsu API REST](https://docs.microsoft.com/rest/api/monitor/). Aby uzyskać więcej informacji, zobacz [Przegląd alertów](../azure-monitor/platform/alerts-overview.md)

## <a name="batch-diagnostics"></a>Diagnostyka usługi Batch

Dzienniki diagnostyczne zawierają informacje, o których wyemitowane przez zasoby platformy Azure, które opisują działania każdego zasobu. Dla usługi Batch można zebrać następujące dzienniki:

* **Dzienniki usługi** zdarzenia emitowane przez usługę Azure Batch w okresie istnienia poszczególnych zasobów usługi Batch, takie jak pulą lub zadaniem. 

* **Metryki** dzienników na poziomie konta. 

Ustawienia, aby włączyć zbieranie dzienników diagnostycznych nie są domyślnie włączone. Jawnie włączyć ustawienia diagnostyczne dla każdego konta usługi Batch, które chcesz monitorować.

### <a name="log-destinations"></a>Miejsca docelowe dziennika

Typowy scenariusz polega na wybranie konta usługi Azure Storage jako miejsce docelowe dziennika. Do przechowywania dzienników w usłudze Azure Storage, konto należy utworzyć przed włączeniem zbieranie dzienników. Jeśli konto magazynu jest skojarzony z kontem usługi Batch, można wybrać tego konta, jako miejsce docelowe dziennika. 

Inne opcjonalne miejsca docelowe dla dzienników diagnostycznych:

* Stream zdarzeń dzienniki diagnostyczne usługi Batch do [usługi Azure Event Hub](../event-hubs/event-hubs-what-is-event-hubs.md). Usługa Event Hubs pozyskiwać miliony zdarzeń na sekundę, które można przetworzyć i zapisać za pomocą dowolnego dostawcy analiz w czasie rzeczywistym. 

* Wysyłanie dzienników diagnostycznych do [dzienniki usługi Azure Monitor](../log-analytics/log-analytics-overview.md), którym można analizować je lub wyeksportować je do analizy w usłudze Power BI lub programie Excel.

> [!NOTE]
> Może pociągnąć za sobą dodatkowe koszty przechowywania lub przetwarzania danych dzienników diagnostycznych z usługami platformy Azure. 
>

### <a name="enable-collection-of-batch-diagnostic-logs"></a>Włącz zbieranie dzienniki diagnostyczne usługi Batch

1. W portalu, kliknij przycisk **wszystkich usług** > **konta usługi Batch**, a następnie kliknij nazwę swojego konta usługi Batch.
2. W obszarze **monitorowanie**, kliknij przycisk **dzienniki diagnostyczne** > **Włącz diagnostykę**.
3. W **ustawień diagnostycznych**, wprowadź nazwę dla ustawienia i wybierz miejsce docelowe dziennika (istniejące konto magazynu Centrum zdarzeń i dzienników usługi Azure Monitor). Wybierz jeden lub oba **ServiceLog** i **AllMetrics**.

    Po wybraniu konta magazynu, opcjonalnie ustawić zasady przechowywania. Jeśli nie określisz liczbę dni przechowywania, dane są przechowywane w cyklu życia na koncie magazynu.

4. Kliknij pozycję **Zapisz**.

    ![Diagnostyka usługi Batch](media/batch-diagnostics/diagnostics-portal.png)

Inne opcje, aby włączyć zbieranie danych dziennika obejmują: użycia usługi Azure Monitor w portalu w ustawień diagnostycznych, należy użyć [szablonu usługi Resource Manager](../azure-monitor/platform/diagnostic-logs-stream-template.md), lub za pomocą programu Azure PowerShell lub interfejsu wiersza polecenia platformy Azure. zobacz [zbieranie i używanie dane dzienników z zasobów platformy Azure](../azure-monitor/platform/diagnostic-logs-overview.md#how-to-enable-collection-of-diagnostic-logs).


### <a name="access-diagnostics-logs-in-storage"></a>Dzienniki diagnostyczne dostępu w magazynie

Jeśli możesz zarchiwizować dzienniki diagnostyczne usługi Batch w ramach konta magazynu, kontenera magazynu zostanie utworzony w ramach konta magazynu, zaraz po wystąpieniu zdarzenia powiązane. Obiekty BLOB są tworzone zgodnie z następującym wzorcem nazewnictwa:

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
Każdy plik blob PT1H.json zawiera zdarzenia w formacie JSON, które wystąpiły w ciągu godziny określonej w adresie URL obiektu blob (na przykład h = 12). Zdarzenia występujące w danej chwili są na bieżąco dołączane do pliku PT1H.json. Wartość minut (m = 00) jest zawsze 00, ponieważ dziennik diagnostyczny zdarzenia są dzielone na poszczególne obiekty BLOB na godzinę. (Wszystkie godziny są w formacie UTC).


Aby uzyskać więcej informacji o schemacie dzienniki diagnostyczne na koncie magazynu, zobacz [archiwizowanie dzienników diagnostycznych usługi Azure](../azure-monitor/platform/archive-diagnostic-logs.md#schema-of-diagnostic-logs-in-the-storage-account).

Aby programowo uzyskać dostęp dzienniki na koncie magazynu, przy użyciu interfejsów API magazynu. 

### <a name="service-log-events"></a>Usługa dziennika zdarzeń
Dzienniki platformy Azure Batch usługi, jeżeli pobierane, zawierają zdarzenia emitowane przez usługę Azure Batch w okresie istnienia poszczególnych zasobów usługi Batch, takie jak pulą lub zadaniem. Każde zdarzenie emitowane przy użyciu usługi Batch są rejestrowane w formacie JSON. Na przykład, to jest treść próbkę **zdarzenie utworzenia puli**:

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

Usługa Batch obecnie emituje następujące zdarzenia dziennika usługi. Ta lista nie może być wyczerpujące, ponieważ dodatkowe zdarzenia mogły zostać dodane od czasu ostatniej aktualizacji w tym artykule.

| **Usługa dziennika zdarzeń** |
| --- |
| [Tworzenie puli](batch-pool-create-event.md) |
| [Rozpoczęcia usuwania puli](batch-pool-delete-start-event.md) |
| [Zakończenie usuwania puli](batch-pool-delete-complete-event.md) |
| [Rozpoczęcia zmiany rozmiaru puli](batch-pool-resize-start-event.md) |
| [Pełne rozmiaru puli](batch-pool-resize-complete-event.md) |
| [Zadanie uruchamiania](batch-task-start-event.md) |
| [Zadanie ukończone](batch-task-complete-event.md) |
| [Niepowodzenie zadania](batch-task-fail-event.md) |



## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [interfejsach API i narzędziach usługi Batch](batch-apis-tools.md) umożliwiających tworzenie rozwiązań usługi Batch.
* Dowiedz się więcej o [monitorowania rozwiązań usługi Batch](monitoring-overview.md).
