---
title: Utwórz grupę zasobów i zasoby w subskrypcji — szablon usługi Azure Resource Manager
description: W tym artykule opisano sposób tworzenia grupy zasobów w szablonie usługi Azure Resource Manager. Pokazano również, jak wdrażać zasoby w zakresie subskrypcji platformy Azure.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: bdba294e1ee776d90b93f715e930ec26765abb7f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "66129167"
---
# <a name="create-resource-groups-and-resources-at-the-subscription-level"></a>Tworzenie grupy zasobów i zasoby na poziomie subskrypcji

Zazwyczaj wdrażania zasobów platformy Azure do grupy zasobów w subskrypcji platformy Azure. Jednakże można także tworzyć grupy zasobów platformy Azure i tworzenie zasobów platformy Azure na poziomie subskrypcji. Aby wdrożyć szablony na poziomie subskrypcji, należy użyć wiersza polecenia platformy Azure i programu Azure PowerShell. Witryna Azure portal nie obsługuje wdrażania na poziomie subskrypcji.

Aby utworzyć grupę zasobów w szablonie usługi Azure Resource Manager, należy zdefiniować [ **Microsoft.Resources/resourceGroups** ](/azure/templates/microsoft.resources/allversions) zasób mający nazwę i lokalizację grupy zasobów. Można utworzyć grupę zasobów i wdrażania zasobów w tej grupie zasobów, w tym samym szablonie. Zasoby, które można wdrożyć na poziomie subskrypcji obejmują: [Zasady](../governance/policy/overview.md), i [kontroli dostępu opartej na rolach](../role-based-access-control/overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="deployment-considerations"></a>Zagadnienia dotyczące wdrażania

Wdrażanie na poziomie subskrypcji różni się od wdrożenia grupy zasobów w następujących aspektach:

### <a name="schema-and-commands"></a>Schemat i polecenia

Schemat i poleceń, których można użyć na potrzeby wdrożeń poziom subskrypcji są inne niż wdrożenia grupy zasobów. 

Dla schematu, należy użyć `https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`.

Polecenie wdrożenia wiersza polecenia platformy Azure, można użyć [tworzenia wdrożenia az](/cli/azure/deployment?view=azure-cli-latest#az-deployment-create). Na przykład następujące polecenie interfejsu wiersza polecenia służy do wdrażania szablonu, aby utworzyć grupę zasobów:

```azurecli
az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json \
  --parameters rgName=demoResourceGroup rgLocation=centralus
```

Polecenie wdrożenia programu PowerShell, użyj [New AzDeployment](/powershell/module/az.resources/new-azdeployment). Na przykład następujące polecenie programu PowerShell służy do wdrażania szablonu, aby utworzyć grupę zasobów:

```azurepowershell
New-AzDeployment `
  -Name demoDeployment `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json `
  -rgName demoResourceGroup `
  -rgLocation centralus
```

### <a name="deployment-name-and-location"></a>Nazwa wdrożenia i lokalizacji

Podczas wdrażania do Twojej subskrypcji, należy podać lokalizację dla wdrożenia. Można również podać nazwę dla wdrożenia. Jeśli nie określisz nazwę wdrożenia, nazwę szablonu jest używana jako nazwa wdrożenia. Na przykład wdrażania szablonu o nazwie **azuredeploy.json** tworzy domyślną nazwę wdrożenia **azuredeploy**.

Lokalizacja wdrożenia poziomu subskrypcji jest niezmienny. Nie można utworzyć wdrożenia w jednej lokalizacji w przypadku istniejącego wdrożenia o takiej samej nazwie, ale inną lokalizację. Jeśli zostanie wyświetlony kod błędu: `InvalidDeploymentLocation`, użyj innej nazwy lub tej samej lokalizacji co poprzedniego wdrożenia dla tej nazwy.

### <a name="use-template-functions"></a>Użyj szablonu funkcji

W przypadku wdrożeń na poziomie subskrypcji istnieją pewne istotne kwestie, korzystając z funkcji szablonu:

* [ResourceGroup()](resource-group-template-functions-resource.md#resourcegroup) funkcja **nie** obsługiwane.
* [ResourceId()](resource-group-template-functions-resource.md#resourceid) funkcja jest obsługiwana. Należy użyć go, aby uzyskać identyfikator zasobu dla zasobów, które są używane w wdrożeń poziomu subskrypcji. Na przykład uzyskać identyfikator zasobu definicji zasad za pomocą `resourceId('Microsoft.Authorization/roleDefinitions/', parameters('roleDefinition'))`
* [Reference()](resource-group-template-functions-resource.md#reference) i [list()](resource-group-template-functions-resource.md#list) funkcje są obsługiwane.

## <a name="create-resource-groups"></a>Tworzenie grupy zasobów

Następujący szablon tworzy pustą grupę zasobów.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "rgName": {
            "type": "string"
        },
        "rgLocation": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2018-05-01",
            "location": "[parameters('rgLocation')]",
            "name": "[parameters('rgName')]",
            "properties": {}
        }
    ],
    "outputs": {}
}
```

Schemat szablonu, można znaleźć w folderze [tutaj](/azure/templates/microsoft.resources/allversions). Podobne szablony, można znaleźć w folderze [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments).

## <a name="create-multiple-resource-groups"></a>Utwórz wiele grup zasobów

Użyj [copy element](resource-group-create-multiple.md) z grupami zasobów, aby utworzyć więcej niż jednej grupy zasobów. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "rgNamePrefix": {
            "type": "string"
        },
        "rgLocation": {
            "type": "string"
        },
        "instanceCount": {
            "type": "int"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2018-05-01",
            "location": "[parameters('rgLocation')]",
            "name": "[concat(parameters('rgNamePrefix'), copyIndex())]",
            "copy": {
                "name": "rgCopy",
                "count": "[parameters('instanceCount')]"
            },
            "properties": {}
        }
    ],
    "outputs": {}
}
```

Aby uzyskać informacji na temat zasobów iteracji, zobacz [wdrożenie więcej niż jednego wystąpienia zasobu lub właściwości w szablonach usługi Resource Manager platformy Azure](./resource-group-create-multiple.md), i [samouczka: Tworzenie wielu wystąpień zasobów za pomocą szablonów usługi Resource Manager](./resource-manager-tutorial-create-multiple-instances.md).

## <a name="create-resource-group-and-deploy-resources"></a>Utwórz grupę zasobów i wdrażanie zasobów

Aby utworzyć grupę zasobów i wdrażania zasobów, użyj zagnieżdżonych szablonów. Zagnieżdżony szablon definiuje zasoby do wdrożenia w grupie zasobów. Ustaw zagnieżdżonych szablonów jako zależny od grupy zasobów, aby upewnić się, że grupa zasobów znajduje się przed przystąpieniem do wdrażania zasobów.

Poniższy przykład tworzy grupę zasobów, a następnie wdraża konto magazynu do grupy zasobów.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "rgName": {
            "type": "string"
        },
        "rgLocation": {
            "type": "string"
        },
        "storagePrefix": {
            "type": "string",
            "maxLength": 11
        }
    },
    "variables": {
        "storageName": "[concat(parameters('storagePrefix'), uniqueString(subscription().id, parameters('rgName')))]"
    },
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2018-05-01",
            "location": "[parameters('rgLocation')]",
            "name": "[parameters('rgName')]",
            "properties": {}
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2018-05-01",
            "name": "storageDeployment",
            "resourceGroup": "[parameters('rgName')]",
            "dependsOn": [
                "[resourceId('Microsoft.Resources/resourceGroups/', parameters('rgName'))]"
            ],
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "type": "Microsoft.Storage/storageAccounts",
                            "apiVersion": "2017-10-01",
                            "name": "[variables('storageName')]",
                            "location": "[parameters('rgLocation')]",
                            "kind": "StorageV2",
                            "sku": {
                                "name": "Standard_LRS"
                            }
                        }
                    ],
                    "outputs": {}
                }
            }
        }
    ],
    "outputs": {}
}
```

## <a name="create-policies"></a>Utwórz zasady

### <a name="assign-policy"></a>Przypisz zasady

Poniższy przykład przypisuje istniejącą definicję zasad do subskrypcji. Jeśli zasady pobiera parametry, należy podać je jako obiekt. Jeśli zasady nie przyjmuje parametrów, należy użyć domyślnego pustego obiektu.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "policyDefinitionID": {
            "type": "string"
        },
        "policyName": {
            "type": "string"
        },
        "policyParameters": {
            "type": "object",
            "defaultValue": {}
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Authorization/policyAssignments",
            "name": "[parameters('policyName')]",
            "apiVersion": "2018-03-01",
            "properties": {
                "scope": "[subscription().id]",
                "policyDefinitionId": "[parameters('policyDefinitionID')]",
                "parameters": "[parameters('policyParameters')]"
            }
        }
    ]
}
```

