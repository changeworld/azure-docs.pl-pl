---
title: Przygotowywanie do migracji alertów klasycznych usługi Azure Monitor, aktualizując usługi logic apps i elementów runbook
description: Dowiedz się, jak zmodyfikować element webhook, aplikację logiki i elementy runbook w celu przygotowania do migracji dobrowolne.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 3c47404826d5055d4a82d4842523f790fb11f000
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60346895"
---
# <a name="prepare-your-logic-apps-and-run-books-for-classic-alert-rules-migration"></a>Przygotowanie aplikacji usługi logic apps i uruchom książki na potrzeby migracji klasycznej reguły alertu

Jako [ogłoszonej wcześniej](monitoring-classic-retirement.md), alertów klasycznych w usłudze Azure Monitor są zostanie wycofana w lipcu 2019 r. Narzędzie migracji, aby wyzwolić dobrowolnie migracji jest dostępne w witrynie Azure portal i wprowadza się klienci, którzy używają klasycznej reguły alertu.

Jeśli zdecydujesz się dobrowolnie regułami alertów klasycznych na nowe reguły alertu, istnieją pewne różnice między dwoma systemami, które należy wiedzieć. Ten artykuł przeprowadzi Cię różnice między dwoma systemami i jak można przygotować zmiany.

## <a name="api-changes"></a>Zmiany interfejsu API

Interfejsy API używane do tworzenia/zarządzania klasyczne reguły alertów (`microsoft.insights/alertrules`) różni się od interfejsy API używane do tworzenia/zarządzania nowych alertów dotyczących metryk (`microsoft.insights/metricalerts`). Jeśli możesz programowo utworzyć/Zarządzanie klasycznej reguły alertu już dziś, należy zaktualizować skrypty wdrażania do pracy z nowych interfejsów API.

Poniższa tabela zawiera odwołanie do interfejsów programistycznych, klasycznych i nowych alertów.

