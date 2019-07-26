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
ms.date: 07/19/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: e6511ff84c251577a5ff483f892387ab7d3d4d41
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360448"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-azure-resource-manager-templates"></a>Zarządzanie dostępem do zasobów platformy Azure przy użyciu szablonów RBAC i Azure Resource Manager

[Kontrola dostępu oparta na rolach (RBAC, Role Based Access Control)](overview.md) to sposób zarządzania dostępem do zasobów platformy Azure. Oprócz używania Azure PowerShell lub interfejsu wiersza polecenia platformy Azure można zarządzać dostępem do zasobów platformy Azure przy użyciu szablonów RBAC i [Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md). Szablony mogą być przydatne, jeśli trzeba spójnie i wielokrotnie wdrażać zasoby. W tym artykule opisano sposób zarządzania dostępem przy użyciu RBAC i szablonów.

## <a name="assign-role-to-resource-group-or-subscription"></a>Przypisywanie roli do grupy zasobów lub subskrypcji

Aby udzielić dostępu za pomocą kontroli dostępu opartej na rolach, tworzy się przypisanie roli. Poniższy szablon demonstruje:
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

Poniżej przedstawiono przykład przypisania roli czytnika do użytkownika dla grupy zasobów po wdrożeniu szablonu.

![Przypisanie roli przy użyciu szablonu](./media/role-assignments-template/role-assignment-template.png)

Zakres przypisania roli jest określany na podstawie poziomu wdrożenia. W tym artykule przedstawiono zarówno polecenie wdrożenia grupy zasobów, jak i poziomu subskrypcji.

## <a name="assign-role-to-resource"></a>Przypisywanie roli do zasobu

Jeśli konieczne jest utworzenie przypisania roli na poziomie zasobu, format przypisania roli jest inny. Podaj przestrzeń nazw dostawcy zasobów i typ zasobu zasobu, do którego ma zostać przypisana rola. Należy również podać nazwę zasobu w nazwie przypisania roli.

W polu Typ i nazwa przypisania roli Użyj następującego formatu:

```json
"type": "{resource-provider-namespace}/{resource-type}/providers/roleAssignments",
"name": "{resource-name}/Microsoft.Authorization/{role-assign-GUID}"
```

Poniższy szablon wdraża konto magazynu i przypisuje do niego rolę. Należy wdrożyć je za pomocą poleceń grupy zasobów.

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

Poniżej przedstawiono przykład przypisania roli współautor do użytkownika dla konta magazynu po wdrożeniu szablonu.

![Przypisanie roli przy użyciu szablonu](./media/role-assignments-template/role-assignment-template-resource.png)

## <a name="deploy-template-using-azure-powershell"></a>Wdróż szablon przy użyciu Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

Aby wdrożyć poprzedni szablon do grupy zasobów lub subskrypcji przy użyciu Azure PowerShell, wykonaj następujące kroki.

1. Utwórz nowy plik o nazwie RBAC-RG. JSON i skopiuj poprzedni szablon.

1. Zaloguj się do programu [Azure PowerShell](/powershell/azure/authenticate-azureps).

1. Uzyskaj unikatowy identyfikator użytkownika, grupy lub aplikacji. Na przykład możesz użyć polecenia [Get-AzADUser](/powershell/module/az.resources/get-azaduser) , aby wyświetlić listę użytkowników usługi Azure AD.

    ```azurepowershell
    $userid = (Get-AzADUser -DisplayName "{name}").id
    ```

1. Szablon generuje wartość domyślną identyfikatora GUID, który jest używany do identyfikowania przypisania roli. Jeśli musisz określić konkretny identyfikator GUID, przekaż tę wartość w parametrze roleNameGuid. Identyfikator ma format:`11111111-1111-1111-1111-111111111111`

Aby przypisać rolę na poziomie zasobu lub grupy zasobów, wykonaj następujące kroki:

1. Utwórz przykładową grupę zasobów.

    ```azurepowershell
    New-AzResourceGroup -Name ExampleGroup -Location "Central US"
    ```

1. Użyj polecenia [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) , aby rozpocząć wdrażanie.

    ```azurepowershell
    New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-rg.json -principalId $userid -builtInRoleType Reader
    ```

    Poniżej przedstawiono przykład danych wyjściowych.

    ```Output
    PS /home/user> New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-rg.json -principalId $userid -builtInRoleType Reader
    
    DeploymentName          : rbac-rg
    ResourceGroupName       : ExampleGroup
    ProvisioningState       : Succeeded
    Timestamp               : 7/17/2018 7:46:32 PM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                              Name             Type                       Value
                              ===============  =========================  ==========
                              principalId      String                     22222222-2222-2222-2222-222222222222
                              builtInRoleType  String                     Reader
                              roleNameGuid     String                     11111111-1111-1111-1111-111111111111
    
    Outputs                 :
    DeploymentDebugLogLevel :
    ```

