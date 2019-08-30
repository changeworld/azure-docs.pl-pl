---
title: Monitorowanie fabryk danych przy użyciu Azure Monitor | Microsoft Docs
description: Informacje na temat używania Azure Monitor do monitorowania potoków Data Factory przez włączenie dzienników diagnostycznych z informacjami z Azure Data Factory.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/11/2018
ms.openlocfilehash: 0614de8bbb1429c84bf5f2e55c1765f3e4863f3a
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141127"
---
# <a name="alert-and-monitor-data-factories-using-azure-monitor"></a>Generowanie alertów i monitorowanie fabryk danych przy użyciu Azure Monitor
Aplikacje w chmurze są złożone z wieloma ruchomymi częściami. Monitorowanie zapewnia dane, aby upewnić się, że aplikacja będzie działać w dobrej kondycji. Pomaga również w staveniu potencjalnych problemów lub rozwiązywaniu problemów z poprzednimi. Ponadto możesz użyć monitorowania danych, aby uzyskać szczegółowe informacje o aplikacji. Ta wiedza może pomóc w zwiększeniu wydajności aplikacji lub utrzymaniu lub zautomatyzowaniu działań, które w przeciwnym razie wymagają ręcznej interwencji.

Azure Monitor udostępnia metryki i dzienniki infrastruktury na poziomie podstawowym dla większości usług w Microsoft Azure. Aby uzyskać szczegółowe informacje, zobacz [Omówienie monitorowania](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor). Dzienniki diagnostyczne platformy Azure to dzienniki emitowane przez zasób, który zapewnia rozbudowane i częste dane dotyczące operacji tego zasobu. Data Factory wyprowadza dzienniki diagnostyczne w Azure Monitor.

## <a name="persist-data-factory-data"></a>Utrwalaj dane Data Factory
Data Factory przechowuje tylko dane uruchomienia potoku przez 45 dni. Jeśli chcesz utrwalać dane przebiegu potoku przez ponad 45 dni, przy użyciu Azure Monitor nie możesz kierować dzienników diagnostycznych do analizy, możesz je utrwalać na koncie magazynu, aby uzyskać informacje o fabryce w czasie trwania wybierania.

## <a name="diagnostic-logs"></a>Dzienniki diagnostyczne

* Zapisz je na **koncie magazynu** , aby przeprowadzić inspekcję lub inspekcję ręczną. Możesz określić czas przechowywania (w dniach) przy użyciu ustawień diagnostycznych.
* Przesyłaj strumieniowo do **Event Hubs** na potrzeby pozyskiwania przez usługę innej firmy lub rozwiązanie do analizy niestandardowej, takie jak Power BI.
* Analizuj przy użyciu **log Analytics**

Możesz użyć konta magazynu lub przestrzeni nazw centrum zdarzeń, która nie znajduje się w tej samej subskrypcji, co zasób emitujący dzienniki. Użytkownik, który konfiguruje ustawienie, musi mieć dostęp do obu subskrypcji przy użyciu kontroli dostępu opartej na rolach (RBAC).

## <a name="set-up-diagnostic-logs"></a>Konfigurowanie dzienników diagnostycznych

### <a name="diagnostic-settings"></a>Ustawienia diagnostyczne
Dzienniki diagnostyczne dla zasobów nieobliczeniowych są konfigurowane przy użyciu ustawień diagnostycznych. Ustawienia diagnostyczne dla kontrolki zasobu:

* Miejsce, w którym są wysyłane dzienniki diagnostyczne (konto magazynu, Event Hubs lub dzienniki Azure Monitor).
* Które kategorie dzienników są wysyłane.
* Jak długo każda kategoria dziennika ma być przechowywana na koncie magazynu.
* Wpisanie wartości zero oznacza, że dzienniki są przechowywane w nieskończoność. W przeciwnym razie wartość może być dowolną liczbę dni z zakresu od 1 do 2147483647.
* Jeśli zasady przechowywania są ustawione, ale przechowywanie dzienników na koncie magazynu jest wyłączone (na przykład wybrano opcję tylko Event Hubs lub Azure Monitor dzienników), zasady przechowywania nie będą miały żadnego efektu.
* Zasady przechowywania są stosowane dziennie, aby na koniec dnia (UTC), dzienniki w dzień, w którym jest teraz, po przekroczeniu przechowywania zasady zostaną usunięte. Na przykład jeśli masz zasady przechowywania w jeden dzień, na początku dnia już dziś dzienników z wczoraj zanim dnia zostaną usunięte.

