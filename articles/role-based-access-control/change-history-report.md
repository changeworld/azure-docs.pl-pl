---
title: Wyświetlanie dzienników aktywności dla RBAC zmieni się z zasobami platformy Azure | Dokumentacja firmy Microsoft
description: Wyświetl dzienniki aktywności dla kontroli dostępu opartej na rolach (RBAC) zmiany do zasobów platformy Azure w ciągu ostatnich 90 dni.
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
ms.date: 02/02/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b808654baded5bbe721866441a8d1115eff7bcaa
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "59997907"
---
# <a name="view-activity-logs-for-rbac-changes-to-azure-resources"></a>Wyświetlanie dzienników aktywności dla zmian RBAC do zasobów platformy Azure

Czasami potrzebujesz informacji na temat zmian kontroli dostępu opartej na rolach do zasobów platformy Azure, takich jak inspekcji lub rozwiązywania problemów. Ilekroć ktoś zmienia przypisania ról lub definicje ról w ramach subskrypcji, rejestrowane są zmiany [dziennika aktywności platformy Azure](../azure-monitor/platform/activity-logs-overview.md). Możesz wyświetlić dzienniki aktywności, aby wyświetlić wszystkie zmiany funkcji RBAC w ciągu ostatnich 90 dni.

## <a name="operations-that-are-logged"></a>Operacje, które są rejestrowane

Oto operacje związane z RBAC, które są rejestrowane w dzienniku aktywności:

- Utwórz przypisanie roli
- Usuń przypisanie roli
- Utwórz lub zaktualizuj niestandardową definicję roli
- Usuń niestandardową definicję roli

## <a name="azure-portal"></a>Azure Portal

Najprostszym sposobem na rozpoczęcie pracy jest wyświetlanie dzienników aktywności w witrynie Azure portal. Poniższy zrzut ekranu przedstawia przykład dziennika aktywności, która została przefiltrowana do wyświetlania przypisania roli i operacje definicji roli. Zawiera także łącza do pobierania dzienników do pliku CSV.

![Dzienniki aktywności przy użyciu portalu — zrzut ekranu](./media/change-history-report/activity-log-portal.png)

Dziennik aktywności w portalu ma kilka filtrów. W tym miejscu znajdują się filtry dotyczące kontroli RBAC:

|Filtr  |Wartość  |
|---------|---------|
|Kategoria zdarzenia     | <ul><li>Administracyjne</li></ul>         |
|Operacja     | <ul><li>Utwórz przypisanie roli</li> <li>Usuń przypisanie roli</li> <li>Utwórz lub zaktualizuj niestandardową definicję roli</li> <li>Usuń niestandardową definicję roli</li></ul>      |


Aby uzyskać więcej informacji na temat dzienników aktywności, zobacz [wyświetlać zdarzenia w dzienniku aktywności](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json).

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

Aby wyświetlić dzienniki aktywności przy użyciu programu Azure PowerShell, użyj [Get AzLog](/powershell/module/Az.Monitor/Get-AzLog) polecenia.

