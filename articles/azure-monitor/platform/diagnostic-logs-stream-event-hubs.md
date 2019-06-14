---
title: Stream dzienniki diagnostyczne platformy Azure do Centrum zdarzeń
description: Dowiedz się, jak przesyłać strumieniowo dzienniki diagnostyczne platformy Azure do Centrum zdarzeń.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/25/2018
ms.author: johnkem
ms.subservice: ''
ms.openlocfilehash: b5299af375646e7759d0770139df2cd6d7ce105c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60237705"
---
# <a name="stream-azure-diagnostic-logs-to-an-event-hub"></a>Stream dzienniki diagnostyczne platformy Azure do Centrum zdarzeń
**[Dzienniki diagnostyczne platformy Azure](diagnostic-logs-overview.md)**  może być przesyłany strumieniowo w czasie zbliżonym do rzeczywistego do dowolnej aplikacji przy użyciu wbudowanych opcji "Eksportuj do usługi Event Hubs" w portalu lub przez włączenie identyfikator reguły autoryzacji Centrum zdarzeń w ustawieniu diagnostycznym za pośrednictwem platformy Azure Polecenia cmdlet programu PowerShell lub Azure interfejsu wiersza polecenia.

## <a name="what-you-can-do-with-diagnostics-logs-and-event-hubs"></a>Co można zrobić za pomocą dzienników diagnostyki i usługi Event Hubs
Oto kilka sposobów, można na przykład możliwość przesyłania strumieniowego dla dzienników diagnostycznych:

* **Stream dzienniki 3 systemach innych firm rejestrowania i dane telemetryczne** — przesyłanie strumieniowe wszystkich dzienników diagnostycznych do jednego Centrum zdarzeń do potoku danych dziennika narzędzia analizy rozwiązania SIEM lub dziennika innych firm.
* **Aby wyświetlić kondycję usługi, przesyłania strumieniowego "Ścieżka aktywna" dane do usługi Power BI** — przy użyciu usługi Event Hubs, Stream Analytics i Power BI, można łatwo przekształcać dane diagnostyczne w prawie w czasie rzeczywistym szczegółowych informacji dotyczących usług platformy Azure. [Ten artykuł dokumentacji zawiera wszechstronne omówienie sposobu konfigurowania usługi Event Hubs, przetwarzanie danych za pomocą usługi Stream Analytics i używaj usługi Power BI jako dane wyjściowe](../../stream-analytics/stream-analytics-power-bi-dashboard.md). Poniżej przedstawiono kilka porad dotyczących pobierania skonfigurować przy użyciu dzienników diagnostycznych:

  * Centrum zdarzeń dla kategorii dzienników diagnostycznych jest tworzona automatycznie po zaznaczeniu opcji w portalu lub ją włączyć za pomocą programu PowerShell, tak, aby wybrać Centrum zdarzeń w przestrzeni nazw o nazwie, która rozpoczyna się od **insights -** .
  * Poniższy kod SQL jest przykładowe zapytanie usługi Stream Analytics, którego można użyć do analizowania wszystkie dane dziennika w tabeli usługi Power BI:

    ```sql
    SELECT
    records.ArrayValue.[Properties you want to track]
    INTO
    [OutputSourceName – the Power BI source]
    FROM
    [InputSourceName] AS e
    CROSS APPLY GetArrayElements(e.records) AS records
    ```

