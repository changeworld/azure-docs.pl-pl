---
title: Wdrażanie zasobów w dzierżawie
description: Opisuje sposób wdrażania zasobów w zakresie dzierżawy w szablonie Azure Resource Manager.
ms.topic: conceptual
ms.date: 03/06/2020
ms.openlocfilehash: d63697f3c140b5ad374607f1ecb00dad20e697de
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2020
ms.locfileid: "78899141"
---
# <a name="create-resources-at-the-tenant-level"></a>Tworzenie zasobów na poziomie dzierżawy

Zazwyczaj zasoby platformy Azure są wdrażane w grupie zasobów w ramach subskrypcji platformy Azure. Można jednak również utworzyć zasoby w:

* [poziom subskrypcji](deploy-to-subscription.md)
* [poziom grupy zarządzania](deploy-to-management-group.md)
* poziom dzierżawy (objęty w tym artykule)

Wdrożenia na poziomie dzierżawy służą do podejmowania działań, które mają sens na tym poziomie, takich jak przypisywanie [kontroli dostępu opartej na rolach](../../role-based-access-control/overview.md) lub stosowanie [zasad](../../governance/policy/overview.md).

## <a name="supported-resources"></a>Obsługiwane zasoby

Na poziomie dzierżawy można wdrożyć następujące typy zasobów:

* [wdrożenia](/azure/templates/microsoft.resources/deployments) — dla szablonów zagnieżdżonych wdrażanych w ramach grup lub subskrypcji zarządzania.
* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

### <a name="schema"></a>Schemat

Schemat używany do wdrożeń dzierżawców różni się od schematu dla wdrożeń grup zasobów.

W przypadku szablonów Użyj:

```json
https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#
```

W przypadku plików parametrów należy użyć:

```json
https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentParameters.json#
```

## <a name="required-access"></a>Wymagany dostęp

Podmiot zabezpieczeń wdrażający szablon musi mieć uprawnienia do tworzenia zasobów w zakresie dzierżawy. Podmiot zabezpieczeń musi mieć uprawnienia do wykonywania akcji wdrażania (`Microsoft.Resources/deployments/*`) i tworzenia zasobów zdefiniowanych w szablonie. Na przykład, aby utworzyć grupę zarządzania, podmiot zabezpieczeń musi mieć uprawnienie współautor w zakresie dzierżawy. Aby utworzyć przypisania ról, podmiot zabezpieczeń musi mieć uprawnienia właściciela.

Administrator globalny Azure Active Directory nie ma automatycznie uprawnienia do przypisywania ról. Aby włączyć wdrożenia szablonów w zakresie dzierżawy, Administrator globalny musi wykonać następujące czynności:

1. Podnieś poziom dostępu do konta, aby administrator globalny mógł przypisywać role. Aby uzyskać więcej informacji, zobacz [Podnieś poziom dostępu do zarządzania wszystkimi subskrypcjami i grupami zarządzania platformy Azure](../../role-based-access-control/elevate-access-global-admin.md).

1. Przypisz właściciela lub współautora do podmiotu zabezpieczeń, który musi wdrożyć szablony.

   ```azurepowershell-interactive
   New-AzRoleAssignment -SignInName "[userId]" -Scope "/" -RoleDefinitionName "Owner"
   ```

   ```azurecli-interactive
   az role assignment create --assignee "[userId]" --scope "/" --role "Owner"
   ```

Podmiot zabezpieczeń ma teraz wymagane uprawnienia do wdrożenia szablonu.

## <a name="deployment-commands"></a>Polecenia wdrażania

Polecenia dla wdrożeń dzierżawców są inne niż polecenia dla wdrożeń grup zasobów.

Aby uzyskać Azure PowerShell, użyj polecenie [New-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment).

```azurepowershell-interactive
New-AzTenantDeployment `
  -Location "West US" `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-level-deployments/new-mg/azuredeploy.json
```

W przypadku interfejsu API REST Użyj [wdrożeń — Utwórz lub zaktualizuj w zakresie dzierżawy](/rest/api/resources/deployments/createorupdateattenantscope).

## <a name="deployment-location-and-name"></a>Lokalizacja i nazwa wdrożenia

