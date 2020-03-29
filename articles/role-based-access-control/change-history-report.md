---
title: Wyświetlanie dzienników aktywności dla zmian rbac platformy Azure
description: Wyświetlanie dzienników aktywności dla zmian kontroli dostępu opartej na rolach platformy Azure (Azure RBAC) w zasobach platformy Azure w ciągu ostatnich 90 dni.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 2bc68595-145e-4de3-8b71-3a21890d13d9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e2024bd14241184338195ed635039bae774da816
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78161792"
---
# <a name="view-activity-logs-for-azure-rbac-changes"></a>Wyświetlanie dzienników aktywności dla zmian rbac platformy Azure

Czasami potrzebujesz informacji o zmianach kontroli dostępu opartej na rolach platformy Azure (Azure RBAC), takich jak inspekcja lub rozwiązywanie problemów. Za każdym razem, gdy ktoś wprowadza zmiany w przypisaniach ról lub definicjach ról w ramach subskrypcji, zmiany są rejestrowane w [dzienniku aktywności platformy Azure.](../azure-monitor/platform/platform-logs-overview.md) Można wyświetlić dzienniki aktywności, aby wyświetlić wszystkie zmiany usługi Azure RBAC w ciągu ostatnich 90 dni.

## <a name="operations-that-are-logged"></a>Operacje rejestrowane

Oto operacje związane z funkcją Azure RBAC, które są rejestrowane w dzienniku aktywności:

- Tworzenie przypisania roli
- Usuwanie przypisania roli
- Tworzenie lub aktualizowanie niestandardowej definicji roli
- Usuwanie niestandardowej definicji roli

## <a name="azure-portal"></a>Portal Azure

Najprostszym sposobem rozpoczęcia pracy jest wyświetlenie dzienników aktywności za pomocą witryny Azure Portal. Poniższy zrzut ekranu przedstawia przykład operacji przypisywania ról w dzienniku działań. Zawiera również opcję pobierania dzienników jako pliku CSV.

![Dzienniki aktywności za pomocą portalu - zrzut ekranu](./media/change-history-report/activity-log-portal.png)

Dziennik aktywności w portalu zawiera kilka filtrów. Oto filtry związane z funkcją Azure RBAC:

| Filtr | Wartość |
| --------- | --------- |
| Kategoria zdarzenia | <ul><li>Administracyjne</li></ul> |
| Operacja | <ul><li>Tworzenie przypisania roli</li><li>Usuwanie przypisania roli</li><li>Tworzenie lub aktualizowanie niestandardowej definicji roli</li><li>Usuwanie niestandardowej definicji roli</li></ul> |

Aby uzyskać więcej informacji na temat dzienników aktywności, zobacz [Wyświetlanie dzienników aktywności](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)w celu monitorowania akcji zasobów .

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

Aby wyświetlić dzienniki aktywności za pomocą programu Azure PowerShell, należy użyć polecenia [Get-AzLog.](/powershell/module/Az.Monitor/Get-AzLog)