### <a name="enable-diagnostic-logs-via-rest-apis"></a>Włączanie dzienników diagnostycznych za pośrednictwem interfejsów API REST

Utwórz lub zaktualizuj ustawienie diagnostyki w interfejsie API REST Azure Monitor

**Żądanie**
```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

**Nagłówki**
* Zastąp element `{api-version}` pytaniem `2016-09-01`.
* Zamień `{resource-id}` na identyfikator zasobu zasobu, dla którego chcesz edytować ustawienia diagnostyczne. Aby uzyskać więcej informacji [przy użyciu grup zasobów do zarządzania zasobami platformy Azure](../azure-resource-manager/manage-resource-groups-portal.md).
* Ustaw nagłówek na `application/json`. `Content-Type`
* Ustaw nagłówek autoryzacji na token sieci Web JSON uzyskany z Azure Active Directory. Aby uzyskać więcej informacji, zobacz [uwierzytelnianie żądań](../active-directory/develop/authentication-scenarios.md).

**Body**
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

| Właściwość | Type | Opis |
| --- | --- | --- |
| storageAccountId |String | Identyfikator zasobu konta magazynu, do którego chcesz wysłać dzienniki diagnostyczne |
| serviceBusRuleId |String | Identyfikator reguły usługi Service Bus w przestrzeni nazw usługi Service Bus, w której chcesz utworzyć Event Hubs dla dzienników diagnostycznych przesyłania strumieniowego. Identyfikator reguły ma format: "{Identyfikator zasobu usługi Service Bus}/authorizationrules/{Key Name}".|
| workspaceId | Typ złożony | Tablica ziaren i ich zasad przechowywania. Obecnie ta właściwość jest pusta. |
|metryki| Wartości parametrów przebiegu potoku do przekazanie do wywołanego potoku| Nazwy parametrów mapowania obiektu JSON na wartości argumentów |
| logs| Typ złożony| Nazwa kategorii dziennika diagnostycznego dla typu zasobu. Aby uzyskać listę kategorii dzienników diagnostycznych dla zasobu, należy najpierw wykonać operację pobierania ustawień diagnostycznych. |
| category| String| Tablica kategorii dzienników i ich zasad przechowywania |
| timeGrain | String | Stopień szczegółowości metryk, które są przechwytywane w formacie czasu trwania ISO 8601. Musi być PT1M (jedna minuta)|
| enabled| Boolean | Określa, czy dla tego zasobu jest włączona kolekcja tej kategorii metryki lub dziennika|
| retentionPolicy| Typ złożony| Zawiera opis zasad przechowywania dla kategorii Metryka lub dziennik. Używany tylko dla opcji konto magazynu.|
| days| Int| Liczba dni przechowywania metryk lub dzienników. Wartość 0 powoduje, że dzienniki są przechowywane przez czas nieokreślony. Używany tylko dla opcji konto magazynu. |

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

Uzyskaj informacje na temat ustawień diagnostycznych w interfejsie API REST Azure Monitor

**Żądanie**
```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

**Nagłówki**
* Zastąp element `{api-version}` pytaniem `2016-09-01`.
* Zamień `{resource-id}` na identyfikator zasobu zasobu, dla którego chcesz edytować ustawienia diagnostyczne. Aby uzyskać więcej informacji przy użyciu grup zasobów do zarządzania zasobami platformy Azure.
* Ustaw nagłówek na `application/json`. `Content-Type`
* Ustaw nagłówek autoryzacji na token sieci Web JSON uzyskany z Azure Active Directory. Aby uzyskać więcej informacji, zobacz Uwierzytelnianie żądań.

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
[Więcej informacji znajdziesz tutaj](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)

## <a name="schema-of-logs--events"></a>Schemat dzienników & zdarzeń