* **Tworzenie niestandardowej telemetrii i rejestrowania platformy** — Jeśli masz już telemetrii niestandardowej platformy lub są po prostu, myśl o zbudowania jej o wysokim stopniu skalowalności, publikowania/subskrybowania charakter usługi Event hubs umożliwia elastyczne pozyskanie dzienników diagnostycznych. [Zobacz Przewodnik Dan Rosanova do korzystania z usługi Event Hubs w skali globalnej platformy danych telemetrycznych w tym miejscu](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="enable-streaming-of-diagnostic-logs"></a>Włączanie przesyłania strumieniowego dzienników diagnostycznych

Aby umożliwić przesyłanie strumieniowe dzienników diagnostycznych programowo, w portalu lub przy użyciu [interfejsów API REST usługi Azure Monitor](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings). W obu przypadkach można utworzyć ustawienie diagnostyczne, w którym należy określić przestrzeni nazw usługi Event Hubs i kategorie dzienników i metryk, które chcesz wysłać do przestrzeni nazw. Centrum zdarzeń jest tworzony w przestrzeni nazw dla każdej kategorii dziennika, w której zostanie włączone. Diagnostyka **kategoria dziennika** jest typem dziennika, który może zbierać zasobu.

> [!WARNING]
> Strumieniowe przesyłanie dzienników diagnostycznych z zasobów obliczeniowych (na przykład maszyny wirtualne lub usługi Service Fabric) i włączenie [wymaga innego zestawu kroków](../../azure-monitor/platform/diagnostics-extension-stream-event-hubs.md).

Usługa Event Hubs, których nazw nie muszą znajdować się w tej samej subskrypcji co zasób emitowane dzienniki, jak długo użytkownik, który konfiguruje ustawienie ma odpowiedni dostęp RBAC do subskrypcji i obu subskrypcji są częścią tej samej dzierżawie usługi AAD.

> [!NOTE]
> Wysyłanie metryk wielowymiarowych za pomocą ustawień diagnostycznych nie jest obecnie obsługiwane. Metryki wielowymiarowe są eksportowane jako spłaszczone metryki jednowymiarowe z wartościami zagregowanymi we wszystkich wymiarach.
>
> *Na przykład*: metrykę „Komunikaty przychodzące” w centrum zdarzeń można przeglądać i przedstawiać na wykresie na poziomie pojedynczej kolejki. Jednak w przypadku eksportowania za pomocą ustawień diagnostycznych metryka ta jest przedstawiana jako wszystkie komunikaty przychodzące we wszystkich kolejkach w centrum zdarzeń.
>
>

## <a name="stream-diagnostic-logs-using-the-portal"></a>Dzienniki diagnostyczne Stream przy użyciu portalu

1. W portalu, przejdź do usługi Azure Monitor, a następnie kliknij pozycję **ustawień diagnostycznych**

    ![Monitorowanie sekcji usługi Azure Monitor](media/diagnostic-logs-stream-event-hubs/diagnostic-settings-blade.png)

2. Opcjonalnie filtrować listę według grupy zasobów lub typ zasobu, a następnie kliknij na zasób, dla którego chcesz skonfigurować ustawienie diagnostyczne.

3. Jeśli ustawienia nie istnieje w zasobie wybrany, zostanie wyświetlony monit, aby utworzyć ustawienie. Kliknij przycisk "Włącz diagnostykę."

   ![Dodaj ustawienie diagnostyczne — nie istniejące ustawienia](media/diagnostic-logs-stream-event-hubs/diagnostic-settings-none.png)

   W przypadku ustawienia istniejące na zasób zostanie wyświetlona lista ustawień już skonfigurowany dla tego zasobu. Kliknij pozycję "Dodaj ustawienie diagnostyczne".

   ![Dodaj ustawienie diagnostyczne — istniejące ustawienia](media/diagnostic-logs-stream-event-hubs/diagnostic-settings-multiple.png)

3. Nadaj nazwę ustawienia i zaznacz pole **Stream do usługi event hub**, następnie wybierz przestrzeń nazw usługi Event Hubs.

   ![Dodaj ustawienie diagnostyczne — istniejące ustawienia](media/diagnostic-logs-stream-event-hubs/diagnostic-settings-configure.png)

   Wybrana przestrzeń nazw będzie gdzie (jeżeli jest to pierwszy przesyłania strumieniowego dzienników diagnostycznych czasu) lub przesyłane strumieniowo do Centrum zdarzeń (jeśli istnieją już zasoby, które są przesyłania strumieniowego dziennika kategorii, aby ta przestrzeń nazw), a zasada definiuje uprawnienia, przesyłanie strumieniowe mechanizm ma. Już dziś przesyłanie strumieniowe do Centrum zdarzeń musi mieć uprawnienia zarządzania, wysyłania i nasłuchiwania. Można utworzyć lub zmodyfikować zasady dostępu udostępnionego w przestrzeni nazw usługi Event Hubs w portalu na karcie Konfigurowanie dla przestrzeni nazw usługi. Aby zaktualizować jeden z tych ustawień diagnostycznych, klient musi mieć uprawnienie ListKey w regule autoryzacji usługi Event Hubs. Można również Opcjonalnie określ nazwę Centrum zdarzeń. Jeśli określisz nazwę Centrum zdarzeń, dzienniki są kierowane do tego Centrum zdarzeń, a nie do nowo utworzonego zdarzenia koncentratora dla każdej kategorii dziennika.

4. Kliknij pozycję **Zapisz**.

Po kilku chwilach nowe ustawienie jest wyświetlane na liście ustawień dla tego zasobu, i jak tylko nowe dane zdarzenia są generowane dzienniki diagnostyczne są przesyłane strumieniowo do tego Centrum zdarzeń.

### <a name="via-powershell-cmdlets"></a>Za pomocą poleceń cmdlet programu PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Przesyłania strumieniowego za pośrednictwem [poleceń cmdlet programu PowerShell Azure](../../azure-monitor/platform/powershell-quickstart-samples.md), możesz użyć `Set-AzDiagnosticSetting` polecenia cmdlet z następującymi parametrami:

```powershell
Set-AzDiagnosticSetting -ResourceId [your resource ID] -EventHubAuthorizationRuleId [your Event Hub namespace auth rule ID] -Enabled $true
```

Identyfikator reguły autoryzacji Centrum zdarzeń jest ciągiem o następującym formacie: `{Event Hub namespace resource ID}/authorizationrules/{key name}`, na przykład `/subscriptions/{subscription ID}/resourceGroups/{resource group}/providers/Microsoft.EventHub/namespaces/{Event Hub namespace}/authorizationrules/RootManageSharedAccessKey`. Nie można obecnie wybierz nazwę Centrum określonego zdarzenia przy użyciu programu PowerShell.

### <a name="via-azure-cli"></a>Za pomocą wiersza polecenia platformy Azure

Do przesyłania strumieniowego za pośrednictwem [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest), możesz użyć [tworzenie az monitor diagnostic-settings](https://docs.microsoft.com/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az-monitor-diagnostic-settings-create) polecenia.

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --event-hub <event hub name> \
    --event-hub-rule <event hub rule ID> \
    --resource <target resource object ID> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true
    }
    ]'
