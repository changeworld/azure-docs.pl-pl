---
title: Konfigurowanie alertów dotyczących kondycji zasobów platformy Azure przy użyciu szablonów usługi Resource Manager | Dokumentacja firmy Microsoft
description: Tworzenie alertów programowe, które powiadomienie, gdy zasoby platformy Azure staną się niedostępne.
author: stephbaron
ms.author: stbaron
ms.topic: conceptual
ms.service: service-health
ms.date: 9/4/2018
ms.openlocfilehash: de5419bbfa5a364cb570651fd4d57d7560e4aafd
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58663438"
---
# <a name="configure-resource-health-alerts-using-resource-manager-templates"></a>Konfigurowanie alertów dotyczących kondycji zasobów przy użyciu szablonów usługi Resource Manager

W tym artykule pokazano sposób tworzenia zasobów kondycji alertów dziennika aktywności programowo przy użyciu szablonów usługi Azure Resource Manager i programu Azure PowerShell.

Usługa Azure Resource Health przechowuje, poinformowani o bieżące i historyczne kondycja zasobów platformy Azure. Alerty usługi Azure Resource Health może generować powiadomienia w niemal w czasie rzeczywistym, gdy te zasoby mają zmianę stanu kondycji. Tworzenie Resource Health alerty programowo pozwalają użytkownikom na tworzenie i Dostosowywanie alertów w trybie zbiorczym.

> [!NOTE]
> Alerty dotyczące kondycji zasobów są obecnie dostępne w wersji zapoznawczej.

## <a name="prerequisites"></a>Wymagania wstępne

Postępuj zgodnie z instrukcjami na tej stronie, należy wcześniej skonfigurować kilka ustawień:

1. Musisz zainstalować [modułu Azure PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) (`AzureRm`)
2. Musisz [tworzenia lub ponownego użycia grupy akcji](../azure-monitor/platform/action-groups.md) skonfigurowane powiadomienia użytkownika

## <a name="instructions"></a>Instrukcje
1. Przy użyciu programu PowerShell, zaloguj się do platformy Azure przy użyciu swojego konta i wybierz subskrypcję, której ma nastąpić interakcja

        Login-AzureRmAccount
        Select-AzureRmSubscription -Subscription <subscriptionId>

    > Możesz użyć `Get-AzureRmSubscription` listę subskrypcji możesz mieć możliwość.

2. Znajdź, a następnie zapisz pełny identyfikator usługi Azure Resource Manager dla grupy akcji

        (Get-AzureRmActionGroup -ResourceGroupName <resourceGroup> -Name <actionGroup>).Id

