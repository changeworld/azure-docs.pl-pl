---
title: Strumieniowanie dzienników platformy Azure do centrum zdarzeń
description: Dowiedz się, jak przesyłać strumieniowo dzienniki zasobów platformy Azure do centrum zdarzeń, aby wysyłać dane do systemów zewnętrznych, takich jak siemie i inne rozwiązania do analizy dzienników.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/15/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 72341b6da0068ba4b7e3f53b08e6015cafb70f09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658918"
---
# <a name="stream-azure-platform-logs-to-azure-event-hubs"></a>Strumieniuj dzienniki platformy Azure do usługi Azure Event Hubs
[Dzienniki platformy](platform-logs-overview.md) na platformie Azure, w tym dzienniki aktywności platformy Azure i dzienniki zasobów, zawierają szczegółowe informacje diagnostyczne i inspekcji dla zasobów platformy Azure i platformy Azure, na których zależą.  W tym artykule opisano dzienniki platformy przesyłania strumieniowego do centrów zdarzeń w celu wysyłania danych do systemów zewnętrznych, takich jak siemie i inne rozwiązania analizy dzienników.


## <a name="what-you-can-do-with-platform-logs-sent-to-an-event-hub"></a>Co można zrobić z dziennikami platformy wysyłanymi do centrum zdarzeń
Dzienniki platformy strumienia na platformie Azure do centrów zdarzeń, aby zapewnić następujące funkcje:

* **Dzienniki strumienia do systemów rejestrowania i telemetrii innych firm** — przesyłaj strumieniowo wszystkie dzienniki platformy do centrum zdarzeń, aby potokować dane dziennika do narzędzia SIEM lub analizy dzienników innej firmy.
  
* **Tworzenie niestandardowej platformy telemetryczne i rejestrowania** — wysoce skalowalne publikowania i subskrybowania charakter centrów zdarzeń pozwala elastycznie pozyskiwania logów platformy do niestandardowej platformy teletry. Zobacz [projektowanie i tworzenie rozmiaru globalnej platformy telemetrii skali w usłudze Azure Event Hubs, aby](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/) uzyskać szczegółowe informacje.

* **Wyświetlanie kondycji usługi przez przesyłanie strumieniowe danych do usługi Power BI** — użyj centrów zdarzeń, usługi Stream Analytics i Usługi Power BI, aby przekształcić dane diagnostyczne w niemal w czasie rzeczywistym wgląd w usługi platformy Azure. Zobacz [Usługa Stream Analytics i Usługa Power BI: pulpit nawigacyjny analizy w czasie rzeczywistym, aby uzyskać](../../stream-analytics/stream-analytics-power-bi-dashboard.md) szczegółowe informacje na temat tego rozwiązania.

    Poniższy kod SQL to przykładowa kwerenda usługi Stream Analytics, której można użyć do przeanalizowania wszystkich danych dziennika w tabeli usługi Power BI:
    
    ```sql
    SELECT
    records.ArrayValue.[Properties you want to track]
    INTO
    [OutputSourceName – the Power BI source]
    FROM
    [InputSourceName] AS e
    CROSS APPLY GetArrayElements(e.records) AS records
    ```

## <a name="prerequisites"></a>Wymagania wstępne
Musisz [utworzyć centrum zdarzeń,](../../event-hubs/event-hubs-create.md) jeśli jeszcze go nie masz. Jeśli masz już ustawienie diagnostyczne przy użyciu tej przestrzeni nazw Centrum zdarzeń, centrum zdarzeń zostanie ponownie używane.

Zasady dostępu współdzielonego dla obszaru nazw definiuje uprawnienia, które ma mechanizm przesyłania strumieniowego. Przesyłanie strumieniowe do centrów zdarzeń wymaga uprawnień Zarządzanie, Wysyłanie i Nasłuchij. Zasady dostępu udostępnionego można tworzyć lub modyfikować w portalu Azure w obszarze Konfigurowanie obszaru nazw centrum zdarzeń.

Aby zaktualizować ustawienie diagnostyczne w celu uwzględnienia przesyłania strumieniowego, musisz mieć uprawnienie ListKey dla tej reguły autoryzacji centrum zdarzeń. Obszar nazw centrum zdarzeń nie musi być w tej samej subskrypcji co subskrypcja emitująca dzienniki, o ile użytkownik, który konfiguruje to ustawienie, ma odpowiedni dostęp RBAC do obu subskrypcji i obie subskrypcje znajdują się w tej samej dzierżawie usługi AAD.

## <a name="create-a-diagnostic-setting"></a>Tworzenie ustawienia diagnostycznego
Wysyłaj dzienniki platformy do centrum zdarzeń i innych miejsc docelowych, tworząc ustawienie diagnostyczne dla zasobu platformy Azure. Zobacz [Tworzenie ustawień diagnostycznych do zbierania dzienników i metryk na platformie Azure,](diagnostic-settings.md) aby uzyskać szczegółowe informacje.

## <a name="collect-data-from-compute-resources"></a>Zbieranie danych z zasobów obliczeniowych
Ustawienia diagnostyczne będą zbierać dzienniki zasobów zasobów obliczeniowych platformy Azure, jak każdy inny zasób, ale nie ich system operacyjny gościa lub obciążeń. Aby zebrać te dane, zainstaluj [agenta usługi Log Analytics](log-analytics-agent.md). 


## <a name="consuming-log-data-from-event-hubs"></a>Korzystanie z danych dziennika z centrów zdarzeń
Dzienniki platformy z centrów zdarzeń są używane w formacie JSON z elementami w poniższej tabeli.

| Nazwa elementu | Opis |
| --- | --- |
| Rekordy |Tablica wszystkich zdarzeń dziennika w tym ładunku. |
| time |Czas, w którym wystąpiło zdarzenie. |
| category |Kategoria dziennika dla tego zdarzenia. |
| resourceId |Identyfikator zasobu, który wygenerował to zdarzenie. |
| operationName |Nazwa operacji. |
| poziom |Element opcjonalny. Wskazuje poziom zdarzenia dziennika. |
| properties |Właściwości zdarzenia. Będą one się różnić dla każdej [ ]()usługi platformy Azure, jak opisano w . |


Poniżej przedstawiono przykładowe dane wyjściowe z Centrum zdarzeń dla dziennika zasobów:

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



## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o dziennikach zasobów](platform-logs-overview.md).
* [Utwórz ustawienie diagnostyczne do zbierania dzienników i metryk na platformie Azure](diagnostic-settings.md).
* [Przesyłaj strumieniowo dzienniki usługi Azure Active Directory za pomocą usługi Azure Monitor](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md).
* [Wprowadzenie do centrów zdarzeń](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md).

