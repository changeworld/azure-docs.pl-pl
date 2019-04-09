---
title: Przykład — dozwolone jednostki SKU dla kont magazynu i maszyn wirtualnych
description: Te przykładowe definicje zasad wymagają, aby konta magazynu i maszyny wirtualne korzystały z zatwierdzonych jednostek SKU.
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 01/23/2019
ms.author: dacoulte
ms.openlocfilehash: 088dd8414c93b609326e7cd72828426dcb00fc28
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59274573"
---
# <a name="sample---allowed-skus-for-storage-accounts-and-virtual-machines"></a>Przykład — dozwolone jednostki SKU dla kont magazynu i maszyn wirtualnych

Te zasady wymagają, aby konta magazynu i maszyny wirtualne używały zatwierdzonych jednostek SKU. Używają wbudowanych zasad w celu zapewnienia zatwierdzonych jednostek SKU. Do Ciebie należy określenie tablicy z zatwierdzonymi jednostkami SKU maszyn wirtualnych i tablicy z zatwierdzonymi jednostkami SKU kont magazynu.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Przykładowy szablon

[!code-json[main](../../../../policy-templates/samples/PolicyInitiatives/skus-for-multiple-types/azurepolicyset.json "Allowed SKUs for Storage Accounts and Virtual Machines")]

Ten szablon można wdrożyć przy użyciu [witryny Azure Portal](#deploy-with-the-portal) lub [programu PowerShell](#deploy-with-powershell).

## <a name="deploy-with-the-portal"></a>Wdrażanie przy użyciu portalu

[![Dprzykładowe zasady na platformie Azure eploy](https://azuredeploy.net/deploybutton.png)](https://aka.ms/getpolicy)

## <a name="deploy-with-powershell"></a>Wdrażanie przy użyciu programu PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

```azurepowershell-interactive
$policydefinitions = "https://raw.githubusercontent.com/Azure/azure-policy/master/samples/PolicyInitiatives/skus-for-multiple-types/azurepolicyset.definitions.json"
$policysetparameters = "https://raw.githubusercontent.com/Azure/azure-policy/master/samples/PolicyInitiatives/skus-for-multiple-types/azurepolicyset.parameters.json"

$policyset= New-AzPolicySetDefinition -Name "skus-for-multiple-types" -DisplayName "Allowed SKUs for Storage Accounts and Virtual Machines" -Description "This policy allows you to speficy what skus are allowed for storage accounts and virtual machines" -PolicyDefinition $policydefinitions -Parameter $policysetparameters 
 
New-AzPolicyAssignment -PolicySetDefinition $policyset -Name <assignmentName> -Scope <scope>  -LISTOFALLOWEDSKUS_1 <VM SKUs> -LISTOFALLOWEDSKUS_2 <Storage Account SKUs>
```

### <a name="clean-up-powershell-deployment"></a>Czyszczenie po wdrożeniu przy użyciu PowerShell

Uruchom następujące polecenie, aby usunąć przypisanie zasad i definicję.

```azurepowershell-interactive
Remove-AzPolicyAssignment -Name <assignmentName>
Remove-AzPolicySetDefinitions -Name "skus-for-multiple-types"
```

## <a name="deploy-with-azure-cli"></a>Wdrażanie przy użyciu interfejsu wiersza polecenia platformy Azure

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

```azurecli-interactive
az policy set-definition create --name "skus-for-multiple-types" --display-name "Allowed SKUs for Storage Accounts and Virtual Machines" --description "This policy allows you to speficy what skus are allowed for storage accounts and virtual machines" --definitions "https://raw.githubusercontent.com/Azure/azure-policy/master/samples/PolicyInitiatives/skus-for-multiple-types/azurepolicyset.definitions.json" --params "https://raw.githubusercontent.com/Azure/azure-policy/master/samples/PolicyInitiatives/skus-for-multiple-types/azurepolicyset.parameters.json"

az policy assignment create --name <assignmentName> --scope <scope> --policy-set-definition "skus-for-multiple-types" --params "{ 'LISTOFALLOWEDSKUS_1': { 'value': <VM SKU Array> }, 'LISTOFALLOWEDSKUS_2': { 'value': <Storage Account SKU Array> } }"
```

### <a name="clean-up-azure-cli-deployment"></a>Czyszczenie wdrożenia przeprowadzonego za pomocą interfejsu wiersza polecenia platformy Azure

Uruchom następujące polecenie, aby usunąć przypisanie zasad i definicję.

```azurecli-interactive
az policy assignment delete --name <assignmentName>
az policy set-definition delete --name "skus-for-multiple-types"
```

## <a name="next-steps"></a>Kolejne kroki

- Zobacz więcej przykładów w witrynie [Przykłady dla usługi Azure Policy](index.md)