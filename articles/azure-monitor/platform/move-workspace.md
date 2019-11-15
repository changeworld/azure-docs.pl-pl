---
title: Przenoszenie obszaru roboczego Log Analytics w Azure Monitor | Microsoft Docs
description: Dowiedz się, jak przenieść obszar roboczy Log Analytics do innej subskrypcji lub grupy zasobów.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/13/2019
ms.openlocfilehash: fd7ff7aa2275defba57aa24b5ef0b9adc78a5355
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74093789"
---
# <a name="move-a-log-analytics-workspace-to-different-subscription-or-resource-group"></a>Przenoszenie obszaru roboczego Log Analytics do innej subskrypcji lub grupy zasobów

W tym artykule omówiono procedurę przenoszenia obszaru roboczego Log Analytics do innej grupy zasobów lub subskrypcji w tym samym regionie. Aby dowiedzieć się więcej o przenoszeniu zasobów platformy Azure za pomocą Azure Portal, programu PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API REST. [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../../azure-resource-manager/resource-group-move-resources.md). 

> [!IMPORTANT]
> Nie można przenieść obszaru roboczego do innego regionu.

## <a name="verify-active-directory-tenant"></a>Weryfikowanie Active Directory dzierżawy
W ramach tej samej dzierżawy Azure Active Directory musi istnieć źródłowa i docelowa subskrypcja obszaru roboczego. Użyj Azure PowerShell, aby sprawdzić, czy obie subskrypcje mają ten sam identyfikator dzierżawy.

``` PowerShell
(Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
(Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
```

## <a name="remove-solutions"></a>Usuwanie rozwiązań
Rozwiązania zarządzane, które są zainstalowane w obszarze roboczym, zostaną przeniesione z Log Analytics operacji przenoszenia obszaru roboczego. Ponieważ należy usunąć łącze z obszaru roboczego do dowolnego konta usługi Automation, należy usunąć rozwiązania korzystające z tego linku.

Rozwiązania, które należy usunąć, obejmują następujące elementy: 

- Zarządzanie aktualizacjami
- Śledzenie zmian
- Uruchamianie lub zatrzymywanie maszyn wirtualnych po godzinach pracy


### <a name="azure-portal"></a>Azure Portal
Aby usunąć rozwiązania przy użyciu Azure Portal, należy wykonać czynności opisane w poniższej procedurze:

1. Otwórz menu dla grupy zasobów, w której zainstalowano wszystkie rozwiązania.
2. Wybierz rozwiązania do usunięcia.
3. Kliknij pozycję **Usuń zasoby** , a następnie potwierdź zasoby, które mają zostać usunięte, klikając przycisk **Usuń**.

![Usuwanie rozwiązań](media/move-workspace/delete-solutions.png)

### <a name="powershell"></a>PowerShell

Aby usunąć rozwiązania przy użyciu programu PowerShell, użyj polecenia cmdlet [Remove-AzResource](/powershell/module/az.resources/remove-azresource?view=azps-2.8.0) , jak pokazano w następującym przykładzie:

``` PowerShell
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM(<workspace-name>)" -ResourceGroupName <resource-group-name>
```

## <a name="remove-alert-rules"></a>Usuń reguły alertów
W przypadku rozwiązania do **uruchamiania/zatrzymywania maszyn wirtualnych** należy również usunąć reguły alertów utworzone przez rozwiązanie. Aby usunąć te reguły, należy wykonać poniższą procedurę w Azure Portal.

1. Otwórz menu **monitor** , a następnie wybierz pozycję **alerty**.
2. Kliknij pozycję **Zarządzaj regułami alertów**.
3. Wybierz następujące trzy reguły alertów, a następnie kliknij przycisk **Usuń**.

   - AutoStop_VM_Child
   - ScheduledStartStop_Parent
   - SequencedStartStop_Parent

    ![Usuwanie reguł](media/move-workspace/delete-rules.png)

## <a name="unlink-automation-account"></a>Odłącz konto usługi Automation
Aby odłączyć konto usługi Automation od obszaru roboczego przy użyciu Azure Portal, wykonaj czynności opisane w poniższej procedurze:

1. Otwórz menu **konta usługi Automation** , a następnie wybierz konto, które chcesz usunąć.
2. W sekcji **powiązane zasoby** w menu wybierz pozycję **połączony obszar roboczy**. 
3. Kliknij przycisk **Odłącz obszar roboczy** , aby odłączyć obszar roboczy od konta usługi Automation.

    ![Odłącz obszar roboczy](media/move-workspace/unlink-workspace.png)

## <a name="move-your-workspace"></a>Przenoszenie obszaru roboczego

### <a name="azure-portal"></a>Azure Portal
Aby przenieść obszar roboczy przy użyciu Azure Portal, wykonaj czynności opisane w poniższej procedurze:

1. Otwórz menu **log Analytics obszary robocze** , a następnie wybierz obszar roboczy.
2. Na stronie **Przegląd** kliknij pozycję **Zmień** obok **grupy zasobów** lub **subskrypcji**.
3. Zostanie otwarta nowa strona z listą zasobów związanych z obszarem roboczym. Wybierz zasoby, które mają zostać przeniesione do tej samej docelowej subskrypcji i grupy zasobów, co obszar roboczy. 
4. Wybierz docelową **subskrypcję** i **grupę zasobów**. Jeśli przenosisz obszar roboczy do innej grupy zasobów w ramach tej samej subskrypcji, opcja **subskrypcji** nie zostanie wyświetlona.
5. Kliknij przycisk **OK** , aby przenieść obszar roboczy i wybrane zasoby.

    ![Portal](media/move-workspace/portal.png)

### <a name="powershell"></a>PowerShell
Aby przenieść obszar roboczy przy użyciu programu PowerShell, użyj polecenia [Move-AzResource](/powershell/module/AzureRM.Resources/Move-AzureRmResource) jak w poniższym przykładzie:

``` PowerShell
Move-AzResource -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyResourceGroup01/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace" -DestinationSubscriptionId "00000000-0000-0000-0000-000000000000" -DestinationResourceGroupName "MyResourceGroup02"
```



> [!IMPORTANT]
> Po zakończeniu operacji przenoszenia, usunięte rozwiązania i link do konta usługi Automation należy zmienić konfigurację, aby przywrócić obszar roboczy z powrotem do poprzedniego stanu.


## <a name="next-steps"></a>Następne kroki
- Aby zapoznać się z listą obsługiwanych zasobów, zobacz [przenoszenie obsługi zasobów](../../azure-resource-manager/move-support-resources.md).