### <a name="azure-monitor-schema"></a>Schemat Azure Monitor

#### <a name="activity-run-logs-attributes"></a>Atrybuty uruchamiania dzienników działania

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

| Właściwość | Type | Opis | Przykład |
| --- | --- | --- | --- |
| Poziom |String | Poziom dzienników diagnostycznych. Poziom 4 zawsze jest uwzględniany w przypadku dzienników uruchomienia działania. | `4`  |
| correlationId |String | Unikatowy identyfikator do śledzenia określonego żądania od końca do końca | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | String | Godzina zdarzenia w wartości TimeSpan, format UTC`YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|activityRunId| String| Identyfikator uruchomienia działania | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|pipelineRunId| String| Identyfikator uruchomienia potoku | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|resourceId| String | Identyfikator skojarzonego zasobu dla zasobu usługi Data Factory | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| String | Kategoria dzienników diagnostycznych. Ustaw tę właściwość na "ActivityRuns" | `ActivityRuns` |
|level| String | Poziom dzienników diagnostycznych. Ustaw tę właściwość na "informacyjny" | `Informational` |
|operationName| String |Nazwa działania o stanie. Jeśli stan jest pulsem startowym, jest to `MyActivity -`. Jeśli stan jest pulsem końcowym, jest `MyActivity - Succeeded` on końcowym stanem | `MyActivity - Succeeded` |
|pipelineName| String | Nazwa potoku | `MyPipeline` |
|activityName| String | Nazwa działania | `MyActivity` |
|start| String | Początek uruchomienia działania w wartości TimeSpan, format UTC | `2017-06-26T20:55:29.5007959Z`|
|end| String | Koniec uruchomienia działania w formacie TimeSpan, UTC. Jeśli działanie nie zakończyło się jeszcze (Dziennik diagnostyczny dla uruchomienia działania), `1601-01-01T00:00:00Z` zostanie ustawiona wartość domyślna równa.  | `2017-06-26T20:55:29.5007959Z` |

#### <a name="pipeline-run-logs-attributes"></a>Atrybuty dzienników uruchomienia potoku

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

| Właściwość | Type | Opis | Przykład |
| --- | --- | --- | --- |
| Poziom |String | Poziom dzienników diagnostycznych. Poziom 4 jest uwzględniany w przypadku dzienników uruchomienia działania. | `4`  |
| correlationId |String | Unikatowy identyfikator do śledzenia określonego żądania od końca do końca | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | String | Godzina zdarzenia w wartości TimeSpan, format UTC`YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|runId| String| Identyfikator uruchomienia potoku | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|resourceId| String | Identyfikator skojarzonego zasobu dla zasobu usługi Data Factory | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| String | Kategoria dzienników diagnostycznych. Ustaw tę właściwość na "PipelineRuns" | `PipelineRuns` |
|level| String | Poziom dzienników diagnostycznych. Ustaw tę właściwość na "informacyjny" | `Informational` |
|operationName| String |Nazwa potoku ze stanem. "Potok — powodzenie" ze stanem końcowym po zakończeniu przebiegu potoku| `MyPipeline - Succeeded` |
|pipelineName| String | Nazwa potoku | `MyPipeline` |
|start| String | Początek uruchomienia działania w wartości TimeSpan, format UTC | `2017-06-26T20:55:29.5007959Z`|
|end| String | Koniec uruchomienia działania w formacie TimeSpan, UTC. Jeśli działanie nie zakończyło się jeszcze (Dziennik diagnostyczny dla uruchomienia działania), `1601-01-01T00:00:00Z` zostanie ustawiona wartość domyślna równa.  | `2017-06-26T20:55:29.5007959Z` |
|status| String | Końcowy stan uruchomienia potoku (powodzenie lub niepowodzenie) | `Succeeded`|

#### <a name="trigger-run-logs-attributes"></a>Atrybuty uruchamiania dzienników wyzwalacza

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