W przypadku wdrożeń na poziomie dzierżawy należy podać lokalizację wdrożenia. Lokalizacja wdrożenia jest oddzielona od lokalizacji wdrażanych zasobów. Lokalizacja wdrożenia określa miejsce przechowywania danych wdrożenia.

Możesz podać nazwę wdrożenia lub użyć domyślnej nazwy wdrożenia. Nazwa domyślna to nazwa pliku szablonu. Na przykład wdrożenie szablonu o nazwie **azuredeploy. JSON** tworzy domyślną nazwę wdrożenia **azuredeploy**.

Dla każdej nazwy wdrożenia lokalizacja jest niezmienna. Nie można utworzyć wdrożenia w jednej lokalizacji, gdy istnieje wdrożenie o tej samej nazwie w innej lokalizacji. Jeśli zostanie wyświetlony kod błędu `InvalidDeploymentLocation`, użyj innej nazwy lub tej samej lokalizacji co poprzednie wdrożenie dla tej nazwy.

## <a name="use-template-functions"></a>Korzystanie z funkcji szablonu

W przypadku wdrożeń dzierżawców istnieją pewne ważne zagadnienia dotyczące korzystania z funkcji szablonu:

* Funkcja [przesourceing ()](template-functions-resource.md#resourcegroup) **nie** jest obsługiwana.
* Funkcja [Subscription ()](template-functions-resource.md#subscription) **nie** jest obsługiwana.
* Obsługiwane są funkcje [Reference ()](template-functions-resource.md#reference) i [list ()](template-functions-resource.md#list) .
* Użyj funkcji [tenantResourceId ()](template-functions-resource.md#tenantresourceid) , aby uzyskać identyfikator zasobu dla zasobów wdrożonych na poziomie dzierżawy.

  Aby na przykład uzyskać identyfikator zasobu definicji zasad, należy użyć:
  
  ```json
  tenantResourceId('Microsoft.Authorization/policyDefinitions/', parameters('policyDefinition'))
  ```
  
  Identyfikator zwróconego zasobu ma następujący format:
  
  ```json
  /providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="create-management-group"></a>Tworzenie grupy zarządzania

[Poniższy szablon](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-level-deployments/new-mg) tworzy grupę zarządzania.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "mgName": {
      "type": "string",
      "defaultValue": "[concat('mg-', uniqueString(newGuid()))]"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Management/managementGroups",
      "apiVersion": "2019-11-01",
      "name": "[parameters('mgName')]",
      "properties": {
      }
    }
  ]
}
```

## <a name="assign-role"></a>Przypisz rolę

[Poniższy szablon](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-level-deployments/tenant-role-assignment) przypisuje rolę w zakresie dzierżawy.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "type": "string",
      "metadata": {
        "description": "principalId if the user that will be given contributor access to the resourceGroup"
      }
    },
    "roleDefinitionId": {
      "type": "string",
      "defaultValue": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
      "metadata": {
        "description": "roleDefinition for the assignment - default is owner"
      }
    }
  },
  "variables": {
    // This creates an idempotent guid for the role assignment
    "roleAssignmentName": "[guid('/', parameters('principalId'), parameters('roleDefinitionId'))]"
  },
  "resources": [
    {
      "name": "[variables('roleAssignmentName')]",
      "type": "Microsoft.Authorization/roleAssignments",
      "apiVersion": "2019-04-01-preview",
      "properties": {
        "roleDefinitionId": "[tenantResourceId('Microsoft.Authorization/roleDefinitions', parameters('roleDefinitionId'))]",
        "principalId": "[parameters('principalId')]",
        "scope": "/"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat przypisywania ról, zobacz [Zarządzanie dostępem do zasobów platformy Azure przy użyciu usług RBAC i Azure Resource Manager templates](../../role-based-access-control/role-assignments-template.md).
* Aby dowiedzieć się więcej na temat tworzenia szablonów Azure Resource Manager, zobacz Tworzenie [szablonów](template-syntax.md).
* Aby uzyskać listę dostępnych funkcji w szablonie, zobacz [funkcje szablonu](template-functions.md).