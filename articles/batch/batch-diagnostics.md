---
title: Metryki, alerty i dzienniki diagnostyczne — Azure Batch | Microsoft Docs
description: Rejestrowanie i analizowanie zdarzeń dziennik diagnostyczny dla zasobów konta usługi Azure Batch, np. pulami i zadaniami podrzędnymi.
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 12/05/2018
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 68d5976a5a79dbde88b7f80b02b39793ffc86de9
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2020
ms.locfileid: "78254858"
---
# <a name="batch-metrics-alerts-and-logs-for-diagnostic-evaluation-and-monitoring"></a>Metryki usługi Batch, alerty i dzienniki diagnostyczne oceny i monitorowania

 
W tym artykule wyjaśniono, jak monitorować konto usługi Batch przy użyciu funkcji programu [Azure monitor](../azure-monitor/overview.md). Azure Monitor zbiera [metryki](../azure-monitor/platform/data-platform-metrics.md) i [dzienniki diagnostyczne](../azure-monitor/platform/platform-logs-overview.md) dla zasobów na koncie usługi Batch. Zbieranie i używanie tych danych na wiele sposobów na monitorowanie konta usługi Batch i diagnozować problemy. Możesz również skonfigurować [alerty metryk](../azure-monitor/platform/alerts-overview.md) , aby otrzymywać powiadomienia, gdy Metryka osiągnie określoną wartość. 

## <a name="batch-metrics"></a>Metryki usługi Batch

Metryki są danych telemetrycznych platformy Azure (nazywanych również liczników wydajności) wyemitowane przez Twoich zasobów platformy Azure, które są używane przez usługę Azure Monitor. Przykładowe metryki na koncie usługi Batch obejmują: zdarzenia tworzenia puli, liczba węzłów o niskim priorytecie i zdarzenia ukończone zadania. 

Zapoznaj się z [listą obsługiwanych metryk wsadowych](../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts).

Metryki są:

* Domyślnie każde konto usługi Batch bez dodatkowej konfiguracji
* Generowane co minutę
* Nie jest automatycznie zachowywane, ale ma 30-dniową historię stopniowe. Możesz utrwalać metryki działania w ramach rejestrowania diagnostycznego.

### <a name="view-metrics"></a>Wyświetlanie metryk

Wyświetlanie metryk dla konta usługi Batch w witrynie Azure portal. Na stronie **Przegląd** konta domyślnie są wyświetlane metryki węzeł klucza, rdzeń i zadanie. 

Aby wyświetlić wszystkie metryki konta usługi Batch: 

1. W portalu kliknij kolejno pozycje **wszystkie usługi** > **Batch konta**, a następnie kliknij nazwę swojego konta w usłudze Batch.
2. W obszarze **monitorowanie**kliknij pozycję **metryki**.
3. Wybierz co najmniej jednej z metryk. Jeśli chcesz, wybierz pozycję dodatkowe metryki zasobów przy użyciu opcji **subskrypcje**, **Grupa zasobów**, **Typ zasobu**i listy rozwijane **zasobów** .
    * W przypadku metryk opartych na liczbie (takich jak "dedykowana liczba rdzeni" lub "Liczba węzłów o niskim priorytecie") Użyj agregacji "Średnia". W przypadku metryk opartych na zdarzeniach (takich jak "zmiany rozmiaru puli zakończonych zdarzeń") Użyj agregacji "Count".

> [!WARNING]
> Nie należy używać agregacji "Sum", która dodaje wartości wszystkich punktów danych odebranych w okresie wykresu
> 
> 

    ![Batch metrics](media/batch-diagnostics/metrics-portal.png)

Aby programowo pobrać metryki, należy użyć interfejsów API usługi Azure Monitor. Na przykład zobacz [pobieranie metryk Azure monitor przy użyciu platformy .NET](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/).

## <a name="batch-metric-reliability"></a>Niezawodność metryki usługi Batch

