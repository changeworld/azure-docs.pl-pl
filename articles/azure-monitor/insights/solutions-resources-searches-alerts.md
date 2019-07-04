---
title: Zapisane wyszukiwania w rozwiązaniach do zarządzania | Dokumentacja firmy Microsoft
description: Rozwiązania do zarządzania zwykle zawierają zapisane wyszukiwania w usłudze Log Analytics do analizowania danych zbieranych przez to rozwiązanie. Mogą również definiować alerty, aby powiadomić użytkownika lub automatycznie podjąć działania w odpowiedzi na problem krytyczny. W tym artykule opisano sposób definiowania zapisane wyszukiwania w szablonie usługi Resource Manager, aby mogły one zostać uwzględnione w rozwiązaniach do zarządzania w usłudze Log Analytics.
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2019
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0975b23a8f96da6fc2dfcc8bd9ad046847a68aa9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62104834"
---
# <a name="adding-log-analytics-saved-searches-and-alerts-to-management-solution-preview"></a>Dodawanie usługi Log Analytics zapisane wyszukiwania i alerty w rozwiązaniu do zarządzania (wersja zapoznawcza)

> [!IMPORTANT]
> Szczegółowe informacje w tym miejscu do tworzenia alertu przy użyciu szablonu usługi Resource Manager są poza Data teraz [alertów usługi Log Analytics zostały rozszerzone do usługi Azure Monitor](../platform/alerts-extend.md). Aby uzyskać więcej informacji na temat tworzenia alertu dziennika za pomocą szablonu usługi Resource Manager, zobacz [alertów dzienników zarządzanie przy użyciu szablonu usługi Azure Resource](../platform/alerts-log.md#managing-log-alerts-using-azure-resource-template).

> [!NOTE]
> Jest to wstępne dokumentację dotyczącą tworzenia rozwiązań do zarządzania, które są obecnie dostępne w wersji zapoznawczej. Żadnego schematu opisanych poniżej ulec zmianie.

[Rozwiązania do zarządzania](solutions.md) zwykle zawierają [zapisane wyszukiwania](../../azure-monitor/log-query/log-query-overview.md) w usłudze Log Analytics do analizowania danych zbieranych przez to rozwiązanie. Mogą również definiować [alerty](../../azure-monitor/platform/alerts-overview.md) powiadomić użytkownika lub automatycznie podjąć działania w odpowiedzi na problem krytyczny. W tym artykule opisano sposób definiowania zapisanego wyszukiwania analizy dzienników i alertów w [szablonu Resource Management](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md) , dzięki czemu mogą być zawarte w [rozwiązań do zarządzania](solutions-creating.md).

> [!NOTE]
> Przykłady w tym artykule użyć parametrów i zmiennych, które są wymagane lub wspólne dla rozwiązania do zarządzania i opisem w artykule [projektowanie i tworzenie rozwiązania do zarządzania na platformie Azure](solutions-creating.md)

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule założono, że znasz już sposób [utworzyć to rozwiązanie do zarządzania](solutions-creating.md) i struktury [szablonu usługi Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md) i plik rozwiązania.


## <a name="log-analytics-workspace"></a>Obszar roboczy usługi Log Analytics
Wszystkie zasoby w usłudze Log Analytics są zawarte w [obszaru roboczego](../../azure-monitor/platform/manage-access.md). Zgodnie z opisem w [obszar roboczy usługi Log Analytics i konto usługi Automation](solutions.md#log-analytics-workspace-and-automation-account), obszar roboczy nie jest zawarty w rozwiązaniu do zarządzania, ale musi istnieć przed zainstalowaniem rozwiązania. Jeśli nie jest dostępne, zainstaluj rozwiązania kończy się niepowodzeniem.

Nazwa obszaru roboczego jest nazwę każdego zasobu usługi Log Analytics. Jest to realizowane w rozwiązaniu za pomocą **obszaru roboczego** parametru, jak w poniższym przykładzie zasobów zapisanego wyszukiwania.

    "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearchId'))]"

## <a name="log-analytics-api-version"></a>Wersja interfejsu API analizy dziennika
Wszystkie zasoby usługi Log Analytics, zdefiniowane w szablonie usługi Resource Manager mają właściwość **apiVersion** definiujący wersję interfejsu API, należy użyć zasobu.

Poniższa tabela zawiera listę wersji interfejsu API dla zasobów używanych w tym przykładzie.

| Typ zasobu | Wersja interfejsu API | Zapytanie |
|:---|:---|:---|
| savedSearches | 2017-03-15-preview | Zdarzenie &#124; gdzie EventLevelName == "Error"  |


## <a name="saved-searches"></a>Zapisane wyszukiwania
Obejmują [zapisane wyszukiwania](../../azure-monitor/log-query/log-query-overview.md) w ramach rozwiązania, aby umożliwić użytkownikom zapytania o dane zebrane przez rozwiązania. Zapisane wyszukiwania są wyświetlane w obszarze **zapisane wyszukiwania** w witrynie Azure portal. Zapisane wyszukiwanie jest także wymagane dla każdego alertu.

[Zapisane wyszukiwanie analizy dzienników](../../azure-monitor/log-query/log-query-overview.md) zasoby mają typ `Microsoft.OperationalInsights/workspaces/savedSearches` i mają następującą strukturę. W tym wspólnych zmiennych i parametrów, aby można skopiuj i wklej następujący fragment kodu do pliku rozwiązania i Zmień nazwy parametrów.

    {
        "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name)]",
        "type": "Microsoft.OperationalInsights/workspaces/savedSearches",
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "dependsOn": [
        ],
        "tags": { },
        "properties": {
            "etag": "*",
            "query": "[variables('SavedSearch').Query]",
            "displayName": "[variables('SavedSearch').DisplayName]",
            "category": "[variables('SavedSearch').Category]"
        }
    }

Każda właściwość zapisanego kryterium wyszukiwania jest opisane w poniższej tabeli.

| Właściwość | description |
|:--- |:--- |
| category | Kategoria dla zapisanego wyszukiwania.  Wszystkie zapisane wyszukiwania, w tym samym rozwiązaniu często współużytkują jednej kategorii, dzięki czemu są one zgrupowane razem w konsoli. |
| displayName | Nazwa do wyświetlenia dla zapisanego wyszukiwania w portalu. |
| query | Zapytanie do uruchomienia. |

> [!NOTE]
> Może być konieczne użycie znaków ucieczki w zapytaniu, jeśli zawiera znaki, które może zostać zinterpretowane jako kod JSON. Na przykład, jeśli zapytanie zostało **AzureActivity | OperationName:"Microsoft.Compute/virtualMachines/write"** , powinny być zapisywane w pliku rozwiązania jako **AzureActivity | OperationName: /\"Microsoft.Compute/virtualMachines/write\"** .

## <a name="alerts"></a>Alerty
[Alerty dzienników platformy Azure](../../azure-monitor/platform/alerts-unified-log.md) są tworzone przez reguły alertów platformy Azure, które uruchamiają zapytania określonego dziennika w regularnych odstępach czasu. Jeśli wyniki zapytania pasują do określonych kryteriów, zostaje utworzony rekord alertu i co najmniej jednej akcji są uruchamiane przy użyciu [grup akcji](../../azure-monitor/platform/action-groups.md).

> [!NOTE]
> Począwszy od 14 maja 2018 r. wszystkie alerty w wystąpieniu chmury publicznej platformy Azure w obszarze roboczym usługi Log Analytics już rozszerzone na platformę Azure. Aby uzyskać więcej informacji, zobacz [rozszerzyć alerty na platformę Azure](../../azure-monitor/platform/alerts-extend.md). Dla użytkowników, którzy rozszerzanie alertów do platformy Azure akcje są obecnie kontrolowane w grup akcji platformy Azure. Gdy obszar roboczy i jego alerty zostały rozszerzone na platformę Azure, można pobrać lub dodać akcje przy użyciu [grupy akcji — szablon usługi Azure Resource Manager](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).
Reguły alertów w rozwiązaniu do zarządzania składają się z następujących trzech różnych zasobów.

- **Zapisane wyszukiwanie.** Definiuje przeszukiwania dzienników, która jest uruchamiana. Wiele reguł alertów można udostępniać pojedynczy zapisanego kryterium wyszukiwania.
- **Harmonogram.** Definiuje, jak często wykonywane jest wyszukiwanie w dzienniku. Każda reguła alertu ma jeden i tylko jeden harmonogram.
- **Akcja alertu.** Każda reguła alertu ma jeden zasób grupy akcji lub Akcja zasobu (starsze) z typem **Alert** definiujący szczegóły alertu, takie jak kryteria podczas tworzenia rekordu alertu i ważności alertu. [Grupa akcji](../../azure-monitor/platform/action-groups.md) zasób może zawierać listę skonfigurowane akcje do wykonania po wyzwoleniu alertu — takich jak połączenie głosowe i wiadomość SMS, wiadomości e-mail, element webhook, narzędziem ITSM, element runbook usługi automation, aplikacja logiki itp.

Zasób akcji (starsze) opcjonalnie definiują odpowiedzi poczty e-mail i elementów runbook.
- **Akcja elementu Webhook (starsza wersja).** Reguła alertu wywołuje element webhook, a następnie wymaga zasób dodatkowych akcji z typem **elementu Webhook**.

Zapisane wyszukiwania, które zasoby są opisane powyżej. Inne zasoby są opisane poniżej.

### <a name="schedule-resource"></a>Zasób harmonogramu

Zapisane wyszukiwanie może mieć co najmniej jeden harmonogram z każdym harmonogramie reprezentujący oddzielne reguły alertu. Harmonogram definiuje, jak często wyszukiwanie jest uruchamiania i przedział czasu, przez który dane są pobierane. Planowanie zasobów mają typ `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/` i mają następującą strukturę. W tym wspólnych zmiennych i parametrów, aby można skopiuj i wklej następujący fragment kodu do pliku rozwiązania i Zmień nazwy parametrów.

    {
        "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name, '/', variables('Schedule').Name)]",
        "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/",
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('SavedSearch').Name)]"
        ],
        "properties": {
            "etag": "*",
            "interval": "[variables('Schedule').Interval]",
            "queryTimeSpan": "[variables('Schedule').TimeSpan]",
            "enabled": "[variables('Schedule').Enabled]"
        }
    }
