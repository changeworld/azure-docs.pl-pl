---
title: Przykładowe zasady usługi Azure Policy — Uniemożliwianie korzystania z sieci równorzędnych w sieci ER
description: Te przykładowe zasady uniemożliwiają skojarzenie sieci równorzędnych z siecią znajdującą się w określonej grupie zasobów.
services: azure-policy
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 09/18/2018
ms.author: dacoulte
ms.custom: mvc
ms.openlocfilehash: 4754bcf210ba735d60ae17d76438763bdfca435d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46985235"
---
# <a name="no-network-peering-to-er-network"></a>Uniemożliwianie korzystania z sieci równorzędnych w sieci ER

Te zasady uniemożliwiają skojarzenie sieci równorzędnej z siecią znajdującą się w określonej grupie zasobów. Pozwalają one zapobiegać nawiązywaniu połączeń z centralnie zarządzaną infrastrukturą sieci. Należy określić nazwę grupy zasobów, aby uniemożliwić skojarzenie.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Przykładowy szablon

[!code-json[main](../../../../policy-templates/samples/Network/no-network-peerings-to-er-network/azurepolicy.json "No network peering to ER network")]

Ten szablon można wdrożyć przy użyciu [witryny Azure Portal](#deploy-with-the-portal) lub [programu PowerShell](#deploy-with-powershell) albo [interfejsu wiersza polecenia platformy Azure](#deploy-with-azure-cli).

## <a name="deploy-with-the-portal"></a>Wdrażanie przy użyciu portalu

[![Wdrażanie na platformie Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?feature.customportal=false&microsoft_azure_policy=true&microsoft_azure_policy_policyinsights=true&feature.microsoft_azure_security_policy=true&microsoft_azure_marketplace_policy=true#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FNetwork%2Fno-network-peerings-to-er-network%2Fazurepolicy.json)

## <a name="deploy-with-powershell"></a>Wdrażanie przy użyciu programu PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh.md)]

```azurepowershell-interactive
$definition = New-AzureRmPolicyDefinition -Name "no-network-peerings-to-er-network" -DisplayName "No network peering to ER network" -description "No network peering can be associated to networks in network in a resource group containing central managed network infrastructure." -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/no-network-peerings-to-er-network/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/no-network-peerings-to-er-network/azurepolicy.parameters.json' -Mode All
$definition
$assignment = New-AzureRMPolicyAssignment -Name <assignmentname> -Scope <scope>  -resourceGroupName <ER Network Resource Group Name> -PolicyDefinition $definition
$assignment
```

### <a name="clean-up-powershell-deployment"></a>Czyszczenie po wdrożeniu przy użyciu PowerShell

Uruchom następujące polecenie, aby usunąć grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="deploy-with-azure-cli"></a>Wdrażanie przy użyciu interfejsu wiersza polecenia platformy Azure

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

```azurecli-interactive
az policy definition create --name 'no-network-peerings-to-er-network' --display-name 'No network peering to ER network' --description 'No network peering can be associated to networks in network in a resource group containing central managed network infrastructure.' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/no-network-peerings-to-er-network/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/no-network-peerings-to-er-network/azurepolicy.parameters.json' --mode All

az policy assignment create --name <assignmentname> --scope <scope> --policy "no-network-peerings-to-er-network"
```

### <a name="clean-up-azure-cli-deployment"></a>Czyszczenie wdrożenia przeprowadzonego za pomocą interfejsu wiersza polecenia platformy Azure

Uruchom następujące polecenie, aby usunąć grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Następne kroki

- Zobacz więcej przykładów w temacie [Przykłady dla usługi Azure Policy](index.md)