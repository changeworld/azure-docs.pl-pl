---
title: Ograniczanie instalacji rozszerzenia maszyny Wirtualnej za pomocą usługi Azure Policy
description: Użyj usługi Azure Policy, aby ograniczyć wdrożenia rozszerzeń.
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/23/2018
ms.author: akjosh
ms.reviewer: cynthn
ms.openlocfilehash: 428db340ce43463939ce71ffadd4188060f3e732
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74073110"
---
# <a name="use-azure-policy-to-restrict-extensions-installation-on-windows-vms"></a>Ograniczanie instalacji rozszerzeń na maszynach wirtualnych systemu Windows za pomocą usługi Azure Policy

Aby zapobiec używaniu lub instalacji niektórych rozszerzeń na maszynach wirtualnych systemu Windows, można utworzyć zasady platformy Azure przy użyciu programu PowerShell w celu ograniczenia rozszerzeń dla maszyn wirtualnych w ramach grupy zasobów. 

W tym samouczku używa programu Azure PowerShell w ramach powłoki chmury, która jest stale aktualizowana do najnowszej wersji. 

 

## <a name="create-a-rules-file"></a>Tworzenie pliku reguł

Aby ograniczyć, jakie rozszerzenia mogą być instalowane, należy mieć [regułę,](../../governance/policy/concepts/definition-structure.md#policy-rule) aby zapewnić logikę do identyfikowania rozszerzenia.

W tym przykładzie pokazano, jak odmówić rozszerzenia opublikowane przez "Microsoft.Compute" przez utworzenie pliku reguł w usłudze Azure Cloud Shell, ale jeśli pracujesz w programie PowerShell lokalnie, można również utworzyć plik lokalny i zastąpić ścieżkę ($home/clouddrive) ścieżką do pliku lokalnego na komputerze.

W [przysłonie chmury](https://shell.azure.com/powershell)wpisz:

```azurepowershell-interactive
nano $home/clouddrive/rules.json
```

Skopiuj i wklej następujący plik .json do pliku.

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

Po zakończeniu naciśnij **klawisze Ctrl + O,** a następnie **wprowadź,** aby zapisać plik. Naciśnij **klawisze Ctrl + X,** aby zamknąć plik i wyjść.

## <a name="create-a-parameters-file"></a>Tworzenie pliku parametrów

Potrzebny jest również plik [parametrów,](../../governance/policy/concepts/definition-structure.md#parameters) który tworzy strukturę, która będzie używana do przekazywania na liście rozszerzeń do zablokowania. 

W tym przykładzie pokazano, jak utworzyć plik parametrów dla maszyn wirtualnych w aplikacji Cloud Shell, ale jeśli pracujesz w programie PowerShell lokalnie, można również utworzyć plik lokalny i zastąpić ścieżkę ($home/clouddrive) ścieżką do pliku lokalnego na komputerze.

W [usłudze Cloud Shell](https://shell.azure.com/powershell)wpisz:

```azurepowershell-interactive
nano $home/clouddrive/parameters.json
```

Skopiuj i wklej następujący plik .json do pliku.

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

Po zakończeniu naciśnij **klawisze Ctrl + O,** a następnie **wprowadź,** aby zapisać plik. Naciśnij **klawisze Ctrl + X,** aby zamknąć plik i wyjść.

## <a name="create-the-policy"></a>Tworzenie zasad

Definicja zasad jest obiektem używanym do przechowywania konfiguracji, której chcesz użyć. Definicja zasad używa reguł i parametrów plików do definiowania zasad. Utwórz definicję zasad przy użyciu polecenia cmdlet [New-AzPolicyDefinition.](https://docs.microsoft.com/powershell/module/az.resources/new-azpolicydefinition)

 Reguły i parametry zasad to pliki utworzone i przechowywane jako pliki json w powłoce chmury.


```azurepowershell-interactive
$definition = New-AzPolicyDefinition `
   -Name "not-allowed-vmextension-windows" `
   -DisplayName "Not allowed VM Extensions" `
   -description "This policy governs which VM extensions that are explicitly denied."   `
   -Policy 'C:\Users\ContainerAdministrator\clouddrive\rules.json' `
   -Parameter 'C:\Users\ContainerAdministrator\clouddrive\parameters.json'
```




## <a name="assign-the-policy"></a>Przypisywanie zasad

W tym przykładzie przypisuje zasadę do grupy zasobów przy użyciu [new-AzPolicyAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azpolicyassignment). Żadna maszyna wirtualna utworzona w grupie zasobów **myResourceGroup** nie będzie mogła zainstalować rozszerzenia programu VM Access Agent ani Niestandardowe skrypty. 

Korzystanie z [programu Get-AzSubscription | Polecenie](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription) cmdlet formatu tabeli, aby uzyskać identyfikator subskrypcji do użycia zamiast jednego w przykładzie.

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

Aby przetestować zasady, spróbuj użyć rozszerzenia dostępu do maszyny Wirtualnej. Poniższy komunikat "Set-AzVMAccessExtension: Resource 'myVMAccess' został niedozwolony przez zasady."

```azurepowershell-interactive
Set-AzVMAccessExtension `
   -ResourceGroupName "myResourceGroup" `
   -VMName "myVM" `
   -Name "myVMAccess" `
   -Location EastUS 
```

W portalu zmiana hasła powinna zakończyć się niepowodzeniem z "Wdrożenie szablonu nie powiodło się z powodu naruszenia zasad." .

## <a name="remove-the-assignment"></a>Usuwanie przypisania

```azurepowershell-interactive
Remove-AzPolicyAssignment -Name not-allowed-vmextension-windows -Scope $scope
```

## <a name="remove-the-policy"></a>Usuwanie zasad

```azurepowershell-interactive
Remove-AzPolicyDefinition -Name not-allowed-vmextension-windows
```
    
## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji, zobacz [Azure Policy](../../governance/policy/overview.md).
