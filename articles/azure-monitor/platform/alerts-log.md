---
title: Tworzenie, wyświetlanie i zarządzanie alertami dziennika za pomocą usługi Azure Monitor | Dokumenty firmy Microsoft
description: Użyj usługi Azure Monitor do tworzenia, wyświetlania i zarządzania regułami alertów dziennika na platformie Azure.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 07/29/2019
ms.subservice: alerts
ms.openlocfilehash: 96b1bd86576f8cf34428eb60e2d3f476312311c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249427"
---
# <a name="create-view-and-manage-log-alerts-using-azure-monitor"></a>Tworzenie, wyświetlanie i zarządzanie alertami dziennika przy użyciu usługi Azure Monitor

## <a name="overview"></a>Omówienie
W tym artykule pokazano, jak skonfigurować alerty dziennika przy użyciu interfejsu alertów w witrynie Azure Portal. Definicja reguły alertu składa się z trzech części:
- Cel: Określony zasób platformy Azure, który ma być monitorowany
- Kryteria: Określony warunek lub logika, które są widoczne w signal, powinny wywołać działanie
- Działanie: Konkretne wezwanie wysłane do odbiorcy powiadomienia - e-mail, SMS, webhook itp.

Termin **Alerty dziennika opisujące** alerty, w których sygnał jest kwerendą dziennika w [obszarze roboczym usługi Log Analytics](../learn/tutorial-viewdata.md) lub [usłudze Application Insights.](../app/analytics.md) Dowiedz się więcej o funkcjach, terminologii i typach z [alertów dziennika — omówienie](alerts-unified-log.md).

> [!NOTE]
> Popularne dane dziennika z [obszaru roboczego usługi Log Analytics](../../azure-monitor/learn/tutorial-viewdata.md) są teraz również dostępne na platformie metrycznej w usłudze Azure Monitor. Aby uzyskać widok szczegółów, [alert metryki dla dzienników](alerts-metric-logs.md)

## <a name="managing-log-alerts-from-the-azure-portal"></a>Zarządzanie alertami dziennika z witryny Azure portal

Szczegółowe informacje na temat następnego jest przewodnik krok po kroku do korzystania z alertów dziennika przy użyciu interfejsu portalu Azure.

### <a name="create-a-log-alert-rule-with-the-azure-portal"></a>Tworzenie reguły alertów dziennika za pomocą portalu Azure

