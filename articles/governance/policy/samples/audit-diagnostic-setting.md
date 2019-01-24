---
title: Przykład — przeprowadzanie inspekcji ustawienia diagnostyki
description: Te przykładowe zasady przeprowadzają inspekcję, czy ustawienia diagnostyki nie są włączone dla określonych typów zasobów.
services: azure-policy
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 01/23/2019
ms.author: dacoulte
ms.openlocfilehash: db043cff92a0902a606639746d7599491d2395b5
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2019
ms.locfileid: "54844181"
---
# <a name="audit-diagnostic-setting"></a>Przeprowadzanie inspekcji ustawienia diagnostyki

Te wbudowane zasady przeprowadzają inspekcję, czy ustawienia diagnostyki nie są włączone dla określonych typów zasobów. Należy określić tablicę typów zasobów, aby sprawdzić, czy ustawienia diagnostyki są włączone.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Przykładowy szablon

[!code-json[main](../../../../policy-templates/samples/Monitoring/audit-diagnostic-setting/azurepolicy.json "Audit diagnostic setting")]

Ten szablon można wdrożyć przy użyciu [witryny Azure Portal](#deploy-with-the-portal) lub [programu PowerShell](#deploy-with-powershell) albo [interfejsu wiersza polecenia platformy Azure](#deploy-with-azure-cli). Aby uzyskać dostęp do wbudowanych zasad, użyj identyfikatora `7f89b1eb-583c-429a-8828-af049802c1d9`.

## <a name="parameters"></a>Parametry

Aby przekazać wartości parametru, użyj następującego formatu:

```json
{"listOfResourceTypes":{"value":["Microsoft.Cache/Redis","Microsoft.Compute/virtualmachines"]}}
```

## <a name="deploy-with-the-portal"></a>Wdrażanie przy użyciu portalu

Podczas przypisywania zasad wybierz pozycję **Przeprowadź inspekcję ustawienia diagnostyki** z dostępnych wbudowanych definicji.

## <a name="deploy-with-powershell"></a>Wdrażanie przy użyciu programu PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh.md)]

```azurepowershell-interactive
$definition = Get-AzPolicyDefinition -Id /providers/Microsoft.Authorization/policyDefinitions/7f89b1eb-583c-429a-8828-af049802c1d9

New-AzPolicyAssignment -name "Audit diagnostics" -PolicyDefinition $definition -PolicyParameter '{"listOfResourceTypes":{"value":["Microsoft.Cache/Redis","Microsoft.Compute/virtualmachines"]}}' -Scope <scope>
```

### <a name="clean-up-powershell-deployment"></a>Czyszczenie po wdrożeniu przy użyciu PowerShell

Uruchom następujące polecenie, aby usunąć grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby.

```azurepowershell-interactive
Remove-AzPolicyAssignment -Name "Audit diagnostics" -Scope <scope>
```

## <a name="deploy-with-azure-cli"></a>Wdrażanie przy użyciu interfejsu wiersza polecenia platformy Azure

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

```azurecli-interactive
az policy assignment create --scope <scope> --name "Audit diagnostics" --policy 7f89b1eb-583c-429a-8828-af049802c1d9 --params '{"listOfResourceTypes":{"value":["Microsoft.Cache/Redis","Microsoft.Compute/virtualmachines"]}}'
```

### <a name="clean-up-azure-cli-deployment"></a>Czyszczenie wdrożenia przeprowadzonego za pomocą interfejsu wiersza polecenia platformy Azure

Uruchom następujące polecenie, aby usunąć grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby.

```azurecli-interactive
az policy assignment delete --name "Audit diagnostics" --resource-group myResourceGroup
```

## <a name="next-steps"></a>Następne kroki

- Zobacz więcej przykładów w witrynie [Przykłady dla usługi Azure Policy](index.md)