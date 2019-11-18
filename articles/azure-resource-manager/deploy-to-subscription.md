---
title: Wdrażanie zasobów w ramach subskrypcji
description: Opisuje sposób tworzenia grupy zasobów w szablonie Azure Resource Manager. Przedstawiono w nim również sposób wdrażania zasobów w zakresie subskrypcji platformy Azure.
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: d8a99a19038f98766df25551343a48b9789c8587
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74147147"
---
# <a name="create-resource-groups-and-resources-at-the-subscription-level"></a>Tworzenie grup zasobów i zasobów na poziomie subskrypcji

Zazwyczaj zasoby platformy Azure są wdrażane w grupie zasobów w ramach subskrypcji platformy Azure. Można jednak również tworzyć zasoby na poziomie subskrypcji. Wdrożenia na poziomie subskrypcji służą do podejmowania działań, które mają sens na tym poziomie, takich jak tworzenie grup zasobów lub przypisywanie [kontroli dostępu opartej na rolach](../role-based-access-control/overview.md).

Aby wdrażać szablony na poziomie subskrypcji, użyj interfejsu wiersza polecenia platformy Azure, programu PowerShell lub API REST. Azure Portal nie obsługuje wdrażania na poziomie subskrypcji.

## <a name="supported-resources"></a>Obsługiwane zasoby

Na poziomie subskrypcji można wdrożyć następujące typy zasobów:

* [komputerów](/azure/templates/microsoft.resources/deployments)
* [peerAsns](/azure/templates/microsoft.peering/peerasns)
* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [resourceGroups](/azure/templates/microsoft.resources/resourcegroups)
* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

### <a name="schema"></a>Schemat

Schemat używany do wdrożeń na poziomie subskrypcji różni się od schematu dla wdrożeń grup zasobów.

W przypadku szablonów Użyj:

```json
https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#
```

W przypadku plików parametrów należy użyć:

```json
https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentParameters.json#
```

## <a name="deployment-commands"></a>Polecenia wdrażania

Polecenia dla wdrożeń na poziomie subskrypcji są inne niż polecenia dla wdrożeń grup zasobów.

