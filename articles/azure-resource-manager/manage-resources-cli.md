---
title: Zarządzanie zasobami platformy Azure przy użyciu wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Użyj wiersza polecenia platformy Azure i usługi Azure Resource Manager do zarządzania zasobami.
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 3293d08f63cf573c7833fae5dd15bfe3119fd6c7
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206517"
---
# <a name="manage-azure-resources-by-using-azure-cli"></a>Zarządzanie zasobami platformy Azure przy użyciu wiersza polecenia platformy Azure

Dowiedz się, jak używać interfejsu wiersza polecenia platformy Azure za pomocą [usługi Azure Resource Manager](resource-group-overview.md) do zarządzania zasobami platformy Azure. Aby zarządzać grupami zasobów, zobacz [Zarządzanie grup zasobów platformy Azure przy użyciu wiersza polecenia platformy Azure](./manage-resource-groups-cli.md).

Inne artykuły dotyczące zarządzania zasobami:

- [Zarządzanie zasobami platformy Azure przy użyciu witryny Azure portal](./manage-resources-portal.md)
- [Zarządzanie zasobami platformy Azure przy użyciu programu Azure PowerShell](./manage-resources-powershell.md)

## <a name="deploy-resources-to-an-existing-resource-group"></a>Wdrażanie zasobów do istniejącej grupy zasobów

Wdrażaj zasoby platformy Azure bezpośrednio przy użyciu programu Azure PowerShell lub wdrażanie szablonu usługi Resource Manager do tworzenia zasobów platformy Azure.

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

Poniższy skrypt tworzy wdrażanie szablonu szybkiego startu, aby utworzyć konto magazynu. Aby uzyskać więcej informacji, zobacz temat [Szybki start: Tworzenie szablonów usługi Azure Resource Manager przy użyciu programu Visual Studio Code](./resource-manager-quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell).

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group deployment create --resource-group $resourceGroupName --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

Aby uzyskać więcej informacji, zobacz [wdrażanie zasobów za pomocą szablonów usługi Resource Manager i interfejsu wiersza polecenia Azure](./resource-group-template-deploy-cli.md).

## <a name="deploy-a-resource-group-and-resources"></a>Wdrażanie grupy zasobów i zasobów

Można utworzyć grupę zasobów i wdrażania zasobów w grupie. Aby uzyskać więcej informacji, zobacz [Tworzenie grupy zasobów i wdrażanie zasobów](./deploy-to-subscription.md#create-resource-group-and-deploy-resources).

## <a name="deploy-resources-to-multiple-subscriptions-or-resource-groups"></a>Wdrażanie zasobów na wiele grup zasobów lub subskrypcji

Zazwyczaj można wdrażać wszystkich zasobów w szablonie do pojedynczej grupy zasobów. Jednak istnieją scenariusze, w której chcesz wdrożyć zestaw zasobów ze sobą, ale umieścić je w różnych grupach zasobów lub subskrypcji. Aby uzyskać więcej informacji, zobacz [wdrażania zasobów platformy Azure do wielu grup zasobów lub subskrypcji](./resource-manager-cross-resource-group-deployment.md).

## <a name="delete-resources"></a>Usuwanie zasobów

Poniższy skrypt pokazuje, jak usunąć konto magazynu.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account delete --resource-group $resourceGroupName --name $storageAccountName 
```

Aby uzyskać więcej informacji o jak usługi Azure Resource Manager porządkuje usuwania zasobów, zobacz [Usuwanie grupy zasobów usługi Azure Resource Manager](./resource-group-delete.md).

## <a name="move-resources"></a>Przenoszenie zasobów

Poniższy skrypt pokazuje, jak usunąć konto magazynu z jednej grupy zasobów do innej grupy zasobów.

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

Aby uzyskać więcej informacji, zobacz [Move resources to new resource group or subscription](resource-group-move-resources.md) (Przenoszenie zasobów do nowej grupy lub subskrypcji).

## <a name="lock-resources"></a>Blokowanie zasobów

Zablokowanie uniemożliwia innym użytkownikom w organizacji przypadkowo usuwanie i modyfikowanie krytyczne zasoby, takie jak subskrypcji platformy Azure, grupę zasobów lub zasobu. 

Poniższy skrypt blokuje konta magazynu, więc nie można usunąć konta.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az lock create --name LockSite --lock-type CanNotDelete --resource-group $resourceGroupName --resource-name $storageAccountName --resource-type Microsoft.Storage/storageAccounts 
```

Poniższy skrypt pobiera wszystkie blokady dla konta magazynu:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az lock list --resource-group $resourceGroupName --resource-name $storageAccountName --resource-type Microsoft.Storage/storageAccounts --parent ""
```

Poniższy skrypt usuwa blokadę konta magazynu:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
lockId=$(az lock show --name LockSite --resource-group $resourceGroupName --resource-type Microsoft.Storage/storageAccounts --resource-name $storageAccountName --output tsv --query id)&&
az lock delete --ids $lockId
```

Aby uzyskać więcej informacji, zobacz [Lock resources with Azure Resource Manager](resource-group-lock-resources.md) (Blokowanie zasobów w usłudze Azure Resource Manager).

## <a name="tag-resources"></a>Tagowanie zasobów

Znakowanie pozwala logicznie organizowania swoją grupę zasobów i zasobów. Aby uzyskać informacje, zobacz [porządkowanie zasobów na platformie Azure za pomocą tagów](./resource-group-using-tags.md#azure-cli).

## <a name="manage-access-to-resources"></a>Zarządzanie dostępem do zasobów

[Kontrola dostępu oparta na rolach (RBAC, Role Based Access Control)](../role-based-access-control/overview.md) to sposób zarządzania dostępem do zasobów na platformie Azure. Aby uzyskać więcej informacji, zobacz [zarządzanie dostępem przy użyciu RBAC i wiersza polecenia platformy Azure](../role-based-access-control/role-assignments-cli.md).

## <a name="next-steps"></a>Kolejne kroki

- Aby poznać usługi Azure Resource Manager, zobacz [Omówienie usługi Azure Resource Manager](./resource-group-overview.md).
- Aby dowiedzieć się składni szablonu usługi Resource Manager, zobacz [Omówienie struktury i składni szablonów usługi Azure Resource Manager](./resource-group-authoring-templates.md).
- Aby dowiedzieć się, jak opracowywać szablony, zobacz [samouczki krok po kroku](/azure/azure-resource-manager/).
- Aby wyświetlić schematy szablonów usługi Azure Resource Manager, zobacz [odwołanie do szablonu](/azure/templates/).