Aby zastosować wbudowane zasady do Twojej subskrypcji platformy Azure, użyj następujących poleceń interfejsu wiersza polecenia platformy Azure:

```azurecli
# Built-in policy that does not accept parameters
definition=$(az policy definition list --query "[?displayName=='Audit resource location matches resource group location'].id" --output tsv)

az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json \
  --parameters policyDefinitionID=$definition policyName=auditRGLocation
```

Aby wdrożyć ten szablon przy użyciu programu PowerShell, należy użyć:

```azurepowershell
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Audit resource location matches resource group location' }

New-AzDeployment `
  -Name policyassign `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json `
  -policyDefinitionID $definition.PolicyDefinitionId `
  -policyName auditRGLocation
```

Aby zastosować wbudowane zasady do Twojej subskrypcji platformy Azure, użyj następujących poleceń interfejsu wiersza polecenia platformy Azure:

```azurecli
# Built-in policy that accepts parameters
definition=$(az policy definition list --query "[?displayName=='Allowed locations'].id" --output tsv)

az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json \
  --parameters policyDefinitionID=$definition policyName=setLocation policyParameters="{'listOfAllowedLocations': {'value': ['westus']} }"
```

Aby wdrożyć ten szablon przy użyciu programu PowerShell, należy użyć:

```azurepowershell
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Allowed locations' }

$locations = @("westus", "westus2")
$policyParams =@{listOfAllowedLocations = @{ value = $locations}}

New-AzDeployment `
  -Name policyassign `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json `
  -policyDefinitionID $definition.PolicyDefinitionId `
  -policyName setLocation `
  -policyParameters $policyParams