```

Dodatkowe kategorie można dodawać do dzienników diagnostycznych, dodając słowników do tablicy JSON przekazywane jako `--logs` parametru.

`--event-hub-rule` Argument ma taki sam format jak identyfikator reguły autoryzacji Centrum zdarzeń zgodnie z objaśnieniem dla polecenia Cmdlet programu PowerShell.

## <a name="how-do-i-consume-the-log-data-from-event-hubs"></a>Jak korzystać z danych dzienników z usługi Event Hubs?

Poniżej przedstawiono przykładowe dane wyjściowe z usługi Event Hubs:

```json
{
    "records": [
        {
            "time": "2016-07-15T18:00:22.6235064Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330013509921957/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Error",
            "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T17:58:55.048482Z",
                "endTime": "2016-07-15T18:00:22.4109204Z",
                "status": "Failed",
                "code": "BadGateway",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330013509921957",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "29a9862f-969b-4c70-90c4-dfbdc814e413",
                    "clientTrackingId": "08587330013509921958"
                }
            }
        },
        {
            "time": "2016-07-15T18:01:15.7532989Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330012106702630/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Information",
            "operationName": "Microsoft.Logic/workflows/workflowActionStarted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T18:01:15.5828115Z",
                "status": "Running",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330012106702630",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "042fb72c-7bd4-439e-89eb-3cf4409d429e",
                    "clientTrackingId": "08587330012106702632"
                }
            }
        }
    ]
}
```

| Nazwa elementu | Opis |
| --- | --- |
| Rekordy |Tablica wszystkich zdarzeń dziennika, w tym ładunku. |
| time |Czas, w którym wystąpiło zdarzenie. |
| category |Kategoria dziennika dla tego zdarzenia. |
| resourceId |Identyfikator zasobu zasób, który wygenerował zdarzenie. |
| operationName |Nazwa operacji. |
| poziom |Opcjonalny. Wskazuje poziom dziennika zdarzeń. |
| properties |Właściwości zdarzenia. |

Można wyświetlić listę wszystkich dostawców zasobów, które obsługują przesyłanie strumieniowe do usługi Event Hubs [tutaj](diagnostic-logs-overview.md).

## <a name="stream-data-from-compute-resources"></a>Stream dane z zasobów obliczeniowych

Obejmuje także strumieniowe przesyłanie dzienników diagnostycznych z zasobów obliczeniowych, przy użyciu agenta usługi Windows Azure Diagnostics. [Ten artykuł](../../azure-monitor/platform/diagnostics-extension-stream-event-hubs.md) jak to skonfigurować.

## <a name="next-steps"></a>Kolejne kroki

* [Stream dzienniki usługi Azure Active Directory z usługą Azure Monitor](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)
* [Dowiedz się więcej o dziennikach diagnostycznych platformy Azure](diagnostic-logs-overview.md)
* [Rozpoczynanie pracy z usługą Event Hubs](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

