---
title: Monitorowanie fabryki danych przy użyciu usługi Azure Monitor | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać usługi Azure Monitor do monitorowania potoków usługi Data Factory przez włączenie dzienników diagnostycznych przy użyciu informacji z usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: shlo
ms.openlocfilehash: 722d77bf27e3cd7eb921b09e0a1d4732a5b5f874
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514418"
---
# <a name="alert-and-monitor-data-factories-using-azure-monitor"></a>Zgłoś alert i monitorowania fabryki danych przy użyciu usługi Azure Monitor
Aplikacje w chmurze są złożone z wielu ruchomych elementów. Monitorowanie zapewnia dane, aby upewnić się, że aplikacja stale się i działa w dobrej kondycji. Pomaga również stave potencjalnych problemów lub Rozwiązywanie problemów z przeszłości te. Ponadto można użyć danych monitorowania do uzyskania szczegółowych informacji o aplikacji. Ta wiedza może pomóc zwiększyć wydajność aplikacji lub łatwość konserwacji lub Automatyzuj akcje, które w przeciwnym razie wymagają ręcznej interwencji.

Usługa Azure Monitor zapewnia podstawowy poziom infrastrukturą metryk i dzienników dla większości usług na platformie Microsoft Azure. Aby uzyskać więcej informacji, zobacz [monitorowanie — Przegląd](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor). Dzienniki diagnostyczne platformy Azure są dzienniki emitowane przez zasób, które zapewniają bogate, często dane o działaniu tego zasobu. Data Factory danych wyjściowych dzienników diagnostycznych w usłudze Azure Monitor.

## <a name="persist-data-factory-data"></a>Utrwalanie danych fabryki danych
Fabryka danych przechowuje tylko po danych uruchomieniom 45 dni. Jeśli chcesz zachować uruchomieniom danych przez ponad 45 dni, przy użyciu usługi Azure Monitor tylko nie można skierować dzienniki diagnostyczne na potrzeby analizy, jednak można utrwalić je na konto magazynu pozwala uzyskać informacje o fabryce na czas trwania wybrane.

## <a name="diagnostic-logs"></a>Dzienniki diagnostyczne

* Zapisywanie ich **konta magazynu** do wglądu, inspekcji czy ręcznie. Można określić czas przechowywania (w dniach), za pomocą ustawień diagnostycznych.
* Stream im **usługi Event Hubs** dla pozyskiwania przez usługi innych firm lub rozwiązania analizy niestandardowe, takie jak Power BI.
* Analizuj je za pomocą **usługi Log Analytics**

Możesz użyć magazynu konta lub event hub przestrzeni nazw, która nie znajduje się w tej samej subskrypcji co zasób, który jest emitowane dzienniki. Użytkownik, który konfiguruje ustawienie, musi mieć odpowiedni dostęp opartej na rolach (RBAC) dostępu do obu subskrypcji.

## <a name="set-up-diagnostic-logs"></a>Konfigurowanie dzienników diagnostycznych

### <a name="diagnostic-settings"></a>Ustawienia diagnostyczne
Dzienniki diagnostyczne za zasoby obliczeniowe nie są skonfigurowane za pomocą ustawień diagnostycznych. Ustawienia diagnostyczne dla formantu zasobów:

* Której dzienniki diagnostyczne są wysyłane (konto magazynu, usługa Event Hubs lub dzienniki usługi Azure Monitor).
* Kategorie dziennika, które są wysyłane.
* Jak długo każda kategoria dziennika powinny być przechowywane na koncie magazynu.
* Wpisanie wartości zero oznacza, że dzienniki są przechowywane w nieskończoność. W przeciwnym razie wartość może być dowolną liczbę dni z zakresu od 1 do 2147483647.
* Jeśli ustawiono zasady przechowywania, ale przechowywania dzienników na koncie magazynu jest wyłączona (na przykład tylko usługi Event Hubs lub usługi Azure Monitor są zaznaczone opcje dzienników), zasad przechowywania przyniosło żadnego skutku.
* Zasady przechowywania są stosowane dziennie, aby na koniec dnia (UTC), dzienniki w dzień, w którym jest teraz, po przekroczeniu przechowywania zasady zostaną usunięte. Na przykład jeśli masz zasady przechowywania w jeden dzień, na początku dnia już dziś dzienników z wczoraj zanim dnia zostaną usunięte.

