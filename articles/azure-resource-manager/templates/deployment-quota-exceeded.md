---
title: Przekroczono przydział wdrożenia
description: W tym artykule opisano sposób rozwiązywania problemu z posiadaniem więcej niż 800 wdrożeń w historii grupy zasobów.
ms.topic: troubleshooting
ms.date: 10/04/2019
ms.openlocfilehash: 919cd9a3482401cd47516e2677b0bf58387488b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80245093"
---
# <a name="resolve-error-when-deployment-count-exceeds-800"></a>Rozwiązywanie problemów, gdy liczba wdrożeń przekracza 800

Każda grupa zasobów jest ograniczona do 800 wdrożeń w historii wdrażania. W tym artykule opisano błąd, który pojawia się, gdy wdrożenie nie powiedzie się, ponieważ przekracza dozwolone 800 wdrożeń. Aby rozwiązać ten problem, usuń wdrożenia z historii grupy zasobów. Usunięcie wdrożenia z historii nie ma wpływu na żadne zasoby, które zostały wdrożone.

## <a name="symptom"></a>Objaw

Podczas wdrażania pojawia się błąd informujący, że bieżące wdrożenie przekroczy przydział 800 wdrożeń.

## <a name="solution"></a>Rozwiązanie

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Użyj polecenia [usuń grupy wdrażania AZ,](/cli/azure/group/deployment) aby usunąć wdrożenia z historii.

```azurecli-interactive
az deployment group delete --resource-group exampleGroup --name deploymentName
```

Aby usunąć wszystkie wdrożenia starsze niż pięć dni, należy użyć:

```azurecli-interactive
startdate=$(date +%F -d "-5days")
deployments=$(az deployment group list --resource-group exampleGroup --query "[?properties.timestamp<'$startdate'].name" --output tsv)

for deployment in $deployments
do
  az deployment group delete --resource-group exampleGroup --name $deployment
done
```

Bieżącą liczbę w historii wdrażania można uzyskać za pomocą następującego polecenia:

```azurecli-interactive
az deployment group list --resource-group exampleGroup --query "length(@)"
```

### <a name="azure-powershell"></a>Azure PowerShell

Użyj polecenia [Usuń-AzResourceGroupDeployment,](/powershell/module/az.resources/remove-azresourcegroupdeployment) aby usunąć wdrożenia z historii.

```azurepowershell-interactive
Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name deploymentName
```

Aby usunąć wszystkie wdrożenia starsze niż pięć dni, należy użyć:

```azurepowershell-interactive
$deployments = Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup | Where-Object Timestamp -lt ((Get-Date).AddDays(-5))

foreach ($deployment in $deployments) {
  Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name $deployment.DeploymentName
}
```

Bieżącą liczbę w historii wdrażania można uzyskać za pomocą następującego polecenia:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup).Count
```

## <a name="third-party-solutions"></a>Rozwiązania innych firm

Następujące rozwiązania zewnętrzne dotyczą określonych scenariuszy:

* [Usługi Azure Logic Apps i rozwiązania powershell](https://devkimchi.com/2018/05/30/managing-excessive-arm-deployment-histories-with-logic-apps/)
* [Rozszerzenie AzDevOps](https://github.com/christianwaha/AzureDevOpsExtensionCleanRG)
