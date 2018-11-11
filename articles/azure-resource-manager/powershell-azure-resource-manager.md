---
title: Zarządzanie rozwiązaniami platformy Azure przy użyciu programu PowerShell | Dokumentacja firmy Microsoft
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
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: tomfitz
ms.openlocfilehash: 0ecb497e9b07119f6c32149a875f27ee7c338d74
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/09/2018
ms.locfileid: "51344641"
---
# <a name="manage-resources-with-azure-powershell"></a>Zarządzanie zasobami za pomocą programu Azure PowerShell

[!INCLUDE [Resource Manager governance introduction](../../includes/resource-manager-governance-intro.md)]

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Jeśli postanowisz zainstalować program PowerShell i używać go lokalnie, zobacz [Instalowanie modułu programu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzureRmAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="understand-scope"></a>Objaśnienie zakresu

[!INCLUDE [Resource Manager governance scope](../../includes/resource-manager-governance-scope.md)]

W tym artykule zastosowanie wszystkich ustawień zarządzania w grupie zasobów, dzięki czemu można łatwo usunąć te ustawienia po zakończeniu.

Utwórz grupę zasobów.

```azurepowershell-interactive
Set-AzureRmContext -Subscription <subscription-name>
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

Grupa zasobów jest obecnie pusta.

## <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach

[!INCLUDE [Resource Manager governance policy](../../includes/resource-manager-governance-rbac.md)]

### <a name="assign-a-role"></a>Przypisywanie roli

W tym artykule wdrożysz maszynę wirtualną i jej powiązane sieci wirtualnej. W przypadku zarządzania rozwiązaniami maszyn wirtualnych dostępne są 3 role specyficzne dla zasobów, które zapewniają najczęściej potrzebny dostęp:

* [Współautor maszyny wirtualnej](../role-based-access-control/built-in-roles.md#virtual-machine-contributor)
* [Współautor sieci](../role-based-access-control/built-in-roles.md#network-contributor)
* [Współautor konta magazynu](../role-based-access-control/built-in-roles.md#storage-account-contributor)

Zamiast przypisywać role poszczególnym użytkownikom, często łatwiej jest [utworzyć grupę usługi Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) dla użytkowników, którzy muszą wykonywać podobne działania. Następnie należy przypisać tę grupę do odpowiedniej roli. Aby uprościć ten artykuł, utwórz grupę usługi Azure Active Directory bez członków. Nadal możesz przypisać tę grupę do roli w zakresie.

Poniższy przykład obejmuje tworzenie grupy i przypisuje go do roli Współautor maszyny wirtualnej dla grupy zasobów. Aby uruchomić `New-AzureAdGroup` polecenia należy używać [usługi Azure Cloud Shell](/azure/cloud-shell/overview) lub [pobieranie modułu programu PowerShell usługi Azure AD](https://www.powershellgallery.com/packages/AzureAD/).

```azurepowershell-interactive
$adgroup = New-AzureADGroup -DisplayName VMDemoContributors `
  -MailNickName vmDemoGroup `
  -MailEnabled $false `
  -SecurityEnabled $true
New-AzureRmRoleAssignment -ObjectId $adgroup.ObjectId `
  -ResourceGroupName myResourceGroup `
  -RoleDefinitionName "Virtual Machine Contributor"
```

Zazwyczaj należy powtórzyć ten proces dla roli **Współautor sieci** i **Współautor konta magazynu**, aby upewnić się, że użytkownicy mogą zarządzać wdrożonymi zasobami. W tym artykule można pominąć te kroki.

## <a name="azure-policy"></a>Azure Policy

Usługa [Azure Policy](../azure-policy/azure-policy-introduction.md) ułatwia zapewnienie, że wszystkie zasoby w subskrypcji spełniają standardy firmy. Subskrypcja ma już kilka definicji zasad. Aby wyświetlić dostępne definicje zasad, użyj:

```azurepowershell-interactive
(Get-AzureRmPolicyDefinition).Properties | Format-Table displayName, policyType
```

Zostaną wyświetlone istniejące definicje zasad. Typ zasad to **Wbudowane** albo **Niestandardowe**. Przejrzyj definicje i znajdź te opisujące warunek, który chcesz przypisać. W tym artykule przypiszesz zasady określające następujące działania:

* ograniczenie lokalizacji dla wszystkich zasobów
* limit jednostek SKU maszyn wirtualnych
* Przeprowadź inspekcję maszyn wirtualnych, które nie korzystają z dysków zarządzanych

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

[!INCLUDE [Resource Manager governance locks](../../includes/resource-manager-governance-locks.md)]

### <a name="lock-a-resource"></a>Blokowanie zasobów

Aby zablokować maszyny wirtualnej i sieciowej grupy zabezpieczeń, należy użyć:

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

Tylko można usunąć maszyny wirtualnej, w szczególności w przypadku usunięcia blokady. Ten krok przedstawiono w sekcji [Oczyszczanie zasobów](#clean-up-resources).

## <a name="tag-resources"></a>Tagowanie zasobów

[!INCLUDE [Resource Manager governance tags](../../includes/resource-manager-governance-tags.md)]

### <a name="tag-resources"></a>Tagowanie zasobów

[!INCLUDE [Resource Manager governance tags Powershell](../../includes/resource-manager-governance-tags-powershell.md)]

Aby zastosować znaczniki do maszyny wirtualnej, należy użyć:

```azurepowershell-interactive
$r = Get-AzureRmResource -ResourceName myVM `
  -ResourceGroupName myResourceGroup `
  -ResourceType Microsoft.Compute/virtualMachines
Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test"; Project="Documentation" } -ResourceId $r.ResourceId -Force
```

### <a name="find-resources-by-tag"></a>Znajdowanie zasobów według tagów

Aby znaleźć zasoby z nazwę i wartość tagu, użyj:

```azurepowershell-interactive
(Find-AzureRmResource -TagName Environment -TagValue Test).Name
```

Zwróconych wartości można użyć na potrzeby zadań zarządzania, takich jak zatrzymanie wszystkich maszyn wirtualnych z daną wartością tagu.

```azurepowershell-interactive
Find-AzureRmResource -TagName Environment -TagValue Test | Where-Object {$_.ResourceType -eq "Microsoft.Compute/virtualMachines"} | Stop-AzureRmVM
```

### <a name="view-costs-by-tag-values"></a>Wyświetlanie kosztów według wartości tagów

Jeśli do zasobów zostaną zastosowane tagi, za pomocą tych tagów można wyświetlić koszty zasobów. Aby w analizie kosztów było wyświetlane najnowsze użycie, musi upłynąć trochę czasu, dlatego koszty mogą nie być jeszcze widoczne. Gdy koszty będą już dostępne, można wyświetlić koszty zasobów między grupami zasobów w ramach subskrypcji. Aby móc zobaczyć koszty, użytkownicy muszą mieć [dostęp do informacji o rozliczeniach na poziomie subskrypcji](../billing/billing-manage-access.md).

Aby w portalu wyświetlić koszty według tagów, wybierz subskrypcję, a następnie wybierz pozycję **Analiza kosztów**.

![Analiza kosztów](./media/powershell-azure-resource-manager/select-cost-analysis.png)

Następnie ustaw filtrowanie według wartości tagu i wybierz pozycję **Zastosuj**.

![Wyświetlanie kosztów według tagów](./media/powershell-azure-resource-manager/view-costs-by-tag.png)

Można również użyć [interfejsów API rozliczeń platformy Azure](../billing/billing-usage-rate-card-overview.md), aby programowo wyświetlić koszty.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Zablokowanej sieciowej grupy zabezpieczeń nie można usunąć, dopóki blokada nie zostanie zdjęta. Aby usunąć blokadę, należy użyć:

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

* Aby dowiedzieć się więcej o monitorowaniu maszyn wirtualnych, zobacz [monitorowania i aktualizowania maszyny wirtualnej Windows przy użyciu programu Azure PowerShell](../virtual-machines/windows/tutorial-monitoring.md).
* Aby dowiedzieć się więcej o korzystaniu z usługi Azure Security Center do zaimplementowania zalecanych zasad zabezpieczania, [monitorowanie zabezpieczeń maszyny wirtualnej przy użyciu usługi Azure Security Center](../virtual-machines/windows/tutorial-azure-security.md).
* Można przenieść istniejące zasoby, do nowej grupy zasobów. Aby uzyskać przykłady, zobacz [przeniesienia zasobów do nowej grupy zasobów lub subskrypcji](resource-group-move-resources.md).
* Aby uzyskać instrukcje dla przedsiębiorstw dotyczące użycia usługi Resource Manager w celu efektywnego zarządzania subskrypcjami, zobacz [Azure enterprise scaffold - prescriptive subscription governance](/azure/architecture/cloud-adoption-guide/subscription-governance) (Szkielet platformy Azure dla przedsiębiorstwa — narzucony nadzór subskrypcji).
