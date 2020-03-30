---
title: Wdrażanie zasobów w grupie zarządzania
description: W tym artykule opisano sposób wdrażania zasobów w zakresie grupy zarządzania w szablonie usługi Azure Resource Manager.
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: 863d1330412fa238b820eb0f1f05351fc723de6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460317"
---
# <a name="create-resources-at-the-management-group-level"></a>Tworzenie zasobów na poziomie grupy zarządzania

W miarę dojrzewania organizacji może być konieczne zdefiniowanie i przypisanie [zasad](../../governance/policy/overview.md) lub [kontroli dostępu opartych na rolach](../../role-based-access-control/overview.md) dla grupy zarządzania. Za pomocą szablonów na poziomie grupy zarządzania można deklaratywnie stosować zasady i przypisywać role na poziomie grupy zarządzania.

## <a name="supported-resources"></a>Obsługiwane zasoby

Na poziomie grupy zarządzania można wdrożyć następujące typy zasobów:

* [wdrożenia](/azure/templates/microsoft.resources/deployments) — dla szablonów zagnieżdżonych, które wdrażają w subskrypcjach lub grupach zasobów.
* [zmiany zasadAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [Policydefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [zasadySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [definicje ról](/azure/templates/microsoft.authorization/roledefinitions)

### <a name="schema"></a>Schemat

Schemat używany do wdrożeń grup zarządzania różni się od schematu dla wdrożeń grup zasobów.

W przypadku szablonów użyj:

```json
https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#
```

Schemat pliku parametrów jest taki sam dla wszystkich zakresów wdrażania. W przypadku plików parametrów należy użyć:

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="deployment-commands"></a>Polecenia wdrażania

Polecenia dla wdrożeń grup zarządzania różnią się od poleceń dla wdrożeń grup zasobów.

W przypadku interfejsu wiersza polecenia platformy Azure należy użyć [programu az deployment mg create:](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-create)

```azurecli-interactive
az deployment mg create \
  --name demoMGDeployment \
  --location WestUS \
  --management-group-id myMG \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

W przypadku programu Azure PowerShell należy użyć [funkcji New-AzManagementGroupDeployment](/powershell/module/az.resources/new-azmanagementgroupdeployment).

```azurepowershell-interactive
New-AzManagementGroupDeployment `
  -Name demoMGDeployment `
  -Location "West US" `
  -ManagementGroupId "myMG" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

W przypadku interfejsu API REST użyj [wdrożeń — tworzenie w zakresie grupy zarządzania](/rest/api/resources/deployments/createorupdateatmanagementgroupscope).

## <a name="deployment-location-and-name"></a>Lokalizacja i nazwa wdrożenia

W przypadku wdrożeń na poziomie grupy zarządzania należy podać lokalizację wdrożenia. Lokalizacja wdrożenia jest oddzielona od lokalizacji wydrążenych zasobów. Lokalizacja wdrożenia określa, gdzie mają być przechowywane dane wdrożenia.

Można podać nazwę wdrożenia lub użyć domyślnej nazwy wdrożenia. Domyślna nazwa to nazwa pliku szablonu. Na przykład wdrożenie szablonu o nazwie **azuredeploy.json** tworzy domyślną nazwę wdrożenia **azuredeploy**.

Dla każdej nazwy wdrożenia lokalizacja jest niezmienna. Nie można utworzyć wdrożenia w jednej lokalizacji, gdy istnieje istniejące wdrożenie o tej samej nazwie w innej lokalizacji. Jeśli zostanie wyświetlony `InvalidDeploymentLocation`kod błędu, użyj innej nazwy lub tej samej lokalizacji co poprzednie wdrożenie dla tej nazwy.

## <a name="use-template-functions"></a>Korzystanie z funkcji szablonu

W przypadku wdrożeń grup zarządzania, istnieją pewne ważne zagadnienia podczas korzystania z funkcji szablonu:

* Funkcja [resourceGroup()](template-functions-resource.md#resourcegroup) **nie** jest obsługiwana.
* Funkcja [subscription()](template-functions-resource.md#subscription) **nie** jest obsługiwana.
* Obsługiwane są funkcje [reference()](template-functions-resource.md#reference) i [list().](template-functions-resource.md#list)
* Funkcja [resourceId()](template-functions-resource.md#resourceid) jest obsługiwana. Użyj go, aby uzyskać identyfikator zasobu dla zasobów, które są używane we wdrożeniach na poziomie grupy zarządzania. Nie podawaj wartości parametru grupy zasobów.

  Na przykład, aby uzyskać identyfikator zasobu dla definicji zasad, należy użyć:
  
  ```json
  resourceId('Microsoft.Authorization/policyDefinitions/', parameters('policyDefinition'))
  ```
  
  Identyfikator zwróconego zasobu ma następujący format:
  
  ```json
  /providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="create-policies"></a>Tworzenie zasad

### <a name="define-policy"></a>Definiowanie zasad

W poniższym przykładzie pokazano, jak [zdefiniować](../../governance/policy/concepts/definition-structure.md) zasady na poziomie grupy zarządzania.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Authorization/policyDefinitions",
      "apiVersion": "2018-05-01",
      "name": "locationpolicy",
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
    }
  ]
}
```

### <a name="assign-policy"></a>Przypisywanie zasad

Poniższy przykład przypisuje istniejącą definicję zasad do grupy zarządzania. Jeśli zasady przyjmuje parametry, podaj je jako obiekt. Jeśli zasady nie przyjmują parametrów, należy użyć domyślnego pustego obiektu.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
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
      "apiVersion": "2018-03-01",
      "name": "[parameters('policyName')]",
      "properties": {
        "policyDefinitionId": "[parameters('policyDefinitionID')]",
        "parameters": "[parameters('policyParameters')]"
      }
    }
  ]
}
```

## <a name="template-sample"></a>Przykład szablonu

* [Tworzenie grupy zasobów, zasad i przypisań zasad](https://github.com/Azure/azure-docs-json-samples/blob/master/management-level-deployment/azuredeploy.json).

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o przypisywaniu ról, zobacz [Zarządzanie dostępem do zasobów platformy Azure przy użyciu szablonów RBAC i Usługi Azure Resource Manager](../../role-based-access-control/role-assignments-template.md).
* Na przykład wdrażania ustawień obszaru roboczego dla usługi Azure Security Center zobacz [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Można również wdrożyć szablony na [poziomie subskrypcji](deploy-to-subscription.md) i na [poziomie dzierżawy](deploy-to-tenant.md).
