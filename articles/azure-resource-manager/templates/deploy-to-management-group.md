---
title: Wdrażanie zasobów w grupie zarządzania
description: Opisuje sposób wdrażania zasobów w zakresie grupy zarządzania w szablonie Azure Resource Manager.
ms.topic: conceptual
ms.date: 03/06/2020
ms.openlocfilehash: ae561468531b0c3fa584a02793c58ee64ca3610f
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2020
ms.locfileid: "78894892"
---
# <a name="create-resources-at-the-management-group-level"></a>Tworzenie zasobów na poziomie grupy zarządzania

Zazwyczaj zasoby platformy Azure są wdrażane w grupie zasobów w ramach subskrypcji platformy Azure. Można jednak również utworzyć zasoby w:

* [poziom subskrypcji](deploy-to-subscription.md)
* poziom grupy zarządzania (objęty w tym artykule)
* [poziom dzierżawy](deploy-to-tenant.md)

Wdrożenia na poziomie grupy zarządzania umożliwiają podejmowanie działań, które są odpowiednie dla tego poziomu, takich jak przypisywanie [kontroli dostępu opartej na rolach](../../role-based-access-control/overview.md) lub stosowanie [zasad](../../governance/policy/overview.md).

## <a name="supported-resources"></a>Obsługiwane zasoby

Na poziomie grupy zarządzania można wdrożyć następujące typy zasobów:

* [wdrożenia](/azure/templates/microsoft.resources/deployments) — dla szablonów zagnieżdżonych wdrażanych w ramach subskrypcji lub grup zasobów.
* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

### <a name="schema"></a>Schemat

Schemat używany do wdrożeń grup zarządzania różni się od schematu dla wdrożeń grup zasobów.

W przypadku szablonów Użyj:

```json
https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#
```

W przypadku plików parametrów należy użyć:

```json
https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentParameters.json#
```

## <a name="deployment-commands"></a>Polecenia wdrażania

Polecenia dla wdrożeń grup zarządzania są inne niż polecenia dla wdrożeń grup zasobów.

Aby uzyskać Azure PowerShell, użyj polecenie [New-AzManagementGroupDeployment](/powershell/module/az.resources/new-azmanagementgroupdeployment). 

```azurepowershell-interactive
New-AzManagementGroupDeployment `
  -ManagementGroupId "myMG" `
  -Location "West US" `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json
```

W przypadku interfejsu API REST Użyj funkcji [wdrożenia — Utwórz w zakresie grupy zarządzania](/rest/api/resources/deployments/createorupdateatmanagementgroupscope).

## <a name="deployment-location-and-name"></a>Lokalizacja i nazwa wdrożenia

W przypadku wdrożeń na poziomie grupy zarządzania należy podać lokalizację wdrożenia. Lokalizacja wdrożenia jest oddzielona od lokalizacji wdrażanych zasobów. Lokalizacja wdrożenia określa miejsce przechowywania danych wdrożenia.

Możesz podać nazwę wdrożenia lub użyć domyślnej nazwy wdrożenia. Nazwa domyślna to nazwa pliku szablonu. Na przykład wdrożenie szablonu o nazwie **azuredeploy. JSON** tworzy domyślną nazwę wdrożenia **azuredeploy**.

Dla każdej nazwy wdrożenia lokalizacja jest niezmienna. Nie można utworzyć wdrożenia w jednej lokalizacji, gdy istnieje wdrożenie o tej samej nazwie w innej lokalizacji. Jeśli zostanie wyświetlony kod błędu `InvalidDeploymentLocation`, użyj innej nazwy lub tej samej lokalizacji co poprzednie wdrożenie dla tej nazwy.

## <a name="use-template-functions"></a>Korzystanie z funkcji szablonu

W przypadku wdrożeń grup zarządzania istnieją pewne ważne zagadnienia dotyczące korzystania z funkcji szablonu:

* Funkcja [przesourceing ()](template-functions-resource.md#resourcegroup) **nie** jest obsługiwana.
* Funkcja [Subscription ()](template-functions-resource.md#subscription) **nie** jest obsługiwana.
* Obsługiwane są funkcje [Reference ()](template-functions-resource.md#reference) i [list ()](template-functions-resource.md#list) .
* Funkcja [ResourceID ()](template-functions-resource.md#resourceid) jest obsługiwana. Służy do uzyskiwania identyfikatora zasobu dla zasobów używanych w wdrożeniach na poziomie grupy zarządzania. Nie należy podawać wartości parametru grupy zasobów.

  Aby na przykład uzyskać identyfikator zasobu definicji zasad, należy użyć:
  
  ```json
  resourceId('Microsoft.Authorization/policyDefinitions/', parameters('policyDefinition'))
  ```
  
  Identyfikator zwróconego zasobu ma następujący format:
  
  ```json
  /providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="create-policies"></a>Tworzenie zasad

### <a name="define-policy"></a>Definiowanie zasad

Poniższy przykład pokazuje, jak [zdefiniować](../../governance/policy/concepts/definition-structure.md) zasady na poziomie grupy zarządzania.

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

Poniższy przykład przypisuje istniejącą definicję zasad do grupy zarządzania. Jeśli zasady pobierają parametry, podaj je jako obiekt. Jeśli zasady nie przyjmują parametrów, Użyj domyślnego pustego obiektu.

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

## <a name="template-sample"></a>Przykładowy szablon

* [Tworzenie grupy zasobów, zasad i przypisania zasad](https://github.com/Azure/azure-docs-json-samples/blob/master/management-level-deployment/azuredeploy.json).

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat przypisywania ról, zobacz [Zarządzanie dostępem do zasobów platformy Azure przy użyciu usług RBAC i Azure Resource Manager templates](../../role-based-access-control/role-assignments-template.md).
* Przykład wdrażania ustawień obszaru roboczego dla Azure Security Center można znaleźć w pliku [deployASCwithWorkspaceSettings. JSON](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Aby dowiedzieć się więcej na temat tworzenia szablonów Azure Resource Manager, zobacz Tworzenie [szablonów](template-syntax.md).
* Aby uzyskać listę dostępnych funkcji w szablonie, zobacz [funkcje szablonu](template-functions.md).