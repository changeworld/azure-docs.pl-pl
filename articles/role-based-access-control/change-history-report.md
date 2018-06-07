---
title: Wyświetl dzienniki aktywności RBAC zmian na platformie Azure | Dokumentacja firmy Microsoft
description: Wyświetl dzienniki aktywności zmiany kontroli dostępu opartej na rolach w ciągu ostatnich 90 dni.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 2bc68595-145e-4de3-8b71-3a21890d13d9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/23/2018
ms.author: rolyon
ms.reviewer: rqureshi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 737f8d49d8254806891d3177cde59187cd8e1f2e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34640187"
---
# <a name="view-activity-logs-for-role-based-access-control-changes"></a>Wyświetl dzienniki aktywności zmiany kontroli dostępu opartej na rolach

Czasami potrzebny informacji na temat zmiany kontroli dostępu opartej na rolach, takich jak dla inspekcji lub rozwiązywania problemów. Ilekroć ktoś zmienia przypisania roli lub definicje ról w ramach subskrypcji, rejestrowane są zmiany [dziennika aktywności platformy Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md). Można wyświetlać dzienniki aktywności, aby wyświetlić wszystkie zmiany RBAC w ciągu ostatnich 90 dni.

## <a name="operations-that-are-logged"></a>Operacje związane z unikatowymi

Poniżej przedstawiono operacji związanych z RBAC, które są rejestrowane w dzienniku aktywności:

- Utwórz przypisanie roli
- Usuń przypisanie roli
- Utwórz lub zaktualizuj niestandardową definicję roli
- Usuń niestandardową definicję roli

## <a name="azure-portal"></a>Azure Portal

Najłatwiejszym sposobem na rozpoczęcie pracy jest aby wyświetlić dzienniki aktywności w portalu Azure. Poniższy zrzut ekranu przedstawia przykład dziennik aktywności, która została przefiltrowana do wyświetlenia operacji definicji roli i przypisanie roli. Zawiera również link do pobierania dzienników jako plik CSV.

![Dzienniki aktywności za pomocą portalu — zrzut ekranu](./media/change-history-report/activity-log-portal.png)

Dziennik aktywności w portalu ma kilka filtrów. Poniżej przedstawiono filtry powiązane RBAC:

|Filtr  |Wartość  |
|---------|---------|
|Kategoria zdarzenia     | <ul><li>Administracyjne</li></ul>         |
|Operacja     | <ul><li>Utwórz przypisanie roli</li> <li>Usuń przypisanie roli</li> <li>Utwórz lub zaktualizuj niestandardową definicję roli</li> <li>Usuń niestandardową definicję roli</li></ul>      |


Aby uzyskać więcej informacji o Dzienniki aktywności, zobacz [wyświetlać zdarzenia w dzienniku aktywności](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json).

## <a name="azure-powershell"></a>Azure PowerShell

Aby wyświetlić dzienniki aktywności przy użyciu programu Azure PowerShell, użyj [Get-AzureRmLog](/powershell/module/azurerm.insights/get-azurermlog) polecenia.

To polecenie wyświetla listę wszystkich zmian w przypisaniu roli w ramach subskrypcji w ciągu ostatnich siedmiu dni:

```azurepowershell
Get-AzureRmLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleAssignments/*'}
```

To polecenie wyświetla listę wszystkich zmian definicji roli w grupie zasobów w ciągu ostatnich siedmiu dni:

```azurepowershell
Get-AzureRmLog -ResourceGroupName pharma-sales-projectforecast -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleDefinitions/*'}
```

To polecenie wyświetla listę wszystkich przypisania roli i zmiany definicji roli w ramach subskrypcji w ciągu ostatnich siedmiu dni i wyświetla wyniki w postaci listy:

```azurepowershell
Get-AzureRmLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/role*'} | Format-List Caller,EventTimestamp,{$_.Authorization.Action},Properties
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
                          /Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","Scope":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales-projectforecast"}}

```

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby wyświetlić dzienniki aktywności z wiersza polecenia platformy Azure, użyj [listy dziennik aktywności monitora az](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) polecenia.

To polecenie wyświetla Dzienniki aktywności w grupie zasobów od czasu rozpoczęcia:

```azurecli
az monitor activity-log list --resource-group pharma-sales-projectforecast --start-time 2018-04-20T00:00:00Z
```

To polecenie wyświetla Dzienniki aktywności dla dostawcy zasobów autoryzacji od czasu rozpoczęcia:

```azurecli
az monitor activity-log list --resource-provider "Microsoft.Authorization" --start-time 2018-04-20T00:00:00Z
```

## <a name="azure-log-analytics"></a>Azure Log Analytics

[Analiza dzienników Azure](../log-analytics/log-analytics-overview.md) jest inne narzędzie służy do zbierania i analizowania zmian RBAC dla wszystkich zasobów na platformie Azure. Analiza dzienników ma następujące zalety:

- Pisanie logiki i złożonych zapytań
- Integracja z alertów, usługa Power BI i inne narzędzia
- Zapisz dane przez dłuższy czas przechowywania
- Wstawienia odsyłacza z innych dzienniki zabezpieczeń, maszyny wirtualnej i niestandardowe

Poniżej przedstawiono podstawowe kroki, aby rozpocząć:

1. [Tworzenie obszaru roboczego analizy dzienników](../log-analytics/log-analytics-quick-create-workspace.md).

1. [Konfigurowanie rozwiązania analizy dzienników działania](../log-analytics/log-analytics-activity.md#configuration) obszaru roboczego.

1. [Wyświetl dzienniki aktywności](../log-analytics/log-analytics-activity.md#using-the-solution). Szybko przejść do strony Przegląd analizy dziennika aktywności jest kliknięcie **analizy dzienników** opcji.

   ![Opcja analizy dziennika w portalu](./media/change-history-report/azure-log-analytics-option.png)

1. Opcjonalnie użyj [wyszukiwania dziennika](../log-analytics/log-analytics-log-search.md) strony lub [portal analityka zaawansowane](https://docs.loganalytics.io/docs/Learn) zapytań, sprawdź dzienniki. Aby uzyskać więcej informacji na temat tych dwóch opcji, zobacz [strony wyszukiwania dziennika lub w portalu analityka zaawansowane](../log-analytics/log-analytics-log-search-portals.md).

Oto zapytanie zwracające nowych przypisań ról są zorganizowane według dostawcy zasobów docelowych:

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

![Dzienniki aktywności za pomocą portalu analityka zaawansowane — zrzut ekranu](./media/change-history-report/azure-log-analytics.png)

## <a name="next-steps"></a>Kolejne kroki
* [Wyświetlanie zdarzeń w dzienniku aktywności](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)
* [Monitorowanie aktywności subskrypcji z dziennika aktywności platformy Azure](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)