```

### <a name="define-and-assign-policy"></a>Zdefiniuj i przypisz zasady

Możesz [zdefiniować](../governance/policy/concepts/definition-structure.md) i przypisywanie zasad, w tym samym szablonie.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Authorization/policyDefinitions",
            "name": "locationpolicy",
            "apiVersion": "2018-05-01",
            "properties": {
                "policyType": "Custom",
                "parameters": {},
                "policyRule": {
                    "if": {
                        "field": "location",
                        "equals": "northeurope"
                    },
                    "then": {
                        "effect": "deny"
                    }
                }
            }
        },
        {
            "type": "Microsoft.Authorization/policyAssignments",
            "name": "location-lock",
            "apiVersion": "2018-05-01",
            "dependsOn": [
                "locationpolicy"
            ],
            "properties": {
                "scope": "[subscription().id]",
                "policyDefinitionId": "[resourceId('Microsoft.Authorization/policyDefinitions', 'locationpolicy')]"
            }
        }
    ]
}
```

Aby utworzyć definicję zasad w ramach subskrypcji i zastosować je do subskrypcji, użyj następującego polecenia interfejsu wiersza polecenia:

```azurecli
az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

Aby wdrożyć ten szablon przy użyciu programu PowerShell, należy użyć:

```azurepowershell
New-AzDeployment `
  -Name definePolicy `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

## <a name="create-roles"></a>Tworzenie ról

### <a name="assign-role-at-subscription"></a>Przypisywanie roli w subskrypcji

