---
title: Przekroczono limit przydziału wdrożenia
description: Opisuje sposób rozwiązywania problemów z więcej niż 800 wdrożeniami w historii grupy zasobów.
ms.topic: troubleshooting
ms.date: 10/04/2019
ms.openlocfilehash: 7f389827513562a3add67f022fec360081754b02
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75477826"
---
# <a name="resolve-error-when-deployment-count-exceeds-800"></a>Usuń błąd, gdy liczba wdrożeń przekracza 800

Każda grupa zasobów jest ograniczona do 800 wdrożeń w swojej historii wdrażania. W tym artykule opisano błąd pojawiający się w przypadku niepowodzenia wdrożenia, ponieważ spowodowałoby to przekroczenie dozwolonych wdrożeń 800. Aby rozwiązać ten problem, Usuń wdrożenia z historii grupy zasobów. Usunięcie wdrożenia z historii nie ma wpływu na żadne wdrożone zasoby.

## <a name="symptom"></a>Objaw

Podczas wdrażania zostanie wyświetlony komunikat o błędzie informujący o tym, że bieżące wdrożenie przekroczy przydział 800 wdrożeń.

## <a name="solution"></a>Rozwiązanie

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Użyj polecenia [AZ Group Deployment Delete](/cli/azure/group/deployment#az-group-deployment-delete) , aby usunąć wdrożenia z historii.

```azurecli-interactive
az group deployment delete --resource-group exampleGroup --name deploymentName
```

Aby usunąć wszystkie wdrożenia starsze niż pięć dni, użyj:

```azurecli-interactive
startdate=$(date +%F -d "-5days")
deployments=$(az group deployment list --resource-group exampleGroup --query "[?properties.timestamp<'$startdate'].name" --output tsv)

for deployment in $deployments
do
  az group deployment delete --resource-group exampleGroup --name $deployment
done
```

Bieżącą liczbę można uzyskać w historii wdrożenia za pomocą następującego polecenia:

```azurecli-interactive
az group deployment list --resource-group exampleGroup --query "length(@)"
```

### <a name="azure-powershell"></a>Program Azure PowerShell

Użyj polecenia [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment) , aby usunąć wdrożenia z historii.

```azurepowershell-interactive
Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name deploymentName
```

Aby usunąć wszystkie wdrożenia starsze niż pięć dni, użyj:

```azurepowershell-interactive
$deployments = Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup | Where-Object Timestamp -lt ((Get-Date).AddDays(-5))

foreach ($deployment in $deployments) {
  Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name $deployment.DeploymentName
}
```

Bieżącą liczbę można uzyskać w historii wdrożenia za pomocą następującego polecenia:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup).Count
```

## <a name="third-party-solutions"></a>Rozwiązania innych firm

Poniższe scenariusze dotyczące adresów zewnętrznych:

* [Rozwiązania Azure Logic Apps i programu PowerShell](https://devkimchi.com/2018/05/30/managing-excessive-arm-deployment-histories-with-logic-apps/)
* [Rozszerzenie AzDevOps](https://github.com/christianwaha/AzureDevOpsExtensionCleanRG)
