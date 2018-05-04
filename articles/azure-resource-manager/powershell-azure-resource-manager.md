---
title: Zarządzaj rozwiązaniami Azure przy użyciu programu PowerShell | Dokumentacja firmy Microsoft
description: Użyj programu Azure PowerShell i Menedżera zasobów do zarządzania zasobami.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: b33b7303-3330-4af8-8329-c80ac7e9bc7f
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: powershell
ms.devlang: na
ms.topic: article
ms.date: 02/16/2018
ms.author: tomfitz
ms.openlocfilehash: 7a3dcbfe09d47388b80cee15ff0e46f8b75b474a
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="manage-resources-with-azure-powershell"></a>Zarządzanie zasobami za pomocą programu Azure PowerShell

[!INCLUDE [Resource Manager governance introduction](../../includes/resource-manager-governance-intro.md)]

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Jeśli użytkownik chce zainstalować i lokalnie przy użyciu programu PowerShell, zobacz [modułu instalacji programu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzureRmAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="understand-scope"></a>Zrozumieć zakres

[!INCLUDE [Resource Manager governance scope](../../includes/resource-manager-governance-scope.md)]

W tym artykule zastosowanie wszystkich ustawień zarządzania do grupy zasobów, można łatwo usunąć te ustawienia po zakończeniu.

Teraz Utwórz grupę zasobów.

```azurepowershell-interactive
Set-AzureRmContext -Subscription <subscription-name>
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

Grupa zasobów jest obecnie pusta.

## <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach

[!INCLUDE [Resource Manager governance policy](../../includes/resource-manager-governance-rbac.md)]

### <a name="assign-a-role"></a>Przypisywanie roli

W tym artykule możesz wdrożyć maszyny wirtualnej i jej powiązane sieci wirtualnej. Do zarządzania rozwiązań maszyny wirtualnej, istnieją trzy role określonych zasobów, które zapewniają dostęp do potrzebnych:

* [Współautor maszyny wirtualnej](../role-based-access-control/built-in-roles.md#virtual-machine-contributor)
* [Współautor sieci](../role-based-access-control/built-in-roles.md#network-contributor)
* [Współautor konta magazynu](../role-based-access-control/built-in-roles.md#storage-account-contributor)

Zamiast przypisywać role poszczególnym użytkownikom, łatwiej często [utworzyć grupy usługi Azure Active Directory](../active-directory/active-directory-groups-create-azure-portal.md) dla użytkowników, którzy potrzebują podjęcia działań podobne. Następnie przypisać do odpowiedniej roli. Aby uprościć w tym artykule, należy utworzyć grupy usługi Azure Active Directory bez członków. Nadal można przypisać tej grupy do roli dla zakresu. 

Poniższy przykład tworzy grupę i przypisuje go do roli współautora maszyny wirtualnej dla grupy zasobów. Aby uruchomić `New-AzureAdGroup` polecenia należy używać [powłoki chmury Azure](/azure/cloud-shell/overview) lub [Pobierz moduł Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/).

```azurepowershell-interactive
$adgroup = New-AzureADGroup -DisplayName VMDemoContributors `
  -MailNickName vmDemoGroup `
  -MailEnabled $false `
  -SecurityEnabled $true
New-AzureRmRoleAssignment -ObjectId $adgroup.ObjectId `
  -ResourceGroupName myResourceGroup `
  -RoleDefinitionName "Virtual Machine Contributor"
```

Zwykle, powtórz proces **współautora sieci** i **współautora konta magazynu** się upewnić, że użytkownicy są dodawani do zarządzania zasobami wdrożone. W tym artykule można pominąć te kroki.

## <a name="azure-policies"></a>Zasady usługi Azure

[!INCLUDE [Resource Manager governance policy](../../includes/resource-manager-governance-policy.md)]

### <a name="apply-policies"></a>Stosowanie zasad

Subskrypcja obejmuje już kilka definicje zasad. Aby wyświetlić definicje dostępnych zasad, należy użyć:

```azurepowershell-interactive
(Get-AzureRmPolicyDefinition).Properties | Format-Table displayName, policyType
```

Możesz sprawdzić istniejące definicje zasad. Typ zasad jest **wbudowane** lub **niestandardowy**. Przejrzyj definicje dla tych, które opisują warunek, który chcesz przypisać. W tym artykule należy przypisać zasady który:

* ograniczenie lokalizacji dla wszystkich zasobów
* Ogranicz jednostki SKU dla maszyn wirtualnych
* maszyny wirtualne, które nie korzystają z dysków zarządzanych inspekcji

```azurepowershell-interactive
$locations ="eastus", "eastus2"
$skus = "Standard_DS1_v2", "Standard_E2s_v2"

$rg = Get-AzureRmResourceGroup -Name myResourceGroup

$locationDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed locations"}
$skuDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed virtual machine SKUs"}
$auditDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Audit VMs that do not use managed disks"}

New-AzureRMPolicyAssignment -Name "Set permitted locations" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $locationDefinition `
  -listOfAllowedLocations $locations
New-AzureRMPolicyAssignment -Name "Set permitted VM SKUs" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $skuDefinition `
  -listOfAllowedSKUs $skus
New-AzureRMPolicyAssignment -Name "Audit unmanaged disks" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $auditDefinition
```

## <a name="deploy-the-virtual-machine"></a>Wdróż maszynę wirtualną

Przypisano ról i zasad, więc wszystko jest gotowe do wdrożenia rozwiązania. Rozmiar domyślny to Standard_DS1_v2, które jest jednym z dozwolonych jednostki SKU. Podczas wykonywania tego kroku jest wyświetlany monit o poświadczenia. Wprowadzane wartości są konfigurowane jako nazwa użytkownika i hasło dla maszyny wirtualnej.

```azurepowershell-interactive
New-AzureRmVm -ResourceGroupName "myResourceGroup" `
     -Name "myVM" `
     -Location "East US" `
     -VirtualNetworkName "myVnet" `
     -SubnetName "mySubnet" `
     -SecurityGroupName "myNetworkSecurityGroup" `
     -PublicIpAddressName "myPublicIpAddress" `
     -OpenPorts 80,3389
```

Po zakończeniu wdrożenia, można zastosować więcej ustawienia zarządzania do rozwiązania.

## <a name="lock-resources"></a>Blokowanie zasobów

[!INCLUDE [Resource Manager governance locks](../../includes/resource-manager-governance-locks.md)]

### <a name="lock-a-resource"></a>Zablokowanie zasobu

Aby zablokować maszyny wirtualnej i grupy zabezpieczeń sieci, należy użyć:

```azurepowershell-interactive
New-AzureRmResourceLock -LockLevel CanNotDelete `
  -LockName LockVM `
  -ResourceName myVM `
  -ResourceType Microsoft.Compute/virtualMachines `
  -ResourceGroupName myResourceGroup
New-AzureRmResourceLock -LockLevel CanNotDelete `
  -LockName LockNSG `
  -ResourceName myNetworkSecurityGroup `
  -ResourceType Microsoft.Network/networkSecurityGroups `
  -ResourceGroupName myResourceGroup
```

Tylko można usunąć maszyny wirtualnej, w szczególności usunięcia blokady. Ten krok jest wyświetlany w obszarze [wyczyścić zasoby](#clean-up-resources).

## <a name="tag-resources"></a>Tag zasobów

[!INCLUDE [Resource Manager governance tags](../../includes/resource-manager-governance-tags.md)]

### <a name="tag-resources"></a>Tag zasobów

[!INCLUDE [Resource Manager governance tags Powershell](../../includes/resource-manager-governance-tags-powershell.md)]

Aby zastosować tagi do maszyny wirtualnej, należy użyć:

```azurepowershell-interactive
$r = Get-AzureRmResource -ResourceName myVM `
  -ResourceGroupName myResourceGroup `
  -ResourceType Microsoft.Compute/virtualMachines
Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test"; Project="Documentation" } -ResourceId $r.ResourceId -Force
```

### <a name="find-resources-by-tag"></a>Znajdź zasoby według znaczników

Aby znaleźć zasoby z tagu nazwy i wartości, należy użyć:

```azurepowershell-interactive
(Find-AzureRmResource -TagName Environment -TagValue Test).Name
```

Można użyć wartości zwracane dla zadań zarządzania, takich jak zatrzymanie wszystkich maszyn wirtualnych z wartością tagu.

```azurepowershell-interactive
Find-AzureRmResource -TagName Environment -TagValue Test | Where-Object {$_.ResourceType -eq "Microsoft.Compute/virtualMachines"} | Stop-AzureRmVM
```

### <a name="view-costs-by-tag-values"></a>Wyświetlanie kosztów przez wartości tagów

Po zastosowaniu tagów do zasobów, można wyświetlić kosztów zasobów z tych tagów. Trwa podczas analizy kosztów wyświetlić najnowsze użycie, więc może nie być wyświetlana koszty jeszcze. Gdy koszty są dostępne, można wyświetlić kosztów zasobów między grupami zasobów w ramach subskrypcji. Użytkownicy muszą mieć [subskrypcji poziom dostępu do informacji dotyczących rozliczeń](../billing/billing-manage-access.md) koszty.

Aby wyświetlić koszty według znaczników w portalu, wybierz subskrypcję, a następnie wybierz **analizy kosztów**.

![Analiza kosztów](./media/powershell-azure-resource-manager/select-cost-analysis.png)

Następnie filtrować według wartości tagu i wybierz **Zastosuj**.

![Koszt widok znaczników](./media/powershell-azure-resource-manager/view-costs-by-tag.png)

Można również użyć [interfejsów API usługi Azure rozliczeń](../billing/billing-usage-rate-card-overview.md) można programowo wyświetlać kosztów.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Nie można usunąć grupy zabezpieczeń sieci zablokowane, aż do usunięcia blokady. Aby usunąć blokadę, należy użyć:

```azurepowershell-interactive
Remove-AzureRmResourceLock -LockName LockVM `
  -ResourceName myVM `
  -ResourceType Microsoft.Compute/virtualMachines `
  -ResourceGroupName myResourceGroup
Remove-AzureRmResourceLock -LockName LockNSG `
  -ResourceName myNetworkSecurityGroup `
  -ResourceType Microsoft.Network/networkSecurityGroups `
  -ResourceGroupName myResourceGroup
```

Gdy grupa zasobów, maszyna wirtualna i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup).

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Kolejne kroki
* Aby uzyskać informacje dotyczące monitorowania maszyn wirtualnych, zobacz [monitorowania i aktualizowania maszyny wirtualnej systemu Windows przy użyciu programu Azure PowerShell](../virtual-machines/windows/tutorial-monitoring.md).
* Aby dowiedzieć się więcej o korzystaniu z Centrum zabezpieczeń Azure do wdrożenia rozwiązania w zakresie zabezpieczeń, [monitorowanie zabezpieczeń maszyny wirtualnej przy użyciu Centrum zabezpieczeń Azure](../virtual-machines/windows/tutorial-azure-security.md).
* Można przenieść istniejące zasoby do nowej grupy zasobów. Aby uzyskać przykłady, zobacz [przeniesienia zasobów do nowej grupy zasobów lub subskrypcji](resource-group-move-resources.md).
* Aby uzyskać instrukcje dla przedsiębiorstw dotyczące użycia usługi Resource Manager w celu efektywnego zarządzania subskrypcjami, zobacz [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Szkielet platformy Azure dla przedsiębiorstwa — narzucony nadzór subskrypcji).
