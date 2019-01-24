---
title: Przykład — wymuszanie tagu i jego wartości na grupach zasobów
description: Ta przykładowa zasada wymaga użycia tagu i wartości na grupie zasobów.
services: azure-policy
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 01/23/2019
ms.author: dacoulte
ms.openlocfilehash: 28154d8e48afe9157fff52aff830f0fde25edd25
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2019
ms.locfileid: "54845337"
---
# <a name="enforce-tag-and-its-value-on-resource-groups"></a>Wymuszanie tagu i jego wartości na grupach zasobów

Ta zasada wymaga użycia tagu i wartości na grupie zasobów. Należy określić wymaganą nazwę i wartość tagu.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Przykładowy szablon

[!code-json[main](../../../../policy-templates/samples/ResourceGroup/enforce-resourceGroup-tags/azurepolicy.json "Enforce tag and its value on resource groups")]

Ten szablon można wdrożyć przy użyciu [witryny Azure Portal](#deploy-with-the-portal) lub [programu PowerShell](#deploy-with-powershell) albo [interfejsu wiersza polecenia platformy Azure](#deploy-with-azure-cli).

## <a name="deploy-with-the-portal"></a>Wdrażanie przy użyciu portalu

[![Wdrażanie na platformie Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?feature.customportal=false&microsoft_azure_policy=true&microsoft_azure_policy_policyinsights=true&feature.microsoft_azure_security_policy=true&microsoft_azure_marketplace_policy=true#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FResourceGroup%2Fenforce-resourceGroup-tags%2Fazurepolicy.json)

## <a name="deploy-with-powershell"></a>Wdrażanie przy użyciu programu PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh.md)]

```azurepowershell-interactive
$definition = New-AzPolicyDefinition -Name "enforce-resourceGroup-tags" -DisplayName "Enforce tag and its value on resource groups" -description "Enforces a required tag and its value on resource groups." -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/ResourceGroup/enforce-resourceGroup-tags/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/ResourceGroup/enforce-resourceGroup-tags/azurepolicy.parameters.json' -Mode All
$definition
$assignment = New-AzPolicyAssignment -Name <assignmentname> -Scope <scope>  -tagName <tagName> -tagValue <tagValue> -PolicyDefinition $definition
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
az policy definition create --name 'enforce-resourceGroup-tags' --display-name 'Enforce tag and its value on resource groups' --description 'Enforces a required tag and its value on resource groups.' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/ResourceGroup/enforce-resourceGroup-tags/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/ResourceGroup/enforce-resourceGroup-tags/azurepolicy.parameters.json' --mode All

az policy assignment create --name <assignmentname> --scope <scope> --policy "enforce-resourceGroup-tags"
```

### <a name="clean-up-azure-cli-deployment"></a>Czyszczenie wdrożenia przeprowadzonego za pomocą interfejsu wiersza polecenia platformy Azure

Uruchom następujące polecenie, aby usunąć grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Następne kroki

- Zobacz więcej przykładów w witrynie [Przykłady dla usługi Azure Policy](index.md)