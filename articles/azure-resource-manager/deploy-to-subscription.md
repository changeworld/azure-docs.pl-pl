---
title: Wdrażanie zasobów do subskrypcji platformy Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób tworzenia szablonu usługi Azure Resource Manager, który służy do wdrażania zasobów w zakresie subskrypcji.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2018
ms.author: tomfitz
ms.openlocfilehash: 1d281ebe80c6089c559cfaa77f4875a856566092
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2018
ms.locfileid: "49079382"
---
# <a name="deploy-resources-to-an-azure-subscription"></a>Wdrażanie zasobów z subskrypcją platformy Azure

Zazwyczaj wdrażania zasobów w grupie zasobów w subskrypcji platformy Azure. Jednak niektóre zasoby można wdrożyć na poziomie subskrypcji platformy Azure. Te zasoby mają zastosowanie w ramach subskrypcji. [Zasady](../azure-policy/azure-policy-introduction.md), [kontroli dostępu opartej na rolach](../role-based-access-control/overview.md), i [usługi Azure Security Center](../security-center/security-center-intro.md) usług, które chcesz zastosować na poziomie subskrypcji, a nie względem poziomu grupy zasobów.

W tym artykule korzysta z wiersza polecenia platformy Azure i programu PowerShell, aby wdrożyć szablony.

## <a name="name-and-location-for-deployment"></a>Nazwa i lokalizacja wdrożenia

Podczas wdrażania do Twojej subskrypcji, należy podać lokalizację dla wdrożenia. Można również podać nazwę dla wdrożenia. Jeśli nie określisz nazwę wdrożenia, nazwę szablonu jest używana jako nazwa wdrożenia. Na przykład wdrażania szablonu o nazwie **azuredeploy.json** tworzy domyślną nazwę wdrożenia **azuredeploy**.

Lokalizacja wdrożenia poziomu subskrypcji jest niezmienny. Nie można utworzyć wdrożenia w jednej lokalizacji w przypadku istniejącego wdrożenia o takiej samej nazwie, ale inną lokalizację. Jeśli zostanie wyświetlony kod błędu: `InvalidDeploymentLocation`, użyj innej nazwy lub tej samej lokalizacji co poprzedniego wdrożenia dla tej nazwy.

## <a name="using-template-functions"></a>Za pomocą funkcji szablonu

W przypadku wdrożeń z poziomu subskrypcji istnieją pewne istotne kwestie, korzystając z funkcji szablonu:

* [ResourceGroup()](resource-group-template-functions-resource.md#resourcegroup) funkcja **nie** obsługiwane.
* [ResourceId()](resource-group-template-functions-resource.md#resourceid) funkcja jest obsługiwana. Należy użyć go, aby uzyskać identyfikator zasobu dla zasobów, które są używane w wdrożeń poziomu subskrypcji. Na przykład uzyskać identyfikator zasobu definicji zasad za pomocą `resourceId('Microsoft.Authorization/roleDefinitions/', parameters('roleDefinition'))`
* [Reference()](resource-group-template-functions-resource.md#reference) i [list()](resource-group-template-functions-resource.md#list) funkcje są obsługiwane.

## <a name="assign-policy"></a>Przypisz zasady

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

Aby zastosować wbudowane zasady do Twojej subskrypcji platformy Azure, użyj następujących poleceń interfejsu wiersza polecenia platformy Azure. W tym przykładzie zasady nie mają parametrów

```azurecli-interactive
# Built-in policy that does not accept parameters
definition=$(az policy definition list --query "[?displayName=='Audit resource location matches resource group location'].id" --output tsv)

az deployment create \
  -n policyassign \
  -l southcentralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json \
  --parameters policyDefinitionID=$definition policyName=auditRGLocation
```

Aby wdrożyć ten szablon przy użyciu programu PowerShell, należy użyć:

```azurepowershell-interactive
$definition = Get-AzureRmPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Audit resource location matches resource group location' }

New-AzureRmDeployment `
  -Name policyassign `
  -Location southcentralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json `
  -policyDefinitionID $definition.PolicyDefinitionId `
  -policyName auditRGLocation
```

Aby zastosować wbudowane zasady do Twojej subskrypcji platformy Azure, użyj następujących poleceń interfejsu wiersza polecenia platformy Azure. W tym przykładzie zasady zawierają parametry.

```azurecli-interactive
# Built-in policy that accepts parameters
definition=$(az policy definition list --query "[?displayName=='Allowed locations'].id" --output tsv)

az deployment create \
  -n policyassign \
  -l southcentralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json \
  --parameters policyDefinitionID=$definition policyName=setLocation policyParameters="{'listOfAllowedLocations': {'value': ['westus']} }"
```

Aby wdrożyć ten szablon przy użyciu programu PowerShell, należy użyć:

```azurepowershell-interactive
$definition = Get-AzureRmPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Allowed locations' }

$locations = @("westus", "westus2")
$policyParams =@{listOfAllowedLocations = @{ value = $locations}}

New-AzureRmDeployment `
  -Name policyassign `
  -Location southcentralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json `
  -policyDefinitionID $definition.PolicyDefinitionId `
  -policyName setLocation `
  -policyParameters $policyParams
```

## <a name="define-and-assign-policy"></a>Zdefiniuj i przypisz zasady

Możesz [zdefiniować](../azure-policy/policy-definition.md) i przypisywanie zasad, w tym samym szablonie.

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

Utwórz definicję zasad w ramach subskrypcji i zastosować je do subskrypcji, użyj następującego polecenia interfejsu wiersza polecenia.

```azurecli-interactive
az deployment create \
  -n definePolicy \
  -l southcentralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

Aby wdrożyć ten szablon przy użyciu programu PowerShell, należy użyć:

```azurepowershell-interactive
New-AzureRmDeployment `
  -Name definePolicy `
  -Location southcentralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

## <a name="assign-role"></a>Przypisanie roli

Poniższy przykład przypisuje rolę użytkownika lub grupy.

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

Aby przypisać grupy usługi Active Directory do roli dla Twojej subskrypcji, użyj następujących poleceń interfejsu wiersza polecenia platformy Azure.

```azurecli-interactive
# Get ID of the role you want to assign
role=$(az role definition list --name Contributor --query [].name --output tsv)

# Get ID of the AD group to assign the role to
principalid=$(az ad group show --group demogroup --query objectId --output tsv)

az deployment create \
  -n demoRole \
  -l southcentralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/roleassign.json \
  --parameters principalId=$principalid roleDefinitionId=$role
```

Aby wdrożyć ten szablon przy użyciu programu PowerShell, należy użyć:

```azurepowershell-interactive
$role = Get-AzureRmRoleDefinition -Name Contributor

$adgroup = Get-AzureRmADGroup -DisplayName demogroup

New-AzureRmDeployment `
  -Name demoRole `
  -Location southcentralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/roleassign.json `
  -roleDefinitionId $role.Id `
  -principalId $adgroup.Id
```

## <a name="next-steps"></a>Kolejne kroki
* Aby uzyskać przykład wdrażania ustawień obszaru roboczego usługi Azure Security Center, zobacz [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Aby utworzyć grupę zasobów, zobacz [tworzyć grupy zasobów w szablonach usługi Azure Resource Manager](create-resource-group-in-template.md).
* Aby dowiedzieć się więcej na temat tworzenia szablonów usługi Azure Resource Manager, zobacz [Tworzenie szablonów](resource-group-authoring-templates.md). 
* Aby uzyskać listę dostępnych funkcji w szablonie, zobacz [funkcje szablonu](resource-group-template-functions.md).

