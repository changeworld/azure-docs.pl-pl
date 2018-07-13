---
title: Samouczek — zarządzanie maszynami wirtualnymi platformy Azure za pomocą programu Azure PowerShell | Microsoft Docs
description: Z tego samouczka dowiesz się, jak za pomocą programu Azure PowerShell zarządzać maszynami wirtualnymi platformy Azure, stosując kontrolę dostępu opartą na rolach, zasady, blokady i tagi
services: virtual-machines-windows
documentationcenter: virtual-machines
author: tfitzmac
manager: jeconnoc
editor: tysonn
ms.service: virtual-machines-windows
ms.workload: infrastructure
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: tutorial
ms.date: 05/21/2018
ms.author: tomfitz
ms.custom: mvc
ms.openlocfilehash: 42f7d767162f2f403b2cf921e31a38b711a3c773
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37438238"
---
# <a name="tutorial-learn-about-windows-virtual-machine-governance-with-azure-powershell"></a>Samouczek: informacje o zarządzaniu maszynami wirtualnymi z systemem Windows za pomocą programu Azure PowerShell

[!INCLUDE [Resource Manager governance introduction](../../../includes/resource-manager-governance-intro.md)]

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Przykłady w tym artykule wymagają programu Azure PowerShell w wersji 6.0 lub nowszej. Jeśli używasz programu PowerShell lokalnie i nie masz wersji 6.0 lub nowszej, [zaktualizuj swoją wersję](/powershell/azure/install-azurerm-ps). Należy również uruchomić polecenie `Connect-AzureRmAccount` w celu nawiązania połączenia z platformą Azure. W przypadku instalacji lokalnych należy również [pobrać moduł programu PowerShell usługi Azure AD](https://www.powershellgallery.com/packages/AzureAD/), aby utworzyć nową grupę usługi Azure Active Directory.

## <a name="understand-scope"></a>Objaśnienie zakresu

[!INCLUDE [Resource Manager governance scope](../../../includes/resource-manager-governance-scope.md)]

W tym samouczku zastosujesz wszystkie ustawienia zarządzania do grupy zasobów, aby móc łatwo usunąć te ustawienia po zakończeniu.

Utwórzmy tę grupę zasobów.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

Grupa zasobów jest obecnie pusta.

## <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach

Upewnij się, że użytkownicy w organizacji mają odpowiedni poziom dostępu do tych zasobów. Nie udzielaj użytkownikom nieograniczonego dostępu, ale upewnij się, że mogą oni wykonywać swoją pracę. Dzięki [kontroli dostępu opartej na rolach](../../role-based-access-control/overview.md) możesz zarządzać, którzy użytkownicy mają uprawnienia do wykonywania określonych akcji w danym zakresie.

Aby móc tworzyć i usuwać przypisania roli, użytkownicy muszą mieć dostęp `Microsoft.Authorization/roleAssignments/*`. Ten dostęp jest udzielany za pośrednictwem ról Właściciel lub Administrator dostępu użytkowników.

W przypadku zarządzania rozwiązaniami maszyn wirtualnych dostępne są 3 role specyficzne dla zasobów, które zapewniają najczęściej potrzebny dostęp:

* [Współautor maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)
* [Współautor sieci](../../role-based-access-control/built-in-roles.md#network-contributor)
* [Współautor konta magazynu](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Zamiast przypisywać role poszczególnym użytkownikom, często łatwiej jest [utworzyć grupę usługi Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) dla użytkowników, którzy muszą wykonywać podobne działania. Następnie należy przypisać tę grupę do odpowiedniej roli. Aby uprościć ten artykuł, utwórz grupę usługi Azure Active Directory bez członków. Nadal możesz przypisać tę grupę do roli w zakresie. 

W poniższym przykładzie zostanie utworzona grupa usługi Azure Active Directory o nazwie *VMDemoContributors* i pseudonimie pocztowym *vmDemoGroup*. Pseudonim pocztowy służy jako alias dla grupy.

```azurepowershell-interactive
$adgroup = New-AzureADGroup -DisplayName VMDemoContributors `
  -MailNickName vmDemoGroup `
  -MailEnabled $false `
  -SecurityEnabled $true
```

Propagacja grupy w usłudze Azure Active Directory może potrwać kilka chwil od momentu zwrócenia wyników przez wiersz polecenia. Po upływie 20 lub 30 sekund przypisz nową grupę usługi Azure Active Directory do roli współautora maszyny wirtualnej dla grupy zasobów za pomocą polecenia [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment).  Jeśli uruchomisz następujące polecenie przed zakończeniem propagacji, zostanie wyświetlony komunikat o błędzie **Podmiot zabezpieczeń <guid> nie istnieje w katalogu**. Spróbuj ponownie uruchomić polecenie.

```azurepowershell-interactive
New-AzureRmRoleAssignment -ObjectId $adgroup.ObjectId `
  -ResourceGroupName myResourceGroup `
  -RoleDefinitionName "Virtual Machine Contributor"
```

Zazwyczaj należy powtórzyć ten proces dla roli *Współautor sieci* i *Współautor konta magazynu*, aby upewnić się, że użytkownicy mogą zarządzać wdrożonymi zasobami. W tym artykule można pominąć te kroki.

## <a name="azure-policies"></a>Zasady platformy Azure

[!INCLUDE [Resource Manager governance policy](../../../includes/resource-manager-governance-policy.md)]

### <a name="apply-policies"></a>Stosowanie zasad

Subskrypcja ma już kilka definicji zasad. Aby wyświetlić dostępne definicje zasad, użyj polecenia [Get-AzureRmPolicyDefinition](/powershell/module/AzureRM.Resources/Get-AzureRmPolicyDefinition):

```azurepowershell-interactive
(Get-AzureRmPolicyDefinition).Properties | Format-Table displayName, policyType
```

Zostaną wyświetlone istniejące definicje zasad. Typ zasad to **Wbudowane** albo **Niestandardowe**. Przejrzyj definicje i znajdź te opisujące warunek, który chcesz przypisać. W tym artykule przypiszesz zasady określające następujące działania:

* Ograniczanie lokalizacji dla wszystkich zasobów.
* Ograniczanie jednostek SKU dla maszyn wirtualnych.
* Przeprowadzanie inspekcji maszyn wirtualnych, które nie korzystają z dysków zarządzanych.

W poniższym przykładzie pobierane są 3 definicje zasad na podstawie nazwy wyświetlanej. Aby przypisać te definicje do grup zasobów, użyj polecenia [New-AzureRMPolicyAssignment](/powershell/module/azurerm.resources/new-azurermpolicyassignment). W przypadku niektórych zasad należy podać wartości parametrów, aby określić dozwolone wartości.

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

## <a name="deploy-the-virtual-machine"></a>Wdrażanie maszyny wirtualnej

Role i zasady są przypisane, a zatem pora na wdrożenie rozwiązania. Rozmiar domyślny to Standard_DS1_v2, który jest jedną z dozwolonych jednostek SKU. Podczas wykonywania tego kroku jest wyświetlany monit o poświadczenia. Wprowadzane wartości są konfigurowane jako nazwa użytkownika i hasło dla maszyny wirtualnej.

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

Po zakończeniu wdrażania możesz zastosować do rozwiązania więcej ustawień zarządzania.

## <a name="lock-resources"></a>Blokowanie zasobów

[Blokady zasobów](../../azure-resource-manager/resource-group-lock-resources.md) uniemożliwiają użytkownikom w organizacji przypadkowe usunięcie lub zmodyfikowanie krytycznych zasobów. W przeciwieństwie do kontroli dostępu opartej na rolach blokady zasobów stosują ograniczenie do wszystkich użytkowników i ról. Poziom blokady można ustawić na wartość *CanNotDelete* lub *ReadOnly*.

Aby zablokować maszynę wirtualną i sieciową grupę zabezpieczeń, użyj polecenia [New-AzureRmResourceLock](/powershell/module/azurerm.resources/new-azurermresourcelock):

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

Aby przetestować blokadę, spróbuj uruchomić następujące polecenie:

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup
```

Zostanie wyświetlony komunikat o błędzie informujący, że nie można wykonać operacji usuwania z powodu blokady. Grupę zasobów można usunąć tylko po zdjęciu blokad. Ten krok przedstawiono w sekcji [Oczyszczanie zasobów](#clean-up-resources).

## <a name="tag-resources"></a>Tagowanie zasobów

Stosowanie [tagów](../../azure-resource-manager/resource-group-using-tags.md) do zasobów platformy Azure umożliwia ich logiczne zorganizowanie według kategorii. Każdy tag składa się z nazwy i wartości. Na przykład można zastosować nazwę „Środowisko” i wartość „Produkcyjne” do wszystkich zasobów w środowisku produkcyjnym.

[!INCLUDE [Resource Manager governance tags Powershell](../../../includes/resource-manager-governance-tags-powershell.md)]

Aby zastosować tagi do maszyny wirtualnej, użyj polecenia [Set-AzureRmResource](/powershell/module/azurerm.resources/set-azurermresource):

```azurepowershell-interactive
# Get the virtual machine
$r = Get-AzureRmResource -ResourceName myVM `
  -ResourceGroupName myResourceGroup `
  -ResourceType Microsoft.Compute/virtualMachines

# Apply tags to the virtual machine
Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test"; Project="Documentation" } -ResourceId $r.ResourceId -Force
```

### <a name="find-resources-by-tag"></a>Znajdowanie zasobów według tagów

Aby znaleźć zasoby według wartości i nazwy tagu, użyj polecenia [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource):

```azurepowershell-interactive
(Get-AzureRmResource -Tag @{ Environment="Test"}).Name
```

Zwróconych wartości można użyć na potrzeby zadań zarządzania, takich jak zatrzymanie wszystkich maszyn wirtualnych z daną wartością tagu.

```azurepowershell-interactive
Get-AzureRmResource -Tag @{ Environment="Test"} | Where-Object {$_.ResourceType -eq "Microsoft.Compute/virtualMachines"} | Stop-AzureRmVM
```

### <a name="view-costs-by-tag-values"></a>Wyświetlanie kosztów według wartości tagów

[!INCLUDE [Resource Manager governance tags billing](../../../includes/resource-manager-governance-tags-billing.md)]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Zablokowanej sieciowej grupy zabezpieczeń nie można usunąć, dopóki blokada nie zostanie zdjęta. Aby zdjąć blokadę, użyj polecenia [Remove-AzureRmResourceLock](/powershell/module/azurerm.resources/remove-azurermresourcelock):

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

## <a name="next-steps"></a>Następne kroki

W tym samouczku został utworzony obraz niestandardowy maszyny wirtualnej. W tym samouczku omówiono:

> [!div class="checklist"]
> * Przypisywanie użytkowników do roli
> * Stosowanie zasad wymuszających standardy
> * Ochrona krytycznych zasobów za pomocą blokad
> * Tagowanie zasobów na potrzeby rozliczeń i zarządzania

Przejdź do następnego samouczka, aby dowiedzieć się więcej o maszynach wirtualnych o wysokiej dostępności.

> [!div class="nextstepaction"]
> [Monitorowanie maszyn wirtualnych](tutorial-monitoring.md)

