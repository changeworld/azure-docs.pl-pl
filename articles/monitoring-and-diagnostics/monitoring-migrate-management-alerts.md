---
title: Migrowanie Azure alertów dotyczących zdarzeń zarządzania do alertów dotyczących działań w Dzienniku | Dokumentacja firmy Microsoft
description: Alerty dotyczące zdarzeń zarządzania zostanie usunięty 1 października. Przygotuj przez Migrowanie istniejących alertów.
author: johnkemnetz
manager: orenr
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: johnkem
ms.openlocfilehash: 1cc02664a345da49c276a6876d5cdfaf5bdb8477
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="migrate-azure-alerts-on-management-events-to-activity-log-alerts"></a>Migrowanie Azure alertów dotyczących zdarzeń zarządzania do dziennika aktywności alertów


> [!WARNING]
> Alerty dotyczące zdarzeń zarządzania zostaną wyłączone na lub po 1 października. Użyj instrukcjami poniżej, aby zrozumieć, jeśli masz te alerty i ich migracją, jeśli tak.
>
> 

## <a name="what-is-changing"></a>Co to jest zmieniany

Monitor Azure (poprzednio Azure Insights) oferowane możliwość utworzenia alert wyzwolony wylogowuje zdarzeń zarządzania i generowane powiadomienia na adresy URL lub wiadomości e-mail elementu webhook. Został utworzony dla jednej z tych alertów żadnego z następujących sposobów:
* W portalu Azure dotyczące określonych typów zasobów, w obszarze monitorowanie -> Alerty -> Dodaj alertu, gdzie "W alercie" jest ustawiona na "Zdarzenia"
* Uruchamiając polecenie cmdlet programu PowerShell Dodaj AzureRmLogAlertRule
* Za pomocą bezpośrednio [alertu interfejsu API REST](http://docs.microsoft.com/rest/api/monitor/alertrules) odata.type = "ManagementEventRuleCondition" i dataSource.odata.type = "RuleManagementEventDataSource"
 
Poniższy skrypt programu PowerShell zwraca listę wszystkie alerty dotyczące zdarzeń zarządzania w Twojej subskrypcji, jak również warunków ustawionych na każdy alert.

```powershell
Connect-AzureRmAccount
$alerts = $null
foreach ($rg in Get-AzureRmResourceGroup ) {
  $alerts += Get-AzureRmAlertRule -ResourceGroup $rg.ResourceGroupName
}
foreach ($alert in $alerts) {
  if($alert.Properties.Condition.DataSource.GetType().Name.Equals("RuleManagementEventDataSource")) {
    "Alert Name: " + $alert.Name
    "Alert Resource ID: " + $alert.Id
    "Alert conditions:"
    $alert.Properties.Condition.DataSource
    "---------------------------------"
  }
} 
```

Jeśli masz żadne alerty dotyczące zdarzeń zarządzania powyższego polecenia cmdlet programu PowerShell dane wyjściowe obejmują szereg komunikaty ostrzegawcze podobne do następującego:

`WARNING: The output of this cmdlet will be flattened, i.e. elimination of the properties field, in a future release to improve the user experience.`

Te komunikaty ostrzegawcze można zignorować. Jeśli masz alerty dotyczące zdarzeń zarządzania, dane wyjściowe tego polecenia cmdlet programu PowerShell będzie wyglądać następująco:

```
Alert Name: webhookEvent1
Alert Resource ID: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/microsoft.insights/alertrules/webhookEvent1
Alert conditions:

EventName            : 
EventSource          : 
Level                : 
OperationName        : microsoft.web/sites/start/action
ResourceGroupName    : 
ResourceProviderName : 
Status               : succeeded
SubStatus            : 
Claims               : Microsoft.Azure.Management.Monitor.Management.Models.RuleManagementEventClaimsDataSource
ResourceUri          : /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/Microsoft.Web/sites/samplealertapp

---------------------------------
Alert Name: someclilogalert
Alert Resource ID: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/microsoft.insights/alertrules/someclilogalert
Alert conditions:

EventName            : 
EventSource          : 
Level                : 
OperationName        : Start
ResourceGroupName    : 
ResourceProviderName : 
Status               : 
SubStatus            : 
Claims               : Microsoft.Azure.Management.Monitor.Management.Models.RuleManagementEventClaimsDataSource
ResourceUri          : /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/Microsoft.Compute/virtualMachines/Seaofclouds

---------------------------------
```

Każdy alert jest oddzielona linię kropkowaną i identyfikator zasobu alert i określonej reguły monitorowanych zawierają szczegółowe informacje.

Ta funkcja została przekształcona do [alerty dziennika aktywności monitora Azure](monitoring-activity-log-alerts.md). Te nowe alerty umożliwiają warunek zdarzeń dziennika aktywności i otrzymasz powiadomienie, gdy jest to nowe zdarzenie spełnia warunek. Oferują one również kilka ulepszeń z alertów dotyczących zdarzeń zarządzania:
* Ponowne użycie grupy odbiorców powiadomień ("działania") przez wiele alertów za pomocą [grupy akcji](monitoring-action-groups.md), zmniejsza się złożoność zmian, który powinien zostać wyświetlony alert.
* Zostanie wyświetlone powiadomienie, bezpośrednio na telefonie z grupami akcji przy użyciu programu SMS.
* Możesz [tworzyć alerty dziennika aktywności z szablonami usługi Resource Manager](monitoring-create-activity-log-alerts-with-resource-manager-template.md).
* Warunki można tworzyć z większą elastyczność i złożoność do konkretnych potrzeb.
* Powiadomienia są dostarczane szybciej.
 
## <a name="how-to-migrate"></a>Jak przeprowadzić migrację
 
Aby utworzyć nowe działanie alertów dziennika, możesz:
* Wykonaj [nasze wskazówki na temat tworzenia alertu w portalu Azure](monitoring-activity-log-alerts.md)
* Dowiedz się, jak [utworzyć alert przy użyciu szablonu usługi Resource Manager](monitoring-create-activity-log-alerts-with-resource-manager-template.md)
 
Alerty dotyczące zdarzeń zarządzania, które zostały wcześniej utworzone nie będzie są automatycznie migrowane do alertów dotyczących działań w dzienniku. Należy użyć powyższy skrypt programu PowerShell, aby wyświetlić listę alertów dotyczących zdarzeń zarządzania obecnie zostały skonfigurowane, a następnie ponownie ręcznie utworzyć je jako alertów dotyczących działań w dzienniku. Należy to zrobić przed 1 października, po którym alertów dotyczących zdarzeń zarządzania nie będą już widoczne w Twojej subskrypcji platformy Azure. Innych typów alertów Azure, w tym metryki alerty monitora Azure, alerty usługi Application Insights i analizy dzienników alerty są dotknięte tej zmiany. Jeśli masz pytania, opublikuj na komentarze poniżej.


## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [dziennik aktywności](monitoring-overview-activity-logs.md)
* Skonfiguruj [alerty dziennika aktywności za pośrednictwem portalu Azure](monitoring-activity-log-alerts.md)
* Skonfiguruj [alerty dziennika aktywności za pomocą Menedżera zasobów](monitoring-create-activity-log-alerts-with-resource-manager-template.md)
* Przegląd [schemat alertu elementu webhook dziennika aktywności](monitoring-activity-log-alerts-webhook.md)
* Dowiedz się więcej o [powiadomień usługi](monitoring-service-notifications.md)
* Dowiedz się więcej o [grupy akcji](monitoring-action-groups.md)
