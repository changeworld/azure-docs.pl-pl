---
title: Tworzenie, wyświetlanie i zarządzanie dziennika alertów za pomocą usługi Azure Monitor | Dokumentacja firmy Microsoft
description: Usługi Azure Monitor umożliwia tworzenie, wyświetlanie i zarządzanie reguł alertów dzienników na platformie Azure.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: f758007a0fa0d7fb619873d94d762e7019077e05
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66427452"
---
# <a name="create-view-and-manage-log-alerts-using-azure-monitor"></a>Tworzenie, wyświetlanie i zarządzanie alerty dzienników przy użyciu usługi Azure Monitor

## <a name="overview"></a>Omówienie
W tym artykule pokazano, jak skonfigurować alerty dzienników przy użyciu interfejsu alertów w witrynie Azure portal. Definicja reguły alertu znajduje się w trzech części:
- Cel: Określony zasób platformy Azure, która ma być monitorowane
- Kryteria: Określony warunek lub Logic Apps, gdy widoczne w sygnał, powinny wyzwalać akcję
- Akcja: Wywołań wysyłanych do odbiorcy powiadomienia — Wyślij wiadomość e-mail, wiadomość SMS, element webhook itp.

Termin **alertów dzienników** do opisania alerty, gdy sygnał jest zapytanie dziennika w [obszaru roboczego usługi Log Analytics](../learn/tutorial-viewdata.md) lub [usługi Application Insights](../app/analytics.md). Dowiedz się więcej o funkcji, terminologii i typów z [rejestrowania alertów — omówienie](alerts-unified-log.md).

> [!NOTE]
> Dane dzienników popularnych z [obszar roboczy usługi Log Analytics](../../azure-monitor/learn/tutorial-viewdata.md) jest obecnie również dostępna na platformie metryki w usłudze Azure Monitor. Dla widoku szczegółów [alertu metryki dla dzienników](alerts-metric-logs.md)

## <a name="managing-log-alerts-from-the-azure-portal"></a>Zarządzanie alertami dzienników w witrynie Azure portal

Szczegółowe dalej jest przewodnik krok po kroku za pomocą alertów dzienników przy użyciu interfejsu portalu platformy Azure.

