---
title: Samouczek — zarządzanie maszynami wirtualnymi platformy Azure za pomocą Azure PowerShell | Microsoft Docs
description: W ramach tego samouczka nauczysz się używać Azure PowerShell do zarządzania maszynami wirtualnymi platformy Azure przez zastosowanie RBAC, zasad, blokad i tagów
services: virtual-machines-windows
documentationcenter: virtual-machines
author: tfitzmac
manager: gwallace
editor: tysonn
ms.service: virtual-machines-windows
ms.workload: infrastructure
ms.tgt_pltfrm: vm-windows
ms.topic: tutorial
ms.date: 12/05/2018
ms.author: tomfitz
ms.custom: mvc
ms.openlocfilehash: cb0050f79f2f6a38b9cd58ee87aa88899f0f5a15
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2019
ms.locfileid: "71674988"
---
# <a name="tutorial-learn-about-windows-virtual-machine-management-with-azure-powershell"></a>Samouczek: informacje o zarządzaniu maszynami wirtualnymi systemu Windows za pomocą Azure PowerShell

[!INCLUDE [Resource Manager governance introduction](../../../includes/resource-manager-governance-intro.md)]

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell uruchamiania

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Cloud Shell to bezpłatna interaktywna powłoka, za pomocą której można wykonać kroki opisane w tym artykule. Ma ona wspólne narzędzia platformy Azure preinstalowane i skonfigurowane do użycia z Twoim kontem. 

