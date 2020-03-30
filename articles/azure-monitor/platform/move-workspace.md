---
title: Przenoszenie obszaru roboczego usługi Log Analytics w usłudze Azure Monitor | Dokumenty firmy Microsoft
description: Dowiedz się, jak przenieść obszar roboczy usługi Log Analytics do innej subskrypcji lub grupy zasobów.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/13/2019
ms.openlocfilehash: 9213ddf034e725f6e31c9280d47bd13e4703b3f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659496"
---
# <a name="move-a-log-analytics-workspace-to-different-subscription-or-resource-group"></a>Przenoszenie obszaru roboczego usługi Log Analytics do innej subskrypcji lub grupy zasobów

W tym artykule dowiesz się, jak przenieść obszar roboczy usługi Log Analytics do innej grupy zasobów lub subskrypcji w tym samym regionie. Możesz dowiedzieć się więcej o przenoszeniu zasobów platformy Azure za pośrednictwem witryny Azure portal, powershell, interfejsu wiersza polecenia platformy Azure lub interfejsu API REST. w [sekcji Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../../azure-resource-manager/management/move-resource-group-and-subscription.md). 

> [!IMPORTANT]
> Nie można przenieść obszaru roboczego do innego regionu.

## <a name="verify-active-directory-tenant"></a>Weryfikowanie dzierżawy usługi Active Directory
Subskrypcje źródłowe i docelowe obszaru roboczego muszą istnieć w tej samej dzierżawie usługi Azure Active Directory. Użyj programu Azure PowerShell, aby sprawdzić, czy obie subskrypcje mają ten sam identyfikator dzierżawy.

``` PowerShell
(Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
(Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
```

## <a name="workspace-move-considerations"></a>Zagadnienia dotyczące przenoszenia obszaru roboczego
Rozwiązania zarządzane, które są zainstalowane w obszarze roboczym zostaną przeniesione z operacji przenoszenia obszaru roboczego usługi Log Analytics. Połączeni agenci pozostaną połączeni i zachowają dane do obszaru roboczego po przeprowadzce. Ponieważ operacja przenoszenia wymaga, aby nie było łącza z obszaru roboczego do dowolnego konta automatyzacji, rozwiązania, które opierają się na tym łączu, muszą zostać usunięte.

Rozwiązania, które należy usunąć, aby można było odłączyć konto automatyzacji:

- Zarządzanie aktualizacjami
- Śledzenie zmian
- Uruchamianie lub zatrzymywanie maszyn wirtualnych po godzinach pracy


### <a name="delete-in-azure-portal"></a>Usuwanie w witrynie Azure Portal
Poniższa procedura służy do usuwania rozwiązań przy użyciu witryny Azure portal:

1. Otwórz menu dla grupy zasobów, w których są zainstalowane wszystkie rozwiązania.
2. Wybierz rozwiązania do usunięcia.
3. Kliknij **pozycję Usuń zasoby,** a następnie potwierdź zasoby do usunięcia, klikając pozycję **Usuń**.

![Usuwanie rozwiązań](media/move-workspace/delete-solutions.png)

### <a name="delete-using-powershell"></a>Usuwanie przy użyciu programu PowerShell

Aby usunąć rozwiązania przy użyciu programu PowerShell, należy użyć polecenia cmdlet [Remove-AzResource,](/powershell/module/az.resources/remove-azresource?view=azps-2.8.0) jak pokazano w poniższym przykładzie:

``` PowerShell
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM(<workspace-name>)" -ResourceGroupName <resource-group-name>
```

### <a name="remove-alert-rules"></a>Usuwanie reguł alertów
Dla rozwiązania **Start/Stop maszyn wirtualnych,** należy również usunąć reguły alertów utworzone przez rozwiązanie. Aby usunąć te reguły, użyj poniższej procedury w witrynie Azure portal.

1. Otwórz menu **Monitor,** a następnie wybierz pozycję **Alerty**.
2. Kliknij **pozycję Zarządzaj regułami alertów**.
3. Wybierz następujące trzy reguły alertów, a następnie kliknij przycisk **Usuń**.

   - AutoStop_VM_Child
   - ScheduledStartStop_Parent
   - SequencedStartStop_Parent

    ![Usuń reguły](media/move-workspace/delete-rules.png)

## <a name="unlink-automation-account"></a>Odłącz konto automatyzacji
Poniższa procedura służy do odłączenia konta automatyzacji od obszaru roboczego przy użyciu witryny Azure portal:

1. Otwórz menu **Konta automatyzacji,** a następnie wybierz konto do usunięcia.
2. W sekcji **Zasoby pokrewne** w menu wybierz pozycję **Połączony obszar roboczy**. 
3. Kliknij **pozycję Odłącz obszar roboczy,** aby odłączyć obszar roboczy od konta automatyzacji.

    ![Unlink workspace (Odłączanie obszaru roboczego)](media/move-workspace/unlink-workspace.png)

## <a name="move-your-workspace"></a>Przenoszenie obszaru roboczego

### <a name="azure-portal"></a>Portal Azure
Aby przenieść obszar roboczy za pomocą witryny Azure Portal, użyj następującej procedury:

1. Otwórz menu **Obszarów roboczych usługi Log Analytics,** a następnie wybierz obszar roboczy.
2. Na stronie **Przegląd** kliknij pozycję **Zmień** obok **grupy zasobów** lub **subskrypcji**.
3. Zostanie otwarta nowa strona z listą zasobów związanych z obszarem roboczym. Wybierz zasoby, które mają być przeniesione do tej samej docelowej subskrypcji i grupy zasobów co obszar roboczy. 
4. Wybierz docelową **grupę** **Subskrypcji** i Zasobów . Jeśli przenosisz obszar roboczy do innej grupy zasobów w tej samej subskrypcji, nie zobaczysz opcji **Subskrypcja.**
5. Kliknij **przycisk OK,** aby przenieść obszar roboczy i wybrane zasoby.

    ![Portal](media/move-workspace/portal.png)

### <a name="powershell"></a>PowerShell
Aby przenieść obszar roboczy przy użyciu programu PowerShell, użyj [move-AzResource,](/powershell/module/AzureRM.Resources/Move-AzureRmResource) jak w poniższym przykładzie:

``` PowerShell
Move-AzResource -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyResourceGroup01/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace" -DestinationSubscriptionId "00000000-0000-0000-0000-000000000000" -DestinationResourceGroupName "MyResourceGroup02"
```



> [!IMPORTANT]
> Po operacji przenoszenia usunięte rozwiązania i łącze konta automatyzacji powinny zostać ponownie skonfigurowane, aby przenieść obszar roboczy z powrotem do poprzedniego stanu.


## <a name="next-steps"></a>Następne kroki
- Aby uzyskać listę zasobów, które obsługują przenoszenie zasobów, zobacz [Przenoszenie obsługi operacji dla zasobów](../../azure-resource-manager/management/move-support-resources.md).