To polecenie wyświetla wszystkie zmiany przypisania roli w ramach subskrypcji w ciągu ostatnich siedmiu dni:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleAssignments/*'}
```

To polecenie wyświetla listę wszystkich zmian definicji roli w grupie zasobów w ciągu ostatnich siedmiu dni:

```azurepowershell
Get-AzLog -ResourceGroupName pharma-sales -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleDefinitions/*'}
```

To polecenie wyświetla wszystkie przypisania roli i zmiany definicji roli w ramach subskrypcji w ciągu ostatnich siedmiu dni i wyświetla wyniki w postaci listy:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/role*'} | Format-List Caller,EventTimestamp,{$_.Authorization.Action},Properties
```

```Example
Caller                  : alain@example.com
EventTimestamp          : 4/20/2018 9:18:07 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          statusCode     : Created
                          serviceRequestId: 11111111-1111-1111-1111-111111111111

Caller                  : alain@example.com
EventTimestamp          : 4/20/2018 9:18:05 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          requestbody    : {"Id":"22222222-2222-2222-2222-222222222222","Properties":{"PrincipalId":"33333333-3333-3333-3333-333333333333","RoleDefinitionId":"/subscriptions/00000000-0000-0000-0000-000000000000/providers
                          /Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","Scope":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"}}

```

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby wyświetlić dzienniki aktywności z wiersza polecenia platformy Azure, użyj [az monitor dziennika aktywności listy](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) polecenia.

To polecenie wyświetla listę dzienników aktywności w grupie zasobów, od czasu rozpoczęcia:

```azurecli
az monitor activity-log list --resource-group pharma-sales --start-time 2018-04-20T00:00:00Z
```

To polecenie wyświetla listę dzienników aktywności dla dostawcy zasobów autoryzacji od czasu rozpoczęcia:

```azurecli
az monitor activity-log list --resource-provider "Microsoft.Authorization" --start-time 2018-04-20T00:00:00Z
```

## <a name="azure-monitor-logs"></a>Dzienniki usługi Azure Monitor

[Dzienniki platformy Azure Monitor](../log-analytics/log-analytics-overview.md) to kolejne narzędzie, które umożliwia zbieranie i analizowanie zmian RBAC dla wszystkich zasobów platformy Azure. Dzienniki platformy Azure Monitor ma następujące zalety:

- Zapis złożonych kwerend i Logic Apps
- Integracja z alertów, usługa Power BI i innymi narzędziami
- Zapisz dane przez dłuższy czas przechowywania
- Uzyskane z innych dzienników, takie jak zabezpieczenia, maszyny wirtualnej i niestandardowe

Poniżej przedstawiono podstawowe kroki, aby rozpocząć pracę:

1. [Utwórz obszar roboczy usługi Log Analytics](../azure-monitor/learn/quick-create-workspace.md).

1. [Skonfiguruj rozwiązanie Activity Log Analytics](../azure-monitor/platform/collect-activity-logs.md#configuration) dla obszaru roboczego.

1. [Wyświetlanie dzienników aktywności](../azure-monitor/platform/collect-activity-logs.md#using-the-solution). Szybko przejdź do strony Przegląd rozwiązanie Activity Log Analytics jest kliknięcie **usługi Log Analytics** opcji.

   ![Usługa Azure Monitor dzienniki opcji w portalu](./media/change-history-report/azure-log-analytics-option.png)

1. Opcjonalnie użyć [wyszukiwanie w dzienniku](../log-analytics/log-analytics-log-search.md) strony lub [portalu analizy zaawansowanej](../azure-monitor/log-query/get-started-portal.md) zapytania i wyświetlić dzienniki. Aby uzyskać więcej informacji na temat tych dwóch opcji, zobacz [strony wyszukiwanie w dziennikach lub portalu analizy zaawansowanej](../azure-monitor/log-query/portals.md).

Oto zapytanie zwracające nowego przypisania roli organizowane przez dostawcę zasobów docelowych:

```
AzureActivity
| where TimeGenerated > ago(60d) and OperationName startswith "Microsoft.Authorization/roleAssignments/write" and ActivityStatus == "Succeeded"
| parse ResourceId with * "/providers/" TargetResourceAuthProvider "/" *
| summarize count(), makeset(Caller) by TargetResourceAuthProvider
```

Oto zapytanie zwracające wyświetlane na wykresie zmiany przypisania roli:

```
AzureActivity
| where TimeGenerated > ago(60d) and OperationName startswith "Microsoft.Authorization/roleAssignments"
| summarize count() by bin(TimeGenerated, 1d), OperationName
| render timechart
```

![Dzienniki aktywności przy użyciu portalu analizy zaawansowanej — zrzut ekranu](./media/change-history-report/azure-log-analytics.png)

## <a name="next-steps"></a>Kolejne kroki
* [Wyświetlanie zdarzeń w dzienniku aktywności](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)
* [Monitorowanie aktywności subskrypcji z dziennika aktywności platformy Azure](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)