To polecenie wyświetla listę wszystkich zmian przypisania roli w subskrypcji w ciągu ostatnich siedmiu dni:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleAssignments/*'}
```

To polecenie wyświetla listę wszystkich zmian definicji roli w grupie zasobów w ciągu ostatnich siedmiu dni:

```azurepowershell
Get-AzLog -ResourceGroupName pharma-sales -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleDefinitions/*'}
```

To polecenie wyświetla listę wszystkich zmian przypisania roli i definicji roli w subskrypcji w ciągu ostatnich siedmiu dni i wyświetla wyniki na liście:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/role*'} | Format-List Caller,EventTimestamp,{$_.Authorization.Action},Properties
```

```Example
Caller                  : alain@example.com
EventTimestamp          : 2/27/2020 9:18:07 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          statusCode     : Created
                          serviceRequestId: 11111111-1111-1111-1111-111111111111

Caller                  : alain@example.com
EventTimestamp          : 2/27/2020 9:18:05 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          requestbody    : {"Id":"22222222-2222-2222-2222-222222222222","Properties":{"PrincipalId":"33333333-3333-3333-3333-333333333333","RoleDefinitionId":"/subscriptions/00000000-0000-0000-0000-000000000000/providers
                          /Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","Scope":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"}}

```

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby wyświetlić dzienniki aktywności za pomocą interfejsu wiersza polecenia interfejsu wiersza polecenia platformy Azure, użyj polecenia [az monitor activity-log list.](/cli/azure/monitor/activity-log#az-monitor-activity-log-list)

To polecenie wyświetla listę dzienników aktywności w grupie zasobów od 27 lutego, z niecierpliwością na siedem dni:

```azurecli
az monitor activity-log list --resource-group pharma-sales --start-time 2020-02-27 --offset 7d
```

To polecenie wyświetla listę dzienników aktywności dostawcy zasobów autoryzacji od 27 lutego, z niecierpliwością na siedem dni:

```azurecli
az monitor activity-log list --namespace "Microsoft.Authorization" --start-time 2020-02-27 --offset 7d
```

## <a name="azure-monitor-logs"></a>Dzienniki usługi Azure Monitor

[Dzienniki usługi Azure Monitor](../log-analytics/log-analytics-overview.md) to kolejne narzędzie, którego można używać do zbierania i analizowania zmian usługi Azure RBAC dla wszystkich zasobów platformy Azure. Dzienniki usługi Azure Monitor mają następujące zalety:

- Pisanie złożonych zapytań i logiki
- Integracja z alertami, usługa Power BI i innymi narzędziami
- Zapisywanie danych przez dłuższy okres przechowywania
- Odsyłacz do innych dzienników, takich jak zabezpieczenia, maszyna wirtualna i

Oto podstawowe kroki, aby rozpocząć:

1. [Tworzenie obszaru roboczego usługi Log Analytics](../azure-monitor/learn/quick-create-workspace.md).

1. [Skonfiguruj rozwiązanie analizy dzienników aktywności](../azure-monitor/platform/activity-log-collect.md#activity-logs-analytics-monitoring-solution) dla obszaru roboczego.

1. [Wyświetlanie dzienników aktywności](../azure-monitor/platform/activity-log-collect.md#activity-logs-analytics-monitoring-solution). Szybkim sposobem przejścia do strony Omówienie rozwiązania usługi Activity Log Analytics jest kliknięcie opcji **Dzienniki.**

   ![Opcja Dzienniki usługi Azure Monitor w portalu](./media/change-history-report/azure-log-analytics-option.png)

1. Opcjonalnie użyj [usługi Azure Monitor Log Analytics](../azure-monitor/log-query/get-started-portal.md) do wykonywania zapytań i wyświetlania dzienników. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do zapytań dziennika usługi Azure Monitor](../azure-monitor/log-query/get-started-queries.md).

Oto kwerenda, która zwraca nowe przypisania ról zorganizowane przez docelowego dostawcę zasobów:

```Kusto
AzureActivity
| where TimeGenerated > ago(60d) and Authorization contains "Microsoft.Authorization/roleAssignments/write" and ActivityStatus == "Succeeded"
| parse ResourceId with * "/providers/" TargetResourceAuthProvider "/" *
| summarize count(), makeset(Caller) by TargetResourceAuthProvider
```

Oto kwerenda, która zwraca zmiany przypisania ról wyświetlane na wykresie:

```Kusto
AzureActivity
| where TimeGenerated > ago(60d) and Authorization contains "Microsoft.Authorization/roleAssignments"
| summarize count() by bin(TimeGenerated, 1d), OperationName
| render timechart
```

![Dzienniki aktywności za pomocą portalu Advanced Analytics - zrzut ekranu](./media/change-history-report/azure-log-analytics.png)

## <a name="next-steps"></a>Następne kroki
* [Wyświetlanie zdarzeń w dzienniku aktywności](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)
* [Monitorowanie aktywności subskrypcji za pomocą dziennika aktywności platformy Azure](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)
