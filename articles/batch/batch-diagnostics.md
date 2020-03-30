---
title: Metryki, alerty i dzienniki diagnostyczne — usługa Azure Batch | Dokumenty firmy Microsoft
description: Rejestrowanie i analizowanie zdarzeń dziennika diagnostycznego dla zasobów konta usługi Azure Batch, takich jak pule i zadania.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78254858"
---
# <a name="batch-metrics-alerts-and-logs-for-diagnostic-evaluation-and-monitoring"></a>Metryki wsadowe, alerty i dzienniki do oceny diagnostycznej i monitorowania

 
W tym artykule wyjaśniono, jak monitorować konto usługi Batch przy użyciu funkcji [usługi Azure Monitor](../azure-monitor/overview.md). Usługa Azure Monitor zbiera [metryki](../azure-monitor/platform/data-platform-metrics.md) i [dzienniki diagnostyczne](../azure-monitor/platform/platform-logs-overview.md) dla zasobów na koncie usługi Batch. Zbieraj i korzystaj z tych danych na różne sposoby, aby monitorować konto usługi Batch i diagnozować problemy. Można również skonfigurować [alerty metryki,](../azure-monitor/platform/alerts-overview.md) dzięki czemu otrzymywać powiadomienia, gdy metryka osiągnie określoną wartość. 

## <a name="batch-metrics"></a>Metryki partii

Metryki są dane telemetryczne platformy Azure (nazywane również liczniki wydajności) emitowane przez zasoby platformy Azure, które są używane przez usługę Azure Monitor. Przykładowe metryki na koncie usługi Batch obejmują: Pula Utwórz zdarzenia, Liczba węzłów o niskim priorytecie i Zdarzenia wykonania zadania. 

Zobacz [listę obsługiwanych danych usługi Batch](../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts).

Metryki to:

* Domyślnie włączone na każdym koncie usługi Batch bez dodatkowej konfiguracji
* Generowane co 1 minutę
* Nie utrwalił się automatycznie, ale mają 30-dniową historię toczenia. Metryki aktywności można utrwalić w ramach rejestrowania diagnostycznego.

### <a name="view-metrics"></a>Wyświetlanie metryk

Wyświetlanie metryk dla konta usługi Batch w witrynie Azure portal. Na stronie **Przegląd** konta domyślnie są wyświetlane metryki węzła kluczowego, rdzenia i zadania. 

Aby wyświetlić wszystkie dane konta usługi Batch: 