Poniższy przykład przypisuje rolę użytkownika lub grupy dla subskrypcji. W tym przykładzie nie Określ zakres przypisania, ponieważ zakres jest automatycznie ustawiana na subskrypcję.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string"
        },
        "roleDefinitionId": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "name": "[guid(parameters('principalId'), deployment().name)]",
            "apiVersion": "2017-09-01",
            "properties": {
                "roleDefinitionId": "[resourceId('Microsoft.Authorization/roleDefinitions', parameters('roleDefinitionId'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

Aby przypisać grupy usługi Active Directory do roli dla Twojej subskrypcji, użyj następujących poleceń interfejsu wiersza polecenia platformy Azure:

```azurecli
# Get ID of the role you want to assign
role=$(az role definition list --name Contributor --query [].name --output tsv)

# Get ID of the AD group to assign the role to
principalid=$(az ad group show --group demogroup --query objectId --output tsv)

az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/roleassign.json \
  --parameters principalId=$principalid roleDefinitionId=$role
```

Aby wdrożyć ten szablon przy użyciu programu PowerShell, należy użyć:

```azurepowershell
$role = Get-AzRoleDefinition -Name Contributor

$adgroup = Get-AzADGroup -DisplayName demogroup

New-AzDeployment `
  -Name demoRole `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/roleassign.json `
  -roleDefinitionId $role.Id `
  -principalId $adgroup.Id
```

### <a name="assign-role-at-scope"></a>Przypisz rolę w zakresie

Następujący szablon poziom subskrypcji przypisuje rolę użytkownika lub grupy, które są ograniczone do grupy zasobów w ramach subskrypcji. Zakres musi być większa niż stopień wdrożenia. Można wdrożyć do subskrypcji i określić przypisania roli do zakresu grupy zasobów, w ramach tej subskrypcji. Jednak nie można wdrożyć w grupie zasobów i określ zakres przypisania roli w subskrypcji.

Aby przypisać rolę w zakresie, należy użyć do wdrożenia zagnieżdżonego. Należy zauważyć, że nazwa grupy zasobów jest określony we właściwościach zasobu wdrażania i we właściwości zakresu przypisania roli.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "principalId": {
            "type": "string"
        },
        "roleDefinitionId": {
            "type": "string"
        },
        "rgName": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2018-05-01",
            "name": "assignRole",
            "resourceGroup": "[parameters('rgName')]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "type": "Microsoft.Authorization/roleAssignments",
                            "name": "[guid(parameters('principalId'), deployment().name)]",
                            "apiVersion": "2017-09-01",
                            "properties": {
                                "roleDefinitionId": "[resourceId('Microsoft.Authorization/roleDefinitions', parameters('roleDefinitionId'))]",
                                "principalId": "[parameters('principalId')]",
                                "scope": "[concat(subscription().id, '/resourceGroups/', parameters('rgName'))]"
                            }
                        }
                    ],
                    "outputs": {}
                }
            }
        }
    ],
    "outputs": {}
}
```

Aby przypisać grupy usługi Active Directory do roli dla Twojej subskrypcji, użyj następujących poleceń interfejsu wiersza polecenia platformy Azure:

```azurecli
# Get ID of the role you want to assign
role=$(az role definition list --name Contributor --query [].name --output tsv)

# Get ID of the AD group to assign the role to
principalid=$(az ad group show --group demogroup --query objectId --output tsv)

az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scopedRoleAssign.json \
  --parameters principalId=$principalid roleDefinitionId=$role rgName demoRg
```

Aby wdrożyć ten szablon przy użyciu programu PowerShell, należy użyć:

```azurepowershell
$role = Get-AzRoleDefinition -Name Contributor

$adgroup = Get-AzADGroup -DisplayName demogroup

New-AzDeployment `
  -Name demoRole `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scopedRoleAssign.json `
  -roleDefinitionId $role.Id `
  -principalId $adgroup.Id `
  -rgName demoRg
```

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać przykład wdrażania ustawień obszaru roboczego usługi Azure Security Center, zobacz [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Aby dowiedzieć się więcej na temat tworzenia szablonów usługi Azure Resource Manager, zobacz [Tworzenie szablonów](resource-group-authoring-templates.md). 
* Aby uzyskać listę dostępnych funkcji w szablonie, zobacz [funkcje szablonu](resource-group-template-functions.md).