### <a name="enable-diagnostic-logs-via-rest-apis"></a>Włączanie dzienników diagnostycznych za pośrednictwem interfejsów API REST

Utwórz lub zaktualizuj ustawienia diagnostyki, w interfejsie API REST usługi Azure Monitor

**Żądanie**
```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

**Nagłówki**
* Zastąp element `{api-version}` pytaniem `2016-09-01`.
* Zastąp `{resource-id}` o identyfikatorze zasobu zasobu, dla którego chcesz edytować ustawienia diagnostyczne. Aby uzyskać więcej informacji [używanie grup zasobów do zarządzania zasobami platformy Azure](../azure-resource-manager/manage-resource-groups-portal.md).
* Ustaw `Content-Type` nagłówka do `application/json`.
* Ustaw nagłówek autoryzacji na token internetowy JSON, którą można uzyskać z usługi Azure Active Directory. Aby uzyskać więcej informacji, zobacz [uwierzytelniania żądań](../active-directory/develop/authentication-scenarios.md).

**Treść**
```json
{
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "metrics": [
        ],
        "logs": [
                {
                    "category": "PipelineRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "TriggerRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "ActivityRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                }
            ]
    },
    "location": ""
}
```

| Właściwość | Typ | Opis |
| --- | --- | --- |
| storageAccountId |String | Identyfikator zasobu konta magazynu, do którego chcesz wysłać dzienniki diagnostyczne |
| serviceBusRuleId |String | Usługa Service bus reguły identyfikator przestrzeń nazw magistrali usług, w którym chcesz mieć utworzonych dla przesyłania strumieniowego dzienników diagnostycznych w usłudze Event Hubs. Reguły, identyfikator ma format: "{usługi Service bus identyfikator zasobu} /authorizationrules/ {nazwa klucza}".|
| workspaceId | Typ złożony | Tablica ziarna czasu metryki i ich zasady przechowywania. Obecnie ta właściwość jest pusta. |
|metrics| Wartości parametrów potoku, uruchomienia do przekazania do wywoływanej potoku| Obiekt JSON, mapowanie nazwy parametrów do wartości argumentów |
| logs| Typ złożony| Nazwa kategorii dziennik diagnostyczny dla typu zasobu. Aby uzyskać listę kategorii, dzienniki diagnostyczne dla zasobu, należy najpierw wykonać operację pobrania ustawień diagnostycznych. |
| category| String| Kategorie dzienników i ich zasady przechowywania |
| timeGrain | String | Stopień szczegółowości metryki, które są przechwytywane format czasu ISO 8601. Musi być PT1M (jednej minuty)|
| enabled| Boolean | Określa, czy zbiór kategorii metryk lub dzienników jest włączona dla tego zasobu|
| retentionPolicy| Typ złożony| W tym artykule opisano zasady przechowywania dla metryk lub dzienników kategorii. Używane dla tylko opcję konta magazynu.|
| days| Int| Liczba dni przechowywania metryk lub dzienników. Wartość 0 zachowuje dzienniki na czas nieokreślony. Używane dla tylko opcję konta magazynu. |

**Odpowiedź**

200 OK


```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```

Uzyskać informacje na temat ustawienia diagnostyki w interfejsie API REST usługi Azure Monitor

**Żądanie**
```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

