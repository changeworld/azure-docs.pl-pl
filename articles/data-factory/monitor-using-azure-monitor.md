---
title: Monitorowanie fabryki danych Azure monitorze | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać Azure Monitor do monitorowania potoki fabryki danych przez włączenie dzienników diagnostycznych z informacjami z fabryki danych Azure.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/12/2018
ms.author: shlo
ms.openlocfilehash: e60f368115e91cbd8972af8dfa7f0f3d6ea8765b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "36337597"
---
# <a name="alert-and-monitor-data-factories-using-azure-monitor"></a>Alert i Monitor fabryki danych Azure monitora
Aplikacje w chmurze są złożonych z wielu części ruchu. Monitorowanie zawiera danych, aby upewnić się, że aplikacja pozostaje w górę i działa w dobrej kondycji. Pomaga również umożliwia stave potencjalne problemy i rozwiązywanie problemów w przeszłości te. Ponadto można użyć danych monitorowania w celu uzyskania szczegółowych informacji o aplikacji. Wiedzy może pomóc zwiększyć wydajność aplikacji lub utrzymania lub automatyzować czynności, które w przeciwnym razie wymagają ręcznej interwencji.

Azure Monitor udostępnia na podstawowym poziomie infrastruktury metryki i dzienniki dla większości usług platformy Microsoft Azure. Aby uzyskać więcej informacji, zobacz [omówienie monitorowania](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor). Azure dzienników diagnostycznych są dzienniki emitowane przez zasób, które zawierają rozbudowane, często dane dotyczące operacji tego zasobu. Fabryka danych danych wyjściowych dzienników diagnostycznych w monitorze Azure.

> [!NOTE]
> Ten artykuł dotyczy wersji 2 usługi Data Factory, która jest obecnie dostępna w wersji zapoznawczej. Jeśli używasz wersji 1 usługi fabryka danych, która jest ogólnie dostępna (GA), zobacz [monitorowanie i zarządzanie nimi potoków w fabryce danych version1](v1/data-factory-monitor-manage-pipelines.md).

## <a name="persist-data-factory-data"></a>Utrwalanie fabryki danych
Fabryka danych przechowuje dane tylko potoku Uruchom danych 45 dni. Jeśli chcesz zachować potoku Uruchom danych przez więcej niż 45 dni, za pomocą monitora Azure, nie można tylko trasy dzienników diagnostycznych do analizy, można ją utrwalić je do konta magazynu, który pozwala uzyskać fabryki informacji czasie trwania chossing Twojego.

## <a name="diagnostic-logs"></a>Dzienniki diagnostyczne

* Zapisywanie ich **konta magazynu** inspekcji inspekcji lub ręcznie. Można określić czas przechowywania (w dniach) przy użyciu ustawień diagnostycznych.
* Do strumienia **usługi Event Hubs** dla wprowadzanie przez usługi innej firmy lub rozwiązania analizy niestandardowych, takich jak usługi Power BI.
* Analizuj je za pomocą **analizy dzienników**

Możesz użyć magazynu konta lub zdarzenia koncentratora przestrzeni nazw, która nie znajduje się w tej samej subskrypcji co zasób, który jest emitowanie dzienników. Użytkownik, który konfiguruje ustawienie musi mieć prawa dostępu na podstawie ról (RBAC) dostępu do obu subskrypcji.

## <a name="set-up-diagnostic-logs"></a>Konfigurowanie dzienników diagnostycznych

### <a name="diagnostic-settings"></a>Ustawienia diagnostyki
Dzienniki diagnostyczne dla zasobów obliczeniowych nie są skonfigurowane przy użyciu ustawień diagnostycznych. Ustawienia diagnostyki w formancie zasobów:

* W przypadku dzienników diagnostycznych wysyłania (konto magazynu, centra zdarzeń i/lub Log Analytics).
* Kategorie dziennika, które są wysyłane.
* Jak długo każdej kategorii dziennika powinny zostać zachowane na koncie magazynu
* Przechowywanie 0 oznacza, że dzienniki są przechowywane w nieskończoność. W przeciwnym razie wartość może być dowolną liczbę dni od 1 do 2147483647.
* Jeśli ustawiono zasad przechowywania zapisywania dzienniki na koncie magazynu będzie jednak wyłączona (na przykład tylko centra zdarzeń lub analizy dzienników są zaznaczone opcje), zasad przechowywania nie mają żadnego skutku.
* Zasady przechowywania są zastosowane na dni, więc pod koniec dnia (UTC), dzienniki od dnia, która jest teraz poza przechowywania zasad są usuwane. Na przykład jeśli masz zasady przechowywania jeden dzień na początku dnia dzisiaj dzienniki na wczoraj zanim dzień zostaną usunięte.

