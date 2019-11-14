---
title: Przykład — sprawdzanie, czy rozszerzenie nie istnieje
description: Te przykładowe definicje zasad sprawdzają, czy rozszerzenie nie zostało wdrożone z maszyną wirtualną.
ms.date: 01/23/2019
ms.topic: sample
ms.openlocfilehash: 72d7728ef89678998a5fc3726d643142e3842635
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74071447"
---
# <a name="sample---audit-if-extension-does-not-exist"></a>Przykład — sprawdzanie, czy rozszerzenie nie istnieje

Te zasady sprawdzają, czy rozszerzenie nie zostało wdrożone z maszyną wirtualną. Należy określić typ i wydawcę rozszerzenia, aby sprawdzić, czy zostało ono wdrożone.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Przykładowy szablon

[!code-json[main](../../../../policy-templates/samples/compute/audit-vm-extension/azurepolicy.json "Audit if extension does not exist")]

Ten szablon można wdrożyć przy użyciu [witryny Azure Portal](#deploy-with-the-portal) lub [programu PowerShell](#deploy-with-powershell) albo [interfejsu wiersza polecenia platformy Azure](#deploy-with-azure-cli).

## <a name="deploy-with-the-portal"></a>Wdrażanie przy użyciu portalu

[![Wdróż przykład zasad na platformie Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?feature.customportal=false&microsoft_azure_policy=true&microsoft_azure_policy_policyinsights=true&feature.microsoft_azure_security_policy=true&microsoft_azure_marketplace_policy=true#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FCompute%2Faudit-vm-extension%2Fazurepolicy.json)

## <a name="deploy-with-powershell"></a>Wdrażanie przy użyciu programu PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

```azurepowershell-interactive
$definition = New-AzPolicyDefinition -Name "audit-vm-extension" -DisplayName "Audit if extension does not exist" -description "This policy audits if a required extension doesn't exist." -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/audit-vm-extension/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/audit-vm-extension/azurepolicy.parameters.json' -Mode All
$definition
$assignment = New-AzPolicyAssignment -Name <assignmentname> -Scope <scope>  -publisher <Extension Publisher> -type <Extension Type> -PolicyDefinition $definition
$assignment
```

### <a name="clean-up-powershell-deployment"></a>Czyszczenie po wdrożeniu przy użyciu PowerShell

Uruchom następujące polecenie, aby usunąć grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="deploy-with-azure-cli"></a>Wdrażanie przy użyciu interfejsu wiersza polecenia platformy Azure

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

```azurecli-interactive
az policy definition create --name 'audit-vm-extension' --display-name 'Audit if extension does not exist' --description 'This policy audits if a required extension doesn't exist.' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/audit-vm-extension/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/audit-vm-extension/azurepolicy.parameters.json' --mode All

az policy assignment create --name <assignmentname> --scope <scope> --policy "audit-vm-extension"
```

### <a name="clean-up-azure-cli-deployment"></a>Czyszczenie wdrożenia przeprowadzonego za pomocą interfejsu wiersza polecenia platformy Azure

Uruchom następujące polecenie, aby usunąć grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Następne kroki

- Przejrzyj więcej przykładów w temacie [Przykłady dla usługi Azure Policy](index.md)