Właściwości harmonogramu zasoby są opisane w poniższej tabeli.

| Nazwa elementu | Wymagane | description |
|:--|:--|:--|
| enabled       | Yes | Określa, czy alert jest włączony podczas jego tworzenia. |
| interval      | Tak | Jak często zapytanie działa w ciągu kilku minut. |
| queryTimeSpan | Tak | Długość czasu w minutach, względem którego ma zostać ocena wyników. |

Zasób harmonogramu powinna zależeć od zapisane wyszukiwanie, tak, aby jest tworzony przed harmonogramu.
> [!NOTE]
> Nazwa harmonogramu musi być unikatowa w danym obszarze roboczym; dwa harmonogramy nie może mieć ten sam identyfikator, nawet jeśli są one skojarzone z inną zapisanych wyszukiwań. Również nazwa wszystkie zapisane wyszukiwania, harmonogramów i działań utworzonych za pomocą interfejsu API analizy dziennika musi być pisane małymi literami.

### <a name="actions"></a>Akcje
Harmonogram może mieć wiele akcji. Akcja może zdefiniować jeden lub więcej procesów do wykonywania takich jak wysłanie wiadomości e-mail lub uruchamianie elementu runbook lub mogą definiować, próg, która określa, kiedy wyniki wyszukiwania pasują do kryteriów. Niektóre akcje definiują zarówno tak, aby procesy są wykonywane po osiągnięciu wartości progowej.
Akcje mogą być definiowane przy użyciu [grupy akcji] zasobu lub akcji.
> [!NOTE]
> Począwszy od 14 maja 2018 r. wszystkie alerty w wystąpieniu chmury publicznej platformy Azure w obszarze roboczym usługi Log Analytics już automatycznie rozszerzone na platformę Azure. Aby uzyskać więcej informacji, zobacz [rozszerzyć alerty na platformę Azure](../../azure-monitor/platform/alerts-extend.md). Dla użytkowników, którzy rozszerzanie alertów do platformy Azure akcje są obecnie kontrolowane w grup akcji platformy Azure. Gdy obszar roboczy i jego alerty zostały rozszerzone na platformę Azure, można pobrać lub dodać akcje przy użyciu [grupy akcji — szablon usługi Azure Resource Manager](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).
Istnieją dwa typy działań zasobu określonego przez **typu** właściwości. Harmonogram wymaga jednego **Alert** akcji, która definiuje szczegóły reguły alertów i jakie akcje są wykonywane podczas tworzenia alertu. Akcja zasoby mają typ `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions`.