### <a name="enable-diagnostic-logs-via-rest-apis"></a>Włączanie dzienników diagnostycznych za pośrednictwem interfejsów API REST

Utwórz lub zaktualizuj ustawienia diagnostyki w interfejsu API REST Monitor Azure

**Żądanie**
```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

**Nagłówki**
* Zastąp `{api-version}` z `2016-09-01`.
* Zastąp `{resource-id}` o identyfikatorze zasobu zasobu, dla którego chcesz edytować ustawienia diagnostyki. Aby uzyskać więcej informacji [używanie grup zasobów do zarządzania zasobami Azure](../azure-resource-manager/resource-group-portal.md).
* Ustaw `Content-Type` nagłówka do `application/json`.
* Ustaw nagłówek autoryzacji do tokenu web JSON, którą można uzyskać z usługi Azure Active Directory. Aby uzyskać więcej informacji, zobacz [uwierzytelniania żądań](../active-directory/develop/active-directory-authentication-scenarios.md).

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
| storageAccountId |Ciąg | Identyfikator zasobu konta magazynu, do którego chcesz wysłać dzienniki diagnostyczne |
| serviceBusRuleId |Ciąg | Identyfikator reguły magistrali usługi z przestrzeń nazw magistrali usług, w którym chcesz mieć centra zdarzeń utworzonych dla przesyłania strumieniowego dzienników diagnostycznych. Reguła identyfikator jest w formacie: "{identyfikator zasobu magistrali usługi} /authorizationrules/ {nazwa klucza}".|
| workspaceId | Typ złożony | Tablica ziarno czasu metryki i ich zasad przechowywania. Obecnie ta właściwość jest pusta. |
|metrics| Wartości parametrów potoku Uruchom do przekazania do wywoływanej potoku| Obiekt JSON mapowania nazw parametrów do wartości argumentów |
| dzienniki| Typ złożony| Nazwa kategorii dzienników diagnostycznych dla typu zasobu. Aby uzyskać listę kategorii dzienników diagnostycznych dla zasobu, należy najpierw wykonać operację pobierania ustawień diagnostycznych. |
| category| Ciąg| Tablica kategorii dzienników i ich zasady przechowywania |
| ziarnem czasu | Ciąg | Poziom szczegółowości metryk, które są przechwytywane w formacie czasu trwania ISO 8601. Musi być PT1M (jednej minuty)|
| enabled| Wartość logiczna | Określa, czy kolekcja tej kategorii Metryka lub dziennika jest włączona dla tego zasobu|
| retentionPolicy| Typ złożony| Opis zasad przechowywania dla kategorii Metryka lub dziennika. Używany tylko opcji konta magazynu.|
| dni| Int| Liczba dni przechowywania metryki lub dzienniki. Wartość 0 zachowuje dzienniki w nieskończoność. Używany tylko opcji konta magazynu. |

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

Pobieranie informacji na temat ustawienia diagnostyki w interfejsu API REST Monitor Azure

**Żądanie**
```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

