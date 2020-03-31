---
title: Przygotowanie do migracji alertów klasycznych usługi Azure Monitor przez aktualizowanie aplikacji logiki i śmięty
description: Dowiedz się, jak zmodyfikować elementy webhook, aplikacje logiki i elementy runbook, aby przygotować się do dobrowolnej migracji.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 03/19/2018
ms.subservice: alerts
ms.openlocfilehash: 9219e105acb98424939030af76b526d475585619
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665596"
---
# <a name="prepare-your-logic-apps-and-runbooks-for-migration-of-classic-alert-rules"></a>Przygotowywanie aplikacji logiki i elementów Runbook do migracji reguł alertów klasycznych

Zgodnie [z wcześniejszymi zapowiedziami](monitoring-classic-retirement.md)klasyczne alerty w usłudze Azure Monitor są wycofywane we wrześniu 2019 r. (pierwotnie w lipcu 2019 r.). Narzędzie do migracji jest dostępne w witrynie Azure portal dla klientów, którzy używają klasycznych reguł alertów i którzy sami chcą wyzwolić migrację.

> [!NOTE]
> Ze względu na opóźnienie we wprowadzaniu narzędzia migracji data wycofania dla migracji alertów klasycznych została przedłużona do 31 sierpnia 2019 r. z pierwotnie ogłoszonej daty 30 czerwca 2019 r.

Jeśli zdecydujesz się dobrowolnie migrować klasyczne reguły alertów do nowych reguł alertów, należy pamiętać, że istnieją pewne różnice między tymi dwoma systemami. W tym artykule wyjaśniono te różnice i jak można przygotować się do zmiany.

## <a name="api-changes"></a>Zmiany api

Interfejsy API, które tworzą klasyczne`microsoft.insights/alertrules`reguły alertów i nimi zarządzają ( )`microsoft.insights/metricalerts`różnią się od interfejsów API, które tworzą nowe alerty metryki i zarządzają nimi ( ). Jeśli programowo utworzyć i zarządzać klasyczne reguły alertów dzisiaj, zaktualizować skrypty wdrażania do pracy z nowymi interfejsami API.

Poniższa tabela jest odwołaniem do interfejsów programowych dla alertów klasycznych i nowych:

|         |Alerty klasyczne  |Nowe alerty metryki |
|---------|---------|---------|
|Interfejs API REST     | [microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules)         | [microsoft.insights/metricalerts](https://docs.microsoft.com/rest/api/monitor/metricalerts)       |
|Interfejs wiersza polecenia platformy Azure     | [alert monitora az](https://docs.microsoft.com/cli/azure/monitor/alert?view=azure-cli-latest)        | [alert o metrykach monitora az](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest)        |
|PowerShell      | [Tematy pomocy](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrule)       |  [Tematy pomocy](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrulev2)    |
| Szablon usługi Azure Resource Manager | [W przypadku klasycznych alertów](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-enable-template)|[Nowe alerty dotyczące metryk](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates)|

## <a name="notification-payload-changes"></a>Zmiany ładunku powiadomień

Format ładunku powiadomień różni się nieco między [klasycznymi regułami alertów](alerts-webhooks.md) a [nowymi alertami metryk.](alerts-metric-near-real-time.md#payload-schema) Jeśli masz żadnych webhook, aplikacji logiki lub runbook akcje, które są wyzwalane przez klasyczne reguły alertów, należy zaktualizować te punkty końcowe powiadomień, aby zaakceptować format ładunku nowych alertów metryki.

Poniższa tabela służy do mapowania pól ładunku elementu webhook z formatu klasycznego na nowy format:

|  |Alerty klasyczne  |Nowe alerty metryki |
|---------|---------|---------|
|Czy alert został aktywowany lub rozwiązany?    | **Stan**       | **data.status** |
|Informacje kontekstowe o wpisie     | **Kontekście**        | **data.context**        |
|Sygnatura czasowa, w której alert został aktywowany lub rozwiązany     | **sygnatura czasowa context.time**       | **data.context.timestamp**        |
| Identyfikator reguły alertu | **context.id** | **data.context.id** |
| Nazwa reguły alertu | **context.name** | **data.context.name** |
| Opis reguły alertu | **context.description** | **data.context.description** |
| Warunek reguły alertu | **context.condition** | **data.context.condition** |
| Nazwa metryki | **context.condition.metricName** | **data.context.condition.allOf[0].metricName** |
| Agregacja czasu (jak metryka jest agregowana w oknie oceny)| **context.condition.timeAgregacja** | **context.condition.timeAgregacja** |
| Okres oceny | **context.condition.windowSize** | **data.context.condition.windowZmiaśnia** |
| Operator (jak porównywana jest zagregowana wartość metryki z wartością progową) | **context.condition.operator** | **data.context.condition.operator** |
| Próg | **context.condition.threshold** | **data.context.condition.allOf[0].próg** |
| Wartość metryczna | **context.condition.metricWartość** | **data.context.condition.allOf[0].metricValue** |
| Identyfikator subskrypcji | **identyfikator context.subscriptionId** | **identyfikator data.context.subscriptionId** |
| Grupa zasobów zasobu, którego dotyczy problem | **context.resourceGroup** | **data.context.resourceGroup** |
| Nazwa zasobu, którego dotyczy problem | **nazwa pliku context.resourceName** | **data.context.resourceName** |
| Typ zasobu, którego dotyczy problem | **context.resourceType** | **data.context.resourceType** |
| Identyfikator zasobu, którego dotyczy problem | **identyfikator context.resource** | **identyfikator data.context.resourceId** |
| Bezpośrednie łącze do strony podsumowania zasobów portalu | **context.portalLink** | **data.context.portalLink** |
| Niestandardowe pola ładunku, które mają być przekazywane do elementu webhook lub aplikacji logiki | **Właściwości** | **data.properties** |

Ładunki są podobne, jak widać. W poniższej sekcji przedstawiono następujące informacje:

- Szczegółowe informacje na temat modyfikowania aplikacji logiki do pracy z nowym formatem.
- Przykład owy, który analizuje ładunek powiadomień dla nowych alertów.

## <a name="modify-a-logic-app-to-receive-a-metric-alert-notification"></a>Modyfikowanie aplikacji logiki w celu odebrania powiadomienia o alertach metryk

Jeśli używasz aplikacji logiki z alertami klasycznymi, należy zmodyfikować kod aplikacji logiki, aby przeanalizować ładunek alertów nowej metryki. Wykonaj następujące kroki:

1. Utwórz nową aplikację logiki.

1. Użyj szablonu "Azure Monitor — Metrics Alert Handler". Ten szablon ma wyzwalacz **żądania HTTP** z odpowiednim zdefiniowanym schematem.

    ![logika-app-szablon](media/alerts-migration/logic-app-template.png "Szablon alertu metryki")

1. Dodaj akcję do hosta logiki przetwarzania.

## <a name="use-an-automation-runbook-that-receives-a-metric-alert-notification"></a>Użyj systemu runbook automatyzacji, który odbiera powiadomienie o alertach metryki

Poniższy przykład zawiera kod programu PowerShell do użycia w życiówce. Ten kod można przeanalizować ładunki dla reguł alertów klasycznej metryki i nowych reguł alertów metryki.

```PowerShell
## Example PowerShell code to use in a runbook to handle parsing of both classic and new metric alerts.

[OutputType("PSAzureOperationResponse")]

param
(
    [Parameter (Mandatory=$false)]
    [object] $WebhookData
)

$ErrorActionPreference = "stop"

if ($WebhookData)
{
    # Get the data object from WebhookData.
    $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Determine whether the alert triggering the runbook is a classic metric alert or a new metric alert (depends on the payload schema).
    $schemaId = $WebhookBody.schemaId
    Write-Verbose "schemaId: $schemaId" -Verbose
    if ($schemaId -eq "AzureMonitorMetricAlert") {

        # This is the new metric alert schema.
        $AlertContext = [object] ($WebhookBody.data).context
        $status = ($WebhookBody.data).status

        # Parse fields related to alert rule condition.
        $metricName = $AlertContext.condition.allOf[0].metricName
        $metricValue = $AlertContext.condition.allOf[0].metricValue
        $threshold = $AlertContext.condition.allOf[0].threshold
        $timeAggregation = $AlertContext.condition.allOf[0].timeAggregation
    }
    elseif ($schemaId -eq $null) {
        # This is the classic metric alert schema.
        $AlertContext = [object] $WebhookBody.context
        $status = $WebhookBody.status

        # Parse fields related to alert rule condition.
        $metricName = $AlertContext.condition.metricName
        $metricValue = $AlertContext.condition.metricValue
        $threshold = $AlertContext.condition.threshold
        $timeAggregation = $AlertContext.condition.timeAggregation
    }
    else {
        # The schema is neither a classic metric alert nor a new metric alert.
        Write-Error "The alert data schema - $schemaId - is not supported."
    }

    # Parse fields related to resource affected.
    $ResourceName = $AlertContext.resourceName
    $ResourceType = $AlertContext.resourceType
    $ResourceGroupName = $AlertContext.resourceGroupName
    $ResourceId = $AlertContext.resourceId
    $SubId = $AlertContext.subscriptionId

    ## Your logic to handle the alert here.
}
else {
    # Error
    Write-Error "This runbook is meant to be started from an Azure alert webhook only."
}

```

Pełny przykład uruchomieniu, który zatrzymuje maszynę wirtualną po wyzwoleniu alertu, zobacz [dokumentację usługi Azure Automation.](https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook)

## <a name="partner-integration-via-webhooks"></a>Integracja z partnerami za pośrednictwem elementów webhook

Większość [naszych partnerów, którzy integrują się z klasycznymi alertami,](https://docs.microsoft.com/azure/azure-monitor/platform/partners) obsługuje już nowsze alerty metryczne za pośrednictwem ich integracji. Znane integracje, które już działają z nowymi alertami metryk są:

- [PagerDuty](https://www.pagerduty.com/docs/guides/azure-integration-guide/)
- [OpsGenie](https://docs.opsgenie.com/docs/microsoft-azure-integration)
- [Znak 4](https://www.signl4.com/blog/mobile-alert-notifications-azure-monitor/)

Jeśli używasz integracji partnera, który nie jest wymieniony w tym miejscu, upewnij się z dostawcą integracji, że integracja działa z nowymi alertami metryki.

## <a name="next-steps"></a>Następne kroki

- [Jak korzystać z narzędzia do migracji](alerts-using-migration-tool.md)
- [Informacje o sposobie działania narzędzia do migracji](alerts-understand-migration.md)