Akcje alertu mają następującą strukturę. W tym wspólnych zmiennych i parametrów, aby można skopiuj i wklej następujący fragment kodu do pliku rozwiązania i Zmień nazwy parametrów.

```json
{
    "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name, '/', variables('Schedule').Name, '/', variables('Alert').Name)]",
    "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions",
    "apiVersion": "[variables('LogAnalyticsApiVersion')]",
    "dependsOn": [
        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('SavedSearch').Name, '/schedules/', variables('Schedule').Name)]"
    ],
    "properties": {
        "etag": "*",
        "type": "Alert",
        "name": "[variables('Alert').Name]",
        "description": "[variables('Alert').Description]",
        "severity": "[variables('Alert').Severity]",
        "threshold": {
            "operator": "[variables('Alert').Threshold.Operator]",
            "value": "[variables('Alert').Threshold.Value]",
            "metricsTrigger": {
                "triggerCondition": "[variables('Alert').Threshold.Trigger.Condition]",
                "operator": "[variables('Alert').Trigger.Operator]",
                "value": "[variables('Alert').Trigger.Value]"
            },
        },
        "AzNsNotification": {
            "GroupIds": "[variables('MyAlert').AzNsNotification.GroupIds]",
            "CustomEmailSubject": "[variables('MyAlert').AzNsNotification.CustomEmailSubject]",
            "CustomWebhookPayload": "[variables('MyAlert').AzNsNotification.CustomWebhookPayload]"
        }
    }
}
```

