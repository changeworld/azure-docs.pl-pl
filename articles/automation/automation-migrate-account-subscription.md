---
title: Migrowanie kont automatyzacji i zasobów
description: W tym artykule opisano, jak przenieść konto usługi Automatyzacja w automatyzacji Azure oraz zasoby skojarzone z jedną subskrypcją.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: d417f26eb9bad47b9742921156ff232e65ffd5aa
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
---
# <a name="migrate-automation-account-and-resources"></a>Migrowanie kont automatyzacji i zasobów
Dla kont automatyzacji i jej zasobach skojarzone (to znaczy zasoby, elementy runbook, moduły itp.), które zostały utworzone w portalu Azure i chcesz przeprowadzić migrację między grupami zasobów lub między jedną subskrypcję do innej można wykonać to prosty sposób z [przenoszenia zasobów](../azure-resource-manager/resource-group-move-resources.md) funkcji dostępnej w portalu Azure. Jednak przed wykonaniem tej akcji, należy najpierw przejrzeć następujące [listę kontrolną przed przeniesieniem zasobów](../azure-resource-manager/resource-group-move-resources.md#checklist-before-moving-resources) , a ponadto poniżej dotyczą automatyzacji.  

1. Docelowej subskrypcji/grupy zasobów musi być w tym samym regionie co źródło. To znaczy, konta automatyzacji nie można przenieść w regionach.
2. Podczas przenoszenia zasobów (na przykład elementów runbook, zadania, itp.), zarówno grupy źródłowej i docelowej grupy są zablokowane na czas trwania operacji. Zapisu i usuwania działań są blokowane w grupach, dopiero po zakończeniu przenoszenia. 
3. Wszystkie elementy runbook lub zmienne, które odwołują się do Identyfikatora zasobu lub subskrypcji z istniejącej subskrypcji, należy uaktualnić po zakończeniu migracji.  

> [!NOTE]
> Ta funkcja nie obsługuje przenoszenia zasobów automatyzacji klasycznego.
>
>

## <a name="to-move-the-automation-account-using-the-portal"></a>Aby przenieść przy użyciu portalu konta automatyzacji
1. Twoje konto usługi Automatyzacja kliknij **Przenieś** w górnej części strony.<br> ![Przenieś opcji](media/automation-migrate-account-subscription/automation-menu-move.png)<br>
2. Wybierz, aby przenieść do innej grupy zasobów lub inną subskrypcję konta automatyzacji.
3. Na **przenoszenia zasobów** okienka, powiadomienia stanowi zasobów związanych z zarówno konto automatyzacji, jak i z grup zasobów. Wybierz **subskrypcji** i **grupy zasobów** z listy rozwijanej lub wybierz opcję **Utwórz nową grupę zasobów** , a następnie wprowadź nazwę nowej grupy zasobów w polu. 
4. Przejrzyj i zaznacz pole wyboru, musisz potwierdzić *zrozumieć narzędzia i skrypty musi zostać zaktualizowany w celu użycia nowych identyfikatorów zasobów. po przeniesieniu zasobów* , a następnie kliknij przycisk **OK**.<br> ![Przenoszenie zasobów okienka](media/automation-migrate-account-subscription/automation-move-resources-blade.png)<br>   

Ta akcja może potrwać kilka minut. W **powiadomienia**, jest wyświetlana stan każdej akcji, która ma miejsce — Weryfikacja, migracji, a następnie finally, gdy zostanie ukończona.    

## <a name="to-move-the-automation-account-using-powershell"></a>Aby przenieść konto automatyzacji za pomocą programu PowerShell
Aby przenieść istniejące zasoby automatyzacji do innej grupy zasobów lub subskrypcji, należy użyć **Get-AzureRmResource** polecenia cmdlet można pobrać określonego konta automatyzacji, a następnie **AzureRmResource przenoszenia** polecenia cmdlet do wykonania czynności.

Pierwszym przykładzie pokazano, jak można przenieść konto automatyzacji do nowej grupy zasobów.

   ```
    $resource = Get-AzureRmResource -ResourceName "TestAutomationAccount" -ResourceGroupName "ResourceGroup01"
    Move-AzureRmResource -ResourceId $resource.ResourceId -DestinationResourceGroupName "NewResourceGroup"
   ```

Po wykonaniu powyższych przykładów kodu, zostanie wyświetlony monit, czy chcesz wykonać tę akcję. Po kliknięciu **tak** i umożliwiają skryptu kontynuować, nie otrzymasz żadnych powiadomień podczas wykonywania migracji. 

Aby przejść do nowej subskrypcji, zawierają wartość *DestinationSubscriptionId* parametru.

   ```
    $resource = Get-AzureRmResource -ResourceName "TestAutomationAccount" -ResourceGroupName "ResourceGroup01"
    Move-AzureRmResource -ResourceId $resource.ResourceId -DestinationResourceGroupName "NewResourceGroup" -DestinationSubscriptionId "SubscriptionId"
   ```

Podobnie jak w poprzednim przykładzie, wyświetlany jest monit o potwierdzenie przeniesienie. 

## <a name="next-steps"></a>Kolejne kroki
* Aby uzyskać więcej informacji na temat przenoszenia zasobów do nowej grupy zasobów lub subskrypcji, zobacz [przenoszenia zasobów do nowej grupy zasobów lub subskrypcji](../azure-resource-manager/resource-group-move-resources.md)
* Aby uzyskać więcej informacji na temat opartej na rolach kontroli dostępu w usłudze Automatyzacja Azure, zobacz [kontroli dostępu opartej na rolach w automatyzacji Azure](automation-role-based-access-control.md).
* Aby dowiedzieć się więcej na temat poleceń cmdlet programu PowerShell do zarządzania subskrypcją, zobacz [przy użyciu programu Azure PowerShell z usługą Resource Manager](../azure-resource-manager/powershell-azure-resource-manager.md)
* Aby dowiedzieć się więcej o funkcjach portalu do zarządzania subskrypcją, zobacz [przy użyciu portalu Azure do zarządzania zasobami](../azure-resource-manager/resource-group-portal.md).