1. W portalu kliknij pozycję **Wszystkie konta** > **usługi Usługi wsadowe**, a następnie kliknij nazwę konta usługi Batch.
2. W obszarze **Monitorowanie**kliknij **pozycję Metryki**.
3. Wybierz jedną lub więcej metryk. Jeśli chcesz, wybierz dodatkowe metryki zasobów za pomocą listy rozwijanej **Subskrypcje,** **Grupa zasobów,** **Typ zasobu**i **Zasób.**
    * W przypadku metryk opartych na liczbie (takich jak "Liczba rdzeni dedykowanych" lub "Liczba węzłów o niskim priorytecie") użyj agregacji "Średnia". W przypadku metryk opartych na zdarzeniach (takich jak "Rozmiar rozmiaru puli complete events" należy użyć agregacji "Liczba".

> [!WARNING]
> Nie należy używać agregacji "Suma", która sumuje wartości wszystkich punktów danych otrzymanych w okresie wykresu
> 
> 

    ![Batch metrics](media/batch-diagnostics/metrics-portal.png)

Aby programowo pobierać metryki, użyj interfejsów API usługi Azure Monitor. Na przykład zobacz [Pobieranie metryk usługi Azure Monitor za pomocą platformy .NET](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/).

## <a name="batch-metric-reliability"></a>Niezawodność metryki partii

Metryki są przeznaczone do wykorzystania w trendach i analizie danych. Dostawa metryczna nie jest gwarantowana i podlega dostawie poza zamówienie, utracie danych i/lub powielaniu. Nie zaleca się używania pojedynczych zdarzeń do alertów lub wyzwalania funkcji. Zobacz [Batch metric alerts](#batch-metric-alerts) sekcji, aby uzyskać więcej informacji na temat ustawiania progów alertów.

Metryki emitowane w ciągu ostatnich 3 minut mogą nadal agregować. W tym przedziale czasowym wartości metryki mogą być zaniżone.

## <a name="batch-metric-alerts"></a>Alerty metryki partii

Opcjonalnie należy skonfigurować *alerty w* czasie zbliżonym do rzeczywistego, które wyzwalają, gdy wartość określonej metryki przekroczy przypisany próg. Alert generuje [powiadomienie,](../monitoring-and-diagnostics/insights-alerts-portal.md) które można wybrać, gdy alert jest "Aktywowany" (gdy próg zostanie przekroczony i warunek alertu jest spełniony), a także gdy jest "Rozwiązany" (gdy próg zostanie przekroczony ponownie i warunek nie jest już spełniony). Alerty oparte na pojedynczych punktach danych nie są zalecane, ponieważ metryki podlegają dostarczaniu poza kolejności, utracie danych i/lub powielaniu. Alerty powinny korzystać z progów w celu uwzględnienia tych niespójności.

Na przykład można skonfigurować alert metryki, gdy liczba rdzeni o niskim priorytecie spadnie do pewnego poziomu, dzięki czemu można dostosować skład pul. Zaleca się ustawienie okresu 10 lub więcej minut, w którym alerty wyzwalają, jeśli średnia liczba rdzeni o niskim priorytecie spadnie poniżej wartości progowej dla całego okresu. Nie zaleca się alertów w okresie 1-5 minut, ponieważ metryki mogą nadal agregować.

Aby skonfigurować alert metryki w portalu:

1. Kliknij **pozycję Wszystkie konta** > **usługi Usługi wsadowe**, a następnie kliknij nazwę konta usługi Batch.
2. W obszarze **Monitorowanie**kliknij pozycję **Reguły** > alertów**Dodaj alert metryczny**.
3. Wybierz metrykę, warunek alertu (na przykład, gdy metryka przekracza określoną wartość w okresie) i jedno lub więcej powiadomień.

Alert w czasie zbliżonym do rzeczywistego można również skonfigurować za pomocą [interfejsu API REST](https://docs.microsoft.com/rest/api/monitor/). Aby uzyskać więcej informacji, zobacz [Omówienie alertów](../azure-monitor/platform/alerts-overview.md). Aby uwzględnić informacje o zadaniach, zadaniach lub danych dotyczących puli w alertach, zobacz informacje o zapytaniach wyszukiwania w obszarze Odpowiadanie na [zdarzenia za pomocą alertów usługi Azure Monitor](../azure-monitor/learn/tutorial-response.md)

## <a name="batch-diagnostics"></a>Diagnostyka usługi Batch

Dzienniki diagnostyczne zawierają informacje emitowane przez zasoby platformy Azure, które opisują działanie każdego zasobu. W przypadku usługi Batch można zbierać następujące dzienniki:

* **Zdarzenia dzienników usługi** emitowane przez usługę Azure Batch w okresie istnienia pojedynczego zasobu usługi Batch, takiego jak pula lub zadanie. 

* **Dzienniki metryk** na poziomie konta. 

Ustawienia umożliwiające zbieranie dzienników diagnostycznych nie są domyślnie włączone. Jawnie włącz ustawienia diagnostyczne dla każdego konta usługi Batch, które chcesz monitorować.

### <a name="log-destinations"></a>Rejestrowanie miejsc docelowych

Typowym scenariuszem jest wybranie konta usługi Azure Storage jako miejsca docelowego dziennika. Aby przechowywać dzienniki w usłudze Azure Storage, należy utworzyć konto przed włączeniem kolekcji dzienników. Jeśli konto magazynu zostało skojarzone z kontem usługi Batch, można wybrać to konto jako miejsce docelowe dziennika. 

Inne opcjonalne miejsca docelowe dla dzienników diagnostycznych:

* Zdarzenia dziennika diagnostycznego usługi Stream Batch do [usługi Azure Event Hub](../event-hubs/event-hubs-what-is-event-hubs.md). Centra zdarzeń mogą pozyskiwania milionów zdarzeń na sekundę, które można następnie przekształcić i przechowywać przy użyciu dowolnego dostawcy analizy w czasie rzeczywistym. 

* Wysyłaj dzienniki diagnostyczne do [dzienników usługi Azure Monitor,](../log-analytics/log-analytics-overview.md)gdzie można je analizować lub eksportować do analizy w usłudze Power BI lub Excel.

> [!NOTE]
> Użytkownik może ponieść dodatkowe koszty związane z przechowywaniem lub przetwarzaniem danych dziennika diagnostycznego w usługach platformy Azure. 
>

### <a name="enable-collection-of-batch-diagnostic-logs"></a>Włącz zbieranie dzienników diagnostycznych partii

1. W portalu kliknij pozycję **Wszystkie konta** > **usługi Usługi wsadowe**, a następnie kliknij nazwę konta usługi Batch.
2. W obszarze **Monitorowanie**kliknij pozycję **Dzienniki diagnostyczne** > **Włącz diagnostykę**.
3. W **ustawieniach diagnostycznych**wprowadź nazwę ustawienia i wybierz miejsce docelowe dziennika (istniejące konto magazynu, centrum zdarzeń lub dzienniki usługi Azure Monitor). Wybierz jeden lub oba **ServiceLog** i **AllMetrics**.

    Po wybraniu konta magazynu opcjonalnie ustaw zasady przechowywania. Jeśli nie określisz liczby dni przechowywania, dane są przechowywane przez cały okres użytkowania konta magazynu.

4. Kliknij przycisk **Zapisz**.

    ![Diagnostyka usługi Batch](media/batch-diagnostics/diagnostics-portal.png)

Inne opcje, aby włączyć zbieranie dzienników obejmują: użyj usługi Azure Monitor w portalu, aby skonfigurować ustawienia diagnostyczne, użyj [szablonu Usługi Resource Manager](../azure-monitor/platform/diagnostic-settings-template.md)lub użyj programu Azure PowerShell lub interfejsu wiersza polecenia platformy Azure. Zobacz [Zbieranie i korzystanie z danych dziennika z zasobów platformy Azure](../azure-monitor/platform/platform-logs-overview.md).


### <a name="access-diagnostics-logs-in-storage"></a>Dzienniki diagnostyki dostępu w magazynie

Jeśli archiwum dzienników diagnostycznych partii na koncie magazynu, kontener magazynu jest tworzony na koncie magazynu, jak tylko wystąpi powiązane zdarzenie. Obiekty BLOB są tworzone zgodnie z następującym wzorcem nazewnictwa:

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
Każdy `PT1H.json` plik obiektu blob zawiera zdarzenia w formacie JSON, które wystąpiły `h=12`w ciągu godziny określonej w adresie URL obiektu blob (na przykład ). W bieżącej godzinie zdarzenia są dołączane do pliku w miarę `PT1H.json` ich występowania. Wartość minut`m=00`( ) `00`jest zawsze , ponieważ zdarzenia dziennika diagnostycznego są podzielone na poszczególne obiekty blob na godzinę. (Wszystkie czasy są w czasie UTC.)

Poniżej znajduje się `PoolResizeCompleteEvent` przykład `PT1H.json` wpisu w pliku dziennika. Zawiera informacje o bieżącej i docelowej liczbie węzłów dedykowanych i o niskim priorytecie, a także o godzinie rozpoczęcia i zakończenia operacji:

```
{ "Tenant": "65298bc2729a4c93b11c00ad7e660501", "time": "2019-08-22T20:59:13.5698778Z", "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/BATCHACCOUNTS/MYBATCHACCOUNT/", "category": "ServiceLog", "operationName": "PoolResizeCompleteEvent", "operationVersion": "2017-06-01", "properties": {"id":"MYPOOLID","nodeDeallocationOption":"Requeue","currentDedicatedNodes":10,"targetDedicatedNodes":100,"currentLowPriorityNodes":0,"targetLowPriorityNodes":0,"enableAutoScale":false,"isAutoPool":false,"startTime":"2019-08-22 20:50:59.522","endTime":"2019-08-22 20:59:12.489","resultCode":"Success","resultMessage":"The operation succeeded"}}
```

Aby uzyskać więcej informacji na temat schematu dzienników diagnostycznych na koncie magazynu, zobacz [Archiwizuj dzienniki diagnostyczne platformy Azure](../azure-monitor/platform/resource-logs-collect-storage.md#schema-of-platform-logs-in-storage-account). Aby programowo uzyskać dostęp do dzienników na koncie magazynu, użyj interfejsów API magazynu. 

### <a name="service-log-events"></a>Zdarzenia dziennika usługi
Dzienniki usługi azure batch service, jeśli są zbierane, zawierają zdarzenia emitowane przez usługę Azure Batch w okresie istnienia poszczególnych zasobów usługi Batch, takich jak pula lub zadanie. Każde zdarzenie emitowane przez partię jest rejestrowane w formacie JSON. Na przykład jest to treść zdarzenia **tworzenia puli**próbek:

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

Usługa Batch obecnie emituje następujące zdarzenia dziennika usługi. Ta lista może nie być wyczerpująca, ponieważ dodatkowe zdarzenia mogły zostać dodane od czasu ostatniej aktualizacji tego artykułu.

| **Zdarzenia dziennika usługi** |
| --- |
| [Tworzenie puli](batch-pool-create-event.md) |
| [Początek usuwania puli](batch-pool-delete-start-event.md) |
| [Ukończono usuwanie puli](batch-pool-delete-complete-event.md) |
| [Rozpoczęcie ponownego rozmiaru puli](batch-pool-resize-start-event.md) |
| [Rozmiar rozmiaru puli został ukończony](batch-pool-resize-complete-event.md) |
| [Rozpoczęcie zadania](batch-task-start-event.md) |
| [Zadanie ukończone](batch-task-complete-event.md) |
| [Niepowodzenie zadania](batch-task-fail-event.md) |



## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [interfejsach API i narzędziach usługi Batch](batch-apis-tools.md) umożliwiających tworzenie rozwiązań usługi Batch.
* Dowiedz się więcej o [monitorowaniu rozwiązań wsadowych](monitoring-overview.md).