Właściwości dla akcji alertu zasoby są opisane w poniższych tabelach.

| Nazwa elementu | Wymagane | description |
|:--|:--|:--|
| type | Tak | Typ akcji.  Jest to **Alert** dla akcje alertu. |
| name | Tak | Nazwa wyświetlana alertu.  Jest to nazwa, która jest wyświetlana w konsoli dla reguły alertu. |
| description | Nie | Opcjonalny opis alertu. |
| severity | Tak | Ważność alertu rekordu z następujących wartości:<br><br> **Krytyczne**<br>**Ostrzeżenie**<br>**Informacyjny**


#### <a name="threshold"></a>Próg
Ta sekcja jest wymagana. Definiuje właściwości próg alertu.

| Nazwa elementu | Wymagane | description |
|:--|:--|:--|
| Operator | Yes | Operator porównania z następujących wartości:<br><br>**gt = większa niż<br>lt = mniej niż** |
| Wartość | Yes | Wartość do porównania wyników. |

##### <a name="metricstrigger"></a>MetricsTrigger
Ta sekcja jest opcjonalna. Uwzględnij go dla alertu pomiaru metryki.

> [!NOTE]
> Alerty metryki miary są obecnie dostępne w publicznej wersji zapoznawczej.

| Nazwa elementu | Wymagane | description |
|:--|:--|:--|
| TriggerCondition | Tak | Określa, czy próg całkowitą liczbę naruszeń lub kolejne naruszenia spośród następujących wartości:<br><br>**Łączna liczba<br>kolejnych** |
| Operator | Yes | Operator porównania z następujących wartości:<br><br>**gt = większa niż<br>lt = mniej niż** |
| Wartość | Yes | Liczba przypadków, które muszą zostać spełnione kryteria do wyzwolenia alertu. |


#### <a name="throttling"></a>Ograniczanie przepływności
Ta sekcja jest opcjonalna. W tej sekcji należy uwzględnić, jeśli chcesz pominąć alertów z tej samej reguły dla niektórych ilość czasu, po utworzeniu alertu.

| Nazwa elementu | Wymagane | description |
|:--|:--|:--|
| DurationInMinutes | Tak, jeżeli ograniczania elementu uwzględnione | Liczba minut do pomijania alertów po utworzeniu jednego z tych samych reguły alertu. |