### <a name="create-a-log-alert-rule-with-the-azure-portal"></a>Tworzenie reguł alertów dzienników w witrynie Azure portal
1. W [portal](https://portal.azure.com/), wybierz opcję **Monitor** i w sekcji monitorowanie — wybierz **alerty**.

    ![Monitorowanie](media/alerts-log/AlertsPreviewMenu.png)

1. Wybierz **nową regułę alertu** przycisk, aby utworzyć nowego alertu na platformie Azure.

    ![Dodaj Alert](media/alerts-log/AlertsPreviewOption.png)

1. W sekcji Tworzenie alertów jest wyświetlany z trzech części, składający się z: *Zdefiniuj warunek alertu*, *Zdefiniuj szczegóły alertu*, i *zdefiniuj grupę akcji*.

    ![Utwórz regułę](media/alerts-log/AlertsPreviewAdd.png)

1. Zdefiniuj warunek alertu przy użyciu **wybierz zasób** łącze i określenie docelowej wybierz zasób. Filtr, wybierając _subskrypcji_, _typ zasobu_oraz wymagane _zasobów_.

   > [!NOTE]
   > 
   > Do tworzenia dziennika alertów — weryfikowanie **dziennika** sygnał jest dostępna dla wybranego zasobu, przed kontynuowaniem.
   >  ![Wybierz zasób](media/alerts-log/Alert-SelectResourceLog.png)

1. *Alerty dzienników*: Upewnij się, **typ zasobu** jest źródłem analizy, takie jak *usługi Log Analytics* lub *usługi Application Insights* i sygnałów typ jako **dziennika**, niż raz odpowiednie **zasobów** jest wybrana, kliknij przycisk *gotowe*. Dalej używać **Dodaj kryteria** przycisk, aby wyświetlić listę sygnałów dostępnymi dla zasobu, a z listy sygnału **przeszukiwania dzienników niestandardowych** opcję dla wybranego dziennika usługi monitor, takich jak *dziennika Analiza* lub *usługi Application Insights*.

   ![Wybierz zasób — wyszukiwanie dzienników niestandardowych](media/alerts-log/AlertsPreviewResourceSelectionLog.png)

   > [!NOTE]
   > 
   > Listy można zaimportować zapytania usługi analytics jako typ sygnału — alerty **dziennika (zapisane zapytanie)** , jak pokazano powyżej ilustracji. Dzięki temu użytkownicy może zapytania w usłudze Analytics udoskonalania i zapisać je do użytku w przyszłości w alertach — więcej szczegółowych informacji na temat korzystania z Zapisywanie zapytań dostępne pod adresem [przy użyciu zapytań dzienników w usłudze Azure Monitor](../log-query/log-query-overview.md) lub [udostępnione zapytanie w analizy usługi application insights ](../log-query/log-query-overview.md).

1. *Alerty dzienników*: Po wybraniu zapytania dotyczące alertów może być wyrażona w **zapytania wyszukiwania** pola; Jeśli składnia zapytania jest nieprawidłowa, pola są wyświetlane w kolorze CZERWONYM błędu. Jeśli składnia zapytania jest poprawna, — do użytku w danych historycznych w określonej kwerendy jest wyświetlany jako wykres z opcją, aby dostosować okno czasowe z ostatnich sześciu godzin do ostatniego tygodnia.

    ![Konfigurowanie reguły alertu](media/alerts-log/AlertsPreviewAlertLog.png)

   > [!NOTE]
   > 
   > Wizualizacja danych historycznych mogą być wyświetlane tylko jeśli wyniki zapytania mają szczegóły godziny. Jeśli zapytanie powoduje podsumowane dane lub wartości określonej kolumny — sama jest wyświetlany jako pojedynczej powierzchni.
   > Pomiar metryki typu alertów dzienników za pomocą usługi Application Insights lub [przełączona do nowego interfejsu API](alerts-log-api-switch.md), można określić, które określonej zmiennej, aby pogrupować dane przy użyciu **agregowane na** opcji; zgodnie z opisami w poniżej:
   > 
   > ![Agreguj według opcji](media/alerts-log/aggregate-on.png)

1. *Alerty dzienników*: Za pomocą wizualizacji w miejscu **Alert Logic** można wybrać opcje pokazano warunku, agregacji i na koniec próg. Wskaż w logice, czas oceny pod kątem określonego warunku, przy użyciu **okres** opcji. Wraz z częstotliwość uruchamiania alertu, wybierając **częstotliwość**. **Alerty dzienników** może opierać się na:
    - [Liczba rekordów](../../azure-monitor/platform/alerts-unified-log.md#number-of-results-alert-rules): Alert jest tworzony, jeśli liczba rekordów zwróconych przez zapytanie jest większa lub mniejsza niż podana wartość.
    - [Pomiar metryki](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules): Alert jest tworzony, jeśli każdy *agregować wartości* w wynikach przekracza wartość progową, pod warunkiem, i jest *pogrupowane według* wybrana wartość. Liczba naruszeń alertu jest liczba przypadków, gdy próg został przekroczony w wybranym okresie. Łączna liczba naruszeń w jakiejkolwiek kombinacji naruszeń można określić przez zestaw wyników lub kolejne naruszenia, aby wymagać, że naruszeń musi wystąpić w kolejnych próbek.


1. W drugim kroku należy zdefiniować nazwę alertu w **Nazwa reguły alertu** pola wraz z **opis** szczegółowych informacji na temat specyfiki alertu i **ważność** z wartości Opcje przekazane. Te informacje są używane ponownie w wiadomości e-mail alertów, powiadomień lub wypychania wykonywane przez usługi Azure Monitor. Ponadto użytkownik może wybrać od razu aktywować regułę alertu przy tworzeniu odpowiednio przełączając **Włącz regułę po utworzeniu** opcji.

    Aby uzyskać **alertów dzienników** tylko kilka dodatkowych funkcji jest dostępna w szczegółach alertu:

    - **Pomiń alerty**: Po włączeniu funkcji pomijania reguła alertu akcje dla reguły są wyłączone dla określona długość czasu, po utworzeniu nowego alertu. Reguła jest nadal uruchomione i tworzy rekordy alertów, pod warunkiem spełnienia kryteriów. Umożliwiając czasu, należy rozwiązać problem bez konieczności uruchamiania zduplikowane akcji.

        ![Pomiń alerty dla dziennika alertów](media/alerts-log/AlertsPreviewSuppress.png)

        > [!TIP]
        > Określ wartość alertu Pomiń większe niż częstotliwość alertu, aby upewnić się, że powiadomienia zostały zatrzymane bez nakładania się

1. W trzeciej i ostatniej kroku określ ewentualne **grupy akcji** musi być wyzwalane dla reguły alertu, gdy zostanie spełniony warunek alertu. Można wybrać wszystkie istniejące grupy akcji z alertem lub Utwórz nową grupę akcji. Zgodnie z opisem w wybrane grupy akcji, gdy alert jest wyzwalacza Azure będzie: wysyłanie email(s), Wyślij SMS(s), wywoływać elementy webhook, korygowanie za pomocą elementów Runbook usługi Azure, wypychania narzędziem ITSM, itp. Dowiedz się więcej o [grup akcji](action-groups.md).

    > [!NOTE]
    > Zapoznaj się [limity usług subskrypcji platformy Azure](../../azure-subscription-service-limits.md) limity ładunków Runbook wyzwolone dla dziennika alertów za pośrednictwem grup akcji platformy Azure

    Aby uzyskać **alertów dzienników** kilka dodatkowych funkcji jest dostępny zastąpić domyślne akcje:

    - **Powiadomienia e-mail**: Zastępuje *temat wiadomości e-mail* w wiadomości e-mail wysyłane za pośrednictwem grupy akcji, jeśli istnieje co najmniej jednej akcji poczty e-mail, wspomnianych grupy akcji. Nie można zmodyfikować treść wiadomości e-mail i to pole jest **nie** adresu e-mail.
    - **Uwzględnij niestandardowy ładunek Json**: Zastępuje element webhook JSON używany przez grupy akcji; Jeśli istnieje co najmniej jednej akcji elementu webhook w wymienionej grupy akcji. Użytkownik może określić format JSON ma być używany dla wszystkich elementów webhook skonfigurowane w skojarzonej grupy akcji; Aby uzyskać więcej informacji dotyczących formatów elementu webhook, zobacz [Akcja elementu webhook dla dziennika alertów](../../azure-monitor/platform/alerts-log-webhook.md). Opcja Widok do elementu Webhook jest udostępniane na Sprawdź format przy użyciu przykładowych danych JSON.

        ![Akcja zastąpienia dla dziennika alertów](media/alerts-log/AlertsPreviewOverrideLog.png)


1. Jeśli wszystkie pola są prawidłowe i za pomocą znaczników zielony **Utwórz regułę alertu** kliknięcie przycisku, a alert jest tworzony w usłudze Azure Monitor — alerty. Wszystkie alerty mogą być wyświetlane z alerty pulpitu nawigacyjnego.

     ![Tworzenie reguł](media/alerts-log/AlertsPreviewCreate.png)

     W ciągu kilku minut ten alert jest aktywny i wyzwala w sposób opisany wcześniej.

Użytkownicy mogą również zakończona ich zapytania usługi analytics w [dziennika analizy](../log-query/portals.md) i Wypchnij go do utworzenia alertu, klikając przycisk "Ustaw Alert" - następnie postępując zgodnie z instrukcjami krok 6 lub nowszym w tym samouczku powyżej.

 ![Log Analytics — Ustawianie alertu](media/alerts-log/AlertsAnalyticsCreate.png)

### <a name="view--manage-log-alerts-in-azure-portal"></a>Wyświetlanie i zarządzanie alertów dzienników w witrynie Azure portal

1. W [portal](https://portal.azure.com/), wybierz opcję **Monitor** i w sekcji monitorowanie — wybierz **alerty**.

1. **Pulpit nawigacyjny alertów** jest wyświetlany - polegającego wszystkie alerty platformy Azure (w tym alertów dzienników) są wyświetlane w pojedynczej tablicy; w tym gdy reguła alertu dziennika każde wystąpienie zostało uruchomione. Aby dowiedzieć się więcej, zobacz [zarządzania alertami](https://aka.ms/managealertinstances).
    > [!NOTE]
    > Reguł alertów dzienników składają się z niestandardowej logiki oparte na zapytaniach Użytkownicy podali i dlatego bez stanie rozwiązanym. Z powodu której za każdym razem, gdy są spełnione warunki określone w reguł alertów dzienników, jej wyzwoleniu.

1. Wybierz **zarządzanie regułami** znajdujący się na górnym pasku, aby przejść do sekcji Zarządzanie reguły — gdzie znajduje się lista wszystkich reguł alertów utworzone; w tym alerty, które zostały wyłączone.
    ![ Zarządzaj regułami alertów](media/alerts-log/manage-alert-rules.png)

## <a name="managing-log-alerts-using-azure-resource-template"></a>Zarządzanie alertami dziennika przy użyciu szablonu usługi Azure Resource

Alerty dzienników w usłudze Azure Monitor są skojarzone z typem zasobu `Microsoft.Insights/scheduledQueryRules/`. Aby uzyskać więcej informacji na temat tego typu zasobu, zobacz [usługi Azure Monitor — zaplanowane reguły dokumentację interfejsu API zapytań](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/). Alerty dzienników dla usługi Application Insights ani Log Analytics, mogą być tworzone za pomocą [zaplanowane zasady interfejsu API zapytań](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/).

> [!NOTE]
> Alerty dzienników usługi Log Analytics mogą być także zarządzane przy użyciu starszej wersji [interfejsu API Log Analytics alertu](api-alerts.md) i starszej wersji szablony z [usługi Log Analytics zapisane wyszukiwania i alerty](../insights/solutions-resources-searches-alerts.md) także. Aby uzyskać więcej informacji na temat korzystania z nowego interfejsu API ScheduledQueryRules przedstawione w tym miejscu domyślnie, zobacz [przełączyć się do nowego interfejsu API dla alertów usługi Log Analytics](alerts-log-api-switch.md).


### <a name="sample-log-alert-creation-using-azure-resource-template"></a>Przykładowy dziennik tworzenia alertu za pomocą szablonu usługi Azure Resource

Poniżej przedstawiono strukturę dla [tworzenia reguł zapytań zaplanowane](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/createorupdate) na podstawie szablonu zasobów przy użyciu zapytania wyszukiwania standardowym dzienniku [liczba alertu dziennika typu wyników](alerts-unified-log.md#number-of-results-alert-rules), przy użyciu Przykładowy zestaw danych jako zmienne.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "variables": {
        "alertLocation": "southcentralus",
        "alertName": "samplelogalert",
        "alertTag": "hidden-link:/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/components/sampleAIapplication",
        "alertDescription": "Sample log search alert",
        "alertStatus": "true",
        "alertSource":{
            "Query":"requests",
            "SourceId": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/components/sampleAIapplication",
            "Type":"ResultCount"
        },
        "alertSchedule":{
            "Frequency": 15,
            "Time": 60
        },
        "alertActions":{
            "SeverityLevel": "4"
        },
        "alertTrigger":{
            "Operator":"GreaterThan",
            "Threshold":"1"
        },
        "actionGrp":{
            "ActionGroup": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/actiongroups/sampleAG",
            "Subject": "Customized Email Header",
            "Webhook": "{ \"alertname\":\"#alertrulename\", \"IncludeSearchResults\":true }"
        }
    },
    "resources":[ {
        "name":"[variables('alertName')]",
        "type":"Microsoft.Insights/scheduledQueryRules",
        "apiVersion": "2018-04-16",
        "location": "[variables('alertLocation')]",
        "tags":{"[variables('alertTag')]": "Resource"},
        "properties":{
            "description": "[variables('alertDescription')]",
            "enabled": "[variables('alertStatus')]",
            "source": {
                "query": "[variables('alertSource').Query]",
                "dataSourceId": "[variables('alertSource').SourceId]",
                "queryType":"[variables('alertSource').Type]"
            },
            "schedule":{
                "frequencyInMinutes": "[variables('alertSchedule').Frequency]",
                "timeWindowInMinutes": "[variables('alertSchedule').Time]"
            },
            "action":{
                "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.AlertingAction",
                "severity":"[variables('alertActions').SeverityLevel]",
                "aznsAction":{
                    "actionGroup":"[array(variables('actionGrp').ActionGroup)]",
                    "emailSubject":"[variables('actionGrp').Subject]",
                    "customWebhookPayload":"[variables('actionGrp').Webhook]"
                },
                "trigger":{
                    "thresholdOperator":"[variables('alertTrigger').Operator]",
                    "threshold":"[variables('alertTrigger').Threshold]"
                }
            }
        }
    } ]
}

```

> [!IMPORTANT]
> Pole znacznika ukryte w Link do zasobu docelowego jest wymagane użycie [zaplanowane reguł zapytań](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) szablonu połączenia lub zasobu interfejsu API.

Przykładowy kod json powyżej, można zapisać jako (np.) sampleScheduledQueryRule.json na potrzeby ten przewodnik i można wdrożyć przy użyciu [usługi Azure Resource Manager w witrynie Azure portal](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template).


### <a name="log-alert-with-cross-resource-query-using-azure-resource-template"></a>Alert dziennika przy użyciu zapytania obejmujące wiele zasobów przy użyciu szablonu usługi Azure Resource

Poniżej przedstawiono strukturę dla [tworzenia reguł zapytań zaplanowane](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/createorupdate) na podstawie przy użyciu szablonu zasobów [zapytanie wyszukiwania w dzienniku obejmujące wiele zasobów](../../azure-monitor/log-query/cross-workspace-query.md) z [alertu dziennika typu pomiaru metryki](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules), przy użyciu Przykładowy zestaw danych jako zmienne.

```json

{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "variables": {
        "alertLocation": "Region Name for your Application Insights App or Log Analytics Workspace",
        "alertName": "sample log alert",
        "alertDescr": "Sample log search alert",
        "alertStatus": "true",
        "alertTag": "hidden-link:/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews",
        "alertSource":{
            "Query":"union workspace(\"servicews\").Update, app('serviceapp').requests | summarize AggregatedValue = count() by bin(TimeGenerated,1h), Classification",
            "Resource1": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews",
            "Resource2": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/components/serviceapp",
            "SourceId": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews",
            "Type":"ResultCount"
        },
        "alertSchedule":{
            "Frequency": 15,
            "Time": 60
        },
        "alertActions":{
            "SeverityLevel": "4",
            "SuppressTimeinMin": 20
        },
        "alertTrigger":{
            "Operator":"GreaterThan",
            "Threshold":"1"
        },
        "metricMeasurement": {
            "thresholdOperator": "Equal",
            "threshold": "1",
            "metricTriggerType": "Consecutive",
            "metricColumn": "Classification"
        },
        "actionGrp":{
            "ActionGroup": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/actiongroups/sampleAG",
            "Subject": "Customized Email Header",
            "Webhook": "{ \"alertname\":\"#alertrulename\", \"IncludeSearchResults\":true }"
        }
    },
    "resources":[ {
        "name":"[variables('alertName')]",
        "type":"Microsoft.Insights/scheduledQueryRules",
        "apiVersion": "2018-04-16",
        "location": "[variables('alertLocation')]",
        "tags":{"[variables('alertTag')]": "Resource"},
        "properties":{
            "description": "[variables('alertDescr')]",
            "enabled": "[variables('alertStatus')]",
            "source": {
                "query": "[variables('alertSource').Query]",
                "authorizedResources": "[concat(array(variables('alertSource').Resource1), array(variables('alertSource').Resource2))]",
                "dataSourceId": "[variables('alertSource').SourceId]",
                "queryType":"[variables('alertSource').Type]"
            },
            "schedule":{
                "frequencyInMinutes": "[variables('alertSchedule').Frequency]",
                "timeWindowInMinutes": "[variables('alertSchedule').Time]"
            },
            "action":{
                "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.AlertingAction",
                "severity":"[variables('alertActions').SeverityLevel]",
                "throttlingInMin": "[variables('alertActions').SuppressTimeinMin]",
                "aznsAction":{
                    "actionGroup": "[array(variables('actionGrp').ActionGroup)]",
                    "emailSubject":"[variables('actionGrp').Subject]",
                    "customWebhookPayload":"[variables('actionGrp').Webhook]"
                },
                "trigger":{
                    "thresholdOperator":"[variables('alertTrigger').Operator]",
                    "threshold":"[variables('alertTrigger').Threshold]",
                    "metricTrigger":{
                        "thresholdOperator": "[variables('metricMeasurement').thresholdOperator]",
                        "threshold": "[variables('metricMeasurement').threshold]",
                        "metricColumn": "[variables('metricMeasurement').metricColumn]",
                        "metricTriggerType": "[variables('metricMeasurement').metricTriggerType]"
                    }
                }
            }
        }
    } ]
}

```

> [!IMPORTANT]
> Pole znacznika ukryte w Link do zasobu docelowego jest wymagane użycie [zaplanowane reguł zapytań](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) szablonu połączenia lub zasobu interfejsu API. Podczas korzystania z zapytania obejmujące wiele zasobów w dzienniku alertów, użycie [authorizedResources](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/createorupdate#source) jest obowiązkowy i użytkownik musi mieć dostęp do listy zasobów, o których wspomniano

Przykładowy kod json powyżej, można zapisać jako (np.) sampleScheduledQueryRule.json na potrzeby ten przewodnik i można wdrożyć przy użyciu [usługi Azure Resource Manager w witrynie Azure portal](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template).

## <a name="managing-log-alerts-using-powershell"></a>Zarządzanie alertami dziennika przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Usługa Azure Monitor — [interfejs API reguły zapytania zaplanowane](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) jest w pełni zgodna z usługą Azure Resource Manager REST API i interfejs API REST. I poniżej poleceń cmdlet programu PowerShell są dostępne na wykorzystanie [zaplanowane zasady interfejsu API zapytań](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/).

1. [Nowe AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) : Polecenia cmdlet programu PowerShell do tworzenia nowych reguł alertów dzienników.
1. [Zestaw AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule) : Polecenia cmdlet programu PowerShell do zaktualizowania istniejących reguł alertów dzienników.
1. [Nowe AzScheduledQueryRuleSource](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrulesource) : Polecenia cmdlet programu PowerShell do utworzenia lub zaktualizowania obiektu określania parametrów źródła alertu dziennika. Używane jako dane wejściowe przez [New AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) i [AzScheduledQueryRule zestaw](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule) polecenia cmdlet.
1. [New-AzScheduledQueryRuleSchedule](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleSchedule): Polecenia cmdlet programu PowerShell do utworzenia lub zaktualizowania obiektu Określanie parametrami harmonogramu dla alertu dziennika. Używane jako dane wejściowe przez [New AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) i [AzScheduledQueryRule zestaw](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule) polecenia cmdlet.
1. [New-AzScheduledQueryRuleAlertingAction](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleAlertingAction) : Polecenia cmdlet programu PowerShell do utworzenia lub zaktualizowania obiektu, określając parametry akcji dla alertu dziennika. Używane jako dane wejściowe przez [New AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) i [AzScheduledQueryRule zestaw](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule) polecenia cmdlet.
1. [New-AzScheduledQueryRuleAznsActionGroup](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryruleaznsactiongroup) : Polecenia cmdlet programu PowerShell do utworzenia lub zaktualizowania obiektu akcję określania grup parametry alertu dziennika. Używane jako dane wejściowe przez [New AzScheduledQueryRuleAlertingAction](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleAlertingAction) polecenia cmdlet.
1. [Nowe AzScheduledQueryRuleTriggerCondition](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition) : Polecenia cmdlet programu PowerShell do utworzenia lub zaktualizowania obiektu Określanie parametrów warunku wyzwalania alertu dziennika. Używane jako dane wejściowe przez [New AzScheduledQueryRuleAlertingAction](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleAlertingAction) polecenia cmdlet.
1. [Nowe AzScheduledQueryRuleLogMetricTrigger](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrulelogmetrictrigger) : Polecenia cmdlet programu PowerShell do utworzenia lub zaktualizowania obiektu Określanie parametrów warunku wyzwalania metryki dla [alertu dziennika typu pomiaru metryki](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules). Używane jako dane wejściowe przez [New AzScheduledQueryRuleTriggerCondition](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition) polecenia cmdlet.
1. [Get-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/get-azscheduledqueryrule) : Polecenia cmdlet programu PowerShell do istniejącej listy logowania reguł alertów lub regułę alertu w określonym dzienniku
1. [Aktualizacja AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/update-azscheduledqueryrule) : Polecenia cmdlet programu PowerShell, aby włączyć lub wyłączyć reguł alertów dzienników
1. [Remove-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/remove-azscheduledqueryrule): Polecenia cmdlet programu PowerShell, aby usunąć istniejącą regułę alertu dziennika

> [!NOTE]
> Polecenia cmdlet programu ScheduledQueryRules PowerShell można zarządzać tylko polecenia cmdlet reguł utworzonych, sama lub za pomocą usługi Azure Monitor — [zaplanowane zasady interfejsu API zapytań](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/). Zaloguj się reguły alertów utworzone przy użyciu starszej wersji [interfejsu API usługi Log Analytics alertu](api-alerts.md) i starszej wersji szablony [usługi Log Analytics zapisane wyszukiwania i alerty](../insights/solutions-resources-searches-alerts.md) mogą być zarządzane przy użyciu poleceń cmdlet programu ScheduledQueryRules PowerShell tylko po Użytkownik [zmienia preferencje interfejsu API dla alertów usługi Log Analytics](alerts-log-api-switch.md).

## <a name="managing-log-alerts-using-cli-or-api"></a>Zarządzanie alertami dziennika przy użyciu interfejsu wiersza polecenia lub interfejsu API

Usługa Azure Monitor — [interfejs API reguły zapytania zaplanowane](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) jest w pełni zgodna z usługą Azure Resource Manager REST API i interfejs API REST. Dlatego można używać za pośrednictwem programu Powershell przy użyciu poleceń usługi Resource Manager dla wiersza polecenia platformy Azure.


> [!NOTE]
> Alerty dzienników usługi Log Analytics mogą być także zarządzane przy użyciu starszej wersji [interfejsu API Log Analytics alertu](api-alerts.md) i starszej wersji szablony z [usługi Log Analytics zapisane wyszukiwania i alerty](../insights/solutions-resources-searches-alerts.md) także. Aby uzyskać więcej informacji na temat korzystania z nowego interfejsu API ScheduledQueryRules przedstawione w tym miejscu domyślnie, zobacz [przełączyć się do nowego interfejsu API dla alertów usługi Log Analytics](alerts-log-api-switch.md).

Alerty dzienników obecnie nie masz dedykowane poleceń interfejsu wiersza polecenia obecnie; ale jak przedstawiono poniżej może służyć za pomocą polecenia interfejsu wiersza polecenia usługi Azure Resource Manager w przykładzie przedstawionym wcześniej szablon zasobu (sampleScheduledQueryRule.json) w sekcji zasobów szablonu:

```azurecli
az group deployment create --resource-group contosoRG --template-file sampleScheduledQueryRule.json
```

Na powodzenie operacji 201 zostanie przywrócony do stanu nowych Tworzenie reguły alertu lub 200 zostanie zwrócona, jeśli istniejącą regułę alertu został zmodyfikowany.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [alerty dzienników w alertów platformy Azure](../../azure-monitor/platform/alerts-unified-log.md)
* Zrozumienie [Akcje elementu Webhook dla alertów dzienników](../../azure-monitor/platform/alerts-log-webhook.md)
* Dowiedz się więcej o [usługi Application Insights](../../azure-monitor/app/analytics.md)
* Dowiedz się więcej o [rejestrowania zapytań](../log-query/log-query-overview.md).