**Nagłówki**
* Zastąp `{api-version}` z `2016-09-01`.
* Zastąp `{resource-id}` o identyfikatorze zasobu zasobu, dla którego chcesz edytować ustawienia diagnostyki. Aby uzyskać więcej informacji o używanie grup zasobów do zarządzania zasobami platformy Azure.
* Ustaw `Content-Type` nagłówka do `application/json`.
* Ustaw nagłówek autoryzacji żetonu Web JSON uzyskanej od usługi Azure Active Directory. Aby uzyskać więcej informacji zobacz Authenticating żądania.

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
[Aby dowiedzieć się więcej tutaj](https://docs.microsoft.com/en-us/rest/api/monitor/diagnosticsettings)

## <a name="schema-of-logs--events"></a>Schemat dzienniki & zdarzenia

### <a name="activity-run-logs-attributes"></a>Atrybuty dzienniki wykonywania działań

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
   "properties:"
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
| Poziom |Ciąg | Poziom dziennika diagnostycznego. Poziom 4 jest zawsze w przypadku uruchamiania dzienniki działania. | `4`  |
| correlationId |Ciąg | Unikatowy identyfikator określonego żądania end-to-end śledzić | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | Ciąg | Czas zdarzenia w zakres czasu, w formacie UTC | `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|activityRunId| Ciąg| Identyfikator uruchamiania działania | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|pipelineRunId| Ciąg| Identyfikator procesu, uruchom | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|resourceId| Ciąg | Identyfikator skojarzonego zasobu dla zasobu fabryki danych | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| Ciąg | Kategoria dzienników diagnostycznych. Ustaw tę właściwość na "Uruchomień" | `ActivityRuns` |
|poziom| Ciąg | Poziom dziennika diagnostycznego. Ustaw tę właściwość na "Informacyjny" | `Informational` |
|operationName| Ciąg |Nazwa działania o stanie. Jeśli stan jest pulsu start, jest `MyActivity -`. Jeśli stan jest pulsu zakończenia, jest `MyActivity - Succeeded` ze stanem końcowym | `MyActivity - Succeeded` |
|pipelineName| Ciąg | Nazwa potoku | `MyPipeline` |
|activityName| Ciąg | Nazwa działania | `MyActivity` |
|rozpoczynanie| Ciąg | Początek działania Uruchom w zakres czasu, w formacie UTC | `2017-06-26T20:55:29.5007959Z`|
|end| Ciąg | Uruchom kończy działanie w zakres czasu, w formacie UTC. Jeśli działanie nie zakończyła jeszcze (dzienników diagnostycznych do uruchamiania działania), domyślną wartość `1601-01-01T00:00:00Z` jest ustawiona.  | `2017-06-26T20:55:29.5007959Z` |


### <a name="pipeline-run-logs-attributes"></a>Dzienniki wykonywania atrybutów w potoku

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
| Poziom |Ciąg | Poziom dziennika diagnostycznego. Poziom 4 jest tak w przypadku uruchamiania dzienniki działania. | `4`  |
| correlationId |Ciąg | Unikatowy identyfikator określonego żądania end-to-end śledzić | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | Ciąg | Czas zdarzenia w zakres czasu, w formacie UTC | `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|runId| Ciąg| Identyfikator procesu, uruchom | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|resourceId| Ciąg | Identyfikator skojarzonego zasobu dla zasobu fabryki danych | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| Ciąg | Kategoria dzienników diagnostycznych. Ustaw tę właściwość na "PipelineRuns" | `PipelineRuns` |
|poziom| Ciąg | Poziom dziennika diagnostycznego. Ustaw tę właściwość na "Informacyjny" | `Informational` |
|operationName| Ciąg |Nazwa potoku o stanie. "Potoku - zakończyło się pomyślnie" z stan końcowy po zakończeniu potoku uruchamiania| `MyPipeline - Succeeded` |
|pipelineName| Ciąg | Nazwa potoku | `MyPipeline` |
|rozpoczynanie| Ciąg | Początek działania Uruchom w zakres czasu, w formacie UTC | `2017-06-26T20:55:29.5007959Z`|
|end| Ciąg | Uruchamia zakończenia działania w zakres czasu, w formacie UTC. Jeśli działanie nie zakończyła jeszcze (dzienników diagnostycznych do uruchamiania działania), domyślną wartość `1601-01-01T00:00:00Z` jest ustawiona.  | `2017-06-26T20:55:29.5007959Z` |
|status| Ciąg | Stan końcowy potoku uruchamiania (powodzenie lub niepowodzenie) | `Succeeded`|


### <a name="trigger-run-logs-attributes"></a>Wykonywania wyzwalacza logowania atrybutów

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
| Poziom |Ciąg | Poziom dziennika diagnostycznego. Ustaw poziom 4 dla działania uruchamiania dzienników. | `4`  |
| correlationId |Ciąg | Unikatowy identyfikator określonego żądania end-to-end śledzić | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | Ciąg | Czas zdarzenia w zakres czasu, w formacie UTC | `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|triggerId| Ciąg| Identyfikator wyzwalacz uruchomienia | `08587023010602533858661257311` |
|resourceId| Ciąg | Identyfikator skojarzonego zasobu dla zasobu fabryki danych | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| Ciąg | Kategoria dzienników diagnostycznych. Ustaw tę właściwość na "PipelineRuns" | `PipelineRuns` |
|poziom| Ciąg | Poziom dziennika diagnostycznego. Ustaw tę właściwość na "Informacyjny" | `Informational` |
|operationName| Ciąg |Nazwa wyzwalacza z stan końcowy określa, czy pomyślnie uruchomił. "MyTrigger - powiodło się" Jeśli pulsu zakończyło się pomyślnie| `MyTrigger - Succeeded` |
|triggerName| Ciąg | Nazwa wyzwalacza | `MyTrigger` |
|triggerType| Ciąg | Typ wyzwalacza (Ręczne wyzwalacza lub wyzwalacza harmonogram) | `ScheduleTrigger` |
|triggerEvent| Ciąg | Zdarzenia wyzwalacza | `ScheduleTime - 2017-07-06T01:50:25Z` |
|rozpoczynanie| Ciąg | Początek uruchomić wyzwalacz w zakres czasu, w formacie UTC | `2017-06-26T20:55:29.5007959Z`|
|status| Ciąg | Stan końcowy czy wyzwalacz pomyślnie uruchamiany (powodzenie lub niepowodzenie) | `Succeeded`|

## <a name="metrics"></a>Metryki

Azure Monitor umożliwia korzystanie z telemetrię, aby uzyskać wgląd w wydajności i kondycji obciążeń na platformie Azure. Typ najważniejszych danych telemetrycznych platformy Azure jest metryki (nazywanych również liczniki wydajności) emitowane przez zasoby najbardziej platformy Azure. Azure Monitor udostępnia kilka sposobów konfigurowania i korzystać z tych metryk do monitorowania i rozwiązywania problemów.

ADFV2 emituje następujące metryki

| **Metryka**           | **Nazwa wyświetlana metryki**         | **Unit** | **Typ agregacji** | **Opis**                                       |
|----------------------|---------------------------------|----------|----------------------|-------------------------------------------------------|
| PipelineSucceededRun | Pomyślnie metryki uruchamia potoku | Licznik    | Łącznie                | Całkowita liczba potoków uruchamia zakończyło się pomyślnie w ciągu minuty okna |
| PipelineFailedRuns   | Nie powiodło się metryki uruchamia potoku    | Licznik    | Łącznie                | Całkowita liczba potoków uruchamia nie powiodło się w ciągu minuty okna    |
| ActiviySucceededRuns | Pomyślnie metryki uruchomień działania | Licznik    | Łącznie                | Całkowita liczba działanie jest uruchomione pomyślnie w ciągu minuty okna  |
| ActivityFailedRuns   | Nie powiodło się metryki uruchomień działania    | Licznik    | Łącznie                | Całkowita liczba odbywa się działanie nie powiodło się w ciągu minuty okna     |
| TriggerSucceededRuns | Pomyślnie metryki uruchamia wyzwalacz  | Licznik    | Łącznie                | Całkowita wyzwalacz uruchamia zakończyło się pomyślnie w ciągu minuty okna   |
| TriggerFailedRuns    | Nie powiodło się wyzwalacz uruchamia metryk     | Licznik    | Łącznie                | Całkowita wyzwalacz uruchamia nie powiodło się w ciągu minuty okna      |

Aby uzyskać dostęp do metryk, postępuj zgodnie z instrukcjami w artykule- https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics

## <a name="alerts"></a>Alerty

Może zgłaszać alerty w przypadku obsługiwanych metryki w fabryce danych. Kliknij przycisk **alerty** przycisk w fabryce danych **Monitor** strony.

![Opcja alertów](media/monitor-using-azure-monitor/alerts_image1.png)

Powoduje to przejście do **alerty** strony.

![Strona alertów](media/monitor-using-azure-monitor/alerts_image2.png)

Możesz również zalogować się do portalu Azure i kliknij przycisk **Monitor —&gt; alerty** nawiązać **alerty** strony bezpośrednio.

![Alerty w menu portalu](media/monitor-using-azure-monitor/alerts_image3.png)

### <a name="create-alerts"></a>Tworzenie alertów

1.  Kliknij przycisk **+ nową regułę alertu** do utworzenia nowego alertu.

    ![Nowe reguły alertu](media/monitor-using-azure-monitor/alerts_image4.png)

2.  Zdefiniuj **alertów warunku**.

    > [!NOTE]
    > Upewnij się wybrać **wszystkie** w **Filtruj według typu zasobu**.

    ![Warunek alertu, ekranu 1 z 3](media/monitor-using-azure-monitor/alerts_image5.png)

    ![Warunek alertu, ekranu 2 z 3](media/monitor-using-azure-monitor/alerts_image6.png)

    ![Warunek alertu, ekranu 3 z 3](media/monitor-using-azure-monitor/alerts_image7.png)

3.  Zdefiniuj **szczegóły alertu**.

    ![Szczegóły alertu](media/monitor-using-azure-monitor/alerts_image8.png)

4.  Zdefiniuj **grupy akcji**.

    ![Grupy akcji, ekran 1 z 4](media/monitor-using-azure-monitor/alerts_image9.png)

    ![Grupy akcji, ekran 2 z 4](media/monitor-using-azure-monitor/alerts_image10.png)

    ![Grupy akcji, ekran 3 z 4](media/monitor-using-azure-monitor/alerts_image11.png)

    ![Grupy akcji, ekranu 4 z 4](media/monitor-using-azure-monitor/alerts_image12.png)

## <a name="next-steps"></a>Kolejne kroki
Zobacz [monitora i programowe zarządzanie potoki](monitor-programmatically.md) artykułu, aby uzyskać informacje o monitorowaniu i zarządzaniu nimi potoki, uruchamiając.
