---
title: Decydować maszyn wirtualnych platformy Azure przy użyciu programu Azure PowerShell | Dokumentacja firmy Microsoft
description: Samouczek — Zarządzanie maszyn wirtualnych platformy Azure, stosując RBAC, zasady blokad i tagi przy użyciu programu Azure PowerShell
services: virtual-machines-windows
documentationcenter: virtual-machines
author: tfitzmac
manager: jeconnoc
editor: tysonn
ms.service: virtual-machines-windows
ms.workload: infrastructure
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: tomfitz
ms.openlocfilehash: d4e09eb11ea04c31b7e302b7f66f8e67c13e8252
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="virtual-machine-governance-with-azure-powershell"></a>Zarządzanie maszyny wirtualnej przy użyciu programu Azure PowerShell

[!INCLUDE [Resource Manager governance introduction](../../../includes/resource-manager-governance-intro.md)]

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Jeśli użytkownik chce zainstalować i lokalnie przy użyciu programu PowerShell, zobacz [modułu instalacji programu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzureRmAccount`, aby utworzyć połączenie z platformą Azure. W przypadku instalacji lokalnej, należy również [Pobierz moduł Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/) Aby utworzyć nową grupę w usłudze Azure Active Directory.

## <a name="understand-scope"></a>Zrozumieć zakres

[!INCLUDE [Resource Manager governance scope](../../../includes/resource-manager-governance-scope.md)]

W tym samouczku zastosowanie wszystkich ustawień zarządzania do grupy zasobów, można łatwo usunąć te ustawienia po zakończeniu.

Umożliwia tworzenie tej grupy zasobów.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

Grupa zasobów jest obecnie pusta.

## <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach

Chcesz upewnić się, że użytkownicy w organizacji mają odpowiedni poziom dostępu do tych zasobów. Nie chcesz udzielić nieograniczonego dostępu do użytkowników, ale należy również upewnij się, że mogą je z siecią firmową. [Kontrola dostępu oparta na rolach](../../role-based-access-control/overview.md) pozwala na zarządzanie, którzy użytkownicy mają uprawnienia do wykonania określonych czynności w zakresie.

Aby utworzyć i usunąć przypisania roli, użytkownicy muszą mieć `Microsoft.Authorization/roleAssignments/*` dostępu. Ten dostęp za pomocą ról właściciel lub Administrator dostępu użytkowników.

Do zarządzania rozwiązań maszyny wirtualnej, istnieją trzy role określonych zasobów, które zapewniają dostęp do potrzebnych:

* [Współautor maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)
* [Współautor sieci](../../role-based-access-control/built-in-roles.md#network-contributor)
* [Współautor konta magazynu](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Zamiast przypisywać role poszczególnym użytkownikom, łatwiej często [utworzyć grupy usługi Azure Active Directory](../../active-directory/active-directory-groups-create-azure-portal.md) dla użytkowników, którzy potrzebują podjęcia działań podobne. Następnie przypisać do odpowiedniej roli. Aby uprościć w tym artykule, należy utworzyć grupy usługi Azure Active Directory bez członków. Nadal można przypisać tej grupy do roli dla zakresu. 

Poniższy przykład tworzy grupę usługi Azure Active Directory o nazwie *VMDemoContributors* z pseudonim poczty *vmDemoGroup*. Pseudonim poczty służy jako alias dla grupy.

```azurepowershell-interactive
$adgroup = New-AzureADGroup -DisplayName VMDemoContributors `
  -MailNickName vmDemoGroup `
  -MailEnabled $false `
  -SecurityEnabled $true
```

Może potrwać kilka minut po powrocie z wiersza polecenia dla grupy w celu propagowania w usłudze Azure Active Directory. Po upływie 20 lub 30 sekund, użyj [AzureRmRoleAssignment nowy](/powershell/module/azurerm.resources/new-azurermroleassignment) polecenie, aby przypisać nową grupę w usłudze Azure Active Directory do roli współautora maszyny wirtualnej dla grupy zasobów.  Uruchamiając następujące polecenie przed zakończeniem propagacji go zostanie wyświetlony błąd informujący o **główna <guid> nie istnieje w katalogu**. Spróbuj ponownie uruchomić polecenie.

```azurepowershell-interactive
New-AzureRmRoleAssignment -ObjectId $adgroup.ObjectId `
  -ResourceGroupName myResourceGroup `
  -RoleDefinitionName "Virtual Machine Contributor"
```

Zwykle, powtórz proces *współautora sieci* i *współautora konta magazynu* się upewnić, że użytkownicy są dodawani do zarządzania zasobami wdrożone. W tym artykule można pominąć te kroki.

## <a name="azure-policies"></a>Zasady usługi Azure

[!INCLUDE [Resource Manager governance policy](../../../includes/resource-manager-governance-policy.md)]

### <a name="apply-policies"></a>Stosowanie zasad

Subskrypcja obejmuje już kilka definicje zasad. Aby wyświetlić definicje dostępnych zasad, użyj [Get-AzureRmPolicyDefinition](/powershell/module/AzureRM.Resources/Get-AzureRmPolicyDefinition) polecenia:

```azurepowershell-interactive
(Get-AzureRmPolicyDefinition).Properties | Format-Table displayName, policyType
```

Możesz sprawdzić istniejące definicje zasad. Typ zasad jest **wbudowane** lub **niestandardowy**. Przejrzyj definicje dla tych, które opisują warunek, który chcesz przypisać. W tym artykule należy przypisać zasady który:

* Ograniczenie lokalizacji dla wszystkich zasobów.
* Ogranicz jednostki SKU dla maszyn wirtualnych.
* Inspekcja maszyn wirtualnych, które nie korzystają z dysków zarządzanych.

W poniższym przykładzie możesz pobrać trzy definicji zasad opartych na nazwę wyświetlaną. Możesz użyć [AzureRMPolicyAssignment nowy](/powershell/module/azurerm.resources/new-azurermpolicyassignment) polecenie, aby przypisać tych definicji do grupy zasobów. W przypadku niektórych zasad musisz podać wartości parametrów, aby określić dozwolone wartości.

```azurepowershell-interactive
# Values to use for parameters
$locations ="eastus", "eastus2"
$skus = "Standard_DS1_v2", "Standard_E2s_v2"

# Get the resource group
$rg = Get-AzureRmResourceGroup -Name myResourceGroup

# Get policy definitions for allowed locations, allowed SKUs, and auditing VMs that don't use managed disks
$locationDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed locations"}
$skuDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed virtual machine SKUs"}
$auditDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Audit VMs that do not use managed disks"}

# Assign policy for allowed locations
New-AzureRMPolicyAssignment -Name "Set permitted locations" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $locationDefinition `
  -listOfAllowedLocations $locations

# Assign policy for allowed SKUs
New-AzureRMPolicyAssignment -Name "Set permitted VM SKUs" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $skuDefinition `
  -listOfAllowedSKUs $skus

# Assign policy for auditing unmanaged disks
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

[Blokowania zasobów](../../azure-resource-manager/resource-group-lock-resources.md) uniemożliwić użytkownikom w organizacji przypadkowo usuwanie i modyfikowanie kluczowych zasobów. W przeciwieństwie do kontroli dostępu opartej na rolach blokowania zasobów mają zastosowanie ograniczenia we wszystkich użytkowników i ról. Można ustawić poziom blokady *CanNotDelete* lub *tylko do odczytu*.

Aby zablokować maszyny wirtualnej i grupy zabezpieczeń sieci, należy użyć [AzureRmResourceLock nowy](/powershell/module/azurerm.resources/new-azurermresourcelock) polecenia:

```azurepowershell-interactive
# Add CanNotDelete lock to the VM
New-AzureRmResourceLock -LockLevel CanNotDelete `
  -LockName LockVM `
  -ResourceName myVM `
  -ResourceType Microsoft.Compute/virtualMachines `
  -ResourceGroupName myResourceGroup

# Add CanNotDelete lock to the network security group
New-AzureRmResourceLock -LockLevel CanNotDelete `
  -LockName LockNSG `
  -ResourceName myNetworkSecurityGroup `
  -ResourceType Microsoft.Network/networkSecurityGroups `
  -ResourceGroupName myResourceGroup
```

Aby przetestować blokad, spróbuj uruchomić następujące polecenie:

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup
```

Zostanie wyświetlony komunikat o błędzie informujący, że nie można wykonać operacji usuwania z powodu blokady. Tylko można usunąć grupy zasobów, w szczególności usunięcia blokady. Ten krok jest wyświetlany w obszarze [wyczyścić zasoby](#clean-up-resources).

## <a name="tag-resources"></a>Tag zasobów

Należy zastosować [tagi](../../azure-resource-manager/resource-group-using-tags.md) do zasobów platformy Azure na organizację je według kategorii. Każdy tag składa się z nazwy i wartości. Na przykład można zastosować nazwę „Środowisko” i wartość „Produkcyjne” do wszystkich zasobów w środowisku produkcyjnym.

[!INCLUDE [Resource Manager governance tags Powershell](../../../includes/resource-manager-governance-tags-powershell.md)]

Aby zastosować tagi do maszyny wirtualnej, użyj [AzureRmResource zestaw](/powershell/module/azurerm.resources/set-azurermresource) polecenia:

```azurepowershell-interactive
# Get the virtual machine
$r = Get-AzureRmResource -ResourceName myVM `
  -ResourceGroupName myResourceGroup `
  -ResourceType Microsoft.Compute/virtualMachines

# Apply tags to the virtual machine
Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test"; Project="Documentation" } -ResourceId $r.ResourceId -Force
```

### <a name="find-resources-by-tag"></a>Znajdź zasoby według znaczników

Aby znaleźć zasoby z tagu nazwy i wartości, należy użyć [AzureRmResource Znajdź](/powershell/module/azurerm.resources/find-azurermresource) polecenia:

```azurepowershell-interactive
(Find-AzureRmResource -TagName Environment -TagValue Test).Name
```

Można użyć wartości zwracane dla zadań zarządzania, takich jak zatrzymanie wszystkich maszyn wirtualnych z wartością tagu.

```azurepowershell-interactive
Find-AzureRmResource -TagName Environment -TagValue Test | Where-Object {$_.ResourceType -eq "Microsoft.Compute/virtualMachines"} | Stop-AzureRmVM
```

### <a name="view-costs-by-tag-values"></a>Wyświetlanie kosztów przez wartości tagów

[!INCLUDE [Resource Manager governance tags billing](../../../includes/resource-manager-governance-tags-billing.md)]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Nie można usunąć grupy zabezpieczeń sieci zablokowane, aż do usunięcia blokady. Aby usunąć blokadę, użyj [AzureRmResourceLock Usuń](/powershell/module/azurerm.resources/remove-azurermresourcelock) polecenia:

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

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku został utworzony obraz niestandardowy maszyny wirtualnej. W tym samouczku omówiono:

> [!div class="checklist"]
> * Przypisywanie użytkowników do roli
> * Stosowanie zasad, które wymuszają standardów
> * Chroń krytyczne zasoby z blokad
> * Tag zasoby dotyczące rozliczeń i zarządzania

Przejdź do następnego samouczkiem, aby poznać sposób wysokiej dostępności maszyny wirtualnej.

> [!div class="nextstepaction"]
> [Monitorowanie maszyn wirtualnych](tutorial-monitoring.md)