Aby przypisać rolę na poziomie subskrypcji, użyj polecenia [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) i określ lokalizację wdrożenia.

```azurepowershell
New-AzDeployment -Location centralus -TemplateFile rbac-rg.json -principalId $userid -builtInRoleType Reader
```

Ma podobne dane wyjściowe do polecenia wdrożenia dla grup zasobów.

## <a name="deploy-template-using-the-azure-cli"></a>Wdrażanie szablonu przy użyciu interfejsu wiersza polecenia platformy Azure

Aby wdrożyć poprzedni szablon przy użyciu interfejsu wiersza polecenia platformy Azure w grupie zasobów lub subskrypcji, wykonaj następujące kroki.

1. Utwórz nowy plik o nazwie RBAC-RG. JSON i skopiuj poprzedni szablon.

1. Zaloguj się do [interfejsu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli).

1. Uzyskaj unikatowy identyfikator użytkownika, grupy lub aplikacji. Na przykład możesz użyć polecenia [AZ AD User show](/cli/azure/ad/user#az-ad-user-show) , aby wyświetlić użytkownika usługi Azure AD.

    ```azurecli
    userid=$(az ad user show --upn-or-object-id "{email}" --query objectId --output tsv)
    ```

1. Szablon generuje wartość domyślną identyfikatora GUID, który jest używany do identyfikowania przypisania roli. Jeśli musisz określić konkretny identyfikator GUID, przekaż tę wartość w parametrze roleNameGuid. Identyfikator ma format:`11111111-1111-1111-1111-111111111111`

Aby przypisać rolę na poziomie zasobu lub grupy zasobów, wykonaj następujące kroki:

1. Utwórz przykładową grupę zasobów.

    ```azurecli
    az group create --name ExampleGroup --location "Central US"
    ```

1. Użyj polecenia [AZ Group Deployment Create](/cli/azure/group/deployment#az-group-deployment-create) , aby rozpocząć wdrażanie.

    ```azurecli
    az group deployment create --resource-group ExampleGroup --template-file rbac-rg.json --parameters principalId=$userid builtInRoleType=Reader
    ```

    Poniżej przedstawiono przykład danych wyjściowych.

    ```Output
    C:\Azure\Templates>az group deployment create --resource-group ExampleGroup --template-file rbac-rg.json --parameters principalId=$userid builtInRoleType=Reader
    
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/rbac-rg",
      "name": "rbac-rg",
      "properties": {
        "additionalProperties": {
          "duration": "PT9.5323924S",
          "outputResources": [
            {
              "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ExampleGroup/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111",
              "resourceGroup": "ExampleGroup"
            }
          ],
          "templateHash": "0000000000000000000"
        },
        "correlationId": "33333333-3333-3333-3333-333333333333",
        "debugSetting": null,
        "dependencies": [],
        "mode": "Incremental",
        "outputs": null,
        "parameters": {
          "builtInRoleType": {
            "type": "String",
            "value": "Reader"
          },
          "principalId": {
            "type": "String",
            "value": "22222222-2222-2222-2222-222222222222"
          },
          "roleNameGuid": {
            "type": "String",
            "value": "11111111-1111-1111-1111-111111111111"
          }
        },
        "parametersLink": null,
        "providers": [
          {
            "id": null,
            "namespace": "Microsoft.Authorization",
            "registrationState": null,
            "resourceTypes": [
              {
                "aliases": null,
                "apiVersions": null,
                "locations": [
                  null
                ],
                "properties": null,
                "resourceType": "roleAssignments"
              }
            ]
          }
        ],
        "provisioningState": "Succeeded",
        "template": null,
        "templateLink": null,
        "timestamp": "2018-07-17T19:00:31.830904+00:00"
      },
      "resourceGroup": "ExampleGroup"
    }
    ```

Aby przypisać rolę na poziomie subskrypcji, użyj polecenia [AZ Deployment Create](/cli/azure/deployment#az-deployment-create) i określ lokalizację wdrożenia.

```azurecli
az deployment create --location centralus --template-file rbac-rg.json --parameters principalId=$userid builtInRoleType=Reader
```

Ma podobne dane wyjściowe do polecenia wdrożenia dla grup zasobów.

## <a name="next-steps"></a>Następne kroki

- [Szybki start: Tworzenie i wdrażanie szablonów usługi Azure Resource Manager przy użyciu witryny Azure Portal](../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md)
- [Understand the structure and syntax of Azure Resource Manager Templates (Omówienie struktury i składni szablonów usługi Azure Resource Manager)](../azure-resource-manager/resource-group-authoring-templates.md)
- [Szablony Szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/?term=rbac)
