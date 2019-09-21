---
title: Zarządzanie dostępem do zasobów platformy Azure przy użyciu szablonów RBAC i Azure Resource Manager | Microsoft Docs
description: Dowiedz się, jak zarządzać dostępem do zasobów platformy Azure dla użytkowników, grup i aplikacji przy użyciu kontroli dostępu opartej na rolach (RBAC) i szablonów Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/06/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: d9ec2fc76fb1cc33898c331a79167f9291fc8c63
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71172963"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-azure-resource-manager-templates"></a>Zarządzanie dostępem do zasobów platformy Azure przy użyciu szablonów RBAC i Azure Resource Manager

[Kontrola dostępu oparta na rolach (RBAC, Role Based Access Control)](overview.md) to sposób zarządzania dostępem do zasobów platformy Azure. Oprócz używania Azure PowerShell lub interfejsu wiersza polecenia platformy Azure można zarządzać dostępem do zasobów platformy Azure przy użyciu [szablonów Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md). Szablony mogą być przydatne, jeśli trzeba spójnie i wielokrotnie wdrażać zasoby. W tym artykule opisano sposób zarządzania dostępem przy użyciu RBAC i szablonów.

## <a name="create-a-role-assignment-at-a-resource-group-scope-without-parameters"></a>Utwórz przypisanie roli w zakresie grupy zasobów (bez parametrów)

Aby udzielić dostępu za pomocą kontroli dostępu opartej na rolach, tworzy się przypisanie roli. Poniższy szablon przedstawia podstawowy sposób tworzenia przypisania roli. Niektóre wartości są określone w szablonie. Poniższy szablon demonstruje:

-  Jak przypisać rolę [czytnika](built-in-roles.md#reader) do użytkownika, grupy lub aplikacji w zakresie grupy zasobów

Aby użyć szablonu, należy wykonać następujące czynności:

- Utwórz nowy plik JSON i skopiuj szablon
- Zamień `<your-principal-id>` na unikatowy identyfikator użytkownika, grupy lub aplikacji, do której ma zostać przypisana rola. Identyfikator ma format:`11111111-1111-1111-1111-111111111111`

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[guid(resourceGroup().id)]",
            "properties": {
                "roleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
                "principalId": "<your-principal-id>"
            }
        }
    ]
}
```

Oto przykład polecenia [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) i [AZ Group Deployment Create](/cli/azure/group/deployment#az-group-deployment-create) , aby uruchomić wdrożenie w grupie zasobów o nazwie example.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json
```

Poniżej przedstawiono przykład przypisania roli czytnika do użytkownika dla grupy zasobów po wdrożeniu szablonu.

![Przypisanie roli w zakresie grupy zasobów](./media/role-assignments-template/role-assignment-template.png)

## <a name="create-a-role-assignment-at-a-resource-group-or-subscription-scope"></a>Tworzenie przypisania roli w ramach grupy zasobów lub zakresu subskrypcji

Poprzedni szablon nie jest bardzo elastyczny. Następujący szablon używa parametrów i może być używany w różnych zakresach. Poniższy szablon demonstruje:

- Jak przypisać rolę do użytkownika, grupy lub aplikacji w ramach grupy zasobów lub zakresu subskrypcji
- Jak określić role właściciela, współautora i czytnika jako parametr

Aby użyć szablonu, należy określić następujące dane wejściowe:

- Unikatowy identyfikator użytkownika, grupy lub aplikacji, do której ma zostać przypisana rola
- Rola do przypisania
- Unikatowy identyfikator, który będzie używany do przypisania roli, lub można użyć identyfikatora domyślnego

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        },
        "builtInRoleType": {
            "type": "string",
            "allowedValues": [
                "Owner",
                "Contributor",
                "Reader"
            ],
            "metadata": {
                "description": "Built-in role to assign"
            }
        },
        "roleNameGuid": {
            "type": "string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "A new GUID used to identify the role assignment"
            }
        }
    },
    "variables": {
        "Owner": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
        "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[parameters('roleNameGuid')]",
            "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

Aby uzyskać unikatowy identyfikator użytkownika, do którego ma zostać przypisana rola, można użyć poleceń [Get-AzADUser](/powershell/module/az.resources/get-azaduser) lub [AZ AD User show](/cli/azure/ad/user#az-ad-user-show) .

```azurepowershell
$userid = (Get-AzADUser -DisplayName "{name}").id
```

```azurecli
userid=$(az ad user show --upn-or-object-id "{email}" --query objectId --output tsv)
```

Zakres przypisania roli jest określany na podstawie poziomu wdrożenia. Oto przykład polecenia [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) i [AZ Group Deployment Create](/cli/azure/group/deployment#az-group-deployment-create) , aby uruchomić wdrożenie w zakresie grupy zasobów.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $userid -builtInRoleType Reader
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$userid builtInRoleType=Reader
```

Poniżej przedstawiono przykładowe polecenia [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) i [AZ Deployment Create](/cli/azure/deployment#az-deployment-create) dotyczące sposobu uruchamiania wdrożenia w zakresie subskrypcji i określania lokalizacji.

```azurepowershell
New-AzDeployment -Location centralus -TemplateFile rbac-test.json -principalId $userid -builtInRoleType Reader
```

```azurecli
az deployment create --location centralus --template-file rbac-test.json --parameters principalId=$userid builtInRoleType=Reader
```

## <a name="create-a-role-assignment-at-a-resource-scope"></a>Tworzenie przypisania roli w zakresie zasobów

Jeśli konieczne jest utworzenie przypisania roli na poziomie zasobu, format przypisania roli jest inny. Podaj przestrzeń nazw dostawcy zasobów i typ zasobu zasobu, do którego ma zostać przypisana rola. Należy również podać nazwę zasobu w nazwie przypisania roli.

W polu Typ i nazwa przypisania roli Użyj następującego formatu:

```json
"type": "{resource-provider-namespace}/{resource-type}/providers/roleAssignments",
"name": "{resource-name}/Microsoft.Authorization/{role-assign-GUID}"
```

Poniższy szablon demonstruje:

- Jak utworzyć nowe konto magazynu
- Jak przypisać rolę do użytkownika, grupy lub aplikacji w zakresie konta magazynu
- Jak określić role właściciela, współautora i czytnika jako parametr

Aby użyć szablonu, należy określić następujące dane wejściowe:

- Unikatowy identyfikator użytkownika, grupy lub aplikacji, do której ma zostać przypisana rola
- Rola do przypisania
- Unikatowy identyfikator, który będzie używany do przypisania roli, lub można użyć identyfikatora domyślnego


```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        },
        "builtInRoleType": {
            "type": "string",
            "allowedValues": [
                "Owner",
                "Contributor",
                "Reader"
            ],
            "metadata": {
                "description": "Built-in role to assign"
            }
        },
        "roleNameGuid": {
            "type": "string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "A new GUID used to identify the role assignment"
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "Owner": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
        "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
        "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        },
        {
            "type": "Microsoft.Storage/storageAccounts/providers/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[concat(variables('storageName'), '/Microsoft.Authorization/', parameters('roleNameGuid'))]",
            "dependsOn": [
                "[variables('storageName')]"
            ],
            "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

Aby wdrożyć poprzedni szablon, Użyj poleceń grupy zasobów. Oto przykład polecenia [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) i [AZ Group Deployment Create](/cli/azure/group/deployment#az-group-deployment-create) , aby uruchomić wdrożenie w zakresie zasobów.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $userid -builtInRoleType Contributor
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$userid builtInRoleType=Contributor
```

Poniżej przedstawiono przykład przypisania roli współautor do użytkownika dla konta magazynu po wdrożeniu szablonu.

![Przypisanie roli w zakresie zasobów](./media/role-assignments-template/role-assignment-template-resource.png)

## <a name="create-a-role-assignment-for-a-new-service-principal"></a>Tworzenie przypisania roli dla nowej nazwy głównej usługi

Jeśli utworzysz nową nazwę główną usługi i natychmiast spróbujesz przypisać rolę do tej jednostki usługi, w niektórych przypadkach przypisanie roli może zakończyć się niepowodzeniem. Jeśli na przykład utworzysz nową tożsamość zarządzaną, a następnie spróbujesz przypisać rolę do tej jednostki usługi w tym samym szablonie Azure Resource Manager, przypisanie roli może zakończyć się niepowodzeniem. Przyczyną tego błędu jest prawdopodobnie opóźnienie replikacji. Nazwa główna usługi jest tworzona w jednym regionie; jednak przypisanie roli może wystąpić w innym regionie, który jeszcze nie replikuje jednostki usługi. Aby rozwiązać ten scenariusz, należy ustawić `principalType` właściwość na `ServicePrincipal` przy tworzeniu przypisania roli.

Poniższy szablon demonstruje:

- Jak utworzyć nową nazwę główną usługi tożsamości zarządzanej
- Jak określić`principalType`
- Jak przypisać rolę współautor do tej nazwy głównej usługi w zakresie grupy zasobów

Aby użyć szablonu, należy określić następujące dane wejściowe:

- Podstawowa nazwa tożsamości zarządzanej lub można użyć ciągu domyślnego

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "baseName": {
            "type": "string",
            "defaultValue": "msi-test"
        }
    },
    "variables": {
        "identityName": "[concat(parameters('baseName'), '-bootstrap')]",
        "bootstrapRoleAssignmentId": "[guid(concat(resourceGroup().id, 'contributor'))]",
        "contributorRoleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]"
    },
    "resources": [
        {
            "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
            "name": "[variables('identityName')]",
            "apiVersion": "2018-11-30",
            "location": "[resourceGroup().location]"
        },
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[variables('bootstrapRoleAssignmentId')]",
            "dependsOn": [
                "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
            ],
            "properties": {
                "roleDefinitionId": "[variables('contributorRoleDefinitionId')]",
                "principalId": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName')), '2018-11-30').principalId]",
                "scope": "[resourceGroup().id]",
                "principalType": "ServicePrincipal"
            }
        }
    ]
}
```

Oto przykład polecenia [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) i [AZ Group Deployment Create](/cli/azure/group/deployment#az-group-deployment-create) , aby uruchomić wdrożenie w zakresie grupy zasobów.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup2 -TemplateFile rbac-test.json
```

```azurecli
az group deployment create --resource-group ExampleGroup2 --template-file rbac-test.json
```

Poniżej przedstawiono przykład przypisania roli współautor do nowej jednostki usługi tożsamości zarządzanej po wdrożeniu szablonu.

![Przypisanie roli dla nowej nazwy głównej usługi tożsamości zarządzanej](./media/role-assignments-template/role-assignment-template-msi.png)

## <a name="next-steps"></a>Następne kroki

- [Szybki start: Tworzenie i wdrażanie szablonów usługi Azure Resource Manager przy użyciu witryny Azure Portal](../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md)
- [Understand the structure and syntax of Azure Resource Manager Templates (Omówienie struktury i składni szablonów usługi Azure Resource Manager)](../azure-resource-manager/resource-group-authoring-templates.md)
- [Szablony Szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/?term=rbac)