#### <a name="azure-action-group"></a>Grupa akcji platformy Azure
Wszystkie alerty na platformie Azure, użyj akcji grupy jako domyślnego mechanizmu do obsługi akcji. Grupy akcji możesz określić swoje działania raz i skojarz grupę akcji do wielu alertów — na platformie Azure. Bez konieczności wielokrotnego wielokrotnie zadeklarować te same akcje. Grupy akcji obsługują wiele akcji — w tym wiadomości e-mail, wiadomości SMS, połączenie głosowe, połączenia narzędzia ITSM, element Runbook usługi Automation, identyfikator URI elementu Webhook i innych.

Dla użytkownika, który został rozszerzony alerty na platformie Azure — harmonogram powinno zostać udostępnionych szczegółów grupy akcji przekazywane wraz z wartości progowej, aby można było utworzyć alert. Szczegóły wiadomości e-mail, adresy URL elementu Webhook, szczegóły elementu Runbook usługi Automation i innych działań, muszą być zdefiniowane w stronie grupy akcji przed tworzenia alertu; można utworzyć [grupy akcji z usługi Azure Monitor](../../azure-monitor/platform/action-groups.md) w portalu lub użyj [grupy akcji — szablon zasobu](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

| Nazwa elementu | Wymagane | description |
|:--|:--|:--|
| AzNsNotification | Tak | Identyfikator zasobu do grupy akcji platformy Azure do skojarzenia z alertem pobierania odpowiednie działania, po spełnieniu kryteriów alertu. |
| CustomEmailSubject | Nie | Wiersz tematu niestandardowego wiadomości e-mail wysłanych na wszystkie adresy określone w grupie skojarzone z akcją. |
| CustomWebhookPayload | Nie | Ładunek dostosowane do wysłania do wszystkich elementów webhook punktów końcowych zdefiniowanych w grupie skojarzone z akcją. Format zależy od tego, co element webhook oczekuje i powinna być prawidłowym kodem JSON serializacji. |

#### <a name="actions-for-oms-legacy"></a>Akcje w przypadku pakietu OMS (starsza wersja)

Każdy harmonogram ma jeden **alertu** akcji. Definiuje szczegóły alertu, i opcjonalnie akcji powiadomienia i korekty. Powiadomienie wysyła wiadomość e-mail do jednego lub więcej adresów. Korygowanie uruchamia element runbook w usłudze Azure Automation, aby podjąć próbę rozwiązania wykrytego problemu.

> [!NOTE]
> Począwszy od 14 maja 2018 r. wszystkie alerty w wystąpieniu chmury publicznej platformy Azure w obszarze roboczym usługi Log Analytics już automatycznie rozszerzone na platformę Azure. Aby uzyskać więcej informacji, zobacz [rozszerzyć alerty na platformę Azure](../../azure-monitor/platform/alerts-extend.md). Dla użytkowników, którzy rozszerzanie alertów do platformy Azure akcje są obecnie kontrolowane w grup akcji platformy Azure. Gdy obszar roboczy i jego alerty zostały rozszerzone na platformę Azure, można pobrać lub dodać akcje przy użyciu [grupy akcji — szablon usługi Azure Resource Manager](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

##### <a name="emailnotification"></a>EmailNotification
 Ta sekcja jest opcjonalna dołączyć ją, jeśli chcesz, aby alert ma wysyłać wiadomości e-mail do co najmniej jednego adresata.

| Nazwa elementu | Wymagane | description |
|:--|:--|:--|
| Recipients | Yes | Rozdzielana przecinkami lista adresów e-mail, aby wysłać powiadomienia, gdy jest tworzony alert, takie jak w poniższym przykładzie.<br><br>**["recipient1\@contoso.com", "recipient2\@contoso.com"]** |
| Subject | Yes | Wiersz tematu wiadomości e-mail. |
| Attachment | Nie | Załączniki nie są obecnie obsługiwane. Jeśli ten element jest włączone, należy go **Brak**. |

##### <a name="remediation"></a>Korygowanie
Ta sekcja jest opcjonalna dołączyć ją, jeśli chcesz, aby element runbook można uruchomić w odpowiedzi na alert. 

| Nazwa elementu | Wymagane | description |
|:--|:--|:--|
| RunbookName | Tak | Nazwa elementu runbook, aby rozpocząć. |
| WebhookUri | Tak | Identyfikator URI elementu webhook dla elementu runbook. |
| Expiry | Nie | Data i godzina wygaśnięcia korygowania. |

##### <a name="webhook-actions"></a>Akcje elementu Webhook

Akcje elementu Webhook Uruchom proces, przez wywołanie adresu URL i opcjonalnie podania ładunku do wysłania. Są one podobne do akcji korygowania, z wyjątkiem są przeznaczone dla elementów webhook, które może wywoływać procesy inne niż elementów runbook usługi Azure Automation. Zapewniają także dodatkowe opcji związanych z udostępnianiem ładunek został dostarczony do zdalnego procesu.

Jeśli alert będzie wywołanie elementu webhook, a następnie będzie musiała zasób akcji z typem **elementu Webhook** oprócz **Alert** zasób akcji.

    {
      "name": "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name, '/', variables('Schedule').Name, '/', variables('Webhook').Name)]",
      "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions/",
      "apiVersion": "[variables('LogAnalyticsApiVersion')]",
      "dependsOn": [
        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('SavedSearch').Name, '/schedules/', variables('Schedule').Name)]"
      ],
      "properties": {
        "etag": "*",
        "type": "[variables('Alert').Webhook.Type]",
        "name": "[variables('Alert').Webhook.Name]",
        "webhookUri": "[variables('Alert').Webhook.webhookUri]",
        "customPayload": "[variables('Alert').Webhook.CustomPayLoad]"
      }
    }
