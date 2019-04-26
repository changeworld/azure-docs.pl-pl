---
title: Ograniczanie instalacji rozszerzeń maszyny Wirtualnej za pomocą usługi Azure Policy | Dokumentacja firmy Microsoft
description: Użyj usługi Azure Policy, aby ograniczyć rozszerzenia wdrożeń.
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/23/2018
ms.author: roiyz;cynthn
ms.openlocfilehash: dfaeff5e8f90e72645293d15c454f78f17b08fb0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60387336"
---
# <a name="use-azure-policy-to-restrict-extensions-installation-on-windows-vms"></a>Ograniczanie instalacji rozszerzeń na maszynach wirtualnych Windows za pomocą usługi Azure Policy

Jeśli chcesz uniemożliwić używanie lub instalacji niektórych rozszerzeń na maszyn wirtualnych z systemem Windows, można utworzyć zasad platformy Azure przy użyciu programu PowerShell, aby ograniczyć rozszerzenia dla maszyn wirtualnych w grupie zasobów. 

Ten samouczek używa programu Azure PowerShell w usłudze Cloud Shell, które są stale aktualizowane do najnowszej wersji. 

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="create-a-rules-file"></a>Utwórz plik reguł

Aby można było ograniczyć, jakie rozszerzenia można zainstalować, musisz mieć [reguły](../../governance/policy/concepts/definition-structure.md#policy-rule) zapewnienie logiki, aby zidentyfikować rozszerzenia.

W tym przykładzie pokazano, jak odmowa rozszerzenia publikowane przez "Microsoft.Compute" przez utworzenie pliku reguły w usłudze Azure Cloud Shell, ale jeśli pracujesz w programie PowerShell lokalnie, możesz również utworzyć lokalny plik i zastąpić ścieżkę ($home/clouddrive) przy użyciu ścieżki do Plik lokalny na komputerze.

W [Cloud Shell](https://shell.azure.com/powershell), wpisz:

```azurepowershell-interactive
nano $home/clouddrive/rules.json
```

Skopiuj i wklej następujące JSON do pliku.

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

Gdy wszystko będzie gotowe, trafienia **Ctrl + O** i następnie **Enter** można zapisać pliku. Trafienia **Ctrl + X** zamknąć plik i zamknij.

## <a name="create-a-parameters-file"></a>Utwórz plik parametrów

Należy również [parametry](../../governance/policy/concepts/definition-structure.md#parameters) pliku, który tworzy strukturę do użycia do przekazania na liście rozszerzeń, aby zablokować. 

W tym przykładzie pokazano, jak utworzyć plik parametrów dla maszyn wirtualnych w usłudze Cloud Shell, ale jeśli pracujesz w programie PowerShell lokalnie, możesz również utworzyć lokalny plik i zastąpić ścieżkę ($home/clouddrive) ze ścieżką do pliku lokalnego na komputerze.

W [Cloud Shell](https://shell.azure.com/powershell), wpisz:

```azurepowershell-interactive
nano $home/clouddrive/parameters.json
```

Skopiuj i wklej następujące JSON do pliku.

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

Gdy wszystko będzie gotowe, trafienia **Ctrl + O** i następnie **Enter** można zapisać pliku. Trafienia **Ctrl + X** zamknąć plik i zamknij.

## <a name="create-the-policy"></a>Tworzenie zasad

Definicja zasad jest obiekt służący do przechowywania konfiguracji, który chcesz użyć. Definicja zasad używa plików zasady i parametry do zdefiniowania zasad. Tworzenie definicji zasad za pomocą [New AzPolicyDefinition](https://docs.microsoft.com/powershell/module/az.resources/new-azpolicydefinition) polecenia cmdlet.

 Zasady i parametry są pliki tworzone i przechowywane jako pliki JSON w usługi cloud shell.


```azurepowershell-interactive
$definition = New-AzPolicyDefinition `
   -Name "not-allowed-vmextension-windows" `
   -DisplayName "Not allowed VM Extensions" `
   -description "This policy governs which VM extensions that are explicitly denied."   `
   -Policy 'C:\Users\ContainerAdministrator\clouddrive\rules.json' `
   -Parameter 'C:\Users\ContainerAdministrator\clouddrive\parameters.json'
```




## <a name="assign-the-policy"></a>Przypisz zasady

W tym przykładzie przypisuje grupę zasobów za pomocą zasad [New AzPolicyAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azpolicyassignment). Wszystkie maszyny Wirtualnej utworzonej w **myResourceGroup** grupy zasobów nie będzie można zainstalować rozszerzenia maszyny Wirtualnej dostępu agenta lub skryptu niestandardowego. 

Użyj [Get AzSubscription | Format-Table](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription) polecenie cmdlet umożliwiające uzyskanie Identyfikatora subskrypcji należy użyć zamiast co w przykładzie.

```azurepowershell-interactive
$scope = "/subscriptions/<subscription id>/resourceGroups/myResourceGroup"
$assignment = New-AzPolicyAssignment `
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

Aby sprawdzić zasady, spróbuj użyć rozszerzenia VM Access. Następujące powinna zakończyć się niepowodzeniem z komunikatem "AzVMAccessExtension zestawu: Zasób "myVMAccess" zostało zabronione przez zasady."

```azurepowershell-interactive
Set-AzVMAccessExtension `
   -ResourceGroupName "myResourceGroup" `
   -VMName "myVM" `
   -Name "myVMAccess" `
   -Location EastUS 
```

W portalu zmiana hasła powinna zakończyć się niepowodzeniem z "wdrożenie szablonu nie powiodło się z powodu naruszenia zasad." Komunikat.

## <a name="remove-the-assignment"></a>Usuwanie przypisania

```azurepowershell-interactive
Remove-AzPolicyAssignment -Name not-allowed-vmextension-windows -Scope $scope
```

## <a name="remove-the-policy"></a>Usuń zasady

```azurepowershell-interactive
Remove-AzPolicyDefinition -Name not-allowed-vmextension-windows
```
    
## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji, zobacz [usługi Azure Policy](../../governance/policy/overview.md).