**Nagłówki**
* Zastąp element `{api-version}` pytaniem `2016-09-01`.
* Zastąp `{resource-id}` o identyfikatorze zasobu zasobu, dla którego chcesz edytować ustawienia diagnostyczne. Aby uzyskać więcej informacji na używanie grup zasobów do zarządzania zasobami platformy Azure.
* Ustaw `Content-Type` nagłówka do `application/json`.
* Ustaw nagłówek autoryzacji JSON Web Token uzyskany z usługi Azure Active Directory. Aby uzyskać więcej informacji Zobacz uwierzytelnianie żądań.

**Odpowiedź**

200 OK

```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.Storage/storageAccounts/azmonlogs",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.EventHub/namespaces/shloeventhub/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/ADF/providers/Microsoft.OperationalInsights/workspaces/mihaipie",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```
[Więcej informacji w tym miejscu](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)

## <a name="schema-of-logs--events"></a>Schemat dzienniki i zdarzenia

### <a name="activity-run-logs-attributes"></a>Uruchom działania dzienniki atrybutów

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "activityRunId":"",
   "pipelineRunId":"",
   "resourceId":"",
   "category":"ActivityRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "activityName":"",
   "start":"",
   "end":"",
   "properties":
       {
          "Input": "{
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "BlobSink"
              }
           }",
          "Output": "{"dataRead":121,"dataWritten":121,"copyDuration":5,
               "throughput":0.0236328132,"errors":[]}",
          "Error": "{
              "errorCode": "null",
              "message": "null",
              "failureType": "null",
              "target": "CopyBlobtoBlob"
        }
    }
}
```

| Właściwość | Typ | Opis | Przykład |
| --- | --- | --- | --- |
| Poziom |String | Poziom dzienniki diagnostyczne. Poziom 4 jest zawsze w przypadku uruchomienia dzienniki działania. | `4`  |
| correlationId |String | Unikatowy identyfikator w celu śledzenia poszczególnych żądań end-to-end | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | String | Czas zdarzenia w czasu w formacie UTC `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|activityRunId| String| Identyfikator uruchomienia działania | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|pipelineRunId| String| Identyfikator uruchomienia potoku | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|resourceId| String | Identyfikator skojarzonego zasobu dla zasobu fabryki danych | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| String | Kategoria dzienników diagnostycznych. Ustaw tę właściwość na "Uruchomień działania" | `ActivityRuns` |
|poziom| String | Poziom dzienniki diagnostyczne. Ustaw tę właściwość na "Informacyjne" | `Informational` |
|operationName| String |Nazwa działania o stanie. Jeśli stan to start pulsu, jest `MyActivity -`. Jeśli stan jest pulsu zakończenia, jest `MyActivity - Succeeded` ze stanem końcowym | `MyActivity - Succeeded` |
|pipelineName| String | Nazwa potoku | `MyPipeline` |
|activityName| String | Nazwa działania | `MyActivity` |
|rozpoczynanie| String | Początek działania uruchamiane w czasu w formacie UTC | `2017-06-26T20:55:29.5007959Z`|
|end| String | Koniec działań są uruchamiane w czasu w formacie UTC. Jeśli działanie nie zakończyła jeszcze (dziennik diagnostyczny dla działania uruchamiania), wartość domyślna `1601-01-01T00:00:00Z` jest ustawiona.  | `2017-06-26T20:55:29.5007959Z` |

