---
title: Przygotowywanie do migracji alertów klasycznych usługi Azure Monitor, aktualizując usługi logic apps i elementów runbook
description: Dowiedz się, jak zmodyfikować swoje elementy webhook, logic apps i elementy runbook w celu przygotowania do migracji dobrowolne.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: bdbd45c2b10dec8f1c0a85110747a470e818dbf9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66015604"
---
# <a name="prepare-your-logic-apps-and-runbooks-for-migration-of-classic-alert-rules"></a>Przygotowanie usługi logic apps i elementów runbook do migracji z klasycznej reguły alertu

Jako [ogłoszonej wcześniej](monitoring-classic-retirement.md), alertów klasycznych w usłudze Azure Monitor są zostanie wycofana w 2019 września (został pierwotnie 2019 lipca). Narzędzie migracji jest dostępne w portalu Azure, aby klienci używają reguł alertów klasycznych i którzy chcą wyzwolić migrację samodzielnie.

> [!NOTE]
> Ze względu na opóźnienie wdrożenie narzędzie do migracji dacie wycofania migracji alertów klasycznych został rozszerzony do 31 sierpnia 2019 od daty pierwotnie ogłoszone się 30 czerwca 2019 r.

Jeśli zdecydujesz się dobrowolnie regułami alertów klasycznych na nowe reguły alertu, należy pamiętać, że istnieją pewne różnice między dwoma systemami. W tym artykule opisano te różnice i jak można przygotować zmiany.

## <a name="api-changes"></a>Zmiany interfejsu API

Interfejsy API, które tworzenie i zarządzanie nimi reguły alertów klasycznych (`microsoft.insights/alertrules`) różni się od interfejsów API, które tworzenie i zarządzanie nimi nowych alertów dotyczących metryk (`microsoft.insights/metricalerts`). Jeśli można programowo tworzyć i zarządzać klasycznej reguły alertu już dziś, należy zaktualizować skrypty wdrażania do pracy z nowych interfejsów API.

Poniższa tabela jest odwołaniem do interfejsów programistycznych dla klasycznych i nowych alertów:

|         |Alerty klasyczne  |Nowe alerty metryki |
|---------|---------|---------|
|Interfejs API REST     | [microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules)         | [microsoft.insights/metricalerts](https://docs.microsoft.com/rest/api/monitor/metricalerts)       |
|Interfejs wiersza polecenia platformy Azure     | [AZ monitor alert](https://docs.microsoft.com/cli/azure/monitor/alert?view=azure-cli-latest)        | [alert metryki monitora az](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest)        |
|PowerShell      | [Dokumentacja](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrule)       |  [Dokumentacja](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrulev2)    |
| Szablon usługi Azure Resource Manager | [Dla alertów klasycznych](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-enable-template)|[Aby uzyskać nowe alerty metryki](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates)|

## <a name="notification-payload-changes"></a>Powiadomienie ulega zmianie z ładunku

Format ładunku powiadomień różni się nieco między [klasycznej reguły alertu](alerts-webhooks.md) i [nowych alertów dotyczących metryk](alerts-metric-near-real-time.md#payload-schema). W przypadku dowolnego elementu webhook, aplikacja logiki lub akcji elementu runbook, które są wyzwalane przez reguły alertów klasycznych, należy zaktualizować tych punktów końcowych powiadomień o zaakceptowanie format ładunku nowych alertów dotyczących metryk.

Poniższa tabela umożliwia mapowanie pól ładunek elementu webhook z klasycznym formatu do nowego formatu:

|  |Alerty klasyczne  |Nowe alerty metryki |
|---------|---------|---------|
|Został alert aktywowany lub rozwiązane    | **status**       | **data.status** |
|Informacje kontekstowe o alercie     | **Kontekst**        | **data.context**        |
|Sygnatura czasowa, o której aktywowane lub rozwiązany alert     | **context.timestamp**       | **data.context.timestamp**        |
| Identyfikator reguły alertu | **context.ID** | **data.context.id** |
| Nazwa reguły alertu | **context.name** | **data.context.name** |
| Opis reguły alertu | **context.description** | **data.context.description** |
| Warunek reguły alertu | **context.condition** | **data.context.condition** |
| Nazwa metryki | **context.condition.metricName** | **data.context.condition.allOf[0].metricName** |
| Agregacja czasu (w sposób zagregowane metryki przedziale oceny)| **data.context.condition.timeAggregation** | **data.context.condition.timeAggregation** |
| Okres próbny | **context.condition.windowSize** | **data.context.condition.windowSize** |
| Operator (jak zagregowanych wartość metryki jest porównywana wartość progową) | **context.condition.operator** | **data.context.condition.operator** |
| Próg | **context.condition.threshold** | **data.context.condition.allOf[0].threshold** |
| Wartość metryki | **context.condition.metricValue** | **data.context.condition.allOf[0].metricValue** |
| Identyfikator subskrypcji | **context.subscriptionId** | **data.context.subscriptionId** |
| Grupa zasobów zasobu | **context.resourceGroup** | **data.context.resourceGroup** |
| Nazwa zasobu | **context.resourceName** | **data.context.resourceName** |
| Typ zasobu | **context.resourceType** | **data.context.resourceType** |
| Identyfikator zasobu zasobu | **context.resourceId** | **data.context.resourceId** |
| Bezpośredni link do strony Podsumowanie zasobu portalu | **context.portalLink** | **data.context.portalLink** |
| Niestandardowy ładunek pola, które zostaną przekazane do elementu webhook lub aplikacja logiki | **Właściwości** | **data.properties** |

Ładunki są podobne, jak pokazano. Oferują następującą sekcję:

- Szczegółowe informacje dotyczące modyfikowania aplikacji logiki do pracy z nowym formatem.
- Przykład elementu runbook, który analizuje ładunek powiadomienia dotyczące nowych alertów.

## <a name="modify-a-logic-app-to-receive-a-metric-alert-notification"></a>Zmodyfikuj aplikację logiki w celu odbierania powiadomień o alertach metryki

Jeśli używasz aplikacji logiki za pomocą alertów klasycznych, należy zmodyfikować kod aplikacji logiki można przeanalizować ładunku nowych alertów dotyczących metryk. Wykonaj następujące kroki:

1. Utwórz nową aplikację logiki.

1. Szablon "Azure Monitor — metryki obsługi alertu". Ten szablon zawiera **żądania HTTP** wyzwalacza przy użyciu odpowiedniego schematu, które są zdefiniowane.

    ![szablon aplikacji logiki](media/alerts-migration/logic-app-template.png "szablon alertu metryki")

1. Dodawanie akcji do hostowania swojej logiki przetwarzania.

## <a name="use-an-automation-runbook-that-receives-a-metric-alert-notification"></a>Użyj elementu runbook usługi automation, który otrzyma powiadomienie o alertu metryki

W poniższym przykładzie przedstawiono kod programu PowerShell do użycia w elemencie runbook. Ten kod można przeanalizować ładunków dla reguł alertów dotyczących metryk klasycznych i nowych reguł alertów dotyczących metryk.

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

Aby uzyskać pełny przykład element runbook, który zatrzymuje maszynę wirtualną, gdy alert jest wyzwalany, zobacz [dokumentacji usługi Azure Automation](https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook).

## <a name="partner-integration-via-webhooks"></a>Integracja z partnerami za pośrednictwem elementów webhook

Większość [naszych partnerów, które integrują się z alertów klasycznych](https://docs.microsoft.com/azure/azure-monitor/platform/partners) już obsługuje nowszych alertów metryk za pośrednictwem ich integracji. Dostępne są następujące znane integracji, które już działają z nowych alertów dotyczących metryk:

- [PagerDuty](https://www.pagerduty.com/docs/guides/azure-integration-guide/)
- [OpsGenie](https://docs.opsgenie.com/docs/microsoft-azure-integration)
- [Aplikacji Signl4](https://www.signl4.com/blog/mobile-alert-notifications-azure-monitor/)

Jeśli używasz Integracja z partnerami, który nie znajduje się w tym miejscu, upewnij się, za pomocą dostawcy integracji że integracji współpracuje z nowych alertów dotyczących metryk.

## <a name="next-steps"></a>Kolejne kroki

- [Sposób użycia narzędzia migracji](alerts-using-migration-tool.md)
- [Zrozumienie sposobu działania narzędzia migracji](alerts-understand-migration.md)