|         |Alerty klasyczne  |Nowe alerty metryki |
|---------|---------|---------|
|Interfejs API REST     | [microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules)         | [microsoft.insights/metricalerts](https://docs.microsoft.com/rest/api/monitor/metricalerts)       |
|Interfejs wiersza polecenia platformy Azure     | [AZ monitor alert](https://docs.microsoft.com/cli/azure/monitor/alert?view=azure-cli-latest)        | [alert metryki monitora az](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest)        |
|PowerShell      | [Dokumentacja](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrule)       |      |
| Szablon usługi Azure Resource Manager | [Dla alertów klasycznych](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-enable-template)|[Aby uzyskać nowe alerty metryki](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates)|

## <a name="notification-payload-changes"></a>Powiadomienie ulega zmianie z ładunku

Format ładunku powiadomień różni się nieco między [klasycznej reguły alertu](alerts-webhooks.md) i [nowych alertów dotyczących metryk](alerts-metric-near-real-time.md#payload-schema). W przypadku dowolnego elementu webhook, aplikacja logiki lub elementu runbook akcje inicjowane przez reguły alertów klasycznych, należy zaktualizować tych punktów końcowych powiadomień o zaakceptowanie format ładunku nowych alertów dotyczących metryk.

Poniższa tabela umożliwia mapowanie pól między klasycznej reguły alertu, ładunek elementu webhook i nowe ładunek elementu webhook na alertu metryki.

|  |Alerty klasyczne  |Nowe alerty metryki |
|---------|---------|---------|
|Został alert aktywowany lub rozwiązane     | status       | data.status |
|Informacje kontekstowe o alercie     | Kontekst        | data.context        |
|Sygnatura czasowa, o której aktywowane lub rozwiązany alert      | context.timestamp       | data.context.timestamp        |
| Identyfikator reguły alertu | context.ID | data.context.id |
| Nazwa reguły alertu | context.name | data.context.name |
| Opis reguły alertu | context.description | data.context.description |
| Warunek reguły alertu | context.condition | data.context.condition|
| Nazwa metryki | context.condition.metricName| data.context.condition.allOf[0].metricName|
| Agregacja czasu (w sposób zagregowane metryki przedziale oceny)|data.context.condition.timeAggregation|data.context.condition.timeAggregation|
| Okres próbny | context.condition.windowSize | data.context.condition.windowSize|
| Operator (jak zagregowanych wartość metryki jest porównywana wartość progową) | context.condition.operator | data.context.condition.operator|
| Próg | context.condition.threshold| data.context.condition.allOf[0].threshold|
| Wartość metryki | context.condition.metricValue | data.context.condition.allOf[0].metricValue|
| Identyfikator subskrypcji | context.subscriptionId | data.context.subscriptionId|
| Grupa zasobów zasobu | context.resourceGroup | data.context.resourceGroup|
| Nazwa zasobu | context.resourceName | data.context.resourceName |
| Typ zasobu | context.resourceType | data.context.resourceType |
|  Identyfikator zasobu zasobu | context.resourceId | data.context.resourceId |
| Bezpośredni link do strony Podsumowanie zasobu portalu | context.portalLink | data.context.portalLink|
| Niestandardowy ładunek pola, które zostaną przekazane do elementu webhook lub aplikacja logiki | properties |data.properties |

Jak widać, zarówno ładunki są podobne. Tej sekcji zawiera szczegółowe informacje na temat przykładowych aplikacji logiki i przykładowego elementu runbook można przeanalizować ładunek powiadomienia dotyczące nowych alertów.

## <a name="using-a-logic-app-that-receives-a-metric-alert-notification"></a>Przy użyciu aplikacji logiki, który otrzyma powiadomienie o alertu metryki

Jeśli używasz aplikacji logiki za pomocą alertów klasycznych, należy zmodyfikować aplikację logiki, aby przeanalizować ładunku nowych alertów dotyczących metryk.

1. Utwórz nową aplikację logiki.

2. Szablon "Azure Monitor — metryki obsługi alertu". Ten szablon zawiera **żądania HTTP** wyzwalacza przy użyciu odpowiedniego schematu, które są zdefiniowane

    ![szablon aplikacji logiki](media/alerts-migration/logic-app-template.png "szablon alertu metryki")

3. Dodawanie akcji do hostowania swojej logiki przetwarzania.

## <a name="using-an-automation-runbook-that-receives-a-metric-alert-notification"></a>Przy użyciu elementu runbook usługi automation, który otrzyma powiadomienie o alertu metryki

Poniższy przykład zawiera kod programu PowerShell, który może służyć w elemencie runbook, które mogą przeanalizować ładunków dla reguł alertów dotyczących metryk klasycznych i nowych reguł alertów dotyczących metryk.

```PS
## Sample PowerShell code to be used in a runbook to handle parsing of both classic and new metric alerts

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

    # Identify if the alert triggering the runbook is a classic metric alert or a new metric alert (depends on the payload schema).
    $schemaId = $WebhookBody.schemaId
    Write-Verbose "schemaId: $schemaId" -Verbose
    if ($schemaId -eq "AzureMonitorMetricAlert") {

        # This is the new Metric Alert schema
        $AlertContext = [object] ($WebhookBody.data).context
        $status = ($WebhookBody.data).status

        # Parse fields related to alert rule condition
        $metricName = $AlertContext.condition.allOf[0].metricName
        $metricValue = $AlertContext.condition.allOf[0].metricValue
        $threshold = $AlertContext.condition.allOf[0].threshold
        $timeAggregation = $AlertContext.condition.allOf[0].timeAggregation
    }
    elseif ($schemaId -eq $null) {
        # This is the classic Metric Alert schema
        $AlertContext = [object] $WebhookBody.context
        $status = $WebhookBody.status

        # Parse fields related to alert rule condition
        $metricName = $AlertContext.condition.metricName
        $metricValue = $AlertContext.condition.metricValue
        $threshold = $AlertContext.condition.threshold
        $timeAggregation = $AlertContext.condition.timeAggregation
    }
    else {
        # The schema is not either a classic metric alert or a new metric alert
        Write-Error "The alert data schema - $schemaId - is not supported."
    }

    #parse fields related to resource affected
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

Zobacz pełny przykład element runbook, który zatrzymuje Maszynę wirtualną, gdy alert jest wyzwalany w [dokumentacji usługi Azure Automation](https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook).

## <a name="partner-integration-via-webhooks"></a>Integracja z partnerami za pośrednictwem elementów webhook

Większość [naszych partnerów, które integrują się z alertów klasycznych](https://docs.microsoft.com/azure/azure-monitor/platform/partners) już obsługuje nowszych alertów metryk za pośrednictwem ich integracji. Poniżej przedstawiono znane integracji, które już działają z nowych alertów dotyczących metryk.

- [PagerDuty](https://www.pagerduty.com/docs/guides/azure-integration-guide/)
- [OpsGenie](https://docs.opsgenie.com/docs/microsoft-azure-integration)
- [Aplikacji Signl4](https://www.signl4.com/blog/mobile-alert-notifications-azure-monitor/)

Korzystając z integracji z partnerami, który nie znajduje się w tym miejscu, upewnij się, za pomocą dostawcy integracji że integracji współpracuje z nowych alertów dotyczących metryk.

## <a name="next-steps"></a>Kolejne kroki

- [Sposób użycia narzędzia migracji](alerts-using-migration-tool.md)
- [Zrozumienie sposobu działania narzędzia migracji](alerts-understand-migration.md)