### <a name="pipeline-run-logs-attributes"></a>Uruchomienie potoku rejestruje atrybutów

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "runId":"",
   "resourceId":"",
   "category":"PipelineRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "start":"",
   "end":"",
   "status":"",
   "properties":
    {
      "Parameters": {
        "<parameter1Name>": "<parameter1Value>"
      },
      "SystemParameters": {
        "ExecutionStart": "",
        "TriggerId": "",
        "SubscriptionId": ""
      }
    }
}
```

| Właściwość | Typ | Opis | Przykład |
| --- | --- | --- | --- |
| Poziom |String | Poziom dzienniki diagnostyczne. Poziom 4 jest tak w przypadku uruchomienia dzienniki działania. | `4`  |
| correlationId |String | Unikatowy identyfikator w celu śledzenia poszczególnych żądań end-to-end | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | String | Czas zdarzenia w czasu w formacie UTC `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|runId| String| Identyfikator uruchomienia potoku | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|resourceId| String | Identyfikator skojarzonego zasobu dla zasobu fabryki danych | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| String | Kategoria dzienników diagnostycznych. Ustaw tę właściwość na "PipelineRuns" | `PipelineRuns` |
|poziom| String | Poziom dzienniki diagnostyczne. Ustaw tę właściwość na "Informacyjne" | `Informational` |
|operationName| String |Nazwa potoku ze stanem. "Pipeline - powiodło się" ze stanem końcowym po ukończeniu uruchomienia potoku| `MyPipeline - Succeeded` |
|pipelineName| String | Nazwa potoku | `MyPipeline` |
|rozpoczynanie| String | Początek działania uruchamiane w czasu w formacie UTC | `2017-06-26T20:55:29.5007959Z`|
|end| String | Koniec działania jest uruchamiany w czasu w formacie UTC. Jeśli działanie nie zakończyła jeszcze (dziennik diagnostyczny dla działania uruchamiania), wartość domyślna `1601-01-01T00:00:00Z` jest ustawiona.  | `2017-06-26T20:55:29.5007959Z` |
|status| String | Końcowy stan potoku, uruchom (powodzenie lub niepowodzenie) | `Succeeded`|

### <a name="trigger-run-logs-attributes"></a>Przebieg wyzwalacza rejestruje atrybutów

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "triggerId":"",
   "resourceId":"",
   "category":"TriggerRuns",
   "level":"Informational",
   "operationName":"",
   "triggerName":"",
   "triggerType":"",
   "triggerEvent":"",
   "start":"",
   "status":"",
   "properties":
   {
      "Parameters": {
        "TriggerTime": "",
       "ScheduleTime": ""
      },
      "SystemParameters": {}
    }
}

```

| Właściwość | Typ | Opis | Przykład |
| --- | --- | --- | --- |
| Poziom |String | Poziom dzienniki diagnostyczne. Ustaw poziom 4 dla działania uruchamiania dzienniki. | `4`  |
| correlationId |String | Unikatowy identyfikator w celu śledzenia poszczególnych żądań end-to-end | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | String | Czas zdarzenia w czasu w formacie UTC `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|triggerId| String| Identyfikator uruchomienie wyzwalacza | `08587023010602533858661257311` |
|resourceId| String | Identyfikator skojarzonego zasobu dla zasobu fabryki danych | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| String | Kategoria dzienników diagnostycznych. Ustaw tę właściwość na "PipelineRuns" | `PipelineRuns` |
|poziom| String | Poziom dzienniki diagnostyczne. Ustaw tę właściwość na "Informacyjne" | `Informational` |
|operationName| String |Nazwa wyzwalacza na stan końcowy tego, czy pomyślnie wyzwolone. "MyTrigger - powiodło się" Jeśli pulsu zakończyło się pomyślnie| `MyTrigger - Succeeded` |
|triggerName| String | Nazwa wyzwalacza | `MyTrigger` |
|triggerType| String | Typ wyzwalacza (wyzwalacza ręcznego lub wyzwalacz harmonogramu) | `ScheduleTrigger` |
|triggerEvent| String | Zdarzenia wyzwalacza | `ScheduleTime - 2017-07-06T01:50:25Z` |
|rozpoczynanie| String | Początek uruchomienia wyzwalacza w czasu w formacie UTC | `2017-06-26T20:55:29.5007959Z`|
|status| String | Końcowy stan tego, czy pomyślnie wyzwalacza (powodzenie lub niepowodzenie) | `Succeeded`|

## <a name="metrics"></a>Metryki

