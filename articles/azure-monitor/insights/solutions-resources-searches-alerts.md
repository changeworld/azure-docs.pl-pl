---
title: Zapisane wyszukiwania w rozwiązaniach do zarządzania | Dokumenty firmy Microsoft
description: Rozwiązania do zarządzania zazwyczaj obejmują zapisane zapytania dziennika do analizowania danych zebranych przez rozwiązanie. W tym artykule opisano sposób definiowania wyszukiwań zapisanych w usłudze Log Analytics w szablonie Menedżera zasobów.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/29/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 61fc64e140af091b5ff3f631398daf901557791b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663032"
---
# <a name="adding-log-analytics-saved-searches-and-alerts-to-management-solution-preview"></a>Dodawanie usług Log Analytics zapisanych wyszukiwań i alertów do rozwiązania do zarządzania (Wersja zapoznawcza)

> [!IMPORTANT]
> Zgodnie [z wcześniejszymi zapowiedziami,](https://azure.microsoft.com/updates/switch-api-preference-log-alerts/)obszary robocze analizy dzienników utworzone po *1 czerwca 2019 r.* — będą mogły zarządzać regułami alertów przy użyciu **tylko** interfejsu [API REST](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/)platformy Azure scheduledQueryRules , [szablonu usługi Azure Resource Manager](../../azure-monitor/platform/alerts-log.md#managing-log-alerts-using-azure-resource-template) i polecenia [cmdlet programu PowerShell](../../azure-monitor/platform/alerts-log.md#managing-log-alerts-using-powershell). Klienci mogą łatwo [przełączyć preferowane środki zarządzania regułami alertów](../../azure-monitor/platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api) dla starszych obszarów roboczych, aby wykorzystać usługę Azure Monitor scheduledQueryRules jako domyślną i uzyskać wiele [nowych korzyści,](../../azure-monitor/platform/alerts-log-api-switch.md#benefits-of-switching-to-new-azure-api) takich jak możliwość używania natywnych poleceń cmdlet programu PowerShell, wydłużony okres odnośnego okresu w regułach, tworzenie reguł w oddzielnej grupie zasobów lub subskrypcji i wiele więcej.

> [!NOTE]
> Jest to wstępna dokumentacja do tworzenia rozwiązań do zarządzania, które są obecnie w wersji zapoznawczej. Każdy schemat opisany poniżej może ulec zmianie.

[Rozwiązania do zarządzania](solutions.md) zazwyczaj obejmują [zapisane wyszukiwania](../../azure-monitor/log-query/log-query-overview.md) w usłudze Log Analytics w celu analizowania danych zebranych przez rozwiązanie. Mogą również definiować [alerty,](../../azure-monitor/platform/alerts-overview.md) aby powiadomić użytkownika lub automatycznie podjąć działania w odpowiedzi na krytyczny problem. W tym artykule opisano sposób definiowania zapisanych wyszukiwań i alertów usługi Log Analytics w [szablonie zarządzania zasobami,](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) aby można je było uwzględnić w rozwiązaniach do [zarządzania.](solutions-creating.md)

> [!NOTE]
> Przykłady w tym artykule używają parametrów i zmiennych, które są wymagane lub wspólne dla rozwiązań do zarządzania i opisane w [Projektowanie i tworzenie rozwiązania do zarządzania na platformie Azure](solutions-creating.md)

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule założono, że jesteś już zaznajomiony z jak utworzyć rozwiązanie do [zarządzania](solutions-creating.md) i struktury [szablonu Menedżera zasobów](../../azure-resource-manager/templates/template-syntax.md) i pliku rozwiązania.


## <a name="log-analytics-workspace"></a>Obszar roboczy usługi Log Analytics
Wszystkie zasoby w usłudze Log Analytics znajdują się w [obszarze roboczym](../../azure-monitor/platform/manage-access.md). Zgodnie z opisem w [obszarze roboczym usługi Log Analytics i konta automatyzacji](solutions.md#log-analytics-workspace-and-automation-account)obszar roboczy nie jest uwzględniony w rozwiązaniu do zarządzania, ale musi istnieć przed zainstalowaniem rozwiązania. Jeśli nie jest dostępna, instalacja rozwiązania nie powiedzie się.

Nazwa obszaru roboczego jest w nazwie każdego zasobu usługi Log Analytics. Odbywa się to w rozwiązaniu z parametrem **obszaru roboczego,** jak w poniższym przykładzie zasobu SavedSearch.

    "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearchId'))]"

## <a name="log-analytics-api-version"></a>Wersja interfejsu API usługi Log Analytics
Wszystkie zasoby usługi Log Analytics zdefiniowane w szablonie Menedżera zasobów mają **apiVersion** właściwości, która definiuje wersję interfejsu API, którego zasób powinien używać.

W poniższej tabeli wymieniono wersję interfejsu API dla zasobu używanego w tym przykładzie.

| Typ zasobu | Wersja API | Zapytanie |
|:---|:---|:---|
| savedSearches (wyszukiwanie) | 2017-03-15-podgląd | Zdarzenie &#124; gdzie EventLevelName == "Błąd"  |


## <a name="saved-searches"></a>Zapisane wyszukiwania
Uwzględnij [zapisane wyszukiwania](../../azure-monitor/log-query/log-query-overview.md) w rozwiązaniu, aby umożliwić użytkownikom wykonywanie zapytań o dane zebrane przez rozwiązanie. Zapisane wyszukiwania są wyświetlane w obszarze **Zapisane wyszukiwania** w witrynie Azure portal. Dla każdego alertu wymagane jest również zapisane wyszukiwanie.

[Usługi Log Analytics zapisane](../../azure-monitor/log-query/log-query-overview.md) zasoby `Microsoft.OperationalInsights/workspaces/savedSearches` wyszukiwania mają typ i mają następującą strukturę. Obejmuje to typowe zmienne i parametry, dzięki czemu można skopiować i wkleić ten fragment kodu do pliku rozwiązania i zmienić nazwy parametrów.

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

Każda właściwość zapisanego wyszukiwania jest opisana w poniższej tabeli.

| Właściwość | Opis |
|:--- |:--- |
| category | Kategoria zapisanego wyszukiwania.  Wszystkie zapisane wyszukiwania w tym samym rozwiązaniu często współużytkują jedną kategorię, dzięki czemu są zgrupowane razem w konsoli. |
| displayname | Nazwa do wyświetlenia zapisanego wyszukiwania w portalu. |
| query | Kwerenda do uruchomienia. |

> [!NOTE]
> Może być konieczne użycie znaków ucieczki w kwerendzie, jeśli zawiera znaki, które mogą być interpretowane jako JSON. Na przykład, jeśli zapytanie było **AzureActivity | OperationName:"Microsoft.Compute/virtualMachines/write"**, powinno być napisane w pliku rozwiązania jako **AzureActivity | OperationName:/\"Microsoft.Compute/virtualMachines/write\"**.

## <a name="alerts"></a>Alerty
[Alerty dziennika platformy Azure](../../azure-monitor/platform/alerts-unified-log.md) są tworzone przez reguły usługi Azure Alert, które uruchamiają określone zapytania dziennika w regularnych odstępach czasu. Jeśli wyniki kwerendy odpowiadają określonym kryteriom, tworzony jest rekord alertu i jedna lub więcej akcji jest uruchamiana przy użyciu [grup akcji](../../azure-monitor/platform/action-groups.md).

Dla użytkowników, którzy rozszerzają alerty na platformę Azure, akcje są teraz kontrolowane w grupach akcji platformy Azure. Gdy obszar roboczy i jego alerty zostaną rozszerzone na platformę Azure, można pobrać lub dodać akcje przy użyciu [szablonu usługi Azure Resource Manager grupy akcji](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).
Reguły alertów w starszym rozwiązaniu do zarządzania tworzą następujące trzy różne zasoby.

- **Zapisane wyszukiwanie.** Definiuje wyszukiwanie dziennika, które jest uruchamiane. Wiele reguł alertów może współużytkować jedno zapisane wyszukiwanie.
- **Harmonogram.** Określa, jak często jest uruchamiane wyszukiwanie w dzienniku. Każda reguła alertu ma jeden i tylko jeden harmonogram.
- **Akcja alertu.** Każda reguła alertu ma jeden zasób grupy akcji lub zasób akcji (starszy) z typem **Alert,** który definiuje szczegóły alertu, takie jak kryteria tworzenia rekordu alertu i ważność alertu. [Zasób grupy akcji](../../azure-monitor/platform/action-groups.md) może mieć listę skonfigurowanych akcji do podjęcia po uruchomieniu alertu - takich jak połączenie głosowe, SMS, e-mail, element webhook, narzędzie ITSM, system runbook automatyzacji, aplikacja logiczna itp.

Zapisane zasoby wyszukiwania są opisane powyżej. Inne zasoby są opisane poniżej.

### <a name="schedule-resource"></a>Zaplanuj zasób

Zapisane wyszukiwanie może mieć jeden lub więcej harmonogramów, przy czym każdy harmonogram reprezentuje oddzielną regułę alertu. Harmonogram określa, jak często wyszukiwanie jest uruchamiane i przedział czasu, w którym dane są pobierane. Zasoby harmonogramu mają `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/` typ i mają następującą strukturę. Obejmuje to typowe zmienne i parametry, dzięki czemu można skopiować i wkleić ten fragment kodu do pliku rozwiązania i zmienić nazwy parametrów.

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
Właściwości zasobów harmonogramu są opisane w poniższej tabeli.

| Nazwa elementu | Wymagany | Opis |
|:--|:--|:--|
| enabled       | Tak | Określa, czy alert jest włączony podczas jego tworzenia. |
| interval      | Tak | Jak często kwerenda jest uruchamiana w minutach. |
| queryTimeSpan | Tak | Czas w minutach, przez który ma być oceniany wyniki. |

Zasób harmonogramu powinien zależeć od zapisanego wyszukiwania, tak aby został utworzony przed harmonogramem.
> [!NOTE]
> Nazwa harmonogramu musi być unikatowa w danym obszarze roboczym; dwa harmonogramy nie mogą mieć tego samego identyfikatora, nawet jeśli są skojarzone z różnymi zapisanymi wyszukiwaniami. Również nazwa dla wszystkich zapisanych wyszukiwań, harmonogramów i akcji utworzonych za pomocą interfejsu API usługi Log Analytics musi być w małych literach.

### <a name="actions"></a>Akcje
Harmonogram może mieć wiele akcji. Akcja może zdefiniować jeden lub więcej procesów do wykonania, takich jak wysyłanie poczty lub uruchamianie elementu runbook, lub może zdefiniować próg, który określa, kiedy wyniki wyszukiwania odpowiadają niektórym kryteriom. Niektóre akcje zdefiniują oba tak, aby procesy były wykonywane po osiągnięciu progu.
Akcje można definiować za pomocą zasobu [grupa akcji] lub zasobu akcji.

Istnieją dwa typy zasobów akcji określone przez **Type** właściwości. Harmonogram wymaga jednej akcji **alert,** która definiuje szczegóły reguły alertu i jakie akcje są podejmowane podczas tworzenia alertu. Zasoby akcji mają `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions`typ .

Akcje alertów mają następującą strukturę. Obejmuje to typowe zmienne i parametry, dzięki czemu można skopiować i wkleić ten fragment kodu do pliku rozwiązania i zmienić nazwy parametrów.

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

Właściwości zasobów akcji alertu są opisane w poniższych tabelach.

| Nazwa elementu | Wymagany | Opis |
|:--|:--|:--|
| `type` | Tak | Typ akcji.  Jest to **alert** dla akcji alertów. |
| `name` | Tak | Wyświetlana nazwa alertu.  Jest to nazwa wyświetlana w konsoli dla reguły alertu. |
| `description` | Nie | Opcjonalny opis alertu. |
| `severity` | Tak | Ważność rekordu alertu z następujących wartości:<br><br> **Krytyczne**<br>**warning**<br>**Informacyjne**

#### <a name="threshold"></a>Próg
Ta sekcja jest wymagana. Definiuje właściwości progu alertu.

| Nazwa elementu | Wymagany | Opis |
|:--|:--|:--|
| `Operator` | Tak | Operator do porównania z następującymi wartościami:<br><br>**gt =<br>większa niż lt = mniejsza niż** |
| `Value` | Tak | Wartość do porównania wyników. |

##### <a name="metricstrigger"></a>MetrykiTrygger
Ta sekcja jest opcjonalna. Dołącz go do alertu pomiaru metryki.

| Nazwa elementu | Wymagany | Opis |
|:--|:--|:--|
| `TriggerCondition` | Tak | Określa, czy próg dotyczy całkowitej liczby naruszeń lub kolejnych naruszeń z następujących wartości:<br><br>**Razem<br>po kolejnej** |
| `Operator` | Tak | Operator do porównania z następującymi wartościami:<br><br>**gt =<br>większa niż lt = mniejsza niż** |
| `Value` | Tak | Liczba godzin, w których muszą być spełnione kryteria, aby wyzwolić alert. |


#### <a name="throttling"></a>Ograniczanie przepływności
Ta sekcja jest opcjonalna. Dołącz tę sekcję, jeśli chcesz pominąć alerty z tej samej reguły przez pewien czas po utworzeniu alertu.

| Nazwa elementu | Wymagany | Opis |
|:--|:--|:--|
| Czas trwaniaInminutes | Tak, jeśli uwzględniono element ograniczania przepustowości | Liczba minut do wygaszenia alertów po utworzeniu jednej z tej samej reguły alertu. |

#### <a name="azure-action-group"></a>Grupa akcji platformy Azure
Wszystkie alerty na platformie Azure, użyj grupy akcji jako domyślny mechanizm obsługi akcji. Za pomocą grupy akcji można określić akcje raz, a następnie skojarzyć grupę akcji z wieloma alertami — na platformie Azure. Bez konieczności, wielokrotnie deklarować te same działania w kółko. Grupy akcji obsługują wiele działań — w tym pocztę e-mail, SMS, połączenie głosowe, połączenie ITSM, system y runbook automatyzacji, identyfikator URI typu webhook i inne.

Dla użytkowników, którzy rozszerzyli swoje alerty na platformie Azure — harmonogram powinien teraz mieć szczegóły grupy akcji przekazywane wraz z progiem, aby móc utworzyć alert. Szczegóły wiadomości e-mail, adresy URL elementu webhook, szczegóły automatyzacji elementu runbook i inne akcje muszą zostać zdefiniowane w grupie akcji najpierw po stronie grupy akcji przed utworzeniem alertu; można utworzyć [grupę akcji z usługi Azure Monitor](../../azure-monitor/platform/action-groups.md) w portalu lub użyć grupy akcji — szablon [zasobu](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

| Nazwa elementu | Wymagany | Opis |
|:--|:--|:--|
| AzNsNotification (AzNsNotification) | Tak | Identyfikator zasobu grupy akcji platformy Azure, który ma być skojarzony z alertem do podejmowania niezbędnych działań po spełnieniu kryteriów alertu. |
| Usługa CustomEmailSubject | Nie | Niestandardowy wiersz tematu wiadomości wysłanej na wszystkie adresy określone w skojarzonej grupie akcji. |
| Usługa CustomWebhookPłata | Nie | Dostosowany ładunek do wysłania do wszystkich punktów końcowych elementu webhook zdefiniowanych w skojarzonej grupie akcji. Format zależy od tego, czego oczekuje element webhook i powinien być prawidłowy serializowany JSON. |

## <a name="sample"></a>Sample

Poniżej przedstawiono przykład rozwiązania, które zawiera następujące zasoby:

- Zapisane wyszukiwanie
- Harmonogram
- Grupa akcji

Przykład używa zmiennych [parametrów rozwiązania standardowego,]( solutions-solution-file.md#parameters) które często byłyby używane w rozwiązaniu, w przeciwieństwie do wartości hardcoding w definicjach zasobów.

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

Poniższy plik parametru zawiera przykładowe wartości dla tego rozwiązania.

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

## <a name="next-steps"></a>Następne kroki
* [Dodaj widoki](solutions-resources-views.md) do rozwiązania do zarządzania.
* [Dodaj elementy runbook automatyzacji i inne zasoby](solutions-resources-automation.md) do rozwiązania do zarządzania.
