---
title: Migracja platformy Azure alertów dotyczących zdarzeń zarządzania do alertów dziennika aktywności
description: Alerty dotyczące zdarzeń zarządzania zostaną usunięte w dniu 1 października. Przygotować, włączając Migrowanie istniejących alertów.
author: johnkemnetz
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 08/14/2017
ms.author: johnkem
ms.subservice: alerts
ms.openlocfilehash: fb54e11c9da6bec2a1e0354317df6343140cbf09
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/04/2019
ms.locfileid: "59006797"
---
# <a name="migrate-azure-alerts-on-management-events-to-activity-log-alerts"></a>Migracja platformy Azure alertów dotyczących zdarzeń zarządzania do alertów dziennika aktywności

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!WARNING]
> Alerty dotyczące zdarzeń zarządzania zostaną wyłączone w dniu lub po 1 października. Aby dowiedzieć się, jeśli masz te alerty i przeprowadzić ich migrację, jeśli tak, użyj czynności opisane poniżej.

## <a name="what-is-changing"></a>Co ulega zmianie

Usługa Azure Monitor (dawniej Azure Insights) oferowana możliwość tworzenia alertu wyzwolona poza zdarzeń zarządzania i generowane powiadomienia na adresy URL lub adres e-mail elementu webhook. Które mogły zostać utworzone dla jednej z tych alertów, jeden z następujących sposobów:
* W witrynie Azure portal dla niektórych typów zasobów, w obszarze monitorowanie -> Alerty -> Dodaj Alert, gdy "Alert dotyczący" jest równa "Events"
* Uruchamiając polecenie cmdlet programu PowerShell Dodaj AzLogAlertRule
* Za pomocą bezpośrednio [interfejsu API REST alertów](https://docs.microsoft.com/rest/api/monitor/alertrules) odata.type = "ManagementEventRuleCondition" i dataSource.odata.type = "RuleManagementEventDataSource"
 
Poniższy skrypt programu PowerShell zwraca listę wszystkich alertów w zdarzenia zarządzania, które masz w subskrypcji, a także warunków określonych na temat każdego alertu.

```powershell
Connect-AzAccount
$alerts = $null
foreach ($rg in Get-AzResourceGroup ) {
  $alerts += Get-AzAlertRule -ResourceGroup $rg.ResourceGroupName
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

Jeśli masz żadne alerty dotyczące zdarzeń zarządzania powyższe polecenia cmdlet programu PowerShell zwróci szereg komunikaty ostrzegawcze podobny do poniższego:

`WARNING: The output of this cmdlet will be flattened, i.e. elimination of the properties field, in a future release to improve the user experience.`

Można zignorować te ostrzeżenia. Jeśli masz alerty dotyczące zdarzeń zarządzania, dane wyjściowe tego polecenia cmdlet programu PowerShell będzie wyglądać następująco:

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

Każdy alert jest oddzielona linię przerywaną, co i identyfikator zasobu alert i określonej reguły monitorowanych znajdują się szczegółowe informacje.

Ta funkcja została zastąpiona [alertów dziennika aktywności platformy Azure w monitorze](../../azure-monitor/platform/activity-log-alerts.md). Te nowe alerty umożliwiają nastavit warunek zdarzenia dziennika aktywności i Otrzymuj powiadomienie, gdy warunek jest zgodna z nowego zdarzenia. Oferują one także kilka następujących ulepszeń z alertów dotyczących zdarzeń zarządzania:
* Ponowne użycie grupy odbiorców powiadomień ("Akcje") w wielu alertów za pomocą [grup akcji](../../azure-monitor/platform/action-groups.md), co zmniejsza złożoność zmiana, który powinien zostać wyświetlony alert.
* Otrzymasz powiadomienie bezpośrednio na Twój telefon, za pomocą wiadomości SMS za pomocą grup akcji.
* Możesz [Tworzenie alertów dziennika aktywności przy użyciu szablonów usługi Resource Manager](../../azure-monitor/platform/alerts-activity-log.md).
* Warunki można tworzyć z większą elastycznością i złożoność do konkretnych potrzeb.
* Powiadomienia są dostarczane szybciej.
 
## <a name="how-to-migrate"></a>Jak przeprowadzić migrację
 
Aby utworzyć nowe działanie alertów dzienników, można:
* Postępuj zgodnie z [nasz Przewodnik na temat tworzenia alertu w witrynie Azure portal](../../azure-monitor/platform/activity-log-alerts.md)
* Dowiedz się, jak [Utwórz alert przy użyciu szablonu usługi Resource Manager](../../azure-monitor/platform/alerts-activity-log.md)
 
Alerty dotyczące zdarzeń zarządzania, które zostały wcześniej utworzone nie będzie są automatycznie migrowane do alertów dziennika aktywności. Należy użyć powyższy skrypt programu PowerShell, aby wyświetlić listę alertów dotyczących zdarzeń zarządzania obecnie zostały skonfigurowane, a następnie ponownie ręcznie utworzyć je jako alertów dziennika aktywności. Należy to zrobić przed 1 października, po upływie którego alertów dotyczących zdarzeń zarządzania nie będzie już widoczna w Twojej subskrypcji platformy Azure. Inne typy alertów platformy Azure, w tym alertów dotyczących metryk usługi Azure Monitor, alerty usługi Application Insights i wyszukiwanie w dzienniku alertów są ta zmiana nie ma wpływu. Jeśli masz jakieś pytania, Opublikuj w komentarzach poniżej.


## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [dziennika aktywności](../../azure-monitor/platform/activity-logs-overview.md)
* Konfigurowanie [alertów dziennika aktywności w witrynie Azure portal](../../azure-monitor/platform/activity-log-alerts.md)
* Konfigurowanie [alertów dziennika aktywności przy użyciu usługi Resource Manager](../../azure-monitor/platform/alerts-activity-log.md)
* Przegląd [schemat elementów webhook alertu dziennika aktywności](../../azure-monitor/platform/activity-log-alerts-webhook.md)
* Dowiedz się więcej o [powiadomień dotyczących usług](../../azure-monitor/platform/service-notifications.md)
* Dowiedz się więcej o [grupy akcji](../../azure-monitor/platform/action-groups.md)