W poniższych tabelach opisano właściwości zasobów akcji elementu Webhook.

| Nazwa elementu | Wymagane | description |
|:--|:--|:--|
| type | Tak | Typ akcji. Jest to **elementu Webhook** przypadku akcji elementów webhook. |
| name | Tak | Nazwa wyświetlana dla akcji. To nie jest wyświetlana w konsoli. |
| webhookUri | Tak | Identyfikator URI dla elementu webhook. |
| customPayload | Nie | Niestandardowy ładunek do wysłania do elementu webhook. Format zależy od tego, czego oczekuje elementu webhook. |

## <a name="sample"></a>Sample

Poniżej przedstawiono przykładowe rozwiązania, które zawiera następujące zasoby:

- Zapisane wyszukiwanie
- Harmonogram
- grupy akcji

W przykładzie użyto [parametry standardowe rozwiązanie]( solutions-solution-file.md#parameters) zmiennych, które często będzie używana w ramach rozwiązania, w przeciwieństwie do wartości hardcoding w definicji zasobu.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "Description": "Name of Log Analytics workspace"
            }
        },
        "workspaceregionId": {
            "type": "string",
            "metadata": {
                "Description": "Region of Log Analytics workspace"
            }
        },
        "actiongroup": {
            "type": "string",
            "metadata": {
                "Description": "List of action groups for alert actions separated by semicolon"
            }
        }
    },
    "variables": {
        "SolutionName": "MySolution",
        "SolutionVersion": "1.0",
        "SolutionPublisher": "Contoso",
        "ProductName": "SampleSolution",
        "LogAnalyticsApiVersion-Search": "2017-03-15-preview",
        "LogAnalyticsApiVersion-Solution": "2015-11-01-preview",
        "MySearch": {
            "displayName": "Error records by hour",
            "query": "MyRecord_CL | summarize AggregatedValue = avg(Rating_d) by Instance_s, bin(TimeGenerated, 60m)",
            "category": "Samples",
            "name": "Samples-Count of data"
        },
        "MyAlert": {
            "Name": "[toLower(concat('myalert-',uniqueString(resourceGroup().id, deployment().name)))]",
            "DisplayName": "My alert rule",
            "Description": "Sample alert. Fires when 3 error records found over hour interval.",
            "Severity": "critical",
            "ThresholdOperator": "gt",
            "ThresholdValue": 3,
            "Schedule": {
                "Name": "[toLower(concat('myschedule-',uniqueString(resourceGroup().id, deployment().name)))]",
                "Interval": 15,
                "TimeSpan": 60
            },
            "MetricsTrigger": {
                "TriggerCondition": "Consecutive",
                "Operator": "gt",
                "Value": 3
            },
            "ThrottleMinutes": 60,
            "AzNsNotification": {
                "GroupIds": [
                    "[parameters('actiongroup')]"
                ],
                "CustomEmailSubject": "Sample alert"
            }
        }
    },
    "resources": [
        {
            "name": "[concat(variables('SolutionName'), '[' ,parameters('workspacename'), ']')]",
            "location": "[parameters('workspaceRegionId')]",
            "tags": { },
            "type": "Microsoft.OperationsManagement/solutions",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Solution')]",
            "dependsOn": [
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches', parameters('workspacename'), variables('MySearch').Name)]",
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name)]",
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Name)]"
            ],
            "properties": {
                "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspacename'))]",
                "referencedResources": [
                ],
                "containedResources": [
                    "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches', parameters('workspacename'), variables('MySearch').Name)]",
                    "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name)]",
                    "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Name)]"
                ]
            },
            "plan": {
                "name": "[concat(variables('SolutionName'), '[' ,parameters('workspaceName'), ']')]",
                "Version": "[variables('SolutionVersion')]",
                "product": "[variables('ProductName')]",
                "publisher": "[variables('SolutionPublisher')]",
                "promotionCode": ""
            }
        },
        {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Search')]",
            "dependsOn": [ ],
            "tags": { },
            "properties": {
                "etag": "*",
                "query": "[variables('MySearch').query]",
                "displayName": "[variables('MySearch').displayName]",
                "category": "[variables('MySearch').category]"
            }
        },
        {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name, '/', variables('MyAlert').Schedule.Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Search')]",
            "dependsOn": [
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('MySearch').Name)]"
            ],
            "properties": {
                "etag": "*",
                "interval": "[variables('MyAlert').Schedule.Interval]",
                "queryTimeSpan": "[variables('MyAlert').Schedule.TimeSpan]",
                "enabled": true
            }
        },
        {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name, '/', variables('MyAlert').Schedule.Name, '/', variables('MyAlert').Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Search')]",
            "dependsOn": [
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('MySearch').Name, '/schedules/', variables('MyAlert').Schedule.Name)]"
            ],
            "properties": {
                "etag": "*",
                "Type": "Alert",
                "Name": "[variables('MyAlert').DisplayName]",
                "Description": "[variables('MyAlert').Description]",
                "Severity": "[variables('MyAlert').Severity]",
                "Threshold": {
                    "Operator": "[variables('MyAlert').ThresholdOperator]",
                    "Value": "[variables('MyAlert').ThresholdValue]",
                    "MetricsTrigger": {
                        "TriggerCondition": "[variables('MyAlert').MetricsTrigger.TriggerCondition]",
                        "Operator": "[variables('MyAlert').MetricsTrigger.Operator]",
                        "Value": "[variables('MyAlert').MetricsTrigger.Value]"
                    }
                },
                "Throttling": {
                    "DurationInMinutes": "[variables('MyAlert').ThrottleMinutes]"
                },
                "AzNsNotification": {
                    "GroupIds": "[variables('MyAlert').AzNsNotification.GroupIds]",
                    "CustomEmailSubject": "[variables('MyAlert').AzNsNotification.CustomEmailSubject]"
                }
            }
        }
    ]
}
```

Następujący plik parametrów zawiera przykłady wartości dla tego rozwiązania.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspacename": {
            "value": "myWorkspace"
        },
        "accountName": {
            "value": "myAccount"
        },
        "workspaceregionId": {
            "value": "East US"
        },
        "regionId": {
            "value": "East US 2"
        },
        "pricingTier": {
            "value": "Free"
        },
        "actiongroup": {
            "value": "/subscriptions/3b540246-808d-4331-99aa-917b808a9166/resourcegroups/myTestGroup/providers/microsoft.insights/actiongroups/sample"
        }
    }
}
```

## <a name="next-steps"></a>Kolejne kroki
* [Dodawanie widoków](solutions-resources-views.md) do rozwiązania do zarządzania.
* [Dodawanie elementów runbook usługi Automation i innych zasobów](solutions-resources-automation.md) do rozwiązania do zarządzania.
