---
title: Zarządzanie dostępem do zasobów platformy Azure przy użyciu szablonów RBAC i usługi Azure Resource Manager | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarządzać dostępem do zasobów platformy Azure dla użytkowników, grup i aplikacji przy użyciu kontroli dostępu opartej na rolach (RBAC) i szablonów usługi Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/02/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 537ee35e96a41cd02605319e244d39c6567c3bf1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60344606"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-azure-resource-manager-templates"></a>Zarządzanie dostępem do zasobów platformy Azure przy użyciu szablonów RBAC i usługi Azure Resource Manager

[Kontrola dostępu oparta na rolach (RBAC, Role Based Access Control)](overview.md) to sposób zarządzania dostępem do zasobów platformy Azure. Oprócz używania programu Azure PowerShell lub interfejsu wiersza polecenia platformy Azure, możesz zarządzać dostępem do zasobów platformy Azure przy użyciu funkcji RBAC i [szablonów usługi Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md). Szablony mogą być przydatne, jeśli zajdzie potrzeba wdrożenia zasobów spójnego i wielokrotnego. W tym artykule opisano, jak możesz zarządzać dostępem przy użyciu RBAC i szablony.

## <a name="example-template-to-create-a-role-assignment"></a>Przykładowy szablon, można utworzyć przypisania roli

Aby udzielić dostępu za pomocą kontroli dostępu opartej na rolach, tworzy się przypisanie roli. Przedstawia następujący szablon:
- Jak przypisać rolę do użytkownika, grupy lub aplikacji w zakresie grupy zasobów
- Sposób określania właściciela, współautora i czytelnika ról jako parametr

Aby użyć szablonu, należy określić następujące dane wejściowe:
- Nazwa grupy zasobów
- Unikatowy identyfikator użytkownika, grupy lub aplikacji, aby przypisać rolę
- Przypisywanie roli
- Unikatowy identyfikator, który będzie używany do przypisania roli

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
      "metadata": {
        "description": "A new GUID used to identify the role assignment"
      }
    }
  },
  "variables": {
    "Owner": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
    "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
    "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
    "scope": "[resourceGroup().id]"
  },
  "resources": [
    {
      "type": "Microsoft.Authorization/roleAssignments",
      "apiVersion": "2017-05-01",
      "name": "[parameters('roleNameGuid')]",
      "properties": {
        "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
        "principalId": "[parameters('principalId')]",
        "scope": "[variables('scope')]"
      }
    }
  ]
}
```

Poniżej przedstawiono przykład czytnik przypisania roli do użytkownika po wdrożeniu szablonu.

![Przypisanie roli przy użyciu szablonu](./media/role-assignments-template/role-assignment-template.png)

## <a name="deploy-template-using-azure-powershell"></a>Wdrażanie szablonu przy użyciu programu Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

Aby wdrożyć poprzedni szablon przy użyciu programu Azure PowerShell, wykonaj następujące kroki.

1. Utwórz nowy plik o nazwie rbac rg.json i skopiuj poprzedni szablon.

1. Zaloguj się do programu [Azure PowerShell](/powershell/azure/authenticate-azureps).

1. Uzyskaj Unikatowy identyfikator użytkownika, grupy lub aplikacji. Na przykład, można użyć [Get AzADUser](/powershell/module/az.resources/get-azaduser) polecenie, aby wyświetlić listę użytkowników usługi Azure AD.

    ```azurepowershell
    Get-AzADUser
    ```

1. Narzędzie identyfikator GUID można wygenerować unikatowy identyfikator, który będzie używany do przypisania roli. Identyfikator ma następujący format: `11111111-1111-1111-1111-111111111111`

1. Utwórz grupę zasobów w przykładzie.

    ```azurepowershell
    New-AzResourceGroup -Name ExampleGroup -Location "Central US"
    ```

1. Użyj [New AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) polecenie, aby rozpocząć wdrażanie.

    ```azurepowershell
    New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-rg.json
    ```

    Prośba do określania wymaganych parametrów. Poniżej przedstawiono przykładowe dane wyjściowe.

    ```Output
    PS /home/user> New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-rg.json
    
    cmdlet New-AzResourceGroupDeployment at command pipeline position 1
    Supply values for the following parameters:
    (Type !? for Help.)
    principalId: 22222222-2222-2222-2222-222222222222
    builtInRoleType: Reader
    roleNameGuid: 11111111-1111-1111-1111-111111111111
    
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

## <a name="deploy-template-using-the-azure-cli"></a>Wdrażanie szablonu przy użyciu wiersza polecenia platformy Azure

Aby wdrożyć poprzedni szablon przy użyciu wiersza polecenia platformy Azure, wykonaj następujące kroki.

1. Utwórz nowy plik o nazwie rbac rg.json i skopiuj poprzedni szablon.

1. Zaloguj się do [wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli).

1. Uzyskaj Unikatowy identyfikator użytkownika, grupy lub aplikacji. Na przykład, można użyć [listy użytkowników usługi ad az](/cli/azure/ad/user#az-ad-user-list) polecenie, aby wyświetlić listę użytkowników usługi Azure AD.

    ```azurecli
    az ad user list
    ```

1. Narzędzie identyfikator GUID można wygenerować unikatowy identyfikator, który będzie używany do przypisania roli. Identyfikator ma następujący format: `11111111-1111-1111-1111-111111111111`

1. Utwórz grupę zasobów w przykładzie.

    ```azurecli
    az group create --name ExampleGroup --location "Central US"
    ```

1. Użyj [Utwórz wdrożenie grupy az](/cli/azure/group/deployment#az-group-deployment-create) polecenie, aby rozpocząć wdrażanie.

    ```azurecli
    az group deployment create --resource-group ExampleGroup --template-file rbac-rg.json
    ```

    Prośba do określania wymaganych parametrów. Poniżej przedstawiono przykładowe dane wyjściowe.

    ```Output
    C:\Azure\Templates>az group deployment create --resource-group ExampleGroup --template-file rbac-rg.json
    Please provide string value for 'principalId' (? for help): 22222222-2222-2222-2222-222222222222
    Please provide string value for 'builtInRoleType' (? for help):
     [1] Owner
     [2] Contributor
     [3] Reader
    Please enter a choice [1]: 3
    Please provide string value for 'roleNameGuid' (? for help): 11111111-1111-1111-1111-111111111111
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
    
## <a name="next-steps"></a>Kolejne kroki

- [Szybki start: Tworzenie i wdrażanie szablonów usługi Azure Resource Manager przy użyciu witryny Azure Portal](../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md)
- [Understand the structure and syntax of Azure Resource Manager Templates (Omówienie struktury i składni szablonów usługi Azure Resource Manager)](../azure-resource-manager/resource-group-authoring-templates.md)
- [Szablony Szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/?term=rbac)