Usługa Azure Monitor umożliwia korzystanie z telemetrii, aby uzyskać wgląd w wydajność i kondycja obciążeń na platformie Azure. Typ najważniejszych danych telemetrycznych platformy Azure jest metryki (nazywane również liczników wydajności) wyemitowane przez zasoby najbardziej platformy Azure. Usługa Azure Monitor zapewnia kilka sposobów na konfigurowanie i korzystanie z tych metryk monitorowania i rozwiązywania problemów.

ADFV2 emituje następujące metryki

| **Metryka**           | **Nazwa wyświetlana metryki**         | **Unit** | **Typ agregacji** | **Opis**                                       |
|----------------------|---------------------------------|----------|----------------------|-------------------------------------------------------|
| PipelineSucceededRuns | Powodzenie metryki uruchomienia potoku | Licznik    | Łącznie                | Łączna liczba potoków jest uruchamiany zakończyło się pomyślnie w ramach minutowym oknie |
| PipelineFailedRuns   | Nie powiodło się metryki uruchomienia potoku    | Licznik    | Łącznie                | Potoki łączna liczba przebiegów zakończonych niepowodzeniem w minutowym oknie    |
| ActivitySucceededRuns | Powodzenie metryki uruchomień działań | Licznik    | Łącznie                | Łączna liczba działanie uruchamia zakończyło się pomyślnie w minutowym oknie  |
| ActivityFailedRuns   | Nie powiodło się metryki uruchomień działań    | Licznik    | Łącznie                | Całkowita aktywność przebiegów zakończonych niepowodzeniem w minutowym oknie     |
| TriggerSucceededRuns | Powodzenie metryki uruchomień wyzwalacza  | Licznik    | Łącznie                | Łączna liczba wyzwalacz jest uruchamiany zakończyło się pomyślnie w ciągu minuty okna   |
| TriggerFailedRuns    | Nie powiodło się metryki uruchomień wyzwalacza     | Licznik    | Łącznie                | Wyzwalacz łączna liczba przebiegów zakończonych niepowodzeniem w minutowym oknie      |

Aby uzyskać dostęp do metryk, postępuj zgodnie z instrukcjami w artykule- https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics

## <a name="monitor-data-factory-metrics-with-azure-monitor"></a>Metryki fabryki danych monitorowania z usługą Azure Monitor

Za pomocą integracji usługi Azure Data Factory i Azure Monitor do kierowania danych do usługi Azure Monitor. Jest to przydatne w następujących scenariuszach:

1.  Chcesz napisać złożonych zapytań w bogaty zestaw metryk, która zostanie opublikowana przez usługę Data Factory do usługi Azure Monitor. Można również utworzyć niestandardowe alerty na te zapytania za pomocą usługi Azure Monitor.

2.  Chcesz monitorować całej fabryki danych. Za przekazywanie danych z różnych fabryk danych z jednym obszarem roboczym usługi Azure Monitor.

7 minutowym wprowadzenie i pokaz działania tej funkcji Obejrzyj poniższy film wideo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

### <a name="configure-diagnostic-settings-and-workspace"></a>Konfigurowanie ustawień diagnostycznych i obszaru roboczego

Włączanie ustawień diagnostycznych dla fabryki danych.

1.  Wybierz **usługi Azure Monitor** -> **ustawień diagnostycznych** -> Wybierz usługi data factory -> Włącz diagnostykę.

    ![monitor-oms-image1.png](media/data-factory-monitor-oms/monitor-oms-image1.png)

2.  Podaj ustawienia diagnostyczne, w tym konfiguracją obszaru roboczego.

    ![monitor-oms-image2.png](media/data-factory-monitor-oms/monitor-oms-image2.png)

### <a name="install-azure-data-factory-analytics-from-azure-marketplace"></a>Instalowanie usługi Azure Data Factory Analytics z witryny Azure Marketplace