Aby otworzyć Cloud Shell, po prostu wybierz opcję **Wypróbuj** w prawym górnym rogu bloku kodu. Cloud Shell można również uruchomić na oddzielnej karcie przeglądarki, przechodząc do [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Wybierz pozycję **Kopiuj** , aby skopiować bloki kodu, wkleić je do Cloud Shell i naciśnij klawisz ENTER, aby go uruchomić.

## <a name="understand-scope"></a>Opis zakresu

[!INCLUDE [Resource Manager governance scope](../../../includes/resource-manager-governance-scope.md)]

W tym samouczku zastosowano wszystkie ustawienia zarządzania do grupy zasobów, dzięki czemu można łatwo usunąć te ustawienia po zakończeniu.

Utwórzmy tę grupę zasobów.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

Obecnie Grupa zasobów jest pusta.

## <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach

Chcesz upewnić się, że użytkownicy w organizacji mają odpowiedni poziom dostępu do tych zasobów. Nie chcesz przyznawać użytkownikom nieograniczonego dostępu, ale musisz również upewnić się, że mogą oni wykonać swoją służbę. [Kontrola dostępu oparta na rolach](../../role-based-access-control/overview.md) umożliwia zarządzanie użytkownikami, którzy mają uprawnienia do wykonywania określonych działań w zakresie.

Aby tworzyć i usuwać przypisania ról, użytkownicy muszą mieć dostęp do `Microsoft.Authorization/roleAssignments/*`. Ten dostęp jest udzielany przez role administratora dostępu właściciela lub użytkownika.

W przypadku zarządzania rozwiązaniami maszyn wirtualnych istnieją trzy role specyficzne dla zasobów, które zapewniają często wymagany dostęp:

* [Współautor maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)
* [Współautor sieci](../../role-based-access-control/built-in-roles.md#network-contributor)
* [Współautor konta magazynu](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Zamiast przypisywania ról do poszczególnych użytkowników często łatwiej jest używać grupy Azure Active Directory, która ma użytkowników, którzy muszą podejmować podobne działania. Następnie przypisz tę grupę do odpowiedniej roli. W tym artykule należy użyć istniejącej grupy do zarządzania maszyną wirtualną lub użyć portalu do [utworzenia grupy Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Po utworzeniu nowej grupy lub znalezieniu istniejącej należy użyć polecenia [New-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment) , aby przypisać grupę Azure Active Directory do roli współautor maszyny wirtualnej dla grupy zasobów.  

```azurepowershell-interactive
$adgroup = Get-AzADGroup -DisplayName <your-group-name>

New-AzRoleAssignment -ObjectId $adgroup.id `
  -ResourceGroupName myResourceGroup `
  -RoleDefinitionName "Virtual Machine Contributor"
```

Jeśli wystąpi błąd z informacją o **głównym \<guid > nie istnieje w katalogu**, Nowa grupa nie jest propagowana w całej Azure Active Directory. Spróbuj ponownie uruchomić polecenie.

Zazwyczaj należy powtórzyć proces dla współautorów *sieci* i *współautorów konta magazynu* , aby upewnić się, że użytkownicy są przypisani do zarządzania wdrożonymi zasobami. W tym artykule można pominąć te kroki.

## <a name="azure-policy"></a>Azure Policy

[Azure Policy](../../governance/policy/overview.md) pomaga upewnić się, że wszystkie zasoby w subskrypcji są zgodne ze standardami firmy. Twoja subskrypcja ma już kilka definicji zasad. Aby wyświetlić dostępne definicje zasad, użyj polecenia [Get-AzPolicyDefinition](https://docs.microsoft.com/powershell/module/az.resources/Get-AzPolicyDefinition) :

```azurepowershell-interactive
(Get-AzPolicyDefinition).Properties | Format-Table displayName, policyType
```

Zobaczysz istniejące definicje zasad. Typ zasad jest **wbudowany** lub **niestandardowy**. Przejrzyj definicje dla tych, które opisują warunek, który chcesz przypisać. W tym artykule opisano przypisywanie zasad:

* Ogranicz lokalizacje dla wszystkich zasobów.
* Ogranicz jednostki SKU dla maszyn wirtualnych.
* Inspekcja maszyn wirtualnych, które nie korzystają z dysków zarządzanych.

W poniższym przykładzie można pobrać trzy definicje zasad na podstawie nazwy wyświetlanej. Użyj polecenia [New-AzPolicyAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azpolicyassignment) , aby przypisać te definicje do grupy zasobów. W przypadku niektórych zasad należy podać wartości parametrów, aby określić dozwolone wartości.

```azurepowershell-interactive
# Values to use for parameters
$locations ="eastus", "eastus2"
$skus = "Standard_DS1_v2", "Standard_E2s_v2"

# Get the resource group
$rg = Get-AzResourceGroup -Name myResourceGroup

# Get policy definitions for allowed locations, allowed SKUs, and auditing VMs that don't use managed disks
$locationDefinition = Get-AzPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed locations"}
$skuDefinition = Get-AzPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed virtual machine SKUs"}
$auditDefinition = Get-AzPolicyDefinition | where-object {$_.properties.displayname -eq "Audit VMs that do not use managed disks"}

# Assign policy for allowed locations
New-AzPolicyAssignment -Name "Set permitted locations" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $locationDefinition `
  -listOfAllowedLocations $locations

# Assign policy for allowed SKUs
New-AzPolicyAssignment -Name "Set permitted VM SKUs" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $skuDefinition `
  -listOfAllowedSKUs $skus

# Assign policy for auditing unmanaged disks
New-AzPolicyAssignment -Name "Audit unmanaged disks" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $auditDefinition
```

## <a name="deploy-the-virtual-machine"></a>Wdróż maszynę wirtualną

Przypisano role i zasady, aby można było wdrożyć rozwiązanie. Domyślny rozmiar to Standard_DS1_v2, który jest jedną z dozwolonych jednostek SKU. Po uruchomieniu tego kroku zostanie wyświetlony monit o podanie poświadczeń. Wprowadzane wartości są konfigurowane jako nazwa użytkownika i hasło dla maszyny wirtualnej.

```azurepowershell-interactive
New-AzVm -ResourceGroupName "myResourceGroup" `
     -Name "myVM" `
     -Location "East US" `
     -VirtualNetworkName "myVnet" `
     -SubnetName "mySubnet" `
     -SecurityGroupName "myNetworkSecurityGroup" `
     -PublicIpAddressName "myPublicIpAddress" `
     -OpenPorts 80,3389
```

Po zakończeniu wdrażania możesz zastosować więcej ustawień zarządzania do rozwiązania.

## <a name="lock-resources"></a>Zablokuj zasoby

[Blokady zasobów](../../azure-resource-manager/resource-group-lock-resources.md) uniemożliwiają użytkownikom w organizacji przypadkowe usuwanie lub modyfikowanie zasobów krytycznych. W przeciwieństwie do kontroli dostępu opartej na rolach, blokady zasobów stosują ograniczenie dla wszystkich użytkowników i ról. Możesz ustawić poziom blokady na *CanNotDelete* lub tylko do *odczytu*.

Aby zablokować maszynę wirtualną i sieciową grupę zabezpieczeń, użyj polecenia [New-AzResourceLock](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcelock) :

```azurepowershell-interactive
# Add CanNotDelete lock to the VM
New-AzResourceLock -LockLevel CanNotDelete `
  -LockName LockVM `
  -ResourceName myVM `
  -ResourceType Microsoft.Compute/virtualMachines `
  -ResourceGroupName myResourceGroup

# Add CanNotDelete lock to the network security group
New-AzResourceLock -LockLevel CanNotDelete `
  -LockName LockNSG `
  -ResourceName myNetworkSecurityGroup `
  -ResourceType Microsoft.Network/networkSecurityGroups `
  -ResourceGroupName myResourceGroup
```

Aby przetestować blokady, spróbuj uruchomić następujące polecenie:

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroup
```

Zostanie wyświetlony komunikat o błędzie informujący, że nie można ukończyć operacji usuwania z powodu blokady. Grupę zasobów można usunąć tylko wtedy, gdy zostały usunięte blokady. Ten krok jest pokazywany w [Oczyszczanie zasobów](#clean-up-resources).

## <a name="tag-resources"></a>Dodawanie tagów do zasobów

[Znaczniki](../../azure-resource-manager/resource-group-using-tags.md) do zasobów platformy Azure są stosowane w celu logicznego organizowania ich według kategorii. Każdy tag składa się z nazwy i wartości. Na przykład można zastosować nazwę "Environment" i wartość "Product" do wszystkich zasobów w środowisku produkcyjnym.

[!INCLUDE [Resource Manager governance tags Powershell](../../../includes/resource-manager-governance-tags-powershell.md)]

Aby zastosować Tagi do maszyny wirtualnej, użyj polecenia [Set-AzResource](https://docs.microsoft.com/powershell/module/az.resources/set-azresource) :

```azurepowershell-interactive
# Get the virtual machine
$r = Get-AzResource -ResourceName myVM `
  -ResourceGroupName myResourceGroup `
  -ResourceType Microsoft.Compute/virtualMachines

# Apply tags to the virtual machine
Set-AzResource -Tag @{ Dept="IT"; Environment="Test"; Project="Documentation" } -ResourceId $r.ResourceId -Force
```

### <a name="find-resources-by-tag"></a>Znajdź zasoby według tagu

Aby znaleźć zasoby z nazwą i wartością tagu, użyj polecenia [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource) :

```azurepowershell-interactive
(Get-AzResource -Tag @{ Environment="Test"}).Name
```

Można użyć zwracanych wartości zadań zarządzania, takich jak zatrzymanie wszystkich maszyn wirtualnych z wartością tagu.

```azurepowershell-interactive
Get-AzResource -Tag @{ Environment="Test"} | Where-Object {$_.ResourceType -eq "Microsoft.Compute/virtualMachines"} | Stop-AzVM
```

### <a name="view-costs-by-tag-values"></a>Wyświetlanie kosztów według wartości tagów

[!INCLUDE [Resource Manager governance tags billing](../../../includes/resource-manager-governance-tags-billing.md)]

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Nie można usunąć zablokowanej sieciowej grupy zabezpieczeń, dopóki blokada nie zostanie usunięta. Aby usunąć blokadę, użyj polecenia [Remove-AzResourceLock](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcelock) :

```azurepowershell-interactive
Remove-AzResourceLock -LockName LockVM `
  -ResourceName myVM `
  -ResourceType Microsoft.Compute/virtualMachines `
  -ResourceGroupName myResourceGroup
Remove-AzResourceLock -LockName LockNSG `
  -ResourceName myNetworkSecurityGroup `
  -ResourceType Microsoft.Network/networkSecurityGroups `
  -ResourceGroupName myResourceGroup
```

Gdy grupa zasobów, maszyna wirtualna i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) .

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono niestandardowy obraz maszyny wirtualnej. Wiesz już, jak:

> [!div class="checklist"]
> * Przypisywanie użytkowników do roli
> * Stosowanie zasad, które wymuszają standardy
> * Ochrona najważniejszych zasobów przy użyciu blokad
> * Otaguj zasoby dotyczące rozliczeń i zarządzania

Przejdź do następnego samouczka, aby dowiedzieć się, jak identyfikować zmiany i zarządzać aktualizacjami pakietów na maszynie wirtualnej z systemem Linux.

> [!div class="nextstepaction"]
> [Zarządzanie maszynami wirtualnymi](tutorial-config-management.md)