1. W [portalu](https://portal.azure.com/)wybierz **pozycję Monitor** i w sekcji MONITOR WYBIERZ pozycję **Alerty**.

    ![Monitorowanie](media/alerts-log/AlertsPreviewMenu.png)

1. Wybierz przycisk **Nowa reguła alertu,** aby utworzyć nowy alert na platformie Azure.

    ![Dodaj alert](media/alerts-log/AlertsPreviewOption.png)

1. Sekcja Utwórz alert jest wyświetlana z trzema częściami składającymi się z: *Zdefiniuj warunek* *alertu, Zdefiniuj szczegóły alertu*i *Zdefiniuj grupę akcji*.

    ![Tworzenie reguły](media/alerts-log/AlertsPreviewAdd.png)

1. Zdefiniuj warunek alertu przy użyciu łącza **Wybierz zasób** i określając obiekt docelowy, wybierając zasób. Filtruj, wybierając _subskrypcję,_ _typ zasobu_i wymagany _zasób_.

   > [!NOTE]
   > Do tworzenia alertu dziennika - sprawdź, czy sygnał **dziennika** jest dostępny dla wybranego zasobu przed kontynuowaniem.
   >  ![Wybierz zasób](media/alerts-log/Alert-SelectResourceLog.png)

1. *Alerty dziennika:* Upewnij się, że **typ zasobu** jest źródłem analizy, takim jak *usługa Log Analytics* lub Usługa Application *Insights* i typ sygnału jako **dziennik,** a następnie po wybraniu odpowiedniego **zasobu** kliknij przycisk *Gotowe*. Następnie użyj przycisku **Dodaj kryteria,** aby wyświetlić listę opcji sygnału dostępnych dla zasobu i z listy sygnałów **Opcja niestandardowego wyszukiwania dziennika** dla wybranej usługi monitora dziennika, takiej jak Usługa Log *Analytics* lub *Application Insights*.

   ![Wybieranie zasobu — niestandardowe wyszukiwanie w dzienniku](media/alerts-log/AlertsPreviewResourceSelectionLog.png)

   > [!NOTE]
   > 
   > Listy alertów mogą importować kwerendę analityczną jako typ sygnału - **Dziennik (Zapisane zapytanie),** jak pokazano na powyższej ilustracji. Dzięki temu użytkownicy mogą udoskonalić zapytanie w Analytics, a następnie zapisać je do wykorzystania w przyszłości w alertach — więcej szczegółów na temat używania zapytania zapisującego dostępnego [przy użyciu zapytania dziennika w usłudze Azure Monitor](../log-query/log-query-overview.md) lub zapytania [udostępnionego w analizie aplikacji.](../app/app-insights-overview.md)

1. *Alerty dziennika:* Po wybraniu kwerendy do alertów można podać w polu **Kwerenda wyszukiwania;** jeśli składnia kwerendy jest niepoprawna, pole wyświetla błąd w red. Jeśli składnia kwerendy jest poprawna — dla odwołania dane historyczne podanej kwerendy jest wyświetlany jako wykres z opcją, aby dostosować przedział czasu z ostatnich sześciu godzin do ostatniego tygodnia.

    ![Konfigurowanie reguły alertu](media/alerts-log/AlertsPreviewAlertLog.png)

   > [!NOTE]
   > 
   > Wizualizacja danych historycznych może być wyświetlana tylko wtedy, gdy wyniki kwerendy mają szczegóły czasu. Jeśli zapytanie powoduje podsumowanie danych lub określonych wartości kolumn - to samo jest wyświetlane jako wykres pojedynczej.
   > W przypadku typu alertów dziennika pomiaru metryki przy użyciu usługi Application Insights lub [przełączonych do nowego interfejsu API](alerts-log-api-switch.md)można określić, która zmienna należy grupować dane przy użyciu opcji **Agreguj na;** jak pokazano poniżej:
   > 
   > ![agregacja na opcji](media/alerts-log/aggregate-on.png)

1. *Alerty dziennika:* Z wizualizacji w miejscu, **logika alertu** można wybrać z pokazanych opcji Warunek, Agregacja i wreszcie próg. Na koniec określ w logice czas, aby ocenić dla określonego warunku, za pomocą opcji **Okres.** Wraz z tym, jak często alert powinien być uruchamiany, wybierając **opcję Częstotliwość**. **Alerty dziennika** mogą być oparte na:
    - [Liczba rekordów:](../../azure-monitor/platform/alerts-unified-log.md#number-of-results-alert-rules)Alert jest tworzony, jeśli liczba rekordów zwróconych przez kwerendę jest większa lub mniejsza niż podana wartość.
    - [Pomiar metryki:](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules)Alert jest tworzony, jeśli każda *wartość zagregowana* w wynikach przekracza podana wartość progowa i jest *pogrupowana według* wybranej wartości. Liczba naruszeń dla alertu jest liczba razy próg jest przekroczony w wybranym okresie czasu. Można określić całkowite naruszenia dla dowolnej kombinacji naruszeń w zestawie wyników lub kolejnych naruszeń, aby wymagać, aby naruszenia te wystąpiły w kolejnych próbkach.


1. W drugim kroku zdefiniuj nazwę alertu w polu **Nazwa reguły alertu** wraz z **opisem** zawierającym szczegółowe informacje dotyczące wartości alertu i **ważności** z podanych opcji. Te szczegóły są ponownie używane we wszystkich wiadomościach e-mail alertów, powiadomieniach lub wypychanych wykonywanych przez usługę Azure Monitor. Ponadto użytkownik może natychmiast aktywować regułę alertu podczas tworzenia, odpowiednio **przełączanie reguły włączania przy tworzeniu.**

    Tylko w przypadku **alertów dziennika** niektóre dodatkowe funkcje są dostępne w szczegółach alertu:

    - **Pomiń alerty:** Po włączeniu pomijania reguły alertu akcje dla reguły są wyłączone przez określony czas po utworzeniu nowego alertu. Reguła jest nadal uruchomiona i tworzy rekordy alertów, pod warunkiem spełnienia kryteriów. Czas na rozwiązanie problemu bez uruchamiania zduplikowanych akcji.

        ![Pomijanie alertów dla alertów dziennika](media/alerts-log/AlertsPreviewSuppress.png)

        > [!TIP]
        > Określ wartość alertu tłumiącą większą niż częstotliwość alertu, aby upewnić się, że powiadomienia są zatrzymywane bez nakładania się

1. Jako trzeci i ostatni krok określ, czy dowolna **grupa akcji** musi zostać wyzwolona dla reguły alertu po spełnieniu warunku alertu. Możesz wybrać dowolną istniejącą grupę akcji z alertem lub utworzyć nową grupę akcji. Zgodnie z wybranym action group, gdy alert jest wyzwalany Platforma Azure będzie: wysyłanie wiadomości e-mail, wysyłać wiadomości SMS,call(s), wywołać Webhook(s), korygować przy użyciu elementów runbook platformy Azure, wypychania do narzędzia ITSM, itp. Dowiedz się więcej o [grupach akcji](action-groups.md).

    > [!NOTE]
    > Aby uzyskać limity dotyczące ładunków w ramach rejestru za pośrednictwem grup akcji platformy Azure, zapoznaj się z limitami dotyczącymi subskrypcji [platformy Azure.](../../azure-resource-manager/management/azure-subscription-service-limits.md)

    W przypadku **alertów dziennika** dostępne są dodatkowe funkcje zastępowania akcji domyślnych:

    - **Powiadomienie e-mail:** Zastępuje *temat wiadomości e-mail* w wiadomości e-mail, wysyłane za pośrednictwem grupy działania; jeśli w tej grupie akcji istnieje co najmniej jedna lub więcej akcji e-mail. Nie można zmodyfikować treści wiadomości e-mail, a to pole **nie** dotyczy adresu e-mail.
    - **Dołącz niestandardowy ładunek Json:** Zastępuje element JSON elementu webhook używanego przez grupy akcji; jeśli w tej grupie działania istnieje co najmniej jedna lub więcej akcji elementu webhook. Użytkownik może określić format JSON, który ma być używany dla wszystkich elementów webhook skonfigurowanych w skojarzonej grupie akcji; Aby uzyskać więcej informacji na temat formatów łączy webhook, zobacz [działanie elementu webhook dla alertów dziennika](../../azure-monitor/platform/alerts-log-webhook.md). Opcja Wyświetl element Webhook służy do sprawdzania formatu przy użyciu przykładowych danych JSON.

        ![Zastępowania akcji dla alertów dziennika](media/alerts-log/AlertsPreviewOverrideLog.png)


1. Jeśli wszystkie pola są prawidłowe i z zielonym zaznaczyć przycisk **reguły alertu** można kliknąć i alert jest tworzony w usłudze Azure Monitor — alerty. Wszystkie alerty można wyświetlać na pulpicie nawigacyjnym alertów.

     ![Tworzenie reguł](media/alerts-log/AlertsPreviewCreate.png)

     W ciągu kilku minut alert jest aktywny i wyzwala, jak opisano wcześniej.

Użytkownicy mogą również sfinalizować swoje zapytanie analityczne w [analizie dziennika,](../log-query/portals.md) a następnie nacisnąć go, aby utworzyć alert za pomocą przycisku "Ustaw alert" — następnie postępując zgodnie z instrukcjami od kroku 6 w powyższym samouczku.

 ![Analiza dzienników — ustawianie alertu](media/alerts-log/AlertsAnalyticsCreate.png)

### <a name="view--manage-log-alerts-in-azure-portal"></a>Wyświetlanie & zarządzanie alertami dziennika w witrynie Azure Portal

1. W [portalu](https://portal.azure.com/)wybierz **pozycję Monitor** i w sekcji MONITOR WYBIERZ pozycję **Alerty**.

1. Zostanie wyświetlony **pulpit nawigacyjny alertów** — w którym wszystkie alerty platformy Azure (w tym alerty dziennika) są wyświetlane na tablicy pojedynczej; w tym każde wystąpienie, gdy reguła alertu dziennika została zwolniona. Aby dowiedzieć się więcej, zobacz [Zarządzanie alertami](https://aka.ms/managealertinstances).
    > [!NOTE]
    > Reguły alertów dziennika obejmują niestandardową logikę opartą na zapytaniach dostarczoną przez użytkowników, a zatem bez stanu rozwiązanego. Dzięki temu za każdym razem, gdy spełnione są warunki określone w regule alertu dziennika, jest on uruchamiany.

1. Wybierz przycisk **Zarządzaj regułami** na górnym pasku, aby przejść do sekcji zarządzanie regułami — gdzie są wyświetlane wszystkie utworzone reguły alertów; w tym alerty, które zostały wyłączone.
    ![zarządzanie regułami alertów](media/alerts-log/manage-alert-rules.png)

## <a name="managing-log-alerts-using-azure-resource-template"></a>Zarządzanie alertami dziennika przy użyciu szablonu zasobów platformy Azure

Alerty dziennika w usłudze Azure `Microsoft.Insights/scheduledQueryRules/`Monitor są skojarzone z typem zasobu . Aby uzyskać więcej informacji na temat tego typu zasobu, zobacz [Azure Monitor — odwołanie do interfejsu API reguły kwerendy scheduled](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/). Alerty dziennika dla usługi Application Insights lub Log Analytics można tworzyć za pomocą [interfejsu API zaplanowane reguły kwerendy](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/).

> [!NOTE]
> Alerty dziennika dla usługi Log Analytics można również zarządzać przy użyciu starszego [interfejsu API alertów usługi Log Analytics](api-alerts.md) i starszych szablonów usługi Log Analytics [zapisanych wyszukiwań i alertów,](../insights/solutions-resources-searches-alerts.md) jak również. Aby uzyskać więcej informacji na temat korzystania z nowego interfejsu API ScheduledQueryRules opisanego tutaj domyślnie, zobacz [Przełączanie do nowego interfejsu API dla alertów usługi Log Analytics](alerts-log-api-switch.md).


### <a name="sample-log-alert-creation-using-azure-resource-template"></a>Tworzenie alertów dziennika przykładowego przy użyciu szablonu zasobu platformy Azure

Poniżej przedstawiono strukturę szablonu zasobów opartego na [tworzeniu reguł zestawowanych zapytań](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/createorupdate) przy użyciu standardowej kwerendy wyszukiwania dziennika [o liczbie alertów dziennika typu wyników,](alerts-unified-log.md#number-of-results-alert-rules)z przykładowymi danymi ustawionymi jako zmiennymi.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "variables": {
        "alertLocation": "southcentralus",
        "alertName": "samplelogalert",
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

Przykładowy json powyżej można zapisać jako (powiedzmy) sampleScheduledQueryRule.json na potrzeby tego spaceru i można go wdrożyć za pomocą [usługi Azure Resource Manager w witrynie Azure portal.](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)


### <a name="log-alert-with-cross-resource-query-using-azure-resource-template"></a>Alert dziennika z kwerendą między zasobami przy użyciu szablonu zasobu platformy Azure

Poniżej przedstawiono strukturę szablonu zasobów opartego na [tworzeniu reguł zestawowanych zapytań](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/createorupdate) przy użyciu [kwerendy wyszukiwania dziennika między zasobami alertu](../../azure-monitor/log-query/cross-workspace-query.md) [dziennika typu pomiaru metryki](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules)z przykładowym zestawem danych jako zmiennych.

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
> W przypadku korzystania z kwerendy międzysóbowej w alertie dziennika użycie [authorizedResources](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/createorupdate#source) jest obowiązkowe, a użytkownik musi mieć dostęp do listy podanych zasobów

Przykładowy json powyżej można zapisać jako (powiedzmy) sampleScheduledQueryRule.json na potrzeby tego spaceru i można go wdrożyć za pomocą [usługi Azure Resource Manager w witrynie Azure portal.](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)

## <a name="managing-log-alerts-using-powershell"></a>Zarządzanie alertami dziennika przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Interfejs [API reguł zestawionych zapytań](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) usługi Azure Monitor — jest interfejsem API REST firmy REST firmy Azure Monitor. A poniżej wymienione polecenia cmdlet programu PowerShell mogą korzystać z [interfejsu API zaplanowanych reguł zapytań.](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/)

1. [New-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) : Polecenie cmdlet programu Powershell w celu utworzenia nowej reguły alertu dziennika.
1. [Set-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule) : Polecenie cmdlet programu Powershell w celu zaktualizowania istniejącej reguły alertu dziennika.
1. [New-AzScheduledQueryRuleSource](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrulesource) : Polecenie cmdlet programu Powershell do tworzenia lub aktualizowania obiektu określającego parametry źródła dla alertu dziennika. Używane jako dane wejściowe przez polecenie cmdlet [New-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) i [Set-AzScheduledQueryRule.](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule)
1. [New-AzScheduledQueryRuleSchedule](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleSchedule): Polecenie cmdlet programu Powershell do tworzenia lub aktualizowania obiektu określającego parametry harmonogramu alertu dziennika. Używane jako dane wejściowe przez polecenie cmdlet [New-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) i [Set-AzScheduledQueryRule.](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule)
1. [New-AzScheduledQueryRuleAlertingAction:](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleAlertingAction) Polecenie cmdlet programu Powershell do tworzenia lub aktualizowania obiektu określającego parametry akcji dla alertu dziennika. Używane jako dane wejściowe przez polecenie cmdlet [New-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) i [Set-AzScheduledQueryRule.](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule)
1. [New-AzScheduledQueryRuleAznsActionGroup](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryruleaznsactiongroup) : Polecenie cmdlet programu Powershell do tworzenia lub aktualizowania obiektu określającego parametry grup akcji dla alertu dziennika. Używane jako dane wejściowe przez polecenie cmdlet [New-AzScheduledQueryRuleAlertingAction.](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleAlertingAction)
1. [New-AzScheduledQueryRuleTriggerCondition](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition) : Polecenie cmdlet programu Powershell do tworzenia lub aktualizowania obiektu określającego parametry warunku wyzwalacza dla alertu dziennika. Używane jako dane wejściowe przez polecenie cmdlet [New-AzScheduledQueryRuleAlertingAction.](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleAlertingAction)
1. [New-AzScheduledQueryRuleLogMetricTrigger](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrulelogmetrictrigger) : Polecenie cmdlet programu Powershell do tworzenia lub aktualizowania obiektu określającego parametry wyzwalacza metryki dla [alertu dziennika typu pomiaru metryki](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules). Używane jako dane wejściowe przez polecenie cmdlet [New-AzScheduledQueryRuleTriggerCondition.](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition)
1. [Get-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/get-azscheduledqueryrule) : Polecenie cmdlet programu Powershell do listy istniejących reguł alertu dziennika lub określonej reguły alertu dziennika
1. [Update-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/update-azscheduledqueryrule) : Polecenie cmdlet programu Powershell w celu włączenia lub wyłączenia reguły alertu dziennika
1. [Usuń-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/remove-azscheduledqueryrule): Polecenie cmdlet programu Powershell w celu usunięcia istniejącej reguły alertu dziennika

> [!NOTE]
> Polecenia cmdlet programu PowerShell scheduledQueryRules mogą zarządzać tylko regułami utworzonymi przez polecenie cmdlet lub przy użyciu [interfejsu API reguł zaplanowanych zapytań](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/)usługi Azure Monitor . Reguły alertów dziennika utworzone przy użyciu starszego [interfejsu API alertów usługi Log Analytics](api-alerts.md) i starszych szablonów [alertów rejestrowanych w usłudze Log Analytics](../insights/solutions-resources-searches-alerts.md) można zarządzać za pomocą poleceń cmdlet ScheduledQueryRules PowerShell tylko po [przełączeniu preferencji interfejsu API użytkownika dla alertów usługi Log Analytics](alerts-log-api-switch.md).

Następnie pokazano kroki tworzenia reguły alertu dziennika przykładowego przy użyciu poleceń cmdlet programu PowerShell scheduledQueryRules.
```powershell
$source = New-AzScheduledQueryRuleSource -Query 'Heartbeat | summarize AggregatedValue = count() by bin(TimeGenerated, 5m), _ResourceId' -DataSourceId "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews"

$schedule = New-AzScheduledQueryRuleSchedule -FrequencyInMinutes 15 -TimeWindowInMinutes 30

$metricTrigger = New-AzScheduledQueryRuleLogMetricTrigger -ThresholdOperator "GreaterThan" -Threshold 2 -MetricTriggerType "Consecutive" -MetricColumn "_ResourceId"

$triggerCondition = New-AzScheduledQueryRuleTriggerCondition -ThresholdOperator "LessThan" -Threshold 5 -MetricTrigger $metricTrigger

$aznsActionGroup = New-AzScheduledQueryRuleAznsActionGroup -ActionGroup "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/actiongroups/sampleAG" -EmailSubject "Custom email subject" -CustomWebhookPayload "{ `"alert`":`"#alertrulename`", `"IncludeSearchResults`":true }"

$alertingAction = New-AzScheduledQueryRuleAlertingAction -AznsAction $aznsActionGroup -Severity "3" -Trigger $triggerCondition

New-AzScheduledQueryRule -ResourceGroupName "contosoRG" -Location "Region Name for your Application Insights App or Log Analytics Workspace" -Action $alertingAction -Enabled $true -Description "Alert description" -Schedule $schedule -Source $source -Name "Alert Name"
```

## <a name="managing-log-alerts-using-cli-or-api"></a>Zarządzanie alertami dziennika przy użyciu interfejsu wiersza polecenia lub interfejsu API

Interfejs [API reguł zestawionych zapytań](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) usługi Azure Monitor — jest interfejsem API REST firmy REST firmy Azure Monitor. W związku z tym może służyć za pośrednictwem programu Powershell przy użyciu poleceń Resource Manager dla interfejsu wiersza polecenia platformy Azure.


> [!NOTE]
> Alerty dziennika dla usługi Log Analytics można również zarządzać przy użyciu starszego [interfejsu API alertów usługi Log Analytics](api-alerts.md) i starszych szablonów usługi Log Analytics [zapisanych wyszukiwań i alertów,](../insights/solutions-resources-searches-alerts.md) jak również. Aby uzyskać więcej informacji na temat korzystania z nowego interfejsu API ScheduledQueryRules opisanego tutaj domyślnie, zobacz [Przełączanie do nowego interfejsu API dla alertów usługi Log Analytics](alerts-log-api-switch.md).

Alerty dziennika obecnie nie mają dedykowanych poleceń interfejsu wiersza polecenia obecnie; ale jak pokazano poniżej, można użyć za pomocą polecenia interfejsu wiersza polecenia usługi Azure Resource Manager dla przykładowego szablonu zasobu pokazanego wcześniej (sampleScheduledQueryRule.json) w sekcji Szablon zasobu:

```azurecli
az group deployment create --resource-group contosoRG --template-file sampleScheduledQueryRule.json
```

Po pomyślnej operacji 201 zostaną zwrócone do stanu nowego tworzenia reguły alertu lub 200 zostaną zwrócone, jeśli istniejąca reguła alertu została zmodyfikowana.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [alertach dziennika w alertach platformy Azure](../../azure-monitor/platform/alerts-unified-log.md)
* Opis [akcji elementu Webhook dla alertów dziennika](../../azure-monitor/platform/alerts-log-webhook.md)
* Dowiedz się więcej o [usłudze Application Insights](../../azure-monitor/app/analytics.md)
* Dowiedz się więcej o [zapytaniach dziennika](../log-query/log-query-overview.md).