Metryki są przeznaczone do służyć do określania trendów i przeprowadzać analizę danych. Dostarczanie metryk nie jest gwarantowane i może ulec dostarczania poza kolejnością, utraty danych i/lub dublowania. Przy użyciu pojedynczego zdarzenia do funkcji alertu lub wyzwalacz nie jest zalecane. Więcej informacji na temat ustawiania progów alertów można znaleźć w sekcji [alerty metryk usługi Batch](#batch-metric-alerts) .

Metryki emitowane w ciągu ostatnich 3 minut mogą nadal być agregowania. Tym horyzoncie czasowym wartości metryk mogą niespójność.

## <a name="batch-metric-alerts"></a>Alerty metryki usługi Batch

Opcjonalnie można skonfigurować *alerty metryk* niemal w czasie rzeczywistym, które wyzwalają, gdy wartość określonej metryki przekroczy przypisany próg. Alert generuje [powiadomienie](../monitoring-and-diagnostics/insights-alerts-portal.md) wybierane, gdy alert jest "aktywowany" (gdy próg zostanie przekroczony i spełniony jest warunek alertu), a także wtedy, gdy jest "rozwiązany" (gdy próg zostanie przekroczony, a warunek nie jest już spełniany). Alerty oparte na pojedyncze punkty danych nie jest zalecane, ponieważ metryk jest zależna od dostarczania poza kolejnością, utraty danych i/lub dublowania. Alerty należy wprowadzić Użyj wartości progowych, aby uwzględnić problemy powodujące te niespójności.

Na przykład można skonfigurować alertu dotyczącego metryki, gdy Twoje liczba rdzeni o niskim priorytecie znajduje się na określonym poziomie, aby można było zmienić skład puli. Zalecane jest, aby ustawić okres co najmniej 10 minut, gdzie alertów wyzwalacza, jeśli liczba rdzeni średni, niski priorytet spadnie poniżej wartości progowej przez cały okres. Alerty dotyczące okres od 1 do 5 minut, jak metryki nadal może być agregowana jest niezalecane.

Aby skonfigurować alert metryki w portalu:

1. Kliknij pozycję **Wszystkie usługi** > **Konta usługi Batch** i kliknij nazwę odpowiedniego konta usługi Batch.
2. W obszarze **monitorowanie**kliknij pozycję **reguły alertów** > **Dodaj alert metryki**.
3. Wybierz metrykę, warunek alertu (na przykład gdy Metryka przekracza określoną wartość w okresie) i co najmniej jedno powiadomienie.

Możesz również skonfigurować alert niemal w czasie rzeczywistym za pomocą [interfejsu API REST](https://docs.microsoft.com/rest/api/monitor/). Aby uzyskać więcej informacji, zobacz [Omówienie alertów](../azure-monitor/platform/alerts-overview.md). Aby uwzględnić w alertach informacje dotyczące zadania, zadania lub puli, zobacz informacje na temat wyszukiwania zapytań w odpowiedzi na [zdarzenia z alertami Azure monitor](../azure-monitor/learn/tutorial-response.md)

## <a name="batch-diagnostics"></a>Diagnostyka usługi Batch

Dzienniki diagnostyczne zawierają informacje, o których wyemitowane przez zasoby platformy Azure, które opisują działania każdego zasobu. Dla usługi Batch można zebrać następujące dzienniki:

* **Usługa rejestruje** zdarzenia, które są emitowane przez usługę Azure Batch w okresie istnienia pojedynczego zasobu usługi Batch, takiego jak Pula lub zadanie. 

* Dzienniki **metryk** na poziomie konta. 

Ustawienia, aby włączyć zbieranie dzienników diagnostycznych nie są domyślnie włączone. Jawnie włączyć ustawienia diagnostyczne dla każdego konta usługi Batch, które chcesz monitorować.

### <a name="log-destinations"></a>Miejsca docelowe dziennika

Typowy scenariusz polega na wybranie konta usługi Azure Storage jako miejsce docelowe dziennika. Do przechowywania dzienników w usłudze Azure Storage, konto należy utworzyć przed włączeniem zbieranie dzienników. Jeśli konto magazynu jest skojarzony z kontem usługi Batch, można wybrać tego konta, jako miejsce docelowe dziennika. 

Inne opcjonalne miejsca docelowe dla dzienników diagnostycznych:

* Zdarzenia dziennika diagnostycznego usługi Stream Part strumienia do [centrum zdarzeń platformy Azure](../event-hubs/event-hubs-what-is-event-hubs.md). Usługa Event Hubs pozyskiwać miliony zdarzeń na sekundę, które można przetworzyć i zapisać za pomocą dowolnego dostawcy analiz w czasie rzeczywistym. 

* Wyślij dzienniki diagnostyczne do [dzienników Azure monitor](../log-analytics/log-analytics-overview.md), gdzie można je analizować lub wyeksportować do analizy w programie Power BI lub Excel.

> [!NOTE]
> Może pociągnąć za sobą dodatkowe koszty przechowywania lub przetwarzania danych dzienników diagnostycznych z usługami platformy Azure. 
>

### <a name="enable-collection-of-batch-diagnostic-logs"></a>Włącz zbieranie dzienniki diagnostyczne usługi Batch

1. W portalu kliknij kolejno pozycje **wszystkie usługi** > **Batch konta**, a następnie kliknij nazwę swojego konta w usłudze Batch.
2. W obszarze **monitorowanie**kliknij pozycję **dzienniki diagnostyczne** , > **włączyć diagnostykę**.
3. W obszarze **Ustawienia diagnostyczne**wprowadź nazwę ustawienia i wybierz miejsce docelowe dziennika (istniejące konto magazynu, centrum zdarzeń lub dzienniki Azure monitor). Wybierz albo zarówno **ServiceLog** , jak i **AllMetrics**.

    Po wybraniu konta magazynu, opcjonalnie ustawić zasady przechowywania. Jeśli nie określisz liczbę dni przechowywania, dane są przechowywane w cyklu życia na koncie magazynu.

4. Kliknij przycisk **Save** (Zapisz).

    ![Diagnostyka usługi Batch](media/batch-diagnostics/diagnostics-portal.png)

Inne opcje włączania zbierania dzienników: Użyj Azure Monitor w portalu, aby skonfigurować ustawienia diagnostyczne, użyć [szablonu Menedżer zasobów](../azure-monitor/platform/diagnostic-settings-template.md)lub użyć Azure PowerShell lub interfejsu wiersza polecenia platformy Azure. Zobacz [zbieranie danych dzienników z zasobów platformy Azure i korzystanie z nich](../azure-monitor/platform/platform-logs-overview.md).


### <a name="access-diagnostics-logs-in-storage"></a>Dzienniki diagnostyczne dostępu w magazynie

Jeśli możesz zarchiwizować dzienniki diagnostyczne usługi Batch w ramach konta magazynu, kontenera magazynu zostanie utworzony w ramach konta magazynu, zaraz po wystąpieniu zdarzenia powiązane. Obiekty BLOB są tworzone zgodnie z następującym wzorcem nazewnictwa:

```
insights-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/
RESOURCEGROUPS/{resource group name}/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/{Batch account name}/y={four-digit numeric year}/
m={two-digit numeric month}/d={two-digit numeric day}/
h={two-digit 24-hour clock hour}/m=00/PT1H.json
```
Przykład:

```
insights-metrics-pt1m/resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/
RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/MYBATCHACCOUNT/y=2018/m=03/d=05/h=22/m=00/PT1H.json
```
Każdy `PT1H.json` plik BLOB zawiera zdarzenia w formacie JSON, które wystąpiły w ciągu godziny określonej w adresie URL obiektu BLOB (na przykład `h=12`). W ciągu tej godziny zdarzenia są dołączane do pliku `PT1H.json` w miarę ich występowania. Wartość minuta (`m=00`) jest zawsze `00`, ponieważ zdarzenia dzienników diagnostycznych są dzielone na pojedyncze obiekty blob na godzinę. (Wszystkie godziny są w formacie UTC).

Poniżej znajduje się przykład wpisu `PoolResizeCompleteEvent` w pliku dziennika `PT1H.json`. Zawiera informacje o bieżącej i docelowej liczbie węzłów dedykowanych i o niskim priorytecie oraz o godzinie rozpoczęcia i zakończenia operacji:

```
{ "Tenant": "65298bc2729a4c93b11c00ad7e660501", "time": "2019-08-22T20:59:13.5698778Z", "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/BATCHACCOUNTS/MYBATCHACCOUNT/", "category": "ServiceLog", "operationName": "PoolResizeCompleteEvent", "operationVersion": "2017-06-01", "properties": {"id":"MYPOOLID","nodeDeallocationOption":"Requeue","currentDedicatedNodes":10,"targetDedicatedNodes":100,"currentLowPriorityNodes":0,"targetLowPriorityNodes":0,"enableAutoScale":false,"isAutoPool":false,"startTime":"2019-08-22 20:50:59.522","endTime":"2019-08-22 20:59:12.489","resultCode":"Success","resultMessage":"The operation succeeded"}}
```

Aby uzyskać więcej informacji na temat schematu dzienników diagnostycznych na koncie magazynu, zobacz [Archiwizowanie dzienników diagnostycznych platformy Azure](../azure-monitor/platform/resource-logs-collect-storage.md#schema-of-platform-logs-in-storage-account). Aby programowo uzyskać dostęp dzienniki na koncie magazynu, przy użyciu interfejsów API magazynu. 

### <a name="service-log-events"></a>Usługa dziennika zdarzeń
Dzienniki platformy Azure Batch usługi, jeżeli pobierane, zawierają zdarzenia emitowane przez usługę Azure Batch w okresie istnienia poszczególnych zasobów usługi Batch, takie jak pulą lub zadaniem. Każde zdarzenie emitowane przy użyciu usługi Batch są rejestrowane w formacie JSON. Na przykład jest to treść przykładowego **zdarzenia tworzenia puli**:

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

| **Zdarzenia dziennika usługi** |
| --- |
| [Tworzenie puli](batch-pool-create-event.md) |
| [Początek usuwania puli](batch-pool-delete-start-event.md) |
| [Ukończono Usuwanie puli](batch-pool-delete-complete-event.md) |
| [Początek zmiany rozmiaru puli](batch-pool-resize-start-event.md) |
| [Ukończono Zmienianie rozmiaru puli](batch-pool-resize-complete-event.md) |
| [Rozpoczęcie zadania](batch-task-start-event.md) |
| [Zadanie zakończone](batch-task-complete-event.md) |
| [Niepowodzenie zadania](batch-task-fail-event.md) |



## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [interfejsach API i narzędziach usługi Batch](batch-apis-tools.md) umożliwiających tworzenie rozwiązań usługi Batch.
* Dowiedz się więcej o [monitorowaniu rozwiązań wsadowych](monitoring-overview.md).