![monitor-oms-image3.png](media/data-factory-monitor-oms/monitor-oms-image3.png)

![monitor-oms-image4.png](media/data-factory-monitor-oms/monitor-oms-image4.png)

Kliknij przycisk **Utwórz** i wybierz obszar roboczy oraz obszaru roboczego ustawienia.

![monitor-oms-image5.png](media/data-factory-monitor-oms/monitor-oms-image5.png)

### <a name="monitor-data-factory-metrics"></a>Metryki fabryki danych monitorowania

Instalowanie **usługi Azure Data Factory Analytics** tworzy domyślny zestaw widoków umożliwiająca następujące metryki:

- Uruchomienia potoku usługi ADF przebiegów-1) przez usługę Data Factory

- Uruchomienia działania usługi ADF przebiegów-2) przez usługę Data Factory

- Przebiegi ADF-3) uruchomienia wyzwalacza przez usługę Data Factory

- Błędy usługi ADF-1) 10 najważniejszych potoku błędów przez usługę Data Factory

- Uruchomienia działania pierwszych 10 błędy ADF-2) przez usługę Data Factory

- Błędy usługi ADF-3) 10 najważniejszych wyzwalacza błędów przez usługę Data Factory

- Uruchomienia działania usługi ADF statystyki-1) według typu

- Uruchomienia wyzwalacza statystyki ADF-2) według typu

- Czas trwania uruchomienia potoku Max statystyki ADF-3)

![monitor-oms-image6.png](media/data-factory-monitor-oms/monitor-oms-image6.png)

![monitor-oms-image7.png](media/data-factory-monitor-oms/monitor-oms-image7.png)

Można wizualizować metryki powyżej, Przyjrzyj się zapytania za te metryki, edytowanie zapytań, tworzenie alertów i tak dalej.

![monitor-oms-image8.png](media/data-factory-monitor-oms/monitor-oms-image8.png)

## <a name="alerts"></a>Alerty

Zaloguj się do witryny Azure portal, a następnie kliknij przycisk **Monitor —&gt; alerty** do tworzenia alertów.

![Alerty w menu portalu](media/monitor-using-azure-monitor/alerts_image3.png)

### <a name="create-alerts"></a>Tworzenie alertów

1.  Kliknij przycisk **+ Nowa reguła alertu** można utworzyć nowego alertu.

    ![Nowa reguła alertu](media/monitor-using-azure-monitor/alerts_image4.png)

2.  Zdefiniuj **warunek alertu**.

    > [!NOTE]
    > Upewnij się, że wybrano **wszystkich** w **Filtruj według typu zasobu**.

    ![Zgłoś alert, stan, ekranu 1 z 3](media/monitor-using-azure-monitor/alerts_image5.png)

    ![Zgłoś alert, stan, ekranu 2 z 3](media/monitor-using-azure-monitor/alerts_image6.png)

    ![Zgłoś alert, stan, ekranu 3 z 3](media/monitor-using-azure-monitor/alerts_image7.png)

3.  Zdefiniuj **szczegóły alertu**.

    ![Szczegóły alertu](media/monitor-using-azure-monitor/alerts_image8.png)

4.  Zdefiniuj **grupy akcji**.

    ![Grupa akcji, ekran 1 z 4](media/monitor-using-azure-monitor/alerts_image9.png)

    ![Grupa akcji, ekran 2 z 4](media/monitor-using-azure-monitor/alerts_image10.png)

    ![Grupa akcji, ekran 3 z 4](media/monitor-using-azure-monitor/alerts_image11.png)

    ![Grupa akcji, ekran 4 z 4](media/monitor-using-azure-monitor/alerts_image12.png)

## <a name="next-steps"></a>Kolejne kroki

Zobacz [monitorowanie potoków i zarządzanie nimi programistycznie](monitor-programmatically.md) artykuł, aby dowiedzieć się więcej o monitorowaniu i zarządzaniu potoków przy użyciu kodu.
