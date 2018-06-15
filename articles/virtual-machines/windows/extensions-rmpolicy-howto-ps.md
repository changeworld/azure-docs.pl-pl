---
title: Użyj zasad Azure, aby ograniczyć instalacja rozszerzenia maszyny Wirtualnej | Dokumentacja firmy Microsoft
description: Użyj zasad Azure, aby ograniczyć wdrożeń rozszerzenia.
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/23/2018
ms.author: danis;cynthn
ms.openlocfilehash: da5b0db997ba1aa0e998f6fe2645e955b490951d
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
ms.locfileid: "30248177"
---
# <a name="use-azure-policy-to-restrict-extensions-installation-on-windows-vms"></a>Umożliwia ograniczenie instalacji rozszerzeń na maszynach wirtualnych Windows Azure zasad

Jeśli chcesz uniemożliwić używanie lub instalacji niektórych rozszerzeń dla maszyn wirtualnych systemu Windows, możesz utworzyć zasady Azure przy użyciu programu PowerShell, aby ograniczyć rozszerzenia dla maszyn wirtualnych w grupie zasobów. 

W tym samouczku korzysta z programu Azure PowerShell w powłoce chmury jest stale aktualizowany do najnowszej wersji. Jeśli użytkownik chce zainstalować i używać środowiska PowerShell lokalnie, ten samouczek wymaga programu Azure PowerShell w wersji modułu 3,6 lub nowszej. Uruchom polecenie ` Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). 

## <a name="create-a-rules-file"></a>Tworzenie pliku reguł

Aby ograniczyć, jakie rozszerzenia można zainstalować, musisz mieć [reguły](/azure/azure-policy/policy-definition#policy-rule) zapewnienie logiki, aby zidentyfikować rozszerzenia.

W tym przykładzie przedstawiono sposób Odmów rozszerzenia opublikowanych przez "Microsoft.Compute", tworząc plik reguł w powłoce chmury Azure, ale jeśli pracujesz w środowisku PowerShell lokalnie, można również utworzyć lokalnego pliku i zastąpić ścieżki ($home/clouddrive) ze ścieżką do Plik lokalny na komputerze.

W [powłoki chmury](https://shell.azure.com/powershell), wpisz:

```azurepowershell-interactive
nano $home/clouddrive/rules.json
```

Skopiuj i wklej następujący JSON do pliku.

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.Compute/virtualMachines/extensions"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                "equals": "Microsoft.Compute"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/type",
                "in": "[parameters('notAllowedExtensions')]"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

Gdy wszystko będzie gotowe, kliknij przycisk **Ctrl + O** , a następnie **Enter** można zapisać pliku. Trafienia **Ctrl + X** zamknąć plik i zamknij.

## <a name="create-a-parameters-file"></a>Utwórz plik parametrów

Należy również [parametry](/azure/azure-policy/policy-definition#parameters) pliku strukturę do użycia dla przekazywania na liście rozszerzeń do blokowania. 

W tym przykładzie przedstawiono sposób tworzenia pliku parametrów dla maszyn wirtualnych w chmurze powłoki, ale jeśli pracujesz w środowisku PowerShell lokalnie, można również utworzyć lokalnego pliku i zastąpić ścieżki ($home/clouddrive) ze ścieżką do pliku lokalnego na tym komputerze.

W [powłoki chmury](https://shell.azure.com/powershell), wpisz:

```azurepowershell-interactive
nano $home/clouddrive/parameters.json
```

Skopiuj i wklej następujący JSON do pliku.

```json
{
    "notAllowedExtensions": {
        "type": "Array",
        "metadata": {
            "description": "The list of extensions that will be denied.",
            "strongType": "type",
            "displayName": "Denied extension"
        }
    }
}
```

Gdy wszystko będzie gotowe, kliknij przycisk **Ctrl + O** , a następnie **Enter** można zapisać pliku. Trafienia **Ctrl + X** zamknąć plik i zamknij.

## <a name="create-the-policy"></a>Tworzenie zasad

Definicja zasad jest obiekt służący do przechowywania konfiguracji, który chcesz użyć. Definicja zasad używa plików reguł i parametry do zdefiniowania zasad. Utwórz definicję zasad przy użyciu [AzureRmPolicyDefinition nowy](/powershell/module/azurerm.resources/new-azurermpolicydefinition) polecenia cmdlet.

 Reguły zasad i parametry są pliki tworzone i przechowywane jako pliki JSON w chmurze powłoki.


```azurepowershell-interactive
$definition = New-AzureRmPolicyDefinition `
   -Name "not-allowed-vmextension-windows" `
   -DisplayName "Not allowed VM Extensions" `
   -description "This policy governs which VM extensions that are explicitly denied."   `
   -Policy 'C:\Users\ContainerAdministrator\clouddrive\rules.json' `
   -Parameter 'C:\Users\ContainerAdministrator\clouddrive\parameters.json'
```




## <a name="assign-the-policy"></a>Przypisania zasad

W tym przykładzie przypisuje zasady grupy zasobów przy użyciu [AzureRMPolicyAssignment nowy](/powershell/module/azurerm.resources/new-azurermpolicyassignment). Wszystkie maszyny Wirtualnej utworzonej w **myResourceGroup** grupy zasobów nie będzie można zainstalować rozszerzenia maszyny Wirtualnej dostępu do agenta lub niestandardowego skryptu. 

Użyj [Get-AzureRMSubscription | Formatowanie tabeli](/powershell/module/azurerm.profile/get-azurermsubscription) polecenia cmdlet, aby uzyskać identyfikator subskrypcji do użycia zamiast co w przykładzie.

```azurepowershell-interactive
$scope = "/subscriptions/<subscription id>/resourceGroups/myResourceGroup"
$assignment = New-AzureRMPolicyAssignment `
   -Name "not-allowed-vmextension-windows" `
   -Scope $scope `
   -PolicyDefinition $definition `
   -PolicyParameter '{
    "notAllowedExtensions": {
        "value": [
            "VMAccessAgent",
            "CustomScriptExtension"
        ]
    }
}'
$assignment
```

## <a name="test-the-policy"></a>Testowanie zasad

Aby przetestować zasady, spróbuj użyć rozszerzenia dostępu do maszyny Wirtualnej. Następujące powinna zakończyć się niepowodzeniem z komunikatem "AzureRmVMAccessExtension zestaw:"myVMAccess"zasobu jest zabronione przez zasady."

```azurepowershell-interactive
Set-AzureRmVMAccessExtension `
   -ResourceGroupName "myResourceGroup" `
   -VMName "myVM" `
   -Name "myVMAccess" `
   -Location EastUS 
```

W portalu Zmienianie hasła powinna zakończyć się niepowodzeniem z "wdrożenie szablonu nie powiodło się z powodu naruszenia zasad." Komunikat.

## <a name="remove-the-assignment"></a>Usuń przypisanie

```azurepowershell-interactive
Remove-AzureRMPolicyAssignment -Name not-allowed-vmextension-windows -Scope $scope
```

## <a name="remove-the-policy"></a>Usuń zasady

```azurepowershell-interactive
Remove-AzureRmPolicyDefinition -Name not-allowed-vmextension-windows
```
    
## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji, zobacz [zasadami Azure](../../azure-policy/azure-policy-introduction.md).