3. Utwórz i Zapisz szablon usługi Resource Manager dla alertów dotyczących kondycji zasobu jako `resourcehealthalert.json` ([Zobacz szczegóły poniżej](#resource-manager-template-for-resource-health-alerts))

4. Utwórz nowe wdrożenie usługi Azure Resource Manager przy użyciu tego szablonu

        New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName <resourceGroup> -TemplateFile <path\to\resourcehealthalert.json>

5. Zostanie wyświetlony monit o wpisanie nazwy alertu i skopiowane wcześniej identyfikator zasobu grupy akcji:

        Supply values for the following parameters:
        (Type !? for Help.)
        activityLogAlertName: <Alert Name>
        actionGroupResourceId: /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/microsoft.insights/actionGroups/<actionGroup>

6. Jeśli wszystko działało pomyślnie, otrzymasz potwierdzenie w programie PowerShell

        DeploymentName          : ExampleDeployment
        ResourceGroupName       : <resourceGroup>
        ProvisioningState       : Succeeded
        Timestamp               : 11/8/2017 2:32:00 AM
        Mode                    : Incremental
        TemplateLink            :
        Parameters              :
                                Name                     Type       Value
                                ===============          =========  ==========
                                activityLogAlertName     String     <Alert Name>
                                activityLogAlertEnabled  Bool       True
                                actionGroupResourceId    String     /...
        
        Outputs                 :
        DeploymentDebugLogLevel :

Należy pamiętać, że jeśli planowane jest w pełni automatyzując ten proces, po prostu musisz edytować szablon usługi Resource Manager, nie monit o podanie wartości w kroku 5.

## <a name="resource-manager-template-for-resource-health-alerts"></a>Szablon usługi Resource Manager alertów dotyczących kondycji zasobu

Tego podstawowego szablonu jako punktu wyjścia służy do tworzenia alertów dotyczących kondycji zasobu. Ten szablon będzie działać zgodnie z zapisane i będą Zarejestruj możesz otrzymywać alerty dla zdarzenia dotyczące kondycji wszystkich zasobów nowo aktywowanego wszystkich zasobów w ramach subskrypcji.

> W dolnej części tego artykułu wprowadzono również bardziej złożony szablon alertu, w którym należy zwiększyć sygnał szum alerty dotyczące kondycji zasobów w porównaniu do tego szablonu.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "activityLogAlertName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Activity log alert."
      }
    },
    "actionGroupResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource Id for the Action group."
      }
    }
  },
  "resources": [   
    {
      "type": "Microsoft.Insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlertName')]",      
      "location": "Global",
      "properties": {
        "enabled": true,
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "ResourceHealth"
            },
            {
              "field": "status",
              "equals": "Active"
            }
          ]
        },
        "actions": {
          "actionGroups":
          [
            {
              "actionGroupId": "[parameters('actionGroupResourceId')]"
            }
          ]
        }
      }
    }
  ]
}
```

Jednak szeroką alert podobny do poniższego ogólnie nie jest zalecane. Dowiedz się, jak ustawić szczegółów tego alertu, aby skoncentrować się na zdarzenia, które Dbamy o poniżej.

### <a name="adjusting-the-alert-scope"></a>Dostosowując zakres alertu

Można skonfigurować alerty dotyczące kondycji zasobów w celu monitorowania zdarzeń w trzech różnych zakresów:

 * Poziom subskrypcji
 * Poziomie grupy zasobów
 * Poziom zasobu

Szablon alertu jest skonfigurowana na poziomie subskrypcji, ale jeśli chcesz skonfigurować alert tylko Powiadomimy Cię o niektórych zasobów lub zasobów w grupie zasobów, po prostu musisz zmodyfikować `scopes` sekcji powyżej szablon.

Dla zakresu poziomu grupy zasobów w sekcji zakresy powinien wyglądać:
```json
"scopes": [
    "/subscriptions/<subscription id>/resourcegroups/<resource group>"
],
```

I poziomu zakresu zasobów, w sekcji zakres powinien wyglądać następująco:

```json
"scopes": [
    "/subscriptions/<subscription id>/resourcegroups/<resource group>/providers/<resource>"
],
```

Na przykład: `"/subscriptions/d37urb3e-ed41-4670-9c19-02a1d2808ff9/resourcegroups/myRG/providers/microsoft.compute/virtualmachines/myVm"`

> Można przejść do witryny Azure Portal i spójrz na adres URL, podczas wyświetlania swoich zasobów platformy Azure, aby uzyskać ten ciąg.

### <a name="adjusting-the-resource-types-which-alert-you"></a>Dopasowywanie zasobów typy, które wysyłania alertów

Alerty na poziomie grupy zasobów lub subskrypcji mogą mieć różnych rodzajów zasobów. Jeśli chcesz ograniczyć alerty pochodzić tylko z podzbioru typów zasobów, można zdefiniować w `condition` części szablonu w następujący sposób:

```json
"condition": {
    "allOf": [
        ...,
        {
            "anyOf": [
                {
                    "field": "resourceType",
                    "equals": "Microsoft.Compute/virtualMachines",
                    "containsAny": null
                },
                {
                    "field": "resourceType",
                    "equals": "Microsoft.Storage/storageAccounts",
                    "containsAny": null
                },
                ...
            ]
        }
    ]
},
```

W tym miejscu użyjemy `anyOf` otoki, aby umożliwić alertu kondycji zasobów do dopasowania którykolwiek z warunków określamy, co zapewnia alerty, przeznaczonych dla określonych typów zasobów.

### <a name="adjusting-the-resource-health-events-that-alert-you"></a>Dostosowywanie zdarzenia kondycji zasobów, które alert
Gdy zasobów podlegają zdarzenie kondycji, może przejść przez serię etapów, który reprezentuje stan zdarzenie kondycji: `Active`, `InProgress`, `Updated`, i `Resolved`.

Może być tylko chcesz otrzymywać powiadomienia, gdy zasób staje się nieprawidłowy, w którym to przypadku chcesz skonfigurować alertu można tylko Powiadom mnie, kiedy `status` jest `Active`. Jednak jeśli chcesz otrzymywać powiadomienia na inne etapy, można dodać te informacje w następujący sposób:

```json
"condition": {
    "allOf": [
        ...,
        {
            "anyOf": [
                {
                    "field": "status",
                    "equals": "Active"
                },
                {
                    "field": "status",
                    "equals": "InProgress"
                },
                        {
                    "field": "status",
                    "equals": "Resolved"
                }
            ]
        }
    ]
}
```

Jeśli chcesz otrzymać powiadomienie o wszystkich czterech etapów zdarzenia dotyczące kondycji, możesz usunąć ten warunek wszystko ze sobą i alert wyświetli powiadomienie, niezależnie od `status` właściwości.

### <a name="adjusting-the-resource-health-alerts-to-avoid-unknown-events"></a>Dostosowywanie alertów dotyczących kondycji zasobów, aby uniknąć zdarzenia "Nieznany"

Funkcja kondycji zasobów platformy Azure może raportować do możesz najnowsze kondycji zasobów, nieustannie monitorując je przy użyciu test Runner. Stany odpowiednie kondycji zgłaszane są: "Dostępne", "Niedostępne" i "Negatywny wpływ na dostępność". Jednak w sytuacjach, w których moduł uruchamiający testy i zasobów platformy Azure, mają nie może komunikować się zgłaszane stan kondycji "Nieznany" dla zasobu, i która jest traktowana jako zdarzenie kondycji "Aktywny".

Jednak gdy zasób zgłosi "Nieznana", jest prawdopodobne, że jej stan kondycji nie zmienił się od ostatniego raportu dokładne. Jeśli chcesz wyeliminować alertów dotyczących zdarzeń "Nieznany", można określić tej logiki w szablonie:

```json
"condition": {
    "allOf": [
        ...,
        {
            "anyOf": [
                {
                    "field": "properties.currentHealthStatus",
                    "equals": "Available",
                    "containsAny": null
                },
                {
                    "field": "properties.currentHealthStatus",
                    "equals": "Unavailable",
                    "containsAny": null
                },
                {
                    "field": "properties.currentHealthStatus",
                    "equals": "Degraded",
                    "containsAny": null
                }
            ]
        },
        {
            "anyOf": [
                {
                    "field": "properties.previousHealthStatus",
                    "equals": "Available",
                    "containsAny": null
                },
                {
                    "field": "properties.previousHealthStatus",
                    "equals": "Unavailable",
                    "containsAny": null
                },
                {
                    "field": "properties.previousHealthStatus",
                    "equals": "Degraded",
                    "containsAny": null
                }
            ]
        },
    ]
},
```

W tym przykładzie są możemy tylko powiadomienia dotyczące zdarzeń, gdy stan kondycji bieżących i wcześniejszych nie ma "Nieznane". Ta zmiana może być dodanie przydatne, gdy alerty są wysyłane bezpośrednio na telefon komórkowy lub adres e-mail.

### <a name="adjusting-the-alert-to-avoid-user-initiated-events"></a>Dostosowywanie alert, aby uniknąć użytkownik zainicjował zdarzenia

Zdarzenia dotyczące kondycji zasobów może być wyzwalacza przez platformę, inicjowane i zdarzenia zainicjowanego przez użytkownika. Rozsądne może okazać się tylko wysłanie powiadomienia, gdy zdarzenie kondycji jest spowodowany przez platformę Azure.

Jest łatwa do skonfigurowania alertu w taki sposób, aby filtrować pod kątem tego rodzaju zdarzenia:

```json
"condition": {
    "allOf": [
        ...,
        {
            "field": "properties.cause",
            "equals": "PlatformInitiated",
            "containsAny": null
        }
    ]
}
```

## <a name="recommended-resource-health-alert-template"></a>Zalecane szablon alertu kondycji zasobu

Przy użyciu różnych dostosowania opisane w poprzedniej sekcji, możemy utworzyć kompleksowe szablon alertu, który jest skonfigurowany w celu zmaksymalizowania sygnału szum.

Oto, co Sugerujemy, możesz użyć:
```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "activityLogAlertName": {
            "type": "string",
            "metadata": {
                "description": "Unique name (within the Resource Group) for the Activity log alert."
            }
        },
        "actionGroupResourceId": {
            "type": "string",
            "metadata": {
                "description": "Resource Id for the Action group."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Insights/activityLogAlerts",
            "apiVersion": "2017-04-01",
            "name": "[parameters('activityLogAlertName')]",
            "location": "Global",
            "properties": {
                "enabled": true,
                "scopes": [
                    "[subscription().id]"
                ],
                "condition": {
                    "allOf": [
                        {
                            "field": "category",
                            "equals": "ResourceHealth",
                            "containsAny": null
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "properties.currentHealthStatus",
                                    "equals": "Available",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.currentHealthStatus",
                                    "equals": "Unavailable",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.currentHealthStatus",
                                    "equals": "Degraded",
                                    "containsAny": null
                                }
                            ]
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "properties.previousHealthStatus",
                                    "equals": "Available",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.previousHealthStatus",
                                    "equals": "Unavailable",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.previousHealthStatus",
                                    "equals": "Degraded",
                                    "containsAny": null
                                }
                            ]
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "properties.cause",
                                    "equals": "PlatformInitiated",
                                    "containsAny": null
                                }
                            ]
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "status",
                                    "equals": "Active",
                                    "containsAny": null
                                },
                                {
                                    "field": "status",
                                    "equals": "Resolved",
                                    "containsAny": null
                                },
                                {
                                    "field": "status",
                                    "equals": "InProgress",
                                    "containsAny": null
                                }
                            ]
                        }
                    ]
                },
                "actions": {
                    "actionGroups": [
                        {
                            "actionGroupId": "[parameters('actionGroupResourceId')]"
                        }
                    ]
                }
            }
        }
    ]
}
```

Będzie jednak najlepiej wiedzieć, jakie konfiguracje zaczynają obowiązywać dla Ciebie, więc użyć narzędzia prowadzone użytkownikowi w tej dokumentacji się własne dostosowania.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat kondycji zasobu:
-  [Omówienie usługi Azure Resource Health](Resource-health-overview.md)
-  [Typy zasobów i kontrole kondycji dostępne w usłudze Azure Resource Health](resource-health-checks-resource-types.md)

Utwórz alerty dotyczące kondycji usługi:
-  [Konfigurowanie alertów w programie Service Health](../azure-monitor/platform/alerts-activity-log-service-notifications.md) 
