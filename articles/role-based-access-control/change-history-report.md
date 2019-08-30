---
title: Wyświetlanie dzienników aktywności związanych ze zmianami RBAC w zasobach platformy Azure | Microsoft Docs
description: Wyświetlanie dzienników aktywności dla zmian kontroli dostępu opartej na rolach (RBAC) dla zasobów platformy Azure w ciągu ostatnich 90 dni.
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
ms.openlocfilehash: e5758f480c9216cf71e47509682053b39f0b15bf
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70172414"
---
# <a name="view-activity-logs-for-rbac-changes-to-azure-resources"></a>Wyświetlanie dzienników aktywności związanych ze zmianami RBAC w zasobach platformy Azure

Czasami potrzebujesz informacji o zmianach kontroli dostępu opartej na rolach (RBAC) w zasobach platformy Azure, takich jak Inspekcja i rozwiązywanie problemów. Za każdym razem, gdy ktoś wprowadza zmiany w przypisaniach ról lub definicjach ról w ramach subskrypcji, zmiany zostaną zarejestrowane w [dzienniku aktywności platformy Azure](../azure-monitor/platform/activity-logs-overview.md). Można wyświetlić dzienniki aktywności, aby zobaczyć wszystkie zmiany RBAC w ciągu ostatnich 90 dni.

## <a name="operations-that-are-logged"></a>Zarejestrowane operacje

Poniżej znajdują się operacje związane z RBAC, które są rejestrowane w dzienniku aktywności:

- Utwórz przypisanie roli
- Usuń przypisanie roli
- Utwórz lub zaktualizuj definicję roli niestandardowej
- Usuń niestandardową definicję roli

## <a name="azure-portal"></a>Azure Portal

Najprostszym sposobem na rozpoczęcie pracy jest wyświetlenie dzienników aktywności z Azure Portal. Poniższy zrzut ekranu przedstawia przykład dziennika aktywności, który został przefiltrowany w celu wyświetlenia operacji przypisania roli i definicji roli. Zawiera również link umożliwiający pobranie dzienników jako plik CSV.

![Dzienniki aktywności przy użyciu portalu — zrzut ekranu](./media/change-history-report/activity-log-portal.png)

Dziennik aktywności w portalu ma kilka filtrów. Oto filtry dotyczące kontroli RBAC:

|Filtr  |Value  |
|---------|---------|
|Kategoria zdarzenia     | <ul><li>Administracyjne</li></ul>         |
|Operacja     | <ul><li>Utwórz przypisanie roli</li> <li>Usuń przypisanie roli</li> <li>Utwórz lub zaktualizuj definicję roli niestandardowej</li> <li>Usuń niestandardową definicję roli</li></ul>      |


Aby uzyskać więcej informacji na temat dzienników aktywności, zobacz [Wyświetlanie zdarzeń w dzienniku aktywności](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json).

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

Aby wyświetlić dzienniki aktywności z Azure PowerShell, użyj polecenia [Get-AzLog](/powershell/module/Az.Monitor/Get-AzLog) .

To polecenie wyświetla listę wszystkich zmian przypisań ról w ramach subskrypcji przez ostatnie siedem dni:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleAssignments/*'}
```

To polecenie wyświetla listę wszystkich zmian definicji ról w grupie zasobów przez ostatnie siedem dni:

```azurepowershell
Get-AzLog -ResourceGroupName pharma-sales -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleDefinitions/*'}
```

To polecenie wyświetla listę wszystkich zmian przypisań ról i definicji ról w ramach subskrypcji przez ostatnie siedem dni, a następnie wyświetla wyniki na liście:

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

Aby wyświetlić dzienniki aktywności przy użyciu interfejsu wiersza polecenia platformy Azure, użyj polecenie [AZ monitor Activity-Log list](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) .

To polecenie wyświetla listę dzienników aktywności w grupie zasobów od momentu rozpoczęcia:

```azurecli
az monitor activity-log list --resource-group pharma-sales --start-time 2018-04-20T00:00:00Z
```

To polecenie wyświetla listę dzienników aktywności dla dostawcy zasobów autoryzacji od momentu rozpoczęcia:

```azurecli
az monitor activity-log list --resource-provider "Microsoft.Authorization" --start-time 2018-04-20T00:00:00Z
```

## <a name="azure-monitor-logs"></a>Dzienniki usługi Azure Monitor

[Dzienniki Azure monitor](../log-analytics/log-analytics-overview.md) to inne narzędzie, za pomocą którego można zbierać i analizować zmiany RBAC dla wszystkich zasobów platformy Azure. Azure Monitor dzienników ma następujące zalety:

- Napisz złożone zapytania i logikę
- Integracja z alertami, Power BI i innymi narzędziami
- Zapisywanie danych w celu uzyskania dłuższych okresów przechowywania
- Odsyłacze z innymi dziennikami, takimi jak zabezpieczenia, maszyna wirtualna i niestandardowe

Oto podstawowe kroki, które należy wykonać, aby rozpocząć pracę:

1. [Utwórz obszar roboczy log Analytics](../azure-monitor/learn/quick-create-workspace.md).

1. [Skonfiguruj rozwiązanie Activity Log Analytics](../azure-monitor/platform/activity-log-collect.md#activity-logs-analytics-monitoring-solution) dla obszaru roboczego.

1. [Wyświetlanie dzienników aktywności](../azure-monitor/platform/activity-log-collect.md#activity-logs-analytics-monitoring-solution). Aby szybko przejść do strony przeglądu Activity Log Analytics rozwiązania, kliknij opcję **log Analytics** .

   ![Opcja dzienników Azure Monitor w portalu](./media/change-history-report/azure-log-analytics-option.png)

1. Opcjonalnie możesz użyć strony przeszukiwania [dzienników](../log-analytics/log-analytics-log-search.md) lub [portalu analizy zaawansowanej](../azure-monitor/log-query/get-started-portal.md) do wykonywania zapytań i wyświetlania dzienników. Aby uzyskać więcej informacji na temat tych dwóch opcji, zobacz [stronę przeszukiwanie dzienników lub portal analizy zaawansowanej](../azure-monitor/log-query/portals.md).

Oto zapytanie zwracające nowe przypisania ról uporządkowane według docelowego dostawcy zasobów:

```
AzureActivity
| where TimeGenerated > ago(60d) and OperationNameValue startswith "Microsoft.Authorization/roleAssignments/write" and ActivityStatus == "Succeeded"
| parse ResourceId with * "/providers/" TargetResourceAuthProvider "/" *
| summarize count(), makeset(Caller) by TargetResourceAuthProvider
```

Oto zapytanie, które zwraca zmiany przypisania roli wyświetlane na wykresie:

```
AzureActivity
| where TimeGenerated > ago(60d) and OperationNameValue startswith "Microsoft.Authorization/roleAssignments"
| summarize count() by bin(TimeGenerated, 1d), OperationNameValue
| render timechart
```

![Dzienniki aktywności przy użyciu portalu analizy zaawansowanej — zrzut ekranu](./media/change-history-report/azure-log-analytics.png)

## <a name="next-steps"></a>Następne kroki
* [Wyświetlanie zdarzeń w dzienniku aktywności](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)
* [Monitorowanie aktywności subskrypcji za pomocą dziennika aktywności platformy Azure](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)