| Właściwość | Type | Opis | Przykład |
| --- | --- | --- | --- |
| Poziom |String | Poziom dzienników diagnostycznych. Ustaw poziom 4 dla dzienników uruchamiania aktywności. | `4`  |
| correlationId |String | Unikatowy identyfikator do śledzenia określonego żądania od końca do końca | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | String | Godzina zdarzenia w wartości TimeSpan, format UTC`YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|triggerId| String| Identyfikator uruchomienia wyzwalacza | `08587023010602533858661257311` |
|resourceId| String | Identyfikator skojarzonego zasobu dla zasobu usługi Data Factory | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| String | Kategoria dzienników diagnostycznych. Ustaw tę właściwość na "PipelineRuns" | `PipelineRuns` |
|level| String | Poziom dzienników diagnostycznych. Ustaw tę właściwość na "informacyjny" | `Informational` |
|operationName| String |Nazwa wyzwalacza ze stanem końcowym, czy element został pomyślnie wyzwolony. "Wyzwalacz — powodzenie", jeśli puls zakończył się pomyślnie| `MyTrigger - Succeeded` |
|triggerName| String | Nazwa wyzwalacza | `MyTrigger` |
|triggerType| String | Typ wyzwalacza (wyzwalacz ręczny lub wyzwalacz harmonogramu) | `ScheduleTrigger` |
|triggerEvent| String | Zdarzenie wyzwalacza | `ScheduleTime - 2017-07-06T01:50:25Z` |
|start| String | Początek wyzwalania wyzwalacza w wartości TimeSpan, format UTC | `2017-06-26T20:55:29.5007959Z`|
|status| String | Końcowy stan, czy wyzwalacz został pomyślnie wywołany (powodzenie lub niepowodzenie) | `Succeeded`|

### <a name="log-analytics-schema"></a>Schemat Log Analytics

Log Analytics dziedziczy schemat z Azure Monitor z następującymi wyjątkami:

* Pierwsza litera w każdej z nazw kolumn zostanie wypisane wielkimi literami, w przypadku której *Identyfikator korelacji* w Azure monitor będzie *identyfikatorem korelacji* w log Analytics.
* *Poziom* kolumny zostanie porzucony.
* *Właściwości* kolumny dynamicznej zostaną zachowane jako następujący typ dynamicznego obiektu BLOB JSON:

    | Kolumna Azure Monitor | Kolumna Log Analytics | Type |
    | --- | --- | --- |
    | $. Properties. UserProperties | UserProperties | Dynamiczne |
    | $. Properties. Adnotacj | Adnotacj | Dynamiczne |
    | $. Properties. Klawiatur | Dane wejściowe | Dynamiczne |
    | $. Properties. Rozdzielczości | Output | Dynamiczne |
    | $. Properties. Błąd. errorCode | Kodzie | int |
    | $. Properties. Błąd. komunikat | ErrorMessage | string |
    | $. Properties. Porn | Błąd | Dynamiczne |
    | $. Properties. Poprzednikami hostowanymi | Poprzednikami hostowanymi | Dynamiczne |
    | $. Properties. Wejściowe | Parametry | Dynamiczne |
    | $. Properties. SystemParameters | SystemParameters | Dynamiczne |
    | $. Properties. Tabliczk | `Tags` | Dynamiczne |
    
## <a name="metrics"></a>Metryki

Azure Monitor umożliwia korzystanie z telemetrii w celu uzyskania wglądu w wydajność i kondycję obciążeń na platformie Azure. Najważniejszym typem danych telemetrii platformy Azure jest metryki (nazywane również licznikami wydajności) emitowane przez większość zasobów platformy Azure. Azure Monitor oferuje kilka sposobów konfigurowania i używania tych metryk do monitorowania i rozwiązywania problemów.

ADFV2 emituje następujące metryki:

| **Metryka**           | **Nazwa wyświetlana metryki**         | **Unit** | **Typ agregacji** | **Opis**                                       |
|----------------------|---------------------------------|----------|----------------------|-------------------------------------------------------|
| PipelineSucceededRuns | Metryki uruchamiania potoków zakończonych powodzeniem | Count    | Łącznie                | Łączna liczba przebiegów zakończonych powodzeniem w oknie minuty |
| PipelineFailedRuns   | Metryki uruchomionych potoków zakończonych niepowodzeniem    | Count    | Łącznie                | Łączna liczba uruchomień potoków nie powiodła się w oknie minuty    |
| ActivitySucceededRuns | Metryki uruchamiania działań zakończonych powodzeniem | Count    | Łącznie                | Łączna liczba przebiegów działań zakończyła się pomyślnie w przedziale czasu  |
| ActivityFailedRuns   | Metryki uruchamiania działań zakończonych niepowodzeniem    | Count    | Łącznie                | Łączna liczba uruchomień działań nie powiodła się w oknie minuty     |
| TriggerSucceededRuns | Wyzwalacze uruchomienia wyzwalają metryki  | Count    | Łącznie                | Łączna liczba uruchomień wyzwalacza zakończyła się pomyślnie w oknie minuty   |
| TriggerFailedRuns    | Metryki uruchomienia wyzwalacza zakończonego niepowodzeniem     | Count    | Łącznie                | Łączna liczba uruchomień wyzwalacza nie powiodła się w oknie minuty      |

Aby uzyskać dostęp do metryk, wykonaj instrukcje podane w temacie [Azure monitor Data Platform](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics).

## <a name="monitor-data-factory-metrics-with-azure-monitor"></a>Monitoruj metryki Data Factory przy użyciu Azure Monitor

Azure Data Factory integrację z usługą Azure Monitor służy do kierowania danych do Azure Monitor. Integracja ta jest przydatna w następujących scenariuszach:

1.  Chcesz pisać złożone zapytania na rozbudowanym zestawie metryk, który jest publikowany przez Data Factory Azure Monitor. Możesz również utworzyć niestandardowe alerty dla tych zapytań za pośrednictwem Azure Monitor.

2.  Chcesz monitorować w ramach fabryk danych. Dane można kierować z wielu fabryk danych do jednego obszaru roboczego Azure Monitor.

Aby zapoznać się z wprowadzeniem do siedmiu minut i demonstracją tej funkcji, Obejrzyj następujące wideo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

### <a name="configure-diagnostic-settings-and-workspace"></a>Konfigurowanie ustawień diagnostycznych i obszaru roboczego

Włącz ustawienia diagnostyczne dla fabryki danych.

1. W portalu przejdź do Azure Monitor a następnie kliknij pozycję **Ustawienia diagnostyczne** w menu **Ustawienia** .

2. Wybierz fabrykę danych, dla której chcesz ustawić ustawienie diagnostyczne.
    
3. Jeśli w wybranej fabryce danych nie ma żadnych ustawień, zostanie wyświetlony monit o utworzenie ustawienia. Kliknij przycisk "Włącz diagnostykę."

   ![Dodaj ustawienie diagnostyczne — nie istniejące ustawienia](media/data-factory-monitor-oms/monitor-oms-image1.png)

   W przypadku istniejących ustawień fabryki danych zostanie wyświetlona lista ustawień, które zostały już skonfigurowane w tej fabryce danych. Kliknij pozycję "Dodaj ustawienie diagnostyczne".

   ![Dodaj ustawienie diagnostyczne — istniejące ustawienia](media/data-factory-monitor-oms/add-diagnostic-setting.png)

4. Nadaj nazwę swojemu ustawieniu i zaznacz pole wyboru **Wyślij do log Analytics**a następnie wybierz obszar roboczy log Analytics.

    ![monitor-oms-image2.png](media/data-factory-monitor-oms/monitor-oms-image2.png)

5. Kliknij polecenie **Zapisz**.

Po kilku chwilach nowe ustawienie zostanie wyświetlone na liście ustawień dla tej fabryki danych, a dzienniki diagnostyczne są przesyłane strumieniowo do tego obszaru roboczego, gdy tylko nowe dane zdarzenia zostaną wygenerowane. Gdy zdarzenie jest emitowane i pojawia się w Log Analytics, może wystąpić do 15 minut.

> [!NOTE]
> Ze względu na jawny limit każdej tabeli dziennika platformy Azure, która nie ma więcej niż 500 kolumn, **zdecydowanie zaleca się użycie trybu określonego dla zasobu**. Aby uzyskać więcej informacji, zobacz [log Analytics znane ograniczenia](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-stream-log-store#known-limitation-column-limit-in-azurediagnostics).

### <a name="install-azure-data-factory-analytics-from-azure-marketplace"></a>Instalowanie Azure Data Factory Analytics w witrynie Azure Marketplace

![monitor-oms-image3.png](media/data-factory-monitor-oms/monitor-oms-image3.png)

![monitor-oms-image4.png](media/data-factory-monitor-oms/monitor-oms-image4.png)

Kliknij przycisk **Utwórz** i wybierz Ustawienia obszaru roboczego i obszaru roboczego.

![monitor-oms-image5.png](media/data-factory-monitor-oms/monitor-oms-image5.png)

### <a name="monitor-data-factory-metrics"></a>Monitoruj metryki Data Factory

Zainstalowanie **Azure Data Factory Analytics** powoduje utworzenie domyślnego zestawu widoków, które włącza następujące metryki:

- Uruchomienia APD-1) uruchomienia potoków przez Data Factory

- Uruchomienia ADF — 2) uruchomienia działań według Data Factory

- Uruchomienia ADF-3) uruchomienia wyzwalacza przez Data Factory

- Błędy ADF — 1) 10 najważniejszych błędów potoku według Data Factory

- Błędy funkcji ADF — 2) pierwsze 10 uruchomienia działań według Data Factory

- Błędy w usłudze ADF — 3) 10 najważniejszych błędów wyzwalaczy według Data Factory

- Statystyka ADF — 1) uruchomienia działań według typu

- Statystyka ADF — 2) uruchomienia wyzwalacza według typu

- Statystyka ADF — 3) maksymalny czas trwania przebiegu potoku

![monitor-oms-image6.png](media/data-factory-monitor-oms/monitor-oms-image6.png)

![monitor-oms-image7.png](media/data-factory-monitor-oms/monitor-oms-image7.png)

Możesz wizualizować powyższe metryki, przyjrzeć się kwerendom związanym z tymi metrykami, edytować zapytania, tworzyć alerty i tak dalej.

![monitor-oms-image8.png](media/data-factory-monitor-oms/monitor-oms-image8.png)

## <a name="alerts"></a>Alerty

Zaloguj się do Azure Portal i kliknij przycisk **Monitoruj** > **alerty** , aby utworzyć alerty.

![Alerty w menu portalu](media/monitor-using-azure-monitor/alerts_image3.png)

### <a name="create-alerts"></a>Tworzenie alertów

1.  Kliknij pozycję **+ Nowa reguła alertu** , aby utworzyć nowy Alert.

    ![Nowa reguła alertu](media/monitor-using-azure-monitor/alerts_image4.png)

2.  Zdefiniuj **warunek alertu**.

    > [!NOTE]
    > Upewnij się, że wybrano opcję **wszystkie** w polu **Filtruj według typu zasobu**.

    ![Warunek alertu, ekran 1 z 3](media/monitor-using-azure-monitor/alerts_image5.png)

    ![Warunek alertu, ekran 2 z 3](media/monitor-using-azure-monitor/alerts_image6.png)

    ![Warunek alertu, ekran 3 z 3](media/monitor-using-azure-monitor/alerts_image7.png)

3.  Zdefiniuj **szczegóły alertu**.

    ![Szczegóły alertu](media/monitor-using-azure-monitor/alerts_image8.png)

4.  Zdefiniuj **grupę akcji**.

    ![Grupa akcji, ekran 1 z 4](media/monitor-using-azure-monitor/alerts_image9.png)

    ![Grupa akcji, ekran 2 z 4](media/monitor-using-azure-monitor/alerts_image10.png)

    ![Grupa akcji, ekran 3 z 4](media/monitor-using-azure-monitor/alerts_image11.png)

    ![Grupa akcji, ekran 4 z 4](media/monitor-using-azure-monitor/alerts_image12.png)

## <a name="next-steps"></a>Następne kroki

Zobacz artykuł [monitorowanie i zarządzanie potokami programowo](monitor-programmatically.md) , aby dowiedzieć się więcej na temat monitorowania potoków i zarządzania nimi za pomocą kodu.
