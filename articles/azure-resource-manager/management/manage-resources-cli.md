---
title: Zarządzanie zasobami — narzędzie Azure CLI
description: Zarządzanie zasobami za pomocą interfejsu wiersza polecenia platformy Azure i usługi Azure Resource Manager. Pokazuje, jak wdrażać i usuwać zasoby.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: f00c1725201b0e49f80fec64e5d69b375ec7a233
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75485535"
---
# <a name="manage-azure-resources-by-using-azure-cli"></a>Zarządzanie zasobami platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure

Dowiedz się, jak używać interfejsu wiersza polecenia platformy Azure za pomocą [usługi Azure Resource Manager](overview.md) do zarządzania zasobami platformy Azure. Aby zarządzać grupami zasobów, zobacz [Zarządzanie grupami zasobów platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](manage-resource-groups-cli.md).

Inne artykuły na temat zarządzania zasobami:

- [Zarządzanie zasobami platformy Azure przy użyciu witryny Azure portal](manage-resources-portal.md)
- [Zarządzanie zasobami platformy Azure przy użyciu programu Azure PowerShell](manage-resources-powershell.md)

## <a name="deploy-resources-to-an-existing-resource-group"></a>Wdrażanie zasobów w istniejącej grupie zasobów

Zasoby platformy Azure można wdrożyć bezpośrednio przy użyciu interfejsu wiersza polecenia platformy Azure lub wdrożyć szablon Usługi Resource Manager w celu utworzenia zasobów platformy Azure.

### <a name="deploy-a-resource"></a>Wdrażanie zasobu

Poniższy skrypt tworzy konto magazynu.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account create --resource-group $resourceGroupName --name $storageAccountName --location $location --sku Standard_LRS --kind StorageV2 &&
az storage account show --resource-group $resourceGroupName --name $storageAccountName 
```

### <a name="deploy-a-template"></a>Wdrażanie szablonu

Poniższy skrypt tworzy wdrożenie szablonu przewodnika Szybki start w celu utworzenia konta magazynu. Aby uzyskać więcej informacji, zobacz [Szybki start: Tworzenie szablonów usługi Azure Resource Manager przy użyciu programu Visual Studio Code](../templates/quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell).

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group deployment create --resource-group $resourceGroupName --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

Aby uzyskać więcej informacji, zobacz [Wdrażanie zasobów za pomocą szablonów Usługi Resource Manager i interfejsu wiersza polecenia platformy Azure](../templates/deploy-cli.md).

## <a name="deploy-a-resource-group-and-resources"></a>Wdrażanie grupy zasobów i zasobów

Można utworzyć grupę zasobów i wdrożyć zasoby w grupie. Aby uzyskać więcej informacji, zobacz [Tworzenie grupy zasobów i wdrażanie zasobów](../templates/deploy-to-subscription.md#resource-group-and-resources).

## <a name="deploy-resources-to-multiple-subscriptions-or-resource-groups"></a>Wdrażanie zasobów w wielu subskrypcjach lub grupach zasobów

Zazwyczaj wszystkie zasoby w szablonie można wdrożyć w jednej grupie zasobów. Istnieją jednak scenariusze, w których chcesz wdrożyć zestaw zasobów razem, ale umieścić je w różnych grupach zasobów lub subskrypcji. Aby uzyskać więcej informacji, zobacz [Wdrażanie zasobów platformy Azure w wielu subskrypcjach lub grupach zasobów](../templates/cross-resource-group-deployment.md).

## <a name="delete-resources"></a>Usuwanie zasobów

W poniższym skrypcie pokazano, jak usunąć konto magazynu.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account delete --resource-group $resourceGroupName --name $storageAccountName 
```

Aby uzyskać więcej informacji o tym, jak usługa Azure Resource Manager zamawia usuwanie zasobów, zobacz [Usuwanie grupy zasobów usługi Azure Resource Manager](delete-resource-group.md).

## <a name="move-resources"></a>Przenoszenie zasobów

W poniższym skrypcie pokazano, jak usunąć konto magazynu z jednej grupy zasobów do innej grupy zasobów.

```azurecli-interactive
echo "Enter the source Resource Group name:" &&
read srcResourceGroupName &&
echo "Enter the destination Resource Group name:" &&
read destResourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
storageAccount=$(az resource show --resource-group $srcResourceGroupName --name $storageAccountName --resource-type Microsoft.Storage/storageAccounts --query id --output tsv) &&
az resource move --destination-group $destResourceGroupName --ids $storageAccount
```

Aby uzyskać więcej informacji, zobacz [Move resources to new resource group or subscription](move-resource-group-and-subscription.md) (Przenoszenie zasobów do nowej grupy lub subskrypcji).

## <a name="lock-resources"></a>Blokowanie zasobów

Blokowanie uniemożliwia innym użytkownikom w organizacji przypadkowe usunięcie lub zmodyfikowanie krytycznych zasobów, takich jak subskrypcja platformy Azure, grupa zasobów lub zasób. 

Poniższy skrypt blokuje konto magazynu, aby nie można było usunąć konta.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az lock create --name LockSite --lock-type CanNotDelete --resource-group $resourceGroupName --resource-name $storageAccountName --resource-type Microsoft.Storage/storageAccounts 
```

Następujący skrypt pobiera wszystkie blokady dla konta magazynu:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az lock list --resource-group $resourceGroupName --resource-name $storageAccountName --resource-type Microsoft.Storage/storageAccounts --parent ""
```

Następujący skrypt usuwa blokadę konta magazynu:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
lockId=$(az lock show --name LockSite --resource-group $resourceGroupName --resource-type Microsoft.Storage/storageAccounts --resource-name $storageAccountName --output tsv --query id)&&
az lock delete --ids $lockId
```

Aby uzyskać więcej informacji, zobacz [Lock resources with Azure Resource Manager](lock-resources.md) (Blokowanie zasobów w usłudze Azure Resource Manager).

## <a name="tag-resources"></a>Tagowanie zasobów

Oznaczanie pomaga logicznie organizować grupę zasobów i zasoby. Aby uzyskać więcej informacji, zobacz [Organizowanie zasobów platformy Azure za pomocą tagów](tag-resources.md#azure-cli).

## <a name="manage-access-to-resources"></a>Zarządzanie dostępem do zasobów

[Kontrola dostępu oparta na rolach (RBAC)](../../role-based-access-control/overview.md) to sposób zarządzania dostępem do zasobów na platformie Azure. Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępem przy użyciu funkcji RBAC i interfejsu wiersza polecenia platformy Azure](../../role-based-access-control/role-assignments-cli.md).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o usłudze Azure Resource Manager, zobacz [Omówienie usługi Azure Resource Manager](overview.md).
- Aby poznać składnię szablonu Menedżera zasobów, zobacz [Opis struktury i składni szablonów usługi Azure Resource Manager](../templates/template-syntax.md).
- Aby dowiedzieć się, jak tworzyć szablony, zobacz [samouczki krok po kroku](/azure/azure-resource-manager/).
- Aby wyświetlić schematy szablonów usługi Azure Resource Manager, zobacz [odwołanie do szablonu](/azure/templates/).