W przypadku interfejsu wiersza polecenia platformy Azure Użyj polecenia [AZ Deployment Create](/cli/azure/deployment?view=azure-cli-latest#az-deployment-create). Poniższy przykład wdraża szablon w celu utworzenia grupy zasobów:

```azurecli-interactive
az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json \
  --parameters rgName=demoResourceGroup rgLocation=centralus
```


W przypadku polecenia wdrażania programu PowerShell Użyj polecenie [New-AzDeployment](/powershell/module/az.resources/new-azdeployment). Poniższy przykład wdraża szablon w celu utworzenia grupy zasobów:

```azurepowershell-interactive
New-AzDeployment `
  -Name demoDeployment `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json `
  -rgName demoResourceGroup `
  -rgLocation centralus
```

W przypadku interfejsu API REST Użyj [wdrożeń — Utwórz zakres subskrypcji](/rest/api/resources/deployments/createorupdateatsubscriptionscope).

## <a name="deployment-location-and-name"></a>Lokalizacja i nazwa wdrożenia

W przypadku wdrożeń na poziomie subskrypcji należy podać lokalizację wdrożenia. Lokalizacja wdrożenia jest oddzielona od lokalizacji wdrażanych zasobów. Lokalizacja wdrożenia określa miejsce przechowywania danych wdrożenia.

Możesz podać nazwę wdrożenia lub użyć domyślnej nazwy wdrożenia. Nazwa domyślna to nazwa pliku szablonu. Na przykład wdrożenie szablonu o nazwie **azuredeploy. JSON** tworzy domyślną nazwę wdrożenia **azuredeploy**.

Dla każdej nazwy wdrożenia lokalizacja jest niezmienna. Nie można utworzyć wdrożenia w jednej lokalizacji, gdy istnieje wdrożenie o tej samej nazwie w innej lokalizacji. Jeśli zostanie wyświetlony kod błędu `InvalidDeploymentLocation`, użyj innej nazwy lub tej samej lokalizacji co poprzednie wdrożenie dla tej nazwy.

## <a name="use-template-functions"></a>Korzystanie z funkcji szablonu

W przypadku wdrożeń na poziomie subskrypcji istnieją pewne ważne zagadnienia dotyczące korzystania z funkcji szablonu:

* Funkcja [przesourceing ()](resource-group-template-functions-resource.md#resourcegroup) **nie** jest obsługiwana.
* Funkcja [ResourceID ()](resource-group-template-functions-resource.md#resourceid) jest obsługiwana. Służy do uzyskiwania identyfikatora zasobu dla zasobów używanych w ramach wdrożeń na poziomie subskrypcji. Na przykład Pobierz identyfikator zasobu dla definicji zasad z `resourceId('Microsoft.Authorization/roleDefinitions/', parameters('roleDefinition'))`. Można też użyć funkcji [subscriptionResourceId ()](resource-group-template-functions-resource.md#subscriptionresourceid) w celu pobrania identyfikatora zasobu dla zasobu poziomu subskrypcji.
* Obsługiwane są funkcje [Reference ()](resource-group-template-functions-resource.md#reference) i [list ()](resource-group-template-functions-resource.md#list) .

## <a name="create-resource-groups"></a>Tworzenie grup zasobów

Aby utworzyć grupę zasobów w szablonie Azure Resource Manager, zdefiniuj zasób [Microsoft. resources/resourceGroups](/azure/templates/microsoft.resources/allversions) z nazwą i lokalizacją grupy zasobów. Można utworzyć grupę zasobów i wdrożyć zasoby w tej grupie zasobów w tym samym szablonie.

Poniższy szablon tworzy pustą grupę zasobów.

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

Użyj [kopiowania elementu](resource-group-create-multiple.md) z grupami zasobów, aby utworzyć więcej niż jedną grupę zasobów. 

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

Aby uzyskać informacje o iteracji zasobów, zobacz [wdrażanie więcej niż jednego wystąpienia zasobu lub właściwości w szablonach Azure Resource Manager](./resource-group-create-multiple.md)i [Samouczek: Tworzenie wielu wystąpień zasobów przy użyciu szablonów Menedżer zasobów](./resource-manager-tutorial-create-multiple-instances.md).

## <a name="resource-group-and-resources"></a>Grupa zasobów i zasoby

Aby utworzyć grupę zasobów i wdrożyć do niej zasoby, użyj szablonu zagnieżdżonego. Szablon zagnieżdżony definiuje zasoby, które mają zostać wdrożone w grupie zasobów. Ustaw szablon zagnieżdżony jako zależny od grupy zasobów, aby upewnić się, że grupa zasobów istnieje przed wdrożeniem zasobów.

Poniższy przykład tworzy grupę zasobów i wdraża konto magazynu w grupie zasobów.

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

## <a name="create-policies"></a>Tworzenie zasad

### <a name="assign-policy"></a>Przypisywanie zasad

Poniższy przykład przypisuje istniejącą definicję zasad do subskrypcji. Jeśli zasady pobierają parametry, podaj je jako obiekt. Jeśli zasady nie przyjmują parametrów, Użyj domyślnego pustego obiektu.

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

Aby wdrożyć ten szablon przy użyciu interfejsu wiersza polecenia platformy Azure, użyj polecenia:

```azurecli-interactive
# Built-in policy that accepts parameters
definition=$(az policy definition list --query "[?displayName=='Allowed locations'].id" --output tsv)

az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json \
  --parameters policyDefinitionID=$definition policyName=setLocation policyParameters="{'listOfAllowedLocations': {'value': ['westus']} }"
```

Aby wdrożyć ten szablon przy użyciu programu PowerShell, użyj polecenia:

```azurepowershell-interactive
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

### <a name="define-and-assign-policy"></a>Definiowanie i przypisywanie zasad

Zasady można [definiować](../governance/policy/concepts/definition-structure.md) i przypisywać w tym samym szablonie.

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

Aby utworzyć definicję zasad w ramach subskrypcji, a następnie zastosować ją do subskrypcji, użyj następującego polecenia CLI:

```azurecli
az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

Aby wdrożyć ten szablon przy użyciu programu PowerShell, użyj polecenia:

```azurepowershell
New-AzDeployment `
  -Name definePolicy `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat przypisywania ról, zobacz [Zarządzanie dostępem do zasobów platformy Azure przy użyciu usług RBAC i Azure Resource Manager templates](../role-based-access-control/role-assignments-template.md).
* Przykład wdrażania ustawień obszaru roboczego dla Azure Security Center można znaleźć w pliku [deployASCwithWorkspaceSettings. JSON](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Przykładowe szablony można znaleźć w witrynie [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments).
* Aby dowiedzieć się więcej na temat tworzenia szablonów Azure Resource Manager, zobacz Tworzenie [szablonów](resource-group-authoring-templates.md). 
* Aby uzyskać listę dostępnych funkcji w szablonie, zobacz [funkcje szablonu](resource-group-template-functions.md).